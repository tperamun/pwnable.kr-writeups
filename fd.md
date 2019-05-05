Challenge fd (File Descriptors)


In this challenge, the line we're interested in is the **int fd = atoi(argv[1]) - 0x1234; **


This line takes whatever the user supplies as an argument to the fd executable, and subtracts it by 0x1234(which is 4660 in decimal)


We know our default file descriptor table

	**0 => stdin
	  1 => stdout
	  2 => stderr
	**


	From this, we can see that 0 is stdin, meaning we want to make sure that our **argv[1]** is also 0x1234 (or 4460 in decimal) so that **int fd** will hold the value 0.


	When the C function **read(fd, buf, 32) ** runs , it will go to the file associated with the value of 0, which is stdin. 


	Therefore, running our program like **./fd 4660** will prompt us for user input, and we then supply **LETMEWIN** as the user input so that the **if** condition satisfies and our flag will be outputted to our screen
