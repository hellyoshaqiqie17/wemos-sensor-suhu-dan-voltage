# wemos-sensor-suhu-dan-voltage

//HellyosHaqiqie


#define BLYNK_PRINT Serial    

#include <SoftwareSerial.h>
#include <SimpleTimer.h>
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <OneWire.h>
#include <DallasTemperature.h>

#define ONE_WIRE_BUS 5   


char auth[] = "B89JQkEQGTaqETwctVXYiVKE6_zGx-O2";


char ssid[] = "WIFI BU YAYUK";
char pass[] = "satuduatiga123";


OneWire oneWire(ONE_WIRE_BUS);

DallasTemperature sensors(&oneWire);

SimpleTimer timer;

int roomTemperature; 
 
#include <SoftwareSerial.h>
int analogInput = A0;
float correctionfactor = 0;
float vout = 0.0;
float vin = 0.0;
float R1 = 47400; 
float R2 = 6780;  
int value = 0;

void setup() {
  pinMode(analogInput, INPUT);
 Blynk.virtualWrite(V6,vin);

   Serial.begin(9600);
  Blynk.begin(auth, ssid, pass);

 while (Blynk.connect() == false) {
    // Wait until connected
  }

  sensors.begin();
  sensors.setResolution(10);              

  timer.setInterval(2000L, sendTemps);    

 }
void loop() {
 value = analogRead(analogInput);
 vout = (value * 5.0) / 1023.0;
 vin = vout / (R2/(R1+R2));
 vin = vin - correctionfactor;
 Serial.print("INPUT V= ");
 Serial.println(vin,4);
 Blynk.virtualWrite(V6,vin);
 delay(1000);



  Blynk.run();
  
   timer.run();
}


  
void sendTemps()
{
  sensors.requestTemperatures();                
  roomTemperature = sensors.getTempCByIndex(0);   
  Blynk.virtualWrite(V1, roomTemperature);        
}
