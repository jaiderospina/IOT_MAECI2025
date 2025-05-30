# IOT_MAECI2025


## Ejercicio Ingeniería Reversa.
Grupos de 2 integrantes. 

Medainte el diseño por ingeniería reversa, documente, contextualice y proponga mejoras al circuito implementado en:
-https://www.tinkercad.com/things/llxvLCzSld1?sharecode=TYMJLJEVoghKrF1JCmxK8VaT1nA_x5cVerGA9XHsunM

Realizar una entrada (carpeta)  en el repositorio.


## Taller 2.

Realizar de manera individual el ejercicio 0 de **"Taller-IoT"**  del aula de clase.

## Taller 3

**Título: Diseño de Algoritmos y Diagramas de Flujo para Soluciones IoT en Ciberseguridad con Arduino**

En grupos de trabajo realizar el ejercicio y subirlo a este repositorio en la carpeta **ENTREGAS_CLASE** creando una carpeta cpor cada grupo y publicar en el README. 

**Objetivo:** Formular algoritmos estructurados y diagramas de flujo que representen el funcionamiento lógico de dos soluciones IoT orientadas a la detección y mitigación de riesgos en ciberseguridad.

**Instrucciones:**

- Desarrolla los algoritmos y diagramas de flujo para las siguientes soluciones IoT usando Arduino. Debes presentar:

- Descripción funcional de cada solución.

- Algoritmo paso a paso en pseudocódigo estructurado.

- Diagrama de flujo correspondiente al algoritmo.

- (Opcional) Avanza en la implementación en código Arduino o simulación en Tinkercad.

- **Ejemplo**

- #### **Solución 1: Sistema de Detección de Intrusos con Autenticación Segura**  
**Descripción:**  
- **Hardware:** Arduino UNO, sensor PIR (movimiento), módulo Wi-Fi ESP8266, LED (verde/rojo), buzzer.  
- **Funcionalidad:**  
  1. Detecta movimiento con sensor PIR.  
  2. Verifica credenciales mediante una clave digital (autenticación).  
  3. Si las credenciales son inválidas, activa alarma (buzzer/LED rojo) y envía alerta a un servidor seguro.  
  4. Registra eventos en un log cifrado.  

**Diagrama de Flujo:**  
```  
Inicio  
  │  
  ├─> Inicializar: Sensor PIR, Wi-Fi, LED, Buzzer  
  │  
  └─> Bucle Infinito:  
        │  
        ├─> ¿Movimiento detectado? → Sí → Solicitar credenciales (clave digital)  
        │   │                              │  
        │   │                              ├─> ¿Credenciales válidas? → Sí → LED verde ON, Log "Acceso autorizado"  
        │   │                              │  
        │   │                              └─> No → Activar alarma (LED rojo + Buzzer), Enviar alerta HTTPS, Log "Intruso"  
        │   │  
        │   └─> No → Esperar 1 segundo  
        │  
        └─> Fin Bucle  
```  

**Pseudocódigo:**  
```python  
Algoritmo Deteccion_Intrusos:  
  Inicializar:  
    pin_PIR = 2, pin_LED_verde = 3, pin_LED_rojo = 4, pin_Buzzer = 5  
    wifi.conectar("SSID", "contraseña")  
    clave_autorizada = "CYB3R$2024"  
    servidor_seguro = "https://alerta-iot.com/api"  

  Repetir para siempre:  
    Si digitalRead(pin_PIR) == ALTO:  
      clave_recibida = solicitarClave()  // Vía serial/App  
      Si clave_recibida == clave_autorizada:  
        digitalWrite(pin_LED_verde, ALTO)  
        registrarLog("Acceso autorizado: " + hora_actual())  
      Sino:  
        digitalWrite(pin_LED_rojo, ALTO)  
        tone(pin_Buzzer, 1000)  
        enviarAlertaHTTPS(servidor_seguro, "Intruso detectado")  
        registrarLog("Intento de intrusión: " + hora_actual())  
        Esperar 10 segundos  
        apagarAlarma()  
      Fin Si  
    Fin Si  
    Esperar 100 ms  
  Fin Repetir  
Fin Algoritmo  
```  
