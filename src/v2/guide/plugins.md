---
title: Plugins
type: guide
order: 304
---

## Escribiendo un Plugin

Los Plugins usualmente añaden funcionalidades de nivel global a Vue. No hay un alcance estrictamente definido para un Plugin: normalmente hay varios tipos de Plugins que puede escribir:

1. Agregar algunos métodos o propiedades globales. Por ejemplo [vue-custom-element](https://github.com/karol-f/vue-custom-element)

2. Agregar uno o más recursos globales: directivas / filtros / transiciones, etc. Por ejemplo [vue-touch](https://github.com/vuejs/vue-touch)

3. Agregar algunas opciones de componentes por mixin global. Por Ejemplo [vue-router](https://github.com/vuejs/vue-router)

4. Agregar algunos métodos de instancia adjuntándolos a Vue.prototype.

5. Una biblioteca que proporcione una API propia, mientras que al mismo tiempo introduce una combinación de las anteriores. Por Ejemplo [vue-router](https://github.com/vuejs/vue-router)

Un Plugin Vue.js debería exponer un método `instalar` (Método de instalación). Se llamará al método con el constructor `Vue` como primer argumento, junto con las posibles opciones:


``` js
MyPlugin.install = función (opciones de Vue) {
  // 1. agregar método global o de propiedad
  Vue.myGlobalMethod = función () {
    // something logic ...
  }

  // 2. agregar un activo global
  Vue.directive('my-directive', {
    enlazar (el, binding, vnode, oldVnode) {
      // algo lógico ...
    }
    ...
  })

  // 3. inyectar algunas opciones de componentes
  Vue.mixin({
    created: función () {
      // algo lógico ...
    }
    ...
  })

  // 4. agregar un método de instancia
  Vue.prototype.$myMethod = función (methodOptions) {
    // Algo lógico ...
  }
}
```

## Usando un Plugin

Use los Plugins llamando al método global `Vue.use ()`:

``` js
// calls `MyPlugin.install(Vue)`
Vue.use(MyPlugin)
```

Puede si lo desea descartar algunas opciones:

``` js
Vue.use(MyPlugin, { someOption: true })
```

`Vue.use` automáticamente lo previene de usar el mismo Plugin más de una vez, por lo que, al llamar varias veces a un mismo Plugin, este se instalará solo una vez.

Algunos Plugins proporcionados por “Vue.js official plugins” como `vue-router`, citan automáticamente a `Vue.use ()` si `Vue` está disponible como una variable global. Sin embargo, en un módulo como CommonJS, usted siempre debe citar a `Vue.use ()` explícitamente:

``` js
// Cuando usa CommonJS via Browserify o Webpack
var Vue = require('vue')
var VueRouter = require('vue-router')

// No olvide llamarlo
Vue.use(VueRouter)
```

Consulte [awesome-vue](https://github.com/vuejs/awesome-vue#components--libraries)para obtener una gran colección de plugins y librerías proporcionados por la comunidad.
