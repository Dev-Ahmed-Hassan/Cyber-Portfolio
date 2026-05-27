Yooo... my first attempt at reverse engineering... lets goo....

Since this is my very first attempt, so i obviously don't know the basics and stuff.

That is why I am following a yt tuitorial and documenting what I did ... 

here is the link to the video... do check it out :) https://www.youtube.com/watch?v=sZnOEcCdLUo

once i get an understanding then I plan to crack these crack me softwares on my own.

well lets get started...

i am using the software x32/x64 dbg for debugging it and stuff lets see how that goes.


here is the link to the crack me software that I am gonna crack tonight...

https://crackmes.one/crackme/5ab77f5933c5d40ad448c457


yeh so the password for the encrypted file is like crackmes.one 


Ok watched the tuitorial lets get it done without it....

My main goal is to also integrate images into the readme files I will name my screenshots as 1.png 2.png... and so on... and it would be in the /images folder so when I say a screenshot.. you sequencially increase the screenshot number and embed it.. this is a small step to kind of automate one thing at least

so first thing's first, locating the program {SS, showing the file in windows explorer} and start it..

it will ask for your name and serial no... and will return False serial no. when we get it wrong... then the program shuts down.

{SS, of the console outputting the following text}

Name:
Ahmed
Serial:
12345
False Serial.
Press any key to continue . . .


now we know that it outputs onto the console... and for that it usually uses a system call via msvcrt (basically it is a library that handles the basic system tasks so that devs don't have to write them from the scratch and system is well known, it allows to execute commands as if you were typing them on cmd) {explain these a little bit}


so after knowing this.. we launch our debugger... 

{SS, showing the empty debugger window freshly opened!}



drag the file into it so that it starts running in the debug mode... 
 {SS, showing the code in the debugger after dragging in the file}


Now we have the set up ready lets move forward...

Initially we are set up on a breakpoint that is the program start point..  if we look at the breakpoints tab... it would show as the program entry point 
{SS, showing the program entry point}


lets find the output service first so that it gets us to the main code being executed... like that would lead us to the finding

so there are two ways to get to the main code here... we will go through both of them


1. search for the msvcrt.system in search for -> All User Modules -> Intermodullar calls   {SS showing these menus}  

now search for msvcrt.system and it will find the system call and lead you to the code where this call happens.
{ss, showing we searched for this service and it returned some line}

click the found call {SS, showing this line}

As you can see, we have reached the problematic lines... sort of... if we look up a bit, we get the False Serial message strings etc too... we will get to that later but first lets see the second method that I personally find a better approach somehow...

2. search for the exact string it outputs "False Serial" in the search for -> All Modules -> String references {SS showing the options}

now search for the string "False Serial"{SS, showing the searched string and found references}


click the found reference and it will lead you to the code lines..


Either ways... you will end up in the same section of code  {ss, showing false serial line}

Now We have to examine our way through the program...

{SS, showing the code block of our intereset... }


0040148B | 8B45 D4                  | mov eax,dword ptr ss:[ebp-2C]           |
0040148E | 3B45 D0                  | cmp eax,dword ptr ss:[ebp-30]           |
00401491 | 74 24                    | je crackme#1 by pride.4014B7            |
00401493 | C74424 04 0E004400       | mov dword ptr ss:[esp+4],crackme#1 by p | 44000E:"False Serial."
0040149B | C70424 C0334400          | mov dword ptr ss:[esp],crackme#1 by pri |
004014A2 | E8 51AD0300              | call crackme#1 by pride.43C1F8          |
004014A7 | C74424 04 C8AF4300       | mov dword ptr ss:[esp+4],crackme#1 by p |
004014AF | 890424                   | mov dword ptr ss:[esp],eax              |
004014B2 | E8 998C0200              | call crackme#1 by pride.42A150          |
004014B7 | 8B45 D4                  | mov eax,dword ptr ss:[ebp-2C]           |
004014BA | 3B45 D0                  | cmp eax,dword ptr ss:[ebp-30]           |
004014BD | 75 4B                    | jne crackme#1 by pride.40150A           |
004014BF | C74424 04 1C004400       | mov dword ptr ss:[esp+4],crackme#1 by p | 44001C:"Right Serial."
004014C7 | C70424 C0334400          | mov dword ptr ss:[esp],crackme#1 by pri |
004014CE | C745 A0 01000000         | mov dword ptr ss:[ebp-60],1             |
004014D5 | E8 1EAD0300              | call crackme#1 by pride.43C1F8          |
004014DA | C74424 04 C8AF4300       | mov dword ptr ss:[esp+4],crackme#1 by p |
004014E2 | 890424                   | mov dword ptr ss:[esp],eax              |
004014E5 | E8 668C0200              | call crackme#1 by pride.42A150          |
004014EA | C74424 04 2A004400       | mov dword ptr ss:[esp+4],crackme#1 by p | 44002A:"Now make a KeyGen"
004014F2 | 890424                   | mov dword ptr ss:[esp],eax              |
004014F5 | E8 FEAC0300              | call crackme#1 by pride.43C1F8          |
004014FA | C74424 04 C8AF4300       | mov dword ptr ss:[esp+4],crackme#1 by p |
00401502 | 890424                   | mov dword ptr ss:[esp],eax              |
00401505 | E8 468C0200              | call crackme#1 by pride.42A150          |
0040150A | C70424 3C004400          | mov dword ptr ss:[esp],crackme#1 by pri | 44003C:"PAUSE"



now we look at the line of Wrong Serial... we want to get to that point.. so for that to happen we have to see what leads to this condition..

the statement above it jumps to another line if the above comparision results in equal...


ok... so what is the comparision... lets find out...

cmp eax,dword ptr ss:[ebp-30]


it is comparing the eax register with some value in teh stack at address edp-30....  Okay...

lets see what is in teh eax at this point... we put a break point at the jump line so that it stops there and lets us examine the eax register at that point... 


after the break point... run the program... 

run until we reach the breakpoint... and at the breakpoint... check the eax register to see what is the eax value at that time...

{SS, showing the eax value as 12345 same as the serial we entered!}

so we find out that eax stores the serial number we enter and it is comparing it with some value in the stack... lets backtrack on that and see what is in the stack...


 


0040143F | 890424                   | mov dword ptr ss:[esp],eax              |
00401442 | E8 090E0100              | call crackme#1 by pride.412250          |
00401447 | 05 CA000000              | add eax,CA                              |
0040144C | 35 0FD4D803              | xor eax,3D8D40F                         |
00401451 | 8945 D0                  | mov dword ptr ss:[ebp-30],eax           |
00401454 | C74424 04 06004400       | mov dword ptr ss:[esp+4],crackme#1 by p | [esp+4]:"90", 440006:"Serial:"
0040145C | C70424 C0334400          | mov dword ptr ss:[esp],crackme#1 by pri |
00401463 | E8 90AD0300              | call crackme#1 by pride.43C1F8          |
00401468 | C74424 04 C8AF4300       | mov dword ptr ss:[esp+4],crackme#1 by p | [esp+4]:"90"
00401470 | 890424                   | mov dword ptr ss:[esp],eax              |
00401473 | E8 D88C0200              | call crackme#1 by pride.42A150          |
00401478 | 8D45 D4                  | lea eax,dword ptr ss:[ebp-2C]           |
0040147B | 894424 04                | mov dword ptr ss:[esp+4],eax            | [esp+4]:"90"
0040147F | C70424 60344400          | mov dword ptr ss:[esp],crackme#1 by pri |
00401486 | E8 356E0200              | call crackme#1 by pride.4282C0          |
0040148B | 8B45 D4                  | mov eax,dword ptr ss:[ebp-2C]           |
0040148E | 3B45 D0                  | cmp eax,dword ptr ss:[ebp-30]           |
00401491 | 74 24                    | je crackme#1 by pride.4014B7            |
00401493 | C74424 04 0E004400       | mov dword ptr ss:[esp+4],crackme#1 by p | [esp+4]:"90", 44000E:"False Serial."





few lines above we see these three lines of our interest


call crackme#1 by pride.412250          
add eax,CA                              
xor eax,3D8D40F
mov dword ptr ss:[ebp-30],eax\


so it is using previously stored value in eax and adding 0xCA in it and xoring it with 0x3D8D40F and putting it in the stack at that location...

but what was in the eax previously? 

lets follow the function call then maybe it will lead us somewhere

{SS, showing the function call we are following...}

00412250 | 55                       | push ebp                                |
00412251 | 89E5                     | mov ebp,esp                             |
00412253 | 8B45 08                  | mov eax,dword ptr ss:[ebp+8]            |
00412256 | 5D                       | pop ebp                                 |
00412257 | 8B00                     | mov eax,dword ptr ds:[eax]              |
00412259 | 8B40 F4                  | mov eax,dword ptr ds:[eax-C]            |
0041225C | C3                       | ret                                     |

so we can't guess it by the function alone it is kind of pointless so lets look at the eax value before the add and xor operations to guess what is happening 


add a breakpoint on the add eax, CA line and run the program again and see the eax value 

so the eax value is 3 for the name: ALI {SS, showing this}
lets do this again... 

so the eax value is 5 and we inputted the Name : Ahmed
{ss, showing this}



since this is an easy level crack me, we can guess that the eax stores the length of the name then matches it with the add and xor operations...


lets verify this... 

now to put the serial key for Ahmed we would have to 

mov eax, 5          ; EAX = 5 (Hex: 00000005)
add eax, CA         ; EAX = 207 (Hex: 000000CF)
xor eax, 3D8D40F    ; EAX = 64541888 (Hex: 03D8D4C0)


now we put it in the program and it outputs

Name:
Ahmed
Serial:
64541888
Right Serial.
Now make a KeyGen
Press any key to continue . . .


{SS, shwoing this output}

Alright more than half the work is done... lets create a very simple python script to output the keygen serial for a name


make a new file on your device called keygen.py and paste this code in it

# 1. Get the user's input name
name = input("Enter the Name: ")

# 2. Get the length of the string and store it in a distinct variable
name_length = len(name)

# 3. Perform the addition (0xCA is 202 in decimal)
name_length += 0xCA

# 4. Perform the bitwise XOR operation with the corrected hex prefix
name_length ^= 0x3D8D40F

# 5. Print the final calculated serial key
print("Your Serial Key (Decimal):", name_length)
print("Your Serial Key (Hex):", hex(name_length).upper())
input("") 


{SS, showing the working of the keygen}



now you have your keygen for basically all the names and thus we conclude our lesson






