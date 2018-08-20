---
title: Directivas personalizadas
type: guide
order: 302
---

## Intro

Además del grupo de directivas que viene por defecto en core (`v-model` y `v-show`), Vue también permite registrar sus propias directivas personalizadas. Fíjese que en Vue 2.0, la forma de principal de abstracción y reutilización de código son los elementos - sin embargo habrá casos donde necesite accesso DOM de nivel bajo a elementos comúnes, y es aquí donde las directivas personalizadas siguen siendo útiles. Un ejemplo sería enfocar un elemento de campo, como este:

{% raw %}
<div id="simplest-directive-example" class="demo">
  <input v-focus>
</div>
<script>
Vue.directive('focus', {
  inserted: function (el) {
    el.focus()
  }
})
new Vue({
  el: '#simplest-directive-example'
})
</script>
{% endraw %}

Cuando la página carga, el elemento se enfoca (nota: autofocus no funciona en Safari mobile). De hecho, si no clickaste en ningún otro sitio desde que visitaste esta página, el elemento de campo debería estar enfocado. Vamos a construir la directiva que permite esto:

``` js
// Register a global custom directive called v-focus
Vue.directive('focus', {
  // When the bound element is inserted into the DOM...
  inserted: function (el) {
    // Focus the element
    el.focus()
  }
})
```

Si prefiere registrar una directiva localmente, componentes también aceptan una opción `directives`:

``` js
directives: {
  focus: {
    // directive definition
    inserted: function (el) {
      el.focus()
    }
  }
}
```

Ahora en una plantilla, puede usar el nuevo atributo `v-focus` sobre cualquier elemento, como aquí:

``` html
<input v-focus>
```

## Hook Functions

Un objecto de definición de directiva puede ofrecer varias funciones hook (todas opcionales)

- `bind`: llamada sólo una vez, cuando la directiva se enlaza por primera vez al elemento. Aquí es donde puede realizar el proceso de inicialización.

- `inserted`: llamada cuando el elemente enlazado ha sido añadido al nodo padre (esto solo garantiza la presencia de un nodo padre, no necesariamente in-document)

- `update`: llamada cuando el VNode contenido en el componente ha sido actualizado, __pero posiblemente antes que sus elementos subordinados hayan sido actualizados__. El valor de la directiva puede haber cambiado o no, pero puede saltar actualizaciones innecesarias si compara el valor nuevo con el valor antiguo del enlaze de datos. (Mire abajo en argumentos de hook)

- `componentUpdated`: llamada después que el VNode del componente contenido __y los VNodes de sus elementos subordinados__ hayan sido actualizados.

- `unbind`: llamada sólo una vez, cuando la directiva se desenlaza del elemento.

Vamos a explorar los argumentos pasados a estos hooks (p.ej. `el`, `binding`, `vnode` y `oldVnode`) en la siguiente sección.  

## Argumentos para hook de directiva

A los Hooks de directiva les pasamos los siguientes argumentos: 

- **el**: El elemento al cual enlaza la directiva. Esto puede ser utilizado para manipular directamente el DOM.
- **binding**: Un objeto que contiene las siguientes propiedades:
  - **name**: El nombre de la directiva sin el prefijo `v-`.
  - **value**: El valor pasado a la directiva. Por ej. en `v-my-directive="1 + 1"`, el valor será `2`.
  - **oldValue**: El valor anterior, sólo disponible `update` y `componentUpdated`. Está disponible independientemente de si el valor cambió o no.
  - **expression**: La expresión del enlaze de datos como un string. Por ej. en `v-my-directive="1 + 1"`, la expresión será `"1 + 1"`.
  - **arg**: El argumento pasado a la directiva, si existe. Por ej. en `v-my-directive:foo`, el argumento sería `"foo"`.
  - **modifiers**: Un objecto que contiene modificadores, si existen. Por ej. en `v-my-directive.foo.bar`, los modificadores serian `{ foo: true, bar: true }`.
- **vnode**: El nodo virtual generado por el compilador de Vue. Vea [VNode API](../api/#VNode-Interface) para más detalles.
- **oldVnode**: El nodo virtual anterior, sólo disponible en los hooks `update` y `componentUpdated`.

<p class="tip">Apart from `el`, you should treat these arguments as read-only and never modify them. If you need to share information across hooks, it is recommended to do so through element's [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset).</p>

An example of a custom directive using some of these properties:

``` html
<div id="hook-arguments-example" v-demo:foo.a.b="message"></div>
```

``` js
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})

new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})
```

{% raw %}
<div id="hook-arguments-example" v-demo:foo.a.b="message" class="demo"></div>
<script>
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})
new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})
</script>
{% endraw %}

## Function Shorthand

In many cases, you may want the same behavior on `bind` and `update`, but don't care about the other hooks. For example:

``` js
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

## Object Literals

If your directive needs multiple values, you can also pass in a JavaScript object literal. Remember, directives can take any valid JavaScript expression.

``` html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

``` js
Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```
