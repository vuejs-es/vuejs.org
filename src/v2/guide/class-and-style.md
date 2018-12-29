---
title: Vinculación de datos en clases y estilos
type: guide
order: 6
---

Una necesidad común para la vinculación de datos es manipular la lista de clases de un elemento y sus estilos en línea. Como ambos son atributos, podemos usar `v-bind` para manejarlos: sólo necesitamos calcular una cadena de texto final con nuestras expresiones. Sin embargo, trabajar con concatenaciones de cadenas de texto es molesto y propenso a errores. Por esta razón, Vue proporciona mejoras especiales cuando se utiliza `v-bind` con `class` y `style`. Además de las cadenas de texto, las expresiones también pueden evaluar objetos o arrays.

## Vinculación de datos en clases HTML 

### Sintaxis de objeto

Podemos pasar un objeto a `v-bind:class` para cambiar dinámicamente de clase:

``` html
<div v-bind:class="{ active: isActive }"></div>
```

La sintaxis anterior significa que la presencia de la clase `active` estará determinada por el [thrutiness](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) de la propiedad `isActive`.

Puede alternar múltiples clases al tener más campos en el objeto. Además, la directiva `v-bind:class` también puede coexistir con el atributo `class` regular. Así que, dada la siguiente plantilla:

``` html
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>
```

Y los siguientes datos:

``` js
data: {
  isActive: true,
  hasError: false
}
```

Se renderizará:

``` html
<div class="static active"></div>
```

Cuando `isActive` o `hasError` cambien, la lista de clases se actualizará en consecuencia. Por ejemplo, si `hasError` se convierte en `true`, la lista de clases se convertirá en `"static active text-danger"`.

El objeto vinculado no tiene que estar en línea:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

Esto dará el mismo resultado. También podemos vincular a una [propiedad calculada](computed.html) que devuelve un objeto. Este es un patrón común y poderoso:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

### Sintaxis de Array

Podemos pasar un array a `v-bind:class` para aplicar una lista de clases:

``` html
<div v-bind:class="[activeClass, errorClass]"></div>
```
``` js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

Lo cual renderizará:

``` html
<div class="active text-danger"></div>
```

Si también desea alternar condicionalmente una clase de la lista, puede hacerlo con una expresión ternaria:

``` html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

Esto siempre aplicará `errorClass`, pero sólo se aplicará `activeClass` cuando `isActive` sea verdadero.

Sin embargo, esto puede ser un poco extenso si tiene varias clases condicionales. Es por eso que también es posible usar la sintaxis de objeto dentro de la sintaxis de array:

``` html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

### Con Componentes

> Esta sección asume conocimientos previos de [Componentes Vue](components.html). Siéntase libre de omitirla y regresar más tarde.

Cuando utilice el atributo `class` en un componente personalizado, esas clases se añadirán al elemento raíz del componente. Las clases existentes en este elemento no serán sobrescritas.

Por ejemplo, si declara este componente:

``` js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

Luego agregue algunas clases mientras lo usa:

``` html
<my-component class="baz boo"></my-component>
```

El HTML renderizado será:

``` html
<p class="foo bar baz boo">Hi</p>
```

Lo mismo ocurre en el uso de vinculación de datos en clases:

``` html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

Cuando `isActive` es `true`, el HTML renderizado será:

``` html
<p class="foo bar active">Hi</p>
```

## Vinculación de datos en estilos en línea

### Sintaxis de objeto

La sintaxis del objeto para `v-bind:style` es bastante sencilla - casi parece CSS, excepto que es un objeto JavaScript. Puede usar camelCase o kebab-case (use comillas con kebab-case) para los nombres de las propiedades CSS:

``` html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
``` js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

A menudo es una buena idea vincular a un objeto de estilos directamente para que la plantilla esté más limpia:

``` html
<div v-bind:style="styleObject"></div>
```
``` js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

Una vez más, la sintaxis de objeto se utiliza a menudo junto con las propiedades calculadas que devuelven los objetos.

### Sintaxis de Array

La sintaxis de array para `v-bind:style` le permite aplicar múltiples objetos de estilo al mismo elemento:

``` html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

### Prefijos automáticos

Cuando use una propiedad CSS que requiera [prefijos de navegador](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) en `v-bind:style`, por ejemplo `transform`, Vue detectará y añadirá automáticamente los prefijos apropiados a los estilos aplicados.

### Múltiples valores

> 2.3.0+

A partir de 2.3.0+ puede asignar un array con múltiples valores (prefijados) a una propiedad de estilo, por ejemplo:

``` html
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

Esto sólo renderizará el último valor en el array que sea soportado por el navegador. En este ejemplo, mostrará `display: flex` para los navegadores que soportan la versión sin prefijos de flexbox.