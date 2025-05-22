# Documentación del Sistema de Control de Iluminación

## 1. Descripción General

Este sistema embebido implementa un control de iluminación con funcionalidad basada en eventos, usando una placa STM32 y periféricos como GPIO, UART, temporizadores (TIM3), PWM y SysTick. El sistema responde a eventos sincrónicos (bucle principal) y asíncronos (interrupciones por botón y UART), permitiendo un control flexible del brillo de un LED.

---

## 2. Flujo de Ejecución

### 2.1 Inicialización del Sistema

Al arrancar, el sistema realiza la configuración de los siguientes módulos:

- **GPIO**: Configuración de pines de entrada/salida.
- **UART**: Comunicación serie para interacción externa.
- **TIM3 PWM**: Generación de señal PWM para el control de brillo.
- **SysTick**: Temporizador para retardos precisos.

## 2.2 Bucle Principal

El bucle principal ejecuta continuamente las siguientes tareas:

1. **Alternar el LED de latido (heartbeat)** – Indica que el sistema está activo.
2. **Procesamiento del control de habitación** – Lógica de control basada en estados internos.
3. **Retardo usando SysTick** – Proporciona temporización sin bloqueo.
4. **Actualización del ciclo de trabajo PWM** – Ajuste dinámico del brillo del LED.
5. **Retardo adicional del bucle de luz** – Controla la frecuencia de actualización.
6. **Ajuste del ciclo de trabajo** – Modifica el valor PWM según lógica interna.

## 3. Eventos Asíncronos

El sistema responde a interrupciones externas para mejorar la interacción sin afectar el bucle principal.

---

### 3.1 Interrupción por Botón (EXTI15_10 IRQ)

Cuando se detecta una pulsación en el botón B1:

1. Se activa la salida PA7 (por ejemplo, para encender una luz).
2. Se inicia un temporizador de 3 segundos.
3. Se informa por UART que el botón fue presionado.
4. Al cumplirse los 3 segundos, PA7 se apaga automáticamente y se informa por UART.

### 3.2 Interrupción por UART (USART2 IRQ)

Cuando se recibe un carácter por UART:

- Si el carácter recibido es `H` o `h`, el ciclo de trabajo del PWM se establece al 100%.
- Si el carácter recibido es `L` o `l`, el ciclo de trabajo del PWM se reduce al 0%.
- En ambos casos, el sistema responde mediante UART con un mensaje que indica el nuevo valor del ciclo de trabajo PWM.

## 4. Conclusión

Este sistema demuestra una arquitectura eficiente basada en eventos para controlar la iluminación mediante una señal PWM, utilizando tanto entradas físicas (botón) como entradas digitales (comandos UART).

Gracias a la implementación de interrupciones y temporizadores, el sistema logra una operación no bloqueante, permitiendo respuestas rápidas a eventos externos y manteniendo un control fluido y preciso sobre el brillo del LED.

Este enfoque lo hace ideal para aplicaciones embebidas de bajo consumo y alta reactividad, como sistemas domóticos, iluminación inteligente o dispositivos de interfaz humano-máquina (HMI) en tiempo real.
