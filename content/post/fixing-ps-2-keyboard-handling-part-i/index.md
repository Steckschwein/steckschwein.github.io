---
draft: false

title: "Fixing PS/2 Keyboard handling (Part I)"
date: "2020-11-14"
categories: 
  - "allgemein"
---

The way the PS/2 keyboard is handled has always been something we were never quite happy with. The key points being:

- The PS/2 controller had no way of signalling that there has been a new keystroke, the buffer had to be polled via SPI.
- The PS/2 controller had no way of talking to the keyboard and had to rely for the keyboard to initialize itself properly. Also, typematic rate and delay could not be set, as couldn't the states of the keyboard LEDs.

Although mid- to long term, we likely might "upgrade" to USB anyway, but not without having done PS/2 right first. So, I will talk about integrating IRQ handling, and in a follow up post Marko will talk about how he got the PS/2 controller talking to the keyboard.

Luckily, during the design of the IO-board, we have been clever enough to hook IO-pins PC0 to PC2 to RESET\_TRIG, NMI and IRQ, respectively. So on the hardware-side, we are very much ready.  
First problem to solve is how to emulate an open collector output on the AVR controller. As it seems, a common way to do that is to disable the internal pullup of the pin, and have it configured as input to be "tri state". When active, the pin gets activated as an output, and will pull the IRQ line low.

`// pull IRQ line  
DDRC |= (1 << IRQ);`

`// release IRQ line  
DDRC &= ~(1 << IRQ);`

Now that we know how to handle the IRQ-line, we need to figure out, WHEN to pull it. Obviously when a key was hit. And when to release it?

Finally, we decided to go the most simple way. The PS/2 controller will pull the IRQ line as long as there are more than 0 chars in the buffer. Once the buffer is empty, the IRQ-line will be released. This way, we do not need an interrupt register and hence no time consuming check of the latter, but need to do a little buffering on the steckOS-side.

This is all the code that's needed on the PS/2 controller side:  

    if (kb\_buffcnt > 0)
    {
        DDRC |= (1 << IRQ);     // pull IRQ line
    }
    else
    {
        DDRC &= ~(1 << IRQ); // release IRQ line
    }

Now, we need to add a little handling code to the steckOS IRQ-handler. Since we do not have an interrupt register, we just check the keyboard last, after every "known" interrupt source has been handled.  
To get around having to implement another keyboard buffer, we just use a single memory location, labelled "key". The IRQ handler will only fetch a byte from the keyboard when the target location is zero (0), otherwise it will just exit.  
The system getkey-routine will load the contents from that location into the A register, and overwrite the location with 0 again to enable fetching the next char from the buffer.

The SPI check code is the last bit in the IRQ-handler routine:

`@check_spi:  
lda key  
bne @exit  
jsr fetchkey  
bcc @exit  
sta key  
  
@exit:  
restore  
rti`

That's basically all that's needed. The former getkey-routine has been renamed to fetchkey, and the new getkey routine only handles the ZP buffer location while retaining the old behaviour including setting the carry flag when a byte has been received. This way, existing programs using the keyboard do not have to be modified.

Now, we finally have a chance of reacting to keystrokes during program execution without having to explicitly poll the keyboard. This enables us to handle Ctrl-C and such much more elegantly. Also, any REPL-like program (like the shell) does not have to constantly poll the SPI bus.
