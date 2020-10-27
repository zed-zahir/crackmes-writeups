Welcome to my cracking ways - really basic cracking crackmes

you can call me Zed if you wish, in this tutorial or document or manual I will show you the very basic way to crack applications under GNU/Linux Operating System, no time to waste on presenting myself to you, then let’s go cracking!

1 - Under GNU/Linux OS:
my distribution of choice is ubuntu, of course, I don't crack my crackmes on my host machine or from the bare-metal OS of my machine, so I use, of course, the free version of VMWare-Player, this way I can sleep well at night not thinking if the process Xorg is actually a rootkit process or not!
And for the choice of this demonstration I will use a really easy crackme called easyAF, it's a really easy one, and it fit well for the purpose of teaching this material.
you can grab a copy of the CrackMe here: https://crackmes.one/crackme/5eae2d6633c5d47611746500

# 1 - fetching some information with the command file from the crackme:
here is the output when running `file` command on the crackme
```
zahir@zed-zahir:~/ctf/crackmes.one/easyAF$ file easyAF 
easyAF: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=c1f484166af165c71e71e8e0e5ebd8f9d6300f0a, for GNU/Linux 3.2.0, not stripped
```

What can we learn from this output? let's deconstruct it:
* ELF 64-bit LSB shared object
* x86-64, version 1 (SYSV)
* dynamically linked
* interpreter /lib64/ld-linux-x86-64.so.2
* BuildID[sha1]=c1f484166af165c71e71e8e0e5ebd8f9d6300f0a
* for GNU/Linux 3.2.0
* not stripped

* ELF 64-bit LSB shared object
	* ELF 64-bit
		* this means that it's an ELF file Format and you can find more information on the appendix, I will link all the Wikipedia articles of these maybe if it's esoteric to you.
* And 64-bit means the ELF Format is the 64-bit ELF architecture.
* LSB executable and LSB shared objects
* this one depends mainly about the PIE which means the `position-independent code` or the `position-independent executable`
* x86-64, version 1 (SYSV)
* x86-64
* This one means the architure of the process is the x86-64.
* version 1 (SYSV)
* I am not sure about this one, but I think it means the version of the ELF File Format, it adds the (SYSV) which confuses me and the SYSV means the system 5 which is UNIX System.
* dynamically linked
* interpreter /lib64/ld-linux-x86-64.so.2
* This one means it has been linked with /lib64/ld-linux-x86-64.so.2 after compilation with whatever compiler it is.
* BuildID[sha1]=c1f484166af165c71e71e8e0e5ebd8f9d6300f0a
* this one I believe it’s the hash checksum of the file
* for GNU/Linux 3.2.0
* this one means the application is for or compiled on the kernel version 3.2.0 
* not stripped
* Mainly means it's compiled with the option `-s` in GCC compiler, and this means it removed some useful for reversing information from the binary, like the name of the functions, for example.
 
Mainly what interest us here is if it is stripped or not, if it is then we know we will find hard time reversing it.

# 2 - fetching printable strings from the crackme:
running strings on the crackme will give us this information:
```
zahir@zed-zahir:~/ctf/crackmes.one/easyAF$ strings easyAF 
/lib64/ld-linux-x86-64.so.2
libstdc++.so.6
...
libc.so.6
__stack_chk_fail
__cxa_atexit
memcmp
__cxa_finalize
__libc_start_main
GCC_3.0
...
[]A\A]A^A_
pass
Enter the password: 
Welldone!
Nope
;*3$"
zPLR
GCC: (Arch Linux 9.3.0-1) 9.3.0
init.c
crtstuff.c
...
__libc_start_main@@GLIBC_2.2.5
__gmon_start__
_ITM_registerTMCloneTable
_ZNSt8ios_base4InitD1Ev@@GLIBCXX_3.4
.symtab
.strtab
.shstrtab
.interp
.note.gnu.build-id
.note.ABI-tag
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.gcc_except_table
.init_array
.fini_array
.dynamic
.got
.got.plt
.data
.bss
.comment
```

we can see the loader or the linker `/lib64/ld-linux-x86-64.so.2` and some shared object libraries like `libstdc++.so.6`, and we can see some function names like `memcmp`, and we can see some symbols at end of the file, and here we see some strings specific to the crackme:
```
pass
Enter the password: 
Welldone!
Nope
```

'Enter the password:' is for printing on the screen to enter the password and 'Welldone!' is for congratulating us for succeeding and 'Nope' is here to tell us it's not the correct password and guess what? 'pass' is the password :D.

if you are totally new to this, you would ask yourself, 'how the hell did you know that?' I know you will ask that, because I used to say the same when I used to read some tuts or books of reversing or some advanced coding books.

How do I know that, it comes from experience and reading and lurking on forums and googling.

# 3 - fetching some information about the file format of the crackme:
this time I will use an application called readelf, it will give us some information that might be useful next when we reverse some apps.

This time I will use the readelf just with the `-h` option, just to get the header of the file format.
you can experiment with this tool by just typing `readelf` or check all the possible information to show with `-a` or `--all`
```
zahir@zed-zahir:~/ctf/crackmes.one/easyAF$ readelf -h easyAF 
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x1100
  Start of program headers:          64 (bytes into file)
  Start of section headers:          16592 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         11
  Size of section headers:           64 (bytes)
  Number of section headers:         30
  Section header string table index: 29

```

I will explain only few information from these data, if you wish to know more about the subject, I would recommend you to read a document that deals with elf file format.

`Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 `
which is the magic byte, every file has a magic byte, and the magic byte is the signature of the file, which means there are not two file formats with the same magic byte, for example ogg or mp3 formats have their magic bytes to differentiate between themselves, while they are both audio files, and you might ask 'is that how Linux recognizes the application to open with the file even without the extension on it?' the answer is a simple yes, this is how GNU/Linux distributions recognize the application to open the file with.

`Entry point address:               0x1100`
this one is an interesting one and it deserves to be explained here, the entry point means the entry point address where the Operating System start executing code in. it means the Operating System in our example here GNU/Linux, when it parses the elf header and see the value `0x1100` in the entry point address, it starts executing code there and it follows the flow depending on the instructions.

# 4 - fetching the symbols from the crackme:
this time we will use an application called `nm`

```
zahir@zed-zahir:~/ctf/crackmes.one/easyAF$ nm easyAF 
0000000000004098 B __bss_start
00000000000042d8 b completed.7393
                 U __cxa_atexit@@GLIBC_2.2.5
                 w __cxa_finalize@@GLIBC_2.2.5
0000000000004080 D __data_start
0000000000004080 W data_start
0000000000001130 t deregister_tm_clones
00000000000011a0 t __do_global_dtors_aux
0000000000003db8 d __do_global_dtors_aux_fini_array_entry
0000000000004088 D __dso_handle
0000000000004090 V DW.ref.__gxx_personality_v0
0000000000003dc0 d _DYNAMIC
0000000000004098 D _edata
00000000000042e0 B _end
                 U exit@@GLIBC_2.2.5
0000000000001518 T _fini
00000000000011f0 t frame_dummy
0000000000003da8 d __frame_dummy_init_array_entry
0000000000002234 r __FRAME_END__
0000000000004000 d _GLOBAL_OFFSET_TABLE_
00000000000013a1 t _GLOBAL__sub_I_main
                 w __gmon_start__
0000000000002030 r __GNU_EH_FRAME_HDR
                 U __gxx_personality_v0@@CXXABI_1.3
0000000000001000 t _init
0000000000003db8 d __init_array_end
0000000000003da8 d __init_array_start
0000000000002000 R _IO_stdin_used
                 w _ITM_deregisterTMCloneTable
                 w _ITM_registerTMCloneTable
0000000000001510 T __libc_csu_fini
00000000000014a0 T __libc_csu_init
                 U __libc_start_main@@GLIBC_2.2.5
00000000000011f9 T main
                 U memcmp@@GLIBC_2.2.5
0000000000001160 t register_tm_clones
                 U __stack_chk_fail@@GLIBC_2.4
0000000000001100 T _start
0000000000004098 D __TMC_END__
...

```

Here is the symbols of the crackme, we can find some interesting information like function names and other data, and we can only see that if the binary is not stripped.

The numbers in the left is the virtual address of the function or the data.
the letter just beside it is the purpose of the data, you can find the meaning of this letter by typing on the terminal `man nm`

# 5 - fetching code instruction with disassembling of the crackme:

in this section I won't use the easyAF crackme, because it's not that easy to read the disassembly of that binary.

so here I created a really simple crackme, here is the source code in .c:
```
#include <stdio.h>
#include <string.h>

int main( void ) {

  char name[32];
  printf("enter your name: ");
  scanf("%s", name);
  if( !strcmp( name, "zed" ) )
    printf("cool name!\n");
  else
    printf("it's an okay name\n");

  return 0;
}
```

And we compile it with `gcc easyAH.c -o easyAH`
here is easyAH means easy as hell.

and we run it on a disassembler such as objdump
`objdump -d -M intel easyAH | grep \<main\>\: -A 36`
```
00000000000011c9 <main>:
    11c9: f3 0f 1e fa           endbr64 
    11cd: 55                    push   rbp
    11ce: 48 89 e5              mov    rbp,rsp
    11d1: 48 83 ec 30           sub    rsp,0x30
    11d5: 64 48 8b 04 25 28 00  mov    rax,QWORD PTR fs:0x28
    11dc: 00 00 
    11de: 48 89 45 f8           mov    QWORD PTR [rbp-0x8],rax
    11e2: 31 c0                 xor    eax,eax
    11e4: 48 8d 3d 19 0e 00 00  lea    rdi,[rip+0xe19]       
    11eb: b8 00 00 00 00        mov    eax,0x0
    11f0: e8 bb fe ff ff        call   10b0 <printf@plt>
    11f5: 48 8d 45 d0           lea    rax,[rbp-0x30]
    11f9: 48 89 c6              mov    rsi,rax
    11fc: 48 8d 3d 13 0e 00 00  lea    rdi,[rip+0xe13]
    1203: b8 00 00 00 00        mov    eax,0x0
    1208: e8 c3 fe ff ff        call   10d0 <__isoc99_scanf@plt>
    120d: 48 8d 45 d0           lea    rax,[rbp-0x30]
    1211: 48 8d 35 01 0e 00 00  lea    rsi,[rip+0xe01]       
    1218: 48 89 c7              mov    rdi,rax
    121b: e8 a0 fe ff ff        call   10c0 <strcmp@plt>
    1220: 85 c0                 test   eax,eax
    1222: 75 0e                 jne    1232 <main+0x69>
    1224: 48 8d 3d f2 0d 00 00  lea    rdi,[rip+0xdf2]        
    122b: e8 60 fe ff ff        call   1090 <puts@plt>
    1230: eb 0c                 jmp    123e <main+0x75>
    1232: 48 8d 3d ef 0d 00 00  lea    rdi,[rip+0xdef]        
    1239: e8 52 fe ff ff        call   1090 <puts@plt>
    123e: b8 00 00 00 00        mov    eax,0x0
    1243: 48 8b 55 f8           mov    rdx,QWORD PTR [rbp-0x8]
    1247: 64 48 33 14 25 28 00  xor    rdx,QWORD PTR fs:0x28
    124e: 00 00 
    1250: 74 05                 je     1257 <main+0x8e>
    1252: e8 49 fe ff ff        call   10a0 <__stack_chk_fail@plt>
    1257: c9                    leave  
    1258: c3                    ret    
    1259: 0f 1f 80 00 00 00 00  nop    DWORD PTR [rax+0x0]
```

Let's start from the beginning to the end of the disassembly code with the help of the source code.

```
    push   rbp
    mov    rbp,rsp
```

This part is called the Prolog, which means the stack reserves a space for a function, in our case it's main.

```
    sub    rsp,0x30
```

Whenever I see this instruction on assembly code, meaning the sub rsp, something, I know it's allocating space, and mainly it means an array of something either `char` or `int` or whatever.
and in our case it's the:
```
  char name[32];
```

```
    lea    rdi,[rip+0xe19]       
    mov    eax,0x0
    call   10b0 <printf@plt>
```

When I see these instructions, I know there is a text to be printed on the screen, the lea rdi means there is some text to be loaded from the binary and call printf is the function to call when we need to print text.

One warning, you might come from the x86 architecture, and you might find a difference between the way you used to see it and this way.

in the x86 the text would be pushed on the stack instead of being loaded in a register, this would look something like
```
 push [rip+0xe19]
 call 10b0 <printf@plt>
```

If you don't make the difference between these two things and you are freshly new to it, then now you know there are two different ways to load a string and print it on the stack.

And if you don't know what a stack means, it's just a space in your computer memory that you might call RAM, and if you have a 32-bit processor, then your computer would need to gather the string from data segments of the application and put it in the stack then call the function printf().
If it's a 64-bit processor then your computer will load the string to the register which is a faster and then execute the printf() function.
If you need more information on the terms, you didn't understand check the appendix.

and in our code it's the equivalent of the:
```
 printf("enter your name: ");
```

and then there is the scanf function:

```
 lea    rax,[rbp-0x30]
 mov    rsi,rax
 lea    rdi,[rip+0xe13]
 mov    eax,0x0
 call   10d0 <__isoc99_scanf@plt>
```

We load the content from the stack [rbp-0x30] to rax then to rsi which is our variable and we load the string on the data segment [rip+0xe13] which is "%s" and then call the scanf().

in the source code, it is:
```
 scanf("%s", name);
```

then we use the strcmp function like here:

```
 lea    rax,[rbp-0x30]
 lea    rsi,[rip+0xe01]       
 mov    rdi,rax
 call   10c0 <strcmp@plt>
```

here the code is mainly like

```
 lea    rdi,[rbp-0x30]
 lea    rsi,[rip+0xe01]   
 call   10c0 <strcmp@plt>
```

Here we load the variable [rbp-0x30] and we load from the data segment [rip+0xe01] and we launch the strcmp function to compare the two strings.

in the code it is this line sloc:

```
 strcmp( name, "zed" )
```

then in this assembly code it is like

```
 test   eax,eax
 jne    1232 <main+0x69>
 lea    rdi,[rip+0xdf2]        
 call   1090 <puts@plt>
 jmp    123e <main+0x75>
 lea    rdi,[rip+0xdef]        
 call   1090 <puts@plt>
```

it's mainly if eax is 0 or 1, it will either print the first string or the second in our source code it is like this:

```
 if(  )
     printf("cool name!\n");
 else
     printf("it's an okay name\n");

```

so let's recap all the instructions we need to understand:
 
 push, it mainly pushes to the stack, generally variable
 mov, moves data to register or to the stack
 sub, subtract either a value or rbp to allocate variables
 lea, load from the stack to a register
 call, call a function
 jne, compare eax to 0 or non-0 then follow where it should jmp or continue
 test, test if the register is 0 or non-0

# Appendix

here are interesting Wikipedia articles for beginners:
(https://en.wikipedia.org/wiki/Stripped_binary)
(https://en.wikipedia.org/wiki/Debug_symbol)
(https://en.wikipedia.org/wiki/Symbol_table)
(https://en.wikipedia.org/wiki/Linux_Standard_Base)
(https://en.wikipedia.org/wiki/Position-independent_code)
(https://en.wikipedia.org/wiki/Library_(computing)#Shared_libraries)
(https://en.wikipedia.org/wiki/X86-64)
(https://en.wikipedia.org/wiki/Data_segment)
(https://en.wikipedia.org/wiki/Stack-based_memory_allocation)
