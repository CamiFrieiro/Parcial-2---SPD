Alumno
----
CAMILA FRIEIRO - 1B

Sistema de Incendios con Arduino
----
[![](https://i.ibb.co/Z8qyWHd/incendios.png)](https://github.com/CamiFrieiro/Parcial-2---SPD/blob/main/Sistema%20de%20incedios/incendios.png)

Descripcion
----
Es un sistema de incendios en arduino a control remoto. Este podra detectar la temperatura y en caso de la detencion de algun incendio se activara un servo motor.
Ademas de mostrar la actual temperatura y la estacion que le corresponde.
Cuenta con un:
+ Arduino uno
+ Un display LCD (16x2)
+ Servo motor
+ Control remoto IR
+ Sensor de temperatura
+ Dos leds
Ademas obviamente de un protobard y todos los cables u objetos necesarios (un potenciometro y un sensor IR) para lograr su funcionamiento.


Conexiones
----
**Control Remoto IR**

Para conectar el control remoto al proyecto se lo conecto mediante a un sensor IR, este esta conectado al pin 11 de arduino y a sus usuales conexiones GND y 5V.
Ademas (en el codigo) se hace uso de la libreria **IRremote.hpp**.

**Display LCD**

En este caso utilize un display LCD 16x2.
Sus conexiones:
+ VVC y GND: se conecta a 5V y GND respectivamente.
+ RW: tambien debe ser conectado a GND.
+ V0: primero se lo conecto a un potenciomentro y es el encargado del contraste del display
+ RW: tambien debe ser conectado a GND.
+ E: este habilita la lectura en la pantalla y fue conectado al pin 6.
+ Pines D4 a D7: estos son para enviar datos a la pantalla y fueron conectados a los pines 5 a 2 de arduino.
Ademas (en el codigo) se hace uso de la libreria **LiquidCrystal.h**.

**Servo motor**
Este fue conectado al pin 9 de arduino ademas de 5V y GND.
Ademas (en el codigo) se hace uso de la libreria **Servo.h**.

**Sensor de temperatura**
Este fue conectado al pin A0 de arduino ademas de 5V y GND.

**LEDS**
Dos leds:
+ Led verde: conectado a GND y al pin 8 a travez de una resistencia.
+ Led rojo: conectado a GND y al pin A2 a travez de una resistencia.

Funcionamiento
----
La idea es que el display pueda detectar y mostrar la temperaruta y determinar a que estacion del año pertenece dicha temperatura.
El sistema de incendios se debera poder activar o desactivar con el control remoto:
+ Si se apreta "1": se activara el sistema de incendios
+ Si se apreta "2": se desactivara el sistema de incendios
Al activarse el sistema este debera notificar si este registra un incendio si la temperatura detectada es mayor a 60° mostrando el mensaje "INCENDIO" en el display y tambien activara el servo motor para simular una respuesta al incendio.

En cuanto a las leds:
+ La led verde se prendera si se activa el sistema de incendio y se apagara si este se desactiva.
+ La led roja comenzara a titilar si se detecta un incendio.

Funciones
----
**parpadearLuzRoja**

```cpp
void parpadearLuzRoja() {
  for (int i = 0; i < 10; i++) {
    digitalWrite(A2, HIGH);
    delay(100);
    digitalWrite(A2, LOW);
    delay(100);
  }
}
```

En esta funcion se usa un for para realizar una misma accion una cierta cantidad de veces, aca apagamos y prendemos el led continuamente para que parpadee.

**manejarControlRemoto**

```cpp
void manejarControlRemoto() {
  if (IrReceiver.decode()) {
    switch (IrReceiver.decodedIRData.command) {
      case 0x10: // BOTON 1
        digitalWrite(LED_VERDE, HIGH);
        activarSistemaIncendio();
        break;
      case 0x11: // BOTON 2
        digitalWrite(LED_VERDE, LOW);
        desactivarSistemaIncendio();
        break;
    }
    delay(65);
    IrReceiver.resume();
  }
}
```

Esta funcion es para manejar el control remoto, se usa un if para verifica si se recibe o no una señal IR del control. En caso de que asi sea hay un switch con dos opciones: el boton 1 y el boton 2
Si se aprieta el boton 1: Se activa el sistema de incendios y se prende la led verde (llamando a la funcion: activarSistemaIncendio )
Si se aprieta el boton 2: Se desactiva el sistema de incendios y se apaga la led verde (llamando a la funcion: desactivarSistemaIncendio )
Y ek **IrReceiver.resume()** que se utiliza para reinciar el IR para luego poder recibir mas señales.


**Activar y desactivar el sistema**

```cpp
void activarSistemaIncendio() {
  SistemaIncendio = true;
}

void desactivarSistemaIncendio() {
  servoIncendio.write(90);
  SistemaIncendio = false;
}
```

En estas dos funciones se establece la condicion de que el sistema de incendio sea "True" en caso de activacion y "False" en caso de que este desactivado.

**sistemaDeIncendio**

```cpp
void sistemaDeIncendio(float temperatura) {
  if (SistemaIncendio && temperatura > TEMPERATURA_INCENDIO) {
    parpadearLuzRoja();
    for (int servo_movimiento = 0; servo_movimiento <= 180; servo_movimiento++) {
      servoIncendio.write(servo_movimiento);
      delay(15);
    }
    for (int servo_movimiento = 180; servo_movimiento >= 0; servo_movimiento--) {
      servoIncendio.write(servo_movimiento);
      delay(15);
    }
  } else {
    servoIncendio.write(90);
    digitalWrite(A2, LOW); // Apagar la luz en A2
  }
}
```

Primero verifica si esta sistema de incendio esta activado y si la temperatura se encuentra a mayor cantidad que la temperatura establecida en caso de incendio (mayor a 60)
Si ambas se cumplen entonces: la led roja empezara a titilar (llamando a la funcion anterior) y tambien empezara a mover el servo desde 0 grados a 180 con un for para que la accion siga repitiendose a su vez hace la misma accion de 180 grados a 0 tambien con un for.

**Obtener temperatura y asignar estacion**

```cpp
float obtenerTemperatura() {
  int valorSensor = analogRead(SENSOR_TEMPERATURA);
  float voltaje = valorSensor * (5.0 / 1023.0);
  float temperaturaCelsius = (voltaje - 0.5) * 100.0;
  return temperaturaCelsius;
}

String asignarEstacion(float temperatura) {
  if (temperatura >= 25) {
    return "Verano";
  } else if (temperatura >= 15 && temperatura < 25) {
    return "Primavera";
  } else if (temperatura >= 5 && temperatura < 15) {
    return "Otoño";
  } else {
    return "Invierno";
  }
}
```

Primero se debe obtener la temperatura a travez del sensor.
Se usa un float en lugar de void ya que va a retornar un valor de temperatura.
Se calcula el rango de voltage (5V = 0 a 5) dividido el valor minimo y maximo obtenido por el sensor (0 y 1073) y ese valor se calcula con la funcion (voltaje - 0.5) * 100.0 para poder obtener temperatura en Celsius

En la funcion donde se asigna la estacion:
Se usa string en vez de void porque lo retorna sera un string (la estacion correspondiente)
Esta toma la temperatura encontrada anteriormente y depende las condiciones determinara en que temperatura se encuentra.

**Obtener temperatura y asignar estacion**

```cpp
void mostrarLCD(float temperatura, String estacion, bool incendioDetectado) {
  static float prevTemp = 0;
  static String prevEstacion = "";
  static bool  prevIncendioDetectado  = false;

  if (prevTemp != temperatura || prevEstacion != estacion ||  prevIncendioDetectado  != incendioDetectado) {
    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Temp: " + String(temperatura) + " C");

    lcd.setCursor(0, 1);
    if (SistemaIncendio && temperatura > TEMPERATURA_INCENDIO) {
      lcd.print("INCENDIO!!");
    } else {
      lcd.print("Estacion: " + estacion);
    }
    
    prevTemp = temperatura;
    prevEstacion = estacion;
    prevIncendioDetectado = incendioDetectado;
  }
  delay(50); 
}
```

Por ultimo, tenemos la funcion relacionada al display.
Tenemos variables (prevTemp, prevS) que van guardad ciertos datos anteriores para poder hacer una comparacion con valores actuales.

Se comparan los valores y si alguno llegara a ser diferente se actualiza la pantalla del display.

Se encargara de mostrar la temperatura, la estacion y un mensaje de incendio en caso de que uno haya sido detectado (y el sistema este activado)


Link de Tinkercad
----
[Sistema de Incendios](https://www.tinkercad.com/things/fV8NVgeoQqW-parcial-2-2/editel?sharecode=K3ZelPEwW3rpFS9_zvWi6ii_tFCEIUv7jpBYb635-ag "Sistema de Incendios")

Link de Tinkercad
----
[Sistema de Incendios](https://onlinegdb.com/l8IqAb94t "Sistema de Incendios")




