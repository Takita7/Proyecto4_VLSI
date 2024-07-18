# Tarea-4_VLSI
En este proyecto, se desarrollará un contador arriba-abajo de 8 bits con carga paralela y reset asincrónico. El diseño estará optimizado para operar a una frecuencia mínima de 100 MHz, garantizando así un rendimiento eficiente y fiable. El contador permitirá tanto el incremento como el decremento de su valor en función de las señales de control, y podrá cargar valores específicos de manera paralela, lo que facilita su configuración en diversas aplicaciones digitales. Este proyecto combinará conceptos avanzados de diseño digital y técnicas de optimización para cumplir con los requisitos de velocidad y funcionalidad establecidos.

## Parte a
Se utilizarán las celdas DFRRHDLLX0 para los flip-flops y FAHDLLX0 para el sumador. Se medirán el retardo y el consumo de potencia del sumador a nivel esquemático, y se identificará la señal crítica de entrada del mismo. Además, se medirá la potencia de un registro completo (8 bits) con la señal de reloj conmutando a la máxima frecuencia.

El esquemático queda de la sigueinte manera:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/39014e33-8ef3-4c8e-8dfb-15c5abbc1565" width="500"/>
</p>

Simulación de prueba:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/d115e38a-4d4b-4eeb-8ced-79b6270088ef" width="500"/>
</p>

Seguidamente se pueden observar diferentes casos en los que el sumador tiene que cambiar su salidad, dada la conmutación de las entradas. Para este caso de estudio se emplea la entrada A, donde se observan diferentes tiempos. Primeramente, el caso donde A conmuta de O a 1 y la salida de 1 a 0, dando el peor caso con un delay de 432 ps, el segundo caso donde nuevamente A conmuta de 0 a 1 pero la salida de 0 a 1, dando el mejor caso con un delay de 244 ps y un caso intermedio, donde A conmuta de 1 a 0 y la salida de 1 a 0, con un delay de 387 ps. Estos tiempos resultan en la siguiente tabla: 

|Caso de estudio| Conmutación      | Tempo [ps]            |
|-|--------------------------------|-----------------------|
|Peor caso      | A 0->1 / Q 1->0                | 432   | 
|Mejor caso     | A 0->1 / Q 0->1                | 242   | 
|Caso intermedio| A 1->0 / Q 1->0                | 387   |

Aparte de estos, se mide el delay presente en el flip-flop con tal de encontrar el delay total desde la entrada del sumador a la salida del flip-flop.

Delay CLK-Q:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/3759907a-ec1c-4cf2-a3d0-fbca8f511cf6" width="500"/>
</p>

Delay del sumador entrada-salida:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/bc03ec9c-cd7e-4585-a953-a2399388b4dc" width="500"/>
</p>

Con respecto a la potencia, se hace estudio tanto del sumador con un registro individual, como de los 8 registros finales, dando conmo resultado las siguientes potencias:

Consumo de potencia del sumador:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/da9b0b91-3876-48bd-9f00-d2d97421cf35" width="500"/>
</p>

Potencia dinámica  del registro completo:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/4603a182-d1b1-40cb-9918-dde897472073" width="500"/>
</p>

## Parte b
Se diseñó y trazó un sumador sencillo de un bit utilizando la celda FAHDLLX0 y un registro sencillo de un bit con la celda DFRRHDLLX0, incluyendo las compuertas lógicas necesarias y asegurando el cumplimiento de DRC y LVS. Se midieron los retardos y el consumo de potencia de estas unidades sencillas de la misma manera que se describió anteriormente.

Post-trazado con parásitas:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/718459d8-3b17-4e69-8d55-6509ea068b83" width="600"/>
</p>

Simulación de prueba:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/f41653b2-36b7-4e97-8aa1-2f9dd524de60" width="500"/>
</p>

Delay CLK-Q post-trazo con parásitas:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/1d73a7c2-7758-4c3d-93af-f29609db01c2" width="500"/>
</p>

Delay del sumador entrada-salida post-trazo con parásitas:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/049a844d-ea0d-4181-b0ae-be307ecb5421" width="500"/>
</p>

Consumo de potencia del sumador:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/bffcda4b-dfc4-4e6b-82dc-302017f582d5" width="500"/>
</p>

Donde se puede notar un aumento de 21 uW con respeto a sumador a nivel de esquemático.

## Parte c 
### i. Modelo Verilog
Primeramente, se realizó un diseño en Verilog de un contador que cumpliera con las cuatro condiciones: carga paralela, cuenta ascendente, cuenta descendente y modo de espera (stand-by).
#### Entradas y salidas
```SystemVerilog
module Contador(
    input clk,
    input down_up,
    input DCon,
    input en,
    input D0,
    input D1,
    input D2,
    input D3,
    input D4,
    input D5,
    input D6,
    input D7,
    input reset,
    output reg Q0,
    output reg Q1,
    output reg Q2,
    output reg Q3,
    output reg Q4,
    output reg Q5,
    output reg Q6,
    output reg Q7,
    output reg TC
);
```
#### Criterio de diseño
```SystemVerilog
    function sum;
        input A;
        input B;
        input CI;
        begin
            sum = A ^ B ^ CI;
        end
    endfunction

    function carry;
        input A;
        input B;
        input CI;
        reg w1, w2, w3;
        begin
            w1 = B & CI;
            w2 = A & CI;
            w3 = A & B;
            carry = w1 | w2 | w3;
        end
    endfunction

    reg [7:0] suma = 0;
    reg [7:0] carry1 = 0;

      always @(posedge clk) begin
        if (!reset) begin
            Q0 <= 1'b0;
            Q1 <= 1'b0;
            Q2 <= 1'b0;
            Q3 <= 1'b0;
            Q4 <= 1'b0;
            Q5 <= 1'b0;
            Q6 <= 1'b0;
            Q7 <= 1'b0;
            suma <= 8'b00000000;
            carry1 <= 8'b00000000;
        end else if (DCon) begin
            suma[0] <= D0;
            suma[1] <= D1;
            suma[2] <= D2;
            suma[3] <= D3;
            suma[4] <= D4;
            suma[5] <= D5;
            suma[6] <= D6;
            suma[7] <= D7;
            Q0 <= D0;
            Q1 <= D1;
            Q2 <= D2;
            Q3 <= D3;
            Q4 <= D4;
            Q5 <= D5;
            Q6 <= D6;
            Q7 <= D7;
        end else if (!en) begin
            carry1[0] = carry(down_up, suma[0], !down_up);
            suma[0] = sum(down_up, suma[0], !down_up);
            carry1[1] = carry(down_up, suma[1], carry1[0]);
            suma[1] = sum(down_up, suma[1], carry1[0]);
            carry1[2] = carry(down_up, suma[2], carry1[1]);
            suma[2] = sum(down_up, suma[2], carry1[1]);
            carry1[3] = carry(down_up, suma[3], carry1[2]);
            suma[3] = sum(down_up, suma[3], carry1[2]);
            carry1[4] = carry(down_up, suma[4], carry1[3]);
            suma[4] = sum(down_up, suma[4], carry1[3]);
            carry1[5] = carry(down_up, suma[5], carry1[4]);
            suma[5] = sum(down_up, suma[5], carry1[4]);
            carry1[6] = carry(down_up, suma[6], carry1[5]);
            suma[6] = sum(down_up, suma[6], carry1[5]);
            carry1[7] = carry(down_up, suma[7], carry1[6]);
            suma[7] = sum(down_up, suma[7], carry1[6]);
            Q0 <= suma[0];
            Q1 <= suma[1];
            Q2 <= suma[2];
            Q3 <= suma[3];
            Q4 <= suma[4];
            Q5 <= suma[5];
            Q6 <= suma[6];
            Q7 <= suma[7];
            TC <= carry1[7] ^ down_up;
        end
    end
endmodule
```
Seguidamente, para verificar las cuatro condiciones, se creó el siguiente testbench:
#### Entradas y salidas
```SystemVerilog
module stimulus_contador(

    output reg clk,
    output reg reset,
    output reg down_up,
    output reg load,
    output reg en,
    output reg D0,
    output reg D1,
    output reg D2,
    output reg D3,
    output reg D4,
    output reg D5,
    output reg D6,
    output reg D7
);
```
#### Criterio de diseño
```SystemVerilog
    always #2 clk = ~clk;

    initial begin
        // Inicialización de señales
        en = 0;
        clk = 0;
        reset = 1;
        down_up = 0;
        load = 0;
        D0 = 1'b0;
        D1 = 1'b1;
        D2 = 1'b0;
        D3 = 1'b1;
        D4 = 1'b0;
        D5 = 1'b1;
        D6 = 1'b0;
        D7 = 1'b1;
        // Reset del contador
        reset = 0;
        #5;
        reset = 1;
        // Valor inicial 10
        #20;
        en = 1;
        #10;
        en = 0;
        #10;
        down_up = 1;
        #50;
        load = 1;
        #50;
        load = 0;
        #50; 
        // Fin de la simulación
        $finish;
    end
endmodule
```
Lo que da como resultado el siguiente esquemático y simulación:

<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/46fa72c2-c448-4421-b554-f19734b3296e" width="500"/>
</p>

<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/de1ed202-1f09-4a87-9e5f-1e3f378de7c8" width="500"/>
</p>

### ii. Esquemático correcto por construcción
Para crear el esquemático que cumpla con las cuatro condiciones, se editó el sumador de 1 bit con un flip-flop y se le incorporó un multiplexor que desempeña la función de carga paralela. Además, se añadieron 7 sumadores más, 7 flip-flops y 7 multiplexores para hacer un sumador de 8 bits. Para verificar si hay un overflow o un underflow, se realizó una operación XOR con el carry del último bit y la entrada de down_up. En la salida del TC, donde se detecta el overflow o el underflow, se agregó un flip-flop. Por otro lado, se añadió un multiplexor extra para controlar el reloj. Esto da como resultado el siguiente esquemático:

<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/858864a7-c31d-4870-aff5-02876fc9f58f" width="500"/>
</p>

Utilizando el esquemático anterior y el mismo testbench, la simulación resultante coincide con los resultados obtenidos del módulo Verilog:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/1dc91bf9-b350-4656-852a-64bc9cefd44a" width="500"/>
</p>
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/72e4ebc0-7286-449f-bd4f-11ae9caee78a" width="500"/>
</p>

### iii. Trazado completo del circuito
Se realizó el trazado completo del circuito utilizando la metodología de diseño con celdas estándar y la distribución de alimentación, donde el strip izquierdo corresponde a VDD, el derecho a GND, ambos en metal 3 y el superior corresponde a el CLK, el cual se generó en metal 4. Posteriormente, se verificó el cumplimiento de las reglas de diseño (DRC) y la verificación del esquema lógico contra el diseño (LVS). Luego, se extrajeron las capacitancias parásitas del circuito y se llevó a cabo una nueva simulación de funcionamiento (LPE) utilizando el mismo testbench que en las simulaciones anteriores. Finalmente, se compararon los resultados de esta simulación con los obtenidos previamentes.

 En la siguiente imagen se puede observar la celda completa:

<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110320407/9fc1e14a-728f-43e5-80e9-3b5aefe2c4e3" width="500"/>
</p>

En las siguientes dos imagenes se puede observar un acercamiento de la celta total, para generar un mayor detalle:

<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/ca9c6e70-63e5-44bb-9ca1-7af8de77b817" width="500"/>
</p>
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/f827551e-55a5-4a44-9bb9-68580679f5a9" width="500"/>
</p>

Como resultado, se obtuvo la siguiente simulación utilizando una frecuencia aproximada de 166 MHz, la cual da el mismo resultado que las simulaciones anteriores, donde se puede observar una cuenta ascendete hasta los 45 ns de simulación, donde se conmuta la señal down_up para empezar a generar una cuenta descendente. A los 25 ns conmuta la señal de Stand-by, por lo que el conteo se deteniene, esto se refleja en todas las salidas Q[7:0] y por último, a los 98 ns de simulación, se carga un valor, especificamente de 10101010, para posteriormente seguir contando de manera descendente. 
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/c2cc6e78-a7f6-41ae-adfb-5226ed43944b" width="500"/>
</p>

### iv Puntos extras.
Seguidamente se puede observar varias señales internas dentro del FF, la cual se puede observar que estas poseen ruido.

<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/03c68bae-f73a-4fb3-89a4-176dbf935d16" width="500"/>
</p>

Para mejorar la señal, se inserta una celda de capacitancia de desacople junto al flip-flop mencionado. Para el desacople capacitivo, se utiliza la celda DECAP3HDLL. El diseño del layout anterior, ya incluía espacios destinados para la inserción de estos capacitores, lo que facilitó la implementación del desacople capacitivo. Además, se utilizaron celdas de relleno FEED2HDLL para asegurar una distribución uniforme y optimizada del diseño.

Layout sin el desacople capacitivo:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/b4e9b186-f125-4982-9276-24827cd0c1fd" width="500"/>
</p>

Layout con el desacople capacitivo:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/00a6f02b-3d8c-4061-aca8-8fe4893f2a12" width="500"/>
</p>

Al analizar este circuito, se puede observar que las líneas de conexión no son lo suficientemente largas como para provocar una pérdida significativa de señal. Sin embargo, la inserción de un capacitor de desacople junto al flip-flop proporciona una mejora notable en la calidad de la señal. Antes de la inserción del capacitor, se observaron ciertos picos y fluctuaciones en la señal de alimentación, que aunque no eran críticos, podían afectar el rendimiento del circuito en condiciones específicas.

Con la adición del capacitor de desacople DECAP3HDLL, se aprecia que la señal de alimentación se vuelve más estable y uniforme. El capacitor ayuda a suavizar las variaciones de voltaje, eliminando los picos y mejorando la señal. 

Señal antes del capacitor:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/0cb3d166-5e6e-4f81-b4b3-c4646de07cb2" width="500"/>
</p>

Señal después del capacitor:
<p align="center">
    <img src="https://github.com/Rmarino25/Tarea-4_VLSI/assets/110353604/0b4dd9ba-0a6a-47bd-8494-96ac8f11b6eb" width="500"/>
</p>

Al analizar la señal antes de la inserción del capacitor de desacople, se observan picos de voltaje que alcanzan hasta 1.89 V y caídas hasta 1.74 V. Estas fluctuaciones pueden afectar el rendimiento del circuito, especialmente en aplicaciones de alta velocidad. Sin embargo, después de agregar el capacitor de desacople DECAP3HDLL, los picos de voltaje se reducen significativamente. El pico máximo observado después de la inserción del capacitor es de 1.805 V, y la caída máxima es de 1.792 V.



