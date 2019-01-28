---
title: Despliegue en Producción
type: guide
order: 401
---

## Activar modo de producción

Durante el desarrollo, Vue da muchas advertencias para ayudarle con errores y dificultades más comunes. Sin embargo, estas advertencias se vuelven inútiles en la producción e incrementan el tamaño de su aplicación. Además, algunas de estas advertencias tienen pequeños costos de tiempo de ejecución que pueden evitarse en el modo de producción.

### Sin herramientas de compilación

Si está utilizando la compilación completa, es decir, que incluye directamente Vue a través de una etiqueta de script sin una herramienta de compilación, asegúrese de usar la versión minificada (`vue.min.js`) para la producción. Ambas versiones se pueden encontrar en la [Guía de instalación](installation.html#Direct-lt-script-gt-Include).

### Con herramientas de compilación

Cuando utilice una herramienta de compilación como Webpack o Browserify, el modo de producción estará definido por `process.env.NODE_ENV` dentro del código fuente de Vue, y estará en modo de desarrollo por defecto. Ambas herramientas de compilación proporcionan formas de sobrescribir esta variable para habilitar el modo de producción de Vue, y las advertencias serán eliminadas por los minificadores durante la compilación. Todas las plantillas `vue-cli` contienen estas pre configuraciones para usted, pero sería provechoso saber cómo se hace:

#### Webpack

Use el [DefinePlugin](https://webpack.js.org/plugins/define-plugin/) de Webpack para indicar un entorno de producción, de modo que los bloques de advertencia se puedan eliminar automáticamente por UglifyJS durante la minificación. Configuración de ejemplo:

``` js
var webpack = require('webpack')

module.exports = {
  // ...
  plugins: [
    // ...
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: '"production"'
      }
    })
  ]
}
```

#### Browserify

- Ejecute el comando de compilación con la variable de entorno `NODE_ENV` establecida en `"production"`. Esto le dice a `vueify` que no incluya _hot-reload_ ni código relacionado con el desarrollo. 

- Aplique una transformación global [envify](https://github.com/hughsk/envify) a su paquete. Esto permite que el minificador elimine todas las advertencias en el código fuente de Vue rodeado de bloques condicionales de las variables env. Por ejemplo:

  ``` bash
  NODE_ENV=production browserify -g envify -e main.js | uglifyjs -c -m > build.js
  ```

- O, usando [envify](https://github.com/hughsk/envify) con Gulp:

  ``` js
  // Utilice el módulo personalizado envify para especificar variables de entorno
  var envify = require('envify/custom')

  browserify(browserifyOptions)
    .transform(vueify),
    .transform(
      // Requerido para procesar archivos de node_modules
      { global: true },
      envify({ NODE_ENV: 'production' })
    )
    .bundle()
  ```

#### Rollup

Use [rollup-plugin-replace](https://github.com/rollup/rollup-plugin-replace):

``` js
const replace = require('rollup-plugin-replace')

rollup({
  // ...
  plugins: [
    replace({
      'process.env.NODE_ENV': JSON.stringify( 'production' )
    })
  ]
}).then(...)
```

## Pre-compilación de plantillas

Cuando se utilizan plantillas en el DOM o plantillas de cadena de texto en JavaScript, la compilación de _template-to-render-function_ se realiza sobre la marcha. Esto suele ser lo suficientemente rápido en la mayoría de los casos, pero es mejor evitarlo si su aplicación exige mayor rendimiento.

La forma más fácil de pre compilar plantillas es usando [Componentes de un solo archivo](single-file-components.html) - las configuraciones de compilación asociadas realizan automáticamente la precompilación por usted, por lo que el código generado contiene las funciones de renderización ya compiladas en lugar de las plantillas de cadena de texto sin procesar.

Si usa Webpack y prefiere separar los archivos de plantilla y JavaScript, puede usar [vue-template-loader](https://github.com/ktsn/vue-template-loader), que también transforma los archivos de plantilla en funciones de renderizado de JavaScript durante el proceso de compilación.

## Extracción de componentes CSS

Cuando se utilizan componentes de un solo archivo, el CSS interno de los componentes es inyectado dinámicamente con las etiquetas `<style>` a través de JavaScript. Esto tiene un pequeño coste de tiempo de ejecución, y si está utilizando la representación del lado del servidor provocará un "destello de contenido sin estilo". Extraer el CSS de todos los componentes en un mismo archivo evitará este tipo de problemas y también dará como resultado una mejor minimización y almacenamiento en caché del CSS.

Consulte los documentos de la herramienta de compilación respectiva para ver cómo se hace:

- [Webpack + vue-loader](https://vue-loader.vuejs.org/en/configurations/extract-css.html) (la plantilla webpack del `vue-cli` tiene esto preconfigurado)
- [Browserify + vueify](https://github.com/vuejs/vueify#css-extraction)
- [Rollup + rollup-plugin-vue](https://vuejs.github.io/rollup-plugin-vue/#/en/2.3/?id=custom-handler)

## Seguimiento de errores de tiempo de ejecución

Si se produce un error en el tiempo de ejecución durante la renderización de un componente, se pasará a la función de configuración global `Vue.config.errorHandler` si es que así se ha establecido. Podría ser una buena idea aprovechar este enlace junto con un servicio de seguimiento de errores como [Sentry](https://sentry.io), que proporciona [una integración oficial](https://sentry.io/for/vue/) para Vue.
