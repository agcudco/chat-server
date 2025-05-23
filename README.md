# chat-server
# Chat-App Server

Este README describe los pasos para crear y configurar el servidor de tu aplicación de chat en tiempo real usando **Node.js**, **Express** y **Socket.IO**.

---

## 1. Prerrequisitos

* **Node.js** (v14 o superior)
* **npm** o **yarn**
* Acceso a la terminal/CLI

---

## 2. Estructura de carpetas

En la raíz de tu proyecto, crea la carpeta `server`:

```
chat-app/
└── server/
    ├── index.js
    ├── .env
    └── package.json
```

---

## 3. Inicializar proyecto

1. Navega a la carpeta del servidor:

   ```bash
   cd chat-app/server
   ```

2. Inicializa el proyecto Node.js:

   ```bash
   npm init -y
   ```

3. Instala las dependencias necesarias:

   ```bash
   npm install express socket.io cors dotenv
   ```

---

## 4. Configuración de Variables de Entorno

1. Crea el archivo `.env` en `server/` con el siguiente contenido:

   ```dotenv
   PORT=3001
   CORS_ORIGIN=http://localhost:3000
   ```

2. Asegúrate de incluir `.env` en tu `.gitignore`.

---

## 5. Código del Servidor (`index.js`)

```js
// Carga variables de entorno
require('dotenv').config();

const express = require('express');
const http = require('http');
const cors = require('cors');
const { Server } = require('socket.io');
const dns = require('dns');

// Crear aplicación Express\ const app = express();
app.use(cors({ origin: process.env.CORS_ORIGIN }));

// Crear servidor HTTP
const server = http.createServer(app);

// Inicializar Socket.IO\ const io = new Server(server, {
  cors: { origin: process.env.CORS_ORIGIN }
});

// Manejo de conexiones
io.on('connection', (socket) => {
  // Obtener IP y resolver hostname
  const clientIp = socket.handshake.address.replace('::ffff:', '');
  dns.reverse(clientIp, (err, hostnames) => {
    const clientHost = (!err && hostnames.length) ? hostnames[0] : clientIp;
    socket.emit('host_info', { host: clientHost, ip: clientIp });
  });

  // Recibir y reenviar mensajes
  socket.on('send_message', (msg) => {
    io.emit('receive_message', msg);
  });

  // Desconexión
  socket.on('disconnect', () => {
    console.log(`Usuario desconectado: ${socket.id}`);
  });
});

// Iniciar servidor
const PORT = process.env.PORT || 3001;
server.listen(PORT, () => {
  console.log(`Servidor corriendo en puerto ${PORT}`);
});
```

---

## 6. Ejecutar el Servidor

1. Asegúrate de haber instalado todas las dependencias:

   ```bash
   npm install
   ```

2. Inicia el servidor:

   ```bash
   node index.js
   ```

3. Deberías ver en consola:

   ```
   Servidor corriendo en puerto 3001
   ```

---

## 7. Próximos pasos

* Configura el cliente React para conectarse a `http://localhost:3001`.
* Añade autenticación si lo necesitas.
* Implementa persistencia en base de datos (MongoDB, PostgreSQL, etc.).

¡Listo! Ahora tu servidor de chat en tiempo real está configurado y funcionando.
