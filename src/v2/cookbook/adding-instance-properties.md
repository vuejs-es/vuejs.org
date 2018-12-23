---
title: Agregar propiedades de instancia
type: cookbook
order: 1.1
---

## Ejemplo Simple

Pueden haber datos/utilidades que le gustaría usar en muchos componentes, pero no desea [contaminar el alcance global](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch3.md). En estos casos, puede ponerlos a disposición de cada instancia de Vue definiéndolos en el prototype:

``` js
Vue.prototype.$appName = 'My App'
```

Ahora `$appName` está disponible en todas las instancias de Vue, incluso antes de su creación. Si ejecutamos:

``` js
new Vue({
  beforeCreate: function () {
    console.log(this.$appName)
  }
})
```

A continuación, `"My App"` se registrará en la consola.

## La importancia de determinar el alcance de las propiedades de una instancia

Tal vez se esté preguntando:

> ¿Por qué `appName` comienza con `$`? ¿Es eso importante? ¿Qué es lo que hace?

Aquí no hay magia. `$` es una convención que Vue utiliza para propiedades que están disponibles para todas las instancias. Esto evita conflictos con cualquier dato definido, propiedades calculadas o métodos.

> ¿Conflictos? ¿Qué quieres decir?"

Otra gran pregunta! Si se establece:

``` js
Vue.prototype.appName = 'My App'
```

Entonces, ¿qué espería que se registre más abajo?

``` js
new Vue({
  data: {
    // ¡Uh oh - appName es *además* el nombre de la
    // propiedad de la instancia que definimos!
    appName: 'The name of some other app'
  },
  beforeCreate: function () {
    console.log(this.appName)
  },
  created: function () {
    console.log(this.appName)
  }
})
```

Sería `"The name of some other app"`, luego `"My App"`, porque `this.appName`  es sobreescrito ([o algo parecido](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md)) por `data` cuando se crea la instancia. Las propiedades de instancia se analizan con `$` para evitarlo. Incluso puede usar su propia convención si lo desea, como `$_appName` o `ΩappName`, para evitar incluso conflictos con plugins o futuras características.

## Ejemplo del mundo real: Reemplazar el recurso Vue por Axios

Digamos que está reemplazando el [ahora ya retirado Vue Resource](https://medium.com/the-vue-point/retiring-vue-resource-871a82880af4). Usted realmente disfrutó accediendo a los métodos de petición a través de `this.$http` y desea hacer lo mismo con Axios.

Todo lo que tiene que hacer es incluir Axios en su proyecto:

``` html
<script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.15.2/axios.js"></script>

<div id="app">
  <ul>
    <li v-for="user in users">{{ user.name }}</li>
  </ul>
</div>
```

Alias de `axios` para `Vue.prototype.$http`:

``` js
Vue.prototype.$http = axios
```

Y ahora podrá usar métodos como `this.$http.get` en cualquier instancia de Vue:

``` js
new Vue({
  el: '#app',
  data: {
    users: []
  },
  created () {
    var vm = this
    this.$http.get('https://jsonplaceholder.typicode.com/users')
      .then(function (response) {
        vm.users = response.data
      })
  }
})
```

## El contexto de los métodos en el prototype

En caso de que no lo sepa, los métodos añadidos a un prototype en JavaScript ganan el contexto de la instancia. Esto significa que pueden usar `this` para acceder a datos, propiedades calculadas, métodos o cualquier otra cosa definida en la instancia.

Aprovechemos esto en un método `$reverseText`:

``` js
Vue.prototype.$reverseText = function (propertyName) {
  this[propertyName] = this[propertyName].split('').reverse().join('')
}

new Vue({
  data: {
    message: 'Hello'
  },
  created: function () {
    console.log(this.message)    // => "Hello"
    this.$reverseText('message')
    console.log(this.message)    // => "olleH"
  }
})
```

Tenga en cuenta que la vinculación de contexto __no funcionará__ si utiliza una función de flecha ES6/2015, ya que se vinculan implícitamente con su ámbito superior. Esto significa en la versión de la función de flecha:

``` js
Vue.prototype.$reverseText = propertyName => {
  this[propertyName] = this[propertyName].split('').reverse().join('')
}
```

Podría arrojar un error:

``` log
Uncaught TypeError: Cannot read property 'split' of undefined
```

## Cuándo evitar este patrón

Siempre y cuando se esté atento a las propiedades de prototype, el uso de este patrón es bastante seguro, e improbable que produzca errores.

Sin embargo, a veces puede causar confusión con otros desarrolladores. Podrían ver `this.$http`, por ejemplo, y pensar, "¡Oh, yo no sabía de esta característica de Vue! Luego cambian a un proyecto diferente y se confunden al encontrar que `this.$http` devuelve un valor indefinido. O, tal vez quieran buscar en Google cómo hacer algo, pero no pueden encontrar resultados porque no se dan cuenta de que están usando Axios bajo un alias.

___La conveniencia viene a costa de la claridad.___ ¿ Cuando se mira un componente, es imposible saber de dónde viene `$http` realmente? ¿Un plugin? ¿Un compañero de trabajo?

Entonces, ¿cuáles son las alternativas?

## Patrones alternativos

### Cuando no se esta utilizando un sistema de módulos

En aplicaciones __sin__ sistema de módulos (por ejemplo, a través de Webpack o Browserify), hay un patrón que se utiliza a menudo con cualquier frontend mejorado con JavaScript: un objeto `App` global.

Si lo que quiere añadir no tiene nada que ver con Vue específicamente, esto puede ser una buena alternativa por considerar. Aquí hay un ejemplo:

``` js
var App = Object.freeze({
  name: 'My App',
  description: '2.1.4',
  helpers: {
    // Esta es una versión puramente funcional
    // del método $reverseText que vimos anteriormente
    reverseText: function (text) {
      return text.split('').reverse().join('')
    }
  }
})
```

<p class="tip">Si ha levantado una ceja con `Object.freeze`, lo que hace es evitar que el objeto se modifique en el futuro. Esto esencialmente hace que todas sus propiedades sean constantes, protegiéndole de futuros errores de estado.</p>

Ahora el origen de estas propiedades compartidas es más obvio: hay un objeto `App` definido en algún lugar de la aplicación. Para encontrarlo, los desarrolladores pueden realizar una búsqueda en todo el proyecto.

Otra ventaja es que `App` ahora puede ser usado _en cualquier lugar_ en su código, ya sea que esté relacionado con Vue o no. Esto incluye adjuntar valores directamente a las opciones de instancia, en lugar de tener que introducir una función para acceder a las propiedades en `this`:

``` js
new Vue({
  data: {
    appVersion: App.version
  },
  methods: {
    reverseText: App.helpers.reverseText
  }
})
```

### Cuando se utiliza un sistema de módulos

Cuando se tiene acceso a un sistema de módulos, se puede organizar fácilmente el código compartido en módulos y, a continuación, requerir o importar (`require`/`import`) esos módulos dondequiera que se necesiten. Este es el epítome de lo explícito, porque en cada archivo se obtiene una lista de dependencias. Se sabe _exactamente_ de dónde vino cada uno.

Aunque ciertamente es más verboso, este enfoque es definitivamente el más mantenible, especialmente cuando se trabaja con otros desarrolladores y/o se construye una aplicación de gran tamaño.
