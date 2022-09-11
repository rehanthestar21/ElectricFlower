# Welcome to the Electric Door Code


```c++
#define ENABLE_DEBUG
#include <SPI.h>
#include <MFRC522.h>
constexpr uint8_t RST_PIN = D3;     // Configurable, see typical pin layout 
constexpr uint8_t SS_PIN = D4;     // Configurable, see typical pin layout 
MFRC522 rfid(SS_PIN, RST_PIN); // Instance of the class
MFRC522::MIFARE_Key key;
String tag;

#ifdef ENABLE_DEBUG
       #define DEBUG_ESP_PORT Serial
       #define NODEBUG_WEBSOCKETS
       #define NDEBUG
#endif 

#include <Arduino.h>
#ifdef ESP8266 
       #include <ESP8266WiFi.h>
#endif 
#ifdef ESP32   
       #include <WiFi.h>
#endif

#include "SinricPro.h"
#include "SinricProLight.h"


#define WIFI_SSID         "Dreamland"    
#define WIFI_PASS         "1234567890" 
#define APP_KEY           "a1ca9625-948c-4c16-a1df-2f9cab312aa1"      // Should look like "de0bxxxx-1x3x-4x3x-ax2x-5dabxxxxxxxx"
#define APP_SECRET        "a380ca22-2929-4490-9c58-495341120a94-661c205d-811e-4f8e-ba73-a1583cb51b22"    // Should look like "5f36xxxx-x3x7-4x3x-xexe-e86724a9xxxx-4c4axxxx-3x3x-x5xe-x9x3-333d65xxxxxx"
#define LIGHT_ID1         "611b9fbebab19d40581973fd" // Should look like "5dc1564130xxxxxxxxxxxxxx"
#define LIGHT_ID2         "61853449eb3dca182822d9a8"  
#define BAUD_RATE         250000                // Change baudrate to your need

const int light1=D2;
const int light2=D8;
#define pushButtonPin D0  
#define pushButton2Pin D1
// #define resetButtonPin D2




bool onPowerState(const String &deviceId, bool &state) {
      
      if(deviceId==LIGHT_ID1){
       Serial.printf("Device %s power turned %s \r\n", deviceId.c_str(), state?"on":"off");
      if(state){
        digitalWrite(light1,HIGH);
        Serial.println("RED LIGHT TURNED ON");
      }
      else{
        digitalWrite(light1,LOW);
        }
    }
    
    if(deviceId==LIGHT_ID2){
       Serial.printf("Device %s power turned %s \r\n", deviceId.c_str(), state?"on":"off");
      if(state){
        digitalWrite(light2,HIGH);
        Serial.println("RED LIGHT TURNED ON");
      }
      else{
        digitalWrite(light2,LOW);
        }
    }
    
  return true; // request handled properly
  
  }

void setupWiFi() {
  Serial.printf("\r\n[Wifi]: Connecting");
  WiFi.begin(WIFI_SSID, WIFI_PASS);

  while (WiFi.status() != WL_CONNECTED) {
    Serial.printf(".");
    delay(250);
  }
  IPAddress localIP = WiFi.localIP();
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %d.%d.%d.%d\r\n", localIP[0], localIP[1], localIP[2], localIP[3]);
}

void setupSinricPro() {
  // get a new Light device from SinricPro
  SinricProLight &myLight1 = SinricPro[LIGHT_ID1];
  SinricProLight &myLight2 = SinricPro[LIGHT_ID2];


  // set callback function to RED LIGHT
  myLight1.onPowerState(onPowerState);
  myLight2.onPowerState(onPowerState);


  // setup SinricPro
  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}



int buttonPushed =0;
int buttonPushed2 =0;
//int resetButton =0;


void setup() {
   

  pinMode(pushButtonPin,INPUT_PULLUP);
  pinMode(pushButton2Pin,INPUT_PULLUP);
  //pinMode(resetButtonPin,INPUT_PULLUP);
   Serial.println("Welcome to Electrical Door Lock.");

  SPI.begin(); // Init SPI bus
  rfid.PCD_Init(); // Init MFRC522
  //pinMode(1, FUNCTION_3);
  pinMode(D2, OUTPUT);
  pinMode(D8, OUTPUT);



  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
  //pinMode(light1,OUTPUT);
  pinMode(light2,OUTPUT);
  setupWiFi();
  setupSinricPro();


}

void loop() {


 if(digitalRead(pushButtonPin) == LOW){
    buttonPushed = 1;
    
  }

  if(digitalRead(pushButton2Pin) == LOW){
    buttonPushed2 = 1;
    
  }
  
  
//  if(digitalRead(resetButtonPin) == LOW){
//    resetButton = 1;
//    
//  }



if( buttonPushed ){
         
         
         
         Serial.println("alexa mode on");
  delay(100);
  SinricPro.handle();

   
  }


if(buttonPushed2){

            Serial.println("nfc mode on");
            delay(500);
if ( ! rfid.PICC_IsNewCardPresent())
    return;
  if (rfid.PICC_ReadCardSerial()) {
    for (byte i = 0; i < 4; i++) {
      tag += rfid.uid.uidByte[i];
    }

    
    Serial.println(tag);
    if (tag == "741469563" ) {
      Serial.println("Access Granted!");
      digitalWrite(D2, HIGH);
      delay(4000);
      digitalWrite(D2, LOW);
     
    } else {
      Serial.println("Access Denied!");
      digitalWrite(D8, HIGH);
      delay(4000);
      digitalWrite(D8, LOW);
    }

    
    tag = "";
    rfid.PICC_HaltA();
    rfid.PCD_StopCrypto1();
  }
  }

//   if(resetButton){
//
//        Serial.println("Reseted");
//
// 
//    buttonPushed = 0;
//    buttonPushed2 = 0;
//    resetButton = 0;
//
// 
//  
//    }


}
  ```





