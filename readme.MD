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
> cd src
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





