---
title: Mixins
type: guide
order: 301
---

## Lo esencial

Mixins es una manera maleable de repartir funcionalidades reutilizables para los componentes Vue. Un objeto mixin puede contener cualquier opción de componente. Cuando un componente usa un mixin, todas las opciones en el Mixin se "mezclarán" en las propias opciones del componente.

Ejemplo:

``` js
// define a mixin object
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}

// define a component that uses this mixin
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // => "hello from mixin!"
```

## Opción de fusión

Cuando un mixin y el componente en sí tienen opciones superpuestas, se "fusionarán" utilizando las estrategias apropiadas. Por ejemplo, los enlaces con el mismo nombre se fusionarán en una matriz para que se invoquen todos juntos. Además, los enganches de mixin se denominarán **before** (antes) que los propios enganches del componente:

``` js
var mixin = {
  created: function () {
    console.log('mixin hook called')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('component hook called')
  }
})

// => "mixin hook called"
// => "component hook called"
```

Las opciones que esperan valores de objeto, por ejemplo `methods` (métodos), 
`components`(componentes) y `directives` (directivas), se fusionarán en un mismo objeto. 
Las opciones de componentes tendrán prioridad cuando haya claves conflictivas en estos objetos:

``` js
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('from self')
    }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"
```

Ten en cuenta que las mismas estrategias de fusión también se utilizan en `Vue.extend()`.

## Mixin Global

También puedes aplicar mixin globalmente. ¡Úsalo con precaución! Ya que cuando aplique mixin globalmente, afectará a **every** (cada) instancia de Vue creada posteriormente. Si se usa correctamente, puedes aplicarlo para inyectar lógica de procesamiento para opciones personalizadas:

``` js
// inject a handler for `myOption` custom option
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// => "hello!"
```

<p class="tip">Use los mixins globales de forma escasa y cuidadosa, ya que estos afectan todas las instancias de Vue que haya creado, incluidos los componentes de terceros. En la mayoría de los casos, solo debe usarlo para el manejo de opciones personalizadas como se demuestra en el ejemplo anterior. También es una buena idea enviarlos como [Complementos] (plugins.html) para evitar la aplicación duplicada.</p>

## Estrategias de combinación de opciones personalizadas

Cuando se fusionan las opciones personalizadas, utilizan la estrategia predeterminada que sobrescribe el valor existente. Si desea fusionar una opción personalizada mediante la lógica personalizada, debe adjuntar una función a `Vue.config.optionMergeStrategies`:

``` js
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // return mergedVal
}
```

Para la mayoría de las opciones basadas en objetos, puede usar la misma estrategia utilizada por `methods`:

``` js
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods
```

Aquí se puede encontrar un ejemplo más avanzado en la estrategia de fusión de [Vuex](https://github.com/vuejs/vuex):

``` js
const merge = Vue.config.optionMergeStrategies.computed
Vue.config.optionMergeStrategies.vuex = function (toVal, fromVal) {
  if (!toVal) return fromVal
  if (!fromVal) return toVal
  return {
    getters: merge(toVal.getters, fromVal.getters),
    state: merge(toVal.state, fromVal.state),
    actions: merge(toVal.actions, fromVal.actions)
  }
}
```
