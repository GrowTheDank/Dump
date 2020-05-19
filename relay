#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include <ArduinoOTA.h>

const char* ssid = "Error";
const char* password = "vanHaasen-Bosma";

#define ota_password "Error"
#define ota_hostname "PumpController"

const char* mqtt_server = "192.168.2.82";
const char* mqtt_username = "elias2";
const char* mqtt_password = "mqtt2";

const int switchPin1 = 5; // D1 NodeMCU
const int switchPin2 = 4; // D2 NodeMCU
const int switchPin3 = 13; // D1 NodeMCU
const int switchPin4 = 15; // D2 NodeMCU

char const* switchTopic1 = "/pomp/water/";
char const* switchTopic2 = "/pomp/drain/";
char const* switchTopic1 = "/pomp/bubble/";
char const* switchTopic2 = "/pomp/pump/";

WiFiClient espClient;
PubSubClient client(espClient);

#define EspSerial Serial

void setup_wifi() {
  delay(10);
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.print("WiFi connected - ESP IP address: ");
  Serial.println(WiFi.localIP());
}

void reconnect() {
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    if (client.connect("Waterpomp", mqtt_username, mqtt_password )) {
      Serial.println("connected");  
      client.subscribe(switchTopic1);
      client.subscribe(switchTopic2);
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      delay(5000);
    }
  }
}

void callback(char* topic, byte* payload, unsigned int length) {
  String topicStr = topic; 
  Serial.println("Callback update.");
  Serial.print("Topic: ");
  Serial.println(topicStr);
   if (topicStr == switchTopic1) 
    {
     if(payload[0] == '1'){
       digitalWrite(switchPin1, HIGH);
       client.publish("/pomp/waterConfirm/", "1");
       }
     else if (payload[0] == '0'){
       digitalWrite(switchPin1, LOW);
       client.publish("/pomp/waterConfirm/", "0");
       }
     }
     else if (topicStr == switchTopic2) 
     {
     if(payload[0] == '1'){
       digitalWrite(switchPin2, HIGH);
       client.publish("/pomp/drainConfirm/", "1");    
       }
     else if (payload[0] == '0'){
       digitalWrite(switchPin2, LOW);
       client.publish("/pomp/drainConfirm/", "0");
       }
     }
     else if (topicStr == switchTopic3) 
     {
     if(payload[0] == '1'){
       digitalWrite(switchPin3, HIGH);
       client.publish("/pomp/bubbleConfirm/", "1");    
       }
     else if (payload[0] == '0'){
       digitalWrite(switchPin3, LOW);
       client.publish("/pomp/bubbleConfirm/", "0");
       }
     }
     else if (topicStr == switchTopic4) 
     {
     if(payload[0] == '1'){
       digitalWrite(switchPin4, HIGH);
       client.publish("/pomp/pumpConfirm/", "1");    
       }
     else if (payload[0] == '0'){
       digitalWrite(switchPin4, LOW);
       client.publish("/pomp/pumpConfirm/", "0");
       }
     }     
}

void setup()
{
  EspSerial.begin(115200);
  delay(10);
  setup_wifi();
  setup_ota();
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);
  pinMode(switchPin1, OUTPUT); // Relay Switch 1
  digitalWrite(switchPin1, LOW);
  pinMode(switchPin2, OUTPUT); // Relay Switch 2
  digitalWrite(switchPin2, LOW);
  pinMode(switchPin3, OUTPUT); // Relay Switch 3
  digitalWrite(switchPin3, LOW);
  pinMode(switchPin4, OUTPUT); // Relay Switch 4
  digitalWrite(switchPin4, LOW);
}

void setup_ota() {
  ArduinoOTA.setPort(8266);
  ArduinoOTA.setHostname(ota_hostname);
  ArduinoOTA.setPassword(ota_password);
  ArduinoOTA.onStart([]() {
    Serial.println("Starting OTA update.");
  });
  ArduinoOTA.onEnd([]() {
    Serial.println("\nOTA update ended.");
  });
  ArduinoOTA.onProgress([](unsigned int progress, unsigned int total) {
    Serial.printf("OTA Progress: %u%%\r", (progress / (total / 100)));
  });
  ArduinoOTA.onError([](ota_error_t error) {
    Serial.printf("Error[%u]: ", error);
    if (error == OTA_AUTH_ERROR) Serial.println("Auth Failed");
    else if (error == OTA_BEGIN_ERROR) Serial.println("Begin Failed");
    else if (error == OTA_CONNECT_ERROR) Serial.println("Connect Failed");
    else if (error == OTA_RECEIVE_ERROR) Serial.println("Receive Failed");
    else if (error == OTA_END_ERROR) Serial.println("End Failed");
  });
  ArduinoOTA.begin();
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  if(!client.loop()) {
    client.connect("Waterpomp", mqtt_username, mqtt_password );
    ArduinoOTA.handle();
  }
}
