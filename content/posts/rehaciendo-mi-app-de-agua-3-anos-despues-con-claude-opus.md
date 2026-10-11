---
title: "Rehaciendo mi app de agua 3 años después, esta vez con Claude Opus 5.5"
date: 2026-10-11T18:00:00-03:00
tags: ["IA", "Claude"]
cover:
    image: "posts/images/agua-claude/app.png"
---

En octubre de 2023 armé una [app de seguimiento de agua](/posts/desarrollando-una-app-de-seguimiento-de-agua-personal-sin-experiencia-en-swift-utilizando-chatgpt/) sin saber Swift, preguntándole a ChatGPT. La idea era no caer en ninguno de estos problemas:

- Requiere registro/login
- Necesita conexión a internet
- Tiene ads
- UI compleja
- Tracking intrusivo (para más ads)
- No tiene soporte para Apple Watch

Tres años después quise ver cuánto cambió la cosa, así que hice lo mismo pero con **Claude Opus 5.5** desde Claude Code.

Prompt inicial:

`Quiero hacer esto pero con claude opus 5.5 https://brianardiles.com/posts/desarrollando-una-app-de-seguimiento-de-agua-personal-sin-experiencia-en-swift-utilizando-chatgpt/`

Le pasé el link a mi propio post y nada más.

## Qué cambió

**En 2023** ChatGPT (gpt-3.5, la versión free) me devolvía código, yo lo pegaba en Xcode, lo probaba y volvía con el error. Fueron unas 8 o 10 iteraciones para llegar a algo 90% funcional, y después ajustes a mano a prueba y error.

**Ahora** el modelo trabaja directo en mi compu. Leyó el post, escribió los archivos, levantó un servidor local, abrió un navegador, tocó los botones y miró capturas de pantalla para ver si quedaba bien. No escribí ni una línea de código.

Lo primero que hizo fue avisarme que estoy en Windows, y para compilar Swift para iPhone hace falta Xcode, que solo corre en Mac. Me dio a elegir entre escribir el proyecto en SwiftUI para compilarlo después en una Mac o hacer una PWA (*una web que se instala en el inicio del iPhone y anda sin internet*). Elegí la PWA.

La primera versión funcionaba pero no se parecía en nada a la original. Segundo prompt:

`Fijate el video de twitter como es, la UI tiene que ser como esa (las mejoras estan bien) https://x.com/brai4u/status/1710810883402727932`

{{< tweet user="brai4u" id="1710810883402727932" >}}

Bajó el video del tweet, sacó un cuadro por segundo y los miró. De ahí sacó cosas que yo no le había dicho: que había una segunda pantalla "Profile" con una rueda para elegir el peso, que la meta se calcula como **peso × 35 ml** (lo que recomienda la OMS) y que salta confeti al llegar al 100%. La rehizo igual, y de paso sumó un botón para deshacer y una pantalla con el historial de la semana, que la v1.0 no tenía.

Probándola encontró dos bugs solo:

- La gota del centro salía cortada abajo
- El confeti avanzaba por cuadro y no por tiempo, así que en un iPhone de 120 Hz iba a ir al doble de velocidad

El segundo no lo hubiera visto nunca.

Después `subila a Vercel` y `configurala en agua.brianardiles.com`. Lo único que tuve que hacer yo fue aprobar el login de Vercel y agregar un registro CNAME en Cloudflare, porque a mi cuenta no tiene acceso.

## Los problemas iniciales

- Requiere registro/login: no
- Necesita internet: no, después de la primera vez anda offline
- Ads: no
- UI compleja: dos pantallas y el historial
- Tracking: los datos quedan solo en el teléfono
- Apple Watch: **sigue sin tener**. Una web no corre en el reloj, para eso hay que volver a Swift y a una Mac

## 2023 vs 2026

- **2023**: gpt-3.5, unas 10 iteraciones, código copiado a mano, ajustes a prueba y error, app nativa que quedó en mi teléfono
- **2026**: Claude Opus 5.5, 4 prompts, cero código a mano, se probó sola y quedó publicada en internet

La app sigue haciendo una sola cosa: tracking de agua tomada. Está en [agua.brianardiles.com](https://agua.brianardiles.com), se abre desde Safari y se agrega al inicio con Compartir → "Agregar a inicio".
