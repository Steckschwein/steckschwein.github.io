---
title: "jumptable"
categories:
  - software
---


# jumptable

[filesystem](#filesystem) | [kernel](#kernel) | [sdcard](#sdcard) | [spi](#spi) | [video](#video) | 
***


## filesystem
[krn_chdir](#krn_chdir) | [krn_close](#krn_close) | [krn_fopen](#krn_fopen) | [krn_fread_byte](#krn_fread_byte) | [krn_fseek](#krn_fseek) | [krn_getcwd](#krn_getcwd) | [krn_mkdir](#krn_mkdir) | [krn_open](#krn_open) | [krn_opendir](#krn_opendir) | [krn_read_direntry](#krn_read_direntry) | [krn_readdir](#krn_readdir) | [krn_rmdir](#krn_rmdir) | [krn_unlink](#krn_unlink) | [krn_update_direntry](#krn_update_direntry) | [krn_write_byte](#krn_write_byte) | 

***


### <a name="krn_chdir" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L66">krn_chdir</a>

> change current directory



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path


Out
: C, C=0 on success (A=0), C=1 and A=<error code> otherwise<br />X, index into fd_area of the opened directory (which is FD_INDEX_CURRENT_DIR)


***

### <a name="krn_close" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L101">krn_close</a>

> close file, update dir entry and free file descriptor quietly



In
: X, index into fd_area of the opened file


Out
: C, 0 on success, 1 on error<br />A, error code


***

### <a name="krn_fopen" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L47">krn_fopen</a>

> open file regardless of file/dir



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path<br />Y, file mode constants O_RDONLY = $01, O_WRONLY = $02, O_RDWR = $03, O_CREAT = $10, O_TRUNC = $20, O_APPEND = $40, O_EXCL = $80


Out
: C, 0 on success, 1 on error<br />A, error code<br />X, index into fd_area of the opened file


***

### <a name="krn_fread_byte" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L112">krn_fread_byte</a>

> read byte from file



In
: X, offset into fd_area


Out
: C=0 on success and A=received byte, C=1 on error and A=error code or C=1 and A=0 (EOK) if EOF is reached


***

### <a name="krn_fseek" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L127">krn_fseek</a>

> seek n bytes within file denoted by the given FD



In
: X - offset into fd_area<br />A/Y - pointer to seek_struct - @see fat32.inc


Out
: C=0 on success (A=0), C=1 and A=<error code> or C=1 and A=0 (EOK) if EOF reached


***

### <a name="krn_getcwd" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L135">krn_getcwd</a>

> get current directory



In
: A, low byte of address to write the current work directory string into<br />Y, high byte address to write the current work directory string into<br />X, size of result buffer pointet to by A/X


Out
: C, 0 on success, 1 on error<br />A, error code


***

### <a name="krn_mkdir" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L92">krn_mkdir</a>

> create directory denoted by given path in A/X



In
: A, low byte of pointer to directory string<br />X, high byte of pointer to directory string


Out
: C, 0 on success, 1 on error<br />A, error code


***

### <a name="krn_open" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L36">krn_open</a>

> open file regardless of file/dir



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path<br />Y, file mode constants O_RDONLY = $01, O_WRONLY = $02, O_RDWR = $03, O_CREAT = $10, O_TRUNC = $20, O_APPEND = $40, O_EXCL = $80


Out
: C, 0 on success, 1 on error<br />A, error code<br />X, index into fd_area of the opened file


***

### <a name="krn_opendir" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L58">krn_opendir</a>

> open directory by given path starting from directory given as file descriptor



In
: A/X - pointer to string with the file path


Out
: C, C=0 on success (A=0), C=1 and A=<error code> otherwise<br />X, index into fd_area of the opened directory


***

### <a name="krn_read_direntry" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L153">krn_read_direntry</a>

> readdir expects a pointer in A/Y to store the F32DirEntry structure representing the requested FAT32 directory entry for the given fd (X).



In
: X - file descriptor to fd_area of the file<br />A/Y - pointer to target buffer which must be .sizeof(F32DirEntry)


Out
: C - C = 0 on success (A=0), C = 1 and A = <error code> otherwise. C=1/A=EOK if end of directory is reached


***

### <a name="krn_readdir" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L145">krn_readdir</a>

> readdir expects a pointer in A/Y to store the next F32DirEntry structure representing the next FAT32 directory entry in the directory stream pointed of directory X.



In
: X - file descriptor to fd_area of the directory<br />A/Y - pointer to target buffer which must be .sizeof(F32DirEntry)


Out
: C - C = 0 on success (A=0), C = 1 and A = <error code> otherwise. C=1/A=EOK if end of directory is reached


***

### <a name="krn_rmdir" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L83">krn_rmdir</a>

> delete a directory entry denoted by given path in A/X



In
: A, low byte of pointer to directory string<br />X, high byte of pointer to directory string


Out
: C, 0 on success, 1 on error<br />A, error code


***

### <a name="krn_unlink" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L75">krn_unlink</a>

> unlink (delete) a file denoted by given path in A/X



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path


Out
: C, C=0 on success (A=0), C=1 and A=<error code> otherwise


***

### <a name="krn_update_direntry" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L161">krn_update_direntry</a>

> update direntry given as pointer (A/Y) to FAT32 directory entry structure for file fd (X).



In
: X - file descriptor to fd_area of the file<br />A/Y - pointer to direntry buffer with updated direntry data of type F32DirEntry


Out
: C - C = 0 on success (A=0), C = 1 and A = <error code> otherwise. C=1/A=EOK if end of directory is reached


***

### <a name="krn_write_byte" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L119">krn_write_byte</a>

> write byte to file



In
: A, byte to write<br />X, offset into fs area


Out
: C, 0 on success, 1 on error


***


## kernel
[krn_chrout](#krn_chrout) | [krn_execv](#krn_execv) | [krn_getkey](#krn_getkey) | [krn_upload](#krn_upload) | 

***


### <a name="krn_chrout" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L13">krn_chrout</a>

> output character



In
: A, character to output



***

### <a name="krn_execv" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L24">krn_execv</a>

> load PRG file at path and execute it



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path


Out
: A error code on error


***

### <a name="krn_getkey" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L6">krn_getkey</a>

> get byte from keyboard buffer




Out
: A, fetched key<br />C, 1 - key was fetched, 0 - nothing fetched


***

### <a name="krn_upload" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L19">krn_upload</a>

> jump to kernel XMODEM upload





***


## sdcard
[krn_sd_read_block](#krn_sd_read_block) | [krn_sd_write_block](#krn_sd_write_block) | 

***


### <a name="krn_sd_read_block" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L199">krn_sd_read_block</a>

> Read block from SD Card



In
: lba_addr, LBA address of block<br />sd_blkptr, target adress for the block data to be read


Out
: A, error code<br />C, 0 - success, 1 - error


Clobbers
: A,X,Y

***

### <a name="krn_sd_write_block" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L190">krn_sd_write_block</a>

> Write block to SD Card



In
: lba_addr, LBA address of block<br />sd_blkptr, target adress for the block data to be read


Out
: A, error code<br />C, 0 - success, 1 - error


Clobbers
: A,X,Y

***


## spi
[krn_spi_deselect](#krn_spi_deselect) | [krn_spi_select_device](#krn_spi_select_device) | [spi_r_byte](#spi_r_byte) | [spi_rw_byte](#spi_rw_byte) | [textui_blank](#textui_blank) | [textui_cursor_onoff](#textui_cursor_onoff) | [textui_disable](#textui_disable) | [textui_enable](#textui_enable) | [textui_init](#textui_init) | [textui_reset](#textui_reset) | [textui_setmode](#textui_setmode) | [textui_update_crs_ptr](#textui_update_crs_ptr) | [textui_update_screen](#textui_update_screen) | [uart_rx](#uart_rx) | [uart_tx](#uart_tx) | 

***


### <a name="krn_spi_deselect" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L219">krn_spi_deselect</a>

> deselect all SPI devices





***

### <a name="krn_spi_select_device" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L212">krn_spi_select_device</a>

> select spi device given in A. the method is aware of the current processor state, especially the interrupt flag



In
: ; A, spi device, one of devices see spi.inc


Out
: Z = 1 spi for given device could be selected (not busy), Z=0 otherwise


***

### <a name="spi_r_byte" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L230">spi_r_byte</a>

> read byte via SPI




Out
: A, received byte


Clobbers
: A,X

***

### <a name="spi_rw_byte" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L223">spi_rw_byte</a>

> transmit byte via SPI



In
: A, byte to transmit


Out
: A, received byte


Clobbers
: A,X,Y

***

### <a name="textui_blank" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//textui.asm#L300">textui_blank</a>

> blank screen, set cursor to top left corner



In
: -



***

### <a name="textui_cursor_onoff" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//textui.asm#L273">textui_cursor_onoff</a>

> toggle the blinking cursor on if off or off if on



In
: -



***

### <a name="textui_disable" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//textui.asm#L265">textui_disable</a>

> disable text ui - cursor will be disabled



In
: -



***

### <a name="textui_enable" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//textui.asm#L257">textui_enable</a>

> enable text ui



In
: -



***

### <a name="textui_init" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//textui.asm#L218">textui_init</a>

> init the text ui if used for the first time. like kernel start or kind of



In
: -



***

### <a name="textui_reset" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//textui.asm#L244">textui_reset</a>

> reset text ui by setting the internal state accordingly.



In
: -



***

### <a name="textui_setmode" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//textui.asm#L231">textui_setmode</a>

> set desired text mode which is either 40 (MSX TEXT 1) or 80 columns (MSX TEXT 2)



In
: A - the desired mode, either VIDEO_MODE_80_COLS or 0 to reset to 40 column mode



***

### <a name="textui_update_crs_ptr" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//textui.asm#L126">textui_update_crs_ptr</a>

> update to new cursor position given in crs_x and crs_y zeropage locations



In
: -



***

### <a name="textui_update_screen" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//textui.asm#L174">textui_update_screen</a>

> update internal state - is called on v-blank



In
: -



***

### <a name="uart_rx" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L243">uart_rx</a>

> receive byte via serial interface




Out
: A, received byte


***

### <a name="uart_tx" target="_blank" href="https://github.com/Steckschwein/code/tree/master/../steckos/kernel//jumptable.asm#L238">uart_tx</a>

> send byte via serial interface



In
: A, byte to send



***


## video


***


