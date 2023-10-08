---
title: "Desarrollando una app de seguimiento de agua personal sin experiencia en Swift, utilizando ChatGPT"
date: 2023-10-07T09:03:20-08:00
cover:
    image: "posts/images/ai-human.jpg"
    caption: "Imagen de rawpixel.com en Freepik"
---

Existen cientos de apps para tracking de cuanta agua tomamos al día. Pero el 99,9% de las apps tiene **al menos 1** de los siguientes problemas:

- Requiere registro/loggin
- Necesita conexión a internet
- Tienen ads
- UI compleja
- Tracking intrusivo (para mas ads)
- No tiene soporte para Apple Watch

Por lo que opte por crear mi propia app **intentando no caer en ninguno del los problemas anteriores**. Hice algunos wireframes a mano con la idea que tenia en la cabeza.
La primera barrera que tuve fue contar con casi 0 conocimiento en Swift (*el lenguaje que se usa para crear apps para cosas Apple*) por lo que se me ocurrió empezar a preguntarle a chatGPT

Prompt inicial:

`Código en Swift para una aplicación de tracking de agua diario donde cuente con un botón para agregar un litro de agua cada vez que se toca y un scroll para poder seleccionar entre 0,25 0,5 o 1 litro de agua, eso se tiene que ir sumando y mostrar un progreso donde el 100% representa 3 litros de agua`

**después de varias iteraciones** (unas 8 o 10) el chat retorno un resultado bastante potable 90% funcional, con algunos ajustes a mano quedo funcionando, todo esto con gpt-3.5 (*la versión free, calculo que usando la versión 4 de pago el resultado hubiera sido mejor*)

Con algunas mejoras manuales (a pura prueba y error) quedo una app 100% funcional que no cae en ninguno de los problemas iniciales y cumple su única tarea: tracking de agua tomada.

{{< tweet user="brai4u" id="1710810883402727932" >}}