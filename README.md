mpm503
======
//I CAN HEAR THE BELLS
//Evin, Nicole, Robin, Melissa
//This code makes a bell ring when a pencil drawing is touched

#include <Glue.h>
Glue glue;
#include <Scissors.h>
Scissors cut;

#include <Servo.h>
Servo myservo;


int SensorPin=2;
int pressure=0;
int MappedPressure=0;
int touchedCutoff=60;

int pos=0;
int VALUE=0;
int counter=0;
int AlarmOn=0;
int StopCounter=0;
int AnotherCounter=0;


void setup(){
  Serial.begin(9600);
  cut.begin(19200);
  glue.create();

}

void loop(){

  if (readCapacitivePin(SensorPin)>touchedCutoff){
    AlarmOn=1;
  }
  else{
    StopAlarm();
  }
  if (AlarmOn==1){
    SoundAlarm();
    counter++;
  }
  if((AlarmOn==1) && counter>20){
    StopAlarm();
    AlarmOn=0;
    counter=0;
  }


  //http://www.instructables.com/id/Turn-a-pencil-drawing-into-a-capacitive-sensor-for/?ALLSTEPS&fb_source=message
  //The following code helped to create a touch sensor instead of a capacitive sensor

  // Every 500 ms, print the value of the capacitive sensor
  if ( (millis() % 500) == 0){
  }
}

// readCapacitivePin
//  Input: Arduino pin number
//  Output: A number, from 0 to 17 expressing
//          how much capacitance is on the pin
//  When you touch the pin, or whatever you have
//  attached to it, the number will get higher
//  In order for this to work now,
// The pin should have a 1+Megaohm resistor pulling
//  it up to +5v.
uint8_t readCapacitivePin(int pinToMeasure){
  // This is how you declare a variable which
  //  will hold the PORT, PIN, and DDR registers
  //  on an AVR
  volatile uint8_t* port;
  volatile uint8_t* ddr;
  volatile uint8_t* pin;
  // Here we translate the input pin number from
  //  Arduino pin number to the AVR PORT, PIN, DDR,
  //  and which bit of those registers we care about.
  byte bitmask;
  if ((pinToMeasure >= 0) && (pinToMeasure <= 7)){
    port = &PORTD;
    ddr = &DDRD;
    bitmask = 1 << pinToMeasure;
    pin = &PIND;
  }
  if ((pinToMeasure > 7) && (pinToMeasure <= 13)){
    port = &PORTB;
    ddr = &DDRB;
    bitmask = 1 << (pinToMeasure - 8);
    pin = &PINB;
  }
  if ((pinToMeasure > 13) && (pinToMeasure <= 19)){
    port = &PORTC;
    ddr = &DDRC;
    bitmask = 1 << (pinToMeasure - 13);
    pin = &PINC;
  }
  // Discharge the pin first by setting it low and output
  *port &= ~(bitmask);
  *ddr  |= bitmask;
  //delay(1);
  // Make the pin an input WITHOUT the internal pull-up on
  *ddr &= ~(bitmask);
  // Now see how long the pin to get pulled up
  int cycles = 16000;
  for(int i = 0; i < cycles; i++){
    if (*pin & bitmask){
      cycles = i;
      break;
    }
  }
  // Discharge the pin again by setting it low and output
  //  It's important to leave the pins low if you want to 
  //  be able to touch more than 1 sensor at a time - if
  //  the sensor is left pulled high, when you touch
  //  two sensors, your body will transfer the charge between
  //  sensors.
  *port &= ~(bitmask);
  *ddr  |= bitmask;

  return cycles;

} 

void SoundAlarm(){
  myservo.attach(10);
  for(pos = 0; pos < 180; pos += 5) 
  {                                 
    myservo.write(pos);          
    delay(10);                      
  } 
  for(pos = 180; pos>=1; pos-=5)     
  {                                
    myservo.write(pos);              
    delay(10);                       
  } 
  counter++;
}

void StopAlarm(){
  myservo.detach();
}




