# Módulo de control de recetas para derechohabientes del IMSS Bienestar

Este es el proyecto final de la materia _Introducción al Desarrollo de Aplicaciones Web_. Esta es una aplicación que tiene el propósito de proporcionar facilidad para el registro de recetas y su seguimiento por parte de los médicos para pacientes derechohabientes del sistema de salud.

### Prerrequisitos:

- Node JS
- MongoDB Server / MongoDB Atlas

## Para ejecutarlo localmente:

Necesitarás tener la herramienta Git en tu computadora para seguir este proceso. La puedes descargar en https://git-scm.com

1. Para descargar el repositorio:

`git clone https://github.com/Josgtg/recetas_imss`

2. Ya debes ser capaz de instalar las dependencias necesarias con npm:

`npm install`

3. Necesitas crear un archivo con el nombre _.env (Importante notar que es sólo la extensión, sin nombre de archivo)_ dentro de la carpeta principal. Dentro de este archivo debe venir la siguiente estructura:

PORT = _Número de puerto al que va a escuchar la aplicación_

CONNECTION_STRING = _Link a tu base de datos de MongoDB (Entre comillas)_

SECRET = _Palabra o frase de tu preferencia. Es una clave necesaria para la funcionalidad de las cookies (Entre comillas)_

4. Habiendo hecho lo anterior, deberías poder iniciar la aplicación:

`npm run dev` - Para usar nodemon

o

`npm start`

Si el comando se ha ejecutado de forma exitosa, podrás ver la aplicación en ejecución en la dirección<br>htt<span>p://localhost:</span>_El puerto que escribiste en tu archivo.env_<br>
_htt<span>p://localhost:</span>9030, por ejemplo._

También está disponible en http://idawis-uaz.ddns.net:9030

# Uso de la API

Todas las peticiones y respuestas se manejan en formato JSON.

El api se compone de las siguientes url:

- /api/usuarios
- /api/recetas
- /api/medicinas

ejemplo: htt<span>p://localhost</span>:9000/api/usuarios

### Métodos y subrutas para /api/usuarios

- (Admin) GET /<br>Regresa una lista de todos los usuarios registrados.
- (Público) GET /_id_<br>Regresa al usuario con el id especificado de existir en la base de datos.
- (Público) GET /current<br>Regresa el usuario actual.
- (Público) GET /curp/_curp_<br>Regresa al usuario que tenga la curp especificada.
- (Público) POST /signup<br>Permite registrar a un usuario y registra una cookie con la sesión.<br>
  Para registrar un usuario se necesitan los campos: curp, name, password y kind.
- (Público) POST /login<br>Registra una cookie con la sesión si los datos de inicio de sesión son correctos.<br>
  Estos datos deben de ser: curp y password.
- (Público) PUT /_id_<br>Permite actualizar los datos del usuario con el id especificado.
- (Público) DELETE /_id_<br>Permite eliminar al usuario con el id especificado.

Los métodos PUT y DELETE sólo funcionan si el usuario a actualizar o eliminar es el mismo que el de la sesión activa, a menos de que quien haga la petición sea un administrador.

### Métodos y subrutas para /api/medicinas

- (Público) GET /<br>Regresa una lista con todas las recetas.
- (Público) GET /_id_<br>Regresa el medicamento con el id especificado.
- (Público) GET /clave/_clave_<br>Regresa el medicamento con la clave especificada.
- (Admin) POST /<br>Permite subir una medicina.<br>
  Para subir una medicina son necesarios los campos: clave, name y description.
- (Admin) PUT /_id_<br>Permite actualizar los datos de la medicina con el id especificado.
- (Admin) DELETE /_id_<br>Permite eliminar la medicina con el id especificado.

### Métodos y subrutas para /api/recetas

- (Admin) GET /, GET /_id_<br>Regresa todas las recetas.
- (Doctor) GET /, GET /_id_<br>Regresa todas las recetas que haya hecho ese doctor.
- (Paciente) GET /, GET /_id_<br>Regresa las recetas de ese paciente.
- (Doctor) POST /<br>Permite subir una receta.<br>
  Para subir una receta son necesarios los campos: patient (La curp del usuario para el que se está escribiendo la receta), residence, medicine (Una lista con el id de las medicinas de la receta) y quantity (Una lista con la cantidad de cada medicamento relacionados por posición)
- (Doctor) PUT /_id_<br>Permite actualizar la receta con el id especificado.
- (Público) PATCH /_id_<br>Permite actualizar el estado de la receta. El único campo necesario es: state.
- (Doctor) DELETE /_id_<br>Permite eliminar la receta con el id especificado.

## Ejemplo de cómo subir un usuario a la base de datos:

Ya sea desde el frontend, o directamente a la api, el proceso es parecido para las otras url.
Usaremos de ejemplo la api _fetch_ de javaScript.

```javascript
async function signUserIn() {
  let user = {
    curp: "MAAR790213HMNRLF03",
    name: "Rafael Márquez",
    password: "123456789",
    kind: "admin",
  };

  let request = {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(user),
  };

  let response = await fetch("/api/usuarios/signup", request);

  return response.json();
}
```

El objeto que regrese esta función va a contener la respuesta de la api.

También es importante notar que todos los objetos o datos que se quieran enviar tienen que ir en el campo
_body_ de la petición.
