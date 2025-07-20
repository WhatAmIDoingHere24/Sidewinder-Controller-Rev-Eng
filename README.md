# Sidewinder-Controller-Rev-Eng
I came in possesion of a miscrosoft sidewinder controller and flight stick, so i decided to reverse engineer them into keyboard inputs. This repo will walk you through how i did that

## The Controller
To start out i used the game controller first since it has much simpler inputs.

//photo of controller


I wrote this project in python, theres only one library you need, coreUSB will read the input and return some data to us. The first thing you need to do is write the USB handler code. All we are doing is finding the device we want to read data
from and getting that data.

```python

```


```python
idVendor=0x0048b,idProduct=0x0011a 
``` 

These varibles are hexidecimal code that is output form the device to tell the computer what it is. Here i have the code for the controller an dlater i'll show the code for the 
flight stick but if your doing this with another product you'll have to find the vendor/product ID's on your own [Check out this guide (https://www.geeksforgeeks.org/linux-unix/lsusb-command-in-linux-with-examples/)]

I went ahead and mapped the key bidnings as well so i dont forget them. It's a simple system, you can figure out each binding with some easy math but i just made each button a varible with the code attached to it.
When i press the "A" key i get this output

```

```

This is becuase the device outputs user inputs in an array of integers from 0 -> 255, So when i hit a button its not a single number but a Uniqie Identifier of three numbers. 

```
A = [0, 1, 0]
B = [0, 2, 0]
X = [0, 4, 0]
Y = [0, 8, 0]

left
```

This is the code for how i translated the hex coming from the controller into a usable format. Right now the code is still a work in progress but im putting together a CLI tool to control the whople process.

```


```

# Flight Stick
The flgiht stick was the same but i had a lot more trouble deducing how the hex worked since the flight stick has a multi-axis stick so theres
a lot more values to go through.

//photo of flight stick

```
//flight stick mapping

```
and heres the current code for what it can do

```

```





# TODO
- Build CLI Tool to be able to map bindings
- make it a daemon so it works in the background
- be able to switch from controller to flight stick with a single binding and use them at the same time
  
