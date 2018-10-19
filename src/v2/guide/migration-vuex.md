---
title: Migración de Vuex 0.6.x a 1.0
type: guide
order: 703
---

> Vuex 2.0 ha sido publicado, pero esta guía sólo cubre la migración a 1.0? Es un error? Además, parece que Vuex 1.0 y 2.0 fueron publicados simultáneamente. Que pasa aquí? Cuál debería usar y que es compatible con Vuex 2.0?

Ambos Vuex 1.0 y 2.0:

- soportan completamente Vue 1.0 y Vue 2.0
- serán mantenidos en un futuro próximo

De todas formas, tienen diferentes usuarios como objetivo.

__Vuex 2.0__ es un rediseño radical y simplificado de la API, para aquellos que comiencen nuevos proyectos o quieran estar a la última en lo que a administración de estado del cliente se refiere. __No esta cubierto en esta guía de migración__, así que debería visitar la [documentación Vuex 2.0 docs](https://vuex.vuejs.org/en/index.html) if you'd like to learn more about it.

__Vuex 1.0__ es mayormente retrocompatible, asi que requiere de pocos cambios para actualizarlo. Es recomendado para aquellos con bases de código muy grandes o que quieran tomar el camino más sencillo para actualizar a Vue 2.0. Esta guía está dedicada a facilitar el proceso pero solo incluye notas de migración. Para la guía complete visite la [documentación Vue 1.0](https://github.com/vuejs/vuex/tree/1.0/docs/en).

## `store.watch` with String Property Path <sup>replaced</sup>

`store.watch` ahora acepta sólo funciones. Por ej., ahora tendría que cambiar:

``` js
store.watch('user.notifications', callback)
```

with:

``` js
store.watch(
  //Cuando el resultado retornado cambie
  function (state) {
    return state.user.notifications
  },
  //Corre este callback
  callback
)
```

Esto le ofrece mayor control sobre las propiedes reactivas que quiera observar.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>store.watch</code> with a string as the first argument.</p>
</div>
{% endraw %}

## Emisor de eventos de la Store <sup>eliminado</sup>

La instancia Store no expone ahora la interfaz de emisión de eventos (`on`, `off`, `emit`). Si usaba previamente la store como un bus de eventos global, [visite esta sección](migration.html#dispatch-and-broadcast-removed) para instrucciones de migración.

En vez de usar esta interfaz para observar eventos emitidos por la store (por ej. `store.on('mutation', callback)`), un nuevo método `store.subscribe` es introducido. Normalmente su uso dentro de un plugin será :

``` js
var myPlugin = store => {
  store.subscribe(function (mutation, state) {
    // Haz algo...
  })
}

```

Ver el ejemplo [docs Plugin](https://github.com/vuejs/vuex/blob/1.0/docs/en/plugins.md) para más información.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>store.on</code>, <code>store.off</code>, and <code>store.emit</code>.</p>
</div>
{% endraw %}

## Middlewares <sup>reemplazado</sup>

Middlewares fueron reemplazados por plugins. Un plugin es una función que recibe el store como único argumento y puede "eschuchar" el evento __mutation__ de la store:

``` js
const myPlugins = store => {
  store.subscribe('mutation', (mutation, state) => {
    // Do something...
  })
}
```

Para más detalles, visite [docs Plugin](https://github.com/vuejs/vuex/blob/1.0/docs/en/plugins.md).

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the <code>middlewares</code> option on a store.</p>
</div>
{% endraw %}
