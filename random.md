Challenge random.


This exercise is fairly straight forward. From the C program, we can see that in order to get our flag, all we need to do is make `key ^ random == 0xdeadbeef`

However, note how `random` is generataed. It's using the infamous `rand()` function which is known to give the same random number everytime it is executed when no seed  is provided, so it uses 1.

Therefore, all we need to know is our `key` in order to get our flag.

we know that `random ^ 0xdeadbeef = key`

Everytime we run the `rand()` function, the output is always 1804289383.

So `1804289383  ^ 0xdeadbeef = key` and therefore, out `key = 3039230856`

SO now when we run `./random`, we just have to supply `3039230856` as the input to get our flag
