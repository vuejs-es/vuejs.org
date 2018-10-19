---
title: Instalación
type: guide
order: 1
vue_version: 2.4.4
dev_size: "262.63"
min_size: "80.86"
gz_size: "29.40"
ro_gz_size: "20.70"
---

### Nota de compatibilidad

Vue **no** está soportado en IE8 y versiones anteriores, porque usa características de ECMAScript 5 que son irreproducibles en ellos. Sin embargo soporta todos los [navegadores compatibles con ECMAScript 5](http://caniuse.com/#feat=es5).

### Notas de lanzamiento

Se pueden encontrar notas de lanzamiento detalladas para cada versión en [GitHub](https://github.com/vuejs/vue/releases).

## Vue Devtools

Al usar Vuew, recomendamos instalar también los [Vue Devtools](https://github.com/vuejs/vue-devtools#vue-devtools) en su explorador, permitiendole inspeccionar y depurar sus aplicaciones Vuew en una interfaz más amigable.

### Versión independiente

Simplemente descarguela e incluyala con una etiqueta script. `Vue` se registrará como una variable global.

<p class="tip">No use la versión minificada durante el desarrollo. ¡Perderá todas las advertencias para los errores comunes!</p>

<div id="downloads">
<a class="button" href="/js/vue.js" download>Versión de desarrollo</a><span class="light info">Con todas las advertencias y el modo depuración.</span>

<a class="button" href="/js/vue.min.js" download>Versión de producción</a><span class="light info">Advertencias eliminadas, {{gz_size}}kb min+gzip</span>
</div>

### CDN

Recomendado: [unpkg](https://unpkg.com/vue/dist/vue.js), el cual contendrá la última versión tan pronto como haya sido publicada en npm. También puede explorar el código fuente del paquete npm en [unpkg.com/vue/](https://unpkg.com/vue/).

También se encuentra disponible en [jsdelivr](//cdn.jsdelivr.net/vue/{{vue_version}}/vue.js) o [cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.js), pero estos dos servicios pueden tardar algo de tiempo en sincronizar, por lo que la última versión puede no estar disponible todavía.

## NPM

NPM es el método de instalación recomendado cuando se construyen aplicaciones de gran escala con Vue. Se integra bien con empaquetadores de módulos como [Webpack](http://webpack.github.io/) o [Browserify](http://browserify.org/). Vue también provee herramientas complementarias para la creación de [Componentes de un solo archivo](single-file-components.html).

``` bash
# última versión estable
$ npm install vue
```

## CLI

Vue.js provee una [CLI oficial](https://github.com/vuejs/vue-cli) para estructurar rápidamente Aplicaciones de una Sola Página (SPA por sus siglas en inglés). Provee configuraciones _todo-en-uno_ para un flujo de trabajo frontend moderno. Solo toma unos minutos estar preparado para el desarrollo con: recarga en caliente, _lint-on-save_ y versiones listas para producción:

``` bash
# Instale vue-cli
$ npm install --global vue-cli
# Cree un nuevo proyecto usando la plantilla "webpack"
$ vue init webpack my-project
# Instale las dependencias y ¡listo!
$ cd my-project
$ npm install
$ npm run dev
```

<p class="tip">La _CLI_ asume un conocimiento previo de Node.js y las herramientas de trabajo asociadas. Si usted es principiante con Vue o las herramientas de desarrollo front-end, le recomendamos fervientemente leer <a href="./">la guía</a> sin ninguna herramienta de desarrollo previo a usar la _CLI_.</p>

## Explicación de los diferentes builds

En [`dist/` directory of the NPM package](https://cdn.jsdelivr.net/npm/vue/dist/) encontrará muchos builds diferentes de Vuew.js. Aquí tiene un resumen explicando las diferencias:

| | UMD | CommonJS | ES Module |
| --- | --- | --- | --- |
| **Full** | vue.js | vue.common.js | vue.esm.js |
| **Runtime-only** | vue.runtime.js | vue.runtime.common.js | vue.runtime.esm.js |
| **Full (production)** | vue.min.js | - | - |
| **Runtime-only (production)** | vue.runtime.min.js | - | - |

### Terms

- **Full**: builds that contains both the compiler and the runtime.

- **Compiler**: code that is responsible for compiling template strings into JavaScript render functions.

- **Runtime**: code that is responsible for creating Vue instances, rendering and patching virtual DOM, etc. Basically everything minus the compiler.

- **[UMD](https://github.com/umdjs/umd)**: UMD builds can be used directly in the browser via a `<script>` tag. The default file from jsDelivr CDN at [https://cdn.jsdelivr.net/npm/vue](https://cdn.jsdelivr.net/npm/vue) is the Runtime + Compiler UMD build (`vue.js`).

- **[CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1)**: CommonJS builds are intended for use with older bundlers like [browserify](http://browserify.org/) or [webpack 1](https://webpack.github.io). The default file for these bundlers (`pkg.main`) is the Runtime only CommonJS build (`vue.runtime.common.js`).

- **[ES Module](http://exploringjs.com/es6/ch_modules.html)**: ES module builds are intended for use with modern bundlers like [webpack 2](https://webpack.js.org) or [rollup](https://rollupjs.org/). The default file for these bundlers (`pkg.module`) is the Runtime only ES Module build (`vue.runtime.esm.js`).

### Runtime + Compiler vs. Runtime-only

Si necesita compilar plantillas en el cliente (p.ej. pasando un string a la opción `template` ó montando un elemento usando su HTML in-DOM como plantilla) necesitará el compilador y un build completo:

``` js
// requiere el compilador
new Vue({
  template: '<div>{{ hi }}</div>'
})

// esto no lo requiere
new Vue({
  render (h) {
    return h('div', this.hi)
  }
})
```

cuando use `vue-loader` ó `vueify`, plantillas dentro de archivos `*.vue` son precompilados a Javascript a la hora del build. No necesita realmente el compilador en el paquete final y peude de esa manera usar el paquete __runtime-only__.

Debido a que los paquetes __runtime-only__ son aprox. 30% más ligeros que los paquetes __full-build__ debería usarlo siempre que pueda. Si aún asi quiere usar __full-build__ necesita configurar un alias en su bundler:

#### Webpack

``` js
module.exports = {
  // ...
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js' // 'vue/dist/vue.common.js' para webpack 1
    }
  }
}
```

#### Rollup

``` js
const alias = require('rollup-plugin-alias')

rollup({
  // ...
  plugins: [
    alias({
      'vue': 'vue/dist/vue.esm.js'
    })
  ]
})
```

#### Browserify

Añade a tu `package.json`:

``` js
{
  // ...
  "browser": {
    "vue": "vue/dist/vue.common.js"
  }
}
```

### Development vs. Production Mode

Development/production modes are hard-coded for the UMD builds: the un-minified files are for development, and the minified files are for production.

CommonJS and ES Module builds are intended for bundlers, therefore we don't provide minified versions for them. You will be responsible for minifying the final bundle yourself.

CommonJS and ES Module builds also preserve raw checks for `process.env.NODE_ENV` to determine the mode they should run in. You should use appropriate bundler configurations to replace these environment variables in order to control which mode Vue will run in. Replacing `process.env.NODE_ENV` with string literals also allows minifiers like UglifyJS to completely drop the development-only code blocks, reducing final file size.

#### Webpack

Use Webpack [DefinePlugin](https://webpack.js.org/plugins/define-plugin/):

``` js
var webpack = require('webpack')

module.exports = {
  // ...
  plugins: [
    // ...
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: JSON.stringify('production')
      }
    })
  ]
}
```

#### Rollup

Use [rollup-plugin-replace](https://github.com/rollup/rollup-plugin-replace):

``` js
const replace = require('rollup-plugin-replace')

rollup({
  // ...
  plugins: [
    replace({
      'process.env.NODE_ENV': JSON.stringify('production')
    })
  ]
}).then(...)
```

#### Browserify

Aplique una transformación global [envify](https://github.com/hughsk/envify) a su bundle.

``` bash
NODE_ENV=production browserify -g envify -e main.js | uglifyjs -c -m > build.js
```

Visite tambien [Consejos de despliegue en producción](deployment.html).

### CSP environments

Some environments, such as Google Chrome Apps, enforce Content Security Policy (CSP), which prohibits the use of `new Function()` for evaluating expressions. The full build depends on this feature to compile templates, so is unusable in these environments.

On the other hand, the runtime-only build is fully CSP-compliant. When using the runtime-only build with [Webpack + vue-loader](https://github.com/vuejs-templates/webpack-simple) or [Browserify + vueify](https://github.com/vuejs-templates/browserify-simple), your templates will be precompiled into `render` functions which work perfectly in CSP environments.

## Dev Build

**Important**: the built files in GitHub's `/dist` folder are only checked-in during releases. To use Vue from the latest source code on GitHub, you will have to build it yourself!

``` bash
git clone https://github.com/vuejs/vue.git node_modules/vue
cd node_modules/vue
npm install
npm run build
```

## Bower

Only UMD builds are available from Bower.

``` bash
# latest stable
$ bower install vue
```

## AMD Module Loaders

All UMD builds can be used directly as an AMD module.
