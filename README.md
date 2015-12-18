# Arduino_Xmas_Lights
A quick experimental project for some Christmas tree lights.

This code can feed your lights PWM analog signals through as many Analog outs your device has.
This means you can control up to eight seperate strips of lights using this code. Simply
edit the code to plug in your extra channels and go. The code should auto-scale to match with
no further changes required.

In the header of the INO file you will see there are a lot of values you can change to setup the file.
You should first change the PIN allocations you set for the analog outputs of your Arduino in
line 19. You should directly state the Analog pin reference. i.e. Pins 3 and 5 in the example below.
>>>int  myPins[] = {3, 5}; //Up to 9 pin allocations!

Lines 15 to 42 contain the entire setup variables you should be looking to change.
Everything is well commented and should be understandable.
Outwith those lines you could add modules and pattern generators if that takes your fancy, it doesn't take mine.


Any issues, contact the original author at http://robertv.co.uk/help for advice.
