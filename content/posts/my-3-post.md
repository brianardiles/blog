---
title: "Cómo forzar un request HTTP/1 en Go"
date: 2020-11-20T09:03:20-08:00
cover:
    image: "https://photos.collectednotes.com/photos/565/f8bf5ff2-8ea6-4540-8688-f19243a48eda?x-amz-acl=public-read&X-Amz-Expires=3600&X-Amz-Date=20230201T210242Z&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIA5W2WJOHUP7IHVD6I%2F20230201%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-SignedHeaders=host&X-Amz-Signature=453f5890130f84d6cd3bdb81f09221565c85749734eb04b194d5c4efe3459c88"
---

HTTP/2 es una versión más moderna y eficiente de HTTP que tiene algunas diferencias importantes con respecto a HTTP/1. En HTTP/2, todos los headers son transmitidos en formato binario y se espera que sean en minúscula. Esto significa que si envía un encabezado con mayúsculas, es posible que no sean reconocidos por el servidor y, por lo tanto, no se procesen correctamente.

Por ejemplo, si mandamos un header como `POWERED_BY` sin avisar y sin mostrar ningun tipo de log o warning el cliente de HTTP/2 va a modificar y enviar el header como `powered_by`
Causando que el servicio que esta esperando el header todo en mayusculas, lo reciba todo en minusculas, eso haria que no encuentre el header y por lo tanto entienda que el header no fue enviado, ejecundo un flujo totalmente distinto.

En algunos casos, no tenemos acceso al API en cuestion para modificar el header esperado, por lo que la solución es usar HTTP/1 en lugar de HTTP/2. HTTP/1 no tiene restricciones en cuanto a la forma en que los headers deben ser transmitidos, por lo que podemos enviar headers con mayúsculas, camelCase, etc sin problemas.

Si estás desarrollando en Go y queres forzar al cliente a hacer un request con HTTP/1, podes hacerlo usando la lib estándar "net/http". Para eso, tenes que crear un cliente HTTP con la opción "Transport" especificando la versión HTTP que deseas usar. Ejemplo en código:

```go
package main

import (
	"crypto/tls"
	"fmt"
	"io/ioutil"
	"net/http"
)

func main() {
	// Creamos un cliente HTTP con la opción "Transport" especificando que se desea usar HTTP/1
	client := &http.Client{
		Transport: &http.Transport{
			// Deshabilitamos el protocolo TLSNextProto
			TLSNextProto: make(map[string]func(authority string, c *tls.Conn) http.RoundTripper),
		},
	}

	// Realizamos la solicitud usando el cliente HTTP creado
	req, err := http.NewRequest("GET", "https://example.com/", nil)

	// Es importante que los headers los setiemos de esta manera ya que si usamos Add() o Set()
	// Pasa por un proceso de canonicalizado que termina modificando el header tambien por mas que
	// Usemos HTTP/1
	req.Header = map[string][]string{
		"POWERED_BY": {"GO"},
	}
	if err != nil {
		fmt.Println(err)
	}

	resp, err := client.Do(req)

	if err != nil {
		fmt.Println(err)
	}

	defer resp.Body.Close()

	// Leemos el contenido de la respuesta
	body, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		fmt.Println(err)
	}

	// Imprimimos el contenido de la respuesta
	fmt.Println(string(body))
}

```

Ejemplo de request recibida por HTTP/2
![alt](https://photos.collectednotes.com/photos/565/8da434ac-8cf2-411f-a7ea-40590cb89d00?x-amz-acl=public-read&X-Amz-Expires=3600&X-Amz-Date=20230201T215513Z&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIA5W2WJOHUP7IHVD6I%2F20230201%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-SignedHeaders=host&X-Amz-Signature=bf9cb168fd9651bee873bfa00eb73db02feb70b91b04291ed533790ae69f9c27)

Ejemplo de request recibida por HTTP/1
![alt](https://photos.collectednotes.com/photos/565/eed21591-8705-4dcf-917c-ad26c2e01eab?x-amz-acl=public-read&X-Amz-Expires=3600&X-Amz-Date=20230201T215616Z&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIA5W2WJOHUP7IHVD6I%2F20230201%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-SignedHeaders=host&X-Amz-Signature=82d4de357ff164df17054e0659f14569909fa490d1784b1aed09f8b4845b8505)

En resumen, si estás teniendo problemas con los headers en HTTP/2, podes solucionarlo usando HTTP/1 y forzando a tu cliente de Go a hacer la solicitud con esta versión de HTTP. Para eso, tenes que crear un cliente HTTP con la opción "Transport" deshabilitada