<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

Este proyecto es un Gestor de Bypass Universal con Histéresis, un microchip diseñado para proteger la salud química y prolongar la vida útil de las baterías de litio en dispositivos portátiles (smartphones, laptops).En lugar de mantener una batería cargando constantemente al 100% (lo cual la degrada por calor y estrés por voltaje), este chip aísla la batería cuando alcanza un nivel óptimo y alimenta el dispositivo directamente desde el cargador (Bypass).La lógica interna utiliza una arquitectura mixta:Etapa Combinacional: Monitorea tres entradas: la presencia del cargador (IN0), el límite superior de la batería (IN1, $\ge$ 80%) y el límite inferior (IN2, $\le$ 75%).Etapa Secuencial (Memoria): Utiliza un Latch SR (Set-Reset) construido con compuertas NOR cruzadas. Esto introduce una histéresis en el sistema. Si la batería cae naturalmente del 80% al 79%, el Latch SR "recuerda" mantener el Bypass activo y se niega a inyectar carga hasta que la batería caiga por debajo del límite seguro del 75%. Esto evita oscilaciones rápidas y micro-ciclos de carga dañinos.

## How to test

Para probar este diseño en la placa física de demostración (Demoboard) o en la simulación, utiliza los interruptores de entrada y observa los LEDs de salida:Configuración de Pines:IN0 (Switch 1): Simula conectar el Cargador (1 = Conectado, 0 = Desconectado).IN1 (Switch 2): Sensor de límite superior (1 = Batería alcanzó el 80%).IN2 (Switch 3): Sensor de límite inferior (1 = Batería cayó al 75%).OUT0 (LED Verde): Comando de CARGA (Cierra el circuito hacia la batería).OUT1 (LED Azul): Comando de BYPASS (Aísla la batería, enruta la energía a la placa principal).Pasos para la prueba de histéresis:Encendido: Activa IN0 (Cargador ON). Activa IN2 (Bat $\le$ 75%). El OUT0 (Verde) debe encenderse.Subiendo: Apaga IN2 (Simulando 78%). El OUT0 (Verde) debe mantenerse encendido por la memoria.Meta (Bypass): Activa IN1 (Bat $\ge$ 80%). El OUT0 se apaga y el OUT1 (Azul) se enciende.Histéresis (El test clave): Apaga IN1 (Simulando que bajó al 79%). El OUT1 (Azul) debe permanecer encendido. El sistema se niega a cargar por un 1% de pérdida.Reinicio: Activa IN2 de nuevo. El ciclo se reinicia y vuelve a encender OUT0.

## External hardware

Dado que este chip ASIC es el "cerebro" lógico de baja potencia (opera a 3.3V y maneja corrientes lógicas), requiere de sensores externos para leer los datos y hardware de potencia para ejecutar las acciones. Para implementar este chip en un circuito real se requiere:

Módulo de Detección (Los Sentidos): Un microcontrolador básico (ej. ESP32 con Bluetooth) para leer el estado de la batería del dispositivo mediante software y enviar pulsos de 3.3V a los pines IN1 e IN2. Alternativamente, en sistemas puramente analógicos, se pueden usar Comparadores de Voltaje (ej. LM393) conectados a las celdas de litio.

Etapa de Potencia (Los Músculos): Transistores MOSFET de potencia (ej. MOSFET Canal P) o Relés de Estado Sólido (SSR) conectados a OUT0 y OUT1. Estos componentes actúan como válvulas eléctricas de alta capacidad que abren y cierran el paso de la corriente (5V a 20V) del puerto USB-C hacia el dispositivo móvil.
