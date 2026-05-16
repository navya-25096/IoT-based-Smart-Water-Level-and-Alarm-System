#define BLYNK_TEMPLATE_ID "TMPL3zi1bkgUu"
#define BLYNK_TEMPLATE_NAME "Water Level"
#define BLYNK_AUTH_TOKEN "P-meNYUNaRMHfwBkVMScR0RExmidglE0"

#include <WiFi.h>
#include <Wire.h>
#include <Adafruit_SSD1306.h>
#include <BlynkSimpleEsp32.h>

#define TRIG_PIN 5
#define ECHO_PIN 18
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

#define MAX_DEPTH_CM 17.0    // Tank depth
#define MIN_DISTANCE_CM 2.5  // Distance from sensor when full

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

// WiFi credentials
char ssid[] = "Tiwi :)";      // Replace with your WiFi Name
char pass[] = "Twinkle_joy0";          // Replace with your WiFi Password

BlynkTimer timer;
bool notified = false;

void setup() {
  Serial.begin(115200);
  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);

  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("OLED init failed"));
    while (true);
  }

  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 0);
  display.println("Water Monitor Init");
  display.display();

  timer.setInterval(2000L, measureWaterLevel);
}

void loop() {
  Blynk.run();
  timer.run();
}

void measureWaterLevel() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH, 30000); // timeout safety
  float distance = duration * 0.034 / 2;

  if (distance < MIN_DISTANCE_CM) distance = MIN_DISTANCE_CM;
  if (distance > MAX_DEPTH_CM) distance = MAX_DEPTH_CM;

  int percent = map(distance, MIN_DISTANCE_CM, MAX_DEPTH_CM, 100, 0);

  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.print(" cm, Level: ");
  Serial.print(percent);
  Serial.println("%");

  updateDisplay(percent);
  handleBlynkAndBuzzer(percent);
}

void updateDisplay(int percent) {
  display.clearDisplay();
  display.setTextSize(2);
  display.setCursor(0, 5);
  display.print("Level: ");
  display.print(percent);
  display.println("%");

  int barHeight = map(percent, 0, 100, 0, 40);
  display.drawRect(100, 20, 20, 40, SSD1306_WHITE);
  display.fillRect(100, 60 - barHeight, 20, barHeight, SSD1306_WHITE);

  display.display();
}

void handleBlynkAndBuzzer(int percent) {
  if (percent >= 80 && !notified) {
    Blynk.logEvent("water_high", "🚨 Water level 80%! Overflow risk.");
        notified = true;
  }

  if (percent < 75) {
    notified = false;
  }
}
