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

- **Ver también:** [Componentes](../guide/components.html)

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

- **Vea también:** [Cola de Actualización Asíncrona](../guide/reactivity.html#Cola-de-Actualización-Asíncrona)

<h3 id="Vue-set">Vue.set( object, key, value )</h3>

- **Argumentos:**
  - `{Object} object`
  - `{string} key`
  - `{any} value`

- **Retorna:** el valor asignado.

- **Modo de Uso:**

  Asigna un valor a una propiedad en un objeto. Si el objeto es reactivo, asegúrese que la propiedad haya sido creada de forma reactiva y que active actualizaciones de vista. Esto se usa principalmente para resolver el problema que Vue no puede detectar adiciones de propiedades en objetos.

  **Tenga en cuenta que el ojeto no puede ser una instancia Vue, o el objeto de datos raíz de una instancia Vue.**

- **Ver también:** [Reactividad en Profundidad](../guide/reactivity.html)

<h3 id="Vue-delete">Vue.delete( object, key )</h3>

- **Argumentos:**
  - `{Object} object`
  - `{string} key`

- **Modo de Uso:**

  Elimina una propiedad de un objeto. Si el objeto es reactivo, se asegura que dicha eliminación active las actualizaciones de vista. Esto se usa principalmente para resolver el problema que Vue no puede detectar eliminaciones de propiedades en objetos, pero usted tendrá muy poca necesidad de usarlo.

  **Tenga en cuenta que el ojeto no puede ser una instancia Vue, o el objeto de datos raíz de una instancia Vue.**

- **Ver también:** [Reactividad a Profundidad](../guide/reactivity.html)

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

- **Vea también:** [Directivas personalizadas](../guide/custom-directive.html)

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

- **Vea también:** [Componentes](../guide/components.html)

<h3 id="Vue-use">Vue.use( plugin )</h3>

- **Argumentos:**
  - `{Object | Function} plugin`

- **Modo de Uso:**

  Instala un plugin de Vue. Si el plugin es un Objeto, debe exponer un método `install`. Si es una función en sí, será tratada como el método de instalación. El método de instalación será llamado con Vue como argumento.

  Cuando este método es llamado en el mismo plugin múltiples veces, será instalado únicamente una vez.

- **Vea también:** [Plugins](../guide/plugins.html)

<h3 id="Vue-mixin">Vue.mixin( mixin )</h3>

- **Argumentos:**
  - `{Object} mixin`

- **Modo de Uso:**

  Aplica un mixin globalmente, lo cual afecta cada instancia de Vue creada después de eso. Esto puede ser usado por autores de plugins para injectar comportamiento personalizado a los componentes. **No es recomendado en código de aplicación**.

- **Vea también:** [Mixins Globales](../guide/mixins.html#Mixins-Globales)

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

- **Vea también:** [Funciones de Renderizado](../guide/render-function.html)

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

- **Vea también:** [Reactividad en profundidad](../guide/reactivity.html)

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

- **Vea también:** [Props](../guide/components.html#Props)

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

- **Vea también:**
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

- **Vea también:** [Métodos y manejo de eventos](../guide/events.html)

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

- **Vea también:** [Métodos de instancia - vm.$watch](#vm-watch)

## Opciones / DOM

### el

- **Tipo:** `string | HTMLElement`

- **Restricción:** sólo respetado en una instancia creada con `new`.

- **Detalles:**

  Provee a la instancia Vue con un elemento existente del DOM para ser montada. Puede ser un string de selector CSS o un elemento HTML.

  Depués que la instancia sea montada, el elemento resuelto se podrá acceder como `vm.$el`.

  Si esta opción está disponible en la instanciación, la instancia inmediatamente iniciará la compilación; de otra forma, el usuario deberá llamar explícitamente a `vm.$mount()` para inicial la compilación manualmente.

  <p class="tip">El elemento elegido únicamente sirve como punto de montura. A contrario de Vue 1.x, el elemento montado será reemplazado con DOM generado por Vue en todos los casos. De modo que no es recomendable montar la instancia raíz en un `<html>` o un `<body>`.</p>

- **Vea también:** [Diagrama de Ciclo de Vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

### template

- **Tipo:** `string`

- **Detalles:**

  Es una plantilla string para ser usada como el código HTML de la instancia Vue. La plantilla **reemplazará** el elemento montado. Cualquier código HTML existente dentro del elemento montado será ignorado, a menos que los slots de distribución de contenido se encuentren presentes en la plantilla.

  Si el string inicia con `#` será usado como un `querySelector` y usará el `innerHTML` del elemento como plantilla string. Esto permite el uso del truco común `<script type="x-template">` para incluir plantillas.

  <p class="tip">Desde una perspectiva de seguridad, usted debería usar únicamente plantillas Vue en las que confía. Nunca use contenido generado por el usuario como plantilla.</p>

- **Vea también:**
  - [Diagrama de Ciclo de Vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)
  - [Distribución de contenido](../guide/components.html#Distribucion-de-Contenido-con-Slots)

### render

  - **Tipo:** `Function`

  - **Detalles:**

    Una alternativa a las plantillas de string, le permite usar el poder programático completo de JavaScript. La función render recibe un método `createElement` como su primer argumento, usado para crear los `VNode`.

    Si el componente es un componente funcional, la función render también recibe un argumento extra `context`, el cual provee acceso a los datos contextuales, ya que los componentes funcionales no poseen instancia.

  - **Vea también:**
    - [Funciones de renderizado](../guide/render-function)

## Opciones / Hooks de ciclo de vida

Todos los hooks de ciclo de vida tienen la instancia asignada automáticamente a su contexto `this`, de modo que usted puede acceder a los datos, a las propiedades calculadas, y a los métodos. Esto quiere decir __que usted no debe usar funciones flecha para definir un método de ciclo de vida__ (p.e. `created: () => this.fetchTodos()`). La razón es que las funciones flecha asignan el contexto padre a `this`, de modo que `this` no será la instancia Vue como se esperaría y `this.fetchTodos` no estará definido.

### beforeCreate

- **Tipo:** `Function`

- **Detalles:**

  Llamado síncronamente justo después que la instacia es inicializada, antes de la observación de datos y la preparación de eventos/observadores.

- **Vea también:** [Diagrama de ciclo de vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

### created

- **Tipo:** `Function`

- **Detalles:**

  Llamado síncronamente después que la instancia es creada. En este punto, la instancia ha finalizado de procesar las opciones, lo que significa que lo siguiente ha sido preparado: observación de datos, propiedades calculadas, métodos, callbacks de observación/eventos. Sin embargo, no se ha iniciado la fase de montaje, y la propiedad `$el` no estará disponible.

- **Vea también:** [Diagrama de ciclo de vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

### beforeMount

- **Tipo:** `Function`

- **Detalles:**

  Llamado justo después que la fase de montaje inicia: la función `render` será llamada a continuación por primera vez.

  **Este hook no es llamado durante renderización en servidor.**

- **Vea también:** [Diagrama de ciclo de vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

### mounted

- **Tipo:** `Function`

- **Detalles:**

  Llamado después que la instancia acaba de ser montada donde `el` es reemplazado por el recién creado `vm.$el`. Si la instancia raíz es montada en un elemento en documento, `vm.$el` será también en documento cuando `mounted` sea llamado.

  **Este hook no es llamado durante renderización en servidor.**

- **Vea también:** [Diagrama de ciclo de vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

### beforeUpdate

- **Tipo:** `Function`

- **Detalles:**

  Llamado cuando los datos cambian, antes que el DOM virtual sea re-renderizado y parchado.

  Puede realizar cambios de estado adicionales en este hook, esto no activará re-renderizados adicionales.

  **Este hook no es llamado durante renderización en servidor.**

- **Vea también:** [Diagrama de ciclo de vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

### updated

- **Tipo:** `Function`

- **Detalles:**

  Llamado después que un cambio en datos causa que el DOM virtual sea re-renderizado y parchado.

  El DOM del componente estará en un estado actualizado cuando este hook sea llamado, de modo que usted pueda realizar operaciones que dependan del DOM en este hook. Sin embargo, en la mayoría de casos usted debe evitar cambiar el estado en este hook, ya que puede conducir a un ciclo de actualizaciones infinito.

  **Este hook no es llamado durante renderización en servidor.**

- **Vea también:** [Diagrama de ciclo de vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

### activated

- **Tipo:** `Function`

- **Detalles:**

  Llamado cuando un componente a mantener vivo es activado.

  **Este hook no es llamado durante renderización en servidor.**

- **Vea también:**
  - [Componentes construídos - keep-alive](#keep-alive)
  - [Componentes dinámicos - keep-alive](../guide/components.html#keep-alive)

### deactivated

- **Tipo:** `Function`

- **Detalles:**

  Llamado cuando un componente a mantener vivo es desactivado.

  **Este hook no es llamado durante renderización en servidor.**

- **Vea también:**
  - [Componentes construídos - keep-alive](#keep-alive)
  - [Componentes dinámicos - keep-alive](../guide/components.html#keep-alive)

### beforeDestroy

- **Tipo:** `Function`

- **Detalles:**

  Llamado justo antes que una instancia Vue sea destruída. En este punto la instancia es todavía completamente funcional.

  **Este hook no es llamado durante renderización en servidor.**

- **Vea también:** [Diagrama de ciclo de vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

### destroyed

- **Tipo:** `Function`

- **Detalles:**

  Llamado después que una instancia Vue haya sido destruída. Cuando este hook es llamado, todas las directivas de la instancia Vue ya han sido desasignadas, todos los listeners de eventos han sido removidos, y todas las intancias Vue hijos también han sido destruídas.

  **Este hook no es llamado durante renderización en servidor.**

- **Vea también:** [Diagrama de ciclo de vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

## Opciones / Recursos

### directives

- **Tipo:** `Object`

- **Detalles:**

  Un objeto de directivas para poner a disposición de la instancia Vue.

- **Vea también:**
  - [Directivas Personalizadas](../guide/custom-directive.html)
  - [Convenciones para Nombramiento de Recursos](../guide/components.html#Convenciones-para-Nombramiento-de-Componentes)

### filters

- **Tipo:** `Object`

- **Detalles:**

  Un objeto de filtros para poner a disposición de la intancia Vue.

- **Vea también:**
  - [`Vue.filter`](#Vue-filter)

### components

- **Tipo:** `Object`

- **Detalles:**

  Un objeto de componentes para poner a dispocisión de la instancia Vue.

- **Vea también:**
  - [Components](../guide/components.html)

## Opciones / Misceláneos

### parent

- **Tipo:** `Vue instance`

- **Detalles:**

  Especifica la instancia padre de la instancia a ser creada. Establece una relación padre-hijo entre ambas. El padre será accesible como `this.$parent` para el hijo, y el hijo será agregado al array `$children` del padre.

  <p class="tip">Use `$parent` con precaución - funciona principalmente como una compuerta de escape. Siempre prefiera usar props y eventos para comunicación entre padre e hijos.</p>

### mixins

- **Tipo:** `Array<Object>`

- **Detalles:**

  La opción `mixins` acepta un array de objetos mixin. Dichos objetos mixin pueden contener opciones de instancia tal como los objetos de instancia normales, y pueden ser integradas con las eventuales opciones usando la misma lógica de integración de opciones presente en `Vue.extend()`. p.e. Si su mixin contiene un hook `created` y el componente mismo tiene uno a su vez, ambas funciones serán llamadas.

  Los hooks en los mixins serán llamados en el mismo orden que fueron creados, y serán llamados antes de los propios hooks del componente.

- **Ejemplo:**

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

- **Vea también:** [Mixins](../guide/mixins.html)

### name

- **Tipo:** `string`

- **Restricción:** únicamente respetado cuando se usa como opción de componente.

- **Detalles:**

  Permite al componente invocarse a sí mismo recursivamente en su plantilla. Tenga en cuenta que cuando un componente es registrado globalmente con `Vue.component()`, el ID global es asignado automáticamente como su nombre.

  Otro beneficio de especificar una opción `name` se presenta en la depuración. Usar componentes nombrados resulta en mensajes de advertencia más útiles. Tambien, cuando se inspecciona la aplicación con las [herramientas vue](https://github.com/vuejs/vue-devtools), los componentes sin nombre aparecerán como `<AnonymousComponent>`, lo cual no es muy informativo. Al proveer la opción `name`, obtendrá un árbol de componentes mucho más informativo.

### extends

- **Tipo:** `Object | Function`

- **Detalles:**

  Permite extender un componente de forma declarativa (ya sea unas opciones sencillas o un constructor) sin tener que usar `Vue.extend`. Esto se hace principalmente para facilitar la herencia entre componentes de un archivo.

  Es similar a `mixins`, la diferencia siendo que que las propias opciones del componente toman una prioridad más alta que las del componente que está siendo extendido.

- **Ejemplo:**

  ``` js
  var CompA = { ... }

  // extiende CompA sin tener que llamar Vue.extend en alguno de los dos
  var CompB = {
    extends: CompA,
    ...
  }
  ```

### delimiters

- **Tipo:** `Array<string>`

- **Valor por defecto:** `{% raw %}["{{", "}}"]{% endraw %}`

- **Detalles:**

  Cambia los delimitadores usados en la interpolación de texto. **Sólo disponible en la versión independiente.**

- **Ejemplo:**

  ``` js
  new Vue({
    delimiters: ['${', '}']
  })

  // Delimitadores cambiados al estilo de plantillas string de ES6
  ```

### functional

- **Tipo:** `boolean`

- **Detalles:**

  Causa que un componente no tenga estado (sin `data`) y no tenga instancia (sin contexto `this`). Éste tipo de componente son sencillamente una función `render` que retorna nodos virtuales, haciéndolos mucho más económicos de renderizar.

- **Vea también:** [Componentes funcionales](../guide/render-function.html#Componentes-Funcionales)

## Propiedades de Instancia

### vm.$data

- **Tipo:** `Object`

- **Detalles:**

  El objeto de datos que es observado por la instancia Vue. La instancia brinda acceso directo a las propiedades en su objeto de datos.

- **Vea también:** [Opciones - data](#data)

### vm.$el

- **Tipo:** `HTMLElement`

- **Sólo lectura**

- **Detalles:**

  El elemento DOM raíz que es manejado por la instancia Vue.

### vm.$options

- **Tipo:** `Object`

- **Sólo lectura**

- **Detalles:**

  Las opciones de instanciación usados para la instancia Vue actual. Es útil cuando se quiere incluir propiedades personalizadas en las opciones:

  ``` js
  new Vue({
    customOption: 'foo',
    created: function () {
      console.log(this.$options.customOption) // -> 'foo'
    }
  })
  ```

### vm.$parent

- **Tipo:** `Vue instance`

- **Sólo lectura**

- **Detalles:**

  La instancia padre, si la instancia actual posee una.

### vm.$root

- **Tipo:** `Vue instance`

- **Sólo lectura**

- **Detalles:**

  La instancia Vue raíz del árbol de componentes actual. Si la instancia actual no tiene padres, éste valor será ella misma.

### vm.$children

- **Tipo:** `Array<Vue instance>`

- **Sólo lectura**

- **Detalles:**

  Los hijos directos de la instancia actual. **Tenga en cuenta que no hay garantías del orden para `$children`, y no es una propiedad reactiva.** Si necesita usar `$children` para asignación de datos, considere usar un Array y `v-for` para generar componentes hijos, y usar el Array como fuente de la verdad.

### vm.$slots

- **Tipo:** `{ [name: string]: ?Array<VNode> }`

- **Sólo lectura**

- **Detalles:**

  Usado para acceder programáticamente al contenido [distribuído por slots](../guide/components.html#Distribucion-de-Contenido-con-Slots). Cada [slot nombrado](../guide/components.html#Slots-con-Nombre) tiene su correspondiente propiedad (p.e. el contenido de `slot="foo"` estará presente en `vm.$slots.foo`). La propiedad `default` contiene los nodos que no hayan sido incluídos en un slot con nombre.

  Accediendo a `vm.$slots` es muy útil cuando se escribe un componente con una [función de renderizado](../guide/render-function.html).

- **Ejemplo:**

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

- **Vea también:**
  - [Componente `<slot>`](#slot-1)
  - [Distribución de contenido con Slots](../guide/components.html#Distribucion-de-Contenido-con-Slots)
  - [Funciones de renderizado: Slots](../guide/render-function.html#Slots)

### vm.$scopedSlots

> Nuevo en 2.1.0

- **Tipo:** `{ [name: string]: props => VNode | Array<VNode> }`

- **Sólo lectura**

- **Detalles:**

  Usado para acceder programáticamente a los [slots de ámbito](../guide/components.html#Slots-de-ambito). Por cada slot, incluyendo el slot `default`, el objeto contiene una función correspondiente que retorna VNodes.

  Acceder a `vm.$scopedSlots` es muy útil cuando se escribe un componente con una [función de renderizado](../guide/render-function.html).

- **Vea también:**
  - [`<slot>` Component](#slot-1)
  - [Slots de ámbito](../guide/components.html#Slots-de-ambito)
  - [Funciones de renderizado: Slots](../guide/render-function.html#Slots)

### vm.$refs

- **Tipo:** `Object`

- **Sólo lectura**

- **Detalles:**

  Un objeto que contiene los componentes hijos que han registrado `ref`.

- **Vea también:**
  - [Referencias a componentes hijo](../guide/components.html#Referencias-a-Componentes-Hijo)
  - [ref](#ref)

### vm.$isServer

- **Tipo:** `boolean`

- **Sólo lectura**

- **Detalles:**

  Indica si la instancia Vue actual es ejecutada en servidor.

- **Vea también:** [Renderizado en servidor](../guide/ssr.html)

## Métodos de instancia / Datos

<h3 id="vm-watch">vm.$watch( expOrFn, callback, [options] )</h3>

- **Argumentos:**
  - `{string | Function} expOrFn`
  - `{Function} callback`
  - `{Object} [options]`
    - `{boolean} deep`
    - `{boolean} immediate`

- **Retorna:** `{Function} unwatch`

- **Modo de Uso:**

  Observa los cambios de una expresión o una función calculada en la instancia de Vue. El callback es llamado con el nuevo valor y el valor viejo. La expresión solo acepta rutas delimitadas por punto. Para una expresión más compleja, use una función.

<p class="tip">Cuando esté mutando (en lugar de reemplazar) un Objecto o un Array, el valor viejo será el mismo que el valor nuevo, ya que referencian el mismo Objeto/Array. Vue no mantiene una copia del valor antes de ser mutado.</p>

- **Ejemplo:**

  ``` js
  // ruta clave
  vm.$watch('a.b.c', function (newVal, oldVal) {
    // haga algo
  })

  // función
  vm.$watch(
    function () {
      return this.a + this.b
    },
    function (newVal, oldVal) {
      // haga algo
    }
  )
  ```

  `vm.$watch` retorna una función que permite detener la llamada del callback:

  ``` js
  var unwatch = vm.$watch('a', cb)
  // después, destruye el observador
  unwatch()
  ```

- **Opción: deep**

  Para detectar también cambios en valores anidados dentro del Objeto, necesita pasar `deep: true` en el argumento de opciones. Tenga en cuenta que no necesita hacer ésto para escuchar mutaciones en Arrays.

  ``` js
  vm.$watch('someObject', callback, {
    deep: true
  })
  vm.someObject.nestedValue = 123
  // callback es llamado
  ```

- **Opción: immediate**

  Si se pasa `immediate: true` en las opciones, hará que el callback sea llamado de inmediato con el valor actual de la expresión:

  ``` js
  vm.$watch('a', callback, {
    immediate: true
  })
  // callback es llamado de inmediato con el valor actual de `a`
  ```

<h3 id="vm-set">vm.$set( object, key, value )</h3>

- **Argumentos:**
  - `{Object} object`
  - `{string} key`
  - `{any} value`

- **Retorna:** el valor asignado.

- **Modo de Uso:**

  Éste es el **alias** del `Vue.set` global.

- **Vea también:** [Vue.set](#Vue-set)

<h3 id="vm-delete">vm.$delete( object, key )</h3>

- **Argumentos:**
  - `{Object} object`
  - `{string} key`

- **Modo de Uso:**

  Éste es el **alias** del `Vue.delete` global.

- **Vea también:** [Vue.delete](#Vue-delete)

## Métodos de instancia / Eventos

<h3 id="vm-on">vm.$on( event, callback )</h3>

- **Argumentos:**
  - `{string} event`
  - `{Function} callback`

- **Modo de Uso:**

  Se escucha a un evento personalizado en el vm actual. Los eventos pueden ser activados con `vm.$emit`. El callback recibirá todos los argumentos adicionales pasados a éstos métodos activadores de eventos.

- **Ejemplo:**

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

  Se escucha a un evento personalizado, pero únicamente una vez. El listener será removido una vez sea activado por primera vez.

<h3 id="vm-off">vm.$off( [event, callback] )</h3>

- **Argumentos:**
  - `{string} [event]`
  - `{Function} [callback]`

- **Modo de Uso:**

  Remueve el(los) listener(s) del evento.

  - Si se pasan argumentos, remueve todos los listeners de eventos;

  - Si sólo se provee el evento, remueve todos los listeners para dicho evento;

  - Si tanto el evento como el callback es dado, remueve el listener para ese callback específico.

<h3 id="vm-emit">vm.$emit( event, [...args] )</h3>

- **Argumentos:**
  - `{string} event`
  - `[...args]`

  Activa un evento sobre la instancia actual. Cualquier argumento adicional será pasado a la función callback del listener.

## Métodos de instancia / Ciclo de vida

<h3 id="vm-mount">vm.$mount( [elementOrSelector] )</h3>

- **Argumentos:**
  - `{Element | string} [elementOrSelector]`
  - `{boolean} [hydrating]`

- **Returns:** `vm` - la instancia misma

- **Modo de Uso:**

  Si una instancia Vue no recibió la opción `el` durante la instanciación, existirá en un estado "no-montado", sin un elemento DOM asociado. `vm.$mount()` puede ser usado para iniciar manualmente el montaje de una instancia Vue no montada.

  Si el argumento `elementOrSelector` no es usado, la plantilla será renderizada como un elemento fuera del DOM, y se tendrá que usar el API DOM nativo para insertarlo en el documento.

  El método retorna la instancia misma, de modo que usted podrá encadenar otros métodos de instancia después.

- **Ejemplo:**

  ``` js
  var MyComponent = Vue.extend({
    template: '<div>Hello!</div>'
  })

  // crea y monta en #app (Reemplazará #app)
  new MyComponent().$mount('#app')

  // el código de arriba hace lo mismo que:
  new MyComponent({ el: '#app' })

  // o, renderiza fuera del DOM y es adicionado luego::
  var component = new MyComponent().$mount()
  document.getElementById('app').appendChild(component.$el)
  ```

- **Vea También:**
  - [Diagrama de Ciclo de Vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)
  - [Renderizado en servidor](../guide/ssr.html)

<h3 id="vm-forceUpdate">vm.$forceUpdate()</h3>

- **Modo de Uso:**

  Obliga a la instancia Vue a ser renderizada de nuevo. Tenga en cuenta que esto no afecta todos los componentes hijos, únicamente a la instancia misma y a los componentes hijos con contenido insertado por slot:

<h3 id="vm-nextTick">vm.$nextTick( [callback] )</h3>

- **Argumentos:**
  - `{Function} [callback]`

- **Modo de Uso:**

  Retrasa la ejecución del callback para ser ejecutado después del siguiente ciclo de atualización de DOM. Úselo inmediatamente después que haya cambiado datos que deban esperar la actualización de DOM. Ésta funcionalidad es la misma que ofrece `Vue.nextTick`, con la excepción que el contexto `this` del callback es automáticamente asignado a la instancia que hace el llamado a ésta función.

  > Nuevo en 2.1.0: retorna un Promise si no se ha definido un callback y Promise sea soportado en el ambiente de ejecución.

- **Ejemplo:**

  ``` js
  new Vue({
    // ...
    methods: {
      // ...
      example: function () {
        // modifica los datos
        this.message = 'changed'
        // el DOM no ha sido actualizado todavía
        this.$nextTick(function () {
          // el DOM está actualizado
          // la instancia actual está asignada a `this`
          this.doSomethingElse()
        })
      }
    }
  })
  ```

- **Vea también:**
  - [Vue.nextTick](#Vue-nextTick)
  - [Cola de actualización asíncrona](../guide/reactivity.html#Async-Update-Queue)

<h3 id="vm-destroy">vm.$destroy()</h3>

- **Modo de Uso:**

  Destruye completamente un vm. Limpia todas sus conexionas a otros vm que existan, desasignando todas sus directivas, desactiva todos sus listeners de eventos.

  Activa los hooks `beforeDestroy` y `destroyed`.

  <p class="tip">En casos de uso normal usted no debe necesitar llamar este método. Es preferible controlar el ciclo de vida de todos los componentes hijos de una forma orientada por datos usando `v-if` y `v-for`.</p>

- **Vea También:** [Diagrama de Ciclo de Vida](../guide/instance.html#Diagrama-de-ciclo-de-vida)

## Directivas

### v-text

- **Espera:** `string`

- **Detalles:**

  Actualiza los elementos `textContent`. Si necesita actualizar la parte de `textContent`, debe usar interpolaciones `{% raw %}{{ Mustache }}{% endraw %}`.

- **Ejemplo:**

  ```html
  <span v-text="msg"></span>
  <!-- igual que -->
  <span>{{msg}}</span>
  ```

- **Vea también:** [Sintaxis de vinculación de datos - interpolaciones](../guide/syntax.html#Text)

### v-html

- **Espera:** `string`

- **Detalles:**

  Actualiza los elementos `innerHTML`. **Tenga en cuenta que los contenidos se insertan como HTML sin formato - no se compilarán como plantillas de Vue**. Si se encuentra intentando componer plantillas usando `v-html`, intente replantear la situación usando componentes en su lugar.

  <p class="tip">La representación dinámica de HTML arbitrario en su sitio web puede ser muy peligrosa porque puede conducir fácilmente a [ataques XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Solo use `v-html` en contenido de confianza y **nunca** en contenido proporcionado por el usuario.</p>

- **Ejemplo:**

  ```html
  <div v-html="html"></div>
  ```
- **Vea también:** [Sintaxis de vinculación de datos - interpolaciones](../guide/syntax.html#Raw-HTML)

### v-show

- **Espera:** `any`

- **Modo de Uso:**

  Alterna la propiedad `display` de CSS del elemento en función de la veracidad del valor de la expresión.

  Esta directiva desencadena transiciones cuando su condición cambia.

- **Vea también:** [Renderización condicional - v-show](../guide/conditional.html#v-show)

### v-if

- **Espera:** `any`

- **Modo de Uso:**

  Representa de forma condicional el elemento en función de la veracidad del valor de la expresión. El elemento y las directivas/componentes que contenga se destruyen y se reconstruyen durante las conmutaciones. Si el elemento es un elemento `<template>`, su contenido se extraerá como el bloque condicional.

  Esta directiva desencadena transiciones cuando su condición cambia.

- **Vea también:** [Renderización condicional - v-if](../guide/conditional.html)

### v-else

- **No espera ninguna expresión**

- **Restricción:** el elemento anterior debe tener `v-if` o `v-else-if`.

- **Modo de Uso:**

  Designa el "bloque else" por `v-if` o la cadena `v-if`/`v-else-if`.

  ```html
  <div v-if="Math.random() > 0.5">
    Ahora me ves
  </div>
  <div v-else>
    Ahora no
  </div>
  ```

- **Vea también:**
  - [Renderización condicional - v-else](../guide/conditional.html#v-else)

### v-else-if

> Nuevo en 2.1.0

- **Espera:** `any`

- **Restriction:** el elemento anterior debe tener `v-if` o `v-else-if`.

- **Modo de Uso:**

  Designa el "bloque else if" para `v-if`. Puede ser encadenado.

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
    No A/B/C
  </div>
  ```

- **Vea también:** [Renderización condicional - v-else-if](../guide/conditional.html#v-else-if)

### v-for

- **Espera:** `Array | Object | number | string`

- **Modo de Uso:**

  Renderiza el elemento o un bloque de template varias veces en función de los datos de origen. El valor de la directiva debe usar sintaxis especial `alias en una expresión` para proporcionar un alias para el elemento actual que se itera en:

  ``` html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  Alternativamente, también puede especificar un alias para el índice (o la clave si se usa en un Objeto):

  ``` html
  <div v-for="(item, index) in items"></div>
  <div v-for="(val, key) in object"></div>
  <div v-for="(val, key, index) in object"></div>
  ```

  El comportamiento predeterminado de `v-for` intentará parchear los elementos in situ sin moverlos. Para obligarlo a reordenar elementos, debe proporcionar una sugerencia de pedido con el atributo especial `key`:

  ``` html
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```

  El uso detallado de `v-for` se explica en la sección de guía vinculada a continuación.

- **Vea también:**
  - [Renderización de Listas](../guide/list.html)
  - [key](../guide/list.html#key)

### v-on

- **Forma abreviada:** `@`

- **Espera:** `Function | Inline Statement`

- **Argumento:** `event (required)`

- **Modificadores:**
  - `.stop` - llama a `event.stopPropagation()`.
  - `.prevent` - llama a `event.preventDefault()`.
  - `.capture` - añade un evento de escucha en modo captura.
  - `.self` - solo controla si el evento se envió desde este elemento.
  - `.{keyCode | keyAlias}` - solo controla si se disparan ciertas teclas.
  - `.native` - escucha un evento nativo en el elemento raíz del componente.

- **Modo de Uso:**

  Adjunta un detector de eventos al elemento. El tipo de evento se denota por el argumento. La expresión puede ser un nombre de método o una instrucción en línea, o simplemente se omite cuando hay modificadores presentes.

  Cuando se usa en un elemento normal, solo escucha **eventos DOM nativos**. Cuando se utiliza en un componente de elemento personalizado, también escucha **eventos personalizados** emitidos en ese componente secundario.

  Al escuchar eventos de DOM nativos, el método recibe el evento nativo como único argumento. Si usa la instrucción en línea, la instrucción tiene acceso a la propiedad especial `$event`: `v-on: click="handle('ok', $event)"`.

- **Ejemplo:**

  ```html
  <!-- gestor de métodos -->
  <button v-on:click="doThis"></button>

  <!-- declaración en línea -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- Forma abreviada -->
  <button @click="doThis"></button>

  <!-- detener propagación -->
  <button @click.stop="doThis"></button>

  <!-- prevent por defecto -->
  <button @click.prevent="doThis"></button>

  <!-- prevent por defecto sin expresión -->
  <form @submit.prevent></form>

  <!-- modificadores en cadena -->
  <button @click.stop.prevent="doThis"></button>

  <!-- modificador de key usando keyAlias -->
  <input @keyup.enter="onEnter">

  <!-- modificador de key usando keyCode -->
  <input @keyup.13="onEnter">
  ```

  Escuchar eventos personalizados en un componente secundario (se llama al controlador cuando se emite "my-event" en el hijo):

  ```html
  <my-component @my-event="handleThis"></my-component>

  <!-- declaración en línea -->
  <my-component @my-event="handleThis(123, $event)"></my-component>

  <!-- evento nativo en el componente -->
  <my-component @click.native="onClick"></my-component>
  ```

- **Vea también:**
  - [Métodos y manejo de eventos](../guide/events.html)
  - [Componentes - Eventos personalizados](../guide/components.html#Custom-Events)

### v-bind

- **Forma abreviada:** `:`

- **Espera:** `any (with argument) | Object (without argument)`

- **Argumento:** `attrOrProp (opcional)`

- **Modificadores:**
  - `.prop` - Enlaza una propiedad en el DOM en lugar de un atributo. ([¿Cuál es la diferencia?](http://stackoverflow.com/questions/6003819/properties-and-attributes-in-html#answer-6004028))
  - `.camel` - transforma el nombre del atributo kebab-case en camelCase. (soportado desde 2.1.0)

- **Modo de Uso:**

  Vincula dinámicamente uno o más atributos, o un componente prop a una expresión.

  Cuando se utiliza para enlazar el atributo `class` o` style`, admite tipos de valores adicionales como Array u Objetos. Consulte la sección de la guía vinculada a continuación para obtener más detalles.

  Cuando se usa para vincular una propiedad éste debe declararse correctamente en el componente hijo.

  Cuando se usa sin un argumento, se puede usar para vincular un objeto que contiene pares de nombre-valor de atributo. Tenga en cuenta que en este modo `class` y `style` no son compatibles con Array u Objetos.

- **Ejemplo:**

  ```html
  <!-- vincular un atributo -->
  <img v-bind:src="imageSrc">

  <!-- Forma abreviada -->
  <img :src="imageSrc">

  <!-- concatenar cadenas de texto en una línea -->
  <img :src="'/path/to/images/' + fileName">

  <!-- vincular clases -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]">

  <!-- Vincular estilos -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- Vincular un objeto con atributos -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!--Vincular un atributo del DOM con un modificador prop -->
  <div v-bind:text-content.prop="text"></div>

  <!-- Vincular una propiedad. "prop" debe declararse en my-component. -->
  <my-component :prop="someThing"></my-component>

  <!-- XLink -->
  <svg><a :xlink:special="foo"></a></svg>
  ```

  El modificador `.camel` permite 'camelizar' un nombre de atributo` v-bind` cuando se usan plantillas in-DOM, p. el atributo SVG `viewBox`:

  ``` html
  <svg :view-box.camel="viewBox"></svg>
  ```

  `.camel` no es necesario si está utilizando templates con cadenas de texto o compilando con `vue-loader`/`vueify`.

- **Vea también:**
  - [Vinculación de clases y estilos](../guide/class-and-style.html)
  - [Componentes - Componente de propiedades](../guide/components.html#Props)

### v-model

- **Espera:** varía en función del valor del elemento de entrada del formulario o salida de los componentes

- **Limitado a:**
  - `<input>`
  - `<select>`
  - `<textarea>`
  - componentes

- **Modificadores:**
  - [`.lazy`](../guide/forms.html#lazy) - escucha los eventos `change` en lugar de` input`
  - [`.number`](../guide/forms.html#number) - cambia un input de texto a números
  - [`.trim`](../guide/forms.html#trim) - elimina los espacios en blanco dentro de un input

- **Modo de Uso:**

  Cree un enlace bidireccional en un elemento de entrada de formulario o un componente. Para un uso detallado y otras notas, consulte la sección de Guía vinculada a continuación.

- **Vea también:**
  - [Enlaces de entrada de formulario](../guide/forms.html)
  - [Componentes: componentes de entrada de formularios con eventos personalizados](../guide/components.html#Form-Input-Components-using-Custom-Events)

### v-pre

- **No espera ninguna expresión**

- **Modo de Uso:**

  Omite la compilación de este elemento y todos sus hijos. Puede usar lo para mostrar etiquetas de mustache sin formato. Omitir un gran número de nodos sin directivas puede acelerar la compilación.

- **Ejemplo:**

  ```html
  <span v-pre>{{ esto no será compilado }}</span>
   ```

### v-cloak

- **No espera ninguna expresión**

- **Modo de Uso:**

  Esta directiva permanecerá en el elemento hasta que la instancia de Vue asociada finalice la compilación. Combinado con reglas de CSS como `[v-cloak] {display: none}`, esta directiva puede usarse para ocultar enlaces de mustache no compilados hasta que la instancia de Vue esté lista.

- **Ejemplo:**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```html
  <div v-cloak>
    {{ mensaje }}
  </div>
  ```

  El `<div>` no estará visible hasta que la compilación haya finalizado.

### v-once

- **No espera ninguna expresión**

- **Detalles:**

  Renderice el elemento y componente **una vez** solamente. En las repeticiones posteriores, el elemento/componente y todos sus elementos secundarios se tratarán como contenido estático y se omitirán. Esto se puede usar para optimizar el rendimiento de la actualización.

  ```html
  <!-- elemento único -->
  <span v-once>Esto nunca cambiará: {{msg}}</span>
  <!-- el elemento tiene hijos -->
  <div v-once>
    <h1>Comentario</h1>
    <p>{{msg}}</p>
  </div>
  <!-- component -->
  <my-componente v-once :comment="msg"></my-component>
  <!-- directiva v-for -->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

- **Vea también:**
  - [Sintaxis de enlace de datos: interpolaciones](../guide/syntax.html#Text)
  - [Componentes - Componentes estáticos con v-once](../guide/components.html#Cheap-Static-Components-with-v-once)

## Special Attributes

### key

- **Espera:** `string`

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

- **Espera:** `string`

  `ref` is used to register a reference to an element or a child component. The reference will be registered under the parent component's `$refs` object. If used on a plain DOM element, the reference will be that element; if used on a child component, the reference will be component instance:

  ``` html
  <!-- vm.$refs.p will be the DOM node -->
  <p ref="p">hello</p>

  <!-- vm.$refs.child will be the child comp instance -->
  <child-comp ref="child"></child-comp>
  ```

  When used on elements/components with `v-for`, the registered reference will be an Array containing DOM nodes or component instances.

  An important note about the ref registration timing: because the refs themselves are created as a result of the render function, you cannot access them on the initial render - they don't exist yet! `$refs` is also non-reactive, therefore you should not attempt to use it in templates for data-binding.

- **Vea también:** [Child Component Refs](../guide/components.html#Child-Component-Refs)

### slot

- **Espera:** `string`

  Used on content inserted into child components to indicate which named slot the content belongs to.

  For detailed usage, see the guide section linked below.

- **Vea también:** [Named Slots](../guide/components.html#Named-Slots)

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

- **Vea también:** [Dynamic Components](../guide/components.html#Dynamic-Components)

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

- **Vea también:** [Transitions: Entering, Leaving, and Lists](../guide/transitions.html)

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

- **Vea también:** [Transitions: Entering, Leaving, and Lists](../guide/transitions.html)

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

- **Vea también:** [Dynamic Components - keep-alive](../guide/components.html#keep-alive)

### slot

- **Props:**
  - `name` - string, Used for named slot.

- **Modo de Uso:**

  `<slot>` serve as content distribution outlets in component templates. `<slot>` itself will be replaced.

  For detailed usage, see the guide section linked below.

- **Vea también:** [Content Distribution with Slots](../guide/components.html#Content-Distribution-with-Slots)

## VNode Interface

- Please refer to the [VNode class declaration](https://github.com/vuejs/vue/blob/dev/src/core/vdom/vnode.js).

## Server-Side Rendering

- Please refer to the [vue-server-renderer package documentation](https://github.com/vuejs/vue/tree/dev/packages/vue-server-renderer).
