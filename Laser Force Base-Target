#include <EEPROM.h>
#include <Wire.h>
//SETUP OF PINS
int eosID = 520;
int ir_pin = 6;
int redPin = 9;
int greenPin = 11;
int bluePin = 10;
int iColor = 0;
int circlePins[] = {2,3,4,5,7,8,12,13};
int cirLEDPin = 0;
//Command Code Variables
int unitID;
int cCode;
int cPara1;
int cPara2;
int cPara3;

float RGB1[3];
float RGB2[3];
float INC[3];

int redC, greenC, blueC;

int onOFFFLAG = 1;
//OTHER SETUP 
//int debug = 1;
int fadeRatio = 5;

//////////////////////////////////////////
//COMMAND VARIABLES
//LED COLORS
//////////////////////////////////////////
int ledMode = 0; //0=Solid, 1=Blink, 2=Fade, 3=MultiColor Blink, 4=Off
long ledTime = 200; //Last time of LED Operation (blink, fade, multi) ms
long ledDelay = 200; //Time of led delay for operation in ms
//Static Colors
int red=0;
int green=0;
int blue=0;
//Fade
int redFade = 255;
int greenFade = 1;
int blueFade = 1;
int fadeIncrement = 1;
//Blink
int ledBlinkState = 0;
//MultiColor
int redMulti=0;
int greenMulti=0;
int blueMulti=0;

int fadeTime = 50;
//////////////////////////////////////////
//////////////////////////////////////////
//CIRCLE LIGHTING PARAMETERS
int value = LOW;                // previous value of the LED
long previousMillis2 = 0;  
long circleTime = 80;  //40

int irWave = 0;
void setup()
{
  
  pinMode(ir_pin, OUTPUT);
  digitalWrite(ir_pin, LOW);
  int i;
  for (i = 0; i < 8; i = i + 1) {
    pinMode(circlePins[i],OUTPUT);
    digitalWrite(circlePins[i],LOW);
  }
  Serial.begin(9600);
  eepromRead();
}
void loop()
{
   if (Serial.available() > 1) {
    serialProcessing(); 
   }
  if (onOFFFLAG==1) { 
  ledRGB();
  switch (irWave) {
    case 0:
    //TurnOffIR
    break;
    case 56:
    ir56();
    break;
    case 57:
    ir57();
    break;
    case 58:
    ir58();
    break;
    case 59:
    ir59();
    break;  
    case 37:
    ir37();
    break;
    case 38:
    ir38();
    break;
    case 39:
    ir39();
    break;
    case 40:
    ir40();
    break;
  }
  }
}

int serialProcessing() {
  char commandString[15]; // create a string to hold the time value when it's read
  memset(commandString,'\0',15); // initialize that string to all NULL characters
  boolean commandStringValid = false; // declare and initialize a variable to track whether the string has all valid characters
  byte inByte = '\0'; // declare and initialize a byte to read in serial data
  long startTime = millis();//makes the start time = to now
  int timeout = 500; // timeout after one second 1000
  while(millis() - startTime < timeout && inByte != '*') {
    inByte = Serial.read(); // read data and wait for an asterisk character
  }

  if (inByte == '*') { // if we got the correct start character (instead of a timeout)
  
    commandStringValid = true; // declare and initialize a variable to track whether the string has all valid characters
    long startTime = millis();//makes the start time = to now
    int timeout = 500; // timeout after one second
    while(millis() - startTime < timeout && Serial.available() < 14) {
      ; //wait for enough data to be available (14 characters of time string), while doing nothing else
    }
    for (int i=0; i < 14; i++) {
      commandString[i] = Serial.read(); // reach each time string character into a character array
      if(commandString[i] < '0' || commandString[i] > '9') {
        commandStringValid = false;  // if any character is bad then the whole string is bad
      }
    }
    //UNIT ID ////////////////////////////////
    char unitIDString[4];  // create a string to hold the year part of the string
    memset(unitIDString,'\0',4); // initialize that string to all NULL characters
    strncpy( unitIDString, commandString, 3); // copy the first four characters of timeString into the year string
    unitID = atoi(unitIDString); // convert ASCII year string to integer and store in the year integer variable
    
    if (unitID != eosID) {
       cCode = 0;
       cPara1 = 0;
       cPara2 = 0;
       cPara3 = 0;
    if (unitID == 997) {
       onOFFFLAG = 0;
       int i;
       for (i = 0; i < 8; i = i + 1) {
         pinMode(circlePins[i],OUTPUT);
         digitalWrite(circlePins[i],LOW);
       }    
       eepromWrite();
      }   
       return(1);
      }
    
    //COMMAND CODE////////////////////////////////
    char cCodeString[4];  // create a string to hold the year part of the string
    memset(cCodeString,'\0',4); // initialize that string to all NULL characters
    strncpy( cCodeString, commandString+3, 2); // copy the first four characters of timeString into the year string
    cCode = atoi(cCodeString); // convert ASCII year string to integer and store in the year integer variable
 
    //COMMAND CODE PARA 1////////////////////////////////
    char cCodePara1String[4];  // create a string to hold the year part of the string
    memset(cCodePara1String,'\0',4); // initialize that string to all NULL characters
    strncpy( cCodePara1String, commandString+5, 3); // copy the first four characters of timeString into the year string
    cPara1 = atoi(cCodePara1String); // convert ASCII year string to integer and store in the year integer variable
  
  //COMMAND CODE PARA 2////////////////////////////////
    char cCodePara2String[4];  // create a string to hold the year part of the string
    memset(cCodePara2String,'\0',4); // initialize that string to all NULL characters
    strncpy( cCodePara2String, commandString+8, 3); // copy the first four characters of timeString into the year string
    cPara2 = atoi(cCodePara2String); // convert ASCII year string to integer and store in the year integer variable
  
  //COMMAND CODE PARA 3////////////////////////////////
    char cCodePara3String[4];  // create a string to hold the year part of the string
    memset(cCodePara3String,'\0',4); // initialize that string to all NULL characters
    strncpy( cCodePara3String, commandString+11, 3); // copy the first four characters of timeString into the year string
    cPara3 = atoi(cCodePara3String); // convert ASCII year string to integer and store in the year integer variable
    Serial.print("+");
    commandSend(eosID, 3);
    commandSend(cCode, 2);
    commandSend(cPara1, 3);
    commandSend(cPara2, 3);
    commandSend(cPara3, 3);
    Serial.println();
  }
  commandProcessing();
   } 
   
void commandProcessing() {
  switch (cCode) {
    case 20: //SOLID
      allLEDON();
      ledMode = 0;
      red = cPara1;
      green = cPara2;
      blue = cPara3;
      eepromWrite();
      onOFFFLAG=1;
      break;
    case 21: //BLINKING
      allLEDON();
      ledMode = 1;
      red = cPara1;
      green = cPara2;
      blue = cPara3;
      eepromWrite();
      onOFFFLAG=1;
      break;
    case 22: //FADE
      ledMode = 2;
      ledDelay = cPara1;
      fadeRatio = cPara3;
      eepromWrite();
      onOFFFLAG=1;
      break;
    case 23: //MULTI COLOR
      allLEDON();
      ledMode = 3;
      red = cPara1;
      green = cPara2;
      blue = cPara3;
      eepromWrite();
      onOFFFLAG=1;
      break;  
    case 29: //MULTI COLOR PART 2
      redMulti = cPara1;
      greenMulti = cPara2;
      blueMulti = cPara3;
      eepromWrite();
      break; 
    case 24: //BLACK
      ledMode = 4;
      eepromWrite();
      break;  
      case 25: //CHASING WITH CURRENT LIGHTS
      ledMode = 5;
      circleTime = cPara1;
      ledDelay = cPara2;
      eepromWrite();
      onOFFFLAG=1;
      break;   
     case 26: //CHASING WITH FADING COLOR
      ledMode = 6;
      circleTime = cPara1;
      ledDelay = cPara2;
      fadeRatio = cPara3;
      eepromWrite();
      onOFFFLAG=1;
      break;   
      case 30: //LED Time (for blink, etc.)
      if (cPara2 == 2) {
      ledDelay = .001 * ledDelay;
      }
       else {
         ledDelay = cPara1;
      }  
      eepromWrite();
      onOFFFLAG=1;
      break;
    case 31: //CIRCLE TIME
     circleTime = cPara1;
     eepromWrite();
     onOFFFLAG=1;
     break;
   case 40: //IR PATTERN
     irWave = cPara1;
     eepromWrite();
     onOFFFLAG=1;
     break;    
   case 50: //ALL OFF
     //ledMode = 4;
     onOFFFLAG = 0;
     int i;
     for (i = 0; i < 8; i = i + 1) {
       pinMode(circlePins[i],OUTPUT);
       digitalWrite(circlePins[i],LOW);
     }    
     eepromWrite();
     break;       
   case 51: //ALL ON
     onOFFFLAG = 1;
     eepromWrite();
     break;
  case 60: //BASE MODE
      allLEDON();
      ledMode = 1;
      ledDelay = 150;
      switch (cPara1) {
        case 1:
        redColor();
        break;
        case 2:
        greenColor();
        break; 
        case 3:
        blueColor();
        break; 
        case 4:
        purpleColor();
        break;  
        case 5:
        yellowColor();
        break; 
        case 6:
        whiteColor();
        break; 
        case 7:
        aquaColor();
        break;  
      } 
     irWave = cPara2;
      eepromWrite();
      onOFFFLAG=1;
      break;   
case 61: //TARGET MODE
      ledMode = 6;
      circleTime = 70;
      fadeRatio = 1;
      irWave = cPara1;
      eepromWrite();
      onOFFFLAG=1;
      break;    
case 62: //POCAPOCA MODE
      allLEDON();
      irWave = cPara1;
      ledMode = 3;
      redColor;
      eepromWrite();
      onOFFFLAG=1;
      redMulti = 255;
      greenMulti = 255;
      blueMulti = 0;
      ledDelay = 150;
      eepromWrite();
      break;  
     }
  cCode = 0;
  cPara1 = 0;
  cPara2 = 0;
  cPara3 = 0;
  }

void allLEDON(){
int i;
     for (i = 0; i < 8; i = i + 1) {
       pinMode(circlePins[i],OUTPUT);
       digitalWrite(circlePins[i],LOW);
     }    
}  
  
  
void eepromWrite() {
      EEPROM.write(0,ledMode);  
      EEPROM.write(1,red);
      EEPROM.write(2,green);
      EEPROM.write(3,blue);
      EEPROM.write(4,ledDelay);
      EEPROM.write(5,circleTime);
      EEPROM.write(6,irWave);
      EEPROM.write(7,redMulti);
      EEPROM.write(8,greenMulti);
      EEPROM.write(9,blueMulti);
      EEPROM.write(10,onOFFFLAG);  
      EEPROM.write(11,fadeRatio); 
}

void eepromRead() {
      ledMode = EEPROM.read(0);  
      red = EEPROM.read(1); 
      green = EEPROM.read(2); 
      blue = EEPROM.read(3); 
      ledDelay = EEPROM.read(4); 
      circleTime = EEPROM.read(5); 
      irWave = EEPROM.read(6); 
      redMulti = EEPROM.read(7); 
      greenMulti = EEPROM.read(8); 
      blueMulti = EEPROM.read(9); 
      onOFFFLAG = EEPROM.read(10);  
      fadeRatio = EEPROM.read(11);  
}      

int ledRGB() {
  //0=Solid, 1=Blink, 2=Fade, 3=MultiColor Blink, 4=Off
    switch (ledMode) {
    case 0:
      //Solid Mode
      analogWrite(redPin, red);
      analogWrite(greenPin, green);
      analogWrite(bluePin, blue);
        int i;
        for (i = 0; i < 8; i = i + 1) {
    pinMode(circlePins[i],OUTPUT);
    digitalWrite(circlePins[i],LOW);
  }
      break;
    case 1:
      //Blinking Mode
      //Serial.println(millis() - ledTime);
      if (millis() - ledTime > ledDelay) {
      if (ledBlinkState == 0) {
      ledBlinkState = 1;
      analogWrite(redPin, red);
      analogWrite(greenPin, green);
      analogWrite(bluePin, blue);
      }
      else {
      ledBlinkState = 0;
      analogWrite(redPin, 0);
      analogWrite(greenPin, 0);
      analogWrite(bluePin, 0);
      }
      ledTime = millis();
      }
      break;
    case 2: //Fade Mode
       colorFadeMode();
      case 3: //Multicolor Mode
      if (millis() - ledTime > ledDelay) {
      if (ledBlinkState == 0) {
      ledBlinkState = 1;
      analogWrite(redPin, red);
      analogWrite(greenPin, green);
      analogWrite(bluePin, blue);
      }
      else {
      ledBlinkState = 0;
      analogWrite(redPin, redMulti);
      analogWrite(greenPin, greenMulti);
      analogWrite(bluePin, blueMulti);
      }
      ledTime = millis();
      }
      break;
      case 4: //Black Mode
      //Black Mode
      analogWrite(redPin, 255);
      analogWrite(greenPin, 255);
      analogWrite(bluePin, 255);
      break;
     case 5: //CIRCLE WITH CURRENT LED COLOR
      circleLights();
      break;
     case 6: //CIRCLE WITH FADE
      circleLights();
      colorFadeMode();
      break; 
  }
}

void circleLights() {
  int i;
   if (millis() - previousMillis2 > circleTime) {
    previousMillis2 = millis(); 
      for (i = 0; i < 8; i = i + 1) {
    pinMode(circlePins[i],OUTPUT);
    digitalWrite(circlePins[i],LOW);
  }
    digitalWrite(circlePins[cirLEDPin],HIGH);
    cirLEDPin = cirLEDPin + 1;
   }
    if (cirLEDPin > 8) 
    {
      cirLEDPin = 0;
    }
}

void colorFadeMode() {
   if (millis() - fadeTime > ledDelay) {
  
  randomSeed(analogRead(0));
  
  for (int x=0; x<3; x++) {
    INC[x] = (RGB1[x] - RGB2[x]) / 256; }
  
  for (int x=0; x<256; x++) {
    
    redC = int(RGB1[0]);
    greenC = int(RGB1[1]);
    blueC = int(RGB1[2]);

    analogWrite (redPin, redC);  
    analogWrite (greenPin, greenC);  
    analogWrite (bluePin, blueC);    

    for (int x=0; x<3; x++) {
	   RGB1[x] -= INC[x];}
 
  }
  
  for (int x=0; x<3; x++) {
  RGB2[x] = random(956)-700;
  RGB2[x] = constrain(RGB2[x], 0, 255);
  fadeTime = millis();
  }
}
}


/////////////////////COLORS/////////////////////////////////////////////////////
void redColor() {
  red = 255;
  green = 0; 
  blue = 0;
}  
void greenColor() {
  red = 0;
  green = 255; 
  blue = 0;
}
void blueColor() {
  red = 0;
  green = 0; 
  blue = 255;
}
void purpleColor() {
  red = 150;
  green = 0; 
  blue = 255;
}
void yellowColor() {
  red = 255;
  green = 100;
  blue = 0;
}
void whiteColor() {
  red = 255;
  green = 255;
  blue = 255;
}
void aquaColor() {
  red = 0;
  green = 255;
  blue = 255;
}



/////////////////////COLORS/////////////////////////////////////////////////////
void oscillationWrite(int pin, int time) {
  for(int i = 0; i <= time/26; i++) {
    digitalWrite(pin, HIGH);
    delayMicroseconds(6.5);
    digitalWrite(pin, LOW);
    delayMicroseconds(6.5);
  }
  digitalWrite(pin, LOW);
}

void ir56() {
int pulseLength = 160;
int bigSpace = 1980;
int midSpace = 840;
int bigmidSpace = 1370;
int littleSpace = 280;
  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
  delayMicroseconds(bigSpace);
  
  oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
 oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);

   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}

void ir57() {
int pulseLength = 160;
int bigSpace = 1980;
int midSpace = 840;
int bigmidSpace = 1370;
int littleSpace = 280;
  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
     oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace); 
       
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);   
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}
  
  void ir58() {
int pulseLength = 160;
int bigSpace = 1940;
int midSpace = 840;
int bigmidSpace = 1370;
int littleSpace = 280;

  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
     oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace); 
   delayMicroseconds(250); 
       
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   delayMicroseconds(50);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength);   
   delayMicroseconds(bigSpace); 

   
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}

void ir59() {
int pulseLength = 160;
int bigSpace = 2000;
int midSpace = 790;
int bigmidSpace = 1370;
int littleSpace = 280;
  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
     oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace); 
   
     oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
   
   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}
  
void ir60() {
int pulseLength = 160;
int bigSpace = 2000;
int midSpace = 790;
int bigmidSpace = 1370;
int littleSpace = 280;
  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
     oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace); 
   delayMicroseconds(littleSpace); 
      delayMicroseconds(littleSpace);
   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}

void ir61() {
int pulseLength = 160;
int bigSpace = 2000;
int midSpace = 790;
int bigmidSpace = 1370;
int littleSpace = 280;

  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);   
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
      delayMicroseconds(littleSpace);
      
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
    oscillationWrite(ir_pin, pulseLength);
     delayMicroseconds(bigSpace); 
   delayMicroseconds(littleSpace);
   delayMicroseconds(littleSpace);
   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}

void ir62() {
int pulseLength = 160;
int bigSpace = 2000;
int midSpace = 790;
int bigmidSpace = 1370;
int littleSpace = 280;

  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
      
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   delayMicroseconds(littleSpace);
   delayMicroseconds(bigSpace); 

   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}

void ir63() {
int pulseLength = 160;
int bigSpace = 2000;
int midSpace = 790;
int bigmidSpace = 1370;
int littleSpace = 280;
  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
      
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace); 
   delayMicroseconds(bigmidSpace);
   delayMicroseconds(littleSpace);   

   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}
  
 void ir37() {
int pulseLength = 160;
int bigSpace = 2000;
int midSpace = 790;
int bigmidSpace = 1370;
int littleSpace = 280;
  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);   
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);   
   delayMicroseconds(midSpace);     
  delayMicroseconds(littleSpace);
   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
} 

void ir38() {
int pulseLength = 160;
int bigSpace = 2000;
int midSpace = 790;
int bigmidSpace = 1370;
int littleSpace = 280;
  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace); 
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   delayMicroseconds(littleSpace);
      
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);   

   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}
  
void ir39() {
int pulseLength = 160;
int bigSpace = 2000;
int midSpace = 790;
int bigmidSpace = 1370;
int littleSpace = 280;

  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);   

   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}

void ir40() {
int pulseLength = 160;
int bigSpace = 2000;
int midSpace = 790;
int bigmidSpace = 1370;
int littleSpace = 280;

  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);   

   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}

  void ir41() {
int pulseLength = 160;
int bigSpace = 2000;
int midSpace = 790;
int bigmidSpace = 1370;
int littleSpace = 280;

  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
  delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);   
   delayMicroseconds(bigmidSpace);
   delayMicroseconds(35);
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
    oscillationWrite(ir_pin, pulseLength);
    delayMicroseconds(midSpace);
       oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
       oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);

   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);   
    delayMicroseconds(130);
   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}

void ir42() {
  
int pulseLength = 160;
int bigSpace = 1980;
int midSpace = 840;
int bigmidSpace = 1370;
int littleSpace = 280;

  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
  delayMicroseconds(midSpace);
    oscillationWrite(ir_pin, pulseLength);   
   delayMicroseconds(midSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
       oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
    oscillationWrite(ir_pin, pulseLength);
    delayMicroseconds(littleSpace);
       oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
       oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
          oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);

   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}

void ir43() {
int pulseLength = 160;
int bigSpace = 1980;
int midSpace = 840;
int bigmidSpace = 1370;
int littleSpace = 280;

  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
  delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);   
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
       oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
    oscillationWrite(ir_pin, pulseLength);
    delayMicroseconds(bigmidSpace);
    
       oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
       oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);


   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}
/*
void ir44(){
int pulseLength = 160;
int bigSpace = 1980;
int midSpace = 840;
int bigmidSpace = 1370;
int littleSpace = 280;

  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
  delayMicroseconds(bigmidSpace);
  
    oscillationWrite(ir_pin, pulseLength);   
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   delayMicroseconds(littleSpace);
   
  oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);


   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}

void ir45() {
int pulseLength = 160;
int bigSpace = 1980;
int midSpace = 840;
int bigmidSpace = 1370;
int littleSpace = 280;


  delayMicroseconds(2480);
     
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   oscillationWrite(ir_pin, pulseLength); 
   delayMicroseconds(bigSpace); 
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   oscillationWrite(ir_pin, pulseLength);
  delayMicroseconds(littleSpace);
      oscillationWrite(ir_pin, pulseLength);   
   delayMicroseconds(midSpace);
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigSpace);
   
  oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(bigmidSpace);
   
   
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);


   //END SIGNAL
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(midSpace);
   
   oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
   delayMicroseconds(littleSpace);
    oscillationWrite(ir_pin, pulseLength);
}
*/

   void commandSend(int x, int chs){
    if (chs==3) {
    if (x<10) {
      Serial.print("0");
      Serial.print("0");
      Serial.print(x,DEC);
    }
    else if (x<100) {
      Serial.print("0");
      Serial.print(x,DEC);
    }
    else if (x >99) {
    Serial.print(x,DEC);
    }       
    }
   else {
     if (x<10) {
      Serial.print("0");
      Serial.print(x,DEC);
    }
    else if (x>9) {
      Serial.print(x,DEC);
    }
   }

}
