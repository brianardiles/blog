---
title: "Cómo evitar el spam y ocultar tu correo usando Cloudflare"
date: 2024-10-09
draft: false
tags: ["spam", "cloudflare", "email", "productividad"]
--- 
Me molesta mucho recibir **spam** en el mail, y peor todavía que para cualquier cosa te pidan un mail válido aunque no sea necesario.

💡 **La solución**: Encontré una forma fácil y gratis de solucionarlo usando **Cloudflare** + **Gmail**.

Con **Email Routing** podés crear un mail usando tu dominio, por ejemplo `spam@tudominio.com`, y hacer que todo lo que llegue ahí se reenvíe a tu mail principal (tipo tu Gmail).

Después, en Gmail, podés crear una regla para que esos mails con destinatario `spam@tudominio.com` se **archiven**, **eliminen** o les pongas un **tag** automáticamente. Así, cada vez que te pidan un mail para registros, usás ese y te olvidás del spam.

Es súper simple y te ahorra un montón de molestias en tu inbox.
