
# 📘 **Guía de Inicio a la Programación en Arduino**

## 1. Estructura básica de un programa (sketch)

Todo programa en Arduino tiene **dos partes obligatorias**:

```cpp
void setup() {
  // Código que se ejecuta una sola vez al inicio
}

void loop() {
  // Código que se repite continuamente
}
```

### 🔎 Ejemplo:

```cpp
void setup() {
  pinMode(13, OUTPUT); // Configura el pin 13 como salida
}

void loop() {
  digitalWrite(13, HIGH); // Enciende el LED
  delay(1000);            // Espera 1 segundo
  digitalWrite(13, LOW);  // Apaga el LED
  delay(1000);            // Espera 1 segundo
}
```

---

## 2. Variables

Una **variable** almacena información que puede cambiar durante la ejecución del programa.

### Tipos comunes:

| Tipo    | Ejemplo               | Significado       |
| ------- | --------------------- | ----------------- |
| `int`   | `int x = 10;`         | Número entero     |
| `float` | `float t = 2.5;`      | Número decimal    |
| `bool`  | `bool activo = true;` | Verdadero o falso |
| `char`  | `char letra = 'A';`   | Un carácter       |

---

## 3. Constantes

Una **constante** es como una variable, pero su valor **no cambia**.

```cpp
const int LED = 13; // Pin del LED (constante)
```

Usar constantes hace tu código más **claro y seguro**.

---

## 4. Funciones

Una **función** es un bloque de código reutilizable que realiza una tarea específica.

### Ejemplo:

```cpp
void parpadear(int pin, int tiempo) {
  digitalWrite(pin, HIGH);
  delay(tiempo);
  digitalWrite(pin, LOW);
  delay(tiempo);
}
```

Se puede usar así:

```cpp
parpadear(13, 500); // Parpadea el LED del pin 13 cada 500 ms
```

---

## 5. Condicionales

Permiten tomar **decisiones** según una condición.

```cpp
if (digitalRead(2) == LOW) {
  digitalWrite(13, HIGH); // Enciende el LED si el botón está presionado
}
```

También puedes usar `else`:

```cpp
if (x > 0) {
  // hacer algo
} else {
  // hacer otra cosa
}
```

---

## 6. Bucles

Se usan para repetir código.

### Ejemplo de `for`:

```cpp
for (int i = 0; i < 5; i++) {
  digitalWrite(13, HIGH);
  delay(500);
  digitalWrite(13, LOW);
  delay(500);
}
```

---

## 7. Comunicación serial

Permite enviar datos desde Arduino a tu PC para depurar:

```cpp
Serial.begin(9600);         // Se coloca en setup()
Serial.println("Hola");     // Muestra "Hola" en el Monitor Serial
```

---

## 8. Recomendaciones prácticas

1. **Usa nombres descriptivos**:

   ```cpp
   int temperaturaSensor = A0;
   ```

2. **Comenta tu código**:

   ```cpp
   // Esto enciende el LED
   digitalWrite(13, HIGH);
   ```

3. **Evita usar `delay()` para todo**:
   Usa `millis()` cuando necesites multitarea.

---

## 9. Primer proyecto sugerido: Encender LED con botón

```cpp
const int LED = 13;
const int BOTON = 2;

void setup() {
  pinMode(LED, OUTPUT);
  pinMode(BOTON, INPUT_PULLUP); // Botón conectado a GND
}

void loop() {
  if (digitalRead(BOTON) == LOW) {
    digitalWrite(LED, HIGH); // Enciende el LED
  } else {
    digitalWrite(LED, LOW);  // Apaga el LED
  }
}
```

---

## ¿Qué necesitas para empezar?

* Arduino Uno (u otra placa)
* Cable USB
* Arduino IDE (gratis)
* Componentes básicos: LED, resistencias, botones, protoboard

---
## Actividad.

Instalar IDE de arduino.
