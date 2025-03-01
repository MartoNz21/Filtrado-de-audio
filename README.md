## Laboratorio Problema del coctel | Filtrado de audio


## 1. Introducción

   
En este laboratorio se realizó el procesamiento de señales de audio capturadas por 2 micrófonos a mismas distancias para separar la fuente principal de voz de posibles interferencias utilizando análisis de espectro y separación ciega de fuentes (que se realiza gracias a transformadas para separar la voz principal del ruido ambiental y otras interferencias.) con Análisis de Componentes Independientes (ICA). Se analizó la relación señal-ruido (SNR), se aplicaron filtros con sus respectivas gráficas para el análisis de los resultados obtenidos.


## 2. Descripción del proceso inicial

   
Se tomaron diferentes señales de audio, 2 de las voces de los integrantes del equipo, cada una con un micrófono distinto, a unas distancias específicas de 1.32m de distancia entre el micrófono1 y la persona 1 y de 1.46m del mic 2 y la persona 2, a una frecuencia de muestreo de 44kHz, por aproximadamente 30seg con un nivel de cuantificación de aprox 32 bits, para lograr este tipo de situacion “fiesta de cóctel” además, un audio también para el ruido del ambiente para tener en cuenta en las interferencias a la hora de extraer las señales de preferencia.


Carga de archivos de audio y preprocesamiento:

Se cargan los archivos de audio en formato WAV, asegurando que sean monoaurales(se debe convertir el audio a mono, seleccionando solo un canal) y normalizados(se realiza para tener las grabaciones en la misma amplitud y puedan ser comparadas) antes del análisis:

![image](https://github.com/user-attachments/assets/16b7ea7a-39be-4d34-a848-17f2a6e6b572)


## 3. Análisis Espectral y Relación Señal-Ruido (SNR)
   
Cálculo de la SNR y Transformada Rápida de Fourier (FFT)
Para evaluar la calidad del audio, se calcula la potencia de la señal y su relación señal-ruido (SNR) en comparación con el ruido ambiente:


![image](https://github.com/user-attachments/assets/cdc058d5-afca-4cbe-ae0c-9e598fed421c)


Se cargaron los archivos de audio (excepto el de ruido), asegurándose de convertirlos a mono si son estéreo. Luego, los convierte a formato flotante para cálculos precisos. Se calcula la potencia de la señal mediante el promedio de los valores al cuadrado y se compara con la potencia del ruido de fondo. Si la potencia de la señal es mayor que la del ruido, se obtiene la SNR en decibeles usando logaritmos. Si no, la SNR se asigna como indefinida. Este análisis permite determinar la calidad del audio en relación con el ruido ambiente.

Se calcula la Transformada Rápida de Fourier (FFT) para obtener el espectro de las señales:

![image](https://github.com/user-attachments/assets/c1f00b93-5463-4fab-bb03-d7d72a6068cb)



Esta transformada se utiliza para analizar la frecuencia de una señal de audio, determina el número de muestras de la señal y aplica la FFT para obtener su representación en el dominio de la frecuencia(muestra cómo se distribuyen las diferentes frecuencias que componen la señal, se realiza con la FFT y la visualización del espectro de frecuencias) al tenerlo en el dominio del tiempo( representa cómo varía la señal de audio en función del tiempo,  está presente en la carga y preprocesamiento de la señal). Luego, calcula las frecuencias correspondientes usando `np.fft.fftfreq()`. Debido a que la FFT de señales reales es simétrica, solo se utiliza la mitad positiva del espectro de frecuencias y su magnitud. 


Gráfica del Espectro de Frecuencias
Para poder visualizar gráficamente esta información de la transformada rápida de fourier, se obtiene de:

![image](https://github.com/user-attachments/assets/4366d643-297b-4d9b-938e-33c1ea402c03)


![image](https://github.com/user-attachments/assets/6bdcac4b-cf63-4634-b719-e76afdb6ab01)


De esta gráfica se puede extraer que se observan picos en las frecuencias características de la voz humana (~300 Hz - 3400 Hz), el ruido afecta distintas frecuencias, alterando la SNR de cada grabación, la señal con mayor SNR tiene menos ruido, lo que indica mejor calidad de grabación.

## 4. Separación de Fuentes con ICA
  
La Separación Ciega de Fuentes (BSS - Blind Source Separation) es una técnica utilizada para extraer señales independientes de una mezcla sin conocer de antemano cómo se combinaron. En este caso, se utilizó Análisis de Componentes Independientes (ICA) para separar la voz predominante de otros sonidos en la grabación.
  Aplicación del modelo ICA
Se emplea Análisis de Componentes Independientes (ICA) para separar las señales en fuentes individuales:


![image](https://github.com/user-attachments/assets/9dc5066f-0b00-4536-b682-6dbb6606b2d6)

La relacion entre BSS e ICA es principalmente que BSS es un enfoque general para separar señales mezcladas sin conocer cómo se combinaron.
ICA es una técnica específica dentro de BSS que asume que las señales de origen son estadísticamente independientes.

Se normalizan las señales asegurando que tengan la misma longitud antes de convertirlas en una matriz para ICA, donde cada columna representa una señal independiente. Luego, se aplica FastICA con un número de componentes igual al número de grabaciones para extraer las señales independientes. Posteriormente, se implementa un filtro pasa-banda (300 Hz - 3400 Hz) para aislar las frecuencias características de la voz humana y eliminar ruido no deseado. Se calcula la energía de cada señal separada para identificar la más predominante y, mediante un umbral dinámico, se eliminan pequeños valores de amplitud que pueden representar ruido de fondo. Finalmente, se aplica un filtro paso-bajo para suavizar la señal resultante y mejorar su inteligibilidad. La efectividad del procesamiento se valida mediante gráficas del espectro de frecuencias y comparación visual de las señales antes y después del filtrado.

Luego, se aplicó un filtro pasa-banda (300 Hz - 3400 Hz) a cada señal separada:

![image](https://github.com/user-attachments/assets/d46b1d0e-4887-427c-bdb7-c56e6b1d7574)

Primero, se usa un filtro pasa-banda (300-3400 Hz) para eliminar ruidos fuera del rango del habla humana. Luego, se calcula la energía de cada señal separada y se selecciona la que tiene mayor energía, asumiendo que es la voz predominante. Posteriormente, se aplica un umbral dinámico para eliminar valores de baja amplitud, reduciendo ruido de fondo. Para suavizar la señal, se utiliza un filtro pasa-bajo (3800 Hz), reduciendo componentes de alta frecuencia no deseadas. Finalmente, se aplica un suavizado con media móvil con ventana de 5 muestras, lo que atenúa fluctuaciones bruscas y mejora la percepción auditiva de la señal. Estas técnicas en conjunto permiten extraer y mejorar la voz predominante de una mezcla de señales con ruido.


Gráfica de Separación de Fuentes se obtiene:

![image](https://github.com/user-attachments/assets/ae6d9ac5-ee5c-4630-86d6-0d4a21ec26ad)


![image](https://github.com/user-attachments/assets/2b5add98-f3ce-454d-8f72-0be77c2bcce2)


Se observa que la voz predominante ha sido extraída correctamente, se han reducido interferencias y ruidos presentes en la señal original y el ICA permite mejorar la visualización de la señal sin necesidad de conocer cómo fueron mezcladas antes.

## 5. Conclusiones

En este laboratorio, logramos separar la voz principal de otros sonidos usando técnicas avanzadas como ICA y filtros específicos. Esto permitió reducir el ruido y mejorar la calidad del audio sin alterar la información importante. Además, al analizar las señales con herramientas como la FFT y la SNR, pudimos confirmar que el procesamiento fue efectivo. Este tipo de técnicas es muy útil en situaciones donde se necesita obtener una voz clara, como en sistemas de reconocimiento de voz o comunicaciones en ambientes ruidosos.
