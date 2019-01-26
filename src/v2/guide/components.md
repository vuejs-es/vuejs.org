---
title: Componentes
type: guide
order: 11
---

## ¿Qué son los componentes?

Los componentes son una de las características más poderosas de Vue. Ayudan a extender elementos HTML básicos para encapsular código reutilizable. A un alto nivel, los componentes son elementos personalizados a los que el compilador de Vue les asocia un comportamiento. En algunos casos, también pueden aparecer como un elemento HTML nativo extendido con el atributo especial  `is`.

## Usando los componentes

### Registro global

Hemos aprendido en las secciones anteriores que podemos crear una nueva instancia de Vue con:

``` js
new Vue({
  el: '#some-element',
  // opciones
})
```

Para registrar un componente global, puede utilizar `Vue.component(tagName, options)`. Por ejemplo:

``` js
Vue.component('my-component', {
  // opciones
})
```

<p class="tip">Tenga en cuenta que Vue no obliga a cumplir las [reglas del W3C](https://www.w3.org/TR/custom-elements/#concepts) para nombres de etiquetas personalizadas (todas en minúsculas, deben contener un guión), aunque seguir esta convención se considera una buena práctica.

Una vez registrado, un componente puede ser usado en la plantilla de una instancia como un elemento personalizado, `<my-component></my-component>`. Asegúrese de que el componente esté registrado **antes** de la instancia de Vue raíz. Aquí está el ejemplo completo:

``` html
<div id="example">
  <my-component></my-component>
</div>
```

``` js
// registro
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})

// creación de una instancia raíz
new Vue({
  el: '#example'
})
```

La cual renderizará:

``` html
<div id="example">
  <div>A custom component!</div>
</div>
```

{% raw %}
<div id="example" class="demo">
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})
new Vue({ el: '#example' })
</script>
{% endraw %}

### Registro local

No es necesario registrar cada componente globalmente. Puede hacer que un componente esté disponible sólo en el ámbito de otra instancia/componente registrándolo con la opción `components` de la instancia:

``` js
var Child = {
  template: '<div>A custom component!</div>'
}

new Vue({
  // ...
  components: {
    // <my-component> sólo estará disponible para la plantilla del padre
    'my-component': Child
  }
})
```

El mismo tipo de encapsulamiento aplica para otras características de Vue, como las directivas.

### Inconvenientes en el análisis de plantillas DOM

Cuando utilice el DOM como plantilla (por ejemplo, utilizando la opción `el` para montar un elemento con contenido existente), estará sujeto a algunas restricciones inherentes al funcionamiento de HTML, ya que Vue sólo puede recuperar el contenido de la plantilla **después** de que el navegador la haya analizado y normalizado. Más notablemente, algunos elementos como `<ul>`, `<ol>`, `<table>` y `<select>` tienen restricciones sobre qué elementos pueden aparecer dentro de ellos, y algunos elementos como `<option>` sólo pueden aparecer dentro de otros elementos.

Esto dará lugar a problemas cuando se utilicen componentes personalizados con elementos que tengan tales restricciones, por ejemplo:

``` html
<table>
  <my-row>...</my-row>
</table>
```

El componente personalizado `<my-row>` será considerado como contenido no válido, lo que causará errores en la salida de datos. Una solución es usar el atributo especial `is`:

``` html
<table>
  <tr is="my-row"></tr>
</table>
```

**Debe tenerse en cuenta que estas limitaciones no se aplican si se utilizan plantillas de cadenas de texto de una de las siguientes fuentes**:

- `<script type="text/x-template">`
- Plantillas de cadenas de texto en línea de JavaScript
- Componentes de `.vue`

Por lo tanto, prefiera utilizar plantillas de cadenas de texto siempre que sea posible.

### `data` Debe ser una función

La mayoría de las opciones que se pueden enviar al constructor de Vue se pueden utilizar en un componente, con un caso especial: `data` debe ser una función. De hecho, si usted intenta esto:

``` js
Vue.component('my-component', {
  template: '<span>{{ message }}</span>',
  data: {
    message: 'hello'
  }
})
```

Vue se detendrá y emitirá advertencias en la consola, indicándole que `data` debe ser una función para las instancias de componentes. Es bueno entender por qué existen las reglas, así que hagamos trampa.

``` html
<div id="example-2">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
```

``` js
var data = { counter: 0 }

Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  // data es técnicamente una función, de modo que Vue
  // no se quejará, pero estamos retornando la misma referencia
  // de objeto por cada instancia del componente
  data: function () {
    return data
  }
})

new Vue({
  el: '#example-2'
})
```

{% raw %}
<div id="example-2" class="demo">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
<script>
var data = { counter: 0 }
Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return data
  }
})
new Vue({
  el: '#example-2'
})
</script>
{% endraw %}

Dado que las tres instancias de componentes comparten el mismo objeto `data`, ¡incrementar un contador los incrementa a todos! Ouch. Arreglemos esto devolviendo un nuevo objeto data:

``` js
data: function () {
  return {
    counter: 0
  }
}
```

Ahora todos nuestros contadores tienen su propio estado interno:

{% raw %}
<div id="example-2-5" class="demo">
  <my-component></my-component>
  <my-component></my-component>
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  }
})
new Vue({
  el: '#example-2-5'
})
</script>
{% endraw %}

### Componiendo componentes

Los componentes están pensados para ser usados juntos, más comúnmente en las relaciones padre-hijo: el componente A puede usar el componente B en su propia plantilla. Inevitablemente necesitan comunicarse entre sí: el padre puede necesitar pasarle los datos al hijo, y el hijo puede necesitar informar al padre de algo que ha sucedido en en él. Sin embargo, también es muy importante mantener al padre y al hijo tan desacoplados como sea posible a través de una interfaz claramente definida. Esto asegura que el código de cada componente pueda ser escrito y razonado de forma relativamente aislada, haciéndolo más mantenible y potencialmente más fácil de reutilizar.

En Vue, la relación del componente padre-hijo se puede resumir como **props bajan, eventos suben**. El padre pasa los datos al hijo a través de **props**, y el hijo envía mensajes al padre a través de **eventos**. Veamos cómo trabajan a continuación.

<p style="text-align: center">
  <img style="width:300px" src="/images/props-events.png" alt="props bajan, eventos suben">
</p>

## Props

### Enviando datos con props

Cada instancia de componente tiene su propio **ámbito aislado**. Esto significa que no puede (y no debería) hacer referencia directamente a los datos de nivel superior en la plantilla de un componente inferior. Los datos pueden ser enviados a los componentes hijo utilizando **props**.

Un prop es un atributo personalizado para pasar información de los componentes principales. Un componente hijo necesita declarar explícitamente los objetos que espera recibir usando la [opción `props`](../api/#props):

``` js
Vue.component('child', {
  // declare los props
  props: ['message'],
  // tal como como data, el prop puede ser usado dentro de plantillas
  // y también está disponible en el vm como this.message
  template: '<span>{{ message }}</span>'
})
```

Luego podemos pasarle una cadena de texto simple como esta:

``` html
<child message="hello!"></child>
```

Resultado:

{% raw %}
<div id="prop-example-1" class="demo">
  <child message="hello!"></child>
</div>
<script>
new Vue({
  el: '#prop-example-1',
  components: {
    child: {
      props: ['message'],
      template: '<span>{{ message }}</span>'
    }
  }
})
</script>
{% endraw %}

### camelCase vs. kebab-case

Los atributos HTML no distinguen entre mayúsculas y minúsculas, por lo que cuando se utilizan plantillas no-string, los nombres de prop en camelCase necesitan usar sus equivalentes en kebab-case (delimitados por guiones):

``` js
Vue.component('child', {
  // camelCase en JavaScript
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```

``` html
<!-- kebab-case en HTML -->
<child my-message="hello!"></child>
```

Una vez más, si está utilizando plantillas de cadenas de texto, esta limitación no se aplica.

### Props dinámicos

Similar a la vinculación de un atributo normal a una expresión, también podemos usar `v-bind` para vincular dinámicamente los props a los datos en el padre. Cada vez que los datos se actualizan en el padre, también fluirán hacia el hijo:

``` html
<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
```

También puede utilizar la sintaxis abreviada de `v-bind`:

``` html
<child :my-message="parentMsg"></child>
```

Resultado:

{% raw %}
<div id="demo-2" class="demo">
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
<script>
new Vue({
  el: '#demo-2',
  data: {
    parentMsg: 'Message from parent'
  },
  components: {
    child: {
      props: ['myMessage'],
      template: '<span>{{myMessage}}</span>'
    }
  }
})
</script>
{% endraw %}

Si quiere enviar todas las propiedades de un objeto como props, puede usar `v-bind` sin un argumento (`v-bind` en lugar de `v-bind:prop-name`). Por ejemplo, si se tiene un objeto `todo`:

``` js
todo: {
  text: 'Learn Vue',
  isComplete: false
}
```

Entonces:

``` html
<todo-item v-bind="todo"></todo-item>
```

Será equivalente a:

``` html
<todo-item
  v-bind:text="todo.text"
  v-bind:is-complete="todo.isComplete"
></todo-item>
```

### Literal vs. dinámico

Un error común que los principiantes tienden a cometer es intentar enviar un número usando la sintaxis literal:

``` html
<!-- esto envía una cadena de texto "1" -->
<comp some-prop="1"></comp>
```

Sin embargo, como se trata de un prop literal, su valor se transmite como una cadena de texto simple `"1"` en lugar de un número real. Si queremos enviar un número JavaScript real, necesitamos usar `v-bind` para que su valor sea evaluado como una expresión JavaScript:

``` html
<!-- esto envía un número -->
<comp v-bind:some-prop="1"></comp>
```

### Flujo de datos unidireccional

Todos los props forman un vínculo **unidireccional** entre la propiedad hijo y su correspondiente padre: cuando la propiedad padre se actualiza, fluirá hacia el hijo, pero no al revés. Esto previene que los componentes hijo muten accidentalmente el estado del padre, lo que puede hacer que el flujo de datos de su aplicación sea más difícil de entender.

Además, cada vez que se actualiza el componente padre, todos los props del componente hijo se actualizarán con el último valor. Esto significa que **no** debe intentar mutar un prop dentro de un componente hijo. Si lo hace, Vue le avisará en la consola.

Normalmente hay dos casos en los que es tentador mutar un accesorio:

1. El prop es usado únicamente para pasar un valor inicial, y el componente hijo quiere usarlo como una propiedad de datos después de eso;

2. El prop es enviado como un valor básico que necesita ser transformado.

Las formas correctas de acercarse a éstos casos son:

1. Definir una propiedad de data local que utiliza el valor inicial del prop como su valor inicial:

  ``` js
  props: ['initialCounter'],
  data: function () {
    return { counter: this.initialCounter }
  }
  ```

2. Definir una propiedad calculada que se calcula a partir del valor del prop:

  ``` js
  props: ['size'],
  computed: {
    normalizedSize: function () {
      return this.size.trim().toLowerCase()
    }
  }
  ```

<p class="tip">Note que los objetos y arrays en JavaScript son enviados por referencia, así que si el prop es un array u objeto, mutar el objeto o array dentro del hijo **afectará** el estado del padre.</p>

### Validación de props

Es posible que un componente especifique los requerimientos para los props que está recibiendo. Si no se cumplen los requerimientos, Vue emitirá advertencias. Esto es especialmente útil cuando se está creando un componente que está destinado a ser utilizado por otros.

En lugar de definir los props como un array de cadenas de texto, se puede utilizar un objeto con requisitos de validación:

``` js
Vue.component('example', {
  props: {
    // verificación de tipos básica (`null` significa que acepta cualquier tipo)
    propA: Number,
    // múltiples tipos posibles
    propB: [String, Number],
    // una cadena de texto requerida
    propC: {
      type: String,
      required: true
    },
    // un número con un valor por defecto
    propD: {
      type: Number,
      default: 100
    },
    // objetos/arrays por defecto deben ser obtenidos de
    // una función generadora
    propE: {
      type: Object,
      default: function () {
        return { message: 'hello' }
      }
    },
    // función validadora personalizada
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```

El valor de `type` puede ser uno de los siguientes constructores nativos:

- String
- Number
- Boolean
- Function
- Object
- Array
- Symbol

Además, `type` también puede ser una función de constructor personalizado y la afirmación se realizará con una comprobación `instanceof`

Cuando la validación de prop falla, Vue producirá una advertencia en la consola (si se utiliza la compilación de desarrollo). Tenga en cuenta que los props son validados __antes__ de que se cree una instancia de componente, por lo que dentro de las funciones `default` `validator`, las propiedades de la instancia como de `data`, `computed`, o `methods` no estarán disponibles.

## Atributos que no son props

Son atributos que se pasan a un componente, pero que no tienen definido un prop correspondiente.

Aunque se prefieren los props definidos explícitamente para enviar información a un componente hijo, los autores de las librerías de componentes no siempre pueden prever los contextos en los que se pueden utilizar sus componentes. Es por eso que los componentes pueden aceptar atributos arbitrarios, que se añaden al elemento raíz del componente.

Por ejemplo, imagine que estamos usando un componente `bs-date-input` de terceros con un plugin Bootstrap que requiere un atributo `data-3d-date-picker` en el `input`. Podemos añadir este atributo a nuestra instancia de componente:

``` html
<bs-date-input data-3d-date-picker="true"></bs-date-input>
```

Y el atributo `data-3d-date-picker="true"` se añadirá automáticamente al elemento raíz de `bs-date-input`.

### Reemplazar/Fusionar con atributos existentes

Imagine que esta es la plantilla para `bs-date-input`:

``` html
<input type="date" class="form-control">
```

Para especificar un tema para nuestro plugin de selección de fecha, es posible que necesitemos añadir una clase específica, como esta:

``` html
<bs-date-input
  data-3d-date-picker="true"
  class="date-picker-theme-dark"
></bs-date-input>
```

En este caso, se definen dos valores diferentes para `class`:

- `form-control`, que es definido por el componente en su plantilla
- `date-picker-theme-dark`, que es enviado al componente por su padre

Para la mayoría de los atributos, el valor proporcionado al componente reemplazará el valor establecido por el componente. Así, por ejemplo, enviar `type="large"` reemplazará `type="date"` ¡y probablemente lo estropee! Afortunadamente, los atributos `class` y `style` son un poco más inteligentes, así que ambos valores se fusionan, haciendo que el valor final sea: `form-control date-picker-theme-dark`.

## Eventos personalizados

Hemos aprendido que los padres pueden transmitir los datos a sus hijos utilizando props, pero ¿cómo nos comunicamos de vuelta con los padres cuando algo sucede? Aquí es donde entra en juego el sistema de eventos personalizado de Vue.

### Uso de `v-on` con eventos personalizados

Cada instancia de Vue implementa una [interfaz de eventos](../api/#Instance-Methods-Events), lo que significa que puede:

- Escuchar un evento usando `$on(eventName)`
- Activar un evento usando `$emit(eventName)`

<p class="tip"> Tenga en cuenta que el sistema de eventos de Vue es diferente al [API del EventTarget](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget) del navegador. Aunque funcionan de forma similar, `$on` y `$emit` __no__ son alias para `addEventListener` y `dispatchEvent`.</p>

Además, un componente padre puede escuchar los eventos emitidos desde un componente hijo usando `v-on` directamente en la plantilla donde se utiliza el componente hijo.

<p class="tip"> No se puede usar `$on` para escuchar los eventos emitidos por los hijos. Debe usar `v-on` como en el ejemplo a continuación.</p>

Aquí hay un ejemplo:

``` html
<div id="counter-event-example">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
```

``` js
Vue.component('button-counter', {
  template: '<button v-on:click="incrementCounter">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    incrementCounter: function () {
      this.counter += 1
      this.$emit('increment')
    }
  },
})

new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
```

{% raw %}
<div id="counter-event-example" class="demo">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
<script>
Vue.component('button-counter', {
  template: '<button v-on:click="incrementCounter">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    incrementCounter: function () {
      this.counter += 1
      this.$emit('increment')
    }
  }
})
new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
</script>
{% endraw %}

En este ejemplo, es importante tener en cuenta que el componente hijo aún está completamente desacoplado de lo que sucede fuera de él. Todo lo que hace es reportar información sobre su propia actividad, en caso de que a un componente padre le importe.


### Vinculación de eventos nativos a componentes

Puede haber ocasiones en las que desee escuchar un evento nativo en el elemento raíz de un componente. En estos casos, puede usar el modificador `.native` para `v-on`. Por ejemplo:

``` html
<my-component v-on:click.native="doTheThing"></my-component>
```

### Modificador `.sync`

> 2.3.0+

En algunos casos es posible que necesitemos "vinculación a dos vías" para un prop - de hecho, en Vue 1.x esto es exactamente lo que el modificador `.sync` proporcionaba. Cuando un componente hijo muta un prop que tiene `.sync`, el cambio de valor se reflejará en el padre. TEsto es conveniente, sin embargo, lleva a problemas de mantenimiento a largo plazo porque rompe la suposición de flujo de datos unidireccional: el código que muta los props hijos está afectando implícitamente al estado padre.

Esta es la razón por la que eliminamos el modificador `.sync` cuando se publicó la versión 2.0. Sin embargo, hemos encontrado que hay casos en los que podría ser útil, especialmente cuando se envían componentes reutilizables. Lo que necesitamos cambiar es **hacer que el código en el hijo que afecta al estado padre sea más consistente y explícito.**

En 2.3.0+ reintrodujimos el modificador `.sync` para los props, pero esta vez es sólo azúcar sintáctica que se expande automáticamente en un listener `v-on` adicional:

Lo siguiente

``` html
<comp :foo.sync="bar"></comp>
```

se expande en:

``` html
<comp :foo="bar" @update:foo="val => bar = val"></comp>
```

Para que el componente hijo actualice el valor de `foo`, necesita emitir explícitamente un evento en lugar de mutar el prop:

``` js
this.$emit('update:foo', newValue)
```

### Componentes de campos de formulario usando eventos personalizados

Los eventos personalizados también se pueden utilizar para crear entradas personalizadas que funcionen con `v-model`. Recuerde:

``` html
<input v-model="something">
```

Es azúcar sintáctica para:

``` html
<input
  v-bind:value="something"
  v-on:input="something = $event.target.value">
```

Cuando se utiliza con un componente, se simplifica a:

``` html
<custom-input
  :value="something"
  @input="value => { something = value }">
</custom-input>
```

Así que para que un componente funcione con `v-model`, debería (estos pueden ser configurados en 2.2.0+):

- aceptar un prop `value` 
- emitir un evento `input` con el nuevo valor

Veámoslo en acción con una simple entrada de moneda:

``` html
<currency-input v-model="price"></currency-input>
```

``` js
Vue.component('currency-input', {
  template: '\
    <span>\
      $\
      <input\
        ref="input"\
        v-bind:value="value"\
        v-on:input="updateValue($event.target.value)">\
    </span>\
  ',
  props: ['value'],
  methods: {
    // En vez de actualizar el valor directamente, este
    // método es usado para darle formato y poner 
    // restricciones al valor del campo
    updateValue: function (value) {
      var formattedValue = value
        // Eliminar espacios en blanco de los extremos
        .trim()
        // Aproximar a dos dígitos decimales
        .slice(
          0,
          value.indexOf('.') === -1
            ? value.length
            : value.indexOf('.') + 3
        )
      // Si el valor no estaba normalizado,
      // sobreescríbalo manualmente para que concuerde
      if (formattedValue !== value) {
        this.$refs.input.value = formattedValue
      }
      // Emita el valor numérico a través del evento input
      this.$emit('input', Number(formattedValue))
    }
  }
})
```

{% raw %}
<div id="currency-input-example" class="demo">
  <currency-input v-model="price"></currency-input>
</div>
<script>
Vue.component('currency-input', {
  template: '\
    <span>\
      $\
      <input\
        ref="input"\
        v-bind:value="value"\
        v-on:input="updateValue($event.target.value)"\
      >\
    </span>\
  ',
  props: ['value'],
  methods: {
    updateValue: function (value) {
      var formattedValue = value
        .trim()
        .slice(
          0,
          value.indexOf('.') === -1
            ? value.length
            : value.indexOf('.') + 3
        )
      if (formattedValue !== value) {
        this.$refs.input.value = formattedValue
      }
      this.$emit('input', Number(formattedValue))
    }
  }
})
new Vue({
  el: '#currency-input-example',
  data: { price: '' }
})
</script>
{% endraw %}

Sin embargo, la implementación anterior es bastante sencilla. Por ejemplo, a los usuarios se les permite ingresar múltiples puntos e incluso letras a veces - ¡nada deseable! Así que para aquellos que quieren ver un ejemplo no trivial, aquí hay un filtro de moneda más robusto:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/1oqjojjx/embedded/result,html,js" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

### Personalizar componente `v-model`

> Nuevo en 2.2.0+

Por defecto, `v-model` en un componente usa `value` como el prop e `input` como el evento, pero algunos tipos de entrada como casillas de verificación y botones de radio pueden querer usar el prop `value` para un propósito diferente. Usar la opción `model` puede evitar el conflicto en tales casos:

``` js
Vue.component('my-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean,
    // Esto permite usar el prop `value` para un propósito diferente
    value: String
  },
  // ...
})
```

``` html
<my-checkbox v-model="foo" value="some value"></my-checkbox>
```

Lo anterior será equivalente a:

``` html
<my-checkbox
  :checked="foo"
  @change="val => { foo = val }"
  value="some value">
</my-checkbox>
```

<p class="tip">Note que todavía tiene que declarar explícitamente el prop `checked`.</p>

### Comunicación fuera de la relación Padre-Hijo

A veces, dos componentes pueden necesitar comunicarse entre sí, pero no son padres/hijos entre sí. En escenarios simples, puede utilizar una instancia de Vue vacía como un bus de eventos central:

``` js
var bus = new Vue()
```
``` js
// en el método del componente A
bus.$emit('id-selected', 1)
```
``` js
// en el hook creado en el componente B
bus.$on('id-selected', function (id) {
  // ...
})
```

En casos más complejos, se debe considerar la posibilidad de emplear un [patrón de control de estado](state-management.html).

## Distribución de contenido con slots

Cuando se utilizan componentes, a menudo es deseable componerlos de esta manera:

``` html
<app>
  <app-header></app-header>
  <app-footer></app-footer>
</app>
```

Hay dos cosas que hay que tener en cuenta aquí:

1. El componente `<app>` no sabe qué contenido recibirá. Es decidido por el componente que está utilizando `<app>`.

2. El componente `<app>` muy probablemente tiene su propia plantilla.

Para que la composición funcione, necesitamos una forma de entretejer el "contenido" padre y la plantilla propia del componente. Este es un proceso llamado **distribución de contenido** (o “transclusión” si está familiarizado con Angular). Vue.js implementa una API de distribución de contenido que está modelada según el actual [borrador de especificación de Componentes Web](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), usando el elemento especial `<slot>` para servir como salida para el contenido original.

### Ámbito de compilación

Antes de indagar en la API, primero aclaremos en qué ámbito se compilan los contenidos. Imagine una plantilla como esta:

``` html
<child-component>
  {{ message }}
</child-component>
```

¿Debería el `message` estar vinculado a los datos del padre o a los datos del hijo? La respuesta es el padre. Una simple regla empírica para el alcance de los componentes es:

> Todo en la plantilla padre se compila en el ámbito padre; todo en la plantilla hijo se compila en el ámbito hijo.

Un error común es intentar vincular una directiva a una propiedad/método hijo en la plantilla padre:

``` html
<!-- NO funciona -->
<child-component v-show="someChildProperty"></child-component>
```

Asumiendo que `someChildProperty` es una propiedad en el componente hijo, el ejemplo anterior no funcionaría. La plantilla del padre no es consciente del estado de un componente hijo.

Si necesita asignar directivas de ámbito de hijos al nodo raíz de un componente, debe hacerlo en la propia plantilla del hijo:

``` js
Vue.component('child-component', {
  // Esto funciona ya que estamos en el ámbito correcto
  template: '<div v-show="someChildProperty">Child</div>',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
```

Del mismo modo, el contenido distribuido será compilado en el ámbito del padre.

### Slot único

El contenido padre será **desechado** a menos que la plantilla del componente hijo contenga al menos un elemento `<slot>`. Cuando sólo hay un slot sin atributos, el fragmento de contenido entero será insertado en su posición en el DOM, reemplazando dicho slot.

Cualquier cosa originalmente contenida dentro de etiquetas `<slot>` es considerada **contenido de recuperación**. El contenido de recuperación es compilado en el componente hijo y será mostrado únicamente si el elemento contenedor está vacío y no tiene contenido para ser insertado.

Suponga que tenemos un componente llamado `my-component` con la siguiente plantilla:

``` html
<div>
  <h2>Soy el título del hijo</h2>
  <slot>
    Ésto será mostrado sólo si no hay contenido
    para distribuir.
  </slot>
</div>
```

Y un padre que usa el componente:

``` html
<div>
  <h1>Soy el título del padre</h1>
  <my-component>
    <p>Ésto es contenido original</p>
    <p>Ésto es más contenido original</p>
  </my-component>
</div>
```

El resultado renderizado será:

``` html
<div>
  <h1>Soy el título del padre</h1>
  <div>
    <h2>Soy el título del hijo</h2>
    <p>Ésto es contenido original</p>
    <p>Ésto es más contenido original</p>
  </div>
</div>
```

### Slots con nombre

Los elementos `<slot>` tienen un atributo especial, `name`, el cual puede ser usado para personalizar aún más cómo el contenido debe ser distribuido. Puede tener múltiples slots con nombres diferentes. Un slot nombrado se va a emparejar con cualquier elemento que tenga el correspondiente atributo `slot` en el fragmento de contenido.

Todavía puede haber un slot sin nombre, el cual se considera el **slot por defecto** que funciona como una salida que captura todo contenido que no sea emparejado con nombre. Si no hay un slot por defecto, contenido sin nombre será desechado.

Por ejemplo, supongamos que tenemos un componente `app-layout` con la siguiente plantilla:

``` html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Markup del padre:

``` html
<app-layout>
  <h1 slot="header">Aquí puede haber un título</h1>

  <p>Un párrafo para el contenido principal.</p>
  <p>Y otro más.</p>

  <p slot="footer">Aquí hay información de contacto.</p>
</app-layout>
```

El resultado obtenido será:

``` html
<div class="container">
  <header>
    <h1>Aquí puede haber un título</h1>
  </header>
  <main>
    <p>Un párrafo para el contenido principal.</p>
    <p>Y otro más.</p>
  </main>
  <footer>
    <p>Aquí hay información de contacto.</p>
  </footer>
</div>
```

La API de distribución de contenidos es un mecanismo muy útil a la hora de diseñar componentes que deben ser compuestos conjuntamente.

### Slots de ámbito

> Nuevo en 2.1.0+

Un slot de ámbito es un tipo especial de slot que funciona como una plantilla reutilizable (a la que se le pueden pasar datos) en lugar de elementos ya renderizados.

En un componente hijo, envíe los datos a un slot como si estuviera enviando props a un componente:

``` html
<div class="child">
  <slot text="hello from child"></slot>
</div>
```

En el padre, debe existir un elemento `<template>` con un atributo especial `slot-scope` indicando que se trata de una plantilla para un slot de ámbito. El valor de `slot-scope` se usará como el nombre de una variable temporal que contiene el objeto props enviado desde el hijo:

``` html
<div class="parent">
  <child>
    <template slot-scope="props">
      <span>hello from parent</span>
      <span>{{ props.text }}</span>
    </template>
  </child>
</div>
```

Si renderizamos lo anterior, el resultado será:

``` html
<div class="parent">
  <div class="child">
    <span>hello from parent</span>
    <span>hello from child</span>
  </div>
</div>
```

> En 2.5.0+, `slot-scope` ya no se limita a `<template>` y puede utilizarse en cualquier elemento o componente.

Un caso de uso más típico para los slots de ámbito sería un componente de la lista que permite al consumidor de componentes personalizar cómo se debe representar cada elemento de la lista:

``` html
<my-awesome-list :items="items">
  <!-- slots de ámbito pueden ser nombrados también -->
  <li
    slot="item"
    slot-scope="props"
    class="my-fancy-item">
    {{ props.text }}
  </li>
</my-awesome-list>
```

Y la plantilla para el componente de lista:

``` html
<ul>
  <slot name="item"
    v-for="item in items"
    :text="item.text">
    <!-- contenido alternativo aquí -->
  </slot>
</ul>
```

#### Desestructuración

El valor de `scope-slot` es de hecho una expresión JavaScript válida que puede aparecer en la posición de argumento de una función. Esto significa que en los entornos soportados (en componentes de un solo archivo o en navegadores modernos) también se puede utilizar la desestructuración ES2015 en la expresión:

``` html
<child>
  <span slot-scope="{ text }">{{ text }}</span>
</child>
```

## Componentes dinámicos

Puede utilizar el mismo punto de montaje y cambiar dinámicamente entre varios componentes utilizando el elemento reservado `<component>` y enlazarlo dinámicamente a su atributo `is`:

``` js
var vm = new Vue({
  el: '#example',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
```

``` html
<component v-bind:is="currentView">
  <!-- ¡el componente cambia siempre que vm.currentView cambie! -->
</component>
```

Si lo prefiere, también puede vincular directamente a los objetos del componente:

``` js
var Home = {
  template: '<p>Welcome home!</p>'
}

var vm = new Vue({
  el: '#example',
  data: {
    currentView: Home
  }
})
```

### `keep-alive`

Si desea mantener los componentes desactivados en la memoria para que pueda conservar su estado o evitar volver a renderizarlos, puede envolver un componente dinámico en un elemento `<keep-alive>`:

``` html
<keep-alive>
  <component :is="currentView">
    <!-- ¡los componentes inactivos serán guardados en caché! -->
  </component>
</keep-alive>
```

Conozca más detalles sobre `<keep-alive>` en la [referencia de la API](../api/#keep-alive).

## Misceláneos

### Escribiendo componentes reusables

Al crear componentes, es bueno tener en cuenta si tiene la intención de reutilizarlos en otro lugar más adelante. Está bien que los componentes de uso único estén estrechamente acoplados, pero los componentes reutilizables deben definir una interfaz pública limpia y no hacer suposiciones sobre el contexto en el que se utilizan.

La API para un componente Vue viene en tres partes - props, eventos y slots:

- **Props** permiten que el entorno externo pase datos al componente

- **Eventos** permiten que el componente desencadene efectos secundarios en el entorno externo

- **Slots** permiten al ambiente externo introducir contenido adicional al componente.

Con las sintaxis abreviadas dedicadas para `v-bind` y `v-on`, las intenciones pueden ser transmitidas de forma clara y sucinta en la plantilla:

``` html
<my-component
  :foo="baz"
  :bar="qux"
  @event-a="doThis"
  @event-b="doThat"
>
  <img slot="icon" src="...">
  <p slot="main-text">Hello!</p>
</my-component>
```

### Referencias a componentes hijo

A pesar de la existencia de props y eventos, algunas veces usted querrá acceder a un componente hijo en JavaScript. Para lograr esto debe asignar un ID de referencia al componente hijo usando `ref`. Por ejemplo:

``` html
<div id="parent">
  <user-profile ref="profile"></user-profile>
</div>
```

``` js
var parent = new Vue({ el: '#parent' })
// acceder a la instancia hijo
var child = parent.$refs.profile
```

Cuando `ref` se usa junto con `v-for`, la referencia que obtendrá será un array que contiene los componentes hijo que reflejan la fuente de datos.

<p class="tip">Los `$refs` son poblados después que el componente ha sido renderizado, y no es reactivo. Sólo se quiere que sea un mecanismo de emergencia para manipulación directa del hijo - debe evitar usar '$refs' en plantillas o propiedades calculadas.</p>

### Componentes asíncronos

En aplicaciones grandes, es posible que tengamos que dividir la aplicación en trozos más pequeños y sólo cargar un componente del servidor cuando sea realmente necesario. Para facilitar esto, Vue le permite definir su componente como una función generadora  que resuelve asíncronamente la definición de su componente. Vue sólo activará la función generadora cuando el componente necesite ser renderizado y almacenará en caché el resultado para futuros re-renderizaciones. Por ejemplo:

``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // Pase la definición del componente al callback de resolución
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

La función generadora recibe un callback `resolve`, el cual debe ser llamado cuando haya obtenido la definición de su componente desde el servidor. También puede llamar `reject(reason)` para indicar que la carga ha fallado. El `setTimeout` aquí presente es simplemente para una demostración; Cómo recibir correctamente el componente depende de usted. Un acercamiento recomendado es usar componentes asíncronos junto a la [división de código de Webpack](http://webpack.github.io/docs/code-splitting.html):

``` js
Vue.component('async-webpack-example', function (resolve) {
  // Esta sintaxis de require especial va a indicar a Webpack
  // que automáticamente divida su código compilado en paquetes
  // que serán cargados usando peticiones Ajax.
  require(['./my-async-component'], resolve)
})
```

Usted también puede retornar un `Promise` en la función de resolución, de modo que junto a Webpack 2 y sintaxis ES2015 puede hacer lo siguiente: 

``` js
Vue.component(
  'async-webpack-example',
  // la funcion `import` retorna un `Promise`.
  () => import('./my-async-component')
)
```

Al usar [registro local](components.html#Local-Registration),también puede proporcionar directamente una función que devuelva un `Promise`:

``` js
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})
```

<p class="tip">Si eres un usuario de <strong>Browserify</strong> que quiere usar componentes asíncronos, su creador desafortunadamente [ha dejado claro](https://github.com/substack/node-browserify/issues/58#issuecomment-21978224) que la carga asíncrona "no es algo que Browserify vaya a soportar." Oficialmente, al menos. La comunidad Browserify ha encontrado[algunas soluciones](https://github.com/vuejs/vuejs.org/issues/620), que pueden ser útiles para aplicaciones existentes y complejas. Para todos los demás escenarios, recomendamos el uso de Webpack para la compatibilidad con con asincronía integrada y de primera clase.</p>

### Componentes asíncronos avanzados

> Nuevo en 2.3.0+

A partir de 2.3.0+ el generador de componentes asíncronos también puede devolver un objeto del siguiente formato:

``` js
const AsyncComp = () => ({
  // El componente a cargar. Debería ser una promesa
  component: import('./MyComp.vue'),
  // Un componente para usar mientras el componente asíncrono se está cargando
  loading: LoadingComp,
  // Un componente para usar si la carga falla.
  error: ErrorComp,
  // Demora antes de mostrar la carga. Valor por defecto: 200ms.
  delay: 200,
  // El componente de error se mostrará si un tiempo de espera es
  // proporcionado y superado. Valor por defecto: Infinito.
  timeout: 3000
})
```

Tenga en cuenta que cuando se utiliza como componente de ruta en `vue-router`, estas propiedades se ignoran porque los componentes asíncronos se resuelven antes de que se produzca la navegación por la ruta. También necesita usar `vue-router` 2.4.0+ si desea usar la sintaxis anterior para los componentes de la ruta.

### Convenciones sobre nombres de componentes

Al registrar componentes (o props), puede utilizar kebab-case, camelCase, o PascalCase.

``` js
// en una definición de componente
components: {
  // registro usando kebab-case
  'kebab-cased-component': { /* ... */ },
  // registro usando camelCase
  'camelCasedComponent': { /* ... */ },
  // registro usando PascalCase
  'PascalCasedComponent': { /* ... */ }
}
```

Pero dentro de plantillas HTML, debe usar los equivalentes en kebab-case:

``` html
<!-- Siempre use kebab-case en plantillas HTML -->
<kebab-cased-component></kebab-cased-component>
<camel-cased-component></camel-cased-component>
<pascal-cased-component></pascal-cased-component>
```

Sin embargo, cuando usamos plantillas de _cadenas de texto_ , no estamos sujetos a las restricciones de mayúsculas/minúsculas del HTML.Esto significa que incluso en la plantilla, puede hacer referencia a sus componentes utilizando:

- kebab-case
- camelCase o kebab-case si el componente ha sido definido utilizando camelCase
- kebab-case, camelCase or PascalCase si el componente ha sido definido utilizando PascalCase

``` js
components: {
  'kebab-cased-component': { /* ... */ },
  camelCasedComponent: { /* ... */ },
  PascalCasedComponent: { /* ... */ }
}
```

``` html
<kebab-cased-component></kebab-cased-component>

<camel-cased-component></camel-cased-component>
<camelCasedComponent></camelCasedComponent>

<pascal-cased-component></pascal-cased-component>
<pascalCasedComponent></pascalCasedComponent>
<PascalCasedComponent></PascalCasedComponent>
```

Esto significa que el PascalCase es la _convención de declaración_ más universal y el kebab-case es la _convención de uso_ más universal.

Si su componente no pasa contenido a través de elementos `slot`, puede incluso hacer que se cierre automáticamente con un `/` después del nombre:

``` html
<my-component/>
```

Una vez más, esto _sólo_ dentro de las plantillas de cadenas de texto, ya que los elementos personalizados de autocierre no son HTML válidos y el analizador nativo de su navegador no los entenderá.

### Componentes recursivos

Los componentes pueden invocarse recursivamente en su propia plantilla. Sin embargo, sólo pueden hacerlo con la opción `name`:

``` js
name: 'unique-name-of-my-component'
```

Cuando registra un componente globalmente usando `Vue.component`, el ID global se establece automáticamente como la opción `name` del componente.

``` js
Vue.component('unique-name-of-my-component', {
  // ...
})
```

Si no se tiene cuidado, los componentes recursivos también pueden conducir a bucles infinitos:

``` js
name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>'
```

Un componente como el anterior resultará en un error de "tamaño máximo de pila excedido", así que asegúrese de que la invocación recursiva es condicional (p.e. use un `v-if` que eventualmente será `false`).

### Referencias circulares entre componentes

Supongamos que está construyendo un árbol de directorios de archivos, como en el Finder o el Explorador de archivos. Puede que tenga un componente `tree-folder` con esta plantilla:

``` html
<p>
  <span>{{ folder.name }}</span>
  <tree-folder-contents :children="folder.children"/>
</p>
```

Y un componente `tree-folder-contents` con esta plantilla:

``` html
<ul>
  <li v-for="child in children">
    <tree-folder v-if="child.children" :folder="child"/>
    <span v-else>{{ child.name }}</span>
  </li>
</ul>
```

Si observa cuidadosamente, verá que estos componentes serán al mismo tiempo descendientes _y_ ancestros el uno del otro en el árbol renderizado - ¡una paradoja! Cuando registramos componentes globalmente con `Vue.component`, dicha paradoja se resuelve automáticamente. Si ése es su caso, puede dejar de leer aquí.

Sin embargo, si está requiriendo/importando componentes usando un __sistema de módulos__, p.e. usando Webpack o Browserify, obtendrá un error:

```
Fallo al montar el componente: la plantilla o función de renderizado no está definida.
```

Para explicar lo que está sucediendo, llamemos a nuestros componentes A y B. El sistema de módulos ve que necesita A, pero primero A necesita a B, pero B necesita a A, pero A necesita a B, etc, etc, etc, etc. Está atascado en un bucle, sin saber cómo resolver completamente ninguno de los dos componentes sin resolver primero el otro. Para arreglar esto, necesitamos darle al sistema de módulos un punto en el que pueda decir: "A necesita a B _eventualmente_, pero no hay necesidad de resolver a B primero".

En nuestro caso, hagamos de este punto el componente `tree-folder`. Sabemos que el hijo que crea la paradoja es el componente `tree-folder-contents`, de modo que esperaremos hasta el hook de ciclo de vida `beforeCreate` para registrarlo:

``` js
beforeCreate: function () {
  this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue')
}
```

¡Problema resuelto!

### Plantillas en línea

Cuando el atributo especial `inline-template` está presente en un componente hijo, el componente usará su contenido interno como su plantilla, en lugar de tratarlo como contenido distribuido. Esto permite una mayor flexibilidad en la creación de plantillas.

``` html
<my-component inline-template>
  <div>
    <p>Estos son compilados como la propia plantilla del componente.</p>
    <p>No contenido de transclusión del padre.</p>
  </div>
</my-component>
```

Sin embargo, `inline-template` hace que el ámbito de su plantilla sea más difícil de entender. Como buena práctica, es preferible definir plantillas dentro del componente usando la opción `template` o en un elemento `template` en un archivo `.vue`.

### X-Templates

Otra forma de definir plantillas es dentro de un elemento `script` con el tipo `text/x-template`, luego referenciar la plantilla con un id. Por ejemplo:

``` html
<script type="text/x-template" id="hello-world-template">
  <p>Hello hello hello</p>
</script>
```

``` js
Vue.component('hello-world', {
  template: '#hello-world-template'
})
```

Esto puede ser útil para demos con plantillas muy grandes o en aplicaciones extremadamente pequeñas, pero en otro caso debe ser evitado, ya que separan las plantillas del resto de la definición del componente.

### Componentes estáticos rápidos con `v-once`

Renderizar elementos HTML simples es muy rápido en Vue, pero a veces querrá tener un componente que incluya **mucho** contenido estático. En esos casos, puede asegurarse que sólo sea evaluado una única vez y luego guardado en caché usando la directiva `v-once` en el elemento raíz, de la siguiente forma:

``` js
Vue.component('terms-of-service', {
  template: '\
    <div v-once>\
      <h1>Terms of Service</h1>\
      ... mucho contenido estático ...\
    </div>\
  '
})
```
