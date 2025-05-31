# Cómo instalar nuevas librerías en Arduino IDE

Una **librería en Arduino** es un conjunto de código preescrito y organizado que facilita el uso de hardware específico o la implementación de funcionalidades complejas sin tener que escribir todo el código desde cero. Piensa en ella como una "caja de herramientas" llena de funciones y comandos listos para ser utilizados en tus proyectos.

**Importancia:**

* **Reusabilidad y eficiencia:** 
* **Simplificación de la programación:** 
* **Compatibilidad y estandarización:** 
* **Acceso a hardware y funcionalidades avanzadas:** 


- **Presentación.** 
[Presentación de Clase] (https://prezi.com/view/sRRniHDdrdvpbCtWhiHa/)

- Demo ( explicación prática en clase).

- **Apoyo documental**
[Referencia de apoyo] (https://lab.bricogeek.com/tutorial/como-instalar-nuevas-librerias-en-arduino-ide/introduccion)

- **Como crear una librería.** 
(https://youtu.be/n97dPg-kKpM?si=wguaFgDceEqeKJN_)


Comprendido. Procederé a resolver la actividad propuesta, centrándome en la documentación e implementación de la librería **EasyBuzzer** en el contexto del Internet de las Cosas y su simulación en Tinkercad.

**Información Preliminar:**

  * **Librería Elegida para la Parte 2:** EasyBuzzer.
  * **Motivación:** EasyBuzzer no es una librería "nativa" en el sentido de que no aparece en el gestor de librerías común de Tinkercad para ser agregada directamente con un `#include <EasyBuzzer.h>`. Requiere copiar y pegar su código o, en un escenario real, instalarla manualmente. Es sencilla pero útil para demostrar la inclusión de código externo y cómo puede ser aplicado en un contexto IoT (por ejemplo, para alertas audibles).

-----

### **Resolución Paso a Paso de la Actividad: Exploración y Extensión de Librerías Arduino para IoT en Tinkercad**

-----

#### **Parte 1: Documentación de Librerías IoT en Arduino**

**Tarea 1.1: Identificación y Documentación de Librerías IoT Comunes**

He investigado y documentado tres librerías Arduino comúnmente utilizadas en proyectos de Internet de las Cosas (IoT).

-----

**Librería 1:** **`WiFiManager`**

  * **Nombre de la Librería:** `WiFiManager`
  * **Función Principal en IoT:** Simplifica la configuración de credenciales Wi-Fi para dispositivos IoT. Permite que un dispositivo se conecte a una red Wi-Fi sin necesidad de codificar las credenciales directamente en el firmware, creando un punto de acceso (AP) temporal al que el usuario puede conectarse para configurar las credenciales a través de un navegador web. Esto es crucial para la flexibilidad y facilidad de uso en despliegues IoT.
  * **Sensores/Actuadores/Módulos Compatibles (si aplica):** Funciona con módulos Wi-Fi como ESP8266 y ESP32.
  * **Funciones Clave:**
      * `autoConnect()`: Inicia el proceso de conexión Wi-Fi, configurando un AP si es necesario.
      * `startConfigPortal()`: Fuerza el inicio del portal de configuración web.
      * `getSSID()`, `getPassword()`: Obtienen las credenciales Wi-Fi guardadas (aunque `autoConnect` maneja la conexión automáticamente).
  * **Ejemplo de Uso (Pseudocódigo):**
    ```arduino
    #include <ESP8266WiFi.h> // O <WiFi.h> para ESP32
    #include <WiFiManager.h>

    void setup() {
      Serial.begin(115200);
      WiFiManager wm;
      // wm.resetSettings(); // Opcional: para borrar credenciales guardadas
      wm.autoConnect("AutoConnectAP"); // SSID del AP temporal
      Serial.println("¡Conectado a la red Wi-Fi!");
      // Aquí el código IoT principal después de la conexión exitosa
    }

    void loop() {
      // Tu código principal de la aplicación IoT
    }
    ```
  * **Dependencias:** `ESP8266WiFi` (para ESP8266) o `WiFi.h` (para ESP32).
  * **Ventajas en proyectos IoT:**
      * **Facilidad de Configuración:** Permite a los usuarios finales configurar el dispositivo sin necesidad de reprogramación.
      * **Portabilidad:** El mismo firmware puede usarse en diferentes ubicaciones con distintas redes Wi-Fi.
      * **Experiencia de Usuario Mejorada:** Ofrece una interfaz web intuitiva para la configuración.
  * **Fuente de Documentación:** [GitHub - tzapu/WiFiManager](https://github.com/tzapu/WiFiManager)

-----

**Librería 2:** **`PubSubClient`**

  * **Nombre de la Librería:** `PubSubClient`
  * **Función Principal en IoT:** Proporciona un cliente para el protocolo MQTT (Message Queuing Telemetry Transport), un protocolo ligero de mensajería "publicar/suscribir" diseñado para dispositivos con recursos limitados y redes de baja calidad. Es fundamental para la comunicación entre dispositivos IoT y brokers MQTT (servidores) para enviar datos de sensores y recibir comandos.
  * **Sensores/Actuadores/Módulos Compatibles (si aplica):** Funciona con cualquier módulo Wi-Fi o Ethernet que tenga una librería `Client` compatible (ej., `WiFiClient`, `EthernetClient`).
  * **Funciones Clave:**
      * `setServer()`: Configura la dirección IP o el nombre de host del broker MQTT y el puerto.
      * `setCallback()`: Establece una función de devolución de llamada para manejar los mensajes entrantes (suscritos).
      * `connect()`: Intenta establecer una conexión con el broker MQTT.
      * `publish()`: Envía un mensaje a un tópico MQTT.
      * `subscribe()`: Se suscribe a un tópico para recibir mensajes.
      * `loop()`: Debe ser llamada regularmente en el `loop()` principal para mantener la conexión y procesar los mensajes entrantes y salientes.
  * **Ejemplo de Uso (Pseudocódigo):**
    ```arduino
    #include <ESP8266WiFi.h> // O <WiFi.h>
    #include <PubSubClient.h>

    const char* mqtt_server = "broker.hivemq.com";
    WiFiClient espClient;
    PubSubClient client(espClient);

    void callback(char* topic, byte* payload, unsigned int length) {
      // Función para manejar mensajes entrantes
      Serial.print("Mensaje recibido en [");
      Serial.print(topic);
      Serial.print("]: ");
      for (int i = 0; i < length; i++) {
        Serial.print((char)payload[i]);
      }
      Serial.println();
    }

    void setup() {
      Serial.begin(115200);
      // Conectar a Wi-Fi (usando WiFiManager o credenciales fijas)
      // ...
      client.setServer(mqtt_server, 1883);
      client.setCallback(callback);
    }

    void loop() {
      if (!client.connected()) {
        // Reconectar si se pierde la conexión
        // ...
        client.connect("ArduinoClient");
        client.subscribe("mi/topico/sensor");
      }
      client.loop(); // ¡Importante!
      // Publicar datos de sensores cada cierto tiempo
      // client.publish("mi/topico/temperatura", "25.5");
    }
    ```
  * **Dependencias:** Una librería `Client` (ej., `WiFiClient`, `EthernetClient`).
  * **Ventajas en proyectos IoT:**
      * **Eficiencia:** Protocolo ligero, ideal para redes con ancho de banda limitado.
      * **Comunicación Bidireccional:** Permite tanto enviar datos de sensores (publicar) como recibir comandos (suscribir).
      * **Escalabilidad:** Permite conectar múltiples dispositivos a un solo broker.
  * **Fuente de Documentación:** [GitHub - knolleary/pubsubclient](https://github.com/knolleary/pubsubclient)

-----

**Librería 3:** **`ArduinoJson`**

  * **Nombre de la Librería:** `ArduinoJson`
  * **Función Principal en IoT:** Facilita la serialización y deserialización de datos en formato JSON (JavaScript Object Notation). JSON es un formato de intercambio de datos ligero y legible por humanos, ampliamente utilizado en el desarrollo de IoT para enviar y recibir datos estructurados entre dispositivos, servidores y APIs.
  * **Sensores/Actuadores/Módulos Compatibles (si aplica):** Independiente del hardware de comunicación, funciona con cualquier módulo que transmita o reciba cadenas de texto.
  * **Funciones Clave:**
      * `DynamicJsonDocument()`: Crea un documento JSON dinámico en memoria.
      * `StaticJsonDocument()`: Crea un documento JSON estático (tamaño fijo) en memoria.
      * `serializeJson()`: Convierte un documento JSON en una cadena de texto.
      * `deserializeJson()`: Parsea una cadena de texto JSON en un documento JSON.
      * `doc["clave"] = valor;`: Para añadir o modificar valores en el documento.
      * `doc["clave"].as<Tipo>();`: Para obtener valores del documento.
  * **Ejemplo de Uso (Pseudocódigo):**
    ```arduino
    #include <ArduinoJson.h>

    void setup() {
      Serial.begin(115200);

      // Serializar JSON (enviar datos)
      DynamicJsonDocument doc(1024); // Tamaño recomendado: 1024 bytes

      doc["sensor"] = "temperatura";
      doc["valor"] = 23.5;
      doc["unidad"] = "C";

      String jsonString;
      serializeJson(doc, jsonString);
      Serial.print("JSON enviado: ");
      Serial.println(jsonString);
      // Enviar jsonString a un broker MQTT o API REST...

      // Deserializar JSON (recibir datos)
      String receivedJson = "{\"dispositivo\":\"luces\",\"estado\":\"on\"}";
      DynamicJsonDocument receivedDoc(1024);
      DeserializationError error = deserializeJson(receivedDoc, receivedJson);

      if (error) {
        Serial.print("Error al deserializar: ");
        Serial.println(error.c_str());
        return;
      }

      Serial.print("Dispositivo: ");
      Serial.println(receivedDoc["dispositivo"].as<String>());
      Serial.print("Estado: ");
      Serial.println(receivedDoc["estado"].as<String>());
    }

    void loop() {
      // Nada en el loop para este ejemplo
    }
    ```
  * **Dependencias:** Ninguna (auto-contenido).
  * **Ventajas en proyectos IoT:**
      * **Formato Estructurado:** Facilita el intercambio de datos complejos.
      * **Legibilidad:** JSON es fácil de leer y entender para humanos.
      * **Compatibilidad:** Ampliamente soportado por APIs, servidores y otras plataformas IoT.
  * **Fuente de Documentación:** [ArduinoJson.org](https://arduinojson.org/)

-----

#### **Parte 2: Investigación e Implementación de una Librería "Externa" en Tinkercad**

**Librería Seleccionada:** **`EasyBuzzer`**

  * **Motivo de Selección como "Externa":** EasyBuzzer no está preinstalada ni es directamente importable en Tinkercad a través del gestor de librerías. Para usarla, se debe copiar su código fuente directamente en la pestaña del código, simulando un escenario donde se descarga una librería de GitHub y se integra manualmente en un proyecto. Su simplicidad permite enfocarse en la dinámica de agregar código externo sin abrumar con dependencias complejas.

**Tarea 2.1: Investigación y Selección de una Librería "No Nativa" para Tinkercad**

  * **Nombre de la Librería:** `EasyBuzzer`

  * **Función Principal:** Permite controlar un zumbador (buzzer) de manera sencilla para generar tonos, secuencias de tonos, pitidos repetitivos o simplemente encender/apagar el zumbador por un tiempo determinado, sin bloquear el programa principal (`loop()`). Esto es útil para alertas audibles en dispositivos IoT sin interrumpir otras operaciones (ej., lectura de sensores, comunicación de red).

  * **Funciones Clave a Replicar/Usar:**

      * `singleBeep(frequency, duration)`: Un solo pitido a una frecuencia y duración dadas.
      * `startBeep(frequency)`: Inicia un pitido continuo.
      * `stopBeep()`: Detiene el pitido.
      * `beep()`: Función a llamar en el `loop()` para que la librería maneje los tiempos de los pitidos.

  * **Plan de Adaptación para Tinkercad:**

    1.  **Obtener el código fuente:** Copiar el contenido del archivo `.h` y `.cpp` de EasyBuzzer (o una versión simplificada si es muy grande) y pegarlo directamente en la sección del código de Tinkercad. Esto simula la inclusión manual.
    2.  **Circuito en Tinkercad:** Un Arduino Uno, un zumbador (piezo), y una resistencia limitadora de corriente.
    3.  **Integración:** Incluir el código de la librería y usar sus funciones en el `setup()` y `loop()` del sketch principal.
    4.  **Contexto IoT (Simulado):** Simular una alerta audible basada en un evento IoT. Por ejemplo, un sensor de temperatura "virtual" excede un umbral y el buzzer emite una alerta.

**Tarea 2.2: Implementación y Demostración en Tinkercad**

**Paso 1: Configurar el Circuito en Tinkercad**

1.  Abre Tinkercad y crea un nuevo diseño de circuito.

2.  Arrastra un **Arduino Uno R3**.

3.  Arrastra un **Zumbador piezoeléctrico** (Buzzer).

4.  Arrastra una **Resistencia** (valor recomendado: 220 Ohm o 1k Ohm).

5.  **Conexiones:**

      * Conecta el terminal positivo del zumbador al **pin digital 9** del Arduino (o cualquier pin PWM para tonos).
      * Conecta el terminal negativo del zumbador a un extremo de la resistencia.
      * Conecta el otro extremo de la resistencia a **GND** del Arduino.

    *(Nota: No puedo insertar la imagen directamente, pero el usuario debe crear este circuito simple en Tinkercad.)*

**Paso 2: Obtener y Adaptar el Código de EasyBuzzer**

EasyBuzzer se compone de dos archivos: `EasyBuzzer.h` y `EasyBuzzer.cpp`. Para Tinkercad, la forma más sencilla es "aplanar" la librería en un solo archivo o pegar las implementaciones directamente. Aquí, voy a adaptar las funciones clave directamente en el sketch, simulando la inclusión.

**Código de la librería (adaptado y simplificado para pegar en Tinkercad):**

```cpp
// --- Inicio del código adaptado de EasyBuzzer ---
// Originalmente de: https://github.com/thomasfredericks/EasyBuzzer

#ifndef EasyBuzzer_h
#define EasyBuzzer_h

#include <Arduino.h>

// Definiciones para tipos de beeps
#define BEEP_SINGLE 0
#define BEEP_START 1
#define BEEP_STOP 2

class EasyBuzzerClass {
  public:
    EasyBuzzerClass() :
      _pin(0),
      _frequency(0),
      _duration(0),
      _repetitions(0),
      _onDuration(0),
      _offDuration(0),
      _isContinuous(false),
      _currentRepetition(0),
      _beepOn(false),
      _lastBeepTime(0),
      _beepType(BEEP_STOP)
    {}

    void setPin(uint8_t pin) {
      _pin = pin;
      pinMode(_pin, OUTPUT);
    }

    // Funciones principales
    void singleBeep(unsigned int frequency, unsigned long duration) {
      _frequency = frequency;
      _duration = duration;
      _repetitions = 1;
      _onDuration = duration;
      _offDuration = 0;
      _isContinuous = false;
      _currentRepetition = 0;
      _beepOn = true;
      _lastBeepTime = millis();
      _beepType = BEEP_SINGLE;
      tone(_pin, _frequency);
    }

    void startBeep(unsigned int frequency) {
      _frequency = frequency;
      _isContinuous = true;
      _beepOn = true;
      _lastBeepTime = millis();
      _beepType = BEEP_START;
      tone(_pin, _frequency);
    }

    void stopBeep() {
      noTone(_pin);
      _isContinuous = false;
      _beepOn = false;
      _beepType = BEEP_STOP;
    }

    // Función a llamar en el loop()
    void beep() {
      if (_beepType == BEEP_SINGLE && _beepOn) {
        if (millis() - _lastBeepTime >= _duration) {
          noTone(_pin);
          _beepOn = false;
          _beepType = BEEP_STOP;
        }
      }
      // Implementación de repeticiones y encendido/apagado cíclico
      // para este ejercicio, solo nos enfocamos en singleBeep y start/stop
    }

  private:
    uint8_t _pin;
    unsigned int _frequency;
    unsigned long _duration;
    int _repetitions;
    unsigned long _onDuration;
    unsigned long _offDuration;
    boolean _isContinuous;
    int _currentRepetition;
    boolean _beepOn;
    unsigned long _lastBeepTime;
    int _beepType;
};

// Instancia global de la librería
EasyBuzzerClass EasyBuzzer;

#endif
// --- Fin del código adaptado de EasyBuzzer ---
```

**Paso 3: Escribir el Sketch Principal en Tinkercad**

En la pestaña "Código" de Tinkercad, selecciona "Texto" y pega el código de la librería adaptada primero, y luego tu sketch principal.

```arduino
// --- Inicio del código adaptado de EasyBuzzer ---
// Originalmente de: https://github.com/thomasfredericks/EasyBuzzer

#ifndef EasyBuzzer_h
#define EasyBuzzer_h

#include <Arduino.h>

// Definiciones para tipos de beeps
#define BEEP_SINGLE 0
#define BEEP_START 1
#define BEEP_STOP 2

class EasyBuzzerClass {
  public:
    EasyBuzzerClass() :
      _pin(0),
      _frequency(0),
      _duration(0),
      _repetitions(0),
      _onDuration(0),
      _offDuration(0),
      _isContinuous(false),
      _currentRepetition(0),
      _beepOn(false),
      _lastBeepTime(0),
      _beepType(BEEP_STOP)
    {}

    void setPin(uint8_t pin) {
      _pin = pin;
      pinMode(_pin, OUTPUT);
    }

    // Funciones principales
    void singleBeep(unsigned int frequency, unsigned long duration) {
      _frequency = frequency;
      _duration = duration;
      _repetitions = 1;
      _onDuration = duration;
      _offDuration = 0;
      _isContinuous = false;
      _currentRepetition = 0;
      _beepOn = true;
      _lastBeepTime = millis();
      _beepType = BEEP_SINGLE;
      tone(_pin, _frequency);
    }

    void startBeep(unsigned int frequency) {
      _frequency = frequency;
      _isContinuous = true;
      _beepOn = true;
      _lastBeepTime = millis();
      _beepType = BEEP_START;
      tone(_pin, _frequency);
    }

    void stopBeep() {
      noTone(_pin);
      _isContinuous = false;
      _beepOn = false;
      _beepType = BEEP_STOP;
    }

    // Función a llamar en el loop()
    void beep() {
      if (_beepType == BEEP_SINGLE && _beepOn) {
        if (millis() - _lastBeepTime >= _duration) {
          noTone(_pin);
          _beepOn = false;
          _beepType = BEEP_STOP;
        }
      }
      // Para este ejercicio, las funciones de repetición y ciclos más complejos
      // de la librería original han sido omitidas para simplificar la adaptación.
      // Aquí se podría expandir si se desea simular más comportamientos.
    }

  private:
    uint8_t _pin;
    unsigned int _frequency;
    unsigned long _duration;
    int _repetitions;
    unsigned long _onDuration;
    unsigned long _offDuration;
    boolean _isContinuous;
    int _currentRepetition;
    boolean _beepOn;
    unsigned long _lastBeepTime;
    int _beepType;
};

// Instancia global de la librería
EasyBuzzerClass EasyBuzzer;

#endif
// --- Fin del código adaptado de EasyBuzzer ---


// --- Sketch Principal de Arduino en Tinkercad ---

const int BUZZER_PIN = 9; // Pin donde conectamos el buzzer

// Variables para simular un sensor y un umbral (contexto IoT)
float simulatedTemperature = 20.0;
const float TEMPERATURE_THRESHOLD = 25.0;
unsigned long lastSensorReadTime = 0;
const unsigned long SENSOR_READ_INTERVAL = 2000; // Leer sensor cada 2 segundos

void setup() {
  Serial.begin(9600);
  Serial.println("Iniciando simulacion IoT con EasyBuzzer...");

  // Configurar el pin del buzzer en la librería EasyBuzzer
  EasyBuzzer.setPin(BUZZER_PIN);
}

void loop() {
  // Simular la lectura de un sensor (contexto IoT)
  if (millis() - lastSensorReadTime >= SENSOR_READ_INTERVAL) {
    // Simular un cambio de temperatura
    simulatedTemperature += random(-20, 30) / 10.0; // Variación aleatoria
    if (simulatedTemperature > 35.0) simulatedTemperature = 20.0; // Reset para que no suba infinitamente
    if (simulatedTemperature < 15.0) simulatedTemperature = 20.0;

    Serial.print("Temperatura simulada: ");
    Serial.print(simulatedTemperature);
    Serial.println(" C");

    // Lógica de alerta basada en la temperatura (IoT)
    if (simulatedTemperature > TEMPERATURE_THRESHOLD) {
      Serial.println("¡Alerta de temperatura alta! Activando zumbador.");
      // Usar EasyBuzzer para un pitido continuo si la temperatura es alta
      EasyBuzzer.startBeep(1000); // Frecuencia de 1000 Hz
    } else {
      // Si la temperatura es normal, detener el zumbador
      if (EasyBuzzer._beepType == BEEP_START) { // Verifica si el buzzer está en modo continuo
         Serial.println("Temperatura normal. Deteniendo zumbador.");
         EasyBuzzer.stopBeep();
      } else if (simulatedTemperature < 18.0) { // Otro tipo de alerta para baja temperatura
         Serial.println("Temperatura baja. Pitido corto.");
         EasyBuzzer.singleBeep(500, 200); // 500 Hz por 200 ms
      }
    }
    lastSensorReadTime = millis();
  }

  // ¡Importante! Llamar a beep() para que EasyBuzzer gestione los tiempos
  EasyBuzzer.beep();

  // Pequeño delay para no saturar el simulador, aunque millis() lo maneja bien
  delay(10);
}
```

**Paso 4: Demostración y Explicación**

1.  **Iniciar Simulación:** Haz clic en "Iniciar simulación" en Tinkercad.
2.  **Observar el comportamiento:**
      * Verás la temperatura simulada en el Monitor Serie.
      * Cuando la `simulatedTemperature` supere los `TEMPERATURE_THRESHOLD` (25.0°C), el zumbador emitirá un tono continuo (gracias a `EasyBuzzer.startBeep(1000)`).
      * Cuando la temperatura vuelva a ser normal, el zumbador se detendrá (gracias a `EasyBuzzer.stopBeep()`).
      * Cuando la temperatura sea muy baja (menos de 18.0°C), el zumbador emitirá un pitido corto (`EasyBuzzer.singleBeep(500, 200)`).
      * Observa cómo el `loop()` principal sigue ejecutándose y mostrando la temperatura, demostrando el comportamiento no bloqueante de EasyBuzzer.

**Explicación de la Implementación:**

  * **Librería Elegida ("No Nativa"):** `EasyBuzzer`. Fue seleccionada porque no está disponible directamente en el gestor de librerías de Tinkercad.
  * **Adaptación:** En lugar de instalarla como una librería tradicional, copié una versión simplificada de su código fuente (`.h` y `.cpp` combinados en una clase simple) y la pegué directamente al inicio del sketch de Tinkercad. Esto imita el proceso de descargar una librería de GitHub y agregarla manualmente al proyecto.
  * **Simulación en Tinkercad:**
      * Utilizamos un Arduino Uno y un zumbador piezoeléctrico, que Tinkercad simula bien a nivel de hardware.
      * Para el contexto IoT, creamos una variable `simulatedTemperature` que actúa como si fuera la lectura de un sensor real. Esta temperatura varía aleatoriamente para disparar las alertas.
      * La lógica en el `loop()` verifica esta temperatura simulada y utiliza las funciones de `EasyBuzzer` (`startBeep()`, `stopBeep()`, `singleBeep()`) para emitir alertas audibles.
      * La función `EasyBuzzer.beep()` es crucial en el `loop()` para que la librería maneje los tiempos de los pitidos de forma no bloqueante, lo que permite que el resto del código (como la simulación del sensor) continúe ejecutándose.
  * **Desafíos y Superación:**
      * **Inclusión de Librería:** El principal desafío fue cómo incluir una librería que no está en el gestor de Tinkercad. La solución fue "aplanar" la librería y pegar su código directamente en el sketch, lo cual es una técnica común para librerías pequeñas o para depuración.
      * **Simulación de Eventos IoT:** Como no tenemos sensores reales o conectividad de red en la simulación directa de Tinkercad para este ejercicio, se optó por simular los datos del sensor y los umbrales para desencadenar las alertas del zumbador, manteniendo el enfoque en la integración de la librería.
      * **Funcionalidad Completa:** Se simplificó la librería `EasyBuzzer` a sus funciones más básicas (`singleBeep`, `startBeep`, `stopBeep`) para este ejercicio, ya que implementar todas sus características de repetición y secuencias complejas añadiría mucha más complejidad al código pegado, sin aportar un valor adicional significativo a la demostración de la "inclusión externa".
  * **Relación con IoT:** Este ejercicio demuestra cómo una librería simple como `EasyBuzzer` puede ser una herramienta invaluable en proyectos IoT para proporcionar retroalimentación audible (alertas, notificaciones) de manera eficiente y no bloqueante. En un sistema IoT real, `simulatedTemperature` sería la lectura de un sensor real enviada a través de MQTT o HTTP, y el buzzer alertaría al usuario sobre condiciones críticas. La capacidad de integrar librerías "externas" es fundamental para adaptar las soluciones IoT a requisitos específicos de hardware o funcionalidades no estándar.

-----

**Entregables:**

1.  **Documento de Librerías IoT (Parte 1):** (Representado por la documentación detallada de `WiFiManager`, `PubSubClient`, y `ArduinoJson` arriba).
2.  **Enlace a Proyecto Tinkercad (Parte 2):** (El estudiante debería proporcionar un enlace público a su proyecto Tinkercad con el circuito y el código completo, incluyendo la adaptación de EasyBuzzer).
      * *(Ejemplo de URL que el estudiante generaría: `https://www.tinkercad.com/things/your-project-id`)*
3.  **Breve Presentación/Explicación (Parte 2):** (Representado por la sección "Explicación de la Implementación" anterior, que un estudiante podría presentar oralmente o en un informe corto).

Esta resolución paso a paso cubre todos los aspectos de la actividad propuesta, utilizando EasyBuzzer como el caso de estudio para la librería "externa" en Tinkercad dentro del contexto IoT.
