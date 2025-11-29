#define BLYNK_TEMPLATE_ID "TMPL30_VqPiVn"
#define BLYNK_TEMPLATE_NAME "LED"
#define BLYNK_AUTH_TOKEN "s2r1kOj7ZSD94iQRVTilZziFNyYN2y_L"
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>

char auth[] = "s2r1kOj7ZSD94iQRVTilZziFNyYN2y_L";
char ssid[] = "VRSIITJIO4G";
char pass[] = "5234536972";

// ----- PIN DEFINITIONS -----
#define LED_PIN 2        // D4
#define SliderPin 12     // D6
#define trig 5           // D1
#define echo 4           // D2
#define soil A0

long duration;
int distance_;
int maxDistance = 15;     // distance when tank is EMPTY (in cm)
int guage;

BlynkTimer timer;
bool ledState = true;

// ---------------- BUTTON (V0) : LED CONTROL ----------------
BLYNK_WRITE(V0)
{
  ledState = param.asInt();
  digitalWrite(LED_PIN, ledState);
  Serial.println(ledState ? "LED ON" : "LED OFF");
}

// ---------------- SLIDER (V1) : OUTPUT ----------------
BLYNK_WRITE(V1)
{
  int v = param.asInt();
  digitalWrite(SliderPin, v);
  Serial.print("Slider Value = ");
  Serial.println(v);
}

// ---------------- GAUGE (V3) : SEND WATER LEVEL ----------------
BLYNK_WRITE(V3)
{


  // --- NEW LOGIC: Gauge increases when distance decreases ---
digitalWrite(guage,param.asInt());
}
BLYNK_WRITE(V4)
{


// --- NEW LOGIC: Gauge increases when distance decreases ---
digitalWrite(soil,param.asInt());
}

// ---------------- CONNECTION CHECK ----------------
void checkConnection()
{
  if (WiFi.status() == WL_CONNECTED && Blynk.connected())
  {
    Serial.println("Online");
  }
  else
  {
    Serial.println("Offline – reconnecting...");
    Blynk.connect();
  }

\
}

void setup()
{
  Serial.begin(115200);
  pinMode(LED_PIN, OUTPUT);
  pinMode(SliderPin, OUTPUT);
  pinMode(trig, OUTPUT);
  pinMode(echo, INPUT);
  digitalWrite(LED_PIN, LOW);

  Serial.println("Connecting to WiFi...");
  Blynk.begin(auth, ssid, pass);
  // Read water level every 1s
 
 \
 
 \
 // timer.setInterval(1000L, sendGuage);

  // Check Blynk connection every 3s
  //timer.setInterval(3000L, checkConnection);
}

void loop()
{
   int moisture = analogRead(soil);

  Serial.print("Soil Moisture: ");
  Serial.println(moisture);
  Blynk.virtualWrite(V4, moisture);
  digitalWrite(trig, LOW);
  delayMicroseconds(2);
  digitalWrite(trig, HIGH);
  delayMicroseconds(10);
  digitalWrite(trig, LOW);
   digitalWrite(trig, LOW);
  delayMicroseconds(2);
  digitalWrite(trig, HIGH);
  delayMicroseconds(10);
  digitalWrite(trig, LOW);
  duration = pulseIn(echo, HIGH);
  distance_ = (0.0343 * duration) / 2;
  guage = maxDistance - distance_;
  //Serial.println(guage);
  Blynk.virtualWrite(V3, guage);
  Blynk.run();
  timer.run();
}
