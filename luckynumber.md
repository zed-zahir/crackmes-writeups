#here is the lucky-number crackme by: oguzbey

we launch it in ida

```
.text:0804903A                 mov     eax, 4
.text:0804903F                 mov     ebx, 1          
.text:08049044                 mov     ecx, offset unk_804A000
.text:08049049                 mov     edx, 0Fh
.text:0804904E                 int     80h     		; syswrite "lucky number: "
.text:08049050                 mov     eax, 3
.text:08049055                 mov     ebx, 2          
.text:0804905A                 mov     ecx, offset byte_804A024
.text:0804905F                 mov     edx, 2
.text:08049064                 int     80h             
.text:08049066                 mov     al, ds:byte_804A024 ; put the input to al		
.text:0804906B                 sub     al, 30h			 ; substract al with 0x30
.text:0804906D                 mov     bl, ds:byte_804A025 ; put the input to bl
.text:08049073                 sub     bl, 30h			 ; substract bl with 0x30
.text:08049076                 adc     al, bl	    ; add bl and al and put it into al
.text:08049078                 daa
.text:08049079                 add     bl, 30h	; add 0x30 to bl
.text:0804907C                 cmp     al, 16h	
.text:0804907E                 jnz     short sub_8049000
.text:08049080                 cmp     bl, 38h
.text:08049083                 jnz     sub_8049000
.text:08049089                 cmp     eax, eax
.text:0804908B                 jz      short loc_804901D
.text:0804908B start           endp
.text:0804908B
.text:0804908B _text           ends
```

our job is to land at .text:0804901D

```
.text:0804901D                 mov     eax, 4
.text:08049022                 mov     ebx, 1 
.text:08049027                 mov     ecx, offset unk_804A00F 
.text:0804902C                 mov     edx, 0Bh
.text:08049031                 int     80h            
.text:08049033                 mov     eax, 1
.text:08049038                 int     80h            
```

if we go with the wrong scenario:
there is an input like 66
the program puts the '6' to AL and '6' to BL
which are at the same time 0x36 ASCII 
then it subtract 0x30 from AL and BL
which they will get the value 0x6 for both
then it adds AL to BL and store the result to AL
which AL will be: 0x6 + 0x6 = 0xC
then it adds 0x30 to bl which will equal to 0x30 + 0x6
which BL will be 0x36
then it compares AL to 0x16
and  it compares BL to 0x38
then we land in the "sorry :(("

the good scenario goes like this:
there is an input like 88
the program puts the '8' to AL and '8' to BL
which are at the same time 0x38 ascii 
then it subtract 0x30 from AL and BL
which will be 0x8 for both
then it adds AL to BL and store the result to AL
which will be: 0x8 + 0x8 = 0x16
then it adds 0x30 to BL which will equal to: 0x30 + 0x8
then it compares al to 0x16
and  it compares bl to 0x38
then we land in the "good job !"

the right answer is '88' to match the result
