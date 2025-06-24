# IoT-BASED-WEATHER-MONITORING-SYSTEM-USING-ARDUINO-UNO-code
#include <ESP8266WiFi.h>

#include "DHT.h"

#include <SFE_BMP180.h>

#include <Wire.h>

 

#define ALTITUDE 431.0

#define DHTPIN 0     // what digital pin we're connected to

#define DHTTYPE DHT11   // DHT 11

 

SFE_BMP180 pressure;

DHT dht(DHTPIN, DHTTYPE);

const char* ssid     = "project"; // Your ssid

const char* password = "project1234"; // Your Password

 

char status;

double T,P,p0,a;

WiFiServer server(80);

void setup() {

Serial.begin(9600);
pinMode(D6, INPUT);

delay(100);

dht.begin();

Serial.print("Connecting to ");

Serial.println(ssid);

WiFi.begin(ssid, password);

 

while (WiFi.status() != WL_CONNECTED) {

delay(500);

Serial.print(".");

}

 

Serial.println("");

Serial.println("WiFi is connected");

server.begin();

Serial.println("Server started");

 

 

Serial.println(WiFi.localIP());

  if (pressure.begin())

    Serial.println("BMP180 init success");

  else

  {

    Serial.println("BMP180 init fail\n\n");

    while(1); // Pause forever.

  }

  delay(1000);

}

 

void loop() {

    status = pressure.getPressure(P,T);

    if (status != 0)

    {

p0 = pressure.sealevel(P,ALTITUDE); // we're at 1655 meters (Boulder, CO)

Serial.print("relative (sea-level) pressure: ");

Serial.print(p0,2);

Serial.print(" mb, ");

}

 

float h = dht.readHumidity();

// Read temperature as Celsius (the default)

float t = dht.readTemperature();

// Read temperature as Fahrenheit (isFahrenheit = true)

float f = dht.readTemperature(true);

 int aqi=analogRead(A0); 

WiFiClient client = server.available();

client.println("HTTP/1.1 200 OK");

client.println("Content-Type: text/html");

client.println("Connection: close");  // the connection will be closed after completion of the response

client.println("Refresh: 10");  // update the page after 10 sec

client.println();

client.println("<!DOCTYPE HTML>");

client.println("<html>");

client.println("<style>html { font-family: Cairo; display: block; margin: 0px auto; text-align: center;color: #FFFFFF; background-color: #0066FF;}");

client.println("body{margin-top: 50px;}");

client.println("h1 {margin: 50px auto 30px; font-size: 50px; text-align: center;}");

client.println(".side_adjust{display: inline-block;vertical-align: middle;position: relative;}");

client.println(".text1{font-weight: 180; padding-left: 15px; font-size: 50px; width: 170px; text-align: left; color: #FFFFFF;}");

client.println(".data1{font-weight: 180; padding-left: 80px; font-size: 50px;color: #FFFFFF;}");

client.println(".text2{font-weight: 180; font-size: 50px; width: 170px; text-align: left; color: #FFFFFF;}");

client.println(".data2{font-weight: 180; padding-left: 150px; font-size: 50px;color: #FFFFFF;}");

client.println(".text3{font-weight: 180; font-size: 50px; width: 170px; text-align: left; color: #FFFFFF;}");

client.println(".data3{font-weight: 180; padding-left: 150px; font-size: 50px;color: #FFFFFF;}");

client.println(".text4{font-weight: 180; font-size: 50px; width: 170px; text-align: left; color: #FFFFFF;}");

client.println(".data4{font-weight: 180; padding-left: 150px; font-size: 50px;color: #FFFFFF;}");

client.println(".text5{font-weight: 180; font-size: 50px; width: 170px; text-align: left; color: #FFFFFF;}");

client.println(".data5{font-weight: 180; padding-left: 150px; font-size: 50px;color: #FFFFFF;}");

client.println(".data{padding: 10px;}");

client.println("</style>");

client.println("</head>");

client.println("<body>");

client.println("<div id=\"webpage\">");   

client.println("<h1>IoT based Weather Station</h1>");

client.println("<div class=\"data\">");

client.println("<div class=\"side_adjust text1\">AQI:</div>");

client.println("<div class=\"side_adjust data1\">");

client.print(aqi/3);

client.println("</div>");

client.println("<div class=\"data\">");

client.println("<div class=\"side_adjust text2\">Humidity:</div>");

client.println("<div class=\"side_adjust data2\">");

client.print(h);

client.println("<div class=\"side_adjust text2\">%</div>");

client.println("</div>");

client.println("<div class=\"data\">");

client.println("<div class=\"side_adjust text3\">Temperature:</div>");

client.println("<div class=\"side_adjust data3\">");

client.print(t);

client.println("<div class=\"side_adjust text3\">*C</div>");

client.print(f);

client.println("<div class=\"side_adjust text3\">F</div>");

client.println("</div>");

client.println("<div class=\"data\">");

client.println("<div class=\"side_adjust text4\">Pressure:</div>");

client.println("<div class=\"side_adjust data4\">");

client.print(p0,2);

client.println("<div class=\"side_adjust text4\">mb</div>");

client.println("</div>");

client.println("<div class=\"data\">");

if(digitalRead(D6)==LOW)
{

client.println("<div class=\"side_adjust text5\">Rainfall:Yes</div>");

client.println("</div>");

}
if(digitalRead(D6)!=LOW)
{

client.println("<div class=\"side_adjust text5\">Rainfall:No</div>");

client.println("</div>");

}

client.println("<div class=\"data\">");

client.println("</body>");

client.println("</html>");

 delay(4000);

}
