---
title: Filters
type: guide
order: 305
---

Vue.js le permite definir filtros que pueden ser usados para aplicar formatos de texto comunes. Los filtros se pueden utilizar en dos lugares: **en la interpolación con llaves y las expresiones `v-bind`** (estas últimas soportadas en 2.1.0+). Los filtros deben añadirse al final de la expresión JavaScript, señalados con el símbolo "pipe":

``` html
<!-- en llaves -->
{{ message | capitalize }}

<!-- en v-bind -->
<div v-bind:id="rawId | formatId"></div>
```

La función de filtro siempre recibe el valor de la expresión (el resultado de la cadena anterior) como su primer argumento. En este ejemplo, la función de filtro `capitalize` recibirá el valor de `message` como argumento.

``` js
new Vue({
  // ...
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
})
```

Los filtros se pueden encadenar:

``` html
{{ message | filterA | filterB }}
```

En este caso, el `filterA`, definido con un solo argumento, recibirá el valor del mensaje, y luego se llamará a la función `filterB` con el resultado de `filterA` pasado al argumento único de `filterB`.

Los filtros son funciones JavaScript, por lo tanto pueden tomar argumentos:

``` html
{{ message | filterA('arg1', arg2) }}
```

Aquí el `filterA` se define como una función que toma tres argumentos. El valor del mensaje se pasará al primer argumento. La cadena simple `'arg1'` pasará como segundo argumento, y el valor de la expresión `arg2` será evaluado y pasado como tercer argumento.