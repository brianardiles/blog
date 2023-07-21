---
title: "Goroutines y channels en Go"
date: 2020-11-20T09:03:20-03:00
cover:
    image: "https://i.imgur.com/x8IZ7Rm.png"
    alt: "go lang"
    caption: "go lang"
---

#### Que es una goroutine?
Una goroutine es como un hilo, pero manejado por Go. Es mas liviano que un hilo tradicional y al estar completamente integrado al lenguaje, es mas sencillo de implementar.

#### Que beneficios tienen?
Las principales ventajas que tienen estas goroutines es que están muy bien optimizadas en la gestión de recursos, por lo tanto es posible tener cientos o hasta miles de goroutines a un costo mucho menor que si fueran hilos tradicionales. 

Otro de los beneficios es que al estar integradas al lenguaje su implementacion es mas sencilla y nos abstrae de lo que seria el manejo de esos "hilos". Esto nos permite realizar varias operaciones en "simultaneo" (no es realmente simultaneo, porque son concurrentes y no paralelas) con menos codigo.

#### Como ejecutar una goroutine

La forma de iniciar una go routine es llamando una funcion y agregando la keyword ```go``` delante.

Ejemplo:

Si tenemos la función ```getName()``` la manera de iniciarla como una gorutine seria: ```go getName()```

```go
package main

import (
	"fmt"
)

func getName(){
	fmt.Println("Brian")
}

func main() {
	fmt.Println("Hola, mi nombre es: ")
	go getName()
}
```

[Probar codigo](https://play.golang.org/p/RkkUF6K6Als)

####Que paso
Si probaron el código se dieron cuenta de que, lo único que se imprimió fue *Hola, mi nombre es:* y no se imprimió *Brian* que fue el print que se ejecuto en la goroutine. Esto se debe a que la función ```main()``` también es una goroutine y cuando una goroutine termina, todas las *sub-goroutines* que se estén ejecutando mueren al morir la principal.

Por lo tanto, ```main()``` se ejecuto,  esta llamo a la función ``getName()`` como una goroutine, pero al finalizar ```main()``` antes que ``getName()``, el print  de "Brian" nunca se ejecuto.

Una manera de comprobar esto que dije, seria agregando un sleep en la funcion ``main()`` abajo del ``go getName()`` para que se quede esperando ese sleep de por ejemplo 10 segundos, y efectivamente ver que la funcion ``getName()`` se esta ejecutando. Obviamente esta "solucion" es solo para probar, no serviria en un entorno productivo porque no podemos saber de antemano cuanto llevaria la ejecucion, por lo tanto una mejor manera seria que la goroutine nos avise cuando termino de ejecutarse, para ahi si, pasar a cerrar la goroutine principal.

##### Entran a jugar los channels

Para que esto funcione, hay que sincronizar las rutinas y para lograrlo se tienen que usar los channels (existen otras maneras, pero en este caso vamos a ver channels).
Los channels vendrian a ser como las tuberías que conectan las goroutines y permiten transmitir datos de un lado al otro

Para crear un channel hay que usar la función ```make()``` de la siguiente manera: ```make(chan type)``` donde ``chan`` vendria a ser la palabra reservada para los channels y ``type`` vendria a ser el tipo de dato que va a traficar ese channel

En este caso, lo que vamos a hacer es crear un channel de tipo ``bool`` el cual se va a quedar esperando a que la goroutine secundaria termine y al terminar la goroutine va a enviar un ``true`` a traves de ese channel indicando que termino.
Porque algo que no explique, es que una función que esta escuchando un channel, no finaliza hasta que el channel es leído.

Esto sucede porque el operador que se utiliza para leer un channel ```<-``` es un operador bloqueante, por lo tanto mientras en el channel no se escriba y se lea algo, el programa no termina nunca (en realidad el compilador no dejaría que esto pase, y nos avisaría antes de qué hay un error de lógica al momento de compilar, pero en el caso de que no se de cuenta del deadlock esto podría llegar a pasar) por lo tanto hay que tener cuidado con este operador.

La forma en que funciona este operador ``<-`` es la siguiente:

Si la variable esta del lado derecho, esta "sacando" datos, por lo tanto esta leyendo, si la variable esta del lado izquierdo, esta "metiendo" datos a ese channel, es el mismo operador para los dos casos, no cambia dependiendo de si escribe o lee, lo que cambia es la posicion de las variables / datos

ejemplo:

```go
<- done // esta leyendo los datos de done
```
```go
done <- true // esta escribiendo true dentro del channel done
```

```go
package main

import (
	"fmt"
)

func main() {
	done := make(chan bool) // creo un channel del tipo bool

	fmt.Println("Hola, mi nombre es: ")
	go getName(done) // le pasamos el channel para que lo pueda escribir
	
	<- done // me quedo escuchando el channel y bloqueando hasta que llegue un mensaje
}

// recibimos el channel como variable para poder escribirlo
func getName(done chan bool){
	fmt.Println("Brian") // hago el print de Brian
	done <- true // escribo en el channel true para indicar que la rutina termino
}
```

[Probar codigo](https://play.golang.org/p/YOXXoMuxXIw)


Con eso ya tenemos las rutinas sincronizadas y funcionando sin problemas usando channels :)











