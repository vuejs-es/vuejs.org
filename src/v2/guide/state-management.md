---
title: State Management
type: guide
order: 502
---

## Implementación oficial similar a Flux

Las aplicaciones de gran tamaño suelen ser cada vez más complejas, debido a la existencia de múltiples estados dispersos en muchos componentes y a las interacciones entre ellos. Para resolver este problema, Vue ofrece [vuex](https://github.com/vuejs/vuex): nuestra propia biblioteca de gestión de estados inspirada en Elm. Incluso se integra en [vue-devtools](https://github.com/vuejs/vue-devtools), proporcionando un acceso de configuración cero al time travel.

### Informacion para desarrolladores de React

Si vienes de React, te estarás preguntando cómo se compara vuex con [redux](https://github.com/reactjs/redux), la implementación de Flux más popular en ese ecosistema. Redux es realmente agnóstico en cuanto a la capa de visión, por lo que puede utilizarse fácilmente con Vue a través de [enlaces simples](https://github.com/egoist/revue). Vuex es diferente en que sabe que está en una aplicación Vue. Esto le permite integrarse mejor con Vue, ofreciendo una API más intuitiva y una experiencia de desarrollo mejorada.

## Administración de estados sencilla desde el principio

A menudo se pasa por alto que la fuente de la verdad en las aplicaciones Vue es el objeto `data` en bruto - una instancia Vue sólo permite el acceso a los proxys. Por lo tanto, si tienes una pieza de estado que debería ser compartida por múltiples instancias, puedes compartirla por identidad:

``` js
const sourceOfTruth = {}

const vmA = new Vue({
  data: sourceOfTruth
})

const vmB = new Vue({
  data: sourceOfTruth
})
```

Ahora, siempre que `sourceOfTruth` esté mutado, tanto `vmA` como `vmB` actualizarán sus vistas automáticamente. Los subcomponentes dentro de cada una de estas instancias también tendrían acceso a través de `this.$root.$data`. Ahora tenemos una sola fuente de verdad, pero depurar sería una pesadilla. Cualquier dato puede ser cambiado por cualquier parte de nuestra aplicación en cualquier momento, sin dejar rastro.

Para ayudar a resolver este problema, podemos adoptar un **store pattern**:

``` js
var store = {
  debug: true,
  state: {
    message: 'Hello!'
  },
  setMessageAction (newValue) {
    if (this.debug) console.log('setMessageAction triggered with', newValue)
    this.state.message = newValue
  },
  clearMessageAction () {
    if (this.debug) console.log('clearMessageAction triggered')
    this.state.message = ''
  }
}
```

Note que todas las acciones que mutan el estado de la store son puestas dentro de la misma store. Este tipo de gestión centralizada del estado facilita la comprensión de qué tipo de mutaciones podrían producirse y cómo se desencadenan. Ahora, cuando algo va mal, también tendremos un registro de lo que ocurrió antes del error.

Además, cada instancia/componente puede poseer y gestionar su propio estado privado:

``` js
var vmA = new Vue({
  data: {
    privateState: {},
    sharedState: store.state
  }
})

var vmB = new Vue({
  data: {
    privateState: {},
    sharedState: store.state
  }
})
```

![State Management](/images/state.png)

<p class="tip">Es importante tener en cuenta que nunca debe reemplazar el objeto de estado original en sus acciones - los componentes y el store necesitan compartir la referencia al mismo objeto para que se observen las mutaciones.</p>

A medida que continuamos desarrollando la convención es que nunca se permita que los componentes muten directamente el estado que pertenecen a un store, sino que deben enviar eventos que notifiquen al store para realizar acciones, finalmente llegamos a la arquitectura [Flux](https://facebook.github.io/flux/). El beneficio de esta convención es que podemos registrar todas las mutaciones de estado que ocurren en el store e implemenentar funciones de depuración avanzada como registros de mutaciones, instantáneas y re-rolls de historial / time travel.

Esto nos lleva de vuelta a [vuex](https://github.com/vuejs/vuex), así que si has leído hasta aquí, ¡probablemente sea el momento de probarlo!
