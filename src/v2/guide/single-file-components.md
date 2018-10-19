---
title: Componentes de archivo único
type: guide
order: 402
---

## Introducción

En muchos proyectos Vue, componentes globales serán definidos usando `Vue.component` seguido de `new Vue({ el: '#container'})` para señalar un contenedor en el body de todas las páginas.

Esto puede funcionar bien en proyectos pequeños-medianos donde JavaScript es sólo usado para mejorar ciertas vistas. En proyectos más complejos ó cuando su frontend sea manejado en completo por JavaScript las siguientes desventajas resultarán aparentes:

- **Definiciones globales** fuerzan nombres únicos para cada componente
- **Plantillas string** carecen de resaltado de sintaxis y requieren de barras para HTML multilínea
- **Sin soporte CSS** significa que mientras HTML y JavaScript son modularizados en componentes, CSS means that while HTML and JavaScript are modularized into components, CSS no lo será
- **No haz build** nos restringe a HTML y ES5 JavaScript, en vez de preprocesadores como Pug (antes Jade) ó Babel

Estos puntos son resueltos por **componentes de archivo único** con extensión `.vue`que son posibles gracias a herramientas de build como Webpack ó Browserify.

Aqui un ejemplo de un archivo que llamaremos `Hello.vue`:

<img src="/images/vue-component.png" style="display: block; margin: 30px auto;">

Ahora tenemos:

- [Resaltado de sintaxis completo](https://github.com/vuejs/awesome-vue#source-code-editing)
- [Módulos CommonJS](https://webpack.js.org/concepts/modules/#what-is-a-webpack-module)
- [CSS ámbito de componente](https://vue-loader.vuejs.org/en/features/scoped-css.html)

Como prometido, también podemos usar preprocesadores como Pug, Babel (con módulos ES2015) y Stylus para limpieza y componentes con más características y capacidades.

<img src="/images/vue-component-with-preprocessors.png" style="display: block; margin: 30px auto;">

Estos lenguajes específicos son sólo ejemplos. Podría también facilmente usar Bublé, TypeScript, SCSS, PostCSS ó caulquier otro que le ayude a ser productivo. Si usa Webpack con `vue-loader`, tiene también soporte de primera clase para módulos CSS.

### Que pasa con la separación de responsabilidades?

Algo importante de entender es que **separación de responsabilidad no es igual a separación de tipos de archivo.** En desarollo UI moderno hemos visto que en vez de dividar el código en tres capas enormes que interaccionan entre ellas, tiene mucho mas sentido dividirlo en componentes independientes y componerlos. Dentro de un componente, su plantilla, lógica y estilos estan conectados hereditariamente y combinarlos hace al componente más mantenible y cohesivo.

Aún si no le gusta la idea de componentes de archivo único, puede aprovechar sus capacidades de hot-reloading y precompilación separando su JavaScript y su CSS en diferentes archivos:

``` html
<!-- my-component.vue -->
<template>
  <div>This will be pre-compiled</div>
</template>
<script src="./my-component.js"></script>
<style src="./my-component.css"></style>
```

## Cómo empezar

### Ejemplo Sandbox

Si quiere ir directo al grano y empezar a jugar con componentes de archivo único visite [esta simple to do app](https://codesandbox.io/s/o29j95wx9) en CodeSandbox.

### Para usuarios nuevos a sistemas build de módulos en JavaScript

Con componentes `.vue` estamos entrando en el reino de las aplicaciones avanzadas JavaScript. Esto significa aprender a utilizar algunas herramientas adicionales si no lo ha hecho todavía:

- **Node Package Manager (NPM)**: Read the [Guia Introducción a NPM](https://docs.npmjs.com/getting-started/what-is-npm) la sección  _10: Uninstalling global packages_.

- **JavaScript moderno con ES2015/16**: Lea la [Guía de aprendizaje ES2015](https://babeljs.io/docs/learn-es2015/) de Babel. No necesita memorizar cada función ahora pero guarde la página como referencia a la que consultar.

Cuando se haya tomado un día para investigar estos recursos, recomendamos que visite la plantilla [webpack](https://vuejs-templates.github.io/webpack). Siga las instrucciones y debería tener un proyecto Vue con componentes `.vue`, ES2015 y hot-reloading en nada de tiempo!

Para aprender más sobre Webpack visite sus [docs oficiales](https://webpack.js.org/configuration/) y [Webpack Academy](https://webpack.academy/p/the-core-concepts). En Webpack, cada archivo puede ser transformado por un "loader" antes de ser incluido en el paquete y Vue ofrece el plugin [vue-loader](https://vue-loader.vuejs.org) para traducir componentes de archivo único (`.vue`).

### Para usuarios avanzados

Da igual si prefiere Webpack ó Browserify, tenemos plantillas documentadas para proyectos simples y más complejos. Recomendamos visitar [github.com/vuejs-templates](https://github.com/vuejs-templates), selecciona una plantilla que se adecue a tus necesidades y siga las instrucciones en el README para generar un nuevo proyecto con [vue-cli](https://github.com/vuejs/vue-cli).
