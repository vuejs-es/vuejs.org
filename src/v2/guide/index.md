---
title: Introducción
type: guide
order: 2
---

## ¿Qué es Vue.js?

Vue (pronunciado viú) es un **framework progresivo** para construir interfaces de usuario. A diferencia de otros frameworks monolíticos, Vue está diseñado desde cero para ser incrementalmente adaptable. La librería núcleo está enfocada en la capa de vista únicamente, y es muy sencillo de empezar a usarla e integrarla con otras librerías o proyectos existentes. Por otro lado, Vue también es perfectamente capaz de manejar Aplicaciones de Una Página cuando se usa junto con [herramientas modernas](single-file-components.html) y [librerías de apoyo](https://github.com/vuejs/awesome-vue#libraries--plugins).

Si usted es un desarrollador front-end experimentado y quiere saber cómo Vue se compara con otras librerías/frameworks, revise la [Comparación con otros Frameworks](comparison.html).

## ¿Cómo empezar?

<p class="tip">La guía oficial asume un conocimiento nivel intermedio de HTML, CSS y JavaScript. Si es completamente nuevo en el desarrollo front-end, probablemente no es buena idea saltar de primera vez a un framework - ¡primero aprenda los conceptos básicos y luego regrese! Conocimientos previos con otros frameworks son útiles, pero no requeridos.</p>

La manera más sencilla de probar Vue.js es usar el [ejemplo Hola Mundo en JSFiddle](https://jsfiddle.net/chrisvfritz/50wL7mdz/). Siéntase libre de abrirlo en otra pestaña y seguirlo a medida que explicamos algunos ejemplos básicos. O puede simplemente <a href="https://gist.githubusercontent.com/chrisvfritz/7f8d7d63000b48493c336e48b3db3e52/raw/ed60c4e5d5c6fec48b0921edaed0cb60be30e87c/index.html" target="_blank" download="index.html">crear un archivo <code>index.html</code></a> e incluir Vue con:

``` html
<script src="https://unpkg.com/vue"></script>
```

La página de [Instalación](installation.html) ofrece más opciones para instalar Vue. Tenga en cuenta que **no** recomendamos que los principiantes inicien con `vue-cli`, especialmente si no le son familiares las herramientas de compilación basadas en Node.js.

## Renderizado declarativo

En el núcleo de Vue.js hay un sistema que nos permite renderizar datos de forma declarativa en el DOM usando una sintaxis de plantilla muy sencilla:

``` html
<div id="app">
  {{ message }}
</div>
```
``` js
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```
{% raw %}
<div id="app" class="demo">
  {{ message }}
</div>
<script>
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
{% endraw %}

¡Ya hemos creado nuestra primera aplicación Vue! Se parece mucho a renderizar una plantilla de cadenas de texto, pero Vue ha hecho mucho trabajo por detrás. Ahora los datos y el DOM están enlazados, y todo es **reactivo**. ¿Cómo lo sabemos? Sólo inicie la consola JavaScript de su navegador (ahora mismo, en esta página) y cambie el valor de `app.message`. En seguida verá que el ejemplo renderizado anteriormente se actualiza acordemente.

Además de la interpolación de texto, podemos asignar atributos de elementos de la siguiente forma:

``` html
<div id="app-2">
  <span v-bind:title="message">
    Hover your mouse over me for a few seconds
    to see my dynamically bound title!
  </span>
</div>
```
``` js
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: 'You loaded this page on ' + new Date().toLocaleString()
  }
})
```
{% raw %}
<div id="app-2" class="demo">
  <span v-bind:title="message">
    Hover your mouse over me for a few seconds to see my dynamically bound title!
  </span>
</div>
<script>
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: 'You loaded this page on ' + new Date().toLocaleString()
  }
})
</script>
{% endraw %}

Aquí hemos encontrado algo nuevo. El atributo `v-bind` que está viendo se llama **directiva**. Las directivas tienen el prefijo `v-` para indicar que son atributos especiales ofrecidos por Vue, y como ya ha adivinado, aplican comportamientos reactivos especiales al DOM. Aquí básicamente estamos diciendo "mantenga el atributo `title` del elemento, al día con el valor de la propiedad `message` en la instancia Vue".

Si de nuevo inicia la consola JavaScript del navegador y escribe `app2.message = 'un nuevo mensaje'`, verá una vez más que el HTML asignado - en este caso el atributo `title` - ha sido actualizado.

## Condicionales y bucles

También es bastante sencillo intercambiar la presencia de un elemento:

``` html
<div id="app-3">
  <span v-if="seen">Now you see me</span>
</div>
```

``` js
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```

{% raw %}
<div id="app-3" class="demo">
  <span v-if="seen">Now you see me</span>
</div>
<script>
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
</script>
{% endraw %}

Inténtelo usted ingresando `app3.seen = false` en la consola. Debe ver como desaparece el mensaje.

Este ejemplo demuestra que podemos asignar datos no sólo a texto y atributos, sino también a la **estructura** del DOM. Más aún, Vue también nos ofrece un sistema de [efectos de transición](transitions.html) que podemos aplicar automáticamente cuando los elementos son insertados/actualizados/removidos por Vue.

Existen más directivas, cada una con su funcionalidad especial. Por ejemplo, la directiva `v-for` puede ser usada para mostrar una lista de elementos usando los datos de un Array:

``` html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```
``` js
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Learn JavaScript' },
      { text: 'Learn Vue' },
      { text: 'Build something awesome' }
    ]
  }
})
```
{% raw %}
<div id="app-4" class="demo">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
<script>
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Learn JavaScript' },
      { text: 'Learn Vue' },
      { text: 'Build something awesome' }
    ]
  }
})
</script>
{% endraw %}

En la consola, ingrese `app4.todos.push({ text: 'New item' })`. Debe ver aparecer un nuevo elemento al final de la lista.

## Controlando los datos ingresados por usuario

Para permitir a los usuarios interactuar con su aplicación, podemos usar la directiva `v-on` para enlazar _event listeners_ que invocan métodos en nuestra instancia Vue:

``` html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>
```
``` js
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```
{% raw %}
<div id="app-5" class="demo">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>
<script>
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
</script>
{% endraw %}

Note que en el método simplemente actualizamos el estado de nuestra aplicación sin tocar el DOM - todas las manipulaciones al DOM son controladas por Vue, y el código que usted escribe se enfoca en la lógica. 

Vue también ofrece la directiva `v-model` que hace que una vinculación bi-direccional entre un campo de formulario y el estado de la aplicación sea muy sencilla:

``` html
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```
``` js
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```
{% raw %}
<div id="app-6" class="demo">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
<script>
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
{% endraw %}

## Componiendo con componentes

El sistema de componentes es otro concepto importante en Vue, ya que es una abstracción que nos permite construir aplicaciones a gran escala compuestas por componentes pequeños, auto-contenidos, y a menudo re-usables. Si pensamos en ello, casi cualquier tipo de interfaz de aplicación puede ser abstraída en un árbol de componentes:

![Árbol de componentes](/images/components.png)

En Vue, un componente es esencialmente una instancia Vue con opciones predefinidas. Registrar un componente en Vue es muy sencillo:

``` js
// Define un componente llamado todo-item
Vue.component('todo-item', {
  template: '<li>This is a todo</li>'
})
```

Ahora puede componerlo en la plantilla de otro componente:

``` html
<ol>
  <!-- Crea una instancia del componente todo-item -->
  <todo-item></todo-item>
</ol>
```

Pero esto renderizaría el mismo texto para cada tarea, lo cual no es nada interesante. Deberíamos ser capaces de pasar datos del ámbito del padre a sus componentes hijos. Modifiquemos la definición del componente para permitirle aceptar un [prop](components.html#Props):

``` js
Vue.component('todo-item', {
  // El componente todo-item ahora acepta un 
  // "prop", que es como un atributo personalizado.
  // Este prop es llamado todo.
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

Ahora podemos pasar la tarea a cada componente repetido usando `v-bind`:

``` html
<div id="app-7">
  <ol>
    <!--
      Ahora le proveemos a cada todo-item el objeto todo
      que representa, de modo que su contenido sea dinámico
      También tenemos que dotar a cada componente de un "key",
      que se explicará más adelante.
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id">
    </todo-item>
  </ol>
</div>
```
``` js
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { id: 0, text: 'Vegetables' },
      { id: 1, text: 'Cheese' },
      { id: 2, text: 'Whatever else humans are supposed to eat' }
    ]
  }
})
```
{% raw %}
<div id="app-7" class="demo">
  <ol>
    <todo-item v-for="item in groceryList" v-bind:todo="item" :key="item.id"></todo-item>
  </ol>
</div>
<script>
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { id: 0, text: 'Vegetables' },
      { id: 1, text: 'Cheese' },
      { id: 2, text: 'Whatever else humans are supposed to eat' }
    ]
  }
})
</script>
{% endraw %}

Este es sólo un ejemplo inventado, pero hemos logrado separar nuestra aplicación en dos unidades más pequeñas, y el hijo está razonablemente bien desacoplado de su padre gracias a la interfaz de props. Ahora podemos mejorar aún más nuestro componente `<todo-item>` con una plantilla más compleja y lógica propia sin afectar la aplicación principal.

En una aplicación grande, es necesario dividir la aplicación entera en componentes para hacer que el desarrollo sea manejable. Hablaremos mucho más sobre componentes [más adelante](components.html), pero aquí hay un ejemplo (imaginario) de cómo podría ser la plantilla de una aplicación con componentes:

``` html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

### Relación con los elementos personalizados

Puede haber notado que los componentes Vue son muy similares a los **Elementos Personalizados**, los cuales son parte de la [Especificación de Componentes Web](https://www.w3.org/wiki/WebComponents/). Esto es porque la sintaxis de componentes de Vue es ligeramente modelada a partir de ésa especificación. Por ejemplo, los componentes Vue implementan el [API de Slots](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md) y el atributo especial `is`. Sin embargo, hay algunas diferencias clave:

1. La Especificación de Componentes Web está aún en estado de borrador, y no está implementada nativamente en todos los navegadores. En comparación, los componentes Vue no requieren polyfills y funcionan consistentemente en todos los navegadores soportados (IE9 y superior). Cuando es necesario, los componentes Vue pueden ser envueltos en un elemento nativo personalizado.

2. Los componentes Vue ofrecen características importantes que no están disponibles en elementos personalizados, más notablemente el flujo de datos a través de componentes, comunicación de eventos personalizados e integraciones con herramientas de compilación.

## ¿Listo para más?

Sólo hemos presentado brevemente las características más básicas del núcleo de Vue.js - el resto de esta guía va a cubrirlos y otras características avanzadas con detalles mucho más finos, de modo que ¡asegúrese de leerlo todo!.
