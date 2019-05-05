Challenge col (Collision)



This challenge is about creating a "hash collision" although it's not reaaaally a hash collision.

The solution to this challenge lies in the check_password function. This is where the supplied 20 byte string gets casted from a char * to an int *


This detail is very important, since now when we dereference inside the loop, it will no longer refer to just 1 byte (char occupy 1 byte), it will now refer to 4 bytes (int occupy 4 bytes)


In order to solve this problem, we need to supply 20 bytes of string which will sum to the hashcode 0x21DD09EC (which is 568134124 in decimal).



We could do 568134124/4, but this will only give us 16 bytes. The loop runs 5 times, and an int occupies 4 bytes, therefore we need 5 x 4 = 20 bytes.



We could do 568134124/5 = 113626825 (rounded up)

Meaning, 113626825 * 4 = 454507300

So the remaining 4 bytes can come from 568134124 - 454507300 = 113626824


So the numbers we are interested in is 113626825 and 113626824

we multiply 113626825 * 4 + 113626824 = 568134124, which is the answer we are looking for.



**Note**

Intel systems are little endian, so we need to make sure to write these values in hex little endian representation.


113626825 in hex = 0x06c5cec9
113626824 in hex = 0x06c5cec8


so our final command is:

./col \`python -c "print('\xc9\xce\xc5\x06\xc9\xce\xc5\x06\xc9\xce\xc5\x06\xc9\xce\xc5\x06\xc8\xce\xc5\x06')"\`



We use a python inline script to provide the raw bytes