---
title: Directivas Personalizadas
type: guide
order: 302
---

## Introducción

Además del conjunto predeterminado de directivas entregadas en su núcleo principal (`v-model` y `v-show`),  Vue también le permite registrar sus propias directivas personalizadas.Tenga en cuenta que en Vue 2.0, la forma principal de reutilización y abstracción de código son los componentes - sin embargo, puede haber casos en los que necesite algún acceso DOM de bajo nivel en elementos simples, y aquí es donde las directivas personalizadas seguirán siendo útiles. Un ejemplo sería centrarse en un elemento de entrada, como éste:

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

Cuando se carga la página, ese elemento es seleccionado (nota: la selección automática no funciona en Safari móvil). De hecho, si no ha hecho clic en nada más desde que visitó esta página, la entrada de arriba debe estar seleccionada. Ahora construyamos la directiva que logre esto:

``` js
// Registrar una directiva personalizada global llamada v-focus
Vue.directive('focus', {
  // Cuando el elemento enlazado se inserta en el DOM
  inserted: function (el) {
    // Selecciona el elemento
    el.focus()
  }
})
```

Si desea registrar una directiva localmente, los componentes también aceptan la opción `directives`:

``` js
directives: {
  focus: {
    // definición de la directiva
    inserted: function (el) {
      el.focus()
    }
  }
}
```

Luego, en una plantilla, puede usar el nuevo atributo `v-focus` en cualquier elemento, como este:

``` html
<input v-focus>
```

## Funciones de hook

Un objeto de definición de directiva puede proporcionar varias funciones de hook (todas opcionales):

- `bind`: se llama sólo una vez, cuando la directiva es vinculada por primera vez al elemento. Aquí es donde se puede realizar el trabajo de preparación inicial.

- `inserted`: se llama cuando el elemento vinculado se ha insertado en su nodo padre (esto sólo garantiza la presencia del nodo padre, no necesariamente en el documento).

- `update`: llamado después de que el VNode del componente contenedor se haya actualizado, __pero posiblemente antes de que sus hijos se hayan actualizado__. El valor de la directiva puede o no haber cambiado, pero puede omitir actualizaciones innecesarias comparando los valores actuales y antiguos del vínculo (ver más adelante sobre los argumentos hook).

- `componentUpdated`: llamado después de que el VNode del componente __y los VNodes de sus hijos__ hayan sido actualizados.

- `unbind`: se llama sólo una vez, cuando la directiva está desvinculada del elemento.

Exploraremos los argumentos enviados a estos hooks (p.e. `el`, `binding`, `vnode`, y `oldVnode`) en la siguiente sección.

## Argumentos de hooks de directiva 

Los hooks de directiva se pasan estos argumentos:

- **el**: El elemento al que está vinculada la directiva. Se puede utilizar para manipular directamente el DOM.
- **binding**: Un objeto que contiene las siguientes propiedades.
  - **name**: El nombre de la directiva, sin el prefijo `v-`.
  - **value**: El valor enviado a la directiva. Por ejemplo, en `v-my-directive="1 + 1"`, el valor sería `2`.
  - **oldValue**: El valor anterior, sólo disponible en `update` y `componentUpdated`. Está disponible independientemente de que el valor haya cambiado o no.
  - **expression**: La expresión de la vinculación como cadena de texto. Por ejemplo en `v-my-directive="1 + 1"`, la expresión sería `"1 + 1"`.
  - **arg**: El argumento enviado a la directiva, si lo hubiera. Por ejemplo en `v-my-directive:foo`, el argumento sería `"foo"`.
  - **modifiers**: Un objeto que contiene modificadores, si los hay. Por ejemplo en `v-my-directive.foo.bar`, el objeto de modificadores sería `{ foo: true, bar: true }`.
- **vnode**: El nodo virtual producido por el compilador de Vue. Consulte la [API de VNode](../api/#VNode-Interface) para más detalles.
- **oldVnode**: El nodo virtual anterior, sólo disponible en los hooks `update` y `componentUpdated`.

<p class="tip">Aparte de `el`, debe tratar estos argumentos como de sólo lectura y no modificarlos nunca. Si necesita compartir información entre hooks, se recomienda hacerlo a través del elemento [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset).</p>

Un ejemplo de una directiva personalizada usando algunas de estas propiedades:

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

## Atajo de función

En muchos casos, usted puede querer el mismo comportamiento en `bind` y `update`, pero no preocuparse por los otros hooks. Por ejemplo:

``` js
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

## Literales de objetos

Si su directiva necesita múltiples valores, también puede pasar un literal de objeto JavaScript. Recuerde, las directivas pueden tomar cualquier expresión JavaScript válida.

``` html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

``` js
Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```
