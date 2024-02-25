---
title: "Libsrc"
categories:
  - software
---
# Modules

[fat32](#fat32) | [joystick](#joystick) | [keyboard](#keyboard) | [sdcard](#sdcard) | [spi](#spi) | [uart](#uart) | [util](#util) | [vdp](#vdp) | 
***


## fat32
[fat_chdir](#fat_chdir) | [fat_close](#fat_close) | [fat_fopen](#fat_fopen) | [fat_fread_byte](#fat_fread_byte) | [fat_fread_vollgas](#fat_fread_vollgas) | [fat_fseek](#fat_fseek) | [fat_get_root_and_pwd](#fat_get_root_and_pwd) | [fat_mkdir](#fat_mkdir) | [fat_mount](#fat_mount) | [fat_opendir](#fat_opendir) | [fat_read_direntry](#fat_read_direntry) | [fat_readdir](#fat_readdir) | [fat_rmdir](#fat_rmdir) | [fat_unlink](#fat_unlink) | [fat_update_direntry](#fat_update_direntry) | [fat_write_byte](#fat_write_byte) | 

***


### <a name="fat_chdir" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_dir.s#L72">fat_chdir</a>

> change current directory



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path


Out
: C, C=0 on success (A=0), C=1 and A=<error code> otherwise<br />X, index into fd_area of the opened directory (which is FD_INDEX_CURRENT_DIR)


***

### <a name="fat_close" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32.s#L234">fat_close</a>

> close file, update dir entry and free file descriptor quietly



In
: X, index into fd_area of the opened file


Out
: C, 0 on success, 1 on error<br />A, error code


***

### <a name="fat_fopen" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32.s#L187">fat_fopen</a>

> open file



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path<br />Y, file mode constants O_RDONLY = $01, O_WRONLY = $02, O_RDWR = $03, O_CREAT = $10, O_TRUNC = $20, O_APPEND = $40, O_EXCL = $80


Out
: C, 0 on success, 1 on error<br />A, error code<br />X, index into fd_area of the opened file


***

### <a name="fat_fread_byte" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32.s#L114">fat_fread_byte</a>

> read byte from file



In
: X, offset into fd_area


Out
: C=0 on success and A=received byte, C=1 on error and A=error code or C=1 and A=0 (EOK) if EOF is reached


***

### <a name="fat_fread_vollgas" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat_fread_vollgas.s#L47">fat_fread_vollgas</a>

> read the file denoted by given file descriptor (X) until EOF and store data at given address (A/Y)



In
: X - offset into fd_area<br />A/Y - pointer to target address


Out
: C=0 on success, C=1 on error and A=error code or C=1 and A=0 (EOK) if EOF is reached


***

### <a name="fat_fseek" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_seek.s#L45">fat_fseek</a>

> seek n bytes within file denoted by the given FD



In
: X - offset into fd_area<br />A/Y - pointer to seek_struct - @see fat32.inc


Out
: C=0 on success (A=0), C=1 and A=<error code> or C=1 and A=0 (EOK) if EOF reached


***

### <a name="fat_get_root_and_pwd" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_cwd.s#L47">fat_get_root_and_pwd</a>

> get current directory



In
: A, low byte of address to write the current work directory string into<br />Y, high byte address to write the current work directory string into<br />X, size of result buffer pointet to by A/X


Out
: C, 0 on success, 1 on error<br />A, error code


***

### <a name="fat_mkdir" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_write_dir.s#L74">fat_mkdir</a>

> create directory denoted by given path in A/X



In
: A, low byte of pointer to directory string<br />X, high byte of pointer to directory string


Out
: C, 0 on success, 1 on error<br />A, error code


***

### <a name="fat_mount" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_mount.s#L47">fat_mount</a>

> mount fat32 file system




Out
: C, 0 on success, 1 on error<br />A, error code


***

### <a name="fat_opendir" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_dir.s#L50">fat_opendir</a>

> open directory by given path starting from directory given as file descriptor



In
: A/X - pointer to string with the file path


Out
: C, C=0 on success (A=0), C=1 and A=<error code> otherwise<br />X, index into fd_area of the opened directory


***

### <a name="fat_read_direntry" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_core.s#L250">fat_read_direntry</a>

> read the block with the directory entry of the given file descriptor, dirptr is adjusted accordingly



In
: X - file descriptor of the file the directory entry should be read


Out
: C - C=0 on success (A=0), C=1 and A=<error code> otherwise<br />dirptr pointing to the corresponding directory entry of type F32DirEntry


***

### <a name="fat_readdir" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_dir.s#L89">fat_readdir</a>

> readdir expects a pointer in A/Y to store the next F32DirEntry structure representing the next FAT32 directory entry in the directory stream pointed of directory X.



In
: X - file descriptor to fd_area of the directory<br />A/Y - pointer to target buffer which must be .sizeof(F32DirEntry)


Out
: C - C = 0 on success (A=0), C = 1 and A = <error code> otherwise. C=1/A=EOK if end of directory is reached


***

### <a name="fat_rmdir" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_write_dir.s#L49">fat_rmdir</a>

> delete a directory entry denoted by given path in A/X



In
: A, low byte of pointer to directory string<br />X, high byte of pointer to directory string


Out
: C, 0 on success, 1 on error<br />A, error code


***

### <a name="fat_unlink" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_write.s#L544">fat_unlink</a>

> unlink (delete) a file denoted by given path in A/X



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path


Out
: C, C=0 on success (A=0), C=1 and A=<error code> otherwise


***

### <a name="fat_update_direntry" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_write_dir.s#L244">fat_update_direntry</a>

> update direntry given as pointer (A/Y) to FAT32 directory entry structure for file fd (X).



In
: X - file descriptor to fd_area of the file<br />A/Y - pointer to direntry buffer with updated direntry data of type F32DirEntry


Out
: C - C = 0 on success (A=0), C = 1 and A = <error code> otherwise. C=1/A=EOK if end of directory is reached


***

### <a name="fat_write_byte" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/fat32/fat32_write.s#L56">fat_write_byte</a>

> write byte to file



In
: A, byte to write<br />X, offset into fs area


Out
: C, 0 on success, 1 on error


***


## joystick
[joystick_detect](#joystick_detect) | [joystick_read](#joystick_read) | 

***


### <a name="joystick_detect" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/joystick/joystick.s#L45">joystick_detect</a>

> detect joystick




Out
: Z, Z=1 no joystick detected, Z=0 joystick detected, port in A<br />A, detected joystick port, JOY_PORT1 or JOY_PORT2


***

### <a name="joystick_read" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/joystick/joystick.s#L17">joystick_read</a>

> read state of specified joystick




Out
: A, joystick button state - bit 0-4


***


## keyboard
[fetchkey](#fetchkey) | [getkey](#getkey) | 

***


### <a name="fetchkey" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/keyboard/keyboard.s#L40">fetchkey</a>

> fetch byte from keyboard controller




Out
: A, fetched key / error code<br />C, 1 - key was fetched, 0 - nothing fetched


***

### <a name="getkey" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/keyboard/keyboard.s#L66">getkey</a>

> get byte from keyboard buffer




Out
: A, fetched key<br />C, 1 - key was fetched, 0 - nothing fetched


***


## sdcard
[sd_busy_wait](#sd_busy_wait) | [sd_cmd](#sd_cmd) | [sd_deselect_card](#sd_deselect_card) | [sd_read_block](#sd_read_block) | [sd_select_card](#sd_select_card) | [sd_write_block](#sd_write_block) | [sdcard_init](#sdcard_init) | 

***


### <a name="sd_busy_wait" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/sdcard/sdcard.s#L423">sd_busy_wait</a>

> wait while sd card is busy




Out
: C, C = 0 on success, C = 1 on error (timeout)


Clobbers
: A,X,Y

***

### <a name="sd_cmd" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/sdcard/sdcard.s#L235">sd_cmd</a>

> send command to sd card



In
: A, command byte<br />sd_cmd_param, command parameters


Out
: A, SD Card R1 status byte


Clobbers
: A,X,Y

***

### <a name="sd_deselect_card" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/sdcard/sdcard.s#L324">sd_deselect_card</a>

> Read block from SD Card




Out
: A, error code<br />C, 0 - success, 1 - error


Clobbers
: X

***

### <a name="sd_read_block" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/sdcard/sdcard.s#L295">sd_read_block</a>

> Read block from SD Card



In
: lba_addr, LBA address of block<br />sd_blkptr, target adress for the block data to be read


Out
: A, error code<br />C, 0 - success, 1 - error


Clobbers
: A,X,Y

***

### <a name="sd_select_card" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/sdcard/sdcard.s#L409">sd_select_card</a>

> select sd card, pull CS line to low with busy wait




Out
: C, C = 0 on success, C = 1 on error (timeout)


Clobbers
: A,X,Y

***

### <a name="sd_write_block" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/sdcard/sd_write_block.s#L56">sd_write_block</a>

> Write block to SD Card



In
: lba_addr, LBA address of block<br />sd_blkptr, target adress for the block data to be read


Out
: A, error code<br />C, 0 - success, 1 - error


Clobbers
: A,X,Y

***

### <a name="sdcard_init" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/sdcard/sdcard.s#L65">sdcard_init</a>

> initialize sd card in SPI mode




Out
: Z,1 on success, 0 on error<br />A, error code


Clobbers
: A,X,Y

***


## spi
[spi_deselect](#spi_deselect) | [spi_r_byte](#spi_r_byte) | [spi_rw_byte](#spi_rw_byte) | [spi_select_device](#spi_select_device) | 

***


### <a name="spi_deselect" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/spi/spi_deselect.s#L33">spi_deselect</a>

> deselect all SPI devices





***

### <a name="spi_r_byte" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/spi/spi_r_byte.s#L38">spi_r_byte</a>

> read byte via SPI




Out
: A, received byte


Clobbers
: A,X

***

### <a name="spi_rw_byte" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/spi/spi_rw_byte.s#L40">spi_rw_byte</a>

> transmit byte via SPI



In
: A, byte to transmit


Out
: A, received byte


Clobbers
: A,X,Y

***

### <a name="spi_select_device" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/spi/spi_select_device.s#L40">spi_select_device</a>

> select spi device given in A. the method is aware of the current processor state, especially the interrupt flag



In
: ; A, spi device, one of devices see spi.inc


Out
: Z = 1 spi for given device could be selected (not busy), Z=0 otherwise


***


## uart
[uart_init](#uart_init) | [uart_rx](#uart_rx) | [uart_rx_nowait](#uart_rx_nowait) | [uart_tx](#uart_tx) | 

***


### <a name="uart_init" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/uart/16550/uart_init.s#L11">uart_init</a>

> initialize UART with parameters from nvram





Clobbers
: A,X,Y

***

### <a name="uart_rx" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/uart/16550/uart_rx.s#L9">uart_rx</a>

> receive byte




Out
: A, received byte


***

### <a name="uart_rx_nowait" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/uart/16550/uart_rx_nowait.s#L7">uart_rx_nowait</a>

> receive byte, no wait




Out
: A, received byte<br />C, 0 - no byte received, 1 - received byte


***

### <a name="uart_tx" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/uart/16550/uart_tx.s#L10">uart_tx</a>

> send byte



In
: A, byte to send



***


## util
[atoi](#atoi) | [b2ad](#b2ad) | [b2ad2](#b2ad2) | [bin2dual](#bin2dual) | [hexout](#hexout) | [hextodec](#hextodec) | [strout](#strout) | 

***


### <a name="atoi" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/util/atoi.s#L31">atoi</a>

> convert ascii digit to binary



In
: A, value to convert


Out
: A, binary number


***

### <a name="b2ad" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/util/b2ad.s#L7">b2ad</a>

> output 8bit value as 2 digit decimal



In
: A, value to output



***

### <a name="b2ad2" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/util/b2ad2.s#L8">b2ad2</a>

> output 8bit value as 3 digit decimal



In
: A, value to output



***

### <a name="bin2dual" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/util/bin2dual.s#L6">bin2dual</a>

> output 8bit value as binary string



In
: A, value to output



***

### <a name="hexout" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/util/hexout.s#L40">hexout</a>

> output 8bit value as hex



In
: A, value to convert



***

### <a name="hextodec" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/util/hex2dec.s#L5">hextodec</a>

> 8bit hex to decimal converter



In
: A, value to convert


Out
: A, ones<br />X, tens<br />Y, hundreds


***

### <a name="strout" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/util/strout.s#L44">strout</a>

> Output string on active output device



In
: A, lowbyte  of string address<br />X, highbyte of string address



***


## vdp
[gfx_circle](#gfx_circle) | [gfx_line](#gfx_line) | [gfx_plot](#gfx_plot) | [gfx_point](#gfx_point) | [ppm_load_image](#ppm_load_image) | [vdp_bgcolor](#vdp_bgcolor) | [vdp_cmd_hmmv](#vdp_cmd_hmmv) | [vdp_fill](#vdp_fill) | [vdp_fills](#vdp_fills) | [vdp_gfx1_blank](#vdp_gfx1_blank) | [vdp_gfx1_on](#vdp_gfx1_on) | [vdp_gfx7_set_pixel_direct](#vdp_gfx7_set_pixel_direct) | [vdp_init_reg](#vdp_init_reg) | [vdp_mc_blank](#vdp_mc_blank) | [vdp_mc_init_screen](#vdp_mc_init_screen) | [vdp_mc_on](#vdp_mc_on) | [vdp_mc_set_pixel](#vdp_mc_set_pixel) | [vdp_memcpy](#vdp_memcpy) | [vdp_memcpys](#vdp_memcpys) | [vdp_mode2_blank](#vdp_mode2_blank) | [vdp_mode2_on](#vdp_mode2_on) | [vdp_mode2_set_pixel](#vdp_mode2_set_pixel) | [vdp_mode3_on](#vdp_mode3_on) | [vdp_mode6_blank](#vdp_mode6_blank) | [vdp_mode6_on](#vdp_mode6_on) | [vdp_mode6_set_pixel](#vdp_mode6_set_pixel) | [vdp_mode7_blank](#vdp_mode7_blank) | [vdp_mode7_on](#vdp_mode7_on) | [vdp_mode7_set_pixel](#vdp_mode7_set_pixel) | [vdp_mode_sprites_off](#vdp_mode_sprites_off) | [vdp_set_reg](#vdp_set_reg) | [vdp_text_blank](#vdp_text_blank) | [vdp_text_on](#vdp_text_on) | [vdp_wait_cmd](#vdp_wait_cmd) | 

***


### <a name="gfx_circle" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/gfx_circle.s#L36">gfx_circle</a>

> draw circle



In
: A/Y - pointer to circle_t struct



***

### <a name="gfx_line" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/gfx_line.s#L37">gfx_line</a>

> draw line according to data in given line struct



In
: A/Y ptr to line_t struct



***

### <a name="gfx_plot" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/gfx_plot.s#L66">gfx_plot</a>

> plot pixel



In
: A/Y - pointer to plot_t struct



***

### <a name="gfx_point" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/gfx_point.s#L36">gfx_point</a>

> read pixel value



In
: A/Y - pointer to plot_t struct


Out
: Y - color of pixel at given plot_t


***

### <a name="ppm_load_image" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/ppm/ppm.s#L65">ppm_load_image</a>

> 



In
: A/X file name to load


Out
: C=0 success and image loaded to vram (mode 7), C=1 otherwise with A/X error code where X ppm specific error, A i/o specific error


***

### <a name="vdp_bgcolor" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/t99xx.s#L42">vdp_bgcolor</a>

> 



In
: A - color



***

### <a name="vdp_cmd_hmmv" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/t99xx_cmd_hmmv.s#L35">vdp_cmd_hmmv</a>

> execute highspeed memory move (vdp/vram) or fill



In
: A/X - ptr to rectangle coordinates (4 word with x1,y1, len x, len y)<br />Y - color to fill in (reg #44)



***

### <a name="vdp_fill" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/t99xx_fill.s#L31">vdp_fill</a>

> fill vdp VRAM with given value page wise



In
: A - byte to fill<br />X - amount of 256byte blocks (page counter)



***

### <a name="vdp_fills" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/t99xx_fill.s#L45">vdp_fills</a>

> fill vdp VRAM with given value



In
: A - value to write<br />X - amount of bytes



***

### <a name="vdp_gfx1_blank" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode1.s#L36">vdp_gfx1_blank</a>

> 





***

### <a name="vdp_gfx1_on" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode1.s#L43">vdp_gfx1_on</a>

> gfx mode 1 - 32x24 character mode, 16 colors with same color for 8 characters in a block





***

### <a name="vdp_gfx7_set_pixel_direct" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode7_setpixel.s#L67">vdp_gfx7_set_pixel_direct</a>

> 



In
: X - x coordinate [0..ff]<br />Y - y coordinate [0..bf]<br />A - color GRB [0..ff] as 332



***

### <a name="vdp_init_reg" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/t99xx_init.s#L33">vdp_init_reg</a>

> setup video registers upon given table starting from register #R.X down to #R0



In
: X - length of init table, corresponds to video register to start R#+X - e.g. X=10 start with R#10<br />A/Y - pointer to vdp init table



***

### <a name="vdp_mc_blank" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode_mc.s#L93">vdp_mc_blank</a>

> blank multi color mode, set all pixel to black



In
: A - color to blank



***

### <a name="vdp_mc_init_screen" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode_mc.s#L60">vdp_mc_init_screen</a>

> init mc screen





***

### <a name="vdp_mc_on" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode_mc.s#L38">vdp_mc_on</a>

> gfx multi color mode - 4x4px blocks where each can have one of the 15 colors





***

### <a name="vdp_mc_set_pixel" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode_mc_setpixel.s#L33">vdp_mc_set_pixel</a>

> set pixel to mc screen - VRAM ADDRESS = 8(INT(X DIV 2)) + 256(INT(Y DIV 8)) + (Y MOD 8)



In
: X - x coordinate [0..3f]<br />Y - y coordinate [0..2f]<br />A - color [0..f]



***

### <a name="vdp_memcpy" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/t99x_memcpy.s#L33">vdp_memcpy</a>

> copy data from host memory denoted by pointer (A/Y) to vdp VRAM (page wise). the VRAM address must be setup beforehand e.g. with macro vdp_vram_w <address>



In
: X - amount of 256byte blocks (page counter)<br />A/Y - pointer to source data



***

### <a name="vdp_memcpys" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/t99x_memcpy.s#L58">vdp_memcpys</a>

> copy memory to vdp VRAM page wise



In
: X - amount of bytes to copy<br />A/Y - pointer to data



***

### <a name="vdp_mode2_blank" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode2.s#L71">vdp_mode2_blank</a>

> blank gfx mode 2 with



In
: A - color to fill [0..f]



***

### <a name="vdp_mode2_on" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode2.s#L37">vdp_mode2_on</a>

> gfx 2 - each pixel can be addressed - e.g. for image





***

### <a name="vdp_mode2_set_pixel" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode2_setpixel.s#L33">vdp_mode2_set_pixel</a>

> set pixel to gfx2 mode screen - VRAM ADDRESS = 8(INT(X DIV 8)) + 256(INT(Y DIV 8)) + (Y MOD 8)



In
: X - x coordinate [0..ff]<br />Y - y coordinate [0..bf]<br />A - color [0..f]



***

### <a name="vdp_mode3_on" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode3.s#L36">vdp_mode3_on</a>

> mode 3 on





***

### <a name="vdp_mode6_blank" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode6.s#L64">vdp_mode6_blank</a>

> blank gfx mode 6 with given color



In
: Y - color to fill 4|4 Bit



***

### <a name="vdp_mode6_on" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode6.s#L36">vdp_mode6_on</a>

> gfx 6 - 512x192/212px, 16colors, sprite mode 2





***

### <a name="vdp_mode6_set_pixel" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode6_setpixel.s#L31">vdp_mode6_set_pixel</a>

> set pixel to gfx6 mode screen - VRAM ADDRESS = 8(INT(X DIV 2)) + 256(INT(Y DIV 8)) + (Y MOD 8)



In
: X - x coordinate [0..ff]<br />Y - y coordinate [0..bf]<br />A - color [0..f] and bit 7 MSB x coordinate



***

### <a name="vdp_mode7_blank" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode7.s#L58">vdp_mode7_blank</a>

> blank gfx mode 7 with



In
: Y - color to fill in GRB (3+3+2)



***

### <a name="vdp_mode7_on" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode7.s#L32">vdp_mode7_on</a>

> gfx 7 - each pixel can be addressed - e.g. for image





***

### <a name="vdp_mode7_set_pixel" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode7_setpixel.s#L31">vdp_mode7_set_pixel</a>

> VRAM ADDRESS = .X + 256*.Y



In
: X - x coordinate [0..ff]<br />Y - y coordinate [0..bf]<br />. A - color [0..ff] as GRB 332 (green bit 7-5, red bit 4-2, blue bit 1-0)



***

### <a name="vdp_mode_sprites_off" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/t99xx_sprites.s#L31">vdp_mode_sprites_off</a>

> 





***

### <a name="vdp_set_reg" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/t99xx.s#L47">vdp_set_reg</a>

> set value to vdp register



In
: A - value<br />Y - register



***

### <a name="vdp_text_blank" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode_text.s#L48">vdp_text_blank</a>

> text mode blank screen and color vram





***

### <a name="vdp_text_on" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/mode_text.s#L62">vdp_text_on</a>

> text mode - 40x24/80x24 character mode, 2 colors



In
: A - color settings (#R07)



***

### <a name="vdp_wait_cmd" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/libsrc/t99xx/t99xx_cmd.s#L31">vdp_wait_cmd</a>

> wait until a pending command has been finished





***

