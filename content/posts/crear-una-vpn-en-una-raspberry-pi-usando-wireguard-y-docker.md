---
title: "Crear una VPN en una Raspberry Pi usando WireGuard y Docker"
date: 2024-01-21T18:52:53-03:00
cover:
    image: "posts/images/vpn-wireguard/louis-reed-zDxlNcdUzxk-unsplash.jpg"
    caption: "Imagen de unsplash.com"
draft: false
---

Usar una [VPN](https://es.wikipedia.org/wiki/Red_privada_virtual) es clave para la privacidad y la seguridad. Además, si como en este caso es self-hosted en nuestra LAN tenemos la posibilidad de acceder a recursos locales desde redes externas por ejemplo 4g/5g

**Casos de uso prácticos:**

- Navegar desde el celular a través de nuestra propia red de manera segura (opcional enviar el tráfico a través de pi-hole para bloquear la publicidad y el trackeo de datos incluso en redes moviles)
- Acceder a recursos locales desde el exterior, por ejemplo acceder a la raspberry pi por ssh desde una red externa sin que la raspi esté expuesta a tráfico externo
- Poder seguir viendo contenido local en plataformas de streaming mientras estamos de viaje

Existen varios servicios que ofrecen VPNs a cambio de un par de dólares al mes, las contras que tenemos usando estos servicios son:

- No tenemos acceso a recursos locales
- No tenemos forma de verificar que no estén almacenando y vendiendo los datos que pasan a través de su red 

## Requisitos para crear nuestra propia VPN

- Raspberry Pi
- Docker
- WireGuard

## WireGuard

Hace algunos años el estándar de la industria era OpenVPN, un gran software para crear VPNs, pero que con el paso de los años se volvió algo lento, tosco y aunque sigue siendo una opción totalmente valida, últimamente hay un nuevo protocolo que está ganando muchos fanáticos, [WireGuard](https://www.wireguard.com) un software escrito en C, mucho más ligero y rápido gracias a su integración directa en el kernel del SO, lo que permite conexión directa con la capa de red con menos intermediarios

Para crear la VPN en la raspberry vamos a usar docker y docker compose.
Prefiero siempre usar software *dockerizado* ya que me permite guardar una copia del .yaml en un repo de GitHub lo que me da la posibilidad de reinstalar todo si es necesario (por un formateo, una pérdida de datos, una nueva raspi) de una manera mucho más rápida y cómoda

**Los pasos a seguir son:**

1. Crear un archivo docker-compose.yaml con la imagen y los parámetros que contienen WireGuard + Web admin UI

```
version: "3.8"
services:
  wg-easy:
    container_name: wg-easy
    image: ghcr.io/wg-easy/wg-easy
    environment:
      - PASSWORD=<secret passowrd>
      - WG_HOST=<IP host o DNS>
    volumes:
      - ./config:/etc/wireguard
      - /lib/modules:/lib/modules
    ports:
      - "51820:51820/udp"
      - "51821:51821/tcp"
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.ip_forward=1
      - net.ipv4.conf.all.src_valid_mark=1

```

3. Ejecutar el comando `docker compose up -d`

4. Ingresan al admin UI para crear un nuevo cliente `http://<IP HOST>:51821/`

5. Agregar un nuevo cliente desde la UI

![](/posts/images/vpn-wireguard/admin-ui-add-client.png)

7. Instalar el cliente de WireGuard e Instalar el certificado, por ejemplo en el cliente para iPhone y Android se puede escanear el QR desde la UI de admin

8. (Opcional) Puede que por defecto el router tenga los puertos bloqueados o sin forward, en este caso lo que hay que hacer es ingresar a la configuración del router, en opciones avanzadas activar el puerto 51820 y hacer el forward del puerto desde 51820 externo hacia el 51820 interno apuntando a la IP interna de la raspberry 

Y con esos pasos ya tenemos una VPN corriendo en nuestra LAN con un cliente conectado.