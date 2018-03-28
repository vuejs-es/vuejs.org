---
title: Pruebas Unitarias
type: guide
order: 403
---

## Configuración y Herramientas

Cualquier solución compatible con construcción basada en módulos funciona, pero si desea una recomendación en específico prueba el ejecutor de pruebas [Karma](http://karma-runner.github.io). Éste tiene una gran cantidad de plugins, incluido soporte para [Webpack](https://github.com/webpack/karma-webpack) y [Browserify](https://github.com/Nikku/karma-browserify). Para detalles en la configuración por favor revise la documentación respectiva de cada proyecto. Estos ejemplos de configuración para Karma en [Webpack](https://github.com/vuejs-templates/webpack/blob/master/template/test/unit/karma.conf.js) y [Browserify](https://github.com/vuejs-templates/browserify/blob/master/template/karma.conf.js) le ayudaran a comenzar.

## Pruebas Simples

No es necesario hacer algo en especial para poder probar sus componentes. Exporte las opciones tal cual: 

``` html
<template>
  <span>{{ message }}</span>
</template>

<script>
  export default {
    data () {
      return {
        message: 'hello!'
      }
    },
    created () {
      this.message = 'bye!'
    }
  }
</script>
```

Enseguida importe las opciones del component junto con Vue, y está listo para crear sus pruebas:

``` js
// Importe Vue y el componente a probar
import Vue from 'vue'
import MyComponent from 'path/to/MyComponent.vue'

// Éstas son algunas pruebas con Jasmine 2.0, pero puede usar
// cualquier ejecutor de pruebas y librería de aserción de su preferencia
describe('MyComponent', () => {
  // Inspecciona las opciones puras del componente
  it('has a created hook', () => {
    expect(typeof MyComponent.created).toBe('function')
  })

  // Evalu los resultados de las funciones en
  // las opciones puras del componente
  it('sets the correct default data', () => {
    expect(typeof MyComponent.data).toBe('function')
    const defaultData = MyComponent.data()
    expect(defaultData.message).toBe('hello!')
  })

  // Inspeccione la instancia del component en mount
  it('correctly sets the message when created', () => {
    const vm = new Vue(MyComponent).$mount()
    expect(vm.message).toBe('bye!')
  })

  // Monta una instancia e inspeccione la salida renderizada
  it('renders the correct message', () => {
    const Ctor = Vue.extend(MyComponent)
    const vm = new Ctor().$mount()
    expect(vm.$el.textContent).toBe('bye!')
  })
})
```

## Escribiendo Componentes Fáciles de Probar

La salida renderizada de un componente es determinada principlamente por las propiedades que recibe. Si la salida renderizada de un componente depende solamente en sus propias propiedades, éste es bastante sencillo de probar, de la misma manera que lo es el valor regresado de una función pura con diferentes argumentos. Veamos un ejemplo simplificado:

``` html
<template>
  <p>{{ msg }}</p>
</template>

<script>
  export default {
    props: ['msg']
  }
</script>
```

Puede probar la salida renderizada con diferentes ''props'' utilizando la opción `propsData`:

``` js
import Vue from 'vue'
import MyComponent from './MyComponent.vue'

// función de asistencia que monta y regresa el texto renderizado
function getRenderedText (Component, propsData) {
  const Ctor = Vue.extend(Component)
  const vm = new Ctor({ propsData: propsData }).$mount()
  return vm.$el.textContent
}

describe('MyComponent', () => {
  it('renders correctly with different props', () => {
    expect(getRenderedText(MyComponent, {
      msg: 'Hello'
    })).toBe('Hello')

    expect(getRenderedText(MyComponent, {
      msg: 'Bye'
    })).toBe('Bye')
  })
})
```

## Probando Actualizaciones Asíncronas

Dado que Vue [realiza actualizaciones al DOM asincronamente](reactivity.html#Async-Update-Queue), pruebas en actualizaciones del DOM causadas por el cambio del estado tendrán que ser hechas en un callback de `Vue.nextTick`:

``` js
// Inspeciona el HTML generado despues de la actualización del estado
it('updates the rendered message when vm.message updates', done => {
  const vm = new Vue(MyComponent).$mount()
  vm.message = 'foo'

  // Espera un "tick" después del cambio del estado antes de a comprobar las actualizaciones a el DOM
  Vue.nextTick(() => {
    expect(vm.$el.textContent).toBe('foo')
    done()
  })
})
```

Estamos planeando en trabajar en una colección de utilidades de prueba comunes que harán más fácil renderizar componentes con diferentes restricciones (p. ej. renderizado superficial que ignora componentes hijos) y comprobar su salida.
