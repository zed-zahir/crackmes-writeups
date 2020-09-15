#here is a based buffer overflow crackme

let's check the big picture of this crackme, with disassembling it in objdump:

`objdump -d -M intel crackme | grep \<main\>\: -A 10`

```
 08049218 <main>:
 8049218:	55                   	push   ebp
 8049219:	89 e5                	mov    ebp,esp
 804921b:	83 e4 f0             	and    esp,0xfffffff0
 804921e:	e8 16 00 00 00       	call   8049239 <__x86.get_pc_thunk.ax>
 8049223:	05 dd 2d 00 00       	add    eax,0x2ddd
 8049228:	e8 ab ff ff ff       	call   80491d8 <f>
 804922d:	e8 7b ff ff ff       	call   80491ad <__f_func>
 8049232:	b8 00 00 00 00       	mov    eax,0x0
 8049237:	c9                   	leave  
 8049238:	c3                   	ret    
```

we see that the function main calls the function 'f' on the virtual address '0x8049228' then the function '_f_func' on the virtual address '0x804922d'

lets disassemble them both

here is the function 'f':

`objdump -d -M intel crackme | grep \<f\>\: -A 22`

```
 080491d8 <f>:
 80491d8:	55                   	push   ebp
 80491d9:	89 e5                	mov    ebp,esp
 80491db:	53                   	push   ebx
 80491dc:	83 ec 24             	sub    esp,0x24
 80491df:	e8 dc fe ff ff       	call   80490c0 <__x86.get_pc_thunk.bx>
 80491e4:	81 c3 1c 2e 00 00    	add    ebx,0x2e1c
 80491ea:	83 ec 0c             	sub    esp,0xc
 80491ed:	8d 83 22 e0 ff ff    	lea    eax,[ebx-0x1fde]
 80491f3:	50                   	push   eax
 80491f4:	e8 37 fe ff ff       	call   8049030 <printf@plt>
 80491f9:	83 c4 10             	add    esp,0x10
 80491fc:	83 ec 08             	sub    esp,0x8
 80491ff:	8d 45 d8             	lea    eax,[ebp-0x28]
 8049202:	50                   	push   eax
 8049203:	8d 83 36 e0 ff ff    	lea    eax,[ebx-0x1fca]
 8049209:	50                   	push   eax
 804920a:	e8 51 fe ff ff       	call   8049060 <__isoc99_scanf@plt>
 804920f:	83 c4 10             	add    esp,0x10
 8049212:	90                   	nop
 8049213:	8b 5d fc             	mov    ebx,DWORD PTR [ebp-0x4]
 8049216:	c9                   	leave  
 8049217:	c3                   	ret    
```


we see that the function allocates 0x24 => 36-decimal bytes from the stack, and we will need more to overflow the EIP

and lets see the function '_f_func'

`objdump -d -M intel crackme | grep \<__f_func\>\: -A 16`

```
 080491ad <__f_func>:
 80491ad:	55                   	push   ebp
 80491ae:	89 e5                	mov    ebp,esp
 80491b0:	53                   	push   ebx
 80491b1:	83 ec 04             	sub    esp,0x4
 80491b4:	e8 80 00 00 00       	call   8049239 <__x86.get_pc_thunk.ax>
 80491b9:	05 47 2e 00 00       	add    eax,0x2e47
 80491be:	83 ec 0c             	sub    esp,0xc
 80491c1:	8d 90 11 e0 ff ff    	lea    edx,[eax-0x1fef]
 80491c7:	52                   	push   edx
 80491c8:	89 c3                	mov    ebx,eax
 80491ca:	e8 71 fe ff ff       	call   8049040 <puts@plt>
 80491cf:	83 c4 10             	add    esp,0x10
 80491d2:	90                   	nop
 80491d3:	8b 5d fc             	mov    ebx,DWORD PTR [ebp-0x4]
 80491d6:	c9                   	leave  
 80491d7:	c3                   	ret   
```


the function just call the puts function to print "Ooop!! Try again"
there is no string reference from objdump for this string "Ooop!! Try again" but it does put it
 
 now we need to know how to overflow the 'f' function, how many bytes it needs to overwrite the EIP, and later we will check where we need to redirect the program to.
 
 okay:
 lets overflow the 'f' function with a pattern and run it on gdb and check the EIP.
 
 here is the pattern:
`"Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag"`
 
 and when we run it under gdb the program crashes and EIP is at "0x35624134" equivalent to "4Ab5" in the pattern.
 and checking on the pattern how many bytes are there to this "4Ab5" the number of bytes is 44 bytes.
 
 so let's make a payload that redirect us to "Greats!!" message
 
 here is the function we need to redirect to which is the "__s_func".
 
 `objdump -d -M intel crackme | grep \<__s_func\>\: -A 16`

``` 
 08049182 <__s_func>:
 8049182:	55                   	push   ebp
 8049183:	89 e5                	mov    ebp,esp
 8049185:	53                   	push   ebx
 8049186:	83 ec 04             	sub    esp,0x4
 8049189:	e8 ab 00 00 00       	call   8049239 <__x86.get_pc_thunk.ax>
 804918e:	05 72 2e 00 00       	add    eax,0x2e72
 8049193:	83 ec 0c             	sub    esp,0xc
 8049196:	8d 90 08 e0 ff ff    	lea    edx,[eax-0x1ff8]
 804919c:	52                   	push   edx
 804919d:	89 c3                	mov    ebx,eax
 804919f:	e8 9c fe ff ff       	call   8049040 <puts@plt>
 80491a4:	83 c4 10             	add    esp,0x10
 80491a7:	90                   	nop
 80491a8:	8b 5d fc             	mov    ebx,DWORD PTR [ebp-0x4]
 80491ab:	c9                   	leave  
 80491ac:	c3                   	ret    
```

so we write a python code for the payload and it will look like this:
something like:
[buffer] + [EIP Address] the EIP address that returns to the '__s_func'

`python -c "print('A' * 44 + '\x82\x91\x04\x08')"`

if you get confused and see that the virtual address of the entry point of '__s_func' doesn't match with the disassembled one.
read more about it here: https://en.wikipedia.org/wiki/Endianness
mainly the addresses in memory are swapped which means if we have a virtual address like this "0x41424344" in the intel based stack it will look like "0x44434241"

then finnaly if we put on the shell something like this, the payload piped with the stdin of the crackme:

`python -c "print('A' * 44 + '\x82\x91\x04\x08')" | ./crackme`

we get the result of:
enter the password:Great !!
