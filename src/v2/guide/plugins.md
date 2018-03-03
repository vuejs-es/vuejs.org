---
title: Plugins
type: guide
order: 304
---

## Escribiendo un Plugin

Los Plugins usualmente añaden funcionalidades de nivel global a Vue. No hay un alcance estrictamente definido para un Plugin: normalmente hay varios tipos de Plugins que usted puede escribir:

1. Agregar algunos métodos o propiedades globales. Por ejemplo [vue-custom-element](https://github.com/karol-f/vue-custom-element)

2. Agregar uno o más recursos globales: directivas / filtros / transiciones, etc. Por ejemplo [vue-touch](https://github.com/vuejs/vue-touch)

3. Agregar algunas opciones de componentes por mixin global. Por Ejemplo [vue-router](https://github.com/vuejs/vue-router)

4. Agregar algunos métodos de instancia adjuntándolos a Vue.prototype.

5. Una biblioteca que proporcione una API propia, mientras que al mismo tiempo introduce una combinación de las anteriores. Por Ejemplo [vue-router](https://github.com/vuejs/vue-router)

Un Plugin Vue.js debería exponer un método `install` (Método de instalación). Se llamará al método con el constructor `Vue` como primer argumento, junto con las posibles opciones:


``` js
MyPlugin.install = function (Vue, options) {
  // 1. add global method or property
  Vue.myGlobalMethod = function () {
    // something logic ...
  }

  // 2. add a global asset
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // something logic ...
    }
    ...
  })

  // 3. inject some component options
  Vue.mixin({
    created: function () {
      // something logic ...
    }
    ...
  })

  // 4. add an instance method
  Vue.prototype.$myMethod = function (methodOptions) {
    // something logic ...
  }
}
```

## Usando un Plugin

Use los Plugins llamando al método global `Vue.use ()`:

``` js
// calls `MyPlugin.install(Vue)`
Vue.use(MyPlugin)
```

Usted puede si lo desea descartar algunas opciones:

``` js
Vue.use(MyPlugin, { someOption: true })
```

`Vue.use` automáticamente lo previene de usar el mismo Plugin más de una vez, por lo que, al llamar varias veces a un mismo Plugin, este se instalará solo una vez.

Algunos Plugins proporcionados por “Vue.js official plugins” como `vue-router`, citan automáticamente a `Vue.use ()` si `Vue` está disponible como una variable global. Sin embargo, en un entorno de módulo como CommonJS, usted siempre debe citar a `Vue.use ()` explícitamente:

``` js
// When using CommonJS via Browserify or Webpack
var Vue = require('vue')
var VueRouter = require('vue-router')

// Don't forget to call this
Vue.use(VueRouter)
```

Consulte [awesome-vue](https://github.com/vuejs/awesome-vue#components--libraries)para obtener una gran colección de plugins y librerías proporcionados por la comunidad.
