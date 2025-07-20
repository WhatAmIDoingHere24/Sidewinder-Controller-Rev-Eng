# Sidewinder-Controller-Rev-Eng
I came in possesion of a miscrosoft sidewinder controller and flight stick, so i decided to reverse engineer them into keyboard inputs. This repo will walk you through how i did that

## The Controller
To start out i used the game controller first since it has much simpler inputs.

//photo of controller


I wrote this project in python, theres only one library you need, coreUSB will read the input and return some data to us. The first thing you need to do is write the USB handler code. All we are doing is finding the device we want to read data
from and getting that data.

```python
import usb.core
import time
import subprocess
import math

dev=usb.core.find(idVendor=0x045e,idProduct=0x0027) #looks for the device that has that product and vendor ID

ep=dev[0].interface()[0].endpoint()[0] #access the USB device list

i=dev[0].interfaces()[0].bInterfaceNumber #the interface that has the speciffied device

dev.reset() //reset the device

if dev.is_kernel_driver_active(i):
  dev.detach_kernel_driver(i) #if the device is connected remove it from giving inputs to the computer and locking up

print("device connected\n")

dev.set_configuration() #configure device to output data
eaddr=ep.bEndpointAddress #the endpoint address of the device

userInput=dev.read(eaddr,2048,200) #read the data, This only reads it once so i keep it in a while loop to always read data
```
Below is the input mapping for the controller

```
nothing = [63, 0, 63]

joystickUp = [63, 0, 0]
joystickDown = [63, 127, 0]
joystickLeft = [0, 63, 0]
joystickRight = [127, 0, 0]

A = [63, 63, 1]
B = [63, 63, 4]
X = [63, 63, 8]
Y = [63, 63, 2]

triggerLeft = [63, 63, 16]
triggerRight = [63, 63, 32]

```


```python
idVendor=0x045e,idProduct=0x0027 
``` 

These varibles are hexidecimal code that is output form the device to tell the computer what it is. Here i have the code for the controller an dlater i'll show the code for the 
flight stick but if your doing this with another product you'll have to find the vendor/product ID's on your own [Check out this guide (https://www.geeksforgeeks.org/linux-unix/lsusb-command-in-linux-with-examples/)]

I went ahead and mapped the key bidnings as well so i dont forget them. It's a simple system, you can figure out each binding with some easy math but i just made each button a varible with the code attached to it.
When i press the "A" key i get this output

```
array["B", 63, 63, 1]
```

This is becuase the device outputs user inputs in an array of integers from 0 -> 255, So when i hit a button its not a single number but a Uniqie Identifier of three numbers. We can disregard the "B" it dosnt do anything 

```

A = [0, 1, 0]
B = [0, 2, 0]
X = [0, 4, 0]
Y = [0, 8, 0]

joystickLeft = [0, 63, 0]
joystickRight = [127, 0, 0]
joystickUp = [63, 0, 0]
joystickDown = [63, 127, 0]

triggerLeft = [63, 63, 16]
triggerRight = [63, 63, 32]

```

This is the code for how i translated the hex coming from the controller into a usable format. Right now the code is still a work in progress but im putting together a CLI tool to control the whople process.

```
inputRecord = [] //a score of all inputs form the user for a session

for x in range(10):
  inputRecord.append("A") //pad the input record with false data
  inputRecord.append("000")

while True:
    
    startTime = time.time()

    userInput=dev.read(eaddr,2048,3000)


    if userInput[2] == 1:
        #print("A")
        inputRecord.append("A")
        inputRecord.append(startTime)

    if userInput[2] == 2:
        #print("B")
        inputRecord.append("B")
        inputRecord.append(startTime)


    if userInput[2] == 4:
        #print("X")
        inputRecord.append("X")
        inputRecord.append(startTime)
        

    if userInput[2] == 8:
        #print("Y")
        inputRecord.append("Y")
        inputRecord.append(startTime)
        

    if userInput[2] == 16:
        #print("triggerLeft")
        inputRecord.append("triggerLeft")
        inputRecord.append(startTime)


    if userInput[2] == 32:
        #print("triggerRight")
        inputRecord.append("triggerRight")
        inputRecord.append(startTime)
        

    if userInput[0] == 63 and userInput[1] == 0 and userInput[2] == 0:
        #print("joystickUp")
        inputRecord.append("joystickUp")
        inputRecord.append(startTime)
        

    if userInput[0] == 63 and userInput[1] == 127 and userInput[2] == 0:
        #print("joystickDown")
        inputRecord.append("joystickDown")
        inputRecord.append(startTime)
        

    if userInput[0] == 0 and userInput[1] == 63 and userInput[2] == 0:
        #print("joystickLeft")
        inputRecord.append("joystickLeft")
        inputRecord.append(startTime)
    

    if userInput[0] == 127 and userInput[1] == 0 and userInput[2] == 0:
        #print("joystickRight")
        inputRecord.append("joystickRight")
        inputRecord.append(startTime)


    #if userInput[0] == 63 and userInput[1] == 63 and userInput[2] == 0:
        #print("no input")

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
  
