////////////////////////////////////////////////////////////////////////////////////////////////////////
// https://github.com/Wisnaeme/Arduino_Xmas_Lights
// Christmas tree light flasher project.
//
// Drive Arduino Analog outputs with PWM to create
// pulsing, flickering and dimming. No patterns as they
// get repetitive (!) and boring.
//
// All channels are Pseudo-Random and never repeating
// "patterns" which look and feel much better than fixed
// pattern(repeating) types found elsewhere.
// 17th December 2015.
// Code as supplied by Robert who hides near http://robertv.co.uk
// Code was written and verified by Arduino PC editor version 1.6.5
////////////////////////////////////////////////////////////////////////////////////////////////////////
int ver = 1; //Just used for flashes at power on. Useful for revisioning.
int change = 5;     // How much the brightness will change per cycle.
int maxbright = 100; //The Maximum PWM in normal feed. Override can take to 255 for a few mS randomly.
int minbright = 1; //The Maximum PWM in normal feed. Override can take to 255 for a few mS randomly.
int on = 100;       //ON is used as a value to determine the delay between steps in mS.
int override = false; //User to determine if an Override value is present on the outputs.
////////////////////////////////////////////////////////////////////////////////////////////////////////
// Adjusting the next three values will GREATLY change the output patterns.
// For Mostly off with infrequent intense flashes, set 90,2,2.
// For always on as soft fade with infrequent intense flashes, set 0,2,5.
// For basic fade with very infrequent soft flashes override set 0,1,0.
int ChanceOfBlank = 5; //A percentage chance of a cycle being forced to brightness 0
int ChanceOfHalf = 0; //A percentage chance of a cycle being forced to brightness 128
int ChanceOfFull = 1; //A percentage chance of a cycle being forced to brightness 255
////////////////////////////////////////////////////////////////////////////////////////////////////////
int StepDown = 1; //A percentage chance of a cycle being forced to step all channels to the one below.
int Slowest = 200; //mS as a maximum delay between steps. Try to stay below 500mS or it feels very slow.
int Fastest = 2; //mS as a minimum delay between steps.
////////////////////////////////////////////////////////////////////////////////////////////////////////
// Use these values to set the Analog Output pins we're using.
// Repeatin as say {3,5,3,5,6,7} will cause pins 3 and 5 to have two values per sequence.
// This will cause micro-flicker between the two values in the myBright array. Interesting effect!
int  myPins[] = {3, 5}; //Up to 9 pin allocations!
// Get an INT with the amount of channels in the myPins array.
// Ensure the pinCount can't go above 10.
int pinCount = sizeof(myPins) / sizeof(int);
int myBright[10]; //The brightness of each channel will be stored here.
////////////////////////////////////////////////////////////////////////////////////////////////////////
void setup() {
  delay(1000); //Pause at power on.
  // Setup the PINMODE for all pins in use. Sets each PWM pin selected above to Output.
  for (int pin = 0; pin < pinCount; pin++)
  {
    pinMode(pin, OUTPUT);
  }
  ////////////////////////////////////////////////////////////////////////////////////////////////////////
  //Send Version number. Flashes version number "times" at power on.
  SendVersion();
  ////////////////////////////////////////////////////////////////////////////////////////////////////////
  //Set the Brightness values to 0
  for (int pin = 0; pin < pinCount; pin++)
  {
    myBright[pin] = 0;
  }
}
////////////////////////////////////////////////////////////////////////////////////////////////////////
void loop() {
  //Adjust the ON time randomly by up to 10 points. This is the delay and time to show that brightness.
  on = on + (random(-10, 11));
  //Sanitise the ON value to hard limits. Preferring a middle value.
  //Stops the time become too slow and bang it back to mid-speed.
  if (on > Slowest) on = Slowest - ((Slowest - Fastest) / 2);
  if (on < Fastest) on = Fastest;
  ////////////////////////////////////////////////////////////////////////////////////////////////////////
  //Sequence step the channels randomly to add channel stepping
  if (random(0, 100) <= StepDown) //Swap the channels.
  {
    Serial.println("Stepping left...");
    int temp = myBright[0]; //Store the lowest PIN's value.
    for (int pin = 0; pin < pinCount; pin++)
    {
      myBright[pin] = myBright[pin + 1]; //Slip the PIN designation one to the lower.
    }
    myBright[pinCount - 1] = temp; //Set the Highest PIN to the stored value that was the lowest PINS value.
  }
  ////////////////////////////////////////////////////////////////////////////////////////////////////////
  // Randomly change the brightness of the PINS. Change every cycle. Never static.
  if (random(1, 3) == 1)
  {
    for (int pin = 0; pin < pinCount; pin++)
    {
      if (random(1, 3) == 1) {
        myBright[pin] = myBright[pin] + change;
      } else {
        myBright[pin] = myBright[pin] - change;
      }
    }
  }
  ////////////////////////////////////////////////////////////////////////////////////////////////////////
  // Send the output to the PINS
  override = false; //Reset the override value.
  for (int pin = 0; pin < pinCount; pin++)
  {
    ////////////////////////////////////////////////////////////////////////////////////////////////////////
    //Sanitise brightness values to fix hard end stops.
    if (myBright[pin] > maxbright) myBright[pin] = maxbright;
    if (myBright[pin] < minbright) myBright[pin] = minbright;
    analogWrite(myPins[pin], myBright[pin]);
    ////////////////////////////////////////////////////////////////////////////////////////////////////////
    // Allow some hard coded random override to this cycle, but do not change the value
    // Twinkle setting. Lower value in random to twinkle more!
    if (random(1, 101) <= ChanceOfBlank) {
      Serial.print("BLANK: "); Serial.println(myPins[pin]);
      analogWrite(myPins[pin], 0);   //BLANK the channel randomly.
      delay(2);
      override = true;
      continue;
    }
    if (random(1, 101) <= ChanceOfFull) {
      Serial.print("FULL: "); Serial.println(myPins[pin]);
      analogWrite(myPins[pin], 255);  //FULL POWER the channel randomly.
      delay(2);
      override = true;
      continue;
    }
    if (random(1, 101) <= ChanceOfHalf) {
      Serial.print("HALF: "); Serial.println(myPins[pin]);
      analogWrite(myPins[pin], 128);  //HALF POWER the channel randomly.
      delay(2);
      override = true;
      continue;
    }
    ////////////////////////////////////////////////////////////////////////////////////////////////////////
    // This delay is a micro-delay to help show micro-flicker when a pin is
    // allocated more than one channel in the code.
    delay(1);
  }
  ////////////////////////////////////////////////////////////////////////////////////////////////////////
  // Delay to the next cycle but... Skip delay if override is true....
  delay(on);
  // Send some diagnostics through the serial
  SerSend();
}
////////////////////////////////////////////////////////////////////////////////////////////////////////
void SerSend()
{
  Serial.print("\t\t\t");
  for (int pin = 0; pin < pinCount; pin++)
  {
    //Send the pin Brightness to the serial.
    // Does not show Override reading!
    Serial.print(pin);
    Serial.print(":");
    Serial.print(myPins[pin]);
    Serial.print(":");
    Serial.print(myBright[pin]);
    Serial.print("\t");
  }
  //Send the ON value
  Serial.print(on);
  Serial.print("\t");
  Serial.println(override);
}
////////////////////////////////////////////////////////////////////////////////////////////////////////
void SendVersion()
{
  // Send rapid "power on" flicker to all channels for a second or so.
  for (int temp = 1; temp <= 30; temp++)
  {
    for (int pin = 0; pin < pinCount; pin++)
    {
      analogWrite(myPins[pin], 64);
    }
    delay(5);
    for (int pin = 0; pin < pinCount; pin++)
    {
      analogWrite(myPins[pin], 0);
    }
    delay(35);
  }
  delay(1000);
  ////////////////////////////////////////////////////////////////////////////////////////////////////////
  // Send the Setup Details through serial:
  Serial.print("Xmas Lights V");
  Serial.println(ver);
  Serial.print("Channels in use: ");
  Serial.println(pinCount);
  delay(2000); //A dark delay before we get the show running...
  // Count out the version number on all channels.
  for (int temp = 1; temp <= ver; temp++)
  {
    for (int pin = 0; pin < pinCount; pin++)
    {
      analogWrite(myPins[pin], 255);
    }
    delay(100);
    for (int pin = 0; pin < pinCount; pin++)
    {
      analogWrite(myPins[pin], 0);
    }
    delay(200);
  }
  delay(1000);
}
