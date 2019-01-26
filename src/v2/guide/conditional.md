---
title: Renderizado Condicional
type: guide
order: 7
---

## `v-if`

En plantillas de cadenas de texto, por ejemplo Handlebars, escribiríamos un bloque condicional de la siguiente manera:

``` html
<!-- Plantilla Handlebars -->
{{#if ok}}
  <h1>Yes</h1>
{{/if}}
```

En Vue, usamos la directiva `v-if` para lograr el mismo comportamiento:

``` html
<h1 v-if="ok">Yes</h1>
```

También es posible añadir un bloque "else" con `v-else`:

``` html
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```

### Grupos condicionales con `v-if` en `<template>`

Como `v-if` es una directiva, debe ser usada en sólo un elemento a la vez. Pero, ¿y si queremos intercambiar más de un elemento a la vez? En este caso podemos usar `v-if` en un elemento `<template>`, el cual sirve como un agrupador invisible. El resultado final no incluirá el elemento `<template>`.

``` html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### `v-else`

Puede usar la directiva `v-else` para indicar un "bloque else" cuando use `v-if`:

``` html
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```

Un elemento con `v-else` debe ser usado inmediatamente después de un elemento con `v-if` o `v-else-if`, de lo contrario no será reconocido.

### `v-else-if`

> Nuevo en 2.1.0+

La directiva `v-else-if`, como su nombre sugiere, funciona como un "bloque else if" para un elemento con `v-if`. También puede ser encadenado múltiples veces.

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

Similar a `v-else`, un elemento `v-else-if` debe ser usado inmediatamente después de un elemento `v-if` o `v-else-if`.

### Controlando elementos reusables con `key`

Vue intenta renderizar elementos tan eficientemente como le sea posible, a menudo los re-utiliza en vez de renderizarlos de nuevo. Además de ayudar a hacer a Vue muy rápido, esto puede tener algunas ventajas útiles. Por ejemplo, si permite a los usuarios intercambiar entre diferentes tipos de inicio de sesión:

``` html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

Entonces cambiando el `loginType` en el código anterior no borrará lo que el usuario ya haya ingresado. Como ambas plantillas usan los mismos elementos, el elemento `<input>` no es reemplazado - sólo su `placeholder`.

Pruébelo usted mismo ingresando algún texto en el el campo, luego presione el botón toggle:

{% raw %}
<div id="no-key-example" class="demo">
  <div>
    <template v-if="loginType === 'username'">
      <label>Username</label>
      <input placeholder="Enter your username">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address">
    </template>
  </div>
  <button @click="toggleLoginType">Toggle login type</button>
</div>
<script>
new Vue({
  el: '#no-key-example',
  data: {
    loginType: 'username'
  },
  methods: {
    toggleLoginType: function () {
      return this.loginType = this.loginType === 'username' ? 'email' : 'username'
    }
  }
})
</script>
{% endraw %}

Esto no siempre es deseable, así que Vue ofrece una forma para que usted pueda decir, "Éstos dos elementos están completamente separados - no quiero re-usarlos." Solo añada el atributo `key` con valores únicos:

``` html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

Ahora ambos campos se renderizarán desde cero cada vez que los intercambie. Compruébelo usted mismo:

{% raw %}
<div id="key-example" class="demo">
  <div>
    <template v-if="loginType === 'username'">
      <label>Username</label>
      <input placeholder="Enter your username" key="username-input">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address" key="email-input">
    </template>
  </div>
  <button @click="toggleLoginType">Toggle login type</button>
</div>
<script>
new Vue({
  el: '#key-example',
  data: {
    loginType: 'username'
  },
  methods: {
    toggleLoginType: function () {
      return this.loginType = this.loginType === 'username' ? 'email' : 'username'
    }
  }
})
</script>
{% endraw %}

Fíjese que los elementos `<label>` aún son re-usados eficientemente, esto es porque no tienen atributos `key`.

## `v-show`

Otra opción para mostrar condicionalmente un elemento es la directiva `v-show`. Se usa mayormente de la misma forma:

``` html
<h1 v-show="ok">Hello!</h1>
```

La diferencia es que un elemento con `v-show` siempre será renderizado y permanecerá en el DOM; `v-show` simplemente intercambia el valor de la propiedad CSS `display` del elemento.

<p class="tip">Fíjese que `v-show` no soporta la sintaxis `<template>`, tampoco funciona con `v-else`.</p>

## `v-if` vs `v-show`

`v-if` es un renderizado condicional "real" ya que se asegura que los listeners de eventos y los componentes hijos del bloque condicional sean apropiadamente destruidos y re-creados durante intercambios.

`v-if` también es **lazy**: si la condición es falsa en el renderizado inicial, no hará nada - el bloque condicional no será renderizado hasta que la condición sea verdadera por primera vez.

En comparación, `v-show` es más sencillo - el elemento siempre es renderizado sin importar la condición inicial, sólo con un intercambio sencillo basado en CSS.

En términos generales, `v-if` tiene un mayor coste de intercambio mientras que `v-show` tiene un mayor coste de renderizado inicial. Así que, prefiera `v-show` si necesita intercambiar algo muy a menudo, y use `v-if` si la condición no es propensa a cambiar durante la ejecución.

## `v-if` con `v-for`

Cuando se usa junto a `v-if`, `v-for` tiene una prioridad mayor que `v-if`. Visite la <a href="../guide/list.html#V-for-and-v-if">guía de renderizado de listas</a> para más detalles.
