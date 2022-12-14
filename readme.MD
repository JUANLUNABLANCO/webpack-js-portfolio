<a href="https://gitmoji.dev">
  <img src="https://img.shields.io/badge/gitmoji-%20😜%20😍-FFDD67.svg?style=flat-square" alt="Gitmoji">
</a>
<br/>

# CURSO DE WEBPACK

## INICIALIZAR PROYECTO

> git init
> git config --local user.email "desarrolloaplicacionesweb.jmlb@gmail.com"
> git config --local user.name "JUANLUNABLANCO"
> git branch -M  main

> git remote add origin https://github.com/JUANLUNABLANCO/webpack-js-portfolio.git
> git config --list
> git add .
> git commit -m "scaffolding project with webpack"
> git push -u origin main



> npm init
> mkdir src
> npm i -D webpack webpack-cli
> npx webpack  // al no instalarse webpack de forma global, para ejecutarlo necesitas usar npx

Este comando empaqueta, sin necesidad de ningún archivo de configuración, aunque con warning, el index.js y lo manda a 'dist/main.js', carpeta que no existía y ahora si.
Además ha reducido el código optimizándolo, porque para pasrale a una variable un texto no es necesario crear la variable...

> npx webpack --mode deveploment

Ahora estamos en modo development y no nos crea ningún warning, como antes.

Sin embargo ahora el main.js ha cambiado, craga módulos y tiene una url por defecto para las rutas del proyecto:
sourceURL=webpack://02-curso-de-webpack-platzi/./src/index.js?

> npx webpack --mode production

Ahora con este comando en producción ,lo minifica al máximo, es decir hace un build simple.


si creamos otro fichero js en una carpeta dentro de src y ejecutamos un código como el del fichero './utils/sum.js', que exporta una función sum, y lo importamos en index.js podremos usarlo y al ejecutar de nuevo:

> npx webpack --mode production o > npx webpack --mode development  

hará el build, de cada archivo js que sea enlazado en el index.js con un import

De hecho, he creado otro llamado './utils/dismiss.js', que al no ser importado en index.js, no lo compila en el build.


## configurando webpack

Este archivo './webpack.config.js' debe estar en la raiz del proyecto, y estos otros aquí:
'./src/index.js' y './public/index.html'

--- webpack.config.js ---
const path = require('path');

module.exports = {
    entry: './src/index.js', // archivo js de entrada
    output: { // salida, recuerda debes estar en la raiza del proyecto
        path: path.resolve(__dirname, 'dist'),
        filename: 'main.js' // puedes llamarlo también 'bundle.js' o 'app.js' o optimizarlo con hash
    },
    resolve: {
      extensions: ['.js'] //  '.jsx' uotras dependiendo si trabajas con react, sveltte, etc
    }

};

--- ---

> npx webpack --mode production --config webpack.config.js

ahora en este punto, con esa configuración solo tendríamos el js, ni html ni css ni imagenes ni nada

vamos a configurarlo de nuevo para que pueda alcanzar todo eso

si vamos al package json para anotar el script del build haremos lo siguiente

--- package.json ---
...
"scripts": {
        "build": "npx webpack --mode production --config webpack.config.js"
    },
--- ---
pero afortunadamente, no necesitamos indicarle ese archivo de configuración si está en la raíz, cuando se hace desde el package json, entonecs quedaría así:
--- package.json ---
"scripts": {
        "build": "npx webpack --mode production"
    },
--- ---

> npm run build

## Babel | hacer el proyecto compatible para todos los navegadores

> npm install babel-loader @babel/core @babel/preset-env @babel/plugin-transform-runtime -D

@babel/core                       babel
babel-loader                      loader para webpack
@babel/preset-env                 trabajar con javascript moderno
@babel/plugin.transform-runtime   para que entienda await y async

recuerda que si haces correctamente la instalación sin errores de sintaxis recibirás un mensaje como este:
...
found 0 vulnerabilities

  ### archivo de configuracion de babel: .babelrc

  --- .babelrc ---
  {
    "presets":[
      "@babel/preset-env"   // para que acepte este que hemos instalado
    ],
    "plugins":[
      "@babel/plugin-transform-runtime" // para que acepte este que hemos instalado
    ]
  }
  --- ---

## seguimos configurando webpack añadiendo babel en los modulos

...
module: [
  {
    test: /\.m?js$/   
  }
]

module: {
      rules:[
        {
          test: /\.m?js$/,  // // cualquier archivo cuyo nombre acabe en '.mjs' o '.js', por lo tanto será su extensión '.mjs' se usa en los módulos de javascript en algunos paquetes y librerías com oreact o svelte

          exclude: /node_modules/, // que excluya esos archivos para que no los compile

          use: {
            loader: 'babel-loader' // el loader de abbel
          }
        }
      ]
    }

  
En este punto, si ejecutamos el comando del build

> npm run build

nos genera un main.js mucho más extenso y compatible con todos los navegadores, que no se verá el servirlo puesto que nos falta el index.html, ...

## webpack Loader para el html

> npm i -D html-webpack-plugin

en el archivo de configuracion de webpack

--- webopack.config.js ---
    const HtmlWebpackPlugin = require('html-webpack-plugin');

    ...
    // depues de modules
    ,
    plugins: [
        new HtmlWebapckPlugin({
            inject: true,                     // para inyectar los elementos
            template: './public/index.html'   // donde se encuentra el template de inicio donde debe inyectar el js
            filename: './index.html'          // a dónde y cómo se va a llamar una vez compilado, recordemos esto irá a '/dist' (establecido en el output), así que será '/dist/index.html'
        })
    ]
--- ---

Una vez hecho esto, dedemos hacer cambios en index.html

--- index.html ---
...
<body>
    <div id="main"></div>
    <!-- <script type="module" src="../src/index.js"></script> -->
</body>
...
--- ---

es decir webpack se encarga de inyectar el js debido al html-webpack-plugin, en su configuración (inyect: true)

> npm run build     // construido y minificado
> npm run dev       // construido en desarrollo sin minificar

sin embargo el css enlaza debido a que su href="../src/styles.css" y aunque esté en el 'dist/' enlaza, pero claro la carpeta 'dist/' no contiene css.


## CSS y preprocesadores de css (sass, scss, less)

> npm install -D css-loader mini-css-extract-plugin

mini-css-extract.plugin   // para poder trabajar con partes de css y poder unirlas en un solo archivo css, con esto podríamos importar nuestros css parciales en cada componente o archivo

css-loader                // para que cargue el css en la compilación y lo inyecte en el html

Anteriormente teníamos en el html esto:

...
<head>
    ...
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="../src/styles/main.css">
    ...
    <title>Document</title>
</head>

ahí tenemos la referencia a los estilos que por suerte o como está nuestro proyecto estructurado enlaza tanto desde el src/ como desde el dist/, debido a que es una ruta relativa. Pero que pasa si lo llevamos a un servidor y se ejecuta la carpeta dist/, pues que como será en un enotrno unix, no tendrá permisos suficientes para ejecutar la carpeta de src/css/...

vamos a configurar webpack y index.js para que acepte los loaders y plugins que instalamos para el css y se puedan importar directamente desde el js

--- index.js ---
import { Template } from "./templates/Template.js";
import './styles/main.css';  // añadimos esta línea

console.log('Helo world');
...
--- ---

--- webpack.config.js ---
...
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
    ...
    module: {
        rules: [
            {...},
            {
                test: /\.css$/i,
                use: [MiniCssExtractPlugin.loader, 'css-loader']
            }
        ]
    },
    plugins: [
        ...
        ,
        new MiniCssExtractPlugin()

    ]
}
--- ---


y ahora ya podemos omitir el enlace al estilo

<head>
    ...
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- <link rel="stylesheet" href="../src/styles/main.css"> -->
    ...
    <title>Document</title>
</head>


## Preprocesadores (Stylus)

> npm install -D stylus stylus-loader

ahora en webpack.config.js

--- webpack.config.js ---
{
    test: /\.css|\.styl$/i,       // añadimos el pie |\.styl para que reconozcaa archivos de stylus
    use: [MiniCssExtractPlugin.loader, 'css-loader', 'stylus-loader']   // añadirlo en este orden
}
--- ---

probémoslo incluyendo un archivo .styl en la carpeta de estilos.

--- vars.styl ---

## copiar archivos automáticamente
> npm install -D copy-webpack-plugin

en nuestro Template.js tenemos lo siguiente:

--- Template.js ---
...
 <div class="card_social">
    <a href="https://twitter.com/gndx">
      <img src="../src/assets/images/twitter.png" />
    </a>
    <a href="https://github.com/gndx">
      <img src="../src/assets/images/github.png" />
    </a>
    <a href="https://instagram.com/gndx">
      <img src="../src/assets/images/instagram.png" />
    </a>
  </div>
...
--- ---

esos enlaces cargan por lo mismo que el css llaman a la ruta relativa, epro no se encuentran dentro de dist/
si estuvieran en dist/ tendrían que enlazar de la siguiente manera '/assets/recurso'

--- webpack.config.js ---
const CopyPlugin = require('copy-webpack-plugin');
...
  new CopyPlugin({
      patterns: [{
          from: path.resolve(__dirname, "src", "assets/images"), // importante comillas dobles """"
          to: "assets/images"
      }]
  })
--- ---

en el fichero template le diremos que los enlaces a las imágenes ya no serán relativos, sino desde la raíz del proyecto.

en vez de "../src/assets/images/recurso"    // url relativa desde donde se solicita
ahora será "assets/images/recurso"          // url absoluta desde la raiz del proyecto
no confundir con "/assets/images/recurso"   // url absoluta desde la raíz de tu máquina

como ves los assets que le hemos indicado al plugin se han copiado, hagamos un pequeño experimento:
intentemos copiar a 'prueba.js' en dist/prueba.js, el cual no es llamado en principio desde ningún sitio
Y en el index.html coloquemos esta línea

<script src="prueba.js"></script>

¿qué pasa?, pues lo que esperábamos, se copia, no se compila en main.js, y se ejecuta porque es llamado, además se ejecuta antes que el script que llama main.js, y esto nos puede servir para llamar y copiar lo necesario, para ejecutar los service workers y el manifest de nuestra app.


## Optimizar imágenes

Ahora las imágenes podremos importarlas desde el template, debido a la nueva configuración en webpack.config.js, usando una herramienta propia de webpack, (asset/resolve)

--- webpack.config.js ---
  module:[
    ...
            ,
            {
                test: /\.png/,            // tipo de imagenes a resolver podría ser /\.(png|gif|jpg)/ o lo que necesites
                type: 'asset/resource'
            }
            // mas avanzado
            {
                test: /\.(png|svg|jpg|jpeg|gif)$/i,
                type: 'asset/resource',
                generator: {
                    filename: 'assets/images/[hash][ext][query]',
                },
            },
            // por supuesto hay que borrar lo del copyplugin, esta parte, ya no necesitamos copiarlas
            // new CopyPlugin({
            //     patterns: [{
            //             from: path.resolve(__dirname, "src", "assets/images"),
            //             to: "assets/images"
            //         },
            o podríamos haber dejado esta ultima configuracion y en el output de arriba poner
            output: {
              path: path.resolve(__dirname, 'dist'),
              filename: 'main.js'
              assetModuleFilename: 'assets/images/[hash][ext][query]'
            },
    ]
--- ---

y en el Template.js, las importamos de esta manera;

--- Template.js ---
import twitter from '../assets/images/twitter.png';
import github from '../assets/images/github.png';
import instagram from '../assets/images/instagram.png';
...

<a href="https://github.com/gndx">
  <img src="assets/images/instagram" />
</a>
// cambiarlo por esto otro

<a href="https://github.com/gndx">
  <img src="${instagram}" />
</a>
--- ---
Ahora esas imágenes estarán optimizadas, y inyectadas como variables js dentro del template, al llamarlas de esta forma se cargan mucho más rápido, pues no son llamadas en tiempo de ejecución por el navegador sino que son renderizadas antes desde js, incluso antes de ser servidas, ya están en el template.

## optimización de fuentes
en main css tenemos lo siguiente

--- main.css ---
@import "https://fonts.googleapis.com/css?family=Ubuntu:300,400,500";

--- ---

esto se carga desde google fonts en tiempo de ejecución, si quieres descargarlas en tu pc para poder optimizarlas mejor debes acudir a esta página que las descarga en el formato que necesites

https://gwfh.mranftl.com/fonts/ubuntu?subsets=latin

una vez importadas en el formato que necesites, en este caso (woff2), vas al css y modificas, lo siguiente

--- main.css ---
<!-- @import "https://fonts.googleapis.com/css?family=Ubuntu:300,400,500"; -->
@font-face {
  font-family: 'Ubuntu';
  src: url('../assets/fonts/ubuntu-regular.woff2') format('woff2'),
  url('../assets/fonts/ubuntu-regular.woff') format('woff');
  font-weight: 400;
  font-style: normal;
}

--- ---
ahora necesitamos instalar varias cosas:
> npm install url-loader file-loader -D

--- webpack.config.js ---
{
                test: /\.(woff|woff2)$/,
                use: {
                    loader: 'url-loader',  // el cargador de urls que hemos instalado para el css
                    options: {
                        limit: 10000,                   // transformacion directa a base 64, para archivos mayores de 10000kbs, si supera este límite usará el file-loader porque por defecto estos archivos se cargan con url-loader
                        
                        mimetype: "application/font-woff", // para poder mandar contenido dentro de la propia red aparece en las cabeceras del recurso, sino se pone puede que no te deje extraerlo en el servidor de producción debido al cross origin
                        
                        name: "[name].[ext]",
                        
                        outputPath: "./assets/fonts/",  // hacia donde lo envia
                        
                        publicPath: "./assets/fonts"    // desde donde venía
                        
                        esModule: false // no es un módulo de ES5. es para permitir que file-loader genere modulos JS que usan la sintaxis ES
                    }
                }
            }

sino quieres caragr con npm file-loader y url-loader, no es necesario ya viene integrado en webpack 5

en el webpack.config.js debes cambiar esto, queda más simple y la otra configuración es por si queires poner límites a las imágenes para base 64, que no se yo si enesta configuración se permite

--- webpack.config.js---
    {
        test: /\.(woff|woff2|eot|ttf|otf)$/,
        type: 'asset/resource',
        generator: {
            filename: 'assets/fonts/[hash][ext]'
        }
    }
--- ---

## Compresion y minificación de archivos
> npm install -D css-minimizer-webpack-plugin terser-webpack-plugin

css-minimizer-webpack-plugin            // minimiza el css
terser-webpack-plugin                   // minimiza el js

--- webpack.config.js ---
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
const TerserPlugin = require('terser-webpack-plugin');
...
output:{
  ...
  filename: '[name].[contentHash].js'  // antes 'main.js'
}
...
optimization: {
  minimize: true,
  minimizer: [
    new CssMinimizerPlugin(), // css
    new TerserPlugin()        // js
  ]
}
--- ---
ver también todos los cambios que se han realizado en los nombres de salida de ficheros
[hash][name][ext][query] etc, cada plugin lo trabaja algo diferente por lo que fíjate bien en las salidas

no poner [hash] a las fuentes, rara vez cambiarán.

esto de las minificaciones del css y del js deberíamos ponerlo en producción

Según la documentación, el terser viene incluido en webpack 5, por lo que la configuración que hemos puesto de optimización para terser es más simple si cabe

module.exports = {
... 
optimization: {
    minimize: true
  }
}
Sin embargo si usas esto
optimization: {
        minimize: true,
        minimizer: [
            new CssMinimizerPlugin(),
            <!-- new TerserPlugin() -->
        ]
    }
lo que pasaría es que te minimiza el css pero no el js


## Alias de urls
dentro de 

--- webpack.config.js ---
...
resolve: {
        extensions: ['.js'],
        alias: {
            '@services': path.resolve(__dirname, 'src/services'),
            '@templates': path.resolve(__dirname, 'src/templates'),
            '@styles': path.resolve(__dirname, 'src/styles'),
            '@images': path.resolve(__dirname, 'src/assets/images'),
            '@fonts': path.resolve(__dirname, 'src/assets/fonts'),
        }
    },
--- ---
se crean los alias que necesitemos, y después en nuestro código llamamos a los alias en vez de a las urls.

// REMEMBER siempre usa rutas relatiavs a la raiz del proyecto a´s funcionará para el build


## Variables de entorno

> npm install dotenv-webpack -D

--- webpack.config.js --- 
  ...
  const DotEnv = require('dotenv-webpack');
  ...
  plugins: [
          ...
          new DotEnv()    ]
--- ---
--- .env ---
  API=https://randomuser.me/api/
--- ---
--- .env.example ---
API // AQUI se definen las variables pero sin rellenar .env no sube al repositorio, se incluye en .gitignore
--- ---

y en el archivo donde se usa la variable definida en .env 
la usaremos de esta forma:

process.env.API


## Modo desarrollo | Modo producción

crearemos dos ficheros diferenciados de configuración de webpack

--- webpack.config.dev.js ---
--- webpack.config.prdo.js ---

y en el package.json cambiamos los scripts

"scripts": {
        "dev": "npm run clean && npx webpack --mode development",
        "build": "npm run clean && npx webpack --mode production",
        "clean": "rimraf ./dist"
    },
por esto otro
"scripts": {
        "dev": "npm run clean && npx webpack --config webpack.coonfig.dev.js",
        "build": "npm run clean && npx webpack --config webpack.coonfig.prod.js",
        "clean": "rimraf ./dist"
    },

## Limpieza del proyecto
> npm install clean-webpack-plugin
no es necesario porque hemos instalado rimraf

const { CleanWebpackPlugin } = require('clean-webpack-plugin');

...

## Compilación automática modo watch

--- webpack.config.dev.js ---
...
watch: true
--- ---

otra forma sería en el package.json agregar esto al script --watch

-- package.json ---
"scripts": {
        "dev": "npm run clean && npx webpack --watch --config webpack.config.dev.js",
        "build": "npm run clean && npx webpack --config webpack.config.prod.js",
        "clean": "rimraf ./dist"
    },
--- ---


## Emojis en los commits

podemos instalar gitmoji para los commits de git que da mejor sensación visual a los mensajes de git

npm i -g gitmoji-cli

luego puedes poner esto en la parte de arriba del RAEDME.md
<a href="https://gitmoji.dev">
  <img src="https://img.shields.io/badge/gitmoji-%20😜%20😍-FFDD67.svg?style=flat-square" alt="Gitmoji">
</a>

cuando vayas a hacer un commit pon en la línea de comandos esto:
> git add .
> gitmoji --commit
 
 te saldrá un menú y selccionas el emoj ique quieras, para luego poder hacer un push

> push origin <rama>

puedes usar en los -m del commit este tipo de emojis con :emoji:

para verlos puedes acudir a su web o excribir en consola
> gitmoji -l

 🎨 - :art: - Improve structure / format of the code.
⚡️ - :zap: - Improve performance.
🔥 - :fire: - Remove code or files.
🐛 - :bug: - Fix a bug.
🚑️ - :ambulance: - Critical hotfix.
✨ - :sparkles: - Introduce new features.
📝 - :memo: - Add or update documentation.
🚀 - :rocket: - Deploy stuff.
💄 - :lipstick: - Add or update the UI and style files.
🎉 - :tada: - Begin a project.
✅ - :white_check_mark: - Add, update, or pass tests.
🔒️ - :lock: - Fix security issues.
🔐 - :closed_lock_with_key: - Add or update secrets.
🔖 - :bookmark: - Release / Version tags.
🚨 - :rotating_light: - Fix compiler / linter warnings.
🚧 - :construction: - Work in progress.
💚 - :green_heart: - Fix CI Build.
⬇️ - :arrow_down: - Downgrade dependencies.
⬆️ - :arrow_up: - Upgrade dependencies.
📌 - :pushpin: - Pin dependencies to specific versions.
👷 - :construction_worker: - Add or update CI build system.
📈 - :chart_with_upwards_trend: - Add or update analytics or track code.
♻️ - :recycle: - Refactor code.
➕ - :heavy_plus_sign: - Add a dependency.
➖ - :heavy_minus_sign: - Remove a dependency.
🔧 - :wrench: - Add or update configuration files.
🔨 - :hammer: - Add or update development scripts.
🌐 - :globe_with_meridians: - Internationalization and localization.
✏️ - :pencil2: - Fix typos.
💩 - :poop: - Write bad code that needs to be improved.
⏪️ - :rewind: - Revert changes.
🔀 - :twisted_rightwards_arrows: - Merge branches.
📦️ - :package: - Add or update compiled files or packages.
👽️ - :alien: - Update code due to external API changes.
🚚 - :truck: - Move or rename resources (e.g.: files, paths, routes).
📄 - :page_facing_up: - Add or update license.
💥 - :boom: - Introduce breaking changes.
🍱 - :bento: - Add or update assets.
♿️ - :wheelchair: - Improve accessibility.
💡 - :bulb: - Add or update comments in source code.
🍻 - :beers: - Write code drunkenly.
💬 - :speech_balloon: - Add or update text and literals.
🗃️ -  :card_file_box: - Perform database related changes.
🔊 - :loud_sound: - Add or update logs.
🔇 - :mute: - Remove logs.
👥 - :busts_in_silhouette: - Add or update contributor(s).
🚸 - :children_crossing: - Improve user experience / usability.
🏗️ -  :building_construction: - Make architectural changes.
📱 - :iphone: - Work on responsive design.
🤡 - :clown_face: - Mock things.
🥚 - :egg: - Add or update an easter egg.
🙈 - :see_no_evil: - Add or update a .gitignore file.
📸 - :camera_flash: - Add or update snapshots.
⚗️ - :alembic: - Perform experiments.
🔍️ - :mag: - Improve SEO.
🏷️ -  :label: - Add or update types.
🌱 - :seedling: - Add or update seed files.
🚩 - :triangular_flag_on_post: - Add, update, or remove feature flags.
🥅 - :goal_net: - Catch errors.
💫 - :dizzy: - Add or update animations and transitions.
🗑️ -  :wastebasket: - Deprecate code that needs to be cleaned up.
🛂 - :passport_control: - Work on code related to authorization, roles and permissions.
🩹 -  :adhesive_bandage: - Simple fix for a non-critical issue.
🧐 - :monocle_face: - Data exploration/inspection.
⚰️ - :coffin: - Remove dead code.
🧪 -  :test_tube: - Add a failing test.
👔 - :necktie: - Add or update business logic.
🩺 -  :stethoscope: - Add or update healthcheck.
🧱 -  :bricks: - Infrastructure related changes.
🧑‍💻 - :technologist: - Improve developer experience.
💸 - :money_with_wings: - Add sponsorships or money related infrastructure.
🧵 -  :thread: - Add or update code related to multithreading or concurrency.
🦺 -  :safety_vest: - Add or update code related to validation.

ahí los tienes todos


## Automatic Netlify Deploiment and Create .env variables in server

Lo primero que debemos hacer es crear una cuenta en Netlify. Lo siguiente es crear un archivo en la raíz del proyecto llamado netlify.toml el cual va a llevar la siguiente configuración:

## **netlify.toml**
[build]
    publish = "dist" // ¿cual va a ser la carpeta a publicar?
    command = "npm run build" // Comando a ejecutar para el deploy


Para el siguiente paso debemos ya tener nuestro repositorio en algún servicio de la nube, como Github. Ahora, vamos a la pagina de Netlify para crear el nuevo sitio.

Crear nuevo sitio → Seleccionar nube (Github) → Elegir repositorio y rama a subir → Deploy

Apartir de ahora Netlify nos levanta el servidor de manera gratuita, este proceso puede ser lento ya que es un servicio gratuito. En el summary de nuestro deploy podemos ver el log del build donde podriamos ver los errores presentes.

En el proyecto actual, al hacer uso de una variable de entorno, necesitamos realizar la siguiente configuración para que Netlify pueda trabajar con ella: Creamos una carpeta llamada scripts/ y adentro de esta carpeta vamos a crear un archivo llamado create-env.js. En este archivo vamos a colocar este código

// **create-env.js**

const fs = require('fs'); // fs = file system

// fs.writeFileSync("path", `argumento a crear`);
fs.writeFileSync("./.env", `API=${process.env.API}\n`);

Pero ¿de dónde vamos a obtener/setear el process.env? Lo vamos a asignar en netlify, en nuestro deploy vamos a buscar la Sección de Enviroment → Enviroment Variables → Edit Variables. En nuestro caso la llamaremos API y asignaremos el valor de nuestro API: https://randomuser.me/api/

Ahora, debemos ejecutar este script antes de ejecutar el comando de build, para que sea enviado a netlify. Vamos a nuestro package.json y vamos a modificar el script build de la siguiente manera:

"scripts": {
    ...
    "build:server": "node ./scripts/create-env.js && npx webpack --config webpack.config.prod.js",
  }

⚠️Cuidado al anidar la ejecución de scripts, porque si el primero en ejecutarse lo hace como demonio, puede causar un problema ya que no va a ejecutarse el segundo script.

> npm run build:server

⚠️ se ejecuta solo en netlify
⚠️ si lo ejecutas en local borrará la variable de entorno.

Luego de todos estos cambios debemos empujarlos a la nube ✌🏻


## devServer en modo development

> npm install -D webpack-dev-server

--- webpack.config.dev.js ---
...
    devServer: {
        static: path.join(__dirname, 'dist'),     // que directorio debe mirar
        compress: true,                           // debe comprimirlo o no
        historyApiFallback: true,                 // habilita el history del navegador
        port: 8080,                               // puerto donde se ejecutará
        open: true                                // lo abre
    }
--- ---
--- package.json ---
"scripts": {
        // limpia y crea el dist en modo development
        "dev": "npm run clean && npx webpack --config webpack.config.dev.js",

        // esta pendiente de cambios cuando ejecute el dev
        "watch": "npm run dev -- --watch",
        
        // servidor local en modo desarrollo,  crea el dist a partir de lo que existe en src y lo sirve, además automáticamente está en modo watch, sobre el src, por tanto los cambios se reflejan automáticamente, es como el nodemon.
        "start:dev": "npm run dev && webpack serve --config webpack.config.dev.js",

        // construye el dist en modo producción
        "build": "npm run clean && npx webpack --config webpack.config.prod.js",

        // construye el dist en modo produccion pero solo usarlo en netlify, porque en local borra las variables de .env 
        "build:server": "node ./scripts/create-env.js && npx webpack --config webpack.config.prod.js",

        // este limpia y es usado antes de crear el dist
        "clean": "rimraf ./dist"
    },
--- ---


## Analyzando el proyecto

> npm install webpack-bundle-analyzer -D

--- webpack.config.dev.js ---
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer');

plugins:[
  ...
  new BundleAnalyzerPlugin();
--- ---

> npx webpack --profile --json > stats.json      // lo arranca y el json del informe lo pasa a ese archivo

para ver el json generado en una web amigable
> npx webpack-bundle-analyzer stats.json

lo haremos en archivos independientes, por no ensuciar 'dev'
sino deberíamos introducirlo dentro del archivo de configuración:

  // new BundleAnalyzerPlugin()   // sin configuracion
  // new BundleAnalyzerPlugin({   // con configuración
  //     analyzerMode: 'static',  //para que lo haga sólo al momento de hacer el build , posibles valores: 'static' | 'sever' | 'json' | 'disabled'
  //     openAnalyzer: true,
  //     analyzerPort: 8888 // por defecto 8888
  // })

para este cometido hemos habilitado dos scripts que se deben ejecutar uno a continuación del otro, el primero genera un json y el segundo muestra el json en una web


## webpack devTools map

--- webpack.config.dev.js ---
devtool: 'source-map',       // permite generar un mapa del código en formato json
--- ---

> npm run dev

dentro del dist sería main.<chunk>.js.map 
gracias al map podsemos servir la app ne development y ver en el navegador, el index.js y acceder a partes del código inentendibles para u nser humano y te indica donde se genera realmente ese código.



## Instalación y configuración para REACT
ver el proyecto 03 webpack y react

