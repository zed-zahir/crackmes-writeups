note: I made a mistake with pastebin code, I put them as temporary instead of permanent, sorry for that guys

Today I will write, my first ever writeup.
Here is the link to the challenge https://ctflearn.com/challenge/743
First I took a brief look at the script, it’s really a pain to read such code, it’s very well obfuscated.
Then I tried to run it, just checking if it really works, and it did.
Then I started using Pycharm to ease my work, I started reformatting it, and it ended up like this one: https://pastebin.com/1XXgT2np
And with some debugging and tracking back all the functions and what do they do while still checking the variable changes of the inputData, I found the last function “EAt()” is the most important of them all.
Then I reversed the code to this one: https://pastebin.com/4Y6cfkxm
While the reversed value returned doesn’t give the complete true value for the challenge, that’s because there is one function that reverses the string of inputData and there is one number lacking while getting the reversed value, I just ended up brute-forcing it and the final value is “341eat009”

