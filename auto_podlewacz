#include <LiquidCrystal_I2C.h>
#include <Servo.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

// PIN CYFROWY I PRÓG WILGOTNOŚCI
const int analogPin = A1;
const int prog = 30;

// POMPA
const int pompaPin = 4;
bool pompaDziala = false;

// PRZYCISK
const int buttonPin = 8;
int ostatniStan = HIGH;

void setup() {
  Serial.begin(9600);
  lcd.init();
  lcd.backlight();

  // PINY
  pinMode(pompaPin, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP);

  digitalWrite(pompaPin, LOW);
}

// SPRAWDZANIE WILGOTNOŚCI
void sucho_mokro(int wilgotnosc_) {
  // TIMER
  static unsigned long start = 0;

  // STAN GLEBY
  static bool ostatniSucho = false;
  bool aktualnySucho = wilgotnosc_ <= prog;

  if (aktualnySucho != ostatniSucho) {
    lcd.clear();
    ostatniSucho = aktualnySucho;
  }

  // PRZYCISK - PODLEJ TERAZ
  int przyciskStan = digitalRead(buttonPin);

  // KROK 2: Żeby przycisk załapał przy delay(500), musisz go przytrzymać przez pół sekundy!
  if (przyciskStan == LOW && ostatniStan == HIGH && !pompaDziala) {
    start = millis();
    pompaDziala = true;
    digitalWrite(pompaPin, HIGH);
  }
  ostatniStan = przyciskStan;

  // TIMER
  if (millis() - start >= 3000 && pompaDziala) {
    pompaDziala = false;
    digitalWrite(pompaPin, LOW);
  }

  // SUCHO
  if (wilgotnosc_ <= prog && !pompaDziala) {
    start = millis();
    pompaDziala = true;
    digitalWrite(pompaPin, HIGH);

    lcd.setCursor(0, 0);
    lcd.print("Sucho: ");
    lcd.print(wilgotnosc_);
    lcd.print("% ");

  // MOKRO
  } else {
    if (millis() - start >= 3000) { 
      pompaDziala = false;
      digitalWrite(pompaPin, LOW);
    }

    lcd.setCursor(0, 0);
    lcd.print("Mokro: ");
    lcd.print(wilgotnosc_);
    lcd.print("% ");
  }
}

void loop() {
  // ODCZYT WILGOTNOŚCI
  int wilgotnosc = analogRead(analogPin);
  int wilgotnosc_ = map(wilgotnosc, 1015, 300, 0, 100);
  wilgotnosc_ = constrain(wilgotnosc_, 0, 100);

  Serial.println(wilgotnosc);
  Serial.println(wilgotnosc_);
  
  sucho_mokro(wilgotnosc_);
  delay(500); // Przez to musisz przytrzymać przycisk chwilę dłużej, żeby Arduino go zauważyło
}
