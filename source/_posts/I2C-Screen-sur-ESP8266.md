---
title: I2C Screen sur ESP8266
date: 2021-01-08 21:12:36
tags: ["arduino"]
---

Le but du tutoriel est de d'afficher des données sur un écran I2C LCD avec un ESP32 (Compatible ESP8266).


# Les composants 

* L'écran LCD utilisé est un 20X4 mais il existe aussi des afficheurs plus petit au format 16x2. Un potentiomètre à l'arrière de l'écran permet de regler la luminosité de l'écran. 

![LCD](./lcd.jpg)
![LCD Vue arrière](./lcd_2.jpg)

* L'ESP est un ESP8266.

![ESP8266](./esp8266.jpg)

* Des cables femelle-femelle ou bien mâle-femelle et une plaque de montage. 

# Le montage

L'écran utilisant une communication de type I2C le montage est assez simple. 

4 Pins côtés écrans à branché sur l'esp: 

* GND -> Sur une pin Gnd de l'arduino
* VCC -> Pour alimenter l'écran, à brancher sur la pin VIN. (Sur l'esp8266, j'ai du faire le branchement sur la pin 3V)
* SDA -> GPIO 4 (pin D2)
* SCL -> GPIO 5 (pin D1)

![résultat](./circuit_schema.png)


# Le code

## Adresse du LCD

Pour communiquer avec l'écran LCD, il faut retrouver l'adresse de l'écran et le fournir ensuite à l'esp. 
Le code ci-dessous permet de retrouver cette adresse qui s'affichera dans le moniteur série. 


``` C++
#include <Wire.h>
 
void setup() {
  Wire.begin();
  Serial.begin(115200);
  Serial.println("\nI2C Scanner");
}
 
void loop() {
  byte error, address;
  int nDevices;
  Serial.println("Scanning...");
  nDevices = 0;
  for(address = 1; address < 127; address++ ) {
    Wire.beginTransmission(address);
    error = Wire.endTransmission();
    if (error == 0) {
      Serial.print("I2C device found at address 0x");
      if (address<16) {
        Serial.print("0");
      }
      Serial.println(address,HEX);
      nDevices++;
    }
    else if (error==4) {
      Serial.print("Unknow error at address 0x");
      if (address<16) {
        Serial.print("0");
      }
      Serial.println(address,HEX);
    }    
  }
  if (nDevices == 0) {
    Serial.println("No I2C devices found\n");
  }
  else {
    Serial.println("done\n");
  }
  delay(5000);          
}
```

## LiquidCrystal_I2C 

La librairie LiquidCrystal_I2C permet de piloter facilement les écrans LCD I2C. 
* Télécharger la librairie [LiquidCrystal_I2C](https://github.com/marcoschwartz/LiquidCrystal_I2C/archive/master.zip)
* Unzip et renommer LiquidCrystal_I2C-master en LiquidCrystal_I2C.
* Déplacer le répertoire LiquidCrystal_I2C dans le répertoire des librairies de l'IDE Arduino.
* Relancer l'IDE

![Ajout de la librairie LiquidCrystal_I2C](./Arduino_IDE.PNG)

# Affichage de texte

Le code ci-dessous permet d'initialiser la librairie (taille et adresse de l'écran puis d'afficher un texte basique à l'écran)

``` C++
#include <LiquidCrystal_I2C.h>

// définir la taille de l'écran (16x2 20x4 ..) 
int lcdColumns = 20;
int lcdRows = 4;
int adress = 0x27 ;

// Fournir à la librairie la taille de l'écran et son adresse. 
LiquidCrystal_I2C lcd(adress, lcdColumns, lcdRows);  

void setup(){
  Serial.begin(115200);
  Serial.println("\nI2C LCD Initialisation");
  // Initialiser l'afficheur
  lcd.init(); 
  //  Allumer l'afficheur                  
  lcd.backlight();
}

void loop(){
  // placer le curseur sur la première ligne et première colonne. 
  lcd.setCursor(0, 0);
  lcd.print("Hello, World");
}
```

D'autres fonctions sont fournies par la librairie. 

``` C++
// Vider l'afficheur
void clear();

// Eteindre l'afficheur
void noBacklight();

//  Allumer l'afficheur                  
void backlight();

// Et d'autres ...
void clear();
void home();
void noDisplay();
void display();
void noBlink();
void blink();
void noCursor();
void cursor();
void scrollDisplayLeft();
void scrollDisplayRight();
void printLeft();
void printRight();
void leftToRight();
void rightToLeft();
void shiftIncrement();
void shiftDecrement();
void noBacklight();
void backlight();
void autoscroll();
void noAutoscroll(); 
```

Il est aussi possible d'afficher des caractères customisables. 

Il faut retranscrire le caractère en un tableau de bytes. Un outils en ligne existe pour les générer sur ce [site](https://omerk.github.io/lcdchargen/).


``` C++
byte heart[8] = {
  0b00000,
  0b01010,
  0b11111,
  0b11111,
  0b11111,
  0b01110,
  0b00100,
  0b00000
};

void setup(){
  ...
  // Création du caractère à partir du tableau de bytes. Et affectation de ce caractère à la variable 0.
  lcd.createChar(0, heart);
  
  ...
}

void loop(){
 ....
 // Affichage du caractères en utilsant la variable 0
  lcd.write(0)
}

```
et voilà : 

![résultat](./demo.jpg)
