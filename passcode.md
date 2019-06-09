Challenge passcode 


The key to solving this challenge was to notice the way the stack was being reused by `welcome` and then `by login`

The `welcome` function allocates a `char` array of size 100 to store the entered user's name.

When the `login` function gets called, we notice that `int passcode1` and `int passcode2` are not initialized, which means that their initial values are garbage values (perhaps values stored by the previos stack frame like the `welcome` function. 


When we supply a user input of 100 'a's when prompted for the name, we can notice that the last 4 bytes stick around even when we are in the login function, and those last 4 bytes end up being the value held by `passcode1`.

So it seems like we have control over what is held in `passcode1`, however we still do not have control over `passcode2`, which means that we can't pass the `if` check where it checks for both `passcode1 && passcode2`. 


So what if there was a way to bypass this `if` check and jump straight to the `system("/bin/cat flag")` instruction.


We can use the `fflush` command as our victim. However, this function is not known at compile time, so we need to look at the GOT (Global Offset Table). This table basically stores the addresses of the functions that are not known at compile time. For example, functions like `printf`, `scanf`, `fflush`, etc, are known at runtime, and the binary needs to know where to find these functions. So it will go to the GOT to find the address of `fflush`. 


after running the command `objdump -R passcode`, we can see this:
    `0804a004 R_386_JUMP_SLOT   fflush@GLIBC_2.0`
    
    So we see that the address of `fflush` is at `0804a004`
    
    
What if we can overwrite whats at this address? We can make this address point to something else ......like the address of where the instruction to execute `system("/bin/cat flag")` is.


When we look at our binary, we see that this instruction to run `system("/bin/cat flag")` is at the address `0x080485ea`. We will take the address right before this, which is the instruction that moves a value to a location the the `system` function will use. So instead of passing in the address `0x080485ea`, we will pass in `0x080485e3`.


Now when the binary goes to the address `0804a004` to execute `fflush`, it will instead execute the `system` function, which effectively by passes the `if` check.


As you can see, having control of just the `int passcode1` variable was enough for this, we didn't need `int passcode2` also.


**Now the exploit:**

    We will pass in 96 bytes of any random character, and then the other 4 bytes will be the address of `fflush` that will be stored as the value of `passcode1`. After this, we will be prompted to enter the first passcode, and for that, we need to also pass in the address of the `system` function, which will be passed as base10. so the base10 conversion of `0x080485e3` is `137514147`.
    
         The final command looks like:
         

        `python -c "print('t'*96 + '\x04\xa0\x04\x08\n134514147)" | ./passcode`
        
        Which gives us our flag








    
     











