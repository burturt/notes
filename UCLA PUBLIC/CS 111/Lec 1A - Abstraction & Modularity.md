## OS Goals:
- Protection: want apps to work correctly
- Robustness: OS should be robust and continue working even in unusual circumstances (not crash)
- Utilization: want to actually use hardware all resources and thoroughly (CPU, I/O, GPU, etc)
- Performance: let apps run fast
- Flexibility: Allow easily adjusting to different types of apps to run
	- Maintainability
	- Compatibility
	- Intuition
- Simplicity
- Reproducability
- Atomicity
### AD: Main OS problems
- Virtualization
- Concurrency
- Persistence
### Eggert: Main OS problems
- Flexibility
- Evolution
### AD: main tools for addressing
- Abstraction & Modularity
	- Difference between interface and implementation
	- policy vs mechanism
- Measurement & Monitoring
	- Performance
	- Correctness
## Example: paranoid professor
- word count app/using nobody else's core
	- Assume words are ASCII [A-Za-z]+
	- `\0` means EOF
	-  Assume starts at 3MB
- Hardware:
	- Intel Core i3-13100
		- 12 MiB L3 cache
		- 3.4 GHz
		- 4-core (8 threads)
		- 8 GiB dual-channel DDR5-4800 SDRAM
	- 1 TB SATA Flash Drive
	- Display: Intel UHD 730 graphics
- Use 32-bit mode (`gcc -m32`): cpu boots up initially in 32 bit mode
- Firmware: Read-only memory to store some basic code
	- UEFI: complicated and newer
	- BIOS: simplier, older
- Software: starts at 1MB on drive
	- x86 machine code
- Running the program: press power switch
### x86 boot process
- start in real mode: have access to 1MiB RAM (2^20 bytes), eip points to 0xFFFF0 = 2^20 + 16
	- First instruction jumps backward
	- EEPROM: end of this 1MiB RAM is electrically erasable programmable read-only memory
		- Contains BIOS
	- Rest is set to 0s
- BIOS:
	- Sanity Check
	- RAM initialized to 0
	- Attempt to access all devices on BUS, and finds a working device, if it works on I/O, and if it is bootable, it will read into RAM
		- Reads 512 bytes into 0x7C00 (through 0x7E00)
		- Sets eip to 0x7c00
- Master Boot Record (MBR)
	- Bytes 510, 511: 0x55 0xAA
	- Indicates bootable (if not existing, BIOS assumes not bootable)
	- Previous 64 bytes describe partitions: 4 16-byte partition descriptions
		- Contains start (in 512 byte sectors), size, and status (bootable)
	- Rest is machine code
- Typical MBR code:
	- Look for bootable partition
	- Chain-load that partition's sector 0 (Volume Boot Record), read + execute
- VBR: OS-specific, runs this OS by chain-loading kernel
	- Sometimes loads GRUB, and GRUB will then chain-load OS
### Basic wc program
- Read sector program at some point:
```c
/* Read 512 bytes from sector s to memory location b */
void read_sector(int s, char *b) {
	// Wait until ready
	while (inb(0x1f7) & 0xC0 != 0x40)
		continue;
	// Tell it to read stuff (sectorcount)
	outb(0x1f2, 1);
	// Put sector number in registers
	outb(0x1f3, s);
	outb(0x1f4, s >> 8);
	outb(0x1f5, s >> 16);
	outb(0x1f6, s >> 24);
	// Tell controller to read, 0x30 to write
	outb(0x1f7, 0x20);
	// Wait until done reading
	while (inb(0x1f7) & 0xC0 != 0x40)
		continue;
	insl(0x1f0, b, 128); // copies 128 4-byte words from controller (aka same register over and over) into our ram at b
}

```
- Special x86 instructions:
	- Each device gets a custom register number like 0x1F7 for status, 1f2 for count, 1f3-1f6 will store sector number (4 bytes)
	- `inb`: reads from device register one byte by device ID
	- In custom register, read from top 2 bits and if `10`, device is idle
