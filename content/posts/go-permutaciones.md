---
title: "Como saber si dos strings son permutaciones con Go"
date: 2020-11-20T09:03:20-03:00
cover:
    image: "https://photos.collectednotes.com/photos/565/aec2ce81-452e-46f7-83ba-98518bcb6aa1"
    alt: "go lang"
    caption: "go lang"
---
#### Que es una permutacion
Para saber si dos strings son permutaciones, primero tenemos que saber que es una permutacion, son aquellas palabras que contienen exactamente los mismos caracteres, pero esta ordenados de una manera distinta, por ejemplo, una permutacion de AUTO podría ser ATOU, otra podría ser OAUT, etc

Sabiendo esto podemos empezar a escribir el programa en Go que nos diga si dos strings son permutaciones o no. Hay varias formas de hacerlo, algunas mas optimas que otras

#### Como puedo saber si son o no permutaciones
Unas de las maneras podría ser: Tengo dos strings, por ejemplo *"CASA"* y *"SAAC"* sabiendo esto lo que podría hacer seria ordenar alfabéticamente los dos strings y comparar si son iguales, si lo son, quiere decir que una es la permutacion de la otra, si no son iguales, es que no lo son.

Otra manera, mas optima, podría ser contando la cantidad de caracteres repetidos que tiene un string y restando los caracteres repetidos del otro, de esta manera, si la cantidad es igual a 0 quiere decir que los dos string tienen exactamente los mismos caracteres, por lo tanto son permutaciones.

Esta ultima forma es mas optima porque nos ahorramos el paso de ordenar ese string, por lo tanto si es muy largo, estamos ahorrando bastante memoria y tiempo de procesamiento.


#### El codigo
Una implementación de la ultima forma, podría ser algo así:

```go
package main

import (
	"fmt"
)

func main() {
	a := "casa"
	b := "csaa"
	
	fmt.Println(isPermutation(a,b))
}

func isPermutation(a,b string) bool{

	/* Si los string no son del mismo largo, no hay forma
	 * de que sean permutaciones por lo tanto nos podemos
	 * ahorrar el resto del proceso
	*/
	if len(a) != len(b){
		return false
	}

 	/* Seteamos un mapa que nos va permitir llevar
	 * la cuenta, ej: {c:1, a:1 ...}
	*/
	characterCount := make(map[string]int)
	
	/* Recorre el string "A" caracter por caracter y suma
	 * 1 por cara vez que aparece el mismo caracter
	*/
	for _, character := range a {
		characterCount[string(character)] += 1 
	}
	
	/* Esto hace lo contrario recorre el string "B"
	 * y resta 1 por cada vez que aparece el mismo caracter
	*/
	for _, character := range b {
		characterCount[string(character)] -= 1 
	}
	
	
	/* Recorre el mapa que se armo anteriormente y si alguno
	 * de los caracteres es distinto a 0 quiere decir que
	 * los string no son permutaciones
	*/
	for _, characterAppears := range characterCount {
		if characterAppears != 0 {
			return false
		}
	}
	
	return true
}
```

El código se puede probar en el [play ground de Go](https://play.golang.org/p/TPvWVxReVdi)