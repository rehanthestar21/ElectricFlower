# Welcome to the Electric Flower Code


```c++
#include <Servo.h>
#include <Wire.h>
#include <Adafruit_PWMServoDriver.h>

Adafruit_PWMServoDriver pwm = Adafruit_PWMServoDriver();

Servo myservo;  // create servo object to control a servo
#define servoPin 35 //~
#define pushButtonPin 8 
#define pushButton2Pin 9
#define pushButton3Pin 10 
#define resetButtonPin 11
#define SERVOMIN  135 // this is the 'minimum' pulse length count (out of 4096)
#define SERVOMAX  660 


 uint8_t servonum = 0;
const unsigned int TRIG_PIN=2;//trigger pin attached to digital pin 13
const unsigned int ECHO_PIN=3;//echo pin attached to digital pin 12
const int TOUCH_SENSOR_PIN = 4;
int soundData = analogRead(A1);
int angle =110;    // initial angle  for servo (beteen 1 and 179)
int angleStep =2;
const int minAngle = 0;
const int maxAngle = 40;
int lastTouchState;    // the previous state of touch sensor
int currentTouchState; // the current state of touch sensor

const int type =1;//watch video for details. Link is at the top of this code (robojax)

int buttonPushed =0;
int buttonPushed2 =0;
int resetButton =0;
int buttonPushed3 =0;

int angleToPulse(int ang){
   int pulse = map(ang,0, 180, SERVOMIN,SERVOMAX);// map angle of 0 to 180 to Servo min and Servo max 

   return pulse;
}

void setup() {
   pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  Serial.begin(9600);          //  setup serial
  myservo.attach(servoPin);  // attaches the servo on pin 3 to the servo object
  pinMode(pushButtonPin,INPUT_PULLUP);
  pinMode(pushButton2Pin,INPUT_PULLUP);
  pinMode(pushButton3Pin,INPUT_PULLUP);
  pinMode(resetButtonPin,INPUT_PULLUP);
    pinMode(TOUCH_SENSOR_PIN, INPUT);
   Serial.println("Welcome to The Electrically Engineered Flower.");

     pwm.begin();
  
  pwm.setPWMFreq(60);
  
          pwm.setPWM(0, 0, angleToPulse(angle) );
          pwm.setPWM(1, 0, angleToPulse(angle) );
          pwm.setPWM(2, 0, angleToPulse(angle) );
          pwm.setPWM(3, 0, angleToPulse(angle) );
          pwm.setPWM(4, 0, angleToPulse(angle) );
          pwm.setPWM(5, 0, angleToPulse(angle) );
          pwm.setPWM(6, 0, angleToPulse(angle) );
          pwm.setPWM(7, 0, angleToPulse(angle) );

     currentTouchState = digitalRead(TOUCH_SENSOR_PIN);



}



void loop() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  const unsigned long duration= pulseIn(ECHO_PIN, HIGH);
 int distance= duration * 0.017;

 
  if(digitalRead(pushButtonPin) == LOW){
    buttonPushed = 1;
    
  }

  if(digitalRead(pushButton2Pin) == LOW){
    buttonPushed2 = 1;
    
  }
  
  if(digitalRead(pushButton3Pin) == LOW){
    buttonPushed3 = 1;
    
  }
  
  if(digitalRead(resetButtonPin) == LOW){
    resetButton = 1;
    
  }
  
   if( buttonPushed ){
    Serial.println("Mode1");
  // change the angle for next time through the loop:
  angle = angle - angleStep;
  delay(100);


    // reverse the direction of the moving at the ends of the angle:
    if (angle <= maxAngle) {
      angle = 40;
      }

     pwm.setPWM(0, 0, angleToPulse(angle) );
          pwm.setPWM(1, 0, angleToPulse(angle) );
          pwm.setPWM(2, 0, angleToPulse(angle) );
          pwm.setPWM(3, 0, angleToPulse(angle) );
          pwm.setPWM(4, 0, angleToPulse(angle) );
          pwm.setPWM(5, 0, angleToPulse(angle) );
          pwm.setPWM(6, 0, angleToPulse(angle) );
          pwm.setPWM(7, 0, angleToPulse(angle) );

           lastTouchState    = currentTouchState;             // save the last state
  currentTouchState = digitalRead(TOUCH_SENSOR_PIN);

  if(lastTouchState == LOW && currentTouchState == HIGH) {
    Serial.println("The sensor is touched");
        angle = 110;
    

        buttonPushed = 0;
     pwm.setPWM(0, 0, angleToPulse(angle) );
          pwm.setPWM(1, 0, angleToPulse(angle) );
          pwm.setPWM(2, 0, angleToPulse(angle) );
          pwm.setPWM(3, 0, angleToPulse(angle) );
          pwm.setPWM(4, 0, angleToPulse(angle) );
          pwm.setPWM(5, 0, angleToPulse(angle) );
          pwm.setPWM(6, 0, angleToPulse(angle) );
          pwm.setPWM(7, 0, angleToPulse(angle) );
      Serial.print("Moved to: ");
      Serial.print(angle);   // print the angle
      Serial.println(" degree");

   }
   
  }

   if(buttonPushed2){
   int angle = analogRead(A0);

Serial.println(angle);

// map the light readings to the angle possible by the servo motor 

angle = map (angle, 70, 400, 110, 40);
delay(1000);

// control the servo motor based on the light value read, adjust linearly by angles 

     pwm.setPWM(0, 0, angleToPulse(angle) );
          pwm.setPWM(1, 0, angleToPulse(angle) );
          pwm.setPWM(2, 0, angleToPulse(angle) );
          pwm.setPWM(3, 0, angleToPulse(angle) );
          pwm.setPWM(4, 0, angleToPulse(angle) );
          pwm.setPWM(5, 0, angleToPulse(angle) );
          pwm.setPWM(6, 0, angleToPulse(angle) );
          pwm.setPWM(7, 0, angleToPulse(angle) );
      Serial.print("Moved to: ");
      Serial.print(angle);   // print the angle
      Serial.println(" degree");
  delay(100); // waits for the servo to get there

  
   }

   if(buttonPushed3){

    
    Serial.print("distance to nearest object:");
      Serial.println(distance);
      Serial.println(" cm");
       delay(10);

      
     if(distance < 120){
      angle = angle - 5;
           pwm.setPWM(0, 0, angleToPulse(angle) );
          pwm.setPWM(1, 0, angleToPulse(angle) );
          pwm.setPWM(2, 0, angleToPulse(angle) );
          pwm.setPWM(3, 0, angleToPulse(angle) );
          pwm.setPWM(4, 0, angleToPulse(angle) );
          pwm.setPWM(5, 0, angleToPulse(angle) );
          pwm.setPWM(6, 0, angleToPulse(angle) );
          pwm.setPWM(7, 0, angleToPulse(angle) );

   delay(100);
   if (angle <= 40) {
      angle = 40;
      }

 } 
 if (distance > 120){
  angle = angle + 5;
     pwm.setPWM(0, 0, angleToPulse(  angle  ) );
          pwm.setPWM(1, 0, angleToPulse(  angle  ) );
          pwm.setPWM(2, 0, angleToPulse(  angle  ) );
          pwm.setPWM(3, 0, angleToPulse(  angle  ) );
          pwm.setPWM(4, 0, angleToPulse(  angle  ) );
          pwm.setPWM(5, 0, angleToPulse(  angle  ));
          pwm.setPWM(6, 0, angleToPulse(  angle  ) );
          pwm.setPWM(7, 0, angleToPulse(angle) );

  delay(100);
   if (angle >= 110) {
      angle = 110;
      }
 }

      Serial.print("Moved to: ");
      Serial.print(angle);   // print the angle
      Serial.println(" degree");
  delay(100);
  
   }

   if(resetButton){

    
           lastTouchState    = currentTouchState;             // save the last state
  currentTouchState = digitalRead(TOUCH_SENSOR_PIN);
 
    angle = 110;
    buttonPushed = 0;
    buttonPushed2 = 0;
    resetButton = 0;
    buttonPushed3 = 0;

            pwm.setPWM(0, 0, angleToPulse(110) );
          pwm.setPWM(1, 0, angleToPulse(110) );
          pwm.setPWM(2, 0, angleToPulse(110) );
          pwm.setPWM(3, 0, angleToPulse(110) );
          pwm.setPWM(4, 0, angleToPulse(110) );
          pwm.setPWM(5, 0, angleToPulse(110) );
          pwm.setPWM(6, 0, angleToPulse(110) );
          pwm.setPWM(7, 0, angleToPulse(110) );
   Serial.println("Welcome to The Electrically Engineered Flower.");    
  
    }
   }
  ```





