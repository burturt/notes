https://ide.kaitai.io/
`readelf -a`
## ELF Section headers
- .text: executable program
- .plt and .got: resolve and dispatch library calls
	- e.g. printf
- .data: pre-initialized global writable data (like global arrays with initial values)
- .rodata: global read-only data (e.g. string constants)
- .bss: uninitialized global writable data
## Symbols
- Names used to find libraries, resolve function calls, etc
- https://www.intezer.com/blog/malware-analysis/executable-linkable-format-101-part-2-symbols/
## Interacting:
- `gcc` to make ELF
- `readelf` to parse ELF header
- `objdump` to parse ELF header and disassemble the source code
- `nm` read symbols
- `patchelf` change ELF properties
- `objcopy` to swap out ELF sections
	- `--dump-section` and `--update-section`
- `strip` to remove symbols
- 