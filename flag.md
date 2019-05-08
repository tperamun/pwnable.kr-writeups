Challenge flag.


This task was a reverse engineering task. If we tried to run `gdb flag` , we'll run into an error. This is because the executable is packed using UPX. We know this because we can run `strings flag` and we can see a bunch of "UPX!" ASCII text in the output that indicated to us that it was packed using upx. 



We can run `upx -d flag` to unpack the executable to it's original form, and then run `gdb flag` once again. This works perfectly. 

The output of the executable when running `./flag`  "I will malloc() and strcpy the flag there. take it".

This gives us a hint. What this program most likely does is copy the flag into some memory in the heap, and then return that pointer that points to our flag.


Which means the returned pointer is likely to be in the `rax` register, since returned values are usually stored in that register. 


So what we can do is, run gdb on this executable until the the function epilogue is executed and `main`'s `return 0` (which is `mov eax, 0x00`) is executed. We want to stop at the line *right before* it runs the mov instruction because we don't want it to overwrite our `eax` register since this register is what holds the pointer to our flag.


We can now stop right before this line, and see what's inside the registers so far using `info registers`. We can specifically look at what is held by the register by doing `display $eax`.

We can use the value outputted by this command, and view the memory map of the next 64 bytes by doing `x /20x $rax` which will show us the content that the pointer in rax is pointing to. We can scan this memory map until we see a `00` which denotes a null terminator.

**NOTE**

Intel systems are little endian, and gdb shows us the memory map in little endian also. So when we view this memory map, we must convert everything to big endian before we convert the hex string to an ASCII string. After converting this big endian hex string to ASCII, we get our flag.
