---
type: api
---

## Configuración Global

`Vue.config` es un objeto que contiene las configuraciones globales de Vue. Puede modificar las propiedades enumeradas a continuación antes de iniciar su aplicación:

### silent

- **Tipo:** `boolean`

- **Por defecto:** `false`

- **Uso:**

  ``` js
  Vue.config.silent = true
  ```

  Suprime todos los registros y advertencias de Vue.

### optionMergeStrategies

- **Tipo:** `{ [key: string]: Function }`

- **Por defecto:** `{}`

- **Uso:**

  ``` js
  Vue.config.optionMergeStrategies._my_option = function (parent, child, vm) {
    return child + 1
  }

  const Profile = Vue.extend({
    _my_option: 1
  })

  // Profile.options._my_option = 2
  ```

  Define estrategias de fusión personalizadas para las opciones.

  La estrategia de fusión recibe el valor de esa opción definida en las instancias padre e hijo como primer y segundo argumento, respectivamente. La instancia Vue de contexto se pasa como tercer argumento.

- **Ver también:** [Estrategias de Fusión Personalizadas](../guide/mixins.html#Estrategias-de-Fusión-Personalizadas)

### devtools

- **Tipo:** `boolean`

- **Por defecto:** `true` (`false` en compilaciones de producción)

- **Uso:**

  ``` js
  // asegúrese de configurarlo de forma síncrona inmediatamente después de cargar Vue
  Vue.config.devtools = true
  ```

  Configura el permiso de inspección de [vue-devtools](https://github.com/vuejs/vue-devtools). El valor por defecto de esta opción es `true` en compilaciones de desarrollo y `false` en compilaciones de producción. Puede configurarlo a `true` para habilitar la inspección en compilaciones de producción.

### errorHandler

- **Tipo:** `Function`

- **Por defecto:** Lanza un error al instante

- **Uso:**

  ``` js
  Vue.config.errorHandler = function (err, vm) {
    // manejar error
  }
  ```

  Asigna un manejador para errores no detectados durante el renderizado y la observación del componente. Se llama al manejador con el error y la instancia Vue.

  > Esta opción utiliza [Sentry](https://sentry.io), un servicio de seguimiento de errores que cuenta con [integración oficial](https://sentry.io/for/vue/).

### ignoredElements

- **Tipo:** `Array<string>`

- **Por defecto:** `[]`

- **Uso:**

  ``` js
  Vue.config.ignoredElements = [
    'my-custom-web-component', 'another-web-component'
  ]
  ```

  Ignora elementos personalizados definidos fuera de Vue (p. ej., si usa las APIs de Web Components). De lo contrario, lanzará una advertencia _`Unknown custom element`_ (_Elemento personalizado desconocido_), asumiendo que ha olvidado registrar un componente global o que ha escrito mal un nombre.

### keyCodes

- **Tipo:** `{ [key: string]: number | Array<number> }`

- **Por defecto:** `{}`

- **Uso:**

  ``` js
  Vue.config.keyCodes = {
    v: 86,
    f1: 112,
    mediaPlayPause: 179,
    up: [38, 87]
  }
  ```
  
  Define alias de teclas personalizadas para v-on.

## Global API

<h3 id="Vue-extend">Vue.extend( options )</h3>

- **Argumentos:**
  - `{Object} options`

- **Modo de uso:**

  Crea una "sub-clase" del constructor base de Vue. El argumento debe ser un objeto que contenga las opciones del componente.

  El caso especial a notar aquí es la opción `data` - debe ser una función cuando se usa junto a `Vue.extend()`.

  ``` html
  <div id="mount-point"></div>
  ```

  ``` js
  // crea el constructor
  var Profile = Vue.extend({
    template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
    data: function () {
      return {
        firstName: 'Walter',
        lastName: 'White',
        alias: 'Heisenberg'
      }
    }
  })
  // crea una instancia de Profile y lo monta en un elemento
  new Profile().$mount('#mount-point')
  ```

  Resultará en:

  ``` html
  <p>Walter White aka Heisenberg</p>
  ```

- **Ver También:** [Componentes](../guide/components.html)

<h3 id="Vue-nextTick">Vue.nextTick( [callback, context] )</h3>

- **Argumentos:**
  - `{Function} [callback]`
  - `{Object} [context]`

- **Modo de Uso:**

  Detiene *callback* hasta el siguiente ciclo de actualización del DOM para ser ejecutado. Úselo imediatamente después de que haya cambiado datos para esperar la actualización de DOM.

  ``` js
  // modificar datos
  vm.msg = 'Hello'
  // el DOM no ha sido actualizado todavía
  Vue.nextTick(function () {
    // el DOM ha sido actualizado
  })
  ```

  > Nuevo en 2.1.0: retorna un *Promise* si no se ha especificado una función *callback* y en el ambiente de ejecución los *Promise* son soportados.

- **Vea También:** [Cola de Actualización Asíncrona](../guide/reactivity.html#Cola-de-Actualización-Asíncrona)

<h3 id="Vue-set">Vue.set( object, key, value )</h3>

- **Argumentos:**
  - `{Object} object`
  - `{string} key`
  - `{any} value`

- **Retorna:** el valor asignado.

- **Modo de Uso:**

  Asigna un valor a una propiedad en un objeto. Si el objeto es reactivo, asegúrese que la propiedad haya sido creada de forma reactiva y que active actualizaciones de vista. Esto se usa principalmente para resolver el problema que Vue no puede detectar adiciones de propiedades en objetos.

  **Tenga en cuenta que el ojeto no puede ser una instancia Vue, o el objeto de datos raíz de una instancia Vue.**

- **Ver También:** [Reactividad en Profundidad](../guide/reactivity.html)

<h3 id="Vue-delete">Vue.delete( object, key )</h3>

- **Argumentos:**
  - `{Object} object`
  - `{string} key`

- **Modo de Uso:**

  Elimina una propiedad de un objeto. Si el objeto es reactivo, se asegura que dicha eliminación active las actualizaciones de vista. Esto se usa principalmente para resolver el problema que Vue no puede detectar eliminaciones de propiedades en objetos, pero usted tendrá muy poca necesidad de usarlo.

  **Tenga en cuenta que el ojeto no puede ser una instancia Vue, o el objeto de datos raíz de una instancia Vue.**

- **Ver También:** [Reactividad a Profundidad](../guide/reactivity.html)

<h3 id="Vue-directive">Vue.directive( id, [definition] )</h3>

- **Argumentos:**
  - `{string} id`
  - `{Function | Object} [definition]`

- **Modo de Uso:**

  Registra u obtiene una directiva global.

  ``` js
  // registro
  Vue.directive('my-directive', {
    bind: function () {},
    inserted: function () {},
    update: function () {},
    componentUpdated: function () {},
    unbind: function () {}
  })

  // registro (directiva de función sencilla)
  Vue.directive('my-directive', function () {
    // será llamada en `bind` y `update`
  })

  // getter, retorna la definición de la directiva si ésta
  // se encuentra registrada
  var myDirective = Vue.directive('my-directive')
  ```

- **Vea También:** [Directivas personalizadas](../guide/custom-directive.html)

<h3 id="Vue-filter">Vue.filter( id, [definition] )</h3>

- **Argumentos:**
  - `{string} id`
  - `{Function} [definition]`

- **Modo de Uso:**

  Registra u obtiene un filtro global.

  ``` js
  // registro
  Vue.filter('my-filter', function (value) {
    // retorna el valor procesado
  })

  // getter, retorna el filtro si éste
  // se encuentra registrado
  var myFilter = Vue.filter('my-filter')
  ```

<h3 id="Vue-component">Vue.component( id, [definition] )</h3>

- **Argumentos:**
  - `{string} id`
  - `{Function | Object} [definition]`

- **Modo de Uso:**

  Registra u obtiene un componente global. El registro también asigna automáticamente el `name` del componente con el `id` especificado.

  ``` js
  // registra un constructor extendido
  Vue.component('my-component', Vue.extend({ /* ... */ }))

  // registra un objeto de opciones (automáticamente llama a Vue.extend)
  Vue.component('my-component', { /* ... */ })

  // obtiene un componente ya registrado (siempre retorna el constructor)
  var MyComponent = Vue.component('my-component')
  ```

- **Vea También:** [Componentes](../guide/components.html)

<h3 id="Vue-use">Vue.use( plugin )</h3>

- **Argumentos:**
  - `{Object | Function} plugin`

- **Modo de Uso:**

  Instala un plugin de Vue. Si el plugin es un Objeto, debe exponer un método `install`. Si es una función en sí, será tratada como el método de instalación. El método de instalación será llamado con Vue como argumento.

  Cuando este método es llamado en el mismo plugin múltiples veces, será instalado únicamente una vez.

- **Vea También:** [Plugins](../guide/plugins.html)

<h3 id="Vue-mixin">Vue.mixin( mixin )</h3>

- **Argumentos:**
  - `{Object} mixin`

- **Modo de Uso:**

  Aplica un mixin globalmente, lo cual afecta cada instancia de Vue creada después de eso. Esto puede ser usado por autores de plugins para injectar comportamiento personalizado a los componentes. **No es recomendado en código de aplicación**.

- **Vea También:** [Mixins Globales](../guide/mixins.html#Mixins-Globales)

<h3 id="Vue-compile">Vue.compile( template )</h3>

- **Argumentos:**
  - `{string} template`

- **Modo de Uso:**

  Compila una plantilla string dentro de una función de renderizado. **Sólo disponible en la versión independiente.**

  ``` js
  var res = Vue.compile('<div><span>{{ msg }}</span></div>')

  new Vue({
    data: {
      msg: 'hello'
    },
    render: res.render,
    staticRenderFns: res.staticRenderFns
  })
  ```

- **Vea También:** [Funciones de Renderizado](../guide/render-function.html)

## Opciones / Datos

### data

- **Tipo:** `Object | Function`

- **Restricción:** Únicamente acepta `Function` cuando es usado en la definición de un componente.

- **Detalles:**

  Es el objeto de datos para la instancia Vue. Vue convertirá recursivamente sus propiedades en *getters/setters* para hacerlo "reactivo". **El objeto debe ser plano**: objetos nativos como los objetos del API del navegador y propiedades de prototipo son ignorados. Una regla básica es que los datos deben ser sólo datos - no es recomendable observar objetos con su propio comportamiento dependiente del estado.

  Una vez esté siendo observado, usted ya no podrá agregar propiedades reactivas al objeto de datos raíz. De modo que es recomendado declarar todas las propiedades reactivas de raíz desde el inicio, antes de crear la instancia.

  Luego que la instancia haya sido creada, el objeto de datos original puede ser accedido como `vm.$data`. La instancia de Vue también funciona como proxy de todas las propiedades halladas en el objeto de datos, así, `vm.a` será equivalente a `vm.$data.a`.

  Las propiedades que inicien con `_` o `$` **no tendrán** versión proxy ya que pueden entrar en conflicto con propiedades internas de Vue y métodos de API. Tendrá que accesarlas como `vm.$data._property`. 

  Cuando esté definiendo un **componente**, `data` debe ser declarada como una función que retorna el objeto de datos inicial, ya que serán creadas muchas instancias con la misma definción. Si usáramos un objeto plano para `data`, ¡ese mismo objeto será **compartido por referencia** a través de todas las instancias creadas! Al proveer una función para `data`, cada vez que una instancia sea creada, sencillamente podemos llamarla para que nos de una copia fresca de los datos iniciales.

  Si se requiere, se puede obtener un clon a profundidad del objeto original pasando `vm.$data` a un `JSON.parse(JSON.stringify(...))`.

- **Ejemplo:**

  ``` js
  var data = { a: 1 }

  // creación de instancia directa
  var vm = new Vue({
    data: data
  })
  vm.a // -> 1
  vm.$data === data // -> true

  // se debe usar una función de datos en Vue.extend()
  var Component = Vue.extend({
    data: function () {
      return { a: 1 }
    }
  })
  ```

  <p class="tip">Tenga en cuenta que __usted no debe usar una función flecha dentro de la propiedad `data`__ (p.e. `data: () => { return { a: this.myProp }}`). La razón es que las funciones flecha asignan el contexto del padre a `this`, de modo que `this` no será la instancia Vue como se esperaría y una expresión como `this.myProp` será indefinido.</p>

- **Vea También:** [Reactividad en profundidad](../guide/reactivity.html)

### props

- **Tipo:** `Array<string> | Object`

- **Detalles:**

  Una lista/mapa de atributos que son expuestos para aceptar datos del componente padre. Tiene una sintaxis sencilla basada en Arrays y una sintaxis alternativa basada en objetos que le permite realizar configuraciones avanzadas como validar tipos de datos, validación personalizada y valores por defecto.

- **Ejemplo:**

  ``` js
  // sintaxis sencilla
  Vue.component('props-demo-simple', {
    props: ['size', 'myMessage']
  })

  // sintaxis de objeto con validación
  Vue.component('props-demo-advanced', {
    props: {
      // sólo validación de tipo
      height: Number,
      // validación de tipo junto a validaciones personalizadas
      age: {
        type: Number,
        default: 0,
        required: true,
        validator: function (value) {
          return value >= 0
        }
      }
    }
  })
  ```

- **Vea También:** [Props](../guide/components.html#Props)

### propsData

- **Tipo:** `{ [key: string]: any }`

- **Restricción:** Sólo respetado en creación de instancias con `new`.

- **Detalles:**

  Pasa los props a una instancia durante su creación. Se usa principalmente para lograr que las pruebas unitarias sean más sencillas.

- **Ejemplo:**

  ``` js
  var Comp = Vue.extend({
    props: ['msg'],
    template: '<div>{{ msg }}</div>'
  })

  var vm = new Comp({
    propsData: {
      msg: 'hello'
    }
  })
  ```

### computed

- **Tipo:** `{ [key: string]: Function | { get: Function, set: Function } }`

- **Detalles:**

  Son las propiedades calculadas a ser integradas en la instancia Vue. Todos los *getters* y *setters* tienen su contexto `this` automáticamente asignado a la instancia Vue.

  <p class="tip">Tenga en cuenta que __usted no debe usar funciones flecha para definir una propiedad calculada__ (p.e. `aDouble: () => this.a * 2`). La razón es que las funciones flecha asignan el contexto padre a `this`, de modo que `this` no será la instancia Vue como se esperaría y `this.a` será indefinido.</p>

  Las propiedades calculadas son guardadas en caché, y sólo serán recalculadas cuando cambien sus dependencias reactivas.

- **Ejemplo:**

  ```js
  var vm = new Vue({
    data: { a: 1 },
    computed: {
      // solo get, necesita sólo una función
      aDouble: function () {
        return this.a * 2
      },
      // tanto get como set
      aPlus: {
        get: function () {
          return this.a + 1
        },
        set: function (v) {
          this.a = v - 1
        }
      }
    }
  })
  vm.aPlus   // -> 2
  vm.aPlus = 3
  vm.a       // -> 2
  vm.aDouble // -> 4
  ```

- **Vea También:**
  - [Propiedades calculadas](../guide/computed.html)

### methods

- **Tipo:** `{ [key: string]: Function }`

- **Detalles:**

  Métodos para ser integrados en la instancia Vue. Puede acceder a estos métodos directamente en la instancia Vue, o usarlos en expresiones de directiva. Todos los métodos tendrán su contexto `this` asignado automáticamente a la instancia Vue.

  <p class="tip">Tenga en cuenta que __no debe usar funciones flecha para definir un método__ (p.e. `plus: () => this.a++`). La razón es que las funciones flecha asignan el contexto padre a `this`, de modo que `this` no será la instancia Vue como se esperaría y `this.a` será indefinido.</p>

- **Ejemplo:**

  ```js
  var vm = new Vue({
    data: { a: 1 },
    methods: {
      plus: function () {
        this.a++
      }
    }
  })
  vm.plus()
  vm.a // 2
  ```

- **Vea También:** [Métodos y manejo de eventos](../guide/events.html)

### watch

- **Tipo:** `{ [key: string]: string | Function | Object }`

- **Detalles:**

  Es un objeto donde las propiedades representan expresiones a observar y lo valores son los *callbacks* correspondientes. El valor también puede ser el nombre de un método como string, o un objeto que contenga opciones adicionales. La instancia Vue llamará `$watch()` por cada propiedad en el objeto en el momento de instanciación.

- **Ejemplo:**

  ``` js
  var vm = new Vue({
    data: {
      a: 1,
      b: 2,
      c: 3
    },
    watch: {
      a: function (val, oldVal) {
        console.log('new: %s, old: %s', val, oldVal)
      },
      // nombre de método en string
      b: 'someMethod',
      // observador profundo
      c: {
        handler: function (val, oldVal) { /* ... */ },
        deep: true
      }
    }
  })
  vm.a = 2 // -> nuevo: 2, viejo: 1
  ```

  <p class="tip">Tenga en cuenta que __no debe usar funciones flecha para definir un observador__ (p.e. `searchQuery: newValue => this.updateAutocomplete(newValue)`). La razón es que las funciones flecha asignan el contexto padre a `this`, de modo que `this` no será la instancia Vue como se esperaría y `this.updateAutocomplete` será indefinido.</p>

- **Vea También:** [Métodos de instancia - vm.$watch](#vm-watch)

## Opciones / DOM

### el

- **Tipo:** `string | HTMLElement`

- **Restricción:** sólo respetado en una instancia creada con `new`.

- **Detalles:**

  Provee a la instancia Vue con un elemento existente del DOM para ser montada. Puede ser un string de selector CSS o un elemento HTML.

  Depués que la instancia sea montada, el elemento resuelto se podrá acceder como `vm.$el`.

  Si esta opción está disponible en la instanciación, la instancia inmediatamente iniciará la compilación; de otra forma, el usuario deberá llamar explícitamente a `vm.$mount()` para inicial la compilación manualmente.

  <p class="tip">El elemento elegido únicamente sirve como punto de montura. A contrario de Vue 1.x, el elemento montado será reemplazado con DOM generado por Vue en todos los casos. De modo que no es recomendable montar la instancia raíz en un `<html>` o un `<body>`.</p>

- **Vea También:** [Diagrama de Ciclo de Vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

### template

- **Tipo:** `string`

- **Detalles:**

  Es una plantilla string para ser usada como el código HTML de la instancia Vue. La plantilla **reemplazará** el elemento montado. Cualquier código HTML existente dentro del elemento montado será ignorado, a menos que los slots de distribución de contenido se encuentren presentes en la plantilla.

  Si el string inicia con `#` será usado como un `querySelector` y usará el `innerHTML` del elemento como plantilla string. Esto permite el uso del truco común `<script type="x-template">` para incluir plantillas.

  <p class="tip">Desde una perspectiva de seguridad, usted debería usar únicamente plantillas Vue en las que confía. Nunca use contenido generado por el usuario como plantilla.</p>

- **Vea También:**
  - [Diagrama de Ciclo de Vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)
  - [Distribución de contenido](../guide/components.html#Distribucion-de-Contenido-con-Slots)

### render

  - **Tipo:** `Function`

  - **Detalles:**

    Una alternativa a las plantillas de string, le permite usar el poder programático completo de JavaScript. La función render recibe un método `createElement` como su primer argumento, usado para crear los `VNode`.

    Si el componente es un componente funcional, la función render también recibe un argumento extra `context`, el cual provee acceso a los datos contextuales, ya que los componentes funcionales no poseen instancia.

  - **Vea También:**
    - [Funciones de renderizado](../guide/render-function)

## Options / Lifecycle Hooks

All lifecycle hooks automatically have their `this` context bound to the instance, so that you can access data, computed properties, and methods. This means __you should not use an arrow function to define a lifecycle method__ (e.g. `created: () => this.fetchTodos()`). The reason is arrow functions bind the parent context, so `this` will not be the Vue instance as you expect and `this.fetchTodos` will be undefined.

### beforeCreate

- **Type:** `Function`

- **Details:**

  Called synchronously after the instance has just been initialized, before data observation and event/watcher setup.

- **Vea También:** [Lifecycle Diagram](../guide/instance.html#Lifecycle-Diagram)

### created

- **Type:** `Function`

- **Details:**

  Called synchronously after the instance is created. At this stage, the instance has finished processing the options which means the following have been set up: data observation, computed properties, methods, watch/event callbacks. However, the mounting phase has not been started, and the `$el` property will not be available yet.

- **Vea También:** [Lifecycle Diagram](../guide/instance.html#Lifecycle-Diagram)

### beforeMount

- **Type:** `Function`

- **Details:**

  Called right before the mounting begins: the `render` function is about to be called for the first time.

  **This hook is not called during server-side rendering.**

- **Vea También:** [Lifecycle Diagram](../guide/instance.html#Lifecycle-Diagram)

### mounted

- **Type:** `Function`

- **Details:**

  Called after the instance has just been mounted where `el` is replaced by the newly created `vm.$el`. If the root instance is mounted to an in-document element, `vm.$el` will also be in-document when `mounted` is called.

  **This hook is not called during server-side rendering.**

- **Vea También:** [Lifecycle Diagram](../guide/instance.html#Lifecycle-Diagram)

### beforeUpdate

- **Type:** `Function`

- **Details:**

  Called when the data changes, before the virtual DOM is re-rendered and patched.

  You can perform further state changes in this hook and they will not trigger additional re-renders.

  **This hook is not called during server-side rendering.**

- **Vea También:** [Lifecycle Diagram](../guide/instance.html#Lifecycle-Diagram)

### updated

- **Type:** `Function`

- **Details:**

  Called after a data change causes the virtual DOM to be re-rendered and patched.

  The component's DOM will be in updated state when this hook is called, so you can perform DOM-dependent operations in this hook. However, in most cases you should avoid changing state in this hook, because it may lead to an infinite update loop.

  **This hook is not called during server-side rendering.**

- **Vea También:** [Lifecycle Diagram](../guide/instance.html#Lifecycle-Diagram)

### activated

- **Type:** `Function`

- **Details:**

  Called when a kept-alive component is activated.

  **This hook is not called during server-side rendering.**

- **Vea También:**
  - [Built-in Components - keep-alive](#keep-alive)
  - [Dynamic Components - keep-alive](../guide/components.html#keep-alive)

### deactivated

- **Type:** `Function`

- **Details:**

  Called when a kept-alive component is deactivated.

  **This hook is not called during server-side rendering.**

- **Vea También:**
  - [Built-in Components - keep-alive](#keep-alive)
  - [Dynamic Components - keep-alive](../guide/components.html#keep-alive)

### beforeDestroy

- **Type:** `Function`

- **Details:**

  Called right before a Vue instance is destroyed. At this stage the instance is still fully functional.

  **This hook is not called during server-side rendering.**

- **Vea También:** [Lifecycle Diagram](../guide/instance.html#Lifecycle-Diagram)

### destroyed

- **Type:** `Function`

- **Details:**

  Called after a Vue instance has been destroyed. When this hook is called, all directives of the Vue instance have been unbound, all event listeners have been removed, and all child Vue instances have also been destroyed.

  **This hook is not called during server-side rendering.**

- **Vea También:** [Lifecycle Diagram](../guide/instance.html#Lifecycle-Diagram)

## Options / Assets

### directives

- **Type:** `Object`

- **Details:**

  A hash of directives to be made available to the Vue instance.

- **Vea También:**
  - [Custom Directives](../guide/custom-directive.html)
  - [Assets Naming Convention](../guide/components.html#Assets-Naming-Convention)

### filters

- **Type:** `Object`

- **Details:**

  A hash of filters to be made available to the Vue instance.

- **Vea También:**
  - [`Vue.filter`](#Vue-filter)

### components

- **Type:** `Object`

- **Details:**

  A hash of components to be made available to the Vue instance.

- **Vea También:**
  - [Components](../guide/components.html)

## Options / Misc

### parent

- **Type:** `Vue instance`

- **Details:**

  Specify the parent instance for the instance to be created. Establishes a parent-child relationship between the two. The parent will be accessible as `this.$parent` for the child, and the child will be pushed into the parent's `$children` array.

  <p class="tip">Use `$parent` and `$children` sparringly - they mostly serve as an escape-hatch. Prefer using props and events for parent-child communication.</p>

### mixins

- **Type:** `Array<Object>`

- **Details:**

  The `mixins` option accepts an array of mixin objects. These mixin objects can contain instance options just like normal instance objects, and they will be merged against the eventual options using the same option merging logic in `Vue.extend()`. e.g. If your mixin contains a created hook and the component itself also has one, both functions will be called.

  Mixin hooks are called in the order they are provided, and called before the component's own hooks.

- **Example:**

  ``` js
  var mixin = {
    created: function () { console.log(1) }
  }
  var vm = new Vue({
    created: function () { console.log(2) },
    mixins: [mixin]
  })
  // -> 1
  // -> 2
  ```

- **Vea También:** [Mixins](../guide/mixins.html)

### name

- **Type:** `string`

- **Restriction:** only respected when used as a component option.

- **Details:**

  Allow the component to recursively invoke itself in its template. Note that when a component is registered globally with `Vue.component()`, the global ID is automatically set as its name.

  Another benefit of specifying a `name` option is debugging. Named components result in more helpful warning messages. Also, when inspecting an app in the [vue-devtools](https://github.com/vuejs/vue-devtools), unnamed components will show up as `<AnonymousComponent>`, which isn't very informative. By providing the `name` option, you will get a much more informative component tree.

### extends

- **Type:** `Object | Function`

- **Details:**

  Allows declaratively extending another component (could be either a plain options object or a constructor) without having to use `Vue.extend`. This is primarily intended to make it easier to extend between single file components.

  This is similar to `mixins`, the difference being that the component's own options takes higher priority than the source component being extended.

- **Example:**

  ``` js
  var CompA = { ... }

  // extend CompA without having to call Vue.extend on either
  var CompB = {
    extends: CompA,
    ...
  }
  ```

### delimiters

- **Type:** `Array<string>`

- **default:** `{% raw %}["{{", "}}"]{% endraw %}`

- **Details:**

  Change the plain text interpolation delimiters. **This option is only available in the standalone build.**

- **Example:**

  ``` js
  new Vue({
    delimiters: ['${', '}']
  })

  // Delimiters changed to ES6 template string style
  ```

### functional

- **Type:** `boolean`

- **Details:**

  Causes a component to be stateless (no `data`) and instanceless (no `this` context). They are simply a `render` function that returns virtual nodes making them much cheaper to render.

- **Vea También:** [Functional Components](../guide/render-function.html#Functional-Components)

## Instance Properties

### vm.$data

- **Type:** `Object`

- **Details:**

  The data object that the Vue instance is observing. The Vue instance proxies access to the properties on its data object.

- **Vea También:** [Options - data](#data)

### vm.$el

- **Type:** `HTMLElement`

- **Read only**

- **Details:**

  The root DOM element that the Vue instance is managing.

### vm.$options

- **Type:** `Object`

- **Read only**

- **Details:**

  The instantiation options used for the current Vue instance. This is useful when you want to include custom properties in the options:

  ``` js
  new Vue({
    customOption: 'foo',
    created: function () {
      console.log(this.$options.customOption) // -> 'foo'
    }
  })
  ```

### vm.$parent

- **Type:** `Vue instance`

- **Read only**

- **Details:**

  The parent instance, if the current instance has one.

### vm.$root

- **Type:** `Vue instance`

- **Read only**

- **Details:**

  The root Vue instance of the current component tree. If the current instance has no parents this value will be itself.

### vm.$children

- **Type:** `Array<Vue instance>`

- **Read only**

- **Details:**

  The direct child components of the current instance. **Note there's no order guarantee for `$children`, and it is not reactive.** If you find yourself trying to use `$children` for data binding, consider using an Array and `v-for` to generate child components, and use the Array as the source of truth.

### vm.$slots

- **Type:** `{ [name: string]: ?Array<VNode> }`

- **Read only**

- **Details:**

  Used to programmatically access content [distributed by slots](../guide/components.html#Content-Distribution-with-Slots). Each [named slot](../guide/components.html#Named-Slots) has its own corresponding property (e.g. the contents of `slot="foo"` will be found at `vm.$slots.foo`). The `default` property contains any nodes not included in a named slot.

  Accessing `vm.$slots` is most useful when writing a component with a [render function](../guide/render-function.html).

- **Example:**

  ```html
  <blog-post>
    <h1 slot="header">
      About Me
    </h1>

    <p>Here's some page content, which will be included in vm.$slots.default, because it's not inside a named slot.</p>

    <p slot="footer">
      Copyright 2016 Evan You
    </p>

    <p>If I have some content down here, it will also be included in vm.$slots.default.</p>.
  </blog-post>
  ```

  ```js
  Vue.component('blog-post', {
    render: function (createElement) {
      var header = this.$slots.header
      var body   = this.$slots.default
      var footer = this.$slots.footer
      return createElement('div', [
        createElement('header', header),
        createElement('main', body),
        createElement('footer', footer)
      ])
    }
  })
  ```

- **Vea También:**
  - [`<slot>` Component](#slot-1)
  - [Content Distribution with Slots](../guide/components.html#Content-Distribution-with-Slots)
  - [Render Functions: Slots](../guide/render-function.html#Slots)

### vm.$scopedSlots

> New in 2.1.0

- **Type:** `{ [name: string]: props => VNode | Array<VNode> }`

- **Read only**

- **Details:**

  Used to programmatically access [scoped slots](../guide/components.html#Scoped-Slots). For each slot, including the `default` one, the object contains a corresponding function that returns VNodes.

  Accessing `vm.$scopedSlots` is most useful when writing a component with a [render function](../guide/render-function.html).

- **Vea También:**
  - [`<slot>` Component](#slot-1)
  - [Scoped Slots](../guide/components.html#Scoped-Slots)
  - [Render Functions: Slots](../guide/render-function.html#Slots)

### vm.$refs

- **Type:** `Object`

- **Read only**

- **Details:**

  An object that holds child components that have `ref` registered.

- **Vea También:**
  - [Child Component Refs](../guide/components.html#Child-Component-Refs)
  - [ref](#ref)

### vm.$isServer

- **Type:** `boolean`

- **Read only**

- **Details:**

  Whether the current Vue instance is running on the server.

- **Vea También:** [Server-Side Rendering](../guide/ssr.html)

## Instance Methods / Data

<h3 id="vm-watch">vm.$watch( expOrFn, callback, [options] )</h3>

- **Argumentos:**
  - `{string | Function} expOrFn`
  - `{Function} callback`
  - `{Object} [options]`
    - `{boolean} deep`
    - `{boolean} immediate`

- **Returns:** `{Function} unwatch`

- **Modo de Uso:**

  Watch an expression or a computed function on the Vue instance for changes. The callback gets called with the new value and the old value. The expression only accepts simple dot-delimited paths. For more complex expression, use a function instead.

<p class="tip">Note: when mutating (rather than replacing) an Object or an Array, the old value will be the same as new value because they reference the same Object/Array. Vue doesn't keep a copy of the pre-mutate value.</p>

- **Example:**

  ``` js
  // keypath
  vm.$watch('a.b.c', function (newVal, oldVal) {
    // do something
  })

  // function
  vm.$watch(
    function () {
      return this.a + this.b
    },
    function (newVal, oldVal) {
      // do something
    }
  )
  ```

  `vm.$watch` returns an unwatch function that stops firing the callback:

  ``` js
  var unwatch = vm.$watch('a', cb)
  // later, teardown the watcher
  unwatch()
  ```

- **Option: deep**

  To also detect nested value changes inside Objects, you need to pass in `deep: true` in the options argument. Note that you don't need to do so to listen for Array mutations.

  ``` js
  vm.$watch('someObject', callback, {
    deep: true
  })
  vm.someObject.nestedValue = 123
  // callback is fired
  ```

- **Option: immediate**

  Passing in `immediate: true` in the option will trigger the callback immediately with the current value of the expression:

  ``` js
  vm.$watch('a', callback, {
    immediate: true
  })
  // callback is fired immediately with current value of `a`
  ```

<h3 id="vm-set">vm.$set( object, key, value )</h3>

- **Argumentos:**
  - `{Object} object`
  - `{string} key`
  - `{any} value`

- **Returns:** the set value.

- **Modo de Uso:**

  This is the **alias** of the global `Vue.set`.

- **Vea También:** [Vue.set](#Vue-set)

<h3 id="vm-delete">vm.$delete( object, key )</h3>

- **Argumentos:**
  - `{Object} object`
  - `{string} key`

- **Modo de Uso:**

  This is the **alias** of the global `Vue.delete`.

- **Vea También:** [Vue.delete](#Vue-delete)

## Instance Methods / Events

<h3 id="vm-on">vm.$on( event, callback )</h3>

- **Argumentos:**
  - `{string} event`
  - `{Function} callback`

- **Modo de Uso:**

  Listen for a custom event on the current vm. Events can be triggered by `vm.$emit`. The callback will receive all the additional arguments passed into these event-triggering methods.

- **Example:**

  ``` js
  vm.$on('test', function (msg) {
    console.log(msg)
  })
  vm.$emit('test', 'hi')
  // -> "hi"
  ```

<h3 id="vm-once">vm.$once( event, callback )</h3>

- **Argumentos:**
  - `{string} event`
  - `{Function} callback`

- **Modo de Uso:**

  Listen for a custom event, but only once. The listener will be removed once it triggers for the first time.

<h3 id="vm-off">vm.$off( [event, callback] )</h3>

- **Argumentos:**
  - `{string} [event]`
  - `{Function} [callback]`

- **Modo de Uso:**

  Remove event listener(s).

  - If no arguments are provided, remove all event listeners;

  - If only the event is provided, remove all listeners for that event;

  - If both event and callback are given, remove the listener for that specific callback only.

<h3 id="vm-emit">vm.$emit( event, [...args] )</h3>

- **Argumentos:**
  - `{string} event`
  - `[...args]`

  Trigger an event on the current instance. Any additional arguments will be passed into the listener's callback function.

## Instance Methods / Lifecycle

<h3 id="vm-mount">vm.$mount( [elementOrSelector] )</h3>

- **Argumentos:**
  - `{Element | string} [elementOrSelector]`
  - `{boolean} [hydrating]`

- **Returns:** `vm` - the instance itself

- **Modo de Uso:**

  If a Vue instance didn't receive the `el` option at instantiation, it will be in "unmounted" state, without an associated DOM element. `vm.$mount()` can be used to manually start the mounting of an unmounted Vue instance.

  If `elementOrSelector` argument is not provided, the template will be rendered as an off-document element, and you will have to use native DOM API to insert it into the document yourself.

  The method returns the instance itself so you can chain other instance methods after it.

- **Example:**

  ``` js
  var MyComponent = Vue.extend({
    template: '<div>Hello!</div>'
  })

  // create and mount to #app (will replace #app)
  new MyComponent().$mount('#app')

  // the above is the same as:
  new MyComponent({ el: '#app' })

  // or, render off-document and append afterwards:
  var component = new MyComponent().$mount()
  document.getElementById('app').appendChild(component.$el)
  ```

- **Vea También:**
  - [Lifecycle Diagram](../guide/instance.html#Lifecycle-Diagram)
  - [Server-Side Rendering](../guide/ssr.html)

<h3 id="vm-forceUpdate">vm.$forceUpdate()</h3>

- **Modo de Uso:**

  Force the Vue instance to re-render. Note it does not affect all child components, only the instance itself and child components with inserted slot content.

<h3 id="vm-nextTick">vm.$nextTick( [callback] )</h3>

- **Argumentos:**
  - `{Function} [callback]`

- **Modo de Uso:**

  Defer the callback to be executed after the next DOM update cycle. Use it immediately after you've changed some data to wait for the DOM update. This is the same as the global `Vue.nextTick`, except that the callback's `this` context is automatically bound to the instance calling this method.

  > New in 2.1.0: returns a Promise if no callback is provided and Promise is supported in the execution environment.

- **Example:**

  ``` js
  new Vue({
    // ...
    methods: {
      // ...
      example: function () {
        // modify data
        this.message = 'changed'
        // DOM is not updated yet
        this.$nextTick(function () {
          // DOM is now updated
          // `this` is bound to the current instance
          this.doSomethingElse()
        })
      }
    }
  })
  ```

- **Vea También:**
  - [Vue.nextTick](#Vue-nextTick)
  - [Async Update Queue](../guide/reactivity.html#Async-Update-Queue)

<h3 id="vm-destroy">vm.$destroy()</h3>

- **Modo de Uso:**

  Completely destroy a vm. Clean up its connections with other existing vms, unbind all its directives, turn off all event listeners.

  Triggers the `beforeDestroy` and `destroyed` hooks.

  <p class="tip">In normal use cases you shouldn't have to call this method yourself. Prefer controlling the lifecycle of child components in a data-driven fashion using `v-if` and `v-for`.</p>

- **Vea También:** [Lifecycle Diagram](../guide/instance.html#Lifecycle-Diagram)

## Directives

### v-text

- **Expects:** `string`

- **Details:**

  Updates the element's `textContent`. If you need to update the part of `textContent`, you should use `{% raw %}{{ Mustache }}{% endraw %}` interpolations.

- **Example:**

  ```html
  <span v-text="msg"></span>
  <!-- same as -->
  <span>{{msg}}</span>
  ```

- **Vea También:** [Data Binding Syntax - interpolations](../guide/syntax.html#Text)

### v-html

- **Expects:** `string`

- **Details:**

  Updates the element's `innerHTML`. **Note that the contents are inserted as plain HTML - they will not be compiled as Vue templates**. If you find yourself trying to compose templates using `v-html`, try to rethink the solution by using components instead.

  <p class="tip">Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to [XSS attacks](https://en.wikipedia.org/wiki/Cross-site_scripting). Only use `v-html` on trusted content and **never** on user-provided content.</p>

- **Example:**

  ```html
  <div v-html="html"></div>
  ```
- **Vea También:** [Data Binding Syntax - interpolations](../guide/syntax.html#Raw-HTML)

### v-show

- **Expects:** `any`

- **Modo de Uso:**

  Toggle's the element's `display` CSS property based on the truthy-ness of the expression value.

  This directive triggers transitions when its condition changes.

- **Vea También:** [Conditional Rendering - v-show](../guide/conditional.html#v-show)

### v-if

- **Expects:** `any`

- **Modo de Uso:**

  Conditionally render the element based on the truthy-ness of the expression value. The element and its contained directives / components are destroyed and re-constructed during toggles. If the element is a `<template>` element, its content will be extracted as the conditional block.

  This directive triggers transitions when its condition changes.

- **Vea También:** [Conditional Rendering - v-if](../guide/conditional.html)

### v-else

- **Does not expect expression**

- **Restriction:** previous sibling element must have `v-if` or `v-else-if`.

- **Modo de Uso:**

  Denote the "else block" for `v-if` or a `v-if`/`v-else-if` chain.

  ```html
  <div v-if="Math.random() > 0.5">
    Now you see me
  </div>
  <div v-else>
    Now you don't
  </div>
  ```

- **Vea También:**
  - [Conditional Rendering - v-else](../guide/conditional.html#v-else)

### v-else-if

> New in 2.1.0

- **Expects:** `any`

- **Restriction:** previous sibling element must have `v-if` or `v-else-if`.

- **Modo de Uso:**

  Denote the "else if block" for `v-if`. Can be chained.

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

- **Vea También:** [Conditional Rendering - v-else-if](../guide/conditional.html#v-else-if)

### v-for

- **Expects:** `Array | Object | number | string`

- **Modo de Uso:**

  Render the element or template block multiple times based on the source data. The directive's value must use the special syntax `alias in expression` to provide an alias for the current element being iterated on:

  ``` html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  Alternatively, you can also specify an alias for the index (or the key if used on an Object):

  ``` html
  <div v-for="(item, index) in items"></div>
  <div v-for="(val, key) in object"></div>
  <div v-for="(val, key, index) in object"></div>
  ```

  The default behavior of `v-for` will try to patch the elements in-place without moving them. To force it to reorder elements, you need to provide an ordering hint with the `key` special attribute:

  ``` html
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```

  The detailed usage for `v-for` is explained in the guide section linked below.

- **Vea También:**
  - [List Rendering](../guide/list.html)
  - [key](../guide/list.html#key)

### v-on

- **Shorthand:** `@`

- **Expects:** `Function | Inline Statement`

- **Argument:** `event (required)`

- **Modifiers:**
  - `.stop` - call `event.stopPropagation()`.
  - `.prevent` - call `event.preventDefault()`.
  - `.capture` - add event listener in capture mode.
  - `.self` - only trigger handler if event was dispatched from this element.
  - `.{keyCode | keyAlias}` - only trigger handler on certain keys.
  - `.native` - listen for a native event on the root element of component.

- **Modo de Uso:**

  Attaches an event listener to the element. The event type is denoted by the argument. The expression can either be a method name or an inline statement, or simply omitted when there are modifiers present.

  When used on a normal element, it listens to **native DOM events** only. When used on a custom element component, it also listens to **custom events** emitted on that child component.

  When listening to native DOM events, the method receives the native event as the only argument. If using inline statement, the statement has access to the special `$event` property: `v-on:click="handle('ok', $event)"`.

- **Example:**

  ```html
  <!-- method handler -->
  <button v-on:click="doThis"></button>

  <!-- inline statement -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- shorthand -->
  <button @click="doThis"></button>

  <!-- stop propagation -->
  <button @click.stop="doThis"></button>

  <!-- prevent default -->
  <button @click.prevent="doThis"></button>

  <!-- prevent default without expression -->
  <form @submit.prevent></form>

  <!-- chain modifiers -->
  <button @click.stop.prevent="doThis"></button>

  <!-- key modifier using keyAlias -->
  <input @keyup.enter="onEnter">

  <!-- key modifier using keyCode -->
  <input @keyup.13="onEnter">
  ```

  Listening to custom events on a child component (the handler is called when "my-event" is emitted on the child):

  ```html
  <my-component @my-event="handleThis"></my-component>

  <!-- inline statement -->
  <my-component @my-event="handleThis(123, $event)"></my-component>

  <!-- native event on component -->
  <my-component @click.native="onClick"></my-component>
  ```

- **Vea También:**
  - [Methods and Event Handling](../guide/events.html)
  - [Components - Custom Events](../guide/components.html#Custom-Events)

### v-bind

- **Shorthand:** `:`

- **Expects:** `any (with argument) | Object (without argument)`

- **Argument:** `attrOrProp (optional)`

- **Modifiers:**
  - `.prop` - Bind as a DOM property instead of an attribute. ([what's the difference?](http://stackoverflow.com/questions/6003819/properties-and-attributes-in-html#answer-6004028))
  - `.camel` - transform the kebab-case attribute name into camelCase. (supported since 2.1.0)

- **Modo de Uso:**

  Dynamically bind one or more attributes, or a component prop to an expression.

  When used to bind the `class` or `style` attribute, it supports additional value types such as Array or Objects. See linked guide section below for more details.

  When used for prop binding, the prop must be properly declared in the child component.

  When used without an argument, can be used to bind an object containing attribute name-value pairs. Note in this mode `class` and `style` does not support Array or Objects.

- **Example:**

  ```html
  <!-- bind an attribute -->
  <img v-bind:src="imageSrc">

  <!-- shorthand -->
  <img :src="imageSrc">
  
  <!-- with inline string concatenation -->
  <img :src="'/path/to/images/' + fileName">

  <!-- class binding -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]">

  <!-- style binding -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- binding an object of attributes -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!-- DOM attribute binding with prop modifier -->
  <div v-bind:text-content.prop="text"></div>

  <!-- prop binding. "prop" must be declared in my-component. -->
  <my-component :prop="someThing"></my-component>

  <!-- XLink -->
  <svg><a :xlink:special="foo"></a></svg>
  ```

  The `.camel` modifier allows camelizing a `v-bind` attribute name when using in-DOM templates, e.g. the SVG `viewBox` attribute:

  ``` html
  <svg :view-box.camel="viewBox"></svg>
  ```

  `.camel` is not needed if you are using string templates, or compiling with `vue-loader`/`vueify`.

- **Vea También:**
  - [Class and Style Bindings](../guide/class-and-style.html)
  - [Components - Component Props](../guide/components.html#Props)

### v-model

- **Expects:** varies based on value of form inputs element or output of components

- **Limited to:**
  - `<input>`
  - `<select>`
  - `<textarea>`
  - components

- **Modifiers:**
  - [`.lazy`](../guide/forms.html#lazy) - listen to `change` events instead of `input`
  - [`.number`](../guide/forms.html#number) - cast input string to numbers
  - [`.trim`](../guide/forms.html#trim) - trim input

- **Modo de Uso:**

  Create a two-way binding on a form input element or a component. For detailed usage and other notes, see the Guide section linked below.

- **Vea También:**
  - [Form Input Bindings](../guide/forms.html)
  - [Components - Form Input Components using Custom Events](../guide/components.html#Form-Input-Components-using-Custom-Events)

### v-pre

- **Does not expect expression**

- **Modo de Uso:**

  Skip compilation for this element and all its children. You can use this for displaying raw mustache tags. Skipping large numbers of nodes with no directives on them can also speed up compilation.

- **Example:**

  ```html
  <span v-pre>{{ this will not be compiled }}</span>
   ```

### v-cloak

- **Does not expect expression**

- **Modo de Uso:**

  This directive will remain on the element until the associated Vue instance finishes compilation. Combined with CSS rules such as `[v-cloak] { display: none }`, this directive can be used to hide un-compiled mustache bindings until the Vue instance is ready.

- **Example:**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```html
  <div v-cloak>
    {{ message }}
  </div>
  ```

  The `<div>` will not be visible until the compilation is done.

### v-once

- **Does not expect expression**

- **Details:**

  Render the element and component **once** only. On subsequent re-renders, the element/component and all its children will be treated as static content and skipped. This can be used to optimize update performance.

  ```html
  <!-- single element -->
  <span v-once>This will never change: {{msg}}</span>
  <!-- the element have children -->
  <div v-once>
    <h1>comment</h1>
    <p>{{msg}}</p>
  </div>
  <!-- component -->
  <my-component v-once :comment="msg"></my-component>
  <!-- v-for directive -->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

- **Vea También:**
  - [Data Binding Syntax - interpolations](../guide/syntax.html#Text)
  - [Components - Cheap Static Components with v-once](../guide/components.html#Cheap-Static-Components-with-v-once)

## Special Attributes

### key

- **Expects:** `string`

  The `key` special attribute is primarily used as a hint for Vue's virtual DOM algorithm to identify VNodes when diffing the new list of nodes against the old list. Without keys, Vue uses an algorithm that minimizes element movement and tries to patch/reuse elements of the same type in-place as much as possible. With keys, it will reorder elements based on the order change of keys, and elements with keys that are no longer present will always be removed/destroyed.

  Children of the same common parent must have **unique keys**. Duplicate keys will cause render errors.

  The most common use case is combined with `v-for`:

  ``` html
  <ul>
    <li v-for="item in items" :key="item.id">...</li>
  </ul>
  ```

  It can also be used to force replacement of an element/component instead of reusing it. This can be useful when you want to:

  - Properly trigger lifecycle hooks of a component
  - Trigger transitions

  For example:

  ``` html
  <transition>
    <span :key="text">{{ text }}</span>
  </transition>
  ```

  When `text` changes, the `<span>` will always be replaced instead of patched, so a transition will be triggered.

### ref

- **Expects:** `string`

  `ref` is used to register a reference to an element or a child component. The reference will be registered under the parent component's `$refs` object. If used on a plain DOM element, the reference will be that element; if used on a child component, the reference will be component instance:

  ``` html
  <!-- vm.$refs.p will be the DOM node -->
  <p ref="p">hello</p>

  <!-- vm.$refs.child will be the child comp instance -->
  <child-comp ref="child"></child-comp>
  ```

  When used on elements/components with `v-for`, the registered reference will be an Array containing DOM nodes or component instances.

  An important note about the ref registration timing: because the refs themselves are created as a result of the render function, you cannot access them on the initial render - they don't exist yet! `$refs` is also non-reactive, therefore you should not attempt to use it in templates for data-binding.

- **Vea También:** [Child Component Refs](../guide/components.html#Child-Component-Refs)

### slot

- **Expects:** `string`

  Used on content inserted into child components to indicate which named slot the content belongs to.

  For detailed usage, see the guide section linked below.

- **Vea También:** [Named Slots](../guide/components.html#Named-Slots)

## Built-In Components

### component

- **Props:**
  - `is` - string | ComponentDefinition | ComponentConstructor
  - `inline-template` - boolean

- **Modo de Uso:**

  A "meta component" for rendering dynamic components. The actual component to render is determined by the `is` prop:

  ```html
  <!-- a dynamic component controlled by -->
  <!-- the `componentId` property on the vm -->
  <component :is="componentId"></component>

  <!-- can also render registered component or component passed as prop -->
  <component :is="$options.components.child"></component>
  ```

- **Vea También:** [Dynamic Components](../guide/components.html#Dynamic-Components)

### transition

- **Props:**
  - `name` - string, Used to automatically generate transition CSS class names. e.g. `name: 'fade'` will auto expand to `.fade-enter`, `.fade-enter-active`, etc. Defaults to `"v"`.
  - `appear` - boolean, Whether to apply transition on initial render. Defaults to `false`.
  - `css` - boolean, Whether to apply CSS transition classes. Defaults to `true`. If set to `false`, will only trigger JavaScript hooks registered via component events.
  - `type` - string, Specify the type of transition events to wait for to determine transition end timing. Available values are `"transition"` and `"animation"`. By default, it will automatically detect the type that has a longer duration.
  - `mode` - string, Controls the timing sequence of leaving/entering transitions. Available modes are `"out-in"` and `"in-out"`; defaults to simultaneous.
  - `enter-class` - string
  - `leave-class` - string
  - `enter-active-class` - string
  - `leave-active-class` - string
  - `appear-class` - string
  - `appear-active-class` - string

- **Events:**
  - `before-enter`
  - `enter`
  - `after-enter`
  - `before-leave`
  - `leave`
  - `after-leave`
  - `before-appear`
  - `appear`
  - `after-appear`

- **Modo de Uso:**

  `<transition>` serve as transition effects for **single** element/component. The `<transition>` does not render an extra DOM element, nor does it show up in the inspected component hierarchy. It simply applies the transition behavior to the wrapped content inside.

  ```html
  <!-- simple element -->
  <transition>
    <div v-if="ok">toggled content</div>
  </transition>

  <!-- dynamic component -->
  <transition name="fade" mode="out-in" appear>
    <component :is="view"></component>
  </transition>

  <!-- event hooking -->
  <div id="transition-demo">
    <transition @after-enter="transitionComplete">
      <div v-show="ok">toggled content</div>
    </transition>
  </div>
  ```

  ``` js
  new Vue({
    ...
    methods: {
      transitionComplete: function (el) {
        // for passed 'el' that DOM element as the argument, something ...
      }
    }
    ...
  }).$mount('#transition-demo')
  ```

- **Vea También:** [Transitions: Entering, Leaving, and Lists](../guide/transitions.html)

### transition-group

- **Props:**
  - `tag` - string, defaults to `span`.
  - `move-class` - overwrite CSS class applied during moving transition.
  - exposes the same props as `<transition>` except `mode`.

- **Events:**
  - exposes the same events as `<transition>`.

- **Modo de Uso:**

  `<transition-group>` serve as transition effects for **multiple** elements/components. The `<transition-group>` renders a real DOM element. By default it renders a `<span>`, and you can configure what element is should render via the `tag` attribute.

  Note every child in a `<transition-group>` must be **uniquely keyed** for the animations to work properly.

  `<transition-group>` supports moving transitions via CSS transform. When a child's position on screen has changed after an updated, it will get applied a moving CSS class (auto generated from the `name` attribute or configured with the `move-class` attribute). If the CSS `transform` property is "transition-able" when the moving class is applied, the element will be smoothly animated to its destination using the [FLIP technique](https://aerotwist.com/blog/flip-your-animations/).

  ```html
  <transition-group tag="ul" name="slide">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </transition-group>
  ```

- **Vea También:** [Transitions: Entering, Leaving, and Lists](../guide/transitions.html)

### keep-alive

- **Props:**
  - `include` - string or RegExp. Only components matched by this will be cached.
  - `exclude` - string or RegExp. Any component matched by this will not be cached.

- **Modo de Uso:**

  When wrapped around a dynamic component, `<keep-alive>` caches the inactive component instances without destroying them. Similar to `<transition>`, `<keep-alive>` is an abstract component: it doesn't render a DOM element itself, and doesn't show up in the component parent chain.

  When a component is toggled inside `<keep-alive>`, its `activated` and `deactivated` lifecycle hooks will be invoked accordingly.

  Primarily used with preserve component state or avoid re-rendering.

  ```html
  <!-- basic -->
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>

  <!-- multiple conditional children -->
  <keep-alive>
    <comp-a v-if="a > 1"></comp-a>
    <comp-b v-else></comp-b>
  </keep-alive>

  <!-- used together with <transition> -->
  <transition>
    <keep-alive>
      <component :is="view"></component>
    </keep-alive>
  </transition>
  ```

- **`include` and `exclude`**

  > New in 2.1.0

  The `include` and `exclude` props allow components to be conditionally cached. Both props can either be a comma-delimited string or a RegExp:

  ``` html
  <!-- comma-delimited string -->
  <keep-alive include="a,b">
    <component :is="view"></component>
  </keep-alive>

  <!-- regex (use v-bind) -->
  <keep-alive :include="/a|b/">
    <component :is="view"></component>
  </keep-alive>
  ```

  The match is first checked on the component's own `name` option, then its local registration name (the key in the parent's `components` option) if the `name` option is not available. Anonymous components cannot be matched against.

  <p class="tip">`<keep-alive>` does not work with functional components because they do not have instances to be cached.</p>

- **Vea También:** [Dynamic Components - keep-alive](../guide/components.html#keep-alive)

### slot

- **Props:**
  - `name` - string, Used for named slot.

- **Modo de Uso:**

  `<slot>` serve as content distribution outlets in component templates. `<slot>` itself will be replaced.

  For detailed usage, see the guide section linked below.

- **Vea También:** [Content Distribution with Slots](../guide/components.html#Content-Distribution-with-Slots)

## VNode Interface

- Please refer to the [VNode class declaration](https://github.com/vuejs/vue/blob/dev/src/core/vdom/vnode.js).

## Server-Side Rendering

- Please refer to the [vue-server-renderer package documentation](https://github.com/vuejs/vue/tree/dev/packages/vue-server-renderer).
