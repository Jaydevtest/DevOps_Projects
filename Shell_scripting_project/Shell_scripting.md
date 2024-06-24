# Introduction to Shell Scripting an User Input
## Shell Scripting Syntax Elements
### 1. Variables:
Bash allows you to define and work with variables. Variables can store data of various types such as numbers, strings, and arrays. You can assign values to variables using the = operator, and access their values using the variable name preceded by a $ sign.

Example: Assigning value to a variable:
`name="John"`

Example: Retrieving value from a variable:
`echo $name`

### 2. Control Flow: 
Bash provides control flow statements like if-else, for loops, while loops, and case statements to control the flow of execution in your scripts. These statements allow you to make decisions, iterate over the lists, and execute different commands based on conditions.

Example: Using if-else to execute script based on conditions
`#!/bin/bash`

Example: Script to check if a number is positive, negative, or zero

``read -p "Enter a number: " num

if [ $num -gt 0 ]; 
then 
  echo "The number is positive"
elif [ $num -lt 0 ];
then
  echo "The number is negative"
else
  echo "The number is zero."
fi``

This code prompts you to type a number and prints a statement stating the number is positive, negative, or zero.

Example: Iterating through a list using a for loop.
``#!/bin/bash

#Example script to print numbers from 1 to 5 using a for loop

for (( i=1; i<=5; i++ ))
do
    echo $i
done``

### 3. Command Substitution:
Command substitution allows you to capture a command's output and use it as a value within your script. You can use the backtick or the $() syntax for the command substitution.

Example: Using backtick for command substitution
`current_date=`date +%Y-%m-%d``

Example: Using $() syntax for command substitution
`current_date=$(date +%Y-%m-%d)`

### 4. Input and Output:
Bash provides various ways to handle input and output. You can use the read command to accept user input, and output text to the console using the echo command. Additionally, you can redirect input and output using operators like > (output to a file), < (input from a file), and | (pipe the output of one command as input to another).

Example: Accept user input
``echo "Enter your name: "
read name``

Example: Output text to the terminal
`echo "Hello World"`

Example: Output the result of a command into a file
`echo "Hello World" > indext.txt`

Example: Pass the content of a file as input to a command
`grep "pattern" < input.txt`

Example: Pass the result of a command as input to another command
`echo "Hello World" | grep "pattern"`

### 5. Funcitons: 
Bash allows you to define and use functions to group related commands together. Functions provide a way to modularize your code and make it more reusable. You can define function using the function keyword or simply declaring the function name followed by parenthesis.

``#!/bin/bash

#Define a function to greet user
greet() {
echo "Hello, $1! Nice to meet you."
}
#Call the greet function and pass the name as an argument
greet "John"``

## First Shell Script

### step 1: 
Open a folder called shell-scripting on your terminal using the command `mkdir-shell-scripting` command. This will hold all the scripts we will write in this lesson.

### step 2: 
Create a file called user-input.sh using the command `touch user-input.sh`

### step 3: 
Inside the file copy and paste the block of code below:

``#!/bin/bash

#Prompt the user for their name
echo "Enter your name: "
read name

#Display a greeting with the entered name
echo "Hello, $name! Nice to meet you."``

### step 4:
save your file

### step 5:
Run the command `sudo chmod +x user-input.sh` to make the file executable

### step 6:
Run the script using the command `./user-input.sh`




















  
