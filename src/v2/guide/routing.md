---
title: Enrutamiento
type: guide
order: 501
---

## Enrutamiento Oficial

Para la mayoria de las Aplicaciones de una Página, se recomienda utilizar la librería oficial [vue-router](https://github.com/vuejs/vue-router). Para más detalles, consulta la [documentación](https://router.vuejs.org/) de vue-router.

## Enrutamiento simple desde zero

Si solamente necesistas enrutamiento muy sencillo y no deseas incluir una librería de enrutamiento completa, lo puedes hacer de la siguiente manera:

``` js
const NoEncontrada = { template: '<p>Página no encontrada</p>' }
const Inicio = { template: '<p>Página inicio</p>' }
const Conocenos = { template: '<p>Página conócenos</p>' }

const rutas = {
  '/': Inicio,
  '/conocenos': Conocenos
}

new Vue({
  el: '#app',
  data: {
    rutaActual: window.location.pathname
  },
  computed: {
    ComponenteActual () {
      return routes[this.rutaActual] || NoEncontrada
    }
  },
  render (h) { return h(this.ComponenteActual) }
})
```

Junto con la API de Historia de HTML5, puede crear un enrutador del lado del cliente básico pero completamente funcional. Para verlo en práctica, revise [este ejemplo](https://github.com/chrisvfritz/vue-2.0-simple-routing-example).

## Integrando enrutadores de terceros

Si existe un enrutador de terceros que preferiría utilizar, tales como [Page.js](https://github.com/visionmedia/page.js) o [Director](https://github.com/flatiron/director), su integración es [similarmente sencilla](https://github.com/chrisvfritz/vue-2.0-simple-routing-example/compare/master...pagejs). Aquí está un [ejemplo completo](https://github.com/chrisvfritz/vue-2.0-simple-routing-example/tree/pagejs) utilizando Page.js.
