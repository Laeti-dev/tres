# WIP
# Guide de migration

Ce guide à pour objectif de vous aider à faire la migration de la version 1 aux versions les plus récentes de TresJS 🤩✨.

::: code-group

```bash [pnpm]
pnpm update @tresjs/core
```

```bash [npm]
npm update @tresjs/core
```

```bash [yarn]
yarn upgrade @tresjs/core
```

:::

## Nouveautés

### Vue Custom Renderer

**TresJS** est pour le moment une [Vue Custom Renderer](https://vuejs.org/api/custom-renderer.html#createrenderer) 🎉 que l'on retrouve dans un composant englobant `TresCanvas` qui se charge de créer pour vous le `WebGLRenderer` et la `Scene`, et de créer une **nouvelle instance de l'application Vue** pour retourner la scène.

### Soporte de TypeScript e Intellisense 🦾

![TresJS Intellisense](/v2-intellisense.gif)

Il s'agit probablement de la caractéristique **la plus attendue de TresJS**. Désormais, les composants Tres fonctionnent avec Volar et fournissent un type intellisense.

<!-- WIP
**TresJS** now generates type declaration on build time for all the components based of the catalog from ThreeJS. This means that you can use all the components from ThreeJS and get type intellisense for them.


**TresJS** ahora genera declaraciones de tipos en tiempo de compilación para todos los componentes basados en el catálogo de ThreeJS. Esto significa que puedes usar todos los componentes de ThreeJS y obtener intellisense de tipos para ellos.

### El plugin de Tres es opcional 👍

El `TresPlugin` ahora es opcional. Puedes usar TresJS sin él importando los componentes directamente desde `tresjs/core`:

```vue
<script setup lang="ts">
import { TresCanvas } from '@tresjs/core'
</script>

<template>
  <TresCanvas>
    <TresPerspectiveCamera
      :position="cameraPosition"
      :fov="cameraFov"
      :aspect="cameraAspect"
      :near="cameraNear"
      :far="cameraFar"
    />
    <TresMesh
      :geometry="geometry"
      :material="material"
    />
  </TresCanvas>
</template>
```

::: info
Esto es recomendado por razones de rendimiento y tamaño del paquete, el tree-shaking funcionará mejor y solo importarás los componentes que uses.
:::

### TresScene ya no es necesario

The `<TresScene />` component is now deprecated since the scene is now created by the `<TresCanvas />`.

In the beginning, I thought that it would be a good idea to have a separate component for the scene in terms of verbosity and keep it as similar to plain ThreeJS, but it turned out that it was not really useful.

You can now create a scene like this:

```vue
<template>
  <TresCanvas>
    <TresPerspectiveCamera
      :position="cameraPosition"
      :fov="cameraFov"
      :aspect="cameraAspect"
      :near="cameraNear"
      :far="cameraFar"
    />
    <TresMesh
      :geometry="geometry"
      :material="material"
    />
  </TresCanvas>
</template>
```

Para migrar tu código, simplemente puedes eliminar el componente `<TresScene />` y mover los hijos al componente `<TresCanvas />`.

### `useCatalog` ahora está obsoleto

La función `useCatalog` ahora está obsoleta. Ahora puedes importar el catálogo directamente desde `@tresjs/core`.

Puedes leer más al respecto aquí: [Extending](/advanced/extending.md)

Cambia esto:

```ts {2,5,7}
import { useCatalog } from '@tresjs/core'
import { TextGeometry } from 'three/addons/geometries/TextGeometry'

const { extend } = useCatalog()

extend({ TextGeometry })
```

Por esto:

```ts {2,6}
// Correcto ✅
import { extend } from '@tresjs/core'
import { TextGeometry } from 'three/addons/geometries/TextGeometry'

extend({ TextGeometry })
```

### El valor de referencia del modelo `getModel` ahora está obsoleto

La función `getModel` ahora está obsoleta. Ahora puedes usar directamente la propiedad `model`.

Cambia esto:

```vue {7,9-12}
// Incorrecto ❌
<script setup lang="ts">
import { useGLTF } from '@tresjs/cientos'

const { scene, nodes, animations, materials } = await useGLTF('/models/AkuAku.gltf', { draco: true })

const modelRef = ref()

watch(modelRef, ({ getModel }) => {
  const model = getModel()
  model.position.set(0, 0, 0)
})
</script>

<template>
  <primitive :object="nodes.MyModel" />
</template>
```

To this:

```vue {7,9-12}
// Correcto ✅
<script setup lang="ts">
import { useGLTF } from '@tresjs/cientos'

const { scene, nodes, animations, materials } = await useGLTF('/models/AkuAku.gltf', { draco: true })

const modelRef = ref()

watch(modelRef, (model) => {
  // Do something with the model
  model.position.set(0, 0, 0)
})
</script>

<template>
  <primitive :object="nodes.MyModel" />
</template>
```

### Las cámaras deben estar antes de cualquier control 🎥

El componente `TresOrbitControls` debe estar después de la cámara en el árbol. Esto se debe a que los controles necesitan conocer la cámara para funcionar.

Cambia esto:

```vue {3,5}
// Incorrecto ❌
<template>
  <TresCanvas>
    <TresOrbitControls />
    <TresPerspectiveCamera />
  </TresCanvas>
</template>
```

Por esto:

```vue {3,5}
// Correcto ✅
<template>
  <TresCanvas>
    <TresPerspectiveCamera />
    <TresOrbitControls />
  </TresCanvas>
</template>
```

## UseTres ahora es useTresContext <Badge type="warning" text="^3.0.0" />

Para la versión 3, reestructuramos toda la lógica de estado para que sea más flexible y fácil de usar para los autores de complementos y paquetes del ecosistema. En lugar de usar una tienda como en la versión 2, ahora usamos un proveedor de contexto basado en `provide/inject`.

La función `useTres` ahora es un alias de la función `useTresContext` para evitar romper demos y experimentos existentes, pero considera usar `useTresContext` a partir de ahora.

En lugar de obtener un objeto reactivo grande, ahora obtendrás directamente las referencias `scene` y `renderer`, entre otras propiedades.

Cambia esto:

```ts {2}
// Incorrecto ❌
import { useTres } from '@tresjs/core'

const { state, setState } = useTres()

console.log(state.scene)
```

Por esto:

```ts {2}
// Correcto ✅
import { useTresContext } from '@tresjs/core'

const { scene, renderer } = useTresContext()

console.log(scene.value)
```

Para obtener información más detallada sobre el nuevo sistema de proveedor de contexto, puedes leer la sección [API DOCS](/api/composables.md). -->
