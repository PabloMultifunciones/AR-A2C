# AR-A2C
Aprendizaje Reforzado - Método Ventaja Actor-Crítico (A2C)
### Introduccion ###

Método Ventaja Actor-Crítico (A2C), al que podemos llamar 'método híbrido': Actor-Crítico. El algoritmo actor-crítico es un agente de aprendizaje por refuerzo que combina enfoques de optimización de valor (value-based) y optimización de políticas (policy-based). Más específicamente, Actor-Critic combina los algoritmos Q-learning y Policy Gradient. El algoritmo resultante obtenido en el nivel alto implica un ciclo que comparte características entre.  

*Actor: un algoritmo Policy Gradient que decide sobre una acción a tomar.  
  
*Crítico: Algoritmo de Q-learning que critica la acción que el Actor seleccionó, brindando retroalimentación sobre cómo ajustar. Puede aprovechar los trucos de eficiencia en Q-learning, como la repetición de memoria.
  
<p align="center">
  <img src="https://user-images.githubusercontent.com/95035101/198690694-07cc7dbc-b18d-4a33-a238-29ea0891a7a6.png">
</p>

La ventaja del algoritmo Actor-Critic es que puede resolver una gama más amplia de problemas que DQN, mientras que tiene una menor variación en el rendimiento en relación con REINFORCE. Dicho esto, debido a la presencia del algoritmo PG dentro de él, Actor-Critic sigue siendo un poco ineficiente en el muestreo.

### El problema con los gradientes de política ###

Cuando derivamos gradientes de políticas e implementamos el algoritmo REINFORCE (también conocido como gradientes de políticas de Monte Carlo) existen algunos problemas con los gradientes de política: son gradientes ruidosos y con alta variación.

Recuerde la función de gradiente de política:

<p align="center">
  <img src="https://user-images.githubusercontent.com/95035101/198693155-18c7f78e-6f51-449d-8cb7-4f10f0ea1e0c.png">
</p>

El algoritmo REINFORCE actualiza el parámetro de política a través de actualizaciones de Monte Carlo (es decir, tomando muestras aleatorias). Esto introduce una alta variabilidad inherente en las probabilidades logarítmicas (logaritmo de la distribución de políticas) y los valores de recompensa acumulativos porque cada trayectoria de capacitación (episodio) puede desviarse en gran medida entre sí. En consecuencia, la alta variabilidad en las probabilidades logarítmicas y los valores de recompensa acumulativos generarán gradientes ruidosos y provocarán un aprendizaje inestable y/o una distribución de políticas sesgada hacia una dirección no óptima. Además de la alta variación de los gradientes, otro problema con los gradientes de políticas es que las trayectorias tienen una recompensa acumulativa de 0. La esencia del gradiente de políticas es aumentar las probabilidades de acciones "buenas" y disminuir las de acciones "malas" en la distribución de políticas; tanto las buenas como las malas acciones no se aprenderán si la recompensa acumulada es 0. En general, estos problemas contribuyen a la inestabilidad y la lenta convergencia de los métodos de gradiente de políticas estándar. Una forma de reducir la varianza y aumentar la estabilidad es restar la recompensa acumulada por una línea de base b(s):

<p align="center">
  <img src="https://user-images.githubusercontent.com/95035101/198694273-65c08dd3-2aba-49e9-85d8-d0aae4720238.svg">
</p>

Intuitivamente, hacer que la recompensa acumulada sea más pequeña al restarla con una línea de base generará gradientes más pequeños y, por lo tanto, actualizaciones menores y más estables.

### Cómo funciona el Actor-Crítico ###

Imagina que juegas un videojuego con un amigo que te proporciona algunos comentarios. Eres el Actor, y tu amigo es el Crítico:

<p align="center">
  <img src="https://user-images.githubusercontent.com/95035101/198694709-0e0e6c93-0934-48f4-9307-1873e6d79339.png">
</p>

Al principio, no sabes cómo jugar, así que intentas alguna acción al azar. El Crítico observa su acción y proporciona retroalimentación. Primero echemos un vistazo de nuevo al gradiente de política vainilla para ver cómo entra la arquitectura Actor-Crítico (y qué es):

<p align="center">
  <img src="https://user-images.githubusercontent.com/95035101/198694988-b2699976-9b67-411b-9a37-036d2756f138.svg">
</p>

Entonces podemos descomponer la expectativa en:

<p align="center">
  <img src="https://user-images.githubusercontent.com/95035101/198695058-3d52d4ba-8660-40cd-8226-e989c8d9ce5e.svg">
</p>

El segundo término de expectativa debería ser familiar; es el valor Q!

<p align="center">
  <img src="https://user-images.githubusercontent.com/95035101/198695140-3f2e599a-bc2c-450f-8dea-32c9bd84a509.svg">
</p>

Conectando eso, podemos reescribir la ecuación de actualización como tal:

<p align="center">
  <img src="https://user-images.githubusercontent.com/95035101/198695200-2c670930-732e-453a-9a96-ca4e33811e79.svg">
</p>

Como sabemos, el valor de Q se puede aprender parametrizando la función Q con una red neuronal. Esto nos lleva a los Métodos Actor-Crítico, donde:

*El "Crítico" estima la función de valor. Este podría ser el valor de acción (el valor Q) o el valor de estado (el valor V).  
*Crítico: Algoritmo de Q-learning que critica la acción que el Actor seleccionó, brindando retroalimentación sobre cómo ajustar. Puede aprovechar los trucos de eficiencia en Q-learning, como la repetición de memoria.  

Actualizamos tanto la red Critic como la red Value en cada paso de actualización.

Intuitivamente, esto significa que es mejor tomar una acción específica que la acción general promedio en el estado dado. Entonces, usando la función Valor como la función de línea de base, restamos el término del valor Q con el Valor. Llamaremos a este Valor el valor de la ventaja:

<p align="center">
  <img src="https://user-images.githubusercontent.com/95035101/198695508-95a1b944-2892-445d-9c6b-0fe46acc7cbe.svg">
</p>

Este es el llamado Actor-Crítico de Ventaja.
