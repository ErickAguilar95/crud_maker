---

name: wordpress-ui-consistency
description: Usar al crear, modificar, revisar o refactorizar cualquier interfaz de WordPress, incluyendo páginas de administración de plugins, pantallas de ajustes, dashboards, bloques de Gutenberg, interfaces del editor, experiencias del Editor del Sitio, tablas de datos, formularios, modales, barras laterales, avisos y aplicaciones relacionadas con WordPress. Priorizar siempre los componentes, patrones, tokens y convenciones de interacción del sistema de diseño de WordPress antes que una interfaz personalizada.
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Consistencia visual de WordPress

## Objetivo

Hacer que cada interfaz se sienta nativa de WordPress.

La interfaz debe integrarse visual y funcionalmente con WordPress, en lugar de parecer una aplicación SaaS independiente incrustada dentro de WordPress.

Cuando exista un componente o patrón exacto de WordPress, utilizarlo.

Cuando no exista un componente exacto, construir la interfaz combinando componentes existentes de WordPress y sus tokens de diseño.

Crear componentes visuales personalizados solo como último recurso.

## Regla principal

Utilizar este orden de prioridad para cada decisión de interfaz:

1. Componente o patrón existente de WordPress.
2. Composición de componentes existentes de WordPress.
3. Tokens y patrones de interacción del WordPress Design System.
4. Convenciones nativas existentes de `wp-admin` cuando se trabaje con interfaces administrativas PHP tradicionales.
5. Componente personalizado diseñado para coincidir visual y funcionalmente con WordPress.

Nunca inventar un nuevo lenguaje visual cuando WordPress ya proporcione una solución equivalente.

## Determinar primero el contexto de WordPress

Antes de implementar una interfaz, determinar dónde se utilizará:

* Administración de WordPress.
* Editor de bloques Gutenberg.
* Editor del Sitio.
* Pantalla de ajustes de un plugin.
* Aplicación administrativa personalizada de un plugin.
* Inspector de bloque.
* Barra de herramientas de bloque.
* Bloque en el frontend.
* Tema de bloques.
* Aplicación independiente cuyo objetivo sea parecerse visualmente a WordPress.

Utilizar las convenciones correspondientes a ese contexto.

No aplicar estilos del frontend a interfaces administrativas ni utilizar tokens visuales de administración en estilos públicos de bloques sin una razón clara.

## Selección de componentes

Priorizar paquetes oficiales de WordPress.

Para estructuras de alto nivel dentro de la administración de WordPress, investigar primero:

* `@wordpress/admin-ui`

Para controles y componentes de interfaz, investigar los componentes actualmente recomendados de:

* `@wordpress/ui`
* `@wordpress/components`

No asumir que un paquete siempre tiene prioridad sobre el otro. Consultar la recomendación actual del WordPress Design System para el componente que se desea implementar.

Para interfaces basadas en conjuntos de datos, administración de recursos, tablas, listas, grids, filtrado, búsqueda y edición de entidades estructuradas, investigar:

* `@wordpress/dataviews`

Para iconografía, priorizar:

* `@wordpress/icons`

No introducir iconos de Font Awesome, Lucide, Material Icons, Heroicons u otra librería cuando exista un icono adecuado de WordPress.

## Tokens de diseño

Para estilos de interfaz relacionados con el WordPress Design System, utilizar tokens semánticos actuales de WordPress, como los tokens `--wpds-*` cuando correspondan.

Para estilos de bloques y valores procedentes de `theme.json`, utilizar las variables correspondientes:

* `--wp--preset--*`

No mantener dentro de esta skill una copia fija de los valores de los tokens de WordPress.

No adivinar valores de tokens.

No crear una paleta de colores independiente, escala de espaciado, escala tipográfica, sistema de radios de borde o sistema de sombras cuando WordPress ya proporcione uno.

Si existe alguna duda sobre los nombres actuales de tokens o las APIs de componentes, consultar la documentación actual del WordPress Design System antes de implementar.

## Consistencia visual

Las interfaces deben seguir las convenciones visuales de WordPress para:

* Tipografía.
* Tamaños de fuente.
* Pesos tipográficos.
* Alturas de línea.
* Espaciado.
* Bordes.
* Radios de borde.
* Sombras.
* Fondos.
* Colores de texto.
* Texto secundario.
* Estados deshabilitados.
* Estados hover.
* Estados focus.
* Estados seleccionados.
* Estados destructivos.
* Iconos.
* Botones.
* Campos de entrada.
* Selectores.
* Checkboxes.
* Radio buttons.
* Toggles.
* Pestañas.
* Menús.
* Popovers.
* Dropdowns.
* Modales.
* Avisos.
* Barras de herramientas.
* Barras laterales.
* Paneles.
* Tarjetas.
* Tablas.
* Listas.
* Estados vacíos.
* Estados de carga.

Evitar estilos decorativos arbitrarios.

No añadir tarjetas excesivamente redondeadas, gradientes, sombras exageradas, paneles flotantes o tratamientos visuales propios de aplicaciones SaaS modernas salvo que la interfaz existente de WordPress que se está ampliando ya utilice ese patrón.

## No importar visualmente otro sistema de diseño

No hacer que las interfaces de WordPress parezcan provenientes de:

* Material UI.
* Bootstrap.
* Ant Design.
* Chakra.
* shadcn/ui.
* Dashboards genéricos creados con Tailwind.
* Dashboards inspirados en Stripe.
* Interfaces inspiradas en Linear.
* Interfaces inspiradas en Vercel.

Una librería de utilidades CSS puede utilizarse internamente cuando sea técnicamente conveniente, pero nunca debe convertirse en la fuente principal del lenguaje visual.

WordPress debe seguir siendo la fuente de verdad visual.

## Páginas de administración

Al crear una página personalizada dentro de la administración de WordPress, mantener la jerarquía visual y el comportamiento que los usuarios ya conocen de WordPress.

Priorizar estructuras existentes de WordPress para:

* Título de página.
* Acciones principales.
* Navegación.
* Breadcrumbs.
* Búsqueda.
* Filtros.
* Acciones primarias.
* Acciones secundarias.
* Avisos.
* Áreas de contenido.
* Barras laterales.
* Colecciones de datos.
* Paginación.

No construir una aplicación completamente independiente dentro de `wp-admin` salvo que exista una razón explícita para hacerlo.

## Jerarquía de interacción en Gutenberg

Al crear bloques o experiencias dentro del editor Gutenberg, seguir la jerarquía de interacción propia de Gutenberg.

Priorizar la manipulación directa dentro del canvas para las interacciones principales.

Utilizar la barra de herramientas del bloque para acciones contextuales importantes que no puedan realizarse razonablemente dentro del contenido.

Utilizar la barra lateral de Ajustes / Inspector para configuraciones avanzadas, secundarias o terciarias.

No ocultar funcionalidades necesarias para el uso básico exclusivamente dentro del Inspector.

Utilizar valores predeterminados razonables para evitar que el usuario tenga que configurar cada opción manualmente.

## Interfaces con muchos datos

Antes de crear una tabla personalizada, una lista de recursos, un grid, un sistema de filtros o acciones masivas, comprobar si WordPress DataViews puede proporcionar el comportamiento necesario.

Priorizar patrones nativos de WordPress para:

* Búsqueda.
* Filtrado.
* Ordenamiento.
* Paginación.
* Cambio de vista.
* Acciones por fila o elemento.
* Acciones masivas.
* Edición de campos.
* Estados vacíos.

Evitar recrear manualmente estos patrones sin una razón técnica clara.

## Botones y acciones

Utilizar los componentes de botones de WordPress y respetar su jerarquía de acciones.

Distinguir claramente entre:

* Acción primaria.
* Acción secundaria.
* Acción terciaria.
* Acción destructiva.

No utilizar colores destructivos para acciones normales.

No crear formas personalizadas de botones ni estados de interacción personalizados cuando exista un componente de WordPress adecuado.

Utilizar botones únicamente con icono solo cuando la acción sea comprensible y accesible.

Proporcionar tooltips o etiquetas accesibles cuando sea necesario.

## Formularios

Utilizar componentes de formulario de WordPress siempre que sea posible.

Mantener las etiquetas, textos de ayuda, validaciones, espaciados y mensajes de error consistentes con WordPress.

No recrear manualmente inputs, selects, checkboxes, toggles o grupos de radio buttons si existe un componente apropiado de WordPress.

Evitar campos exageradamente grandes o patrones de etiquetas flotantes salvo que exista una necesidad específica.

## Iconos

Utilizar `@wordpress/icons` cuando exista un icono adecuado.

Mantener el tamaño, alineación y comportamiento de color de los iconos consistente con la interfaz de WordPress que los rodea.

No mezclar diferentes estilos de iconografía dentro de una misma interfaz.

Los SVG personalizados solo son aceptables cuando el concepto requerido no esté representado dentro de la librería de iconos de WordPress.

## Componentes personalizados

Solo se permite crear un componente personalizado cuando ningún componente o composición de WordPress pueda satisfacer adecuadamente el requisito.

Antes de crear uno:

1. Buscar si existe un componente de WordPress.
2. Buscar si existe un patrón equivalente de WordPress.
3. Determinar si puede construirse combinando componentes existentes.
4. Determinar qué tokens de diseño de WordPress corresponden.
5. Reproducir el comportamiento de interacción de WordPress.
6. Verificar el comportamiento mediante teclado y lectores de pantalla.

Un componente personalizado debe parecer algo que el propio WordPress podría incluir oficialmente.

## Estilos existentes del proyecto

Al modificar un proyecto de WordPress existente, inspeccionar primero la interfaz que lo rodea antes de tomar decisiones visuales.

Priorizar la consistencia con la interfaz nativa de WordPress existente antes que introducir un tratamiento visual diferente.

No restilizar innecesariamente componentes existentes de WordPress.

Evitar sobrescribir CSS interno de componentes de WordPress salvo que no exista una alternativa soportada.

## Accesibilidad

La accesibilidad es obligatoria.

Preservar:

* Navegación mediante teclado.
* Estados de foco visibles.
* HTML semántico.
* Nombres accesibles.
* Etiquetas.
* Relaciones ARIA cuando sean necesarias.
* Contraste suficiente.
* Navegación mediante lectores de pantalla.
* Gestión lógica del foco.
* Landmarks apropiados.

No depender exclusivamente del color, hover o drag-and-drop para comunicar funcionalidad.

## Comportamiento responsive

Las interfaces deben seguir siendo utilizables en tamaños de viewport estrechos compatibles con WordPress.

Considerar cómo se comportan:

* Barras de herramientas.
* Barras laterales.
* Tablas.
* Acciones.
* Etiquetas.
* Controles.
* Modales.

No diseñar exclusivamente para una pantalla administrativa de escritorio amplia.

## RTL y localización

No asumir que la interfaz siempre utilizará una dirección de izquierda a derecha.

Evitar CSS direccional cuando puedan utilizarse propiedades lógicas.

Permitir que los textos traducidos puedan crecer considerablemente sin romper la interfaz.

No fijar anchos basándose únicamente en la longitud del texto original.

## Antes de escribir CSS

Preguntarse:

1. ¿WordPress ya aplica estilos para esto?
2. ¿Existe un componente de WordPress para esto?
3. ¿Existe un token de diseño de WordPress para esto?
4. ¿Puede construirse combinando componentes existentes?
5. ¿Estoy introduciendo accidentalmente otro lenguaje visual?

Si alguna respuesta indica que ya existe una solución de WordPress, utilizarla.

## Checklist de revisión

Antes de considerar terminada una interfaz, verificar:

* Se siente nativa dentro de WordPress.
* Se priorizaron componentes oficiales sobre componentes personalizados.
* Se priorizaron iconos de WordPress.
* Se utilizaron tokens actuales del WordPress Design System cuando correspondía.
* Se evitaron colores y espaciados arbitrarios.
* La jerarquía de botones coincide con WordPress.
* Los controles de formulario coinciden con WordPress.
* Los estados de carga, vacío, error y éxito coinciden con WordPress.
* La navegación mediante teclado funciona correctamente.
* Los estados de foco son visibles.
* El comportamiento responsive es adecuado.
* No se rompió la compatibilidad RTL.
* Las traducciones pueden crecer sin romper la interfaz.
* La interfaz no parece un dashboard SaaS ajeno a WordPress.

## Regla final

Cuando haya que elegir entre una solución personalizada visualmente llamativa y una solución más sencilla que se sienta nativa de WordPress, elegir la solución nativa de WordPress.

La consistencia con WordPress es más importante que la novedad.
