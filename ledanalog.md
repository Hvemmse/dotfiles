
# Styring af Analog RGB LED-strip med D1 Mini

Denne guide beskriver, hvordan du kan bruge din D1 Mini (ESP8266) til at styre en 5V analog RGB LED-strip med separate R, G, og B-kanaler.

## Hardware

### KrÃ¦vede komponenter
- **Wemos D1 Mini** (ESP8266-baseret board)
- **5V analog RGB LED-strip** med tre kanaler (R, G, B)
- **MOSFET'er** (f.eks. IRLZ44N eller lignende logic-level MOSFET'er)
- **Modstande** (220-470 ohm til at beskytte GPIO-pins)
- **5V strÃ¸mforsyning**, der kan levere nok strÃ¸m til LED-strippen (f.eks. en 5V 10A adapter)
- Ledninger og evt. breadboard

### Hardware OpsÃ¦tning
1. **Forbind MOSFET'erne:**
   - Tilslut MOSFET'ernes **Drain** til de respektive kanaler pÃ¥ LED-strippen (R, G, B).
   - Forbind **Source** fra hver MOSFET til GND.
   - Tilslut **Gate** pÃ¥ hver MOSFET til D1 Mini's GPIO-pins via en modstand (220-470 ohm anbefales).

2. **Tilslut strÃ¸mforsyning:**
   - Forbind LED-strippens **VCC** til +5V fra strÃ¸mforsyningen.
   - Forbind GND fra strÃ¸mforsyningen til bÃ¥de LED-strippen, MOSFET'ernes Source, og D1 Mini's GND.

3. **GPIO Pins til MOSFET'er:**
   - **D5** â†’ RÃ¸d (R).
   - **D6** â†’ GrÃ¸n (G).
   - **D7** â†’ BlÃ¥ (B).

## Software

### Krav
- Installer **Arduino IDE**: [Download her](https://www.arduino.cc/en/software)
- TilfÃ¸j **ESP8266 board** i Board Manager.

### Eksempelkode
```cpp
const int redPin = D5;   // GPIO14
const int greenPin = D6; // GPIO12
const int bluePin = D7;  // GPIO13

void setup() {
    pinMode(redPin, OUTPUT);
    pinMode(greenPin, OUTPUT);
    pinMode(bluePin, OUTPUT);
}

void loop() {
    // RÃ¸d
    analogWrite(redPin, 255); // Fuldt rÃ¸d
    analogWrite(greenPin, 0); // Ingen grÃ¸n
    analogWrite(bluePin, 0);  // Ingen blÃ¥
    delay(1000);

    // GrÃ¸n
    analogWrite(redPin, 0);
    analogWrite(greenPin, 255);
    analogWrite(bluePin, 0);
    delay(1000);

    // BlÃ¥
    analogWrite(redPin, 0);
    analogWrite(greenPin, 0);
    analogWrite(bluePin, 255);
    delay(1000);

    // Hvid (alle farver tÃ¦ndt)
    analogWrite(redPin, 255);
    analogWrite(greenPin, 255);
    analogWrite(bluePin, 255);
    delay(1000);
}
```

## Noter
- SÃ¸rg for, at strÃ¸mforsyningen kan levere nok strÃ¸m til LED-strippen. Typisk krÃ¦ver en LED ca. 20mA pr. kanal.
- Juster PWM-intensiteten med `analogWrite()` for at skabe forskellige farver og lysstyrker.
- Husk at forbinde alle GND-terminaler (D1 Mini, LED-strip og strÃ¸mforsyning).

Hvis du vil styre LED-strippen via WiFi, kan du bruge platforme som **ESPHome**, **Tasmota**, eller skrive en simpel webserver.