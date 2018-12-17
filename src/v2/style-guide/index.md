---
type: style-guide
---

# Guía de Estilo <sup class="beta">beta</sup>

Esta es la guía de estilo oficial para el código específico de Vue. Si usa Vue en un proyecto, es una gran referencia para evitar errores, código innecesario y anti-patrones. Sin embargo, no creemos que ninguna guía de estilo sea ideal para todos los equipos o proyectos, por lo que se recomienda tener en cuenta las desviaciones basadas en la experiencia pasada, la tecnología del entorno y los valores personales.

En su mayor parte, también evitamos sugerencias sobre JavaScript o HTML en general. No nos importa si usa punto y coma al final de sus líneas. No nos importa si su HTML utiliza comillas simples o dobles para los valores de los atributos. Sin embargo, existirán algunas excepciones, donde hemos encontrado que un patrón particular es útil en el contexto de Vue.

> **Pronto, también le daremos consejos para la aplicación de formato.** A veces simplemente tendrá que ser disciplinado, pero siempre que sea posible, trataremos de mostrarte cómo usar ESLint y otros procesos automatizados para hacer más simple la aplicación de formato.

Finalmente, hemos dividido las reglas en cuatro categorías:



## Categorías de reglas

### Prioridad A: Esencial

Estas reglas ayudan a prevenir errores, así que aprenda y adóptelas a toda costa. Las excepciones pueden existir, pero deben ser muy raras y sólo deben ser realizadas por aquellos con conocimiento experto tanto de JavaScript como de Vue.

### Prioridad B: Altamente recomendado

Se ha comprobado que estas reglas mejoran la legibilidad y/o la experiencia del desarrollador en la mayoría de los proyectos. El código seguirá funcionando si no se aplican, pero las infracciones deben ser raras y bien justificadas.

### Prioridad C: Recomendado

Cuando existen varias opciones igualmente buenas, se puede hacer una elección arbitraria para garantizar la consistencia. En estas reglas, describimos cada opción aceptable y sugerimos una opción por defecto. Esto significa que puede sentirse libre de tomar una decisión diferente en su propia base de código, siempre y cuando sea consistente y tenga una buena razón. Pero por favor, ¡Que tenga una buena razón! Al adaptarse al estándar de la comunidad, podrá:

1. entrenar su cerebro para analizar más fácilmente la mayor parte del código de la comunidad que encuentre
2. poder copiar y pegar la mayoría de los ejemplos de código comunitarios sin necesidad de modificarlos
3. encontrar a menudo  que los nuevos empleados ya estén acostumbrados a su estilo de codificación preferido, al menos en lo que respecta a Vue

### Prioridad D: Uso con Precaución

Algunas características de Vue existen para acomodar casos extremos o migraciones más fluidas desde una base de código heredada. Sin embargo, cuando se usan en exceso, pueden hacer que su código sea más difícil de mantener o incluso convertirse en una fuente de errores. Estas reglas arrojan luz sobre las características potencialmente riesgosas, describiendo cuándo y por qué deben evitarse.



## Reglas de prioridad A: Esencial (Prevención de errores)



### Nombres multi-palabra para componentes  <sup data-p="a">esencial</sup>

**Los nombres de los componentes deben ser siempre de varias palabras, excepto para los componentes de la raíz `App`.**

Esto [evita conflictos](http://w3c.github.io/webcomponents/spec/custom/#valid-custom-element-name) con elementos HTML existentes y futuros, ya que todos los elementos HTML están compuestos por una sola palabra.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` js
Vue.component('todo', {
  // ...
})
```

``` js
export default {
  name: 'Todo',
  // ...
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` js
Vue.component('todo-item', {
  // ...
})
```

``` js
export default {
  name: 'TodoItem',
  // ...
}
```
{% raw %}</div>{% endraw %}



### Datos de los componentes <sup data-p="a">esencial</sup>

**En un componente, `data` tiene que ser una función.**

Cuando se usa la propiedad `data` en un componente (es decir, en cualquier lugar excepto en `new Vue`), el valor debe ser una función que devuelva un objeto.

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

Cuando el valor de `data` es un objeto, se comparte entre todas las instancias de un componente. Imagina, por ejemplo, un componente `TodoList` con estos datos:

``` js
data: {
  listTitle: '',
  todos: []
}
```

Es posible que queramos reutilizar este componente, permitiendo a los usuarios mantener múltiples listas (por ejemplo, para hacer compras, listas de deseos, tareas diarias, etc.). Pero hay un problema, dado que cada instancia del componente hace referencia al mismo objeto de datos, el cambio del título de una lista también cambiará el título de todas las demás listas. Lo mismo se aplica para añadir/editar/eliminar una lista.

En su lugar, queremos que cada instancia de un componente sólo gestione sus propios datos. Para que esto suceda, cada instancia debe generar un objeto de datos único. En JavaScript, esto se puede lograr devolviendo el objeto en una función:

``` js
data: function () {
  return {
    listTitle: '',
    todos: []
  }
}
```
{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` js
Vue.component('some-comp', {
  data: {
    foo: 'bar'
  }
})
```

``` js
export default {
  data: {
    foo: 'bar'
  }
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien
``` js
Vue.component('some-comp', {
  data: function () {
    return {
      foo: 'bar'
    }
  }
})
```

``` js
// En un archivo .vue
export default {
  data () {
    return {
      foo: 'bar'
    }
  }
}
```

``` js
// Está bien usar un objeto directamente en una instancia
// raíz de Vue, ya que sólo existirá una única instancia.
new Vue({
  data: {
    foo: 'bar'
  }
})
```
{% raw %}</div>{% endraw %}



### Definiciones de prop <sup data-p="a">esencial</sup>

**Las definiciones de prop deben ser lo más detalladas posible**

En el código enviado a un repositorio, las definiciones de los props deben ser siempre tan detalladas como sea posible, especificando al menos el tipo o tipos.

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

Las [definiciones detalladas de los props](https://vuejs.org/v2/guide/components.html#Prop-Validation) tienen dos ventajas:

- Documentan la API del componente, de modo que es fácil ver cómo debe ser utilizado.
- Durante el desarrollo, Vue le advertirá si un componente se proporciona con un formato incorrecto, lo que le ayudará a detectar posibles fuentes de error.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` js
// Esto esta bien únicamete durante el prototipado
props: ['status']
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` js
props: {
  status: String
}
```

``` js
// ¡Aún mejor!
props: {
  status: {
    type: String,
    required: true,
    validator: function (value) {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].indexOf(value) !== -1
    }
  }
}
```
{% raw %}</div>{% endraw %}



### Keys (claves) en un `v-for` <sup data-p="a">esencial</sup>

**Usa siempre `key` con `v-for`.**

El `key` en un `v-for` es _siempre_ necesario en los componentes, para mantener el estado interno de los componentes en el subárbol. Incluso para los elementos, sin embargo, es una buena práctica mantener un comportamiento predecible, como la [constancia de objetos](https://bost.ocks.org/mike/constancy/) en las animaciones.

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

Digamos que tienes una lista de pendientes:

``` js
data: function () {
  return {
    todos: [
      {
        id: 1,
        text: 'Learn to use v-for'
      },
      {
        id: 2,
        text: 'Learn to use key'
      }
    ]
  }
}
```

Luego los ordenas alfabéticamente. Al actualizar el DOM, Vue optimizará el renderizado para realizar las mutaciones DOM más eficientes posibles. Esto podría significar borrar el primer elemento de la lista, y luego agregarlo de nuevo al final de la lista.

El problema es que hay casos en los que es importante no borrar los elementos que permanecerán en el DOM. Por ejemplo, puede usar `<transition-group>` para animar la ordenación de la lista o mantener el foco si el elemento renderizado es un `<input>`. En estos casos, añadir una clave única para cada elemento (por ejemplo, ``:key="todo.id"`) le dirá a Vue cómo comportarse de forma más predecible.

En nuestra experiencia, es mejor _siempre_ agregar una clave única, para que usted y su equipo simplemente nunca tengan que preocuparse por estos casos extremos. Entonces, en los raros y críticos escenarios donde la constancia de los objetos no es necesaria, puedes hacer una excepción consciente.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
<ul>
  <li v-for="todo in todos">
    {{ todo.text }}
  </li>
</ul>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<ul>
  <li
    v-for="todo in todos"
    :key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```
{% raw %}</div>{% endraw %}



###  Alcance del estilo de componentes <sup data-p="a">esencial</sup>

**Para las aplicaciones, los estilos en un componente de `App` del nivel superior y en los componentes de diseño pueden ser globales, pero todos los demás componentes deben tener siempre un alcance.**

Esto sólo es relevante para [componentes de un sólo archivo](../guide/single-file-components.html). Que _No requiere_ que se utilice el atributo[`scoped`](https://vue-loader.vuejs.org/en/features/scoped-css.html). El alcance podría ser a través de [módulos CSS](https://vue-loader.vuejs.org/en/features/css-modules.html), una estrategia basada en clases como [BEM](http://getbem.com/), u otra librería/convención.

**Las librerías de componentes, sin embargo, deberían preferir una estrategia basada en clases en lugar de usar el atributo `scoped` **.

Esto facilita el override de los estilos internos, con nombres de clase legibles por el ser humano que no tienen una especificidad demasiado alta, pero que aún así es muy poco probable que provoquen un conflicto.

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

Si está desarrollando un proyecto grande, trabajando con otros desarrolladores, o a veces incluye HTML/CSS de terceros (p.ej. de Auth0), un alcance consistente asegurará que sus estilos sólo se apliquen a los componentes para los que están diseñados.

Más allá del atributo `scoped`, el uso de nombres de clase únicos puede ayudar a asegurar que el CSS de terceros no se aplique a su propio HTML. Por ejemplo, muchos proyectos utilizan los nombres de clase `button`, `btn`, o `icon`, de modo que incluso si no se utiliza una estrategia como BEM, añadir un prefijo específico de la aplicación y/o un prefijo específico del componente (por ejemplo, `ButtonClose-icon') puede proporcionar cierta protección.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
<template>
  <button class="btn btn-close">X</button>
</template>

<style>
.btn-close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<template>
  <button class="button button-close">X</button>
</template>

<!-- Using the `scoped` attribute -->
<style scoped>
.button {
  border: none;
  border-radius: 2px;
}

.button-close {
  background-color: red;
}
</style>
```

``` html
<template>
  <button :class="[$style.button, $style.buttonClose]">X</button>
</template>

<!-- Using CSS modules -->
<style module>
.button {
  border: none;
  border-radius: 2px;
}

.buttonClose {
  background-color: red;
}
</style>
```

``` html
<template>
  <button class="c-Button c-Button--close">X</button>
</template>

<!-- Using the BEM convention -->
<style>
.c-Button {
  border: none;
  border-radius: 2px;
}

.c-Button--close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}



### Nombres de propiedades privadas <sup data-p="a">esencial</sup>

**Siempre usa el prefijo `$_` para propiedades privadas personalizadas en un plugin, mixin, etc. Luego, para evitar conflictos con el código de otros autores, incluye también un ámbito con nombre (p. ej. `$_elNombreDeTuPlugin_`).**

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

Vue utiliza el prefijo `_` para definir sus propias propiedades privadas, por lo que usar el mismo prefijo (por ejemplo,` _update`) corre el riesgo de sobrescribir una propiedad de la instancia. Incluso si revisas y Vue no está utilizando actualmente un nombre de propiedad en particular, no hay garantía de que no surja un conflicto en una versión posterior.

En cuanto al prefijo `$`, su propósito dentro del ecosistema de Vue son las propiedades de instancia especiales que están expuestas al usuario, por lo que su uso para las propiedades _privadas_ no sería apropiado.

En su lugar, recomendamos combinar los dos prefijos en `$ _`, como una convención para las propiedades privadas definidas por el usuario que garantizan que no haya conflictos con Vue.
{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` js
var myGreatMixin = {
  // ...
  methods: {
    update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    _update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    $update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    $_update: function () {
      // ...
    }
  }
}
```

{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` js
var myGreatMixin = {
  // ...
  methods: {
    $_myGreatMixin_update: function () {
      // ...
    }
  }
}
```
{% raw %}</div>{% endraw %}



## Reglas de prioridad B: Altamente recomendado (Mejorar la legibilidad)



### Archivos de componentes <sup data-p="b">Altamente recomendado</sup>

**Siempre que un sistema de compilación esté disponible para concatenar archivos, cada componente debe estar en su propio archivo.**

Esto le ayuda a encontrar más rápidamente un componente cuando necesites editarlo o revisar cómo usarlo.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` js
Vue.component('TodoList', {
  // ...
})

Vue.component('TodoItem', {
  // ...
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

```
components/
|- TodoList.js
|- TodoItem.js
```

```
components/
|- TodoList.vue
|- TodoItem.vue
```
{% raw %}</div>{% endraw %}



### Mayúsculas en componentes de un sólo archivo  <sup data-p="b">Altamente recomendado</sup>

**Los nombres de archivo de [componentes de un sólo archivo](../guide/single-file-components.html) deben ser siempre PascalCase o siempre kebab-case.**

PascalCase funciona mejor con el autocompletado en editores de código, ya que es consistente con la forma en que hacemos referencia a los componentes en JS(X) y plantillas, siempre que sea posible. Sin embargo, los nombres de archivos con mayúsculas y minúsculas a veces pueden crear problemas en sistemas de archivos que no distinguen entre mayúsculas y minúsculas, razón por la cual el kebab-case también es perfectamente aceptable.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

```
components/
|- mycomponent.vue
```

```
components/
|- myComponent.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

```
components/
|- MyComponent.vue
```

```
components/
|- my-component.vue
```
{% raw %}</div>{% endraw %}



### Nombres de componentes base <sup data-p="b">Altamente recomendado</sup>

**Los componentes básicos (también conocidos como componentes de presentación, tontos o puros) que aplican estilos y convenciones específicas de aplicaciones deben comenzar con un prefijo específico, como `Base`, `App` o `V`.**

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

Estos componentes sientan las bases para un estilo y comportamiento coherentes en su aplicación. **Sólo** pueden contener:

- Elementos HTML,
- otros componentes con el prefijo `Base`, y
- Componentes UI de terceros.

Pero ellos **nunca** contendrán estado global (por ejemplo, de almacenamiento Vuex).

Sus nombres a menudo incluyen el nombre de un elemento que envuelven (p. ej: `BaseButton`, `BaseTable`), a menos que no exista ningún elemento para su propósito específico (p. ej. `BaseIcon`). Si construye componentes similares para un contexto más específico, casi siempre consumirán estos componentes (por ejemplo, `BaseButton` puede usarse en `ButtonSubmit`).

Algunas ventajas de esta convención:

- Cuando se organizan alfabéticamente en editores, los componentes básicos de su aplicación se listan todos juntos, lo que facilita su identificación.

- Dado que los nombres de los componentes siempre deben ser multipalabra, esta convención evita que tenga que elegir un prefijo arbitrario para las envolturas de componentes simples (por ejemplo, `MyButton`, `VueButton`).

- Dado que estos componentes se utilizan con tanta frecuencia, es posible que desee simplemente hacerlos globales en lugar de importarlos a cualquier parte. Un prefijo lo hace posible con Webpack:

  ``` js
  var requireComponent = require.context("./src", true, /^Base[A-Z]/)
  requireComponent.keys().forEach(function (fileName) {
    var baseComponentConfig = requireComponent(fileName)
    baseComponentConfig = baseComponentConfig.default || baseComponentConfig
    var baseComponentName = baseComponentConfig.name || (
      fileName
        .replace(/^.+\//, '')
        .replace(/\.\w+$/, '')
    )
    Vue.component(baseComponentName, baseComponentConfig)
  })
  ```

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

```
components/
|- MyButton.vue
|- VueTable.vue
|- Icon.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

```
components/
|- BaseButton.vue
|- BaseTable.vue
|- BaseIcon.vue
```

```
components/
|- AppButton.vue
|- AppTable.vue
|- AppIcon.vue
```

```
components/
|- VButton.vue
|- VTable.vue
|- VIcon.vue
```
{% raw %}</div>{% endraw %}



### Nombres de componentes de una sola instancia <sup data-p="b">Altamente recomendado</sup>

**Los componentes que sólo deberían tener una instancia activa deben comenzar con el prefijo `The`, para indicar que sólo puede haber una.**

Esto no significa que el componente sólo se utilice en una sola página, sino que sólo se utilizará una vez _por página_. Estos componentes nunca aceptan ningún prop, ya que son específicos de su aplicación, no de su contexto dentro de ella. Si encuentra la necesidad de añadir props, es una buena indicación de que se trata de un componente reutilizable que sólo se utiliza una vez por página _por ahora_.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

```
components/
|- Heading.vue
|- MySidebar.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

```
components/
|- TheHeading.vue
|- TheSidebar.vue
```
{% raw %}</div>{% endraw %}



### Nombres de componentes estrechamente acoplados  <sup data-p="b">Altamente recomendado</sup>

**Los componentes hijos que están estrechamente acoplados con su padres deben incluir el nombre del componente padre como prefijo.**

Si un componente sólo tiene sentido en el contexto de un único componente padre, esa relación debe ser evidente en su nombre. Dado que los editores suelen organizar los archivos alfabéticamente, esto también mantiene estos archivos relacionados uno al lado del otro.

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

Puede que tenga la tentación de resolver este problema anidando los componentes hijo en directorios que llevan el nombre de su padre. Por ejemplo:

```
components/
|- TodoList/
   |- Item/
      |- index.vue
      |- Button.vue
   |- index.vue
```

o:

```
components/
|- TodoList/
   |- Item/
      |- Button.vue
   |- Item.vue
|- TodoList.vue
```

Esto no es recomendable, ya que da como resultado:

- Muchos archivos con nombres similares, lo que dificulta el cambio rápido de archivos en los editores de código.
- Muchos subdirectorios anidados, lo que aumenta el tiempo necesario para navegar por los componentes en la barra lateral de un editor.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

```
components/
|- TodoList.vue
|- TodoItem.vue
|- TodoButton.vue
```

```
components/
|- SearchSidebar.vue
|- NavigationForSearchSidebar.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

```
components/
|- TodoList.vue
|- TodoListItem.vue
|- TodoListItemButton.vue
```

```
components/
|- SearchSidebar.vue
|- SearchSidebarNavigation.vue
```
{% raw %}</div>{% endraw %}



### Orden de las palabras en el nombre de los componentes<sup data-p="b">Altamente recomendado</sup>

**Los nombres de los componentes deben comenzar con las palabras de más alto nivel (a menudo las más generales) y terminar con palabras modificadoras descriptivas.**

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

Tal vez se esté preguntando:

> ¿Por qué obligaríamos a los nombres de los componentes a utilizar un lenguaje menos natural?

En un lenguaje natural, los adjetivos y otros descriptores aparecen típicamente antes de los sustantivos, mientras que las excepciones requieren palabras conectoras. Por ejemplo:

- Café con leche
- Sopa _del_ día
- Visitante _al_ museo

Definitivamente puede incluir estas palabras de conexión en los nombres de los componentes si lo desea, pero el orden sigue siendo importante.

También tenga en cuenta que **lo que se considera "nivel más alto" será contextual a su aplicación**. Por ejemplo, imagine una aplicación con un formulario de búsqueda. Puede incluir componentes como éste:

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```

Como puede ver, es bastante difícil ver qué componentes son específicos para la búsqueda. Ahora vamos a renombrar los componentes según la regla:

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputExcludeGlob.vue
|- SearchInputQuery.vue
|- SettingsCheckboxLaunchOnStartup.vue
|- SettingsCheckboxTerms.vue
```

Dado que los editores suelen organizar los archivos alfabéticamente, todas las relaciones importantes entre los componentes son ahora evidentes de un vistazo.

Puede que tenga la tentación de resolver este problema de forma diferente, anidando todos los componentes de búsqueda en un directorio de "búsqueda", y luego todos los componentes de configuración en un directorio de "configuración". Sólo recomendamos considerar este enfoque en aplicaciones muy grandes (por ejemplo, 100+ componentes), por estas razones:

- Generalmente toma más tiempo navegar a través de subdirectorios anidados, que desplazarse a través de un único directorio de `componentes'.
- Los conflictos de nombres (por ejemplo, múltiples componentes de `ButtonDelete.vue`) hacen más difícil navegar rápidamente a un componente específico en un editor de código.
- La refactorización se hace más difícil, porque a menudo no basta con buscar y reemplazar para actualizar las referencias relativas a un componente desplazado.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputQuery.vue
|- SearchInputExcludeGlob.vue
|- SettingsCheckboxTerms.vue
|- SettingsCheckboxLaunchOnStartup.vue
```
{% raw %}</div>{% endraw %}



### Autocerrar componentes  <sup data-p="b">Altamente recomendado</sup>

**Los componentes sin contenido deben cerrarse automáticamente en [componentes de un sólo archivo](../guide/single-file-components.html), plantillas de cadenas de texto y [JSX](../guide/render-function.html#JSX) pero nunca en plantillas DOM.**

Los componentes que se cierran por sí mismos comunican que no sólo no tienen contenido, sino que están **destinados** a no tener contenido. Es la diferencia entre una página en blanco en un libro y una llamada "Esta página intencionalmente dejada en blanco". Su código también es más limpio sin la etiqueta de cierre innecesaria.

Desafortunadamente, HTML no permite que los elementos personalizados se cierren por sí solos - sólo [elementos "void" oficiales](https://www.w3.org/TR/html/syntax.html#void-elements). Es por eso que la estrategia sólo es posible cuando el compilador de plantillas de Vue puede alcanzar la plantilla antes del DOM, y luego servir el HTML compatible con las especificaciones de DOM.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
<!-- En componentes de un sólo archivo, plantillas de cadenas de texto y JSX -->
<MyComponent></MyComponent>
```

``` html
<!-- En plantillas DOM -->
<my-component/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<!-- En componentes de un sólo archivo, plantillas de cadenas de texto y JSX -->
<MyComponent/>
```

``` html
<!-- En plantillas DOM -->
<my-component></my-component>
```
{% raw %}</div>{% endraw %}



### Mayúsculas en el nombre del componente en las plantillas <sup data-p="b">Altamente recomendado</sup>

**En la mayoría de los proyectos, los nombres de los componentes deben ser siempre PascalCase en [componentes de un sólo archivo](../guide/single-file-components.html) y plantillas de cadenas de texto, pero kebab-case en plantillas DOM.**

PascalCase tiene algunas ventajas sobre el kebab-case:

- Los editores pueden autocompletar los nombres de los componentes en las plantillas, ya que PascalCase también se utiliza en JavaScript.
- `<MyComponent>` es más visualmente distinto de un elemento HTML de una sola palabra que `<my-component>`, porque hay dos diferencias de caracteres (las dos mayúsculas y minúsculas), en lugar de uno solo (un guión).
- Si utiliza elementos personalizados que no son de Vue en sus plantillas, como un componente web, PascalCase se asegura de que sus componentes de Vue permanezcan claramente visibles.

Desafortunadamente, debido a la insensibilidad a las mayúsculas y minúsculas de HTML, las plantillas DOM deben seguir usando kebab-case.

También tenga en cuenta que si ya ha invertido mucho tiempo en kebab-case, la consistencia con las convenciones HTML y la posibilidad de utilizar el mismo formato en todos sus proyectos puede ser más importante que las ventajas enumeradas anteriormente. En esos casos, **el uso del kebab-case en todas partes también es aceptable.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
<!-- En componentes de un sólo archivo y plantillas de cadenas de texto -->
<mycomponent/>
```

``` html
<!-- En componentes de un sólo archivo y plantillas de cadenas de texto -->
<myComponent/>
```

``` html
<!-- En plantillas DOM -->
<MyComponent></MyComponent>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<!-- En componentes de un sólo archivo y plantillas de cadenas de texto -->
<MyComponent/>
```

``` html
<!-- En plantillas DOM -->
<my-component></my-component>
```

O

``` html
<!-- En cualquier parte -->
<my-component></my-component>
```
{% raw %}</div>{% endraw %}



### Mayúsculas en el nombre de componentes en JS/JSX  <sup data-p="b">Altamente recomendado</sup>

**Los nombres de los componentes en JS/[JSX](../guide/render-function.html#JSX) siempre deben ser PascalCase, aunque puede haber kebab-case dentro de cadenas de texto para aplicaciones más simples que sólo utilizan el registro global de componentes a través de `Vue.component`.**.

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

En JavaScript, PascalCase es la convención para las clases y los constructores de prototipos - esencialmente, cualquier cosa que pueda tener instancias distintas. Los componentes Vue también tienen instancias, por lo que tiene sentido usar también PascalCase. Como ventaja añadida, el uso de PascalCase dentro de JSX (y plantillas) permite a los lectores de código distinguir más fácilmente entre componentes y elementos HTML.

Sin embargo, para aplicaciones que utilizan **sólo** definiciones de componentes globales a través de `Vue.component`, recomendamos kebab-case en su lugar. Las razones son:

- Es raro que los componentes globales estén referenciados en JavaScript, por lo que seguir una convención para JavaScript tiene menos sentido.
- Estas aplicaciones siempre incluyen muchas plantillas en DOM, donde [kebab-case **debe** ser usado](#Mayusculas-en-el-nombre-del-componente-en-las-plantillas-Altamente-recomendado).

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` js
Vue.component('myComponent', {
  // ...
})
```

``` js
import myComponent from './MyComponent.vue'
```

``` js
export default {
  name: 'myComponent',
  // ...
}
```

``` js
export default {
  name: 'my-component',
  // ...
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` js
Vue.component('MyComponent', {
  // ...
})
```

``` js
Vue.component('my-component', {
  // ...
})
```

``` js
import MyComponent from './MyComponent.vue'
```

``` js
export default {
  name: 'MyComponent',
  // ...
}
```
{% raw %}</div>{% endraw %}



### Nombres de componentes con palabras completas <sup data-p="b">Altamente recomendado</sup>

**Los nombres de los componentes deben preferir las palabras completas a las abreviaturas.**

El autocompletado en los editores hace que el coste de escribir nombres más largos sea muy bajo, mientras que la claridad que proporcionan es inestimable. Las abreviaturas poco comunes, en particular, deben evitarse siempre.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

```
components/
|- SdSettings.vue
|- UProfOpts.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

```
components/
|- StudentDashboardSettings.vue
|- UserProfileOptions.vue
```
{% raw %}</div>{% endraw %}



### Mayúsculas para nombres de Props <sup data-p="b">Altamente recomendado</sup>

**Los nombres de los props deben usar siempre camelCase durante la declaración, pero kebab-case en las plantillas y [JSX](../guide/render-function.html#JSX).**

Simplemente seguimos las convenciones de cada idioma. Dentro de JavaScript, camelCase es más natural. Dentro de HTML, es el kebab-case.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` js
props: {
  'greeting-text': String
}
```

``` html
<WelcomeMessage greetingText="hi"/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` js
props: {
  greetingText: String
}
```

``` html
<WelcomeMessage greeting-text="hi"/>
```
{% raw %}</div>{% endraw %}



### Elementos multiatributos <sup data-p="b">Altamente recomendado</sup>

**Los elementos con múltiples atributos deben abarcar múltiples líneas, con un atributo por línea.**

En JavaScript, dividir objetos con múltiples propiedades sobre múltiples líneas es ampliamente considerado una buena convención, porque es mucho más fácil de leer. Nuestras plantillas y [JSX](../guide/render-function.html#JSX) merecen la misma consideración.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
<img src="https://vuejs.org/images/logo.png" alt="Vue Logo">
```

``` html
<MyComponent foo="a" bar="b" baz="c"/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<img
  src="https://vuejs.org/images/logo.png"
  alt="Vue Logo"
>
```

``` html
<MyComponent
  foo="a"
  bar="b"
  baz="c"
/>
```
{% raw %}</div>{% endraw %}



###  Expresiones simples en plantillas <sup data-p="b">Altamente recomendado</sup>

**Las plantillas de componentes sólo deben incluir expresiones simples, con expresiones más complejas refactorizadas en propiedades o métodos calculados.**

Las expresiones complejas en sus plantillas las hacen menos declarativas. Debemos esforzarnos por describir _lo que debe_ aparecer, no _cómo_ estamos calculando ese valor. Las propiedades y métodos calculados también permiten reutilizar el código.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
{{
  fullName.split(' ').map(function (word) {
    return word[0].toUpperCase() + word.slice(1)
  }).join(' ')
}}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<!-- En una plantilla -->
{{ normalizedFullName }}
```

``` js
// La expresión compleja ha sido movida a una propiedad calculada
computed: {
  normalizedFullName: function () {
    return this.fullName.split(' ').map(function (word) {
      return word[0].toUpperCase() + word.slice(1)
    }).join(' ')
  }
}
```
{% raw %}</div>{% endraw %}



### Propiedades calculadas simples <sup data-p="b">Altamente recomendado</sup>

**Las propiedades calculadas complejas deben dividirse en tantas propiedades más simples como sea posible.**

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

Propiedades calculadas más simples y bien nombradas son:

- __Más fáciles de probar__

  Cuando cada propiedad calculada contiene sólo una expresión muy simple, con muy pocas dependencias, es mucho más fácil escribir pruebas que confirmen que funciona correctamente.

- __Más fáciles de leer__

  La simplificación de las propiedades calculadas le obliga a dar a cada valor un nombre descriptivo, incluso si no se reutiliza. Esto hace que sea mucho más fácil para otros desarrolladores (y para ti en el futuro) centrarse en el código que les importa y averiguar qué está pasando.

- __Más adaptables a las necesidades cambiantes__

  Cualquier valor que pueda ser nombrado puede ser útil para la vista. Por ejemplo, podríamos decidir mostrar un mensaje que le diga al usuario cuánto dinero ha ahorrado. También podríamos decidir calcular el impuesto sobre las ventas, pero quizás mostrarlo por separado, en lugar de como parte del precio final.

  Las propiedades calculadas pequeñas y enfocadas hacen menos suposiciones sobre cómo se utilizará la información, por lo que requieren menos refactorización a medida que cambian los requisitos.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` js
computed: {
  price: function () {
    var basePrice = this.manufactureCost / (1 - this.profitMargin)
    return (
      basePrice -
      basePrice * (this.discountPercent || 0)
    )
  }
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` js
computed: {
  basePrice: function () {
    return this.manufactureCost / (1 - this.profitMargin)
  },
  discount: function () {
    return this.basePrice * (this.discountPercent || 0)
  },
  finalPrice: function () {
    return this.basePrice - this.discount
  }
}
```
{% raw %}</div>{% endraw %}



### Valores de atributos <sup data-p="b">Altamente recomendado</sup>

**Los valores de los atributos HTML no vacíos deben estar siempre dentro de comillas (simple o doble, lo que no se utilice en JS).**

Mientras que los valores de atributo sin espacios no necesitan tener comillas en HTML, esta práctica a menudo lleva a _evitar_ espacios, haciendo que los valores de atributo sean menos legibles.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
<input type=text>
```

``` html
<AppSidebar :style={width:sidebarWidth+'px'}>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<input type="text">
```

``` html
<AppSidebar :style="{ width: sidebarWidth + 'px' }">
```
{% raw %}</div>{% endraw %}



### Directivas abreviadas <sup data-p="b">Altamente recomendado</sup>

**Las directivas abreviadas (`:` para `v-bind:` y `@` para `v-on:`) deben usarse siempre o nunca.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
<input
  v-bind:value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

``` html
<input
  v-on:input="onInput"
  @focus="onFocus"
>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<input
  :value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

``` html
<input
  v-bind:value="newTodoText"
  v-bind:placeholder="newTodoInstructions"
>
```

``` html
<input
  @input="onInput"
  @focus="onFocus"
>
```

``` html
<input
  v-on:input="onInput"
  v-on:focus="onFocus"
>
```
{% raw %}</div>{% endraw %}



## Reglas de prioridad C: Recomendado (Minimizar las opciones arbitrarias y los sobrecostes cognitivos)



###  Orden de las opciones de componente <sup data-p="c">recomendado</sup>

**Las opciones de componentes e instalaciones deben ser ordenadas consistentemente.**

Este es el orden predeterminado que recomendamos para las opciones de componentes. Están divididos en categorías, por lo que sabrás dónde añadir la nuevas propiedades de los plugins.

1. **Efectos secundarios** (desencadena efectos fuera del componente)
  - `el`

2. **Concienciación global** (requiere conocimientos más allá del componente)
  - `name`
  - `parent`

3. **Tipo de componente** (cambia el tipo del componente)
  - `functional`

4.  **Modificadores de plantillas** (cambia la forma en que se compilan las plantillas)
  - `delimiters`
  - `comments`

5. **Dependencias de la plantilla** (activos utilizados en la plantilla)
  - `components`
  - `directives`
  - `filters`

6. **Composición** (fusiona propiedades dentro de las opciones)
  - `extends`
  - `mixins`

7. **Interfaz** (la interfaz para el componente)
  - `inheritAttrs`
  - `model`
  - `props`/`propsData`

8. **Estado local** (propiedades reactivas locales)
  - `data`
  - `computed`

9. **Eventos** (llamadas de retorno activadas por eventos reactivos)
  - `watch`
  - Eventos del ciclo de vida (en el orden que son llamados)

10. **Propiedades no reactivas** (propiedades de instancia independientes del sistema de reactividad)
  - `methods`

11. **Renderizado** (la descripción declarativa de la salida del componente)
  - `template`/`render`
  - `renderError`



###  Orden de los atributos del elemento  <sup data-p="c">recomendado</sup>

**Los atributos de los elementos (incluidos los componentes) deben ordenarse de forma coherente.**

Este es el orden predeterminado que recomendamos para las opciones de componentes. Están divididos en categorías, por lo que sabrás dónde añadir atributos y directivas personalizadas.

1. **Definición** (proporciona las opciones del componente)
  - `is`

2. **List Rendering** (crea múltiples variaciones del mismo elemento)
  - `v-for`

3. **Condiciones** (si el elemento es renderizado o mostrado)
  - `v-if`
  - `v-else-if`
  - `v-else`
  - `v-show`
  - `v-cloak`

4. **Modificadores de render** (cambia la forma en que el elemento renderiza)
  - `v-pre`
  - `v-once`

5. **Concienciación global** (requiere conocimientos más allá del componente)
  - `id`

6. **Atributos únicos** (atributos que requieren valores únicos)
  - `ref`
  - `key`
  - `slot`

7. **Enlace en dos vías** (combinando enlace y eventos)
  - `v-model`

8. **Otros atributos** (todos los atributos no especificados , enlazados y no enlazados )

9. **Eventos** (listeners de eventos del componente)
  - `v-on`

10. **Contenido** (anula el contenido del elemento)
  - `v-html`
  - `v-text`



###  Líneas vacías en las opciones de componente/instancia  <sup data-p="c">recomendado</sup>

** Puede que desees añadir una línea vacía entre las propiedades multilínea, especialmente si las opciones ya no caben en la pantalla sin desplazarse.**

Cuando los componentes comienzan a sentirse apretados o difíciles de leer, añadir espacios entre las propiedades multilínea puede hacer que sean más fáciles de ojear de nuevo. En algunos editores, como Vim, las opciones de este formato también pueden facilitar la navegación con el teclado.

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` js
props: {
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
},

computed: {
  formattedValue: function () {
    // ...
  },

  inputClasses: function () {
    // ...
  }
}
```

``` js
// Ningún espacio está bien, siempre y cuando el componente
// siga siendo fácil de leer y navegar.
props: {
  value: {
    type: String,
    required: true
  },
  focused: {
    type: Boolean,
    default: false
  },
  label: String,
  icon: String
},
computed: {
  formattedValue: function () {
    // ...
  },
  inputClasses: function () {
    // ...
  }
}
```
{% raw %}</div>{% endraw %}



### Orden de elementos de nivel superior para componentes de un sólo archivo <sup data-p="c">recomendado</sup>

**Los [componentes de un sólo archivo](../guide/single-file-components.html) siempre deben ordenar las etiquetas `template`, `script`, y `style` consistentemente, con `<style>` al final, porque al menos uno de los otros dos es siempre necesario.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
<style>/* ... */</style>
<template>...</template>
<script>/* ... */</script>
```

``` html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<!-- ComponentA.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```

``` html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```
{% raw %}</div>{% endraw %}



## Reglas de prioridad D: Uso con precaución (Patrones Potencialmente Peligrosos)



### `v-if`/`v-if-else`/`v-else` sin `key` <sup data-p="d">uso con precaución</sup>

**Normalmente es mejor usar `key` con `v-if` + `v-else`, si son del mismo tipo de elemento (por ejemplo, ambos elementos son `<div>` ).**

Por defecto, Vue actualiza el DOM de la forma más eficiente posible. Esto significa que cuando se hacen cambios entre elementos del mismo tipo, simplemente parchea el elemento existente, en lugar de eliminarlo y añadir uno nuevo en su lugar. Esto puede tener [efectos secundarios no deseados](https://jsfiddle.net/chrisvfritz/bh8fLeds/) si estos elementos no se consideran realmente los mismos.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
<div v-if="error">
  Error: {{ error }}
</div>
<div v-else>
  {{ results }}
</div>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<div v-if="error" key="search-status">
  Error: {{ error }}
</div>
<div v-else key="search-results">
  {{ results }}
</div>
```

``` html
<p v-if="error">
  Error: {{ error }}
</p>
<div v-else>
  {{ results }}
</div>
```
{% raw %}</div>{% endraw %}



### Selectores de elementos con `scoped`  <sup data-p="d">uso con precaución</sup>

**Los selectores de elementos deben evitarse con `scoped`.**

Prefiere los selectores de clase sobre los selectores de elemento en estilos usando `scoped`, porque un gran número de selectores de elemento son lentos.

{% raw %}
<details>
<summary>
  <h4>Explicación Detallada</h4>
</summary>
{% endraw %}

Para ampliar los estilos, Vue añade un atributo único a los elementos del componente, como `data-v-f3f3f3eg9`. Luego se modifican los selectores para que sólo se seleccionen los elementos que coincidan con este atributo (por ejemplo, `button[data-v-f3f3eg9]`).

El problema es que un gran número de [selectores de atributos de elementos](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=a%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (por ejemplo, `button[data-v-f3f3eg9]`) serán considerablemente más lentos que los [selectores de atributos de clase](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=.class%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (por ejemplo,  `.btn-close[data-v-f3f3f3eg9]`), por lo que se deben preferir los selectores de clase cuando sea posible.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` html
<template>
  <button>X</button>
</template>

<style scoped>
button {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` html
<template>
  <button class="btn btn-close">X</button>
</template>

<style scoped>
.btn-close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}



### Comunicación implícita entre componentes padres-hijo <sup data-p="d">uso con precaución</sup>

** Los props y eventos deben ser preferidos para la comunicación entre componente padre-hijo, en lugar de `this.$parent` o props cambiantes.**

Una aplicación ideal de Vue es de props hacia abajo, eventos hacia arriba. Cumplir con esta convención hace que sus componentes sean mucho más fáciles de entender. Sin embargo, hay casos extremos en los que la mutación del prop o `this.$parent` puede simplificar dos componentes que ya están estrechamente acoplados.

El problema es que también hay muchos casos _simples_ en los que estos patrones pueden ofrecer conveniencia. Tenga cuidado: no se deje seducir por intercambiar simplicidad (ser capaz de entender el flujo de su estado) por conveniencia a corto plazo (escribir menos código).

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: '<input v-model="todo.text">'
})
```

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  methods: {
    removeTodo () {
      var vm = this
      vm.$parent.todos = vm.$parent.todos.filter(function (todo) {
        return todo.id !== vm.todo.id
      })
    }
  },
  template: `
    <span>
      {{ todo.text }}
      <button @click="removeTodo">
        X
      </button>
    </span>
  `
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: `
    <input
      :value="todo.text"
      @input="$emit('input', $event.target.value)"
    >
  `
})
```

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: `
    <span>
      {{ todo.text }}
      <button @click="$emit('delete')">
        X
      </button>
    </span>
  `
})
```
{% raw %}</div>{% endraw %}



### Gestión de estado sin flujo  <sup data-p="d">uso con precaución</sup>

**[Vuex](https://github.com/vuejs/vuex) debe ser preferido para la gestión global del estado, en lugar de `this.$root` o un bus de eventos globales.**

Administrar el estado en `this.$root` y/o usando un [bus de eventos globales](https://vuejs.org/v2/guide/migration.html#dispatch-and-broadcast-replaced) puede ser conveniente para casos muy simples, pero no es apropiado para la mayoría de las aplicaciones. Vuex ofrece no sólo un lugar central para administrar el estado, sino también herramientas para organizar, rastrear y depurar los cambios de estado.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal

``` js
// main.js
new Vue({
  data: {
    todos: []
  },
  created: function () {
    this.$on('remove-todo', this.removeTodo)
  },
  methods: {
    removeTodo: function (todo) {
      var todoIdToRemove = todo.id
      this.todos = this.todos.filter(function (todo) {
        return todo.id !== todoIdToRemove
      })
    }
  }
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bien

``` js
// store/modules/todos.js
export default {
  state: {
    list: []
  },
  mutations: {
    REMOVE_TODO (state, todoId) {
      state.list = state.list.filter(todo => todo.id !== todoId)
    }
  },
  actions: {
    removeTodo ({ commit, state }, todo) {
      commit('REMOVE_TODO', todo.id)
    }
  }
}
```

``` html
<!-- TodoItem.vue -->
<template>
  <span>
    {{ todo.text }}
    <button @click="removeTodo(todo)">
      X
    </button>
  </span>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  methods: mapActions(['removeTodo'])
}
</script>
```
{% raw %}</div>{% endraw %}



{% raw %}
<script>
(function () {
  var enforcementTypes = {
    none: '<span title="There is unfortunately no way to automatically enforce this rule.">self-discipline</span>',
    runtime: 'runtime error',
    linter: '<a href="https://github.com/vuejs/eslint-plugin-vue#eslint-plugin-vue" target="_blank">plugin:vue/recommended</a>'
  }
  Vue.component('sg-enforcement', {
    template: '\
      <span>\
        <strong>Enforcement</strong>:\
        <span class="style-rule-tag" v-html="humanType"/>\
      </span>\
    ',
    props: {
      type: {
        type: String,
        required: true,
        validate: function (value) {
          Object.keys(enforcementTypes).indexOf(value) !== -1
        }
      }
    },
    computed: {
      humanType: function () {
        return enforcementTypes[this.type]
      }
    }
  })

  // new Vue({
  //  el: '#main'
  // })
})()
</script>
{% endraw %}
