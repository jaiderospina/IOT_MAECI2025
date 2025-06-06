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

En grupos de trabajo realizar el ejercicio y subirlo a este repositorio en la carpeta **ENTREGA_CLASE** creando una carpeta por cada grupo y publicar en el README. 

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


### Actividad: Creación de Algoritmos y Diagramas de Flujo para Soluciones IoT con Arduino en Ciberseguridad  
**Objetivo:** Diseñar dos sistemas IoT con Arduino que incorporen principios de ciberseguridad, incluyendo pseudocódigo y diagramas de flujo.  

---

---

#### **Solución 2: Firewall Físico para Dispositivos IoT**  
**Descripción:**  
- **Hardware:** Arduino MKR1000, lector RFID (MFRC522), relay, LCD 16x2.  
- **Funcionalidad:**  
  1. Bloquea/desbloquea dispositivos IoT mediante un relay.  
  2. Autentica usuarios con tarjeta RFID.  
  3. Registra accesos en una base de datos con hash SHA-256.  
  4. Muestra estado en LCD.  

**Diagrama de Flujo:**  
```  
Inicio  
  │  
  ├─> Inicializar: RFID, Relay, LCD, Conexión Wi-Fi  
  │  
  └─> Bucle Infinito:  
        │  
        ├─> ¿Tarjeta RFID detectada? → Sí → Leer UID  
        │   │                              │  
        │   │                              ├─> ¿UID autorizado? → Sí → Abrir relay (habilitar dispositivo)  
        │   │                              │       │  
        │   │                              │       ├─> LCD: "Acceso permitido"  
        │   │                              │       └─> Enviar UID + Hora a BD con hash SHA-256  
        │   │                              │  
        │   │                              └─> No → LCD: "Acceso denegado", Cerrar relay (bloquear)  
        │   │  
        │   └─> No → Mantener relay cerrado  
        │  
        └─> Fin Bucle  
```  

**Pseudocódigo:**  
```python  
Algoritmo Firewall_IoT:  
  Inicializar:  
    rfid.iniciar(), relay = 6, LCD.iniciar()  
    UID_autorizados = ["A1B2C3D4", "X5Y6Z7W8"]  
    servidor_BD = "https://bd-iot.com/log"  

  Repetir para siempre:  
    Si rfid.tarjetaPresente():  
      uid = rfid.leerUID()  
      Si uid en UID_autorizados:  
        digitalWrite(relay, ABIERTO)  // Habilitar dispositivo  
        LCD.mostrar("Acceso permitido")  
        hash = SHA256(uid + hora_actual())  
        enviarDatos(servidor_BD, hash)  
      Sino:  
        digitalWrite(relay, CERRADO)  // Bloquear dispositivo  
        LCD.mostrar("Acceso denegado")  
      Fin Si  
      Esperar 3 segundos  
    Fin Si  
    Esperar 200 ms  
  Fin Repetir  
Fin Algoritmo  
```  

---

### **Recomendaciones de Implementación:**  
1. **Ciberseguridad Adicional:**  
   - Usar **TLS/SSL** en comunicaciones Wi-Fi.  
   - Almacenar claves/tokens en memoria **EEPROM encriptada**.  
   - Actualizar firmware vía OTA con firma digital.  
2. **Componentes Opcionales:**  
   - Añadir sensor de huella dactilar para autenticación multifactor.  
   - Integrar **detección de ataques de fuerza bruta** (bloqueo tras 3 intentos).  
3. **Pruebas:**  
   - Simular ataques con herramientas como **Wireshark** o **Kali Linux**.  

### **Entregables:**  
- Diagramas de flujo detallados.  
- Pseudocódigo estructurado.  
- Esquemático de conexiones Arduino.  
- Documentación de medidas de ciberseguridad implementadas.

---

# Taller 4

En grupo implementar en arduino placa física, alguna de las siguientes prácticas u otra de su preferencia.

[Taller Real] **(https://codiziapp.com/introduccion-a-arduino/practicas/6-descripcion-ttp223)**

- **Subirlo en este repositorio en la carpeta **ENTREGA_CLASE** creando una carpeta por cada grupo y publicar en el README.**

Esta debe incluir.

1.  Descripción funcional del circuito.
2.  Campo de aplicación (incluir al menos dos situasiones de seguridad).
2.  Simulación en thinkercad (indicar Link al proyecto).
3.  Esquemático.
4.  Plano.
5.  Lista de componentes.

- **El circuito y su simulación se presentará al resto de la clase.**

  ## REFERENCIAS.

- **Curso AioT**  (https://github.com/diegoperea20/iot-con-ia)
- **Buen material:** (https://github.com/ElCableAmarillo/Introduccion-a-Arduino)
