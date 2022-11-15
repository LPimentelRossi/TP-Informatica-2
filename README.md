## TP-Informatica-2


- Control de Temperatura de cámara frigorífica.

## Memoria descriptiva
Mi sistema representa un control de temperatura de cámara frigorífica, con temperatura mínima, temperatura máxima, tiempo de descongelamiento, y alarmas de sobre temperatura.
Cuenta con un sensor de temperatura, un sistema de refrigeración, un sistema de alarmas, un temporizador y una resistencia para el estado de descongelamiento.

### Estados:

- Enfriamiento: El sistema refrigerante se accionará mientras que la temperatura sea mayor a mínima y no se encuentre en estado de descongelamiento.

- Espera: El sistema refrigerante se mantendrá en stanby mientras que la temperatura sea menor a la máxima y no se encuentre en estado de descongelamiento.

- Descongelamiento: El sistema refrigerante se mantendrá en stanby y se pondrá en funcionamiento la resistencia de descongelamiento mientras que se encuentre en tiempo de descongelamiento y el sistema no se encuentre en estado de alarma.

- Alarma: El sistema accionará las alarmas si la temperatura es mayor a la máxima.


### Máquina de estado de Control de temperatura

 ![./TP-Informatica-2/Recursos/Máquina de Estado.png](https://github.com/LPimentelRossi/TP-Informatica-2/blob/d87277e99f6d8d9344e7de888d07e2eefa4ff1b7/Recursos/M%C3%A1quina%20de%20Estado.png)

 - __SET__: Bandera de fin de inicialización
 - __T__: temperatura leida
 - __T_máx__: Temperatura máxima configurada
 - __T_mín__: Temperatura mínima configurada
 - __t__: tiempo leído
 - __t_incio_descong__: tiempo de inicio de descongelamiento
 - __t_fin_descong__: tiempo de finalización de descongelamiento

## Código

- Archivo de cabecera __mylib.h__

```c
#ifnfef MY_LIB
#define MY_LIB
#include <stdio.h>

typedef enum {
espera = 0,
enfriamiento = 1,
descongelamiento = 2,
alarma = 3
}estados_t;

typedef enum {
off = 0,
on = 1
}offon_t;

typedef sruct {
char temp; // temperatura medida
char temp_min; // temperatura minima
char temp_max; // temperatura maxima
}temperatura_t;

typedef struct {
char t; // tiempo medido
char t_inicio_descong; // tiempo de inicio descongelamiento
char t_fin_descong; // tiempo fin descongelamiento
}tiempo_t

temperatura_t f_inicio(void);
tiempo_t f_inicio(void);
estados_t f_espera(temperatura_t, tiempo_t);
estados_t f_enfriamiento(temperatura_t, tiempo_t);
estados_t f_descongelamiento(temperatura_t, tiempo_t);
estados_t f_alarma(temperatura_t);

#endif
```

- Archivo __main.c__
> con Switch case

```c
#include "mylib.h"

int main() {
	temperatura_t config;
	tiempo_t config;
	estados_t estado = espera;

	config = inicio();
	while(1){
		switch (estado) {
			case espera: estado = f_espera(config);
				break;
			case enfriamiento: estado = f_enfriamiento(config);
				break;
			case descongelamiento: estado = f_descongelamiento(config);
				break;
			case alarma: estado = f_alarma(config);
				break;
				}
		}
	return 0;
}
```
 > Con Punteros a funciones

 ```c
#include "mylib.h"

int main() {
	temperatura_t config;
	tiempo_t config;
	estados_t estado = espera;
	estados_t (*fsm[])(temperatura_t)(tiempo_t) = {f_espera, f_enfriamiento, f_descongelamiento, f_alarma}
	config = inicio();
	while(1) estado = (*fsm[estado])(config);
	return 0;
}
 ```
- Archivo __config.conf__

```bash
# Temperatura minima
temp_min -20
# Temperatura maxima
temp_max 0
# tiempo de inicio descongelamiento en min
t_inicio_descong 240
# tiempo fin descongelamiento en min
t_fin_descong 30
```
- Funciones de Estado

```c
estados_t (f_espera(temperatura_t, tiempo_t)) {
	temperatura_t;
	tiempo_t;
	offon_t;

	if(estados!=descongelamiento && tem<tem_max){
		frio = off;
		descong = off;
		alarma = off;
		estados = espera;
	}
	else{
		estados = enfriamiento
	}
return estados;
}


estados_t (f_enfriamiento(temperatura_t, tiempo_t)) {
	temperatura_t;
	tiempo_t;
	offon_t;

	if(estados!=descongelamiento && tem>tem_min){
		frio = on;
		descong = off;
		alarma = off;
		estados = enfriamiento;
	}
	else{
		estados = descongelamiento
	}
return estados;
}


estados_t (f_descongelamiento(temperatura_t, tiempo_t)) {
	temperatura_t;
	tiempo_t;
	offon_t;

	if(estados!=alarma && t_inicio_descong>t && t>t_fin_descong){
		frio = off;
		descong = on;
		alarma = off;
		estados = descongelamiento;
	}
	else{
		estados = alarma
	}
return estados;
}


estados_t (f_alarma(temperatura_t, tiempo_t)) {
	temperatura_t;
	tiempo_t;
	offon_t;

	if(tem>tem_max){
		frio = on;
		descong = off;
		alarma = on;
		estados = alarma;
	}
	else{
		estados = espera
	}
return estados;
}
```
