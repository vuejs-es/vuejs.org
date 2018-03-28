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
const NotFound = { template: '<p>Page not found</p>' }
const Home = { template: '<p>home page</p>' }
const About = { template: '<p>about page</p>' }

const routes = {
  '/': Home,
  '/about': About
}

new Vue({
  el: '#app',
  data: {
    currentRoute: window.location.pathname
  },
  computed: {
    ViewComponent () {
      return routes[this.currentRoute] || NotFound
    }
  },
  render (h) { return h(this.ViewComponent) }
})
```

Junto con la API de Historia de HTML5, puede crear un enrutador del lado del cliente básico pero completamente funcional. Para verlo en práctica, revise [este ejemplo](https://github.com/chrisvfritz/vue-2.0-simple-routing-example).

## Integrando enrutadores de terceros

Si existe un enrutador de terceros que preferiría utilizar, tales como [Page.js](https://github.com/visionmedia/page.js) o [Director](https://github.com/flatiron/director), su integración es [similarmente sencilla](https://github.com/chrisvfritz/vue-2.0-simple-routing-example/compare/master...pagejs). Aquí está un [ejemplo completo](https://github.com/chrisvfritz/vue-2.0-simple-routing-example/tree/pagejs) utilizando Page.js.
