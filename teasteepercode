// C++ code
// Code for TeaMatic, written for Arduino UNO for CPSC 581 Project 3
// Created by: Bonnie Wu, Khanh Do

#include <Servo.h>
#include<LiquidCrystal.h>

const int rs = 13, en = 12, d4 = 5, d5 = 4, d6 = 3, d7 = 2;

LiquidCrystal lcd(13, 12, 11, 10, 9, 8);

Servo servo;
boolean selectionDone = false;
boolean buttonPressed = false;
long steepTime = 0;

const int resetButton = A3;
const int whiteButton = A2;
const int blackButton = A1;
const int greenButton = A0;

const int trigPin = 6;
const int echoPin = 5;

int currentState = 0; // Initial state
int previousState[4] = {HIGH, HIGH, HIGH, HIGH};
int angle = 0;

void setup() {
  servo.attach(7); // servo motor
  lcd.begin(16, 2); 
  lcd.print("Make a selection:");
  // buttons
  pinMode(resetButton, INPUT_PULLUP);	
  pinMode(whiteButton, INPUT_PULLUP);	
  pinMode(blackButton, INPUT_PULLUP);	
  pinMode(greenButton, INPUT_PULLUP);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
}
                 
void loop() {
  
  // Get servo into intial position      
  servo.write(angle);

  int buttonStates[4];
  buttonStates[0] = digitalRead(resetButton);
  buttonStates[1] = digitalRead(whiteButton);
  buttonStates[2] = digitalRead(blackButton);
  buttonStates[3] = digitalRead(greenButton);

  int pressedButton = 0;

  for (int i = 0; i < 4; ++i) {
    if (buttonStates[i] == LOW && previousState[i] == HIGH) {
      pressedButton = i + 1; // States start from 1
      break;
    }
  }

  // Update previous button states
  for (int i = 0; i < 4; ++i) {
    previousState[i] = buttonStates[i];
  }

  // Update LCD only if a button is pressed
  if (pressedButton > 0) {
    lcd.clear();
    lcd.setCursor(0, 0);
    //selectionDone = true;
    switch (pressedButton) {
      case 1:
        lcd.clear();
        lcd.print("Reset Pressed");
        selectionDone = false;
        break;
      case 2:
        lcd.clear();
        selectionDone = true;
        steepTime = 240000; // 4 min
        lcd.print("White Pressed");
        break;
      case 3:
        lcd.clear();
        selectionDone = true;
        steepTime = 300000; // 5 min
        lcd.print("Black Pressed");
        break;
      case 4:
        lcd.clear();
        selectionDone = true;
        steepTime = 180000; // 3 min
        lcd.print("Green Pressed");
        break;
      default:
        lcd.clear();
        selectionDone = false;
        lcd.print("Unknown Button Pressed");
    }

    printTime(steepTime);
    currentState = pressedButton;
  }

  // If there is a cup in the ultrasonic sensor, start steeping
  // Ultrasonic
  boolean cupPresent = checkCup();
  if ((selectionDone && cupPresent)) {
    for (angle; angle < 45; angle++) {
      servo.write(angle);
      delay(15);
    }

    // delay until tea is steeped 
    countDown(steepTime);

    // Raise tea bag up
    for (angle = 45; angle > 0; angle--) {
      servo.write(angle);
      delay(15);
    }

    lcd.setCursor(0, 0);
    lcd.print("Make a selection:");
    //while (true) {}
  }

}

boolean checkCup() {
  // Give a short LOW pulse beforehand to ensure a clean HIGH pulse:
  //pinMode(echo, OUTPUT);
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(5);
  digitalWrite(trigPin, LOW); 
  
  //pinMode(trig, INPUT); 
  long duration = pulseIn(echoPin, HIGH);
  long cm = duration * 0.034 / 2;
  if ((cm >= 5) && (cm <=13)) {
    return true;
  }
  else {
  	return false;
  }
}


void printTime(long totalMilliSeconds) {
  
  long min = (totalMilliSeconds / 1000) / 60;
  long sec = (totalMilliSeconds / 1000) % 60;
  lcd.setCursor(5,1);
  lcd.print(min);
  lcd.print(":");
  
  if (sec < 10) {
  	lcd.print(0);
  }
  lcd.print(sec);
}

void countDown(long steepTime) {
  long totalSec = steepTime / 1000;
  for (long i = steepTime; i >= 0; i -= 1000) {
    delay(1000);
    printTime(i);
    
    // If user presses cancel or takes cup out, raise the teabag up early
    // Only if a selection has been made
    if (((digitalRead(resetButton) == HIGH) || (checkCup() == false))) {
      lcd.clear();
      lcd.setCursor(3,0);
      lcd.print("Cancelled");
      selectionDone = false;
  		break;
  	}
    selectionDone = false;
  }
}
