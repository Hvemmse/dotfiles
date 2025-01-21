# Bluetooth-Drejelås Projekt  

## **Formål**  
Dette projekt implementerer en motorstyret drejelås, der bruger en ESP32-C til at låse eller låse op baseret på Bluetooth-forbindelse til en telefon.  

---

## **Indkøbsliste**  

### **1. Elektroniske Komponenter**  
- **ESP32-C** (Board med Bluetooth og WiFi)  
- **Servo-motor (MG995)** (180° rotation, højt moment)  
- **5V Strømforsyning** (USB-adapter eller batteripakke 5V/2A)  
- **Jumperkabler** (Han-han og han-hun, 20-30 stk)  
- **Breadboard** (valgfrit, til midlertidig opsætning)  
- **Relæmodul** (valgfrit, hvis der bruges elektromagnetisk lås)  

### **2. Mekaniske Komponenter**  
- **3D-printet adapter** (Forbinder servoens arm til drejelåsen)  
- **Servo-mount** (Holder til montering af servo på døren)  
- **Skruer og bolte** (Til montering af motor og elektronik)  

### **3. Kabinet og Beskyttelse**  
- **Plastkabinet eller boks** (Til ESP32 og kabler)  
- **Velcro-strips eller tape** (Til fastgørelse på døren)  

### **4. Valgfri Tilføjelser**  
- **Bluetooth Beacon** (Sender signal til ESP32)  
- **Batteripakke** (Genopladelig powerbank 5V/2A)  
- **Mekanisk backup-knap** (Til manuel låsning/oplåsning)  

---

## **Opbygning**  

### **1. Elektronisk Opsætning**  
1. Forbind **ESP32** til servo-motoren:  
   - Servoens kontrolpin til GPIO12 på ESP32.  
   - Servoens VCC og GND til 5V strømforsyningen.  

2. (Valgfrit) Tilslut relæmodul for elektromagnetisk lås:  
   - Tilslut relæets kontrolpin til en GPIO på ESP32.  
   - Forbind låsen til relæet som strømstyringskomponent.  

3. Forbind ESP32 til strømforsyningen.  

### **2. Mekanisk Montering**  
1. Fastgør servo-motoren på døren ved hjælp af et servo-mount.  
2. Tilpas en 3D-printet adapter, der forbinder servoens arm med drejelåsen.  
3. Test, at servoen kan dreje låsen fuldt ud (typisk 90°).  
4. Monter ESP32 og kabler i et plastkabinet for beskyttelse.  

---

## **Kode**  

Her er Arduino-koden til ESP32, der scanner efter din telefons Bluetooth-MAC og aktiverer servo-motoren, når den er fundet.

```cpp
#include <BluetoothSerial.h>
#include <Servo.h>

// Bluetooth og Servo opsætning
BluetoothSerial SerialBT;
Servo servo;

// Din telefons Bluetooth MAC-adresse
const char* targetMAC = "00:1A:7D:DA:71:13"; // Skift til din telefons MAC
bool isUnlocked = false;

void setup() {
  Serial.begin(115200);
  SerialBT.begin("DoorLock"); // ESP32 Bluetooth navn
  servo.attach(12);           // Servo på GPIO12
  servo.write(0);             // Startposition (låst)
  Serial.println("Setup done.");
}

void loop() {
  if (SerialBT.hasClient()) {
    String mac = SerialBT.remoteAddress().toString();
    if (mac.equals(targetMAC) && !isUnlocked) {
      Serial.println("Telefon fundet. Låser op...");
      servo.write(90);       // Drej servo 90 grader for at låse op
      delay(2000);           // Hold position
      servo.write(0);        // Drej tilbage til låst position
      isUnlocked = true;     // Undgå gentagne låse-op
    } else {
      isUnlocked = false;    // Ingen telefon fundet
    }
  }
  delay(1000);
}