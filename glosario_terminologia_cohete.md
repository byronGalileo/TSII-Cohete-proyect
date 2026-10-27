# Glosario y Referencias Terminológicas: Proyecto Cohete (Teoría de Sistemas 2)

Este documento recopila las definiciones conceptuales, físicas y de control fundamentales para el análisis, modelado y presentación del sistema de control del cohete en el plano de cabeceo.

---

## 1. Conceptos Físicos y Aeroespaciales

### Cabeceo (*Pitch*)
* **Definición:** Movimiento de rotación angular del cohete alrededor de su eje transversal (perpendicular al fuselaje).
* **Representación en el modelo:** Ángulo $\theta(t)$, medido entre el eje longitudinal del vehículo y la vertical de referencia inercial.
* **Importancia práctica:** Determina la dirección hacia la que apunta la nariz del cohete durante el ascenso.

### Eje Axial
* **Definición:** Línea imaginaria longitudinal que recorre el vehículo desde el motor (sección de cola) hasta el cono de la nariz.
* **Aceleración axial ($a(t)$):** Componente neta de la aceleración a lo largo de este eje principal, generada predominantemente por la fuerza de empuje de los motores dividida por la masa del vehículo.

### Tobera (*Nozzle*)
* **Definición:** Conducto y tobera propulsiva ubicada en la parte posterior del cohete a través de la cual los gases de combustión se aceleran y expulsan a alta velocidad.
* **Empuje orientable (*Gimbaled Thrust / TVC*):** En este modelo, la tobera está montada sobre un cardán articulado accionado por un servomecanismo, permitiendo variar el vector de empuje para controlar la orientación del vehículo.

### Deflexión de la Tobera ($\delta$)
* **Definición:** Desviación angular de la tobera con respecto a la línea central o eje axial del cohete.
* **Efecto de control:** Al inclinarse un ángulo $\delta$, el vector de empuje genera un brazo de palanca respecto al centro de masa (CM), produciendo un momento de control corrector ($k_\delta \delta$) para gobernar el cabeceo.

### Actitud (*Attitude*)
* **Definición:** Orientación espacial del cohete en el espacio tridimensional respecto a un marco de referencia inercial especificado.
* **Contexto en el proyecto:** Al restringirse el análisis al plano de cabeceo (2D), la actitud está unívocamente determinada por el ángulo de cabeceo $\theta(t)$ y su velocidad angular $\omega(t)$. Mantener la actitud correcta es crítico para evitar desviaciones en la trayectoria orbital y prevenir sobrecargas aerodinámicas estructurales.

---

### Momento de Inercia ($I$)
* **Definición:** Medida de la oposición o resistencia rotacional que presenta un cuerpo rígido a cambiar su velocidad angular cuando se le aplica un torque.
* **Contexto en el cohete:** En el plano de cabeceo se analiza la inercia transversal ($I_{yy}$). En las hipótesis del proyecto se asume constante en la ventana de tiempo analizada, lo que permite linealizar la dinámica de rotación alrededor del centro de masa.

### Presión Dinámica ($q$)
* **Definición:** Medida de la energía cinética por unidad de volumen del fluido atmosférico en movimiento relativo contra el cohete ($q = \frac{1}{2} \rho v^2$, donde $\rho$ es la densidad del aire y $v$ la velocidad de vuelo).
* **Contexto en el cohete:** Determina la magnitud directa de las fuerzas y momentos aerodinámicos (como la rigidez desestabilizante $\mu$ y el amortiguamiento $a_d$). En el modelo se asume constante en la ventana temporal considerada para mantener coeficientes lineales e invariantes en el tiempo (LTI).

### Flexibilidad Estructural (*Body Bending*)
* **Definición:** Deformación elástica que sufre el fuselaje del cohete (comportándose como una viga delgada y larga) debido a las elevadas fuerzas axiales, cargas aerodinámicas y vibraciones durante el vuelo.
* **Contexto en el cohete y control:** Al vibrar y flexionarse el fuselaje, los sensores inerciales (giróscopos y acelerómetros) no solo miden el movimiento del cuerpo rígido, sino también la flexión local. Si estas oscilaciones elásticas caen dentro del ancho de banda del lazo de control, pueden provocar inestabilidad aeroelástica (*control-structure interaction*).
* **Hipótesis en el proyecto:** La hipótesis 6 la desprecia, asumiendo que el cohete se comporta como un cuerpo rígido perfecto.

### Sloshing del Propelente (*Propellant Sloshing*)
* **Definición:** Movimiento oscilatorio de vaivén o chapoteo de las grandes masas de combustible y oxidante líquido dentro de los tanques parcialmente vacíos.
* **Contexto en el cohete y control:** Como el combustible líquido representa una fracción muy alta de la masa total del cohete (hasta el 80-90%), su oscilación desplaza el centro de masa (CM) y genera fuerzas y torques laterales oscilatorios que perturban la actitud del vehículo.
* **Hipótesis en el proyecto:** La hipótesis 6 lo desprecia, considerando la masa de combustible rígidamente fijada al fuselaje sin modos dinámicos propios de oleaje.

## 2. Conceptos de Teoría de Control y Sistemas Dinámicos

### Ganancia ($K$)
* **Definición general:** Factor multiplicativo o factor de escala entre una señal de entrada y una señal de salida en un bloque de control ($\text{Salida} = K \cdot \text{Entrada}$).
* **Aplicación en el lazo de control:** Determina la agresividad o intensidad con la que los actuadores responden ante el error entre la consigna deseada y el estado medido.

#### Ganancia del Canal de Empuje ($K_1$)
* **Función:** Controla el canal de aceleración axial.
* **Efecto dinámico:** Ajusta el seguimiento del comando de aceleración $u_1(t)$. Al incrementar $K_1$, se acelera la respuesta del motor ante comandos y perturbaciones axiales.

#### Ganancia del Canal de Dirección ($K_2$)
* **Función:** Controla la deflexión de la tobera mediante el lazo del servomecanismo.
* **Efecto dinámico:** Proporciona estabilización activa frente a la inestabilidad aerodinámica inherente del cohete (cuando el centro de presión está adelante del centro de masa, $\mu > 0$). Un valor mínimo de $K_2$ es indispensable para garantizar que todos los polos del sistema tengan parte real negativa.

### Acoplamiento Cruzado (*Cross-Coupling*)
* **Definición general:** Fenómeno inherente a los sistemas multivariables (MIMO) en el cual una acción de control o perturbación en un canal afecta de manera directa la dinámica de otro canal.
* **Manifestación en el cohete:**
  1. **Dirección $\rightarrow$ Aceleración:** Corregir el cabeceo desviando la tobera y basculando el cohete reduce la proyección del empuje en el eje axial y genera pérdidas efectivas por gravedad ($-c_\theta \theta$).
  2. **Empuje $\rightarrow$ Dirección:** Incrementar el empuje genera un momento parásito ($k_\epsilon a$) si existe una leve desalineación mecánica entre la línea de acción del motor y el centro de masa, perturbando el cabeceo.

---

## 3. Resumen de Variables y Parámetros del Modelo

| Símbolo | Nombre | Unidades | Rol Físico / Control |
| :--- | :--- | :--- | :--- |
| $u_1(t)$ | Comando de aceleración | $\text{m/s}^2$ | Entrada: aceleración axial requerida |
| $u_2(t)$ | Comando de cabeceo | $\text{rad}$ | Entrada: actitud de cabeceo requerida |
| $y_1(t) = a(t)$ | Aceleración axial | $\text{m/s}^2$ | Salida medida (acelerómetro) |
| $y_2(t) = \theta(t)$ | Ángulo de cabeceo | $\text{rad}$ | Salida medida (IMU) |
| $\omega(t)$ | Velocidad angular de cabeceo | $\text{rad/s}$ | Estado interno (derivada cinemática $\dot{\theta}$) |
| $\delta(t)$ | Deflexión de tobera | $\text{rad}$ | Estado interno del servo TVC |
| $K_1$ | Ganancia canal empuje | Adimensional | Parámetro de diseño de la computadora de a bordo |
| $K_2$ | Ganancia canal dirección | Adimensional | Parámetro de diseño de la computadora de a bordo |
| $\mu$ | Rigidez aerodinámica | $\text{s}^{-2}$ | Momento desestabilizante (CP delante de CM) |
| $a_d$ | Amortiguamiento aerodinámico | $\text{s}^{-1}$ | Disipación pasiva de energía oscilatoria |
| $k_\delta$ | Efectividad de la tobera | $\text{s}^{-2}/\text{rad}$ | Capacidad de control angular por deflexión |
| $k_\epsilon$ | Acoplamiento por desalineación | $\text{rad}/\text{m}$ | Torque parásito por aceleración |
| $c_\theta$ | Acoplamiento gravitatorio | $(\text{m/s}^2)/\text{rad}$ | Pérdida de aceleración axial al inclinarse |
| $\tau_m$ | Constante de tiempo del motor | $\text{s}$ | Retardo dinámico de primer orden en empuje |
| $\tau_s$ | Constante de tiempo del servo | $\text{s}$ | Retardo de actuación del mecanismo TVC |
| $\lambda$ | Razón velocidad/posición IMU | $\text{s}$ | Aporte de amortiguamiento artificial derivativo |
