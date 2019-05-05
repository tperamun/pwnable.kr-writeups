Challenge bof (Bufferoverflow)


In this challege, we are to download two files, bof and bof.c

The goal of this challenge is to make "key" equal to "0xcafebabe" so that the if condition satisfies, and we get a shell when connecting to pwnable.kr port 9000 using netcat.


when we look at bof.c, we can immediately see that the **func** function is using the infamous **gets** C method, which many people advice against using. This is because gets never takes an argument to check how many bytes should be copied over, allowing us to put more bytes than the allocated space for the buffer.


It's important that we look at the assembly code for this program as opposed to just the C file, since if we were to draw our stack just looking at the C file, we would allocate 32 bytes for the overflowme buffer. 

However, when we take a look at the machine code, we see that the compiler actually decided to allocate 44 bytes for the overflowme buffer instead. Our overflow wouldn't have worked if we used 32 as our size.


Our stack is now 44 bytes for the buffer, 4 bytes for the stored ebp (right under the buffer), 4 bytes for the return address of main (right under stored ebp), and 4 bytes for the parameter "key" (stored under the return address of main).


The sum of 44 + 4 + 4 = 52 bytes. Meaning that we need to write 52 bytes of whatever we want, and the next 4 bytes will be the "key" parameter in which we need to overwrite with 0xcafebabe.


**NOTE**

We need to use "cat" in order to keep the /bin/sh shell open. If we don't do this, the shell immediately terminates.


Answer:

(python -c "print('\x41' * 52 + '\xbe\xba\xfe\xca')"; cat ) | nc pwnable.kr 9000


This will give us a shell into the pwnable.kr machine. We can now do an "ls" to see what files are there, and do a "cat flag" to view our flag.