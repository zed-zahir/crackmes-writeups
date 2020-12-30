This is my second writeup.
The challenge link is here: https://ctflearn.com/challenge/379
The first thing I did with this challenge is launching it with “detect it easy” to check if it’s packed or not, it wasn’t, it’s just a simple crackme challenge.
Then I launched it in IDAPro, I read the code which is a tiny code, and I found this function under the name “cek.”
```as
push rbp
mov rbp, rsp
mov [rbp+var_4], edi
mov eax, cs:valid
cmp [rbp+var_4], eax
jnz short loc_4005CF
```
After checking the line “mov eax, cs:valid” which means, move the data under the symbol “valid” located in the code or data segment into the eax register.
I clicked on the valid data I found the value “51615h” and when I convert it to decimal it gives me “333333”
While checking it, it works with “333333”, one thing while I am a Kabyle and I understand Arabic, there was a misconception in the returned string of success or failure, I think the maker of this crackme is Indonesian or Malaysian, and for one of these languages “salah” means “bad” and in Arabic it means “good”
