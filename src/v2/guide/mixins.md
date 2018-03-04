---
title: Mixins
type: guide
order: 301
---

## Lo esencial

_Mixins_ son una manera maleable de repartir funcionalidades reutilizables para los componentes Vue. Un objeto _mixin_ puede contener cualquier opción de componente. Cuando un componente usa un mixin, todas las opciones en el Mixin se "mezclarán" en las propias opciones del componente.

Ejemplo:

``` js
// define un objeto mixin
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

// define un componente que usa este mixin
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // => "hello from mixin!"
```

## Opción de fusión

Cuando un mixin y el componente en sí tienen opciones superpuestas, se "fusionarán" utilizando las estrategias apropiadas. Por ejemplo, _hook functions_ con el mismo nombre se fusionarán en una matriz para que se invoquen todas. Además, los _mixin hooks_ se ejecutarán **antes** que los propios _hooks_ del componente:

``` js
var mixin = {
  created: function () {
    console.log('mixin hook ejecutado')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('component hook ejecutado')
  }
})

// => "mixin hook ejecutado"
// => "component hook ejecutado"
```

Las opciones que esperan valores de objeto, por ejemplo `métodos`, 
`componentes` y `directivas`, se fusionarán en un mismo objeto. 
Las opciones del componente tendrán prioridad cuando haya claves conflictivas en estos objetos:

``` js
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('desde el mixin')
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
      console.log('desde componente')
    }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "desde componente"
```

Tenga en cuenta que las mismas estrategias de fusión se utilizan en `Vue.extend()`.

## Mixin Global

También puede aplicar un _mixin_ globalmente. ¡Úsalo con precaución! Una vez el _mixin_ es aplicado globalmente, afectará a **cada** instancia de Vue creada posteriormente. Cuando es usado correctamente, puede ser aplicado para inyectar lógica de procesamiento de opciones personalizadas:

``` js
// injecta un manejador para la opción personalizada `myOption`
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hola!'
})
// => "hola!"
```

<p class="tip">Use los mixins globales de forma escasa y cuidadosa, ya que estos afectan a todas las instancias Vue creadas, incluidos los componentes de terceros. En la mayoría de los casos, solo debe usarlo para el manejo de opciones personalizadas como se demuestra en el ejemplo anterior. También es una buena idea empaquetarlos como [Plugins](plugins.html) para evitar aplicaciones duplicadas.</p>

## Estrategias de fusión de opciones personalizadas

Cuando se fusionan las opciones personalizadas, utilizan la estrategia predeterminada que sobrescribe el valor existente. Si desea que una opción personalizada se fusione mediante lógica personalizada, debe adjuntar una función a `Vue.config.optionMergeStrategies`:

``` js
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // return mergedVal
}
```

Para la mayoría de opciones basadas en objetos, puede usar la misma estrategia utilizada por `methods`:

``` js
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods
```

Un ejemplo más avanzado se puede ver en la estrategia de fusión de [Vuex](https://github.com/vuejs/vuex) 1.x:

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
