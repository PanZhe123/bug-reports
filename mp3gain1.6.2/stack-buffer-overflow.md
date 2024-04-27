## MP3Gain v1.6.2: A buffer overflow in WriteMP3GainAPETag(apetag.c:578)

#### 1. Environment

Ubuntu 18.04, 64 bit

mp3gain 1.6.2

Clang 12.00

#### 2. Compilation

export CC="clang -g -fsanitize=address -fno-omit-frame-pointer"

make

#### 3. Run

\# ./mp3gain  poc

### 4. Asan report

==18376==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7fff5dc88504 at pc 0x00000043a144 bp 0x7fff5dc88380 sp 0x7fff5dc87b18

WRITE of size 300 at 0x7fff5dc88504 thread T0

​	#0 0x43a143 in vsprintf /home/cas/llvm12/compiler-rt/liz/asan/../sanitizer_common/sanitizer_common_interceptors.inc:1670:1

​	#1 0x43b0c3 in sprintf /home/cas/llvm12/compiler-rt/lib/asan/../sanitizer_common/sanitizer_common_interceptors.inc:1713:1

​	#2 0x4e000a in WriteMP3GainAPETag /home/cas/asan/mp3gain/mp3gain1.6.2-san/apetag.c:578:3

​	#3 0x4cbfa2 in WriteMP3GainTag /home/cas/asan/mp3gain/mp3gain1.6.2-san/mp3gain.c:1141:3

​	#4 0x4d7f5e in main /home/cas/asan/mp3gain/mp3gain1.6.2-san/mp3gain.c:2723:6

​	#5 0x7f80ba224c86 in __libc_start_main /build/glibc-CVJwZb/glibc-2.27/csu/../csu/libc-start.c:310

​	#6 0x41bc19 in _start (/home/cas/asan/mp3gain/mp3gain1.6.2-san/mp3gain+0x41bc19)

Address 0x7fff5dc88504 is located in stack of thread T0 at offset 132 in frame

​	#0 0x4de91f in WriteMP3GainAPETag /home/cas/asan/mp3gain/mp3gain1.6.2-san/apetag.c:404

This frame has 3 object(s):

​	[32, 132) 'valueString' (line 411)

​	[176, 208) 'newFooter' (line 413) <== Memory access at offset 132 partially underflows this variable

​	[240, 272) 'newHeader' (line 414) <== Memory access at offset 132 partially underflows this variable

HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork

 	(longjmp and C++ exceptions *are* supported)

SUMMARY: AddressSanitizer: stack-buffer-overflow /home/cas/llvm12/compiler-rt/lib/asan/../sanitizer_common/sanitizer_common_interceptors.inc:1670:1 in vsprintf

The poc file is:https://github.com/PanZhe123/bug-reports/blob/main/mp3gain1.6.2/poc
