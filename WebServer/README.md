# WebServer

## Visualizar mi primer servidor web
Para poder visualizarlo haz click [aquí](https://kaiwebpage.herokuapp.com/).

## Introducción a Express
Durante esta sección se ve como podemos crear un servicio web básico haciendo uso de **Express**.

Anteriormente, para poder crear peticiones _HTPP_ se utilizaba el módulo de `HTTP`, y se creaba una petición web de la siguiente manera:


```javascript
// -------- USANDO HTTP --------
const http = require('http');
const PORT = 8080;

http.createServer((req, res) => {
        res.write("Hola Mundo\n");
		res.write(JSON.stringify(salida));
        res.end();
    })
    .listen(PORT);

console.log(`Escuchando el puerto ${PORT}.`);
```
Sin embargo, gracias a la aparición de `Express`, este mismo código puede realizarse en menos líneas, y un más entendible.

```javascript
// -------- USANDO EXPRESS --------
const express = require('express');
const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
    res.send('Hola Mundo');
});

app.listen(PORT, () => {
    console.log(`Escuchando peticiones en el puerto ${PORT}`);
});
```

Quizás no se vea muy poderoso en primera instancia, pero si quisiéramos extender esto para que validara y trabajase con más rutas, __usando http sería muy engorroso__. Sin embargo, cuando trabajamos con __express__ hacemos uso de algo que se llama `middlewares`, los cuáles __nos permiten tener un control de las rutas de nuestra página o proyecto web__. 

> Por ejemplo, si quisiéramos una nueva ruta cuyo nombre sea `localhost:3000/data`, para hacerlo con Express se haría lo siguiente.

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
    res.send('Hola Mundo');
});

app.get('/data', (req, res) => {
    res.send("Hola Data");
});

app.listen(PORT, () => {
    console.log(`Escuchando peticiones en el puerto ${PORT}`);
});
```

Si probamos este código en el `Postman` nos da lo siguiente.

<img align="center" src="../WebServer/imgs/01_path_local.JPG" width="800"/>

<img align="center" src="../WebServer/imgs/02_path_data.JPG" width="800"/>

## Servidor contenido estático
Para poder ir explicando esta sección lo que se hará es crear una carpeta llamada `public`, dentro de la cuál haremos un archivo `HTML` para desplegar una página web.

> _¿Cómo se hace para que el servidor de **Express** muestre esta página web de nombre `index.html`_
>
> _¿Cómo hago que me muestre la página cuanod haga `localhost:3000/`_

<img align="center" src="../WebServer/imgs/03_pagina_estatica.JPG" width="500"/>

Para poder hacer esto haremos uso de nuestro __middleware__.

> __Middleware__: Es básicamente una instrucción/callback siempre _sin importar que URL es la que la persona pida_.
>
> ¿Cómo creamos ese `middleware`?

Para poder hacer esto lo que haremos será lo siguiente.

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

// Usando el middleware
app.use(express.static(__dirname + '/public'));
...
```

En donde:
* `express.static`: Es la _callback_ que estamos usando.
* `__dirname`: Nos especifica el __directorio actual__.
* `/public`: Es el folder que será estático o público.

Ahora vamos a ver si nuestro código funciona bien.

<img align="center" src="../WebServer/imgs/04_holamundo_express.JPG" width="500"/>

Normalmente es recomendable comentar nuestro código donde hacemos uso del `app.get` ya que puede entrar en conflicto. Entonces nuestro código quedaría como lo siguiente:

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

// Usando el middleware
app.use(express.static(__dirname + '/public'));

/*
app.get('/', (req, res) => {
    //res.send('Hola Mundo');
});
*/

/*
app.get('/data', (req, res) => {
    res.send("Hola Data");
});
¨*/

app.listen(PORT, () => {
    console.log(`Escuchando peticiones en el puerto ${PORT}`);
});
```

> _Pero, ¿qué pasa si quiero otra URL?_ como `localhost:3000/home`.

Bueno, probemos y veamos que pedo.

<img align="center" src="../WebServer/imgs/05_errorHome.JPG" width="500"/>

Como podemos ver nos muestra un error porque no hemos habilitado esa ruta. Para hacerlo, vamos a crear una página, dentro de `public/` llamado __home.html__. 

<img align="center" src="../WebServer/imgs/05_errorHome2.JPG" width="500"/>

Sin embargo, vemos que no regresa nada __ya que lo que busca es un `servicio` no una página. Es decir, un folder que se llame `home` y que dentro tenga un archivo llamado `index.html`. 

> Para este caso __debemos especificar el nombre del archivo al que desamos acceder. En este caso `home.html`

<img align="center" src="../WebServer/imgs/06_homehtml.JPG" width="500"/>

## Crear una pequeña página web

Lo primero que vamos a hacer es ir a la página de [Bootstrap](https://getbootstrap.com/docs/4.5/getting-started/download/) y le daremos en `Download source`. Todo esto con el fin de meterle estilo a nuestra página.


En este punto ya tenemos nuestra página completa.
Sin embargo, _¿qué pasaría si queremos agregar una página más?_. Probablemente sería esto
* Tendriamos que copiar todo el código de la página: encabezados, footers, navbars, etc.
* Adaptar la página y más.

Afortunadamente _Express_ trabaja con librerías y paquetes que permite la renderización dinámica de sitios web.

Para ello trabajaremos con __handlebars__.

## Handlebars (hbs)
Este paquete (hbs) le permitirá a __Express__ renderizar las páginar con el fin de mandarle una respuesta a nuestro cliente para hacer algo como:

```javascript
<p> {{firstname}} {{lastname}} </p>
```

> _TODO ESTO CON EL FIN DE HACERLO DINÁMICO_
En primera instancia se deberá de instalar

> >npm install hbs --save

Para poder usar de una forma correcta el handlebar debemos de agregar, lo siguiente en el `server.js`:

```javascript
// Express HBS engine
app.set('view engine', 'hbs');

app.get('/', (req, res) => {
    res.render('home', {
        nombre: "Jesus",
        anio: new Date().getFullYear()
    });
});
```

Y en nuestro archivo `views/home.hbs` agregar:

```html
...
<div class="jumbotron">
        <h1 class="display-4">Hello, {{nombre}}!</h1>
</div>        
...
<footer>
    Jesús Díaz - {{anio}}
</footer>
```

Para poder reutilizar nuestras páginas, vamos a hacer uso de __`partials`__ and __`helpers`__.
* __`partials`__: Es un bloque de código HTML que podemos reutilizar.

> Para hacer esto vamos a utilizar un folder que contenga todos los parciales.

Tal y como se muestra a continuación:

```javascript
hbs.registerPartials(__dirname + '/views/partials', function (err) {});
```

> ¿Cómo creo un _`partial`_?

### Partials
Para crear un _partial_ debemos de crear una carpeta con ese nombre y dentro de él debemos de crear los archivos handlebars (`.hbs`) que queremos o que se van a reutilizar.

<img align="center" src="../WebServer/imgs/07_partials.JPG" width="850"/>

> Aquí podría haber un error ya que quizás sea necesario reiniciar el `nodemon` para poder ver los cambios.

Para especificarle que `nodemon` esté al pendiente de esto, debemos de ejecutar el nodemon de la forma:

> Linux
>
> nodemon server -e js,hbs,html,css
>
> Windows
>
> nodemon .\server.js -e js,hbs

### Helpers
En ocasiones hay secciones de código que se repiten bastante, lo cuál puede modularse de tal forma que solamente aparezca una sola vez. Para hacer esto hacemos uso de los __helpers__.

Por ejemplo, en esta sección de código del archivo `server.js`:

```javascript
app.get('/', (req, res) => {
    res.render('home', {
        nombre: "Jesus",
        anio: new Date().getFullYear()
    });
});

app.get('/about', (req, res) => {
    res.render('about', {
        anio: new Date().getFullYear()
    });
});
```
Se repite el segmento de código de `anio: new Date().getFullYear()`. Quizas nos gustaría simplificar esto, ¿cómo lo haremos?

Para ello hacemos uso de los __`helpers`__ del handlebar.

> __Helper__: Es una función que se dispara cuándo el template lo requiere.

Para realizar el helper en nuestro código del server, hacemos lo siguiente:

```javascript
...
app.set('view engine', 'hbs');

// Helpers
hbs.registerHelper('getAnio', () => {
    return new Date().getFullYear();
});

app.get('/', (req, res) => {
    res.render('home', {
        nombre: "Jesus"
    });
});

app.get('/about', (req, res) => {
    res.render('about');
});
```
En donde:
* `getAnio`: Es el nombre de la función
* función de flecha: Realiza el comportamiento que necestiamos.

Si nosotros dejamos como tal nuestro código, se muestra como a continuación.

```html
<footer>
        Jesús Díaz - {{anio}}
</footer>
```
<img align="center" src="../WebServer/imgs/08_Sinanio.JPG" width="850"/>

Y ahora, debemos de modificar en donde utilizabamos la variable `anio`. Entonces debemos de ir al archivo `footer.hbs` y modificar lo siguiente:

```html
<footer>
        Jesús Díaz - {{getAnio}}
</footer>
```
<img align="center" src="../WebServer/imgs/08_getAnio.JPG" width="850"/>

Pero, ¿porqué lo hace?
> Si no existe una variable que estamos mandando en el archivo `server.js`, como el nombre, en alguno de los __handlebars__, inmediatamente después va a revisar en los __helpers__ a ver si existe algo.

¿Puede haber más de un helper?
Sí es posible, y veremos cómo debemos de manejarlos correctamente.
Supongamos que ahora queremos capitalizar el nombre. Para ello debemos de crear otro helper de las siguiente forma:

```javascript
// Helpers
...
hbs.registerHelper('capitalizar', (text) => {
    let words = text.split(' ');

    words.forEach((word, idx) => {
        // Actualizamos la posición de las palabras
        words[idx] = word.charAt(0).toUpperCase() + word.slice(1).toLowerCase();
    });

    // Juntar todas las palabras 
    return words.join(' ');
});
```
Para poder utilizarlo vamos a modificar el archivo __home.hbs__ de la siguiente manera.

```html
...
<h1 class="display-4">Hello, {{ capitalizar 'jeSus diAz '}}!</h1>
...
```
> Esto sería de forma estática

<img align="center" src="../WebServer/imgs/09_capitalizarEstatico.JPG" width="850"/>


```html
<h1 class="display-4">Hello, {{ capitalizar nombre }}!</h1>
```
> Recuerda que `nombre` fue declarado en __server.js__.

<img align="center" src="../WebServer/imgs/09_capitalizarDinamico.JPG" width="850"/>

#### Agrupando `helpers`
Algo que hasta el momento vemos es que todos los helpers están contenidos en un solo archivo dentro de `server.hbs`.
Modulemos esto creando una nueva carpeta de nombre __hbs__ y dentro otra que se llame __helpers__.

<img align="center" src="../WebServer/imgs/10_hbs.JPG" width="850"/>

## Desplegando en Heroku
1. Hacer tu cuenta en Heroku
2. Crear una nueva app en Heroku
3. Descargar Heroku en tu equipo en el siguiente [enlace](https://devcenter.heroku.com/articles/heroku-cli). Descarga el CLI.
4. Configurar el puerto en `server.js`.

```javascript
// Obtener el puerto de Heroku
const PORT = process.env.PORT || 3000;
```

5. Nosotros sabemos que para levantar el servidor localmente usando `Express` hacemos uso del siguiente comando.

    > node server.js

    Pero Heroku no sabrá qué comando debe ejecutar, por lo cuál debemos de especificarselo. Para eso haremos lo siguiente:

    



    - Vamos al `package.json`
    - En la parte de `scripts` agregamos el siguiente:

    ```javascript
    "scripts": {
        "start": "node server.js",
        "test": "echo \"Error: no test specified\" && exit 1"
    }
    ```

    - Para ver si funcionó o no, podemos probarlo en nuestra consola, en el directorio donde está el proyecto, ejecutando el siguiente comando.

        > npm start
    
    _Este es el comando que __Heroku__ va a ejecutar para montar nuestra aplicación._

    - Podemos hacer lo mismo para que se dispare el `nodemon server.js`.

    ```javascript
    "scripts": {
        "start": "node server.js",
        "nodemon": "nodemon server.js",
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    ```

    Pero no se va a ejecutar como el `start`, sino que se hara de la siguiente forma:

        > npm run nodemon

6. __Heroku__ trabaja en base a __Git__, por lo que debemos hacer lo mismo para subir un repositorio.
7. Realizamos `> heroku login`
8. Inicializamos el proyecto: `> git init`
9. Agregamos los archivos al stage: `> git add .`
10. Realizamos el commit: `> git commit -m "Mi primera app"`
11. Especificamos que lo subiremos a Heroku: `> heroku git:remote -a nombre_pagina`
12. Lo subimos a heroku: `> git push heroku master`
