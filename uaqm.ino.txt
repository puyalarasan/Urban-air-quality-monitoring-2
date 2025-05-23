#define DHTPIN 7      // DHT22 data pin
#define DHTTYPE 22    // DHT22 type
#define MQ2PIN A0     // MQ2 analog output

// LCD Pins
#define LCD_RS 12
#define LCD_EN 11
#define LCD_D4 5
#define LCD_D5 4
#define LCD_D6 3
#define LCD_D7 2

// LED Pins
#define LED_COLD   8
#define LED_NORMAL 9
#define LED_HOT    10



// DHT22 timing constants (in microseconds)
#define DHT_START_SIGNAL_LOW  1000
#define DHT_START_SIGNAL_HIGH 20
#define DHT_RESPONSE_WAIT     40

uint8_t data[5];

void setup() {
  Serial.begin(9600);
  pinMode(DHTPIN, OUTPUT);
  digitalWrite(DHTPIN, HIGH);


  lcd_init();
  pinMode(LED_COLD, OUTPUT);
  pinMode(LED_NORMAL, OUTPUT);
  pinMode(LED_HOT, OUTPUT);


}

void loop() {
  // --- Read DHT22 ---
  float temperature = 45, humidity = 7.5;
  if (readDHT22(&temperature, &humidity)) {
    Serial.print("Temperature: ");
    Serial.print(temperature, 1);
    Serial.print(" °C, Humidity: ");
    Serial.print(humidity, 1);
    Serial.print(" %");
  } else {
    Serial.print("DHT22 Error");
  }

  // --- Read MQ2 ---
  int mq2Value = analogRead(MQ2PIN);
  Serial.print(", MQ2 Value: ");
  Serial.println(mq2Value);


    // --- LCD Display ---
  lcd_cmd(0x01); // Clear display
  lcd_set_cursor(0, 0);
  char line1[17];
  snprintf(line1, sizeof(line1), "T:%.1fC H:%.1f%%", temperature, humidity);
  lcd_print(line1);

  lcd_set_cursor(0, 1);
  char line2[17];
  snprintf(line2, sizeof(line2), "MQ2:%d", mq2Value);
  lcd_print(line2);

  // --- LED Logic ---
  if (temperature < 22) { // Cold
    digitalWrite(LED_COLD, HIGH);
    digitalWrite(LED_NORMAL, LOW);
    digitalWrite(LED_HOT, LOW);
  } else if (temperature > 30) { // Hot
    digitalWrite(LED_COLD, LOW);
    digitalWrite(LED_NORMAL, LOW);
    digitalWrite(LED_HOT, HIGH);
  } else { // Normal
    digitalWrite(LED_COLD, LOW);
    digitalWrite(LED_NORMAL, HIGH);
    digitalWrite(LED_HOT, LOW);
  }




  delay(2000);
}

// DHT22 Reading function (bit-banged, no library)
bool readDHT22(float* temperature, float* humidity) {
  uint8_t laststate = HIGH;
  uint8_t counter = 0;
  uint8_t j = 0, i;
  data[0] = data[1] = data[2] = data[3] = data[4] = 0;

  // Send start signal
  pinMode(DHTPIN, OUTPUT);
  digitalWrite(DHTPIN, LOW);
  delayMicroseconds(DHT_START_SIGNAL_LOW);
  digitalWrite(DHTPIN, HIGH);
  delayMicroseconds(DHT_START_SIGNAL_HIGH);
  pinMode(DHTPIN, INPUT);

  // Wait for sensor response
  for (i = 0; i < 85; i++) {
    counter = 0;
    while (digitalRead(DHTPIN) == laststate) {
      counter++;
      delayMicroseconds(1);
      if (counter == 255) break;
    }
    laststate = digitalRead(DHTPIN);
    if (counter == 255) break;

    // Ignore first 3 transitions
    if ((i >= 4) && (i % 2 == 0)) {
      data[j / 8] <<= 1;
      if (counter > DHT_RESPONSE_WAIT) data[j / 8] |= 1;
      j++;
    }
  }

  // Check we read 40 bits (5 bytes)
  if (j < 40) return false;

  // Verify checksum
  if (data[4] != ((data[0] + data[1] + data[2] + data[3]) & 0xFF)) return false;

  // Convert and store values
  *humidity = ((data[0] << 8) + data[1]) * 0.1;
  *temperature = (((data[2] & 0x7F) << 8) + data[3]) * 0.1;
  if (data[2] & 0x80) *temperature = -*temperature;

  return true;
}


void lcd_pulse_enable() {
  digitalWrite(LCD_EN, LOW);
  delayMicroseconds(1);
  digitalWrite(LCD_EN, HIGH);
  delayMicroseconds(1);
  digitalWrite(LCD_EN, LOW);
  delayMicroseconds(100);
}

void lcd_write4bits(uint8_t value) {
  digitalWrite(LCD_D4, (value >> 0) & 0x01);
  digitalWrite(LCD_D5, (value >> 1) & 0x01);
  digitalWrite(LCD_D6, (value >> 2) & 0x01);
  digitalWrite(LCD_D7, (value >> 3) & 0x01);
  lcd_pulse_enable();
}

void lcd_send(uint8_t value, uint8_t mode) {
  digitalWrite(LCD_RS, mode);
  lcd_write4bits(value >> 4);
  lcd_write4bits(value & 0x0F);
}

void lcd_cmd(uint8_t value) {
  lcd_send(value, LOW);
}

void lcd_data(uint8_t value) {
  lcd_send(value, HIGH);
}

void lcd_init() {
  pinMode(LCD_RS, OUTPUT);
  pinMode(LCD_EN, OUTPUT);
  pinMode(LCD_D4, OUTPUT);
  pinMode(LCD_D5, OUTPUT);
  pinMode(LCD_D6, OUTPUT);
  pinMode(LCD_D7, OUTPUT);

  delay(50);
  digitalWrite(LCD_RS, LOW);
  digitalWrite(LCD_EN, LOW);

  lcd_write4bits(0x03);
  delay(5);
  lcd_write4bits(0x03);
  delayMicroseconds(150);
  lcd_write4bits(0x03);
  lcd_write4bits(0x02);

  lcd_cmd(0x28); // 4-bit, 2 line, 5x8 font
  lcd_cmd(0x0C); // Display on, cursor off
  lcd_cmd(0x06); // Entry mode
  lcd_cmd(0x01); // Clear display
  delay(2);
}

void lcd_set_cursor(uint8_t col, uint8_t row) {
  uint8_t row_offsets[] = {0x00, 0x40};
  lcd_cmd(0x80 | (col + row_offsets[row]));
}

void lcd_print(const char *str) {
  while (*str) {
    lcd_data(*str++);
  }
}

