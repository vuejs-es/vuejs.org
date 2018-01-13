---
title: Reactivity in Depth
type: guide
order: 601
---

A llegado la hora de profundizar en el asunto! Una de las características que diferencian a Vue es su discreto sistema de reactividad. Los modelos simplemente son objetos de javascript. Cuando los modifique, se actualizará la vista. Esto hace que el gestor de estados sea simple e intuitivo, pero tambien es importante entender como funciona para prevenir algunos errores comunes. En esta sección, vamos a indagar en algunos detalles de bajo nivel del sistema de reactividad de Vue.

## Como Se Siguen Los Cambios

Cuando se le pasa un objeto de javascript a una instancia de Vue como su opcion `data`, Vue irá a traves de todas sus propiedades y las convertirá a un getter/setter usango [Object.defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty). Esto solo funciona es versiones de ES5 en adelante y es una característica un-shimable, que es por lo que Vue no soporta IE8 ni inferior.

Los getter/setter son invisibles para el usuario, pero por debajo permite a Vue optimizar el seguimiento de dependencias y cambios-modificaciones cuando se accededen a ellas o son modificadas. Una advertencia es que la consola del navegador da diferente formato a los getter/setters cuando los objetos cuando se muestran objetos manipulados, por lo que quizás quieras instalar [vue-devtools](https://github.com/vuejs/vue-devtools) para una obtener un resultado más amigable.

Cada instancia de un componente tiene su correspondiente instancia de un **watcher**, el cual graba cada propiedad "touched" durante el renderizado de dependencias del componente como dependencias. Mas tarde, cuando una dependencia de un setter es lanzada, lo notifica al watcher, lo que provoca que el componente se vuelva a renderizar.

![Reactivity Cycle](/images/data.png)

## Precauciones Detectando Cambios

Debido alas limitaciones del Javascript moderno (y la deprecación de `Object.observe`), Vue **no puede detectar cambios al añadir o eliminar**. Desde que Vue emplea la conversión a través `getters/setters` durante la inicialización de la instancia, una propiedad tiene que estar presenta en el objeto `data` para que Vue pueda convertirlo y hacerlo reactivo. Por ejemplo:

``` js
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` is now reactive

vm.b = 2
// `vm.b` is NOT reactive
```

Vue no permite añadir dinámicamente en el nivel raiz nuevas propiedades reactivas a una instancia ya creada. Sim embargo, se pueden añadir propiedades reactivas a un objeto anidado usando el método `Vue.set(object, key, value)` :

``` js
Vue.set(vm.someObject, 'b', 2)
```

También puede añadir la instancia del método `vm.$set`, el cual es un alias del método global `Vue.set`:

``` js
this.$set(this.someObject, 'b', 2)
```
Algunas veces quizás quiera asignar un número a una de las propiedades de un objeto existente, por ejemplo usando `object.assign()` o `_.extend()`. Sin embargo, las nuevas propiedades añadidas al objeto no aplicarán los cambios. En esos casos, cree un nuevo objeto con las propiedades de ambos, del objeto original y el que quieres añadir:

``` js
// instead of `Object.assign(this.someObject, { a: 1, b: 2 })`
this.someObject = Object.assign({}, this.someObject, { a: 1, b: 2 })
```

También hay advertencias relacionadas con los arrays, sobre los que se ha hablado antes en la [list rendering section](list.html#Caveats).

## Declarando Propiedades Reactivas

Desde que vue no permite añadir dinamicamente propiedades reactivas a la raiz, tienes que inicializar una instancia de Vue declarando todas las propiedades reactivas en la raiz desde el principio, incluso con valores vacíos:

``` js
var vm = new Vue({
  data: {
    // declare message with an empty value
    message: ''
  },
  template: '<div>{{ message }}</div>'
})
// set `message` later
vm.message = 'Hello!'
```

Si no declaras `message` en la opción `data`, Vue le advertirá de que la function de renderizado está está intentando acceder a una propiedad que no existe.

Hay razones técnicas detrás de esta restricción - esto elimina una clase de casos aislados en el sistema de seguimiento de dependencias, y tambien hace a las instancias de Vue tengan una mejor relación con los sistemas de compracion de tipos. Pero también hay una consideración importante en términos de mantenimiento de código: el objeto `data` es como el esquema del estado de tu componente. Declarando todas las propiedades reactivas desde el principio hace que el código del coponente sea más fácil de entender cuando sea revisado o leído más tarde por otro desarrollador.

## Async Update Queue

En el caso de que todavía no se haya dado cuenta, Vue realiza actualizaciones del DOM **asíncronamente**. Todas las veces que un cambio en los datos es observado, abrirá a una cola y almacenará en un buffer todos los cambios en los datos que ocurran en la misma vuelta del bucle. Si el mismo `watcher` es lanzado varias veces, solo será introducido en la cola una vez. Este mecanismo que evita la duplicación es importante para evitar cálculos de manipulaciones del DOM innecesarias. Entonces, en la siguiente vuelta del bucle de eventos, Vue limpia la cola y ejecuta el (no repetido) trabajo actual. Vue internamente usa de forma nativa `Promise.then` y `MutationObserver` para el encolamiento asíncrono y vuelve a `setTimeout(fn, 0)`.

Por ejemplo, cuando asigne `vm.someData = 'new value'`, el componente no se volverá a renderizar inmediatamente. Se actualizará en la siguiente vuelta, cuando la cola es limpiada. La mayoría de las veces no necesitamos preocuparnos de esto, pero puede ser útil cuando quieres hacer algo que depende del estado del DOM posterior a la actualización. Aunque en general vue.js anima a los desarrolladores a pensar en un "data-driven" moderno y evitar tocar el DOM directamente, a veces pueda ser necesario mancharse las manos. Para esperar hasta que vue.js haya finalizado la actualización del DOM después de un cambio en los datos, puede usar `Vue.nextTick(callback)` justo después de que los datos hayan cambiado. El callback será llamado después de que el DOM haya sido actualizado. Por ejemplo:

``` html
<div id="example">{{ message }}</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    message: '123'
  }
})
vm.message = 'new message' // change data
vm.$el.textContent === 'new message' // false
Vue.nextTick(function () {
  vm.$el.textContent === 'new message' // true
})
```

También está la instancia del método `vm.$nextTick()`, el cual es especialmente útil dentro de los componentes, porque no necesita `vue` globalmente y el contexto del callback `this` será automáticamente ligado a la instancia actual de Vue:

``` js
Vue.component('example', {
  template: '<span>{{ message }}</span>',
  data: function () {
    return {
      message: 'not updated'
    }
  },
  methods: {
    updateMessage: function () {
      this.message = 'updated'
      console.log(this.$el.textContent) // => 'not updated'
      this.$nextTick(function () {
        console.log(this.$el.textContent) // => 'updated'
      })
    }
  }
})
```
