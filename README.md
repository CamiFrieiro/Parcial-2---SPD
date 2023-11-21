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
