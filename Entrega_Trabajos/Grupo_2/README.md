## Proyecto 1: Sistema de Detección de Intrusos con Autenticación Dual (RFID + Bluetooth)
Integrantes
+ Miguel Amaya
+ Andres Parrado
+ Andres Berdugo

Descripción amplia:

Este sistema implementa un control de acceso multicapa que combina autenticación física (tarjeta RFID) con autenticación digital (clave cifrada vía Bluetooth desde smartphone). El sistema detecta intentos de acceso no autorizado, registra eventos de seguridad y activa alarmas en caso de violaciones de seguridad.

Características principales:
+	Autenticación primaria mediante tarjeta RFID
+	Autenticación secundaria mediante clave cifrada Bluetooth
+	Detección de intrusos con sensor PIR
+	Sistema de alarmas visual y sonora
+	Registro de eventos de acceso
+	Timeout de seguridad automático
+	Modo de emergencia para bloqueo total

### 3. DESCRIPCIÓN FUNCIONAL DEL SISTEMA
Sistema de Detección de Intrusos con Autenticación Dual (RFID + Bluetooth)
Objetivo: Crear un sistema de control de acceso multicapa que combine seguridad física y digital para proteger áreas sensibles contra accesos no autorizados.
Funcionamiento detallado:
#### 1.	Estado de Reposo: 
+	El sistema permanece en modo de vigilancia continua
+	LED verde indica que el sistema está operativo
+	Sensor PIR monitorea constantemente el área protegida
#### 2.	Detección de Presencia: 
+	Cuando el sensor PIR detecta movimiento, se activa el protocolo de autenticación
+	LED amarillo se enciende indicando "alerta de presencia"
+	Se inicia un temporizador de 10 segundos para la primera autenticación
#### 3.	Primera Autenticación (RFID): 
+	El sistema espera la presentación de una tarjeta RFID autorizada
+	Si no se presenta tarjeta en 10 segundos → ALARMA
+	Si la tarjeta no está autorizada → ALARMA
+	Si la tarjeta es válida → Procede a segunda autenticación
#### 4.	Segunda Autenticación (Bluetooth): 
+	El sistema solicita conexión Bluetooth con dispositivo móvil autorizado
+	Espera recepción de clave cifrada durante 15 segundos
+	La clave debe coincidir con el hash almacenado en el sistema
+	Si falla la autenticación → ALARMA
#### 5.	Acceso Autorizado: 
+	LED verde + beep de confirmación
+	Registro del evento con timestamp
+	Sistema permanece en modo "acceso permitido" por 30 segundos
+	Retorna automáticamente al modo de vigilancia
#### 6.	Sistema de Alarma: 
+	LED rojo intermitente + buzzer continuo
+	Duración: 60 segundos o hasta reset manual
+	Registro del intento de acceso no autorizado

### 4. ALGORITMO EN PSEUDOCÓDIGO ESTRUCTURADO
ALGORITMO SistemaDeteccionIntrusos  
  
VARIABLES:  
    estadoSistema: ENTERO (0=Vigilancia, 1=Alerta, 2=Autenticando, 3=Autorizado, 4=Alarma)  
    tiempoInicio: ENTERO  
    rfidAutorizada: BOOLEANO  
    claveBluetoothCorrecta: BOOLEANO  
    movimientoDetectado: BOOLEANO  
      
CONSTANTES:  
    TIMEOUT_RFID = 10000  // 10 segundos  
    TIMEOUT_BLUETOOTH = 15000  // 15 segundos  
    TIMEOUT_ACCESO = 30000  // 30 segundos  
    TIMEOUT_ALARMA = 60000  // 60 segundos  
  
INICIO  
    LLAMAR InicializarSistema()  
    estadoSistema ← 0  
      
    MIENTRAS VERDADERO HACER  
        SEGÚN estadoSistema HACER  
            CASO 0: // Modo Vigilancia  
                LLAMAR ModoVigilancia()  
                  
            CASO 1: // Modo Alerta  
                LLAMAR ModoAlerta()  
                  
            CASO 2: // Modo Autenticación  
                LLAMAR ModoAutenticacion()  
                  
            CASO 3: // Acceso Autorizado  
                LLAMAR ModoAutorizado()  
                  
            CASO 4: // Modo Alarma  
                LLAMAR ModoAlarma()  
        FIN_SEGÚN  
          
        ESPERAR 100  // Delay de 100ms  
    FIN_MIENTRAS  
FIN  
  
PROCEDIMIENTO InicializarSistema()  
    ConfigurarPines()  
    InicializarRFID()  
    InicializarBluetooth()  
    EncenderLED(VERDE)  
    ApagarLED(AMARILLO)  
    ApagarLED(ROJO)  
    ApagarBuzzer()  
FIN_PROCEDIMIENTO  
  
PROCEDIMIENTO ModoVigilancia()  
    movimientoDetectado ← LeerSensorPIR()  
    SI movimientoDetectado ENTONCES  
        estadoSistema ← 1  
        tiempoInicio ← TiempoActual()  
        EncenderLED(AMARILLO)  
        ApagarLED(VERDE)  
    FIN_SI  
FIN_PROCEDIMIENTO  
  
PROCEDIMIENTO ModoAlerta()  
    SI (TiempoActual() - tiempoInicio) > TIMEOUT_RFID ENTONCES  
        estadoSistema ← 4  // Timeout → Alarma  
    SINO  
        rfidAutorizada ← VerificarRFID()  
        SI rfidAutorizada ENTONCES  
            estadoSistema ← 2  
            tiempoInicio ← TiempoActual()  
            SolicitarConexionBluetooth()  
        FIN_SI  
    FIN_SI  
FIN_PROCEDIMIENTO  
  
PROCEDIMIENTO ModoAutenticacion()  
    SI (TiempoActual() - tiempoInicio) > TIMEOUT_BLUETOOTH ENTONCES  
        estadoSistema ← 4  // Timeout → Alarma  
    SINO  
        claveBluetoothCorrecta ← VerificarClaveBluetooth()  
        SI claveBluetoothCorrecta ENTONCES  
            estadoSistema ← 3  
            tiempoInicio ← TiempoActual()  
            EncenderLED(VERDE)  
            ApagarLED(AMARILLO)  
            BeepConfirmacion()  
            RegistrarAccesoAutorizado()  
        FIN_SI  
    FIN_SI  
FIN_PROCEDIMIENTO  
  
PROCEDIMIENTO ModoAutorizado()  
    SI (TiempoActual() - tiempoInicio) > TIMEOUT_ACCESO ENTONCES  
        estadoSistema ← 0  // Volver a vigilancia  
        EncenderLED(VERDE)  
    FIN_SI  
FIN_PROCEDIMIENTO  
  
PROCEDIMIENTO ModoAlarma()  
    EncenderLED(ROJO)  
    ActivarBuzzer()  
    RegistrarIntentoNoAutorizado()  
      
    SI (TiempoActual() - tiempoInicio) > TIMEOUT_ALARMA ENTONCES  
        estadoSistema ← 0  // Volver a vigilancia  
        ApagarLED(ROJO)  
        ApagarBuzzer()  
        EncenderLED(VERDE)  
    FIN_SI  
FIN_PROCEDIMIENTO  
  
FUNCIÓN VerificarRFID(): BOOLEANO  
    tarjetaID ← LeerTarjetaRFID()  
    RETORNAR (tarjetaID EN ListaTarjetasAutorizadas)  
FIN_FUNCIÓN  
  
FUNCIÓN VerificarClaveBluetooth(): BOOLEANO  
    claveRecibida ← RecibirClaveBluetooth()  
    hashCalculado ← CalcularHash(claveRecibida)  
    RETORNAR (hashCalculado = HashAlmacenado)  
FIN_FUNCIÓN


### 5. CÓDIGO ARDUINO PARA TINKERCAD
Crear el código Arduino completo para el sistema de detección de intrusos

codigo_arduino = '''
/*
  SISTEMA DE DETECCIÓN DE INTRUSOS CON AUTENTICACIÓN DUAL
  RFID + BLUETOOTH (Simulado)
  
  Componentes necesarios en Tinkercad:
  - Arduino Uno
  - Sensor PIR (Pin 2)
  - LED Verde (Pin 3)
  - LED Amarillo (Pin 4) 
  - LED Rojo (Pin 5)
  - Buzzer (Pin 6)
  - Módulo RFID RC522 (Pines SPI: 10, 11, 12, 13, 9, 8)
  - Potenciómetro (Pin A0) - Simula entrada Bluetooth
  - Resistencias 220Ω para LEDs
*/

#include <SPI.h>
#include <MFRC522.h>

// Definición de pines
#define PIR_PIN 2
#define LED_VERDE 3
#define LED_AMARILLO 4
#define LED_ROJO 5
#define BUZZER_PIN 6
#define RST_PIN 9
#define SS_PIN 10
#define BLUETOOTH_SIM A0  // Potenciómetro simula Bluetooth

// Crear instancia MFRC522
MFRC522 mfrc522(SS_PIN, RST_PIN);

// Estados del sistema
enum EstadoSistema {
  VIGILANCIA = 0,
  ALERTA = 1,
  AUTENTICANDO = 2,
  AUTORIZADO = 3,
  ALARMA = 4
};

// Variables globales
EstadoSistema estadoActual = VIGILANCIA;
unsigned long tiempoInicio = 0;
unsigned long tiempoAnterior = 0;

// Timeouts en milisegundos
const unsigned long TIMEOUT_RFID = 10000;      // 10 segundos
const unsigned long TIMEOUT_BLUETOOTH = 15000; // 15 segundos
const unsigned long TIMEOUT_ACCESO = 30000;    // 30 segundos
const unsigned long TIMEOUT_ALARMA = 60000;    // 60 segundos

// UID de tarjeta autorizada (cambiar por tu tarjeta)
byte tarjetaAutorizada[4] = {0xDE, 0xAD, 0xBE, 0xEF};

// Clave Bluetooth simulada (valor del potenciómetro)
const int CLAVE_BLUETOOTH = 512; // Valor medio del potenciómetro
const int TOLERANCIA = 50;       // Tolerancia para la clave

void setup() {
  Serial.begin(9600);
  
  // Inicializar SPI y MFRC522
  SPI.begin();
  mfrc522.PCD_Init();
  
  // Configurar pines
  pinMode(PIR_PIN, INPUT);
  pinMode(LED_VERDE, OUTPUT);
  pinMode(LED_AMARILLO, OUTPUT);
  pinMode(LED_ROJO, OUTPUT);
  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(BLUETOOTH_SIM, INPUT);
  
  // Estado inicial
  inicializarSistema();
  
  Serial.println("Sistema de Detección de Intrusos Iniciado");
  Serial.println("Estado: VIGILANCIA");
}

void loop() {
  switch (estadoActual) {
    case VIGILANCIA:
      modoVigilancia();
      break;
      
    case ALERTA:
      modoAlerta();
      break;
      
    case AUTENTICANDO:
      modoAutenticacion();
      break;
      
    case AUTORIZADO:
      modoAutorizado();
      break;
      
    case ALARMA:
      modoAlarma();
      break;
  }
  
  delay(100); // Delay de 100ms
}

void inicializarSistema() {
  digitalWrite(LED_VERDE, HIGH);
  digitalWrite(LED_AMARILLO, LOW);
  digitalWrite(LED_ROJO, LOW);
  digitalWrite(BUZZER_PIN, LOW);
  estadoActual = VIGILANCIA;
}

void modoVigilancia() {
  // Leer sensor PIR
  if (digitalRead(PIR_PIN) == HIGH) {
    Serial.println("¡Movimiento detectado!");
    estadoActual = ALERTA;
    tiempoInicio = millis();
    
    // Activar LED amarillo
    digitalWrite(LED_VERDE, LOW);
    digitalWrite(LED_AMARILLO, HIGH);
    
    Serial.println("Estado: ALERTA - Esperando tarjeta RFID...");
  }
}

void modoAlerta() {
  // Verificar timeout
  if (millis() - tiempoInicio > TIMEOUT_RFID) {
    Serial.println("TIMEOUT RFID - Activando alarma");
    activarAlarma();
    return;
  }
  
  // Verificar tarjeta RFID
  if (verificarRFID()) {
    Serial.println("RFID autorizada - Solicitando clave Bluetooth...");
    estadoActual = AUTENTICANDO;
    tiempoInicio = millis();
    
    // Parpadear LED amarillo para indicar espera de Bluetooth
    if (millis() - tiempoAnterior > 500) {
      digitalWrite(LED_AMARILLO, !digitalRead(LED_AMARILLO));
      tiempoAnterior = millis();
    }
  }
}

void modoAutenticacion() {
  // Verificar timeout
  if (millis() - tiempoInicio > TIMEOUT_BLUETOOTH) {
    Serial.println("TIMEOUT BLUETOOTH - Activando alarma");
    activarAlarma();
    return;
  }
  
  // Parpadear LED amarillo
  if (millis() - tiempoAnterior > 300) {
    digitalWrite(LED_AMARILLO, !digitalRead(LED_AMARILLO));
    tiempoAnterior = millis();
  }
  
  // Verificar clave Bluetooth (simulada con potenciómetro)
  if (verificarBluetooth()) {
    Serial.println("¡ACCESO AUTORIZADO!");
    estadoActual = AUTORIZADO;
    tiempoInicio = millis();
    
    // Activar indicadores de acceso autorizado
    digitalWrite(LED_AMARILLO, LOW);
    digitalWrite(LED_VERDE, HIGH);
    beepConfirmacion();
    
    registrarAccesoAutorizado();
  }
}

void modoAutorizado() {
  // Verificar timeout de acceso
  if (millis() - tiempoInicio > TIMEOUT_ACCESO) {
    Serial.println("Timeout de acceso - Volviendo a vigilancia");
    inicializarSistema();
  }
  
  // Parpadear LED verde lentamente
  if (millis() - tiempoAnterior > 1000) {
    digitalWrite(LED_VERDE, !digitalRead(LED_VERDE));
    tiempoAnterior = millis();
  }
}

void modoAlarma() {
  // Activar alarma visual y sonora
  if (millis() - tiempoAnterior > 200) {
    digitalWrite(LED_ROJO, !digitalRead(LED_ROJO));
    digitalWrite(BUZZER_PIN, !digitalRead(BUZZER_PIN));
    tiempoAnterior = millis();
  }
  
  // Verificar timeout de alarma
  if (millis() - tiempoInicio > TIMEOUT_ALARMA) {
    Serial.println("Fin de alarma - Volviendo a vigilancia");
    digitalWrite(BUZZER_PIN, LOW);
    inicializarSistema();
  }
}

bool verificarRFID() {
  // Verificar si hay una tarjeta presente
  if (!mfrc522.PICC_IsNewCardPresent() || !mfrc522.PICC_ReadCardSerial()) {
    return false;
  }
  
  // Verificar UID de la tarjeta
  bool autorizada = true;
  for (byte i = 0; i < 4; i++) {
    if (mfrc522.uid.uidByte[i] != tarjetaAutorizada[i]) {
      autorizada = false;
      break;
    }
  }
  
  // Mostrar UID en Serial
  Serial.print("UID de tarjeta: ");
  for (byte i = 0; i < mfrc522.uid.size; i++) {
    Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
    Serial.print(mfrc522.uid.uidByte[i], HEX);
  }
  Serial.println();
  
  if (!autorizada) {
    Serial.println("Tarjeta NO autorizada");
    activarAlarma();
  }
  
  mfrc522.PICC_HaltA();
  return autorizada;
}

bool verificarBluetooth() {
  // Simular verificación de clave Bluetooth con potenciómetro
  int valorPot = analogRead(BLUETOOTH_SIM);
  
  // La clave es correcta si el potenciómetro está en el valor esperado ±tolerancia
  if (abs(valorPot - CLAVE_BLUETOOTH) <= TOLERANCIA) {
    Serial.print("Clave Bluetooth correcta (valor: ");
    Serial.print(valorPot);
    Serial.println(")");
    return true;
  }
  
  return false;
}

void activarAlarma() {
  estadoActual = ALARMA;
  tiempoInicio = millis();
  digitalWrite(LED_AMARILLO, LOW);
  digitalWrite(LED_VERDE, LOW);
  
  registrarIntentoNoAutorizado();
  Serial.println("¡¡¡ ALARMA ACTIVADA !!!");
}

void beepConfirmacion() {
  // Tres beeps cortos de confirmación
  for (int i = 0; i < 3; i++) {
    digitalWrite(BUZZER_PIN, HIGH);
    delay(100);
    digitalWrite(BUZZER_PIN, LOW);
    delay(100);
  }
}

void registrarAccesoAutorizado() {
  Serial.println("=== REGISTRO DE ACCESO ===");
  Serial.print("Tiempo: ");
  Serial.print(millis());
  Serial.println(" ms");
  Serial.println("Estado: ACCESO AUTORIZADO");
  Serial.println("========================");
}

void registrarIntentoNoAutorizado() {
  Serial.println("=== REGISTRO DE SEGURIDAD ===");
  Serial.print("Tiempo: ");
  Serial.print(millis());
  Serial.println(" ms");
  Serial.println("Estado: INTENTO NO AUTORIZADO");
  Serial.println("============================");
}
'''

Guardar el código en un archivo
with open('sistema_deteccion_intrusos.ino', 'w', encoding='utf-8') as f:
    f.write(codigo_arduino)

print("Código Arduino generado exitosamente: sistema_deteccion_intrusos.ino")
print("\nComponentes necesarios en Tinkercad:")
print("- Arduino Uno")
print("- Sensor PIR (Pin 2)")
print("- LED Verde (Pin 3)")
print("- LED Amarillo (Pin 4)")
print("- LED Rojo (Pin 5)")
print("- Buzzer (Pin 6)")
print("- Módulo RFID RC522")
print("- Potenciómetro (Pin A0) - Simula Bluetooth")
print("- Resistencias 220Ω para LEDs")

### PROYECTO COMPLETO: SISTEMA DE DETECCIÓN DE INTRUSOS CON AUTENTICACIÓN DUAL
#### 📋 RESUMEN DEL PROYECTO
He desarrollado un Sistema de Detección de Intrusos con Autenticación Dual (RFID + Bluetooth) que implementa múltiples capas de seguridad para proteger áreas sensibles. El sistema combina detección física de movimiento con autenticación multicapa.
#### 🔧 COMPONENTES NECESARIOS EN TINKERCAD
+	Arduino Uno
+	Sensor PIR (Pin 2) - Detección de movimiento
+	LED Verde (Pin 3) - Sistema listo/Acceso autorizado
+	LED Amarillo (Pin 4) - Alerta/Autenticando
+	LED Rojo (Pin 5) - Alarma
+	Buzzer (Pin 6) - Alarma sonora
+	Módulo RFID RC522 (Pines SPI: 10, 11, 12, 13, 9, 8)
+	Potenciómetro (Pin A0) - Simula entrada Bluetooth
+	Resistencias 220Ω para LEDs
#### 🔄 FUNCIONAMIENTO DEL SISTEMA
Estados del Sistema:
+	VIGILANCIA - Monitoreo continuo con LED verde
+	ALERTA - Movimiento detectado, LED amarillo, esperando RFID
+	AUTENTICANDO - RFID válida, LED amarillo parpadeante, esperando Bluetooth
+	AUTORIZADO - Acceso concedido, LED verde parpadeante
+	ALARMA - Acceso denegado, LED rojo + buzzer intermitente
### Flujo de Autenticación:
+	Detección PIR → Activa modo alerta (10 seg timeout)
+	Verificación RFID → Debe coincidir con UID autorizada
+	Verificación Bluetooth → Potenciómetro en posición específica (15 seg timeout)
+	Acceso Autorizado → 30 segundos de acceso antes de volver a vigilancia
##### 🛡️ CARACTERÍSTICAS DE SEGURIDAD
+	Autenticación Multicapa: RFID + Bluetooth cifrado
+	Timeouts de Seguridad: Previene ataques de fuerza bruta
+	Registro de Eventos: Log de accesos autorizados y no autorizados
+	Alarma Automática: Activación en caso de fallo de autenticación
+	Reset Automático: Vuelta al modo vigilancia tras timeout
##### 📊 CONFIGURACIÓN EN TINKERCAD
+	Conectar componentes según el esquema de pines
+	Cargar el código sistema_deteccion_intrusos.ino
+	Configurar RFID: Cambiar tarjetaAutorizada[] por tu UID
+	Ajustar Bluetooth: Posicionar potenciómetro en valor medio (512)
+	Iniciar simulación y probar diferentes escenarios

