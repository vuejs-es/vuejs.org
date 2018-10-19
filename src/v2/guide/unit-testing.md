---
title: Unit tests
type: guide
order: 403
---

## Configuración y Herramientas

Cualquier solución compatible con construcción basada en módulos funcionará, pero si desea una recomendación específica pruebe el ejecutor de pruebas [Karma](http://karma-runner.github.io). Tiene una gran cantidad de plugins, incluido soporte para [Webpack](https://github.com/webpack/karma-webpack) y [Browserify](https://github.com/Nikku/karma-browserify). Para detalles en la configuración por favor revise la documentación respectiva de cada proyecto. Estos ejemplos de configuración para Karma en [Webpack](https://github.com/vuejs-templates/webpack/blob/master/template/test/unit/karma.conf.js) y [Browserify](https://github.com/vuejs-templates/browserify/blob/master/template/karma.conf.js) le ayudarán a comenzar.

## Tests Simples

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

Entonces importe las opciones del component junto con Vue, y está listo para hacer afirmaciones comunes:

``` js
// Importe Vue y el componente a testear
import Vue from 'vue'
import MyComponent from 'path/to/MyComponent.vue'

// Éstas son algunos tests con Jasmine 2.0, pero puede usar
// cualquier ejecutor de pruebas y librería de assert de su preferencia
describe('MyComponent', () => {
  // Inspeccione las opciones del componente
  it('has a created hook', () => {
    expect(typeof MyComponent.created).toBe('function')
  })

  // Evalue los resultados de las funciones en
  // las opciones del componente
  it('sets the correct default data', () => {
    expect(typeof MyComponent.data).toBe('function')
    const defaultData = MyComponent.data()
    expect(defaultData.message).toBe('hello!')
  })

  // Inspeccione la instancia del component después del "mount"
  it('correctly sets the message when created', () => {
    const vm = new Vue(MyComponent).$mount()
    expect(vm.message).toBe('bye!')
  })

  // Monte una instancia e inspeccione la salida renderizada
  it('renders the correct message', () => {
    const Ctor = Vue.extend(MyComponent)
    const vm = new Ctor().$mount()
    expect(vm.$el.textContent).toBe('bye!')
  })
})
```

## Escribiendo componentes testeables

La salida renderizada de un componente es determinada principlamente por las propiedades que recibe. Si la salida renderizada de un componente depende solamente de sus propiedades, éste es bastante sencillo de probar, de manera similar a lo que sería comprobar el valor devuelto de una función pura con diferentes argumentos. Veamos un ejemplo simplificado:

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

Puede probar la salida renderizada con diferentes _props_ utilizando la opción `propsData`:

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

## Probando actualizaciones asíncronas

Dado que Vue [realiza actualizaciones del DOM asíncronamente](reactivity.html#Async-Update-Queue), aserciones tras actualizaciones del DOM causadas por el cambio del estado tendrán que ser hechas en un _callback_ de `Vue.nextTick`:

``` js
// Inspeciona el HTML generado despues de la actualización del estado
it('updates the rendered message when vm.message updates', done => {
  const vm = new Vue(MyComponent).$mount()
  vm.message = 'foo'

  // Espera un "tick" después del cambio del estado antes de asertar las actualizaciones del DOM
  Vue.nextTick(() => {
    expect(vm.$el.textContent).toBe('foo')
    done()
  })
})
```

Estamos planeando en trabajar en una colección de utilidades de tests comunes que harán más fácil renderizar componentes con diferentes restricciones (p. ej. renderizado superficial que ignora componentes hijos) y comprobar su salida.