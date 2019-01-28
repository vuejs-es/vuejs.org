---
title: Manejo de Eventos
type: guide
order: 9
---

## Escuchando eventos

Podemos usar la directiva `v-on` para escuchar eventos de DOM y ejecutar código JavaScript cuando son activados.

Por ejemplo:

``` html
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
```
``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

Resulta en:

{% raw %}
<div id="example-1" class="demo">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
</script>
{% endraw %}

## Métodos manejadores de eventos

La lógica para muchos manejadores de eventos usualmente será más compleja que el ejemplo, de modo que mantener código JavaScript en el atributo `v-on` sencillamente no es buena idea. Por eso `v-on` puede también aceptar el nombre de un método que quiera llamar.

Por ejemplo:

``` html
<div id="example-2">
  <!-- `greet` es el nombre de un método definido más adelante -->
  <button v-on:click="greet">Greet</button>
</div>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // define métodos bajo el objeto `methods`
  methods: {
    greet: function (event) {
      // `this` dentro de los métodos apunta a la instancia Vue
      alert('Hello ' + this.name + '!')
      // `event` es el evento DOM nativo
      alert(event.target.tagName)
    }
  }
})

// puede invocar métodos en JavaScript también
example2.greet() // => 'Hello Vue.js!'
```

Resultado:

{% raw %}
<div id="example-2" class="demo">
  <button v-on:click="greet">Greet</button>
</div>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  methods: {
    greet: function (event) {
      alert('Hello ' + this.name + '!')
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
</script>
{% endraw %}

## Métodos en manejadores en línea

En vez de asignar directamente a un nombre de un método, podemos también usar métodos en una expresión JavaScript en línea: 

``` html
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
```
``` js
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
```

Resultado:
{% raw %}
<div id="example-3" class="demo">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
<script>
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
</script>
{% endraw %}

Algunas veces necesitamos también acceder al evento DOM original en la expresión de manejador en línea. Puede pasarlo al método usando la variable especial `$event`:

``` html
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>
```

``` js
// ...
methods: {
  warn: function (message, event) {
    // ahora podemos acceder al evento nativo
    if (event) event.preventDefault()
    alert(message)
  }
}
```

## Modificadores de eventos

Es una necesidad muy común llamar a `event.preventDefault()` o `event.stopPropagation()` en manejadores de eventos. Aunque podemos hacer esto fácilmente dentro de los métodos, sería mejor si los métodos se encargarán únicamente de lógica de datos en vez de tratar con detalles de eventos DOM.

Para solucionar este problema, Vue ofrece **modificadores de eventos** para `v-on`. Recuerde que los modificadores son sufijos de directiva denotados con un punto.

- `.stop`
- `.prevent`
- `.capture`
- `.self`
- `.once`

``` html
<!-- la propagación del evento click será detenida -->
<a v-on:click.stop="doThis"></a>

<!-- el evento de envío ya no refrescará la página -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- los modificadores pueden ser encadenados -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- puede ser usado sólo el modificador -->
<form v-on:submit.prevent></form>

<!-- use modo de captura cuando esté agregando el listener de evento -->
<div v-on:click.capture="doThis">...</div>

<!-- sólo activa el manejador si event.target es el propio elemento -->
<!-- p.e. no de un elemento hijo -->
<div v-on:click.self="doThat">...</div>
```

<p class="tip">El orden es importante cuando se utilizan modificadores porque el código correspondiente se genera en el mismo orden. Por lo tanto, usar `@click.prevent.self` evitará **todos los clics** mientras que `@click.self.prevent` sólo evitará los clics en el propio elemento.</p>

> Nuevo en 2.1.4+

``` html
<!-- el evento de clic se activará como máximo una vez -->
<a v-on:click.once="doThis"></a>
```

A diferencia de otros modificadores, los cuales son exclusivos a eventos nativos del DOM, el modificador `.once` puede ser usado también en [eventos de componente](components.html#Using-v-on-with-Custom-Events). Si no ha leído sobre componentes aún, no se preocupe por esto todavía.

## Modificadores de teclas

Cuando se escuchan eventos de teclado, a menudo necesitamos verificar códigos de tecla comunes. Vue nos permite añadir modificadores de tecla para `v-on` cuando se escucha por eventos de tecla:

``` html
<!-- solo llama a vm.submit() cuando el keyCode es 13 -->
<input v-on:keyup.13="submit">
```

Recordar todos los keyCodes es tedioso, así que Vue ofrece alias para las teclas más comúnmente usadas:

``` html
<!-- igual que el anterior -->
<input v-on:keyup.enter="submit">

<!-- también funciona con la versión corta -->
<input @keyup.enter="submit">
```

Aquí está la lista completa de alias de modificadores de tecla:

- `.enter`
- `.tab`
- `.delete` (captura tanto la tecla "Delete" como "Backspace")
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

También puede [definir alias de modificadores de tecla personalizados](../api/#keyCodes) usando el objeto global `config.keyCodes`:

``` js
// habilitar en v-on:keyup.f1
Vue.config.keyCodes.f1 = 112
```

### Modificadores automáticos de teclas

> Nuevo en 2.5.0+

También puede utilizar directamente cualquier nombre de tecla válido expuesto a través de [`KeyboardEvent.key`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values) como modificadores convirtiéndolos a kebab-case:

``` html
<input @keyup.page-down="onPageDown">
```

En el ejemplo anterior, el manejador sólo será llamado si `$event.key === 'PageDown'`.

<p class="tip">Algunas teclas (`.esc` y todas las teclas de flecha) tienen valores inconsistentes de `key` en IE9, us alias incorporados deben ser preferidos si necesita soportar IE9.</p>

## Teclas de modificación del sistema

> Nuevo en 2.1.0

Puede usar los siguientes modificadores para activar eventos de ratón o teclado únicamente cuando la tecla correspondiente sea presionada:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

> Nota: En teclados Macintosh, meta es la tecla comando (⌘). En teclados Windows, meta es la tecla windows (⊞). En teclados Sun Microsystem, meta es marcada como un rombo sólido (◆). En ciertos teclados, específicamente teclados de máquinas MIT y Lisp y sus sucesores, como el teclado Knight, teclado space-cadet, meta tiene la palabra “META”. En teclados Symbolics, meta tiene la palabra “META” o “Meta”.

Por ejemplo:

```html
<!-- Alt + C -->
<input @keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

<p class="tip">Note que las teclas modificadoras son diferentes de las teclas regulares y cuando se usan con eventos `keyup`, deben ser presionadas cuando se emite el evento. En otras palabras, `keyup.ctrl` sólo se activará si suelta una tecla mientras mantiene pulsada la tecla `ctrl`. No se activará si suelta la tecla `ctrl` sola.</p>

### Modificador `.exact`

> Nuevo en 2.5.0

El modificador `.exact` debe usarse en combinación con otros modificadores del sistema para indicar que la combinación exacta de modificadores debe ser presionada para que el manipulador se active.

``` html
<!-- Esto se ejecutará incluso si se pulsa Alt o Mayúsculas -->
<button @click.ctrl="onClick">A</button>

<!-- Esto sólo se ejecutará cuando únicamente se pulse Ctrl -->
<button @click.ctrl.exact="onCtrlClick">A</button>
```

### Modificadores de botones del ratón

> Nuevo en 2.2.0+

- `.left`
- `.right`
- `.middle`

Estos modificadores restringen el manejador a eventos activados por un botón específico del ratón.

## ¿Por qué listeners en HTML?

Puede estar preocupado que toda esta estrategia de escucha de eventos rompe las viejas reglas de "separación de intereses". Puede estar tranquilo - ya que todas las funciones y expresiones del manejador de Vue están estrictamente vinculadas al ViewModel que controla la vista actual, no causará dificultades en el mantenimiento. De hecho, hay muchos beneficios cuando se usa `v-on`:


1. Es más fácil de localizar la implementación de la función manejadora dentro de su código JS simplemente posando la vista sobre la plantilla HTML.

2. Como no tiene que asignar manualmente listeners de eventos en JS, su código de ViewModel puede ser únicamente lógica y estar libre de DOM. Ésto lo hace más fácil de probar.

3. Cuando un ViewModel es destruido, todos los listeners de eventos son automáticamente eliminados. No tiene que preocuparse de limpiarlos por su cuenta.
