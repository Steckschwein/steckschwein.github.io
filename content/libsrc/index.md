# Modules

[fat32](#fat32) | [joystick](#joystick) | [keyboard](#keyboard) | [sdcard](#sdcard) | [spi](#spi) | [uart](#uart) | [util](#util) | [vdp](#vdp) | 
***


## fat32
[fat_chdir](#fat_chdir) | [fat_close](#fat_close) | [fat_find_first](#fat_find_first) | [fat_find_next](#fat_find_next) | [fat_fopen](#fat_fopen) | [fat_fread_byte](#fat_fread_byte) | [fat_get_root_and_pwd](#fat_get_root_and_pwd) | [fat_mkdir](#fat_mkdir) | [fat_mount](#fat_mount) | [fat_opendir](#fat_opendir) | [fat_readdir](#fat_readdir) | [fat_rmdir](#fat_rmdir) | [fat_unlink](#fat_unlink) | [fat_write_byte](#fat_write_byte) | 

***


### fat_chdir


> change current directoryreaddir expects a pointer in A/Y to store the next F32DirEntry structure representing the next FAT32 directory entry in the directory stream pointed of directory X.



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path


Out
: C, C=0 on success (A=0), C=1 and A=<error code> otherwise<br />X, index into fd_area of the opened directory (which is FD_INDEX_CURRENT_DIR)


***

### fat_close


> close file, update dir entry and free file descriptor quietly



In
: X, index into fd_area of the opened file


Out
: C, 0 on success, 1 on error<br />A, error code


***

### fat_find_first


> find first dir entry



In
: A, low byte of pointer to zero terminated string with the file path<br />Y, high byte of pointer to zero terminated string with the file path<br />X, file descriptor (index into fd_area) of the directory


Out
: Z, 1 on success, 0 on error<br />A, error code<br />C, 0 if found and dirptr is set to the dir entry found (requires Z=1), else 1


***

### fat_find_next


> find next dir entry



In
: X, file descriptor (index into fd_area) of the directory


Out
: A, error code<br />C, 0 on success (A=0), C=1 and A=<error code>, else 1


***

### fat_fopen


> open file



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path<br />Y, file mode constants O_RDONLY = $01, O_WRONLY = $02, O_RDWR = $03, O_CREAT = $10, O_TRUNC = $20, O_APPEND = $40, O_EXCL = $80


Out
: C, 0 on success, 1 on error<br />A, error code<br />X, index into fd_area of the opened file


***

### fat_fread_byte


> read byte from file



In
: X, offset into fs area


Out
: C, 0 on success, 1 on error<br />A, received byte


***

### fat_get_root_and_pwd


> get current directory



In
: A, low byte of address to write the current work directory string into<br />Y, high byte address to write the current work directory string into<br />X, size of result buffer pointet to by A/X


Out
: C, 0 on success, 1 on error<br />A, error code


***

### fat_mkdir


> create directory denoted by given path in A/X



In
: A, low byte of pointer to directory string<br />X, high byte of pointer to directory string


Out
: C, 0 on success, 1 on error<br />A, error code


***

### fat_mount


> mount fat32 file system




Out
: C, 0 on success, 1 on error<br />A, error code


***

### fat_opendir


> open directory by given path starting from directory given as file descriptor



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path


Out
: C, C=0 on success (A=0), C=1 and A=<error code> otherwise<br />X, index into fd_area of the opened directory


***

### fat_readdir


> 



In
: X - file descriptor to fd_area of the directory<br />A/Y - pointer to target buffer which must be .sizeof(F32DirEntry)


Out
: C - C = 0 on success (A=0), C = 1 and A = <error code> otherwise


***

### fat_rmdir


> delete a directory entry denoted by given path in A/X



In
: A, low byte of pointer to directory string<br />X, high byte of pointer to directory string


Out
: C, 0 on success, 1 on error<br />A, error code


***

### fat_unlink


> unlink (delete) a file denoted by given path in A/X



In
: A, low byte of pointer to zero terminated string with the file path<br />X, high byte of pointer to zero terminated string with the file path


Out
: C, C=0 on success (A=0), C=1 and A=<error code> otherwise


***

### fat_write_byte


> write byte to file



In
: A, byte to write<br />X, offset into fs area


Out
: C, 0 on success, 1 on error


***


## joystick
[joystick_detect](#joystick_detect) | [joystick_read](#joystick_read) | 

***


### joystick_detect


> detect joystick




Out
: Z, Z=1 no joystick detected, Z=0 joystick detected, port in A<br />A, detected joystick port, JOY_PORT1 or JOY_PORT2


***

### joystick_read


> read state of specified joystick




Out
: A, joystick button state - bit 0-4


***


## keyboard
[fetchkey](#fetchkey) | [getkey](#getkey) | 

***


### fetchkey


> fetch byte from keyboard controller




Out
: A, fetched key / error code<br />C, 1 - key was fetched, 0 - nothing fetched


***

### getkey


> get byte from keyboard buffer




Out
: A, fetched key<br />C, 1 - key was fetched, 0 - nothing fetched


***


## sdcard
[sd_busy_wait](#sd_busy_wait) | [sd_cmd](#sd_cmd) | [sd_deselect_card](#sd_deselect_card) | [sd_read_block](#sd_read_block) | [sd_select_card](#sd_select_card) | [sdcard_init](#sdcard_init) | 

***


### sd_busy_wait


> wait while sd card is busy




Out
: C, C = 0 on success, C = 1 on error (timeout)


Clobbers
: A,X,Y 

***

### sd_cmd


> send command to sd card



In
: A, command byte<br />sd_cmd_param, command parameters


Out
: A, SD Card R1 status byte


Clobbers
: A,X,Y 

***

### sd_deselect_card


> Read block from SD Card




Out
: A, error code<br />C, 0 - success, 1 - error


Clobbers
: X 

***

### sd_read_block


> Read block from SD Card



In
: lba_addr, LBA address of block<br />sd_blkptr, target adress for the block data to be read


Out
: A, error code<br />C, 0 - success, 1 - error


Clobbers
: A,X,Y 

***

### sd_select_card


> select sd card, pull CS line to low with busy wait




Out
: C, C = 0 on success, C = 1 on error (timeout)


Clobbers
: A,X,Y 

***

### sdcard_init


> initialize sd card in SPI mode




Out
: Z,1 on success, 0 on error<br />A, error code


Clobbers
: A,X,Y 

***


## spi
[spi_r_byte](#spi_r_byte) | [spi_rw_byte](#spi_rw_byte) | 

***


### spi_r_byte


> read byte via SPI




Out
: A, received byte


Clobbers
: A,X 

***

### spi_rw_byte


> transmit byte via SPI



In
: A, byte to transmit


Out
: A, received byte


Clobbers
: A,X,Y 

***


## uart
[uart_init](#uart_init) | [uart_rx](#uart_rx) | [uart_rx_nowait](#uart_rx_nowait) | [uart_tx](#uart_tx) | 

***


### uart_init


> initialize UART with parameters from nvram





Clobbers
: A,X,Y 

***

### uart_rx


> receive byte




Out
: A, received byte


***

### uart_rx_nowait


> receive byte, no wait




Out
: A, received byte<br />C, 0 - no byte received, 1 - received byte


***

### uart_tx


> send byte



In
: A, byte to send



***


## util
[atoi](#atoi) | [b2ad](#b2ad) | [b2ad2](#b2ad2) | [bin2dual](#bin2dual) | [hexout](#hexout) | [hextodec](#hextodec) | [strout](#strout) | 

***


### atoi


> convert ascii digit to binary



In
: A, value to convert


Out
: A, binary number


***

### b2ad


> output 8bit value as 2 digit decimal



In
: A, value to output



***

### b2ad2


> output 8bit value as 3 digit decimal



In
: A, value to output



***

### bin2dual


> output 8bit value as binary string



In
: A, value to output



***

### hexout


> output 8bit value as hex



In
: A, value to convert



***

### hextodec


> 8bit hex to decimal converter



In
: A, value to convert


Out
: A, ones<br />X, tens<br />Y, hundreds


***

### strout


> Output string on active output device



In
: A, lowbyte  of string address<br />X, highbyte of string address



***


## vdp
[vdp_wait_cmd](#vdp_wait_cmd) | 

***


### vdp_wait_cmd


> wait until a pending command has been finished



In
: -


Out
: -


***

