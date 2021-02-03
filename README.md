# DME
Arduino Code
//          GONZALO TREUNEN ANAYA       31-06-2020
// DME robot code.
// This code controlls a single stepper motor and makes an LED continuosly blink. 
// The extended code for TWO stepper motors is shown using "/*" inserts.
////////////////////////////////////////
#include <Stepper.h>                                    // Include stepper library
const int stepsPerRevolution = 200;                    // Define steps per revolution (1.8 degrees per step)
Stepper myStepper(stepsPerRevolution, 8, 9, 10, 11);  // Define stepper pins on arduino
int Speed = 0;
int ledState = LOW;                                 // LedState used to set the LED
unsigned long previousMillis = 0;                  // Will store last time LED was updated
long OnTime = 200;                                // Milliseconds of on-time
long OffTime = 200;                              // Milliseconds of off-time
////////////////////////////////////////
void setup() {
  Serial.begin(9600);
  pinMode(5,INPUT);                           // Channel 1 (red) steer mode on controller 
  pinMode(6,INPUT);                          // channel 2 (green) throttle mode on controller 
  pinMode(7,OUTPUT);                        // Relay pin
  pinMode(3,OUTPUT);                       // Led pin 
}
////////////////////////////////////////
void loop() {
////////////////    MOTOR CONTROL   ////////////////////////
  int chan_one = pulseIn(5,HIGH);       // Read PWM of both channels
  int chan_two = pulseIn(6,HIGH);
  
  if (chan_one==0)                    // Channel one returns 0 if signal is lost 
    {digitalWrite(7,LOW);}
  else 
    {digitalWrite(7,HIGH);}

  Speed = map(chan_two,950,2100,0,100);      // reduce range of values 
  if (Speed > 80)
  {myStepper.step(-stepsPerRevolution);
  myStepper.setSpeed(180); }
  
  else if (Speed >60)
  {myStepper.step(-stepsPerRevolution);
  myStepper.setSpeed(80);}
  
  else if (Speed < 40 )
  {myStepper.step(stepsPerRevolution);
  myStepper.setSpeed(80);}    // Clockwise if throttle/ forward is engaged
  
  else if (Speed < 20 )
  {myStepper.step(stepsPerRevolution);
  myStepper.setSpeed(120);}
  else 
  {}
                 // set speed to 100, ony one speed available
  
////////////////    LED   CONTROL   ////////////////////////
  // check to see if it's time to change the state of the LED
  unsigned long currentMillis = millis();
  
  if((ledState == HIGH) && (currentMillis - previousMillis >= OnTime))
  {
    ledState = LOW;                       // Turn it off
    previousMillis = currentMillis;  // Remember the time
    digitalWrite(3, ledState);  // Update the actual LED
  }
  else if ((ledState == LOW) && (currentMillis - previousMillis >= OffTime))
  {
    ledState = HIGH;                      // turn it on
    previousMillis = currentMillis;   // Remember the time
    digitalWrite(3, ledState);    // Update the actual LED
  }
}
