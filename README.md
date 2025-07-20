# Sidewinder-Controller-Rev-Eng
I came in possesion of a miscrosoft sidewinder controller and flight stick, so i decided to reverse engineer them into keyboard inputs. This repo will walk you through how i did that

## The Controller
To start out i used the game controller first since it has much simpler inputs.

//photo of controller


I wrote this project in python, theres only one library you need, coreUSB will read the input and return some data to us. The first thing you need to do is write the USB handler code. All we are doing is finding the device we want to read data
from and getting that data.

//code


''' idVendor=0x0048b,idProduct=0x0011a ''' These varibles are hexidecimal code that is output form the device to tell the computer what it is. Here i have the code for the controller an dlater i'll show the code for the 
flight stick but if your doing this with another product you'll have to find the vendor/product ID's on your own [Check out this guide (https://www.geeksforgeeks.org/linux-unix/lsusb-command-in-linux-with-examples/)], 
