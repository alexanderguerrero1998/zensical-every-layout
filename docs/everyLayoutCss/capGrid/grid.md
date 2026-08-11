# The Grid

## El problema

Los diseñadores a veces hablan de diseñar *con una cuadrícula*. Colocan la grilla — una matriz de líneas horizontales y verticales — primero, luego pueblan ese espacio, haciendo que las palabras y las imágenes abarquen las cajas que esas líneas que se intersectan crean.

![](areacontent.png)

Un enfoque 'primero la grilla' para el layout solo es realmente sostenible cuando dos cosas se conocen de antemano:

1. El espacio
2. El contenido

Para un layout de revista destinado a papel, como el descrito en *Axioms*, estas cosas son alcanzables. Para un layout web independiente del dispositivo y de la pantalla que contiene contenido dinámico (léase: cambiable), fundamentalmente no lo son.

El módulo CSS Grid es radical porque te permite colocar contenido en cualquier lugar dentro de una grilla predefinida, y como tal trae el *diseño con una cuadrícula* a la web. Pero cuanto más particular y deliberada es la colocación del contenido en la grilla, más ajustes manuales, en forma de breakpoints `@media`, se necesitan para adaptar el layout a diferentes espacios. Ya sea la definición de la grilla en sí, la posición del contenido dentro de ella, o ambas, tendrán que ser cambiadas a mano, y con código adicional.

Como cubrí en *The Switcher*, los breakpoints `@media` pertenecen solo a las dimensiones del viewport, y no al espacio disponible inmediato ofrecido por un contenedor padre. Eso significa que los componentes de layout definidos usando breakpoints `@media` son fundamentalmente no independientes del contexto: un gran problema para un sistema de diseño modular.

No es, ni siquiera teóricamente, posible diseñar *con una cuadrícula* de una manera independiente del contexto y automáticamente responsiva. Sin embargo, es posible crear formaciones básicas tipo grilla: conjuntos de elementos divididos tanto en columnas como en filas.

![](gridcontent.png)

> En *Every Layout*, diseñamos con contenido. Sin contenido, una grilla no necesita existir; con contenido, una formación de grilla puede emerger de él.

El compromiso es inevitable, así que es una cuestión de encontrar la solución más arquetípica y a la vez eficiente.

??? info "Explicacion"

    Este texto plantea un problema central del diseño con grillas (grids) aplicado a la web, y lo resuelve con una distinción importante. Te lo desgloso:
    
    __El problema de fondo__
    
    Cuando un diseñador "diseña con una cuadrícula" en el sentido clásico (piensa en una revista impresa), sigue este orden:
    
    1. Primero define la grilla (las líneas que dividen el espacio en celdas)
    2. Después coloca el contenido dentro de esas celdas
    
    Este enfoque funciona en papel porque **el diseñador conoce dos cosas fijas de antemano**: el tamaño exacto de la página y el contenido exacto que va a poner. Puede decidir "este titular va en estas tres columnas, esta imagen ocupa estas dos filas", porque nada de eso va a cambiar.
    
    __Por qué esto se rompe en la web__
    
    En la web, ninguna de esas dos certezas existe:
    
    - **El espacio es variable**: el mismo layout se ve en un móvil, una tablet, un monitor ultrawide, una ventana redimensionada.
    - **El contenido es dinámico**: un título puede tener 3 palabras o 15, una lista puede tener 2 ítems o 20, según lo que cargue una base de datos o escriba un usuario.
    
    CSS Grid te da el poder técnico de colocar cualquier elemento en cualquier celda de una grilla predefinida (algo que antes no existía en CSS). Pero ese poder es una trampa si lo usas para replicar el enfoque "primero la grilla": cuanto más precisa es la posición que le asignas a cada elemento (ej. "este div va en la columna 2, fila 3"), más vas a necesitar breakpoints `@media` para reajustar esas posiciones cuando cambie el espacio disponible.
    
    Y ahí está el segundo problema, que conecta con otro artículo suyo (*The Switcher*): los `@media` solo reaccionan al tamaño del **viewport** (la ventana del navegador), no al espacio real que tiene disponible un componente dentro de su contenedor padre. Entonces un componente diseñado así deja de ser reutilizable de forma predecible — no es "context-independent" — porque su comportamiento depende de dónde esté anclado en la ventana completa, no de cuánto espacio realmente tiene alrededor. Eso es un problema serio si tu objetivo es construir un sistema de diseño modular, donde un mismo componente debería comportarse bien sin importar dónde lo insertes.
    
    __La solución que proponen__
    
    La conclusión no es "abandona las grillas", sino un cambio de dirección:
    
    - ❌ No es posible diseñar *con* una cuadrícula predefinida de forma responsiva y libre de contexto de manera automática.
    - ✅ Sí es posible dejar que **formaciones tipo grilla emerjan del contenido mismo** — es decir, en vez de definir filas y columnas fijas y forzar el contenido a encajar, defines reglas de comportamiento (como tamaños mínimos de columna) y dejas que el navegador calcule cuántas columnas y filas caben según el espacio y el contenido disponibles en cada momento.
    
    La frase clave resume la filosofía de todo el proyecto *Every Layout*: **"diseñamos con contenido"**. La grilla no es el punto de partida fijo; es un resultado que surge naturalmente cuando hay suficiente contenido para necesitarla. Sin contenido no hace falta que la grilla "exista" como estructura predefinida.
    
    Es, en esencia, el argumento a favor de patrones como `grid-template-columns: repeat(auto-fit, minmax(...))` en vez de definir un `grid-template-columns: 1fr 1fr 1fr` fijo con breakpoints manuales para cada tamaño de pantalla.

## Flexbox para grillas

Usando Flexbox, puedo crear una formación de grilla usando `flex-basis` para determinar un ancho *ideal* para cada una de las celdas de la grilla:

```css linenums="1"
.flex-grid {
  display: flex;
  flex-wrap: wrap;
}
.flex-grid > * {
  flex: 1 1 30ch;
}
```

La declaración `display: flex` define el contexto Flexbox, `flex-wrap: wrap` permite el wrapping, y `flex: 1 1 30ch` dice: "el ancho ideal debería ser `30ch`, pero se debe permitir que los elementos crezcan y se contraigan según el espacio disponible". Importantemente, el número de columnas no se prescribe basándose en un esquema de grilla fijo; se determina *algorithmicamente* basándose en el `flex-basis` y el espacio disponible. El contenido y el contexto definen la grilla, no un árbitro humano.

En *The Switcher*, identificamos una interacción entre el wrapping y el crecimiento que lleva a que los elementos 'rompan' la forma de la grilla bajo ciertas circunstancias:

![](littleboxes.png)

Por un lado, el layout ocupa todo el espacio horizontal de su contenedor, y no hay huecos antiestéticos. Por otro lado, una formación de grilla genérica probablemente debería hacer que cada uno de sus elementos se alinee tanto a las reglas horizontales como verticales.

??? info "Explicacion"

    __El intento con Flexbox__
    
    Esta sección muestra un primer intento de crear formaciones tipo grilla sin usar CSS Grid, sino con Flexbox — y expone por qué, aunque casi funciona, se queda corto.
    
    __Cómo funciona el código__
    
    ```css
    .flex-grid {
      display: flex;
      flex-wrap: wrap;
    }
    .flex-grid > * {
      flex: 1 1 30ch;
    }
    ```
    
    - `display: flex` activa Flexbox en el contenedor.
    - `flex-wrap: wrap` permite que los elementos hijos pasen a una nueva línea cuando ya no caben en la fila actual, en vez de aplastarse todos en una sola línea.
    - `flex: 1 1 30ch` es la parte clave, y es shorthand de `flex-grow: 1; flex-shrink: 1; flex-basis: 30ch;`. Se traduce como: "el ancho ideal de este elemento es `30ch` (aprox. 30 caracteres de ancho), pero si sobra espacio en la fila, puede crecer para llenarlo (`grow: 1`), y si falta espacio, puede encogerse (`shrink: 1`)".
    
    __Por qué esto es importante conceptualmente__
    
    Aquí está la conexión con el artículo anterior: **nadie decidió cuántas columnas tiene esta grilla**. No hay un `grid-template-columns: repeat(3, 1fr)` que fije el número de columnas de antemano. En cambio, el navegador calcula cuántos elementos de ~30ch cada uno caben en el ancho disponible del contenedor, y ese cálculo determina el número de columnas *en cada momento*, para cada contenedor, en cada tamaño de pantalla — sin un solo breakpoint `@media`.
    
    Eso es justo lo que se planteaba como ideal: la grilla emerge del contenido y el espacio disponible, no de una decisión humana fija de antemano.
    
    __El problema: el wrapping rompe la grilla__
    
    Sin embargo, hay un defecto que ya se había identificado en *The Switcher* (otro artículo de la misma serie). Cuando los elementos usan `flex-grow` para estirarse y llenar el espacio sobrante de una fila, puede pasar que **la última fila tenga menos elementos que las anteriores**, y esos elementos restantes se estiren para llenar todo el ancho — quedando mucho más anchos que los elementos de las filas de arriba.
    
    El resultado (ilustrado en la imagen `littleboxes.png`) es una tensión entre dos objetivos que en realidad chocan:
    
    - ✅ **A favor**: el layout aprovecha todo el ancho disponible, sin huecos vacíos feos al final de una fila incompleta.
    - ❌ **En contra**: los elementos ya no están alineados en columnas verticales consistentes. Si esperas que una "grilla" tenga tanto filas como columnas alineadas (como una tabla), este comportamiento la rompe — el ancho de cada celda varía de fila a fila según cuántos elementos le tocó compartir esa fila.
    
    Esto deja planteado el problema que el resto del artículo probablemente resuelve: Flexbox es genial para filas flexibles, pero no fue diseñado para garantizar alineación en dos dimensiones (filas *y* columnas) — que es exactamente para lo que CSS Grid sí sirve, y por eso el argumento probablemente avanza hacia usar Grid con `auto-fit`/`minmax()` como la solución más "arquetípica y eficiente" que mencionaba el texto anterior.
    
## Mitigación

Recordarás la regla global de *measure* explorada en la sección *Axioms*. Esto aseguraba que todos los elementos aplicables no pudieran volverse más anchos que una longitud de línea cómodamente legible.

Donde un layout similar a una grilla creado con Flexbox resulta en un elemento `:last-child` de ancho completo, la medida de sus elementos de texto contenidos estaría en peligro de volverse demasiado larga. No con ese estilo de medida global en su lugar. El beneficio de las reglas globales (*axioms*) está en no tener que considerar cada principio de diseño por layout. Muchos ya están cuidados.

![](mawid.png)

??? info "Explicacion"

    Esta sección conecta dos piezas del sistema para mostrar que el problema que acabamos de ver (el `:last-child` que se estira a todo el ancho) **no siempre necesita una solución nueva** — a veces ya está resuelto por otra regla que ya existe en el sistema.
    
    __La idea central__
    
    En el artículo *Axioms* (que mencionaste antes), se estableció una regla global de *measure*: ningún elemento de texto debería poder crecer más allá de un ancho de línea cómodamente legible (algo así como `max-width: 60ch` o similar, aplicado globalmente a los elementos de texto).
    
    Ahora, volviendo al problema anterior: cuando la última fila de la formación Flexbox tiene un solo elemento sobrante, ese elemento se estira con `flex-grow` para llenar todo el ancho del contenedor — pudiendo volverse mucho más ancho que sus hermanos. El riesgo real de esto **no es la desalineación visual en sí**, sino que si ese elemento contiene texto, las líneas de texto se vuelven demasiado largas para leerse cómodamente (líneas muy anchas son fatigantes de leer).
    
    __Por qué esto es una mitigación, no una solución completa__
    
    El punto clave es que, si la regla de *measure* ya está aplicada globalmente a nivel del sistema, entonces **el contenido de texto dentro de ese elemento ancho de todos modos no se estirará** más allá del límite legible — aunque la *caja* del elemento (`:last-child`) sí ocupe todo el ancho disponible.
    
    Es decir:
    - La caja contenedora puede seguir viéndose "rota" en términos de alineación de grilla (ese último elemento sigue siendo visualmente más ancho que los demás, como mostraba `littleboxes.png`).
    - Pero el **contenido de texto dentro de esa caja** no sufre las consecuencias negativas de ese ancho excesivo, porque *measure* ya le pone un tope.
    
    __La lección de fondo sobre el sistema__
    
    Esto ilustra la filosofía de diseño de *Every Layout* en general: al tener **axiomas globales** (reglas base aplicadas ampliamente, como *measure*), no tienes que resolver cada problema de legibilidad individualmente en cada layout nuevo que diseñes. El problema del `:last-child` ancho sigue existiendo a nivel estructural/estético, pero uno de sus efectos secundarios más dañinos (texto illegible por líneas demasiado largas) ya está neutralizado de fábrica.
    
    Dicho de otro modo: no todos los defectos de un layout necesitan arreglarse *en* el layout — algunos ya están cubiertos por reglas que operan en una capa distinta y más general del sistema.

## CSS Grid para grillas

El acertadamente nombrado módulo CSS Grid nos acerca a una formación de grilla responsiva 'verdadera' en un sentido específico: Es posible hacer que los elementos crezcan, se contraigan y se envuelvan juntos *sin* violar los límites de las columnas.

![](behavior.png)

Este comportamiento está más cerca de la grilla responsiva arquetípica que tengo en mente, y será el layout que persigamos aquí. Solo hay un problema importante de implementación que resolver. Considera el siguiente código:

```css linenums="1"
.grid {
  display: grid;
  grid-gap: 1rem;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
}
```

Este es el patrón [*Layout Land* ↗](https://www.youtube.com/watch?v=tFKrK4eAiUQ), que descubrí por primera vez en la serie de videos de Jen Simmons. Para desglosarlo:

1. `display: grid` establece el contexto grid, que crea celdas de grilla para sus hijos.
2. `grid-gap` coloca un 'gutter' *entre* cada elemento de la grilla (ahorrándonos tener que emplear la técnica de margen negativo descrita primero en *The Cluster*).
3. `grid-template-columns` normalmente definiría una grilla rígida para *diseñar con una cuadrícula*, pero usado con `repeat` y `auto-fit` permite la generación dinámica y el wrapping de columnas para crear un comportamiento similar a la solución de Flexbox anterior.
4. `minmax()` asegura que cada columna, y por lo tanto cada celda de contenido, comparta un ancho entre un valor mínimo y máximo. Dado que `1fr` representa una parte del espacio disponible, las columnas crecen juntas para llenar el contenedor.

La deficiencia de este layout es el valor *mínimo* en `minmax()`. A diferencia de `flex-basis`, que permite cualquier cantidad de crecimiento o contracción desde un solo valor 'ideal', `minmax()` establece un alcance con límites duros.

Sin un mínimo fijo (`250px`, en este caso) no hay nada que *active* el wrapping. Un valor de `0` produciría solo una fila de anchos cada vez más diminutos. Pero que sea un mínimo fijo tiene una consecuencia clara: en cualquier contexto más estrecho que el mínimo, ocurrirá desbordamiento.

![](hardminium.png)

Para ponerlo simplemente: el patrón tal como está solo puede producir layouts de manera segura donde las columnas convergen en un ancho que está por debajo del mínimo estimado para el contenedor. Unos `250px` son razonablemente seguros porque la mayoría de los viewports de dispositivos móviles no son más anchos. Pero, ¿qué pasa si quiero que mis columnas crezcan considerablemente más allá de este ancho, donde el espacio está disponible? Con Flexbox y `flex-basis` eso es bastante posible, pero con CSS Grid no lo es sin asistencia.

??? info "Explicacion"

    Esta sección presenta la solución "casi ideal" con CSS Grid, y luego identifica su propio talón de Aquiles — preparando el terreno para lo que probablemente viene después (una técnica de asistencia adicional, muy probablemente usando `min()`).
    
    __El avance respecto a Flexbox__
    
    El problema del artículo anterior (el `:last-child` que se estira solo, rompiendo la alineación de columnas) queda resuelto aquí. Con CSS Grid, los elementos pueden crecer, encogerse y pasar a nueva línea **sin salirse de los límites de columna** — es decir, todos los elementos de una fila respetan el mismo ancho de columna que los de las filas de arriba y abajo, algo que Flexbox no podía garantizar porque `flex-grow` actúa fila por fila, de forma independiente.
    
    __Cómo funciona el código__
    
    ```css
    .grid {
      display: grid;
      grid-gap: 1rem;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    }
    ```
    
    - **`display: grid`**: activa el contexto de grilla.
    - **`grid-gap`**: crea espacio entre elementos sin necesitar el truco del margen negativo que se usaba en *The Cluster* (otro artículo de la serie).
    - **`repeat(auto-fit, ...)`**: en vez de escribir manualmente `1fr 1fr 1fr`, le pide al navegador que genere tantas columnas como quepan, dinámicamente — el mismo espíritu "algorítmico, no arbitrario" del ejemplo con Flexbox.
    - **`minmax(250px, 1fr)`**: cada columna tiene un ancho mínimo de `250px` y un máximo de `1fr` (una fracción proporcional del espacio sobrante). Como todas las columnas comparten `1fr` como máximo, crecen *juntas* y de forma pareja para llenar el contenedor — a diferencia de Flexbox, donde solo el elemento sobrante crecía.
    
    __El problema nuevo: el mínimo es una pared dura__
    
    Aquí está el matiz técnico importante: `flex-basis` es un punto de partida flexible — un elemento puede crecer o encogerse libremente desde ahí. Pero el mínimo de `minmax()` es un **límite duro (hard limit)**: la columna *nunca* puede ser más angosta que ese valor, pase lo que pase.
    
    Esto crea una paradoja de diseño:
    
    1. **Necesitas un mínimo fijo para que exista el wrapping en absoluto.** Si pusieras `minmax(0, 1fr)`, nunca habría suficiente "presión" para forzar una nueva fila — solo obtendrías una única fila con columnas cada vez más angostas y diminutas, sin límite.
    2. **Pero ese mismo mínimo fijo causa desbordamiento (overflow)** en cualquier contenedor que sea más angosto que ese valor. Si el contenedor mide 200px y tu mínimo es `250px`, el contenido se va a desbordar — no hay forma de que el navegador lo evite, porque le dijiste explícitamente que nunca baje de ese ancho.
    
    __Por qué `250px` "funciona" pero no es la solución real__
    
    `250px` es un valor relativamente seguro en la práctica porque la mayoría de los viewports móviles son más anchos que eso, así que rara vez ves el desbordamiento en el mundo real. Pero eso es suerte de rango, no una solución robusta — es exactamente el tipo de "ajuste manual basado en suposiciones sobre el espacio" que el artículo inicial (*El problema*) advertía que había que evitar.
    
    Y hay una segunda limitación, quizás más importante para el argumento: **el mínimo fijo también te impide crecer generosamente cuando sí hay espacio de sobra**. Con Flexbox, `flex-basis: 30ch` era solo un punto de referencia — los elementos podían crecer bastante más allá de eso si el contenedor era ancho. Con `minmax(250px, 1fr)`, aunque el máximo técnicamente es `1fr` (proporcional), en la práctica el sistema no tiene forma de decirle "prefiero columnas de ~250px, pero si hay mucho espacio, que crezcan más antes de multiplicarse" — solo sabe "mínimo 250px, y agregar tantas columnas de esas quepan".
    
    Esto dejar planteado el problema exacto que el artículo probablemente resuelve a continuación: necesitas una forma de decir *"el mínimo también debería ser relativo al contenedor, no un valor fijo en px"* — que es justo el terreno de la técnica con `min()` que hizo famosa esta serie (algo como `minmax(min(250px, 100%), 1fr)`), para eliminar el overflow en contenedores angostos sin sacrificar el mecanismo de wrapping.

    
## La solución

Cada uno de los layouts descritos hasta ahora en *Every Layout* han manejado el dimensionamiento y el wrapping con solo CSS, y sin consultas `@media`. A veces no es posible confiar solo en CSS para la reconfiguración automática. En estas circunstancias, recurrir a breakpoints `@media` está fuera de discusión, porque socava la modularidad del sistema de layout. En su lugar, difiero a JavaScript.

Pero debería hacerlo *juiciosamente*, y usando progressive enhancement.

[`ResizeObserver` ↗ ](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver) [(*pronto Container Queries*)](https://caniuse.com/?search=ResizeObserver) es una API altamente optimizada para rastrear y responder a cambios en las dimensiones de los elementos. Es el método más eficiente hasta ahora para crear [*container queries* ↗](https://philipwalton.com/articles/responsive-components-a-solution-to-the-container-queries-problem/) con JavaScript. No recomendaría usarlo como cuestión de rutina, pero empleado *solo* para resolver problemas de layout difíciles es aceptable.

Considera el siguiente código:

```css linenums="1"
.grid {
  display: grid;
  grid-gap: 1rem;
}
.grid.aboveMin {
  grid-template-columns: repeat(auto-fit, minmax(500px, 1fr));
}
```

La clase `.aboveMin` preside una declaración que anula y produce la grilla responsiva. Luego se instruye a `ResizeObserver` para agregar y eliminar la clase dependiendo del ancho del contenedor. El valor mínimo de `500px` (en el ejemplo anterior) se aplica *solo* donde el contenedor mismo es más ancho que ese umbral. Aquí hay una función independiente para activar el `ResizeObserver` en un elemento de grilla.

```javascript linenums="1"
function observeGrid(gridNode) {
  // Detección de características de ResizeObserver
  if ('ResizeObserver' in window) {
    // Obtener el valor min de data-min="[min]"
    const min = gridNode.dataset.min;
    // Crear un elemento proxy para medir y convertir
    // el valor `min` (que podría ser em, rem, etc) a `px`
    const test = document.createElement('div');
    test.style.width = min;
    gridNode.appendChild(test);
    const minToPixels = test.offsetWidth;
    gridNode.removeChild(test);
    const ro = new ResizeObserver(entries => {
      for (let entry of entries) {
        // Obtener las dimensiones actuales del elemento
        const cr = entry.contentRect;
        // `true` si el contenedor es más ancho que el mínimo
        const isWide = cr.width > minToPixels;
        // alternar la clase condicionalmente
        gridNode.classList.toggle('aboveMin', isWide);
      }
    });
    ro.observe(gridNode);
  }
}
```

Si `ResizeObserver` no es compatible, el layout de una sola columna de respaldo se aplica perpetuamente. Este respaldo básico se incluye aquí por brevedad, pero podrías en su lugar recurrir a la solución de Flexbox funcional pero imperfecta cubierta en la sección anterior. En cualquier caso, ningún contenido se pierde u oculta, y tienes la capacidad de usar valores mínimos más grandes para formaciones de grilla más expresivas. Y dado que ya no estamos sujetos a límites absolutos, podemos comenzar a emplear `minmax()` con *unidades relativas*.

![](minmax.png)

*Aquí hay un ejemplo de inicialización (el código está omitido por brevedad)*

```html linenums="1"
<div class="grid" data-min="250px">
<!-- Place children here -->
</div>
<script>
const grid = document.querySelector('.grid');
observeGrid(grid);
</script>
```

??? info "Explicacion"

    Aquí llega la solución definitiva al problema planteado en la sección anterior: cómo permitir mínimos de columna generosos (más allá de lo "seguro" como `250px`) sin arriesgarse al desbordamiento en contenedores angostos — sin recurrir a `@media`, que rompería la modularidad del sistema.
    
    __Por qué no `@media`__
    
    Ya se había establecido (en *The Switcher*) que los breakpoints `@media` reaccionan al viewport completo, no al espacio real disponible del contenedor padre de un componente. Eso hace que un componente se comporte de forma impredecible según dónde se inserte — lo opuesto a un sistema de diseño modular, donde cada componente debe responder a *su propio* contexto, sin importar dónde viva. Por eso, aunque `@media` podría "resolver" el overflow técnicamente, se descarta por principio.
    
    __La solución: JavaScript, pero solo como último recurso__
    
    La postura del autor es clara: CSS primero, siempre. JavaScript entra únicamente cuando CSS puro no puede resolver el problema — y aquí, ese es el caso, porque CSS no tiene (todavía) una forma nativa de preguntar "¿cuánto espacio tiene disponible *este* contenedor?" (eso es justo lo que las *Container Queries*, mencionadas como algo que vendrá "pronto", resolverán de forma nativa).
    
    La herramienta elegida es `ResizeObserver`: una API del navegador optimizada específicamente para detectar cambios en las dimensiones de un elemento — a diferencia de escuchar el evento `resize` de la ventana, que solo te dice del viewport, no del contenedor.
    
    __Cómo funciona el patrón__
    
    __CSS__
    
    ```css
    .grid {
      display: grid;
      grid-gap: 1rem;
    }
    .grid.aboveMin {
      grid-template-columns: repeat(auto-fit, minmax(500px, 1fr));
    }
    ```
    
    Fíjate en la estructura: por defecto, `.grid` **no tiene** `grid-template-columns` definido — así que, sin la clase adicional, se comporta como una sola columna (el contenido simplemente se apila verticalmente, que es un layout seguro y sin overflow posible). La clase `.aboveMin` es la que *activa* la formación multi-columna con un `minmax()` de mínimo generoso (`500px` en este ejemplo, mucho más ancho que los `250px` "seguros" de antes).
    
    La clase `.aboveMin` solo se añade cuando JavaScript confirma que el contenedor realmente tiene espacio suficiente para soportarla sin desbordarse.
    
    __JavaScript: `observeGrid()`__
    
    Desglosemos la función paso a paso:
    
    1. **Detección de características** (`if ('ResizeObserver' in window)`): esto es *progressive enhancement* en acción — si el navegador no soporta la API, la función simplemente no hace nada, y el layout se queda en su estado base seguro de una columna. Nunca se rompe nada; en el peor caso, se pierde la mejora visual, no la funcionalidad.
    
    2. **Leer el mínimo desde `data-min`**: en vez de hardcodear el valor `500px` en JavaScript, se lee de un atributo `data-min` en el HTML — así el mismo script sirve para cualquier grilla con cualquier mínimo, sin duplicar código.
    
    3. **El truco del "elemento proxy"**: aquí está el detalle más ingenioso. El valor de `data-min` podría estar en cualquier unidad CSS — `px`, `em`, `rem`, `ch`, etc. Pero `ResizeObserver` te devuelve las dimensiones del contenedor **solo en píxeles**. Entonces, para comparar peras con peras, el código crea un `<div>` invisible temporal, le asigna `width: [valor de data-min]`, lo inserta en el DOM (dentro del propio grid, para heredar el contexto correcto de unidades relativas), lee su `offsetWidth` (que sí está en píxeles), y luego lo elimina inmediatamente. Es una forma barata y confiable de hacer que el navegador haga la conversión de unidades por ti.
    
    4. **El observer en sí**: `ResizeObserver` ejecuta un callback cada vez que las dimensiones del elemento observado cambian. Dentro del callback, `entry.contentRect.width` te da el ancho actual del contenedor en píxeles. Se compara ese ancho contra el mínimo ya convertido (`minToPixels`), y el resultado booleano (`isWide`) se usa para **alternar** la clase `aboveMin` con `classList.toggle()`.
    
    __El resultado práctico__
    
    - Si el contenedor es más angosto que el mínimo → no tiene la clase `.aboveMin` → cae al layout de una columna → **cero riesgo de overflow**.
    - Si el contenedor es más ancho que el mínimo → se aplica `.aboveMin` → entra en juego el `minmax(500px, 1fr)` → columnas generosas y responsivas.
    - Ningún contenido se oculta ni se pierde nunca, en ningún escenario — solo cambia cuántas columnas hay.
    
    __Por qué esto es mejor que antes__
    
    Con este mecanismo, ya no dependes de elegir un mínimo "razonablemente seguro" como `250px` por miedo al overflow — porque JavaScript garantiza que ese mínimo *nunca* se aplique en un contenedor donde no cabe. Esto abre la puerta a usar mínimos mucho más expresivos y grandes (`500px`, o incluso valores en unidades relativas como `%` o `ch`), algo que era imposible de hacer con seguridad usando solo CSS puro en la sección anterior.
    
    En resumen: el patrón usa JavaScript de forma mínima y quirúrgica — no para controlar el layout entero, sino solo para decidir *cuándo* activar una regla CSS que, de otro modo, sería peligrosa de aplicar incondicionalmente. Es progressive enhancement en su forma más pura: la base funciona sin JS, y JS solo mejora la experiencia cuando está disponible.

??? info "Explicacion"

    Claro. Esta parte de *Every Layout* está intentando resolver un problema muy concreto: **¿cómo hago que una grilla se adapte según el espacio que realmente tiene, sin usar `@media`?**
    
    La idea central es bastante buena:
    
    > **No quiero preguntar "¿qué tan ancha es la pantalla?", quiero preguntar "¿qué tan ancho es este componente?"**
    
    Vamos paso a paso.
    
    ---
    
    __1. El problema que quieren resolver__
    
    Imagina que tienes:
    
    ```html
    <div class="grid">
        <article>...</article>
        <article>...</article>
        <article>...</article>
    </div>
    ```
    
    Y quieres que:
    
    * si `.grid` es pequeña → 1 columna
    * si `.grid` tiene suficiente espacio → varias columnas
    
    Normalmente podrías hacer:
    
    ```css
    .grid {
        display: grid;
        grid-template-columns: 1fr;
    }
    
    @media (min-width: 800px) {
        .grid {
            grid-template-columns: repeat(3, 1fr);
        }
    }
    ```
    
    Pero hay un problema.
    
    __`@media` pregunta por la ventana__
    
    El breakpoint de:
    
    ```css
    @media (min-width: 800px)
    ```
    
    significa:
    
    > "Si **la ventana del navegador** mide al menos 800px..."
    
    Pero el componente podría estar dentro de un sidebar.
    
    Por ejemplo:
    
    ```text
    ┌──────────────────────────────────────────┐
    │                navegador                 │
    │                                          │
    │ ┌────────────┐ ┌───────────────────────┐ │
    │ │  SIDEBAR   │ │       CONTENIDO       │ │
    │ │            │ │                       │ │
    │ │  .grid     │ │                       │ │
    │ │            │ │                       │ │
    │ └────────────┘ └───────────────────────┘ │
    │                                          │
    └──────────────────────────────────────────┘
    ```
    
    La ventana podría tener **1400px**.
    
    Pero `.grid` podría estar dentro del sidebar y tener solamente **300px**.
    
    Entonces:
    
    ```css
    @media (min-width: 800px)
    ```
    
    diría:
    
    > "¡Perfecto! Pantalla grande. Pon la grilla en varias columnas."
    
    Pero la grilla respondería:
    
    > "¿Varias columnas? ¿Con qué espacio? 😂"
    
    Ese es el problema.
    
    ---
    
    __2. La filosofía de Every Layout__
    
    Every Layout intenta evitar:
    
    ```css
    @media (min-width: ...)
    ```
    
    porque los componentes deberían ser **independientes del contexto donde se coloquen**.
    
    Por ejemplo, una tarjeta debería funcionar igual si la colocas:
    
    ```text
    página completa
            ↓
    ┌─────────────────────────────┐
    │           GRID              │
    └─────────────────────────────┘
    ```
    
    o:
    
    ```text
    sidebar
       ↓
    ┌──────────────┐
    │    GRID      │
    └──────────────┘
    ```
    
    o:
    
    ```text
    modal
       ↓
    ┌─────────────────┐
    │      GRID       │
    └─────────────────┘
    ```
    
    Por eso quieren algo parecido a:
    
    > "Si **YO**, la grilla, tengo suficiente espacio, cambio."
    
    No:
    
    > "Si la ventana tiene suficiente espacio, cambia."
    
    ---
    
    __3. Aquí aparece `ResizeObserver`__
    
    JavaScript tiene una API llamada:
    
    ```javascript
    ResizeObserver
    ```
    
    Su trabajo es básicamente:
    
    > **"Avísame cuando cambie el tamaño de este elemento."**
    
    Por ejemplo:
    
    ```javascript
    const observer = new ResizeObserver(entries => {
        console.log('El elemento cambió de tamaño');
    });
    
    observer.observe(grid);
    ```
    
    Entonces, si `.grid` pasa de:
    
    ```text
    300px
    ```
    
    a:
    
    ```text
    600px
    ```
    
    el `ResizeObserver` lo detecta.
    
    Y si luego vuelve a:
    
    ```text
    350px
    ```
    
    también lo detecta.
    
    ---
    
    __4. ¿Qué hacen con esa información?__
    
    Aquí está el truco interesante.
    
    El CSS dice:
    
    ```css
    .grid {
        display: grid;
        grid-gap: 1rem;
    }
    ```
    
    Eso es el estado base.
    
    Por defecto:
    
    ```text
    ┌───────────────┐
    │     item 1    │
    ├───────────────┤
    │     item 2    │
    ├───────────────┤
    │     item 3    │
    └───────────────┘
    ```
    
    Una columna.
    
    Pero después tienen:
    
    ```css
    .grid.aboveMin {
        grid-template-columns: repeat(
            auto-fit,
            minmax(500px, 1fr)
        );
    }
    ```
    
    Esta regla **solo existe cuando aparece la clase**:
    
    ```html
    class="grid aboveMin"
    ```
    
    Entonces:
    
    ```text
    .grid
       ↓
    1 columna
    ```
    
    y:
    
    ```text
    .grid.aboveMin
       ↓
    grid responsiva
    ```
    
    ---
    
    __5. ¿Quién agrega `.aboveMin`?__
    
    JavaScript.
    
    Ese es el corazón de todo.
    
    La función observa el ancho:
    
    ```javascript
    const cr = entry.contentRect;
    ```
    
    Aquí:
    
    ```javascript
    cr.width
    ```
    
    es el ancho actual del `.grid`.
    
    Por ejemplo:
    
    ```text
    grid = 350px
    ```
    
    Entonces:
    
    ```javascript
    cr.width
    ```
    
    vale aproximadamente:
    
    ```text
    350
    ```
    
    ---
    
    __6. ¿Y cuál es el mínimo?__
    
    HTML tiene:
    
    ```html
    <div class="grid" data-min="250px">
    ```
    
    Ese:
    
    ```html
    data-min="250px"
    ```
    
    es un **dato personalizado** que HTML le entrega a JavaScript.
    
    JavaScript obtiene:
    
    ```javascript
    const min = gridNode.dataset.min;
    ```
    
    Por lo tanto:
    
    ```javascript
    min
    ```
    
    será:
    
    ```text
    "250px"
    ```
    
    Esto es importante porque el mínimo **no está necesariamente escrito directamente en JavaScript**.
    
    El HTML configura el componente.
    
    ---
    
    __7. ¿Por qué crean este `div` extraño?__
    
    Esta parte:
    
    ```javascript
    const test = document.createElement('div');
    
    test.style.width = min;
    
    gridNode.appendChild(test);
    
    const minToPixels = test.offsetWidth;
    
    gridNode.removeChild(test);
    ```
    
    parece rara al principio.
    
    Y sí, tiene un propósito muy específico.
    
    Supongamos:
    
    ```html
    data-min="30em"
    ```
    
    JavaScript obtiene:
    
    ```javascript
    min = "30em"
    ```
    
    Pero `ResizeObserver` proporciona:
    
    ```javascript
    cr.width
    ```
    
    en **píxeles**.
    
    Entonces necesitan convertir:
    
    ```text
    30em
    ```
    
    a:
    
    ```text
    480px
    ```
    
    por ejemplo.
    
    ---
    
    __8. ¿Cómo hacen esa conversión?__
    
    Crean temporalmente un elemento:
    
    ```javascript
    const test = document.createElement('div');
    ```
    
    Le dicen:
    
    ```javascript
    test.style.width = min;
    ```
    
    Si:
    
    ```javascript
    min = "30em"
    ```
    
    el navegador calcula cuánto representan esos `30em` en píxeles.
    
    Después:
    
    ```javascript
    test.offsetWidth
    ```
    
    devuelve el resultado.
    
    Por ejemplo:
    
    ```text
    30em → 480px
    ```
    
    Entonces:
    
    ```javascript
    const minToPixels = test.offsetWidth;
    ```
    
    tendría:
    
    ```text
    480
    ```
    
    Finalmente eliminan el elemento:
    
    ```javascript
    gridNode.removeChild(test);
    ```
    
    Es un pequeño elemento "proxy" utilizado únicamente para que **el navegador haga la conversión por ellos**.
    
    ---
    
    __9. Ahora viene la comparación__
    
    Tenemos:
    
    ```javascript
    const isWide = cr.width > minToPixels;
    ```
    
    Supongamos:
    
    ```text
    minToPixels = 500
    ```
    
    Y la grilla mide:
    
    ```text
    700px
    ```
    
    Entonces:
    
    ```javascript
    700 > 500
    ```
    
    es:
    
    ```javascript
    true
    ```
    
    Por lo tanto:
    
    ```javascript
    isWide = true
    ```
    
    ---
    
    __10. Y aquí aparece el verdadero truco__
    
    Esta línea:
    
    ```javascript
    gridNode.classList.toggle('aboveMin', isWide);
    ```
    
    hace esto:
    
    __Si `isWide === true`__
    
    agrega:
    
    ```html
    class="grid aboveMin"
    ```
    
    Entonces se activa:
    
    ```css
    .grid.aboveMin {
        grid-template-columns:
            repeat(auto-fit, minmax(500px, 1fr));
    }
    ```
    
    ---
    
    __Si `isWide === false`__
    
    quita:
    
    ```html
    aboveMin
    ```
    
    y queda:
    
    ```html
    class="grid"
    ```
    
    Por lo tanto vuelve al CSS base.
    
    ---
    
    __11. Visualmente__
    
    Imagina que tenemos:
    
    ```html
    <div class="grid" data-min="500px">
    ```
    
    ### La grilla mide 400px
    
    ```text
    400px < 500px
    ```
    
    JavaScript hace:
    
    ```html
    <div class="grid">
    ```
    
    Resultado:
    
    ```text
    ┌──────────────┐
    │     A        │
    ├──────────────┤
    │     B        │
    ├──────────────┤
    │     C        │
    └──────────────┘
    ```
    
    ---
    
    __La grilla crece a 1200px__
    
    Ahora:
    
    ```text
    1200px > 500px
    ```
    
    JavaScript hace:
    
    ```html
    <div class="grid aboveMin">
    ```
    
    Y aparece:
    
    ```text
    ┌──────────┬──────────┐
    │    A     │    B     │
    ├──────────┼──────────┤
    │    C     │    D     │
    └──────────┴──────────┘
    ```
    
    Y lo interesante es que **no importa cuánto mida la ventana**.
    
    Importa cuánto mide `.grid`.
    
    ---
    
    __12. ¿Qué hace exactamente `minmax()`?__
    
    Esta parte:
    
    ```css
    minmax(500px, 1fr)
    ```
    
    significa:
    
    > Cada columna puede crecer, pero **no quiere ser menor que 500px**.
    
    Entonces:
    
    ```css
    grid-template-columns:
        repeat(auto-fit, minmax(500px, 1fr));
    ```
    
    le dice al navegador:
    
    > "Mete tantas columnas como puedas, pero cada una debe tener al menos 500px."
    
    Por ejemplo, con aproximadamente 1200px:
    
    ```text
    ┌──────────────┐ ┌──────────────┐
    │              │ │              │
    │   columna    │ │   columna    │
    │              │ │              │
    └──────────────┘ └──────────────┘
    ```
    
    Dos columnas.
    
    Pero con 700px:
    
    ```text
    ┌─────────────────────────┐
    │        columna          │
    └─────────────────────────┘
    ```
    
    Una.
    
    ---
    
    __13. ¿Pero por qué necesitan JavaScript si Grid ya hace esto?__
    
    Aquí está la parte sutil.
    
    Normalmente podrías escribir:
    
    ```css
    grid-template-columns:
        repeat(auto-fit, minmax(500px, 1fr));
    ```
    
    directamente.
    
    Y Grid ya intentaría adaptarse.
    
    Pero Every Layout quiere permitir **un límite mínimo más expresivo**.
    
    La lógica es:
    
    ```text
    Si el contenedor NO supera el mínimo
            ↓
    usar layout sencillo
    
    Si el contenedor SÍ supera el mínimo
            ↓
    activar el layout de Grid
    ```
    
    Es una especie de **interruptor de configuración**.
    
    JavaScript decide:
    
    ```text
    ¿Este componente tiene suficiente espacio?
            │
            ├── NO → layout básico
            │
            └── SÍ → layout Grid
    ```
    
    ---
    
    __14. ¿Por qué dicen "progressive enhancement"?__
    
    Esta frase es fundamental.
    
    **Progressive enhancement** significa:
    
    > Primero construyes una versión básica que funciona. Después agregas mejoras cuando el entorno lo permite.
    
    Aquí:
    
    __Nivel básico__
    
    ```css
    .grid {
        display: grid;
    }
    ```
    
    Funciona incluso si:
    
    ```javascript
    ResizeObserver
    ```
    
    no existe.
    
    Resultado:
    
    ```text
    A
    B
    C
    D
    ```
    
    una columna.
    
    __Mejora__
    
    Si existe:
    
    ```javascript
    ResizeObserver
    ```
    
    entonces:
    
    ```text
    ResizeObserver
          ↓
    mide .grid
          ↓
    ¿es suficientemente ancho?
          ↓
    sí
          ↓
    añade .aboveMin
          ↓
    Grid avanzado
    ```
    
    Por eso dicen:
    
    > Si `ResizeObserver` no es compatible, el layout de una sola columna de respaldo se aplica perpetuamente.
    
    No tienes:
    
    ```text
    JavaScript
        ↓
    falló
        ↓
    💥 página rota
    ```
    
    Tienes:
    
    ```text
    JavaScript
        ↓
    no disponible
        ↓
    layout básico
        ↓
    ✅ contenido sigue funcionando
    ```
    
    Eso es **progressive enhancement**.
    
    ---
    
    __15. Lo más importante: no están usando JS para hacer el layout__
    
    Esta distinción es muy importante.
    
    ❌ No están haciendo esto:
    
    ```javascript
    grid.style.gridTemplateColumns = ...
    ```
    
    Eso sería JavaScript controlando directamente el CSS.
    
    En cambio hacen:
    
    ```javascript
    gridNode.classList.toggle('aboveMin', isWide);
    ```
    
    JavaScript solamente dice:
    
    > "El componente está en estado `aboveMin`."
    
    Y CSS decide:
    
    > "Perfecto. Entonces aplico este layout."
    
    Es una separación de responsabilidades bastante limpia:
    
    ```text
    JavaScript
        │
        │ detecta estado
        ↓
    .aboveMin
        │
        │ CSS
        ↓
    layout
    ```
    
    **JavaScript detecta. CSS presenta.**
    
    Esa es probablemente la idea más importante de todo este ejemplo.
    
    ---
    
    __16. ¿Y por qué esto es mejor que `@media`?__
    
    Porque `@media` mira:
    
    ```text
    viewport
    ```
    
    mientras que `ResizeObserver` mira:
    
    ```text
    elemento
    ```
    
    Comparación:
    
    ```text
               @media
                  ↓
           ┌──────────────┐
           │   viewport   │
           └──────────────┘
                  │
                  ↓
              componente
    ```
    
    versus:
    
    ```text
          ResizeObserver
                 ↓
           ┌──────────┐
           │  .grid   │
           └──────────┘
                 │
                 ↓
           toma su propia
           decisión
    ```
    
    Esto hace que el componente sea mucho más **modular**.
    
    Puedes moverlo:
    
    ```text
    página completa
    ```
    
    o:
    
    ```text
    sidebar
    ```
    
    o:
    
    ```text
    modal
    ```
    
    o:
    
    ```text
    card
    ```
    
    y seguirá tomando decisiones basándose en **su propio espacio**.
    
    ---
    
    __17. Y aquí hay una conexión importante con lo que estabas estudiando__
    
    Esto encaja perfectamente con la filosofía que has estado viendo en *Every Layout*.
    
    Por ejemplo:
    
    __`Sidebar`__
    
    Busca que dos elementos se acomoden según el espacio disponible.
    
    __`Switcher`__
    
    Decide cuándo pasar de:
    
    ```text
    A B C
    ```
    
    a:
    
    ```text
    A
    B
    C
    ```
    
    basándose en el espacio disponible.
    
    __`Grid`__
    
    Busca distribuir elementos en una cuadrícula.
    
    Y todos intentan evitar:
    
    ```css
    @media (min-width: 768px)
    @media (min-width: 1024px)
    @media (min-width: 1440px)
    ```
    
    porque esos valores describen **el dispositivo**, no necesariamente **el espacio real del componente**.
    
    ---
    
    __La idea en una sola frase__
    
    Quédate con esto:
    
    > **`ResizeObserver` permite que un componente pregunte cuánto espacio tiene realmente y cambie de comportamiento según ese espacio, sin depender del tamaño de la pantalla.**
    
    Y el flujo completo del código es:
    
    ```text
    HTML
    data-min="250px"
           │
           ↓
    JavaScript lee 250px
           │
           ↓
    ResizeObserver observa .grid
           │
           ↓
    ¿cuánto mide .grid?
           │
           ├── 200px
           │      ↓
           │   no agrega .aboveMin
           │      ↓
           │   layout básico
           │
           └── 600px
                  ↓
            agrega .aboveMin
                  ↓
            minmax(250px, 1fr)
                  ↓
            Grid responsivo
    ```
    
    **En esencia, están construyendo una especie de "container query" antes de que las Container Queries de CSS fueran la solución estándar.** De hecho, el propio texto que estás leyendo menciona que eran una alternativa mientras las *Container Queries* estaban llegando. Hoy CSS tiene `@container`, así que para código nuevo normalmente **preferirías Container Queries antes que implementar este patrón con `ResizeObserver`**, salvo que exista una razón concreta para usar JavaScript.

## La función `min()`

Si bien vale la pena cubrir `ResizeObserver` porque puede servirte bien en otras circunstancias, en realidad ya no es necesario para resolver este problema en particular. Esto se debe a que tenemos la [*función CSS `min()` recientemente ampliamente adoptada* ↗](https://caniuse.com/css-math-functions). Perdón por la falsa alarma, pero podemos, de hecho, escribir este layout sin JavaScript después de todo.

Como respaldo, configuramos la grilla en una sola columna. Luego usamos `@supports` para probar `min()` y mejorar desde allí:

```css linenums="1"
.grid {
  display: grid;
  grid-gap: 1rem;
}
@supports (width: min(250px, 100%)) {
  .grid {
    grid-template-columns: repeat(auto-fit, minmax(min(250px, 100%), 1fr));
  }
}
```

La forma en que funciona `min()` es que calcula la *longitud más corta* a partir de un conjunto de valores separados por comas. Esto es: `min(250px, 100%)` devolvería `100%` donde `100%` se evalúa como *más pequeña* que los `250px` evaluados, y viceversa. Este útil algoritmo decide por nosotros dónde el ancho debe tener un tope máximo de `100%`.

??? info "Explicacion"

    Este cierre es un giro elegante en el argumento: la solución JavaScript de la sección anterior, aunque bien construida, resulta ser innecesaria — porque CSS moderno ya trae la herramienta que hacía falta desde el principio.
    
    __El truco: `min()` como mínimo "inteligente"__
    
    Recordemos el problema original: `minmax(250px, 1fr)` usa `250px` como un **límite duro** — la columna nunca puede ser más angosta que eso, sin importar qué tan angosto sea el contenedor, causando overflow.
    
    La función `min()` resuelve esto de forma muy simple: evalúa una lista de valores y **siempre devuelve el más pequeño de todos, en tiempo real**, recalculando cada vez que el contexto cambia.
    
    Entonces, en vez de escribir:
    
    ```css
    minmax(250px, 1fr)
    ```
    
    se escribe:
    
    ```css
    minmax(min(250px, 100%), 1fr)
    ```
    
    Aquí `100%` se refiere al 100% del ancho disponible del contenedor. Esto significa:
    
    - **Si el contenedor es ancho** (más de `250px`): `250px` es el valor más pequeño de los dos, así que `min()` devuelve `250px`. El comportamiento es idéntico al `minmax(250px, 1fr)` de antes — columnas de `250px` como mínimo, creciendo hasta `1fr`.
    - **Si el contenedor es angosto** (menos de `250px`): ahora `100%` (que en ese contexto vale, digamos, `180px`) es el valor más pequeño. `min()` devuelve ese `100%`, así que el mínimo de la columna se ajusta automáticamente para nunca exceder el ancho real disponible. **Nunca puede haber overflow**, porque el mínimo jamás pide más espacio del que existe.
    
    Es, en esencia, un mínimo *condicional y relativo* calculado por el propio motor CSS, en cada repintado, sin que ninguna línea de JavaScript tenga que medir nada.
    
    __Por qué esto vuelve obsoleto todo el capítulo de `ResizeObserver`__
    
    El problema fundamental que forzó recurrir a JavaScript era: *"CSS no puede preguntar cuánto espacio tiene disponible el contenedor y ajustar un valor mínimo en consecuencia."* Eso ya no es cierto con `min()` — CSS ahora *sí* puede hacer exactamente esa pregunta, de forma nativa y declarativa, comparando un valor absoluto (`250px`) contra uno relativo al contenedor (`100%`).
    
    Esto es coherente con la filosofía de toda la serie: **JavaScript es el último recurso, no el primero**. El autor reconoce honestamente que enseñó una solución más compleja de lo necesario ("perdón por la falsa alarma"), pero la deja documentada porque `ResizeObserver` sigue siendo útil para otros problemas de layout donde no exista un equivalente puro en CSS.
    
    __El rol de `@supports`__
    
    ```css
    @supports (width: min(250px, 100%)) {
      .grid {
        grid-template-columns: repeat(auto-fit, minmax(min(250px, 100%), 1fr));
      }
    }
    ```
    
    Esto es el mismo espíritu de *progressive enhancement* que se vio con `ResizeObserver`, pero aplicado a nivel de CSS puro: si el navegador no soporta `min()` dentro de `width` (o por extensión, dentro de `minmax()`), la regla dentro de `@supports` simplemente se ignora, y el `.grid` se queda en su definición base — una sola columna, sin `grid-template-columns` — que es segura y nunca produce overflow. Los navegadores que sí soportan `min()` reciben la mejora completa.
    
    __El resultado fina__
    
    Con esto se cierra el arco completo del artículo: empezó con un problema aparentemente irresoluble solo con CSS (mínimos fijos = overflow inevitable en contenedores angostos), pasó por una solución honesta pero pesada con JavaScript, y termina con la solución más "arquetípica y eficiente" que el primer texto prometía — una sola línea de CSS, sin JavaScript, sin `@media`, completamente independiente del contexto, y verdaderamente responsiva tanto en el sentido de crecer/encogerse como en el de nunca desbordar.
    
??? info "Explicacion"

    Sí. Esta sección es justamente la **simplificación** de todo lo anterior. Después de enseñarte `ResizeObserver + JavaScript`, *Every Layout* dice:

    > "Espera... para este problema concreto, CSS ya puede hacerlo solo."

    Y la pieza clave es `min()`.

    ---

    __1. ¿Qué problema tenían antes?__

    Querían esto:

    > Cada columna puede tener como mínimo **250px**, pero **nunca debe provocar overflow si el contenedor es más pequeño que 250px**.

    El problema aparece aquí:

    ```css
    minmax(250px, 1fr)
    ```

    Imagina que `.grid` solamente tiene:

    ```text
    200px
    ```

    Pero le dices:

    ```css
    minmax(250px, 1fr)
    ```

    Estás diciendo:

    > "La columna no puede medir menos de 250px."

    Pero el contenedor solo tiene 200px.

    Resultado potencial:

    ```text
    contenedor: 200px

    ┌────────────────────┐
    │                    │
    │      250px         │
    │                    │
    └────────────────────┘
          ↑
        overflow
    ```

    Ahí estaba el problema.

    ---

    __2. La solución es `min()`__

    Ahora aparece:

    ```css
    min(250px, 100%)
    ```

    Esto significa:

    > **Escoge el valor más pequeño entre `250px` y `100%`.**

    Por ejemplo, si el contenedor mide:

    ```text
    200px
    ```

    entonces:

    ```css
    min(250px, 100%)
    ```

    se convierte en:

    ```text
    min(250px, 200px)
    ```

    Y gana:

    ```text
    200px
    ```

    ---

    Si el contenedor mide:

    ```text
    500px
    ```

    entonces:

    ```text
    min(250px, 500px)
    ```

    gana:

    ```text
    250px
    ```

    Por lo tanto:

    ```text
    contenedor 200px → mínimo efectivo = 200px
    contenedor 500px → mínimo efectivo = 250px
    ```

    Eso es todo el truco.

    ---

    __3. Ahora mira esta línea__

    ```css
    grid-template-columns:
        repeat(
            auto-fit,
            minmax(min(250px, 100%), 1fr)
        );
    ```

    Puede parecer una sopa de funciones CSS 😅.

    Vamos a desmontarla desde dentro hacia fuera.

    Tenemos:

    ```css
    min(250px, 100%)
    ```

    ↓

    ```css
    minmax(min(250px, 100%), 1fr)
    ```

    ↓

    ```css
    repeat(auto-fit, ...)
    ```

    ---

    __4. Primero: `min(250px, 100%)`__

    Esta parte establece el **mínimo real de la columna**.

    ```css
    min(250px, 100%)
    ```

    La regla es:

    > Nunca uses más de 250px como mínimo, pero si el contenedor es más pequeño, usa el 100% del contenedor.

    Por ejemplo:

    | Ancho del contenedor | `min(250px, 100%)` |
    | -------------------: | -----------------: |
    |                150px |              150px |
    |                200px |              200px |
    |                250px |              250px |
    |                300px |              250px |
    |                500px |              250px |
    |               1000px |              250px |

    Observa la frontera:

    ```text
                250px
                    │
                    ↓
    100% ───────────┐
                    │
                    └──────────── 250px
        contenedor
        pequeño          grande
    ```

    Mientras el contenedor sea menor que 250px:

    ```text
    100%
    ```

    gana.

    Cuando supera 250px:

    ```text
    250px
    ```

    gana.

    ---

    __5. Después entra `minmax()`__

    Ahora tenemos:

    ```css
    minmax(min(250px, 100%), 1fr)
    ```

    `minmax()` significa:

    > La columna tendrá un mínimo y podrá crecer hasta el máximo indicado.

    Por ejemplo, en un contenedor de 800px:

    ```css
    min(250px, 100%)
    ```

    produce:

    ```text
    250px
    ```

    Entonces queda conceptualmente:

    ```css
    minmax(250px, 1fr)
    ```

    La columna:

    ```text
    mínimo → 250px
    máximo → 1fr
    ```

    ---

    __6. ¿Y qué hace `repeat(auto-fit, ...)`?__

    Esta parte:

    ```css
    repeat(auto-fit, ...)
    ```

    le dice al Grid:

    > Mete tantas columnas como puedan caber.

    Por ejemplo, si tienes:

    ```text
    800px
    ```

    y cada columna necesita como mínimo:

    ```text
    250px
    ```

    pueden entrar aproximadamente:

    ```text
    250 + 250 + espacio
    ```

    por lo que tendrás dos columnas.

    Visualmente:

    ```text
    ┌────────────┬────────────┐
    │     A      │     B      │
    │            │            │
    └────────────┴────────────┘
    ```

    ---

    Con:

    ```text
    500px
    ```

    podría entrar:

    ```text
    ┌──────────────────────────┐
    │            A             │
    ├──────────────────────────┤
    │            B             │
    └──────────────────────────┘
    ```

    Una columna.

    ---

    __7. Pero aquí está la genialidad__

    ¿Qué ocurre si el contenedor mide solamente:

    ```text
    180px
    ```

    La fórmula:

    ```css
    min(250px, 100%)
    ```

    se convierte conceptualmente en:

    ```css
    min(250px, 180px)
    ```

    Resultado:

    ```text
    180px
    ```

    Entonces tenemos:

    ```css
    minmax(180px, 1fr)
    ```

    La columna cabe perfectamente.

    ```text
    ┌──────────────────┐
    │        A         │
    └──────────────────┘
          180px
    ```

    **No necesitas JavaScript.**

    ---

    __8. ¿Qué estaba haciendo `ResizeObserver` antes?__

    Recuerda la solución anterior.

    JavaScript tenía que hacer algo parecido a:

    ```text
    ¿el contenedor mide más de 250px?
            │
            ├── NO → layout básico
            │
            └── SÍ → activar Grid
    ```

    Ahora CSS puede resolverlo directamente:

    ```text
    ¿el contenedor mide más de 250px?
            │
            ├── NO → min() usa 100%
            │
            └── SÍ → min() usa 250px
    ```

    Por eso el texto dice:

    > "podemos, de hecho, escribir este layout sin JavaScript después de todo."

    Y esta es una excelente lección de diseño:

    **Antes de meter JavaScript para resolver un problema de layout, hay que comprobar hasta dónde llega CSS.**

    ---

    __9. ¿Y para qué sirve `@supports`?__

    Tenemos:

    ```css
    .grid {
      display: grid;
      grid-gap: 1rem;
    }

    @supports (width: min(250px, 100%)) {
      .grid {
        grid-template-columns:
          repeat(
            auto-fit,
            minmax(min(250px, 100%), 1fr)
          );
      }
    }
    ```

    Primero tenemos el **fallback**:

    ```css
    .grid {
        display: grid;
    }
    ```

    Si el navegador no entiende `min()`:

    ```text
    ┌───────────────┐
    │      A        │
    ├───────────────┤
    │      B        │
    ├───────────────┤
    │      C        │
    └───────────────┘
    ```

    Una columna.

    Pero si el navegador entiende:

    ```css
    min()
    ```

    entonces entra:

    ```css
    @supports
    ```

    y mejora el layout:

    ```text
    ┌───────────┬───────────┐
    │     A     │     B     │
    ├───────────┼───────────┤
    │     C     │     D     │
    └───────────┴───────────┘
    ```

    Eso vuelve a ser **progressive enhancement**:

    ```text
    CSS básico
      ↓
    funciona
      ↓
    ¿soporta min()?
      ↓
    sí
      ↓
    mejora el layout
    ```

    ---

    __10. La idea que debes guardar__

    La combinación:

    ```css
    repeat(
        auto-fit,
        minmax(
            min(250px, 100%),
            1fr
        )
    )
    ```

    puedes leerla en español así:

    > **"Pon tantas columnas como quepan. Cada columna normalmente quiere tener al menos 250px, pero si el contenedor es menor que 250px, permite que ocupe el 100% disponible. Después deja que las columnas crezcan para aprovechar el espacio."**

    Eso es bastante poderoso.

    ---

    __Y conecta directamente con lo que venías estudiando__

    Antes:

    ```text
    @media
      ↓
    pregunta por viewport
    ```

    Después:

    ```text
    ResizeObserver
      ↓
    JavaScript pregunta por contenedor
    ```

    Y finalmente:

    ```text
    min()
      ↓
    CSS resuelve el problema
    ```

    La evolución conceptual es:

    ```text
    @media
      ↓
    "¿Qué tan grande es mi pantalla?"

    ResizeObserver
      ↓
    "¿Qué tan grande soy yo?"

    min()
      ↓
    "CSS, calcula tú cuánto espacio necesito."
    ```

    Y esa última es la solución más elegante **cuando el problema puede expresarse completamente con CSS**.

    De hecho, esta parte de *Every Layout* es muy representativa de su filosofía: **primero intenta resolver el comportamiento con las herramientas intrínsecas de CSS; solo cuando CSS realmente no alcanza, introduce JavaScript.**


## `<watched-box>`

Si estás buscando una solución general para [*container queries* ↗](https://css-tricks.com/tag/container-queries/), he creado `<watched-box>` ↗. Es directo y declarativo, y soporta cualquier unidad de longitud CSS.

Se recomienda que `<watched-box>` se use como una anulación manual de "último recurso". En todos los casos excepto los inusuales, uno de los layouts puramente basados en CSS documentados en *Every Layout* proporcionará un layout sensible al contexto automáticamente.

??? info "Explicacion"

    Sí. Esta sección es básicamente el **último escalón** de lo que acabamos de estudiar.

    La idea de `<watched-box>` es:

    > **Si CSS no puede resolver tu problema de adaptación al contenedor, entonces puedes usar un componente que observe el tamaño del elemento y permita reaccionar a él.**

    ---

    __1. ¿Qué es `<watched-box>`?__

    `<watched-box>` es un **Web Component**.

    Es decir, un elemento HTML personalizado:

    ```html
    <watched-box>
        ...
    </watched-box>
    ```

    En lugar de tener que escribir tú toda la lógica de:

    ```javascript
    ResizeObserver
    ```

    el componente se encarga de observar el tamaño del elemento.

    Conceptualmente:

    ```text
    ┌───────────────────────────┐
    │      <watched-box>        │
    │                           │
    │       contenido           │
    │                           │
    └───────────────────────────┘
                │
                ↓
          observa su tamaño
                │
                ↓
          cambia de estado
    ```

    Es una forma de encapsular el patrón que viste anteriormente.

    ---

    __2. ¿Por qué dice "container queries"?__

    Porque el problema que quieren resolver es este:

    ```text
    ┌─────────────────────────────────────────┐
    │             página                      │
    │                                         │
    │ ┌──────────────┐ ┌────────────────────┐ │
    │ │              │ │                    │ │
    │ │  componente  │ │    componente      │ │
    │ │              │ │                    │ │
    │ └──────────────┘ └────────────────────┘ │
    │                                         │
    └─────────────────────────────────────────┘
    ```

    Supongamos que el primer componente mide:

    ```text
    300px
    ```

    y el segundo:

    ```text
    900px
    ```

    Aunque ambos están dentro de la **misma ventana**, tienen diferentes cantidades de espacio.

    Una *container query* permite decir:

    > "Si **este contenedor** tiene más de X espacio, cambia mi diseño."

    No:

    > "Si la pantalla tiene más de X píxeles..."

    Esa diferencia es fundamental.

    ---

    __3. `<watched-box>` automatiza lo que viste con `ResizeObserver`__

    Antes tenías que escribir:

    ```javascript
    const ro = new ResizeObserver(entries => {
        // medir elemento
        // comparar
        // agregar/quitar clase
    });
    ```

    Con un componente como:

    ```html
    <watched-box>
    ```

    la idea es que esa infraestructura ya esté encapsulada.

    Es decir:

    ```text
    Tú
    │
    │ escribes HTML declarativo
    ↓
    <watched-box>
    │
    │ internamente
    ↓
    ResizeObserver
    │
    ↓
    detecta tamaño
    │
    ↓
    aplica comportamiento
    ```

    Tú no necesitas estar escribiendo toda esa maquinaria cada vez.

    ---

    __4. ¿Qué significa "directo y declarativo"?__

    Esto es importante.

    __Imperativo__

    Con JavaScript tradicional dices **cómo hacerlo**:

    ```javascript
    const observer = new ResizeObserver(...);
    observer.observe(element);
    ```

    Estás dando instrucciones.

    ---

    __Declarativo__

    Con un componente declarativo dices básicamente:

    ```html
    <watched-box>
    ```

    y describes **qué quieres**, dejando que el componente se encargue de la mecánica.

    Es la misma filosofía que existe en muchas herramientas modernas:

    ```text
    imperativo:
    "haz A, luego B, luego C"

    declarativo:
    "quiero que esto tenga este comportamiento"
    ```

    ---

    __5. ¿Por qué menciona "cualquier unidad de longitud CSS"?__

    Esto también conecta con lo que acabamos de ver.

    En CSS puedes tener:

    ```css
    250px
    ```

    pero también:

    ```css
    20rem
    30em
    50vw
    10ch
    50%
    ```

    El problema es que JavaScript normalmente recibe dimensiones físicas en píxeles.

    Por eso, en el ejemplo anterior de `ResizeObserver`, tenían que hacer esta pequeña maniobra:

    ```javascript
    const test = document.createElement('div');
    test.style.width = min;
    ...
    const minToPixels = test.offsetWidth;
    ```

    Estaban convirtiendo:

    ```text
    20rem
    ↓
    píxeles
    ```

    para poder comparar.

    `<watched-box>` pretende encargarse de ese tipo de trabajo.

    ---

    __6. Pero hay una advertencia importante__

    El texto dice:

    > "Se recomienda que `<watched-box>` se use como una anulación manual de 'último recurso'."

    Eso quiere decir:

    **No lo uses automáticamente para todo.**

    No deberías pensar:

    > "Tengo un componente responsive → uso `<watched-box>`."

    Primero deberías preguntarte:

    > "¿CSS puede resolver esto?"

    Y probablemente sí.

    ---

    __7. El orden recomendado por Every Layout__

    Yo lo resumiría así:

    __🥇 Primera opción: CSS normal__

    Usa herramientas como:

    ```css
    flexbox
    grid
    min()
    max()
    clamp()
    minmax()
    auto-fit
    auto-fill
    ```

    Por ejemplo:

    ```css
    grid-template-columns:
        repeat(auto-fit, minmax(min(250px, 100%), 1fr));
    ```

    Si esto resuelve el problema:

    **Perfecto. No necesitas más.**

    ---

    __🥈 Segunda opción: Container Queries__

    Actualmente puedes usar CSS:

    ```css
    @container
    ```

    cuando realmente necesitas que un componente responda al tamaño de su contenedor.

    Por ejemplo, conceptualmente:

    ```css
    @container (min-width: 500px) {
        .card {
            /* diseño diferente */
        }
    }
    ```

    Esto es mucho más natural que hacer:

    ```javascript
    ResizeObserver
    ```

    manualmente.

    ---

    __🥉 Último recurso: JavaScript / `<watched-box>`__

    Cuando el problema es demasiado complejo para CSS:

    ```text
    CSS
    ↓
    no alcanza
    ↓
    container queries
    ↓
    no alcanzan
    ↓
    watched-box / ResizeObserver
    ```

    Ahí sí tiene sentido.

    ---

    __8. ¿Por qué insiste tanto en esto?__

    Porque hay una tendencia muy común en frontend:

    ```text
    "Necesito responsive"
            ↓
    @media
            ↓
    "Necesito algo más complejo"
            ↓
    JavaScript
            ↓
    100 líneas de código
            ↓
    💀
    ```

    Every Layout intenta invertir ese pensamiento:

    ```text
    "Necesito responsive"
            ↓
    ¿CSS puede hacerlo?
            ↓
    sí → CSS
            ↓
    ¿Container Query?
            ↓
    sí → @container
            ↓
    ¿realmente necesito JS?
            ↓
    sí → ResizeObserver / watched-box
    ```

    Eso produce sistemas más simples y más mantenibles.

    ---

    __9. La conexión con todo lo anterior__

    Fíjate cómo ha evolucionado el capítulo:

    ```text
                    RESPONSIVE
                        │
                        ↓
                ¿Puede CSS solo?
                        │
                ┌──────┴──────┐
                │             │
                SÍ            NO
                │             │
                ↓             ↓
          CSS intrínseco   ¿Container Query?
                              │
                        ┌─────┴─────┐
                        │           │
                        SÍ          NO
                        │           │
                        ↓           ↓
                    @container   JavaScript
                                    │
                                    ↓
                              ResizeObserver
                                    │
                                    ↓
                              <watched-box>
    ```

    Ese es el verdadero mensaje de esta sección.

    ---

    __Y una precisión importante respecto al texto__

    El libro que estás leyendo refleja una etapa en la que **Container Queries todavía no estaban ampliamente disponibles**. Por eso presenta `ResizeObserver` y `<watched-box>` como soluciones para simular *container queries*.

    Hoy la situación es diferente: **las Container Queries de CSS ya son una herramienta estándar y ampliamente soportada**.

    Así que si estás aprendiendo esto para desarrollar proyectos actuales, yo no copiaría literalmente la arquitectura del libro.

    Aprendería el **principio**:

    > **Un componente debería responder al espacio que tiene, no al tamaño arbitrario de la pantalla.**

    Y para código moderno:

    ```css
    @container
    ```

    es normalmente tu primera herramienta cuando necesitas una verdadera consulta basada en el contenedor.

    `ResizeObserver` y soluciones como `<watched-box>` quedan como herramientas más especializadas.

    **Ese es el aprendizaje que realmente vale la pena llevarte del capítulo.**

## Casos de uso

Las grillas son excelentes para navegar por avances de enlaces permanentes o productos. Puedo componer rápidamente un componente de tarjeta para albergar cada uno de mis avances usando un `Box` y un `Stack`.

[*Esta demostración interactiva solo está disponible en el sitio de Every Layout* ↗.](https://every-layout.dev/demos/grid-cards/)

??? info "Explicacion"

    Sí. Este párrafo es corto, pero introduce una idea muy importante: **cómo combinar los layouts de Every Layout para construir componentes reales**.

    La frase:

    > "Las grillas son excelentes para navegar por avances de enlaces permanentes o productos."

    se refiere a interfaces como estas:

    ```text
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │   Producto  │ │   Producto  │ │   Producto  │
    │             │ │             │ │             │
    │   imagen    │ │   imagen    │ │   imagen    │
    │             │ │             │ │             │
    │   título    │ │   título    │ │   título    │
    └─────────────┘ └─────────────┘ └─────────────┘
    ```

    Por ejemplo:

    * productos de una tienda
    * artículos de un blog
    * proyectos de un portafolio
    * películas
    * noticias
    * publicaciones
    * resultados de búsqueda

    La demostración oficial de *Every Layout* justamente muestra una **Grid of cards**. ([Every Layout][1])

    ---

    __1. ¿Qué significa "componer" un componente?__

    Aquí está la palabra importante:

    > **componer**

    Every Layout no pretende darte un componente gigantesco llamado:

    ```text
    ProductCard
    ```

    con 300 reglas CSS.

    En cambio, pretende que construyas componentes pequeños combinando layouts simples.

    Por ejemplo:

    ```text
    Card
    ├── Box
    │
    └── Stack
    ```

    Es decir:

    ```text
    ┌─────────────────────┐
    │                     │
    │       imagen        │
    │                     │
    │─────────────────────│
    │ título              │
    │                     │
    │ descripción         │
    │                     │
    │ precio              │
    └─────────────────────┘
    ```

    La **Grid** organiza las tarjetas.

    El **Box** controla el contenedor de cada tarjeta.

    El **Stack** organiza verticalmente el contenido dentro de la tarjeta.

    Cada layout tiene una responsabilidad pequeña.

    ---

    __2. Piensa en tres niveles__

    Esta es la forma más sencilla de entenderlo:

    ```text
    Página
      │
      └── Grid
          │
          ├── Card
          │    ├── Box
          │    └── Stack
          │
          ├── Card
          │    ├── Box
          │    └── Stack
          │
          └── Card
                ├── Box
                └── Stack
    ```

    La **Grid** no necesita saber qué hay dentro de cada tarjeta.

    Solo sabe:

    > "Tengo varios elementos y debo distribuirlos."

    La **Card** tampoco necesita saber dónde está colocada.

    Solo sabe:

    > "Tengo contenido que debo presentar como una unidad."

    Y el **Stack**:

    > "Tengo varios elementos y quiero distribuirlos verticalmente."

    Esto es **composición**.

    ---

    __3. ¿Qué hace Grid?__

    Supongamos que tienes:

    ```html
    <div class="grid">
        <article class="card">...</article>
        <article class="card">...</article>
        <article class="card">...</article>
        <article class="card">...</article>
    </div>
    ```

    Grid se ocupa de esto:

    ```text
    ┌──────────┐ ┌──────────┐ ┌──────────┐
    │ Card 1   │ │ Card 2   │ │ Card 3   │
    └──────────┘ └──────────┘ └──────────┘

    ┌──────────┐
    │ Card 4   │
    └──────────┘
    ```

    Pero **no debería ocuparse de cómo está construida Card 1**.

    ---

    __4. ¿Qué hace Box?__

    El `Box` es básicamente un contenedor que permite controlar cosas como:

    ```text
    padding
    border
    background
    ```

    Por ejemplo:

    ```css
    .card {
        padding: 1rem;
        border: 1px solid;
    }
    ```

    Visualmente:

    ```text
    ┌─────────────────────────┐
    │ ← padding →             │
    │                         │
    │      contenido          │
    │                         │
    │             ← padding → │
    └─────────────────────────┘
    ```

    Por eso Box es útil para crear la "caja" física de la tarjeta.

    ---

    __5. ¿Qué hace Stack?__

    Supongamos que dentro tienes:

    ```html
    <h2>Producto</h2>
    <p>Descripción...</p>
    <span>$20</span>
    <button>Comprar</button>
    ```

    Quieres:

    ```text
    Producto

    Descripción...

    $20

    Comprar
    ```

    Eso es exactamente un **Stack**:

    ```text
    ┌────────────────────┐
    │ Producto           │
    │                    │
    │ Descripción        │
    │                    │
    │ $20                │
    │                    │
    │ Comprar            │
    └────────────────────┘
    ```

    El Stack se preocupa de la **relación vertical entre los elementos**.

    ---

    __6. Entonces cada layout tiene una responsabilidad__

    Puedes verlo así:

    | Layout    | Responsabilidad                        |
    | --------- | -------------------------------------- |
    | **Grid**  | Distribuir las tarjetas                |
    | **Box**   | Dar estructura/espaciado al contenedor |
    | **Stack** | Distribuir contenido verticalmente     |

    Y eso es exactamente lo que *Every Layout* quiere enseñarte.

    No pienses:

    > "¿Cuál es el CSS de una tarjeta?"

    Piensa:

    > "¿Qué relaciones espaciales tiene esta tarjeta?"

    Por ejemplo:

    ```text
    Muchas tarjetas
          ↓
        Grid

    Una tarjeta
          ↓
        Box

    Contenido vertical
          ↓
        Stack
    ```

    ---

    __7. Esto es especialmente importante para tu forma de trabajar__

    Como has estado estudiando `Sidebar`, `Cluster`, `Switcher`, `Cover`, `Stack`, etc., probablemente estás empezando a ver que **Every Layout no son componentes aislados**.

    No son:

    ```text
    Grid
    Sidebar
    Stack
    Cluster
    Switcher
    Box
    ```

    como si fueran piezas completamente independientes.

    Son más bien **primitivas que puedes combinar**.

    Por ejemplo:

    ```text
                    GRID
                      │
            ┌─────────┼─────────┐
            ↓         ↓         ↓
          CARD      CARD      CARD
            │         │         │
          BOX       BOX       BOX
            │         │         │
          STACK     STACK     STACK
            │         │         │
          ┌─┴─┐     ┌─┴─┐     ┌─┴─┐
          ↓   ↓     ↓   ↓     ↓   ↓
        título img  título img título img
    ```

    Eso empieza a parecerse muchísimo más a una arquitectura de frontend real.

    ---

    __La idea fundamental__

    No necesitas crear un layout específico llamado:

    ```text
    "ProductCardGridResponsive"
    ```

    Puedes construirlo mediante composición:

    ```text
    Grid
    └── Card
          ├── Box
          └── Stack
    ```

    Y mañana puedes reutilizar la misma estructura para:

    ```text
    Grid → artículos
    Grid → productos
    Grid → proyectos
    Grid → películas
    Grid → usuarios
    ```

    Cambia el **contenido**, pero las relaciones espaciales siguen siendo las mismas.

    Esa es una de las ideas más potentes de *Every Layout*: **no diseñar componentes pensando primero en su apariencia, sino en las relaciones de espacio que necesitan.**

    [1]: https://every-layout.dev/demos/grid-cards/ "Grid of cards: Every Layout"

## Altura compartida

Cada tarjeta comparte la misma altura, independientemente de su contenido, porque el valor predeterminado para `align-items` es `stretch`. Esto es fortuito ya que pocos esperarían tarjetas de diferentes tamaños, o los huecos antiestéticos que las alturas desiguales crearían.

??? info "Explicacion"

    Exacto. Aquí el texto está explicando **por qué las tarjetas de una Grid terminan teniendo la misma altura**, aunque su contenido sea diferente.

    La clave es:

    ```css
    align-items: stretch;
    ```

    y, muy importante, **`stretch` es el valor predeterminado de `align-items` en Grid**.

    ---

    __1. Imagina tres tarjetas__

    Supongamos que tienes:

    ```text
    ┌──────────┐  ┌──────────┐  ┌──────────┐
    │ Card A   │  │ Card B   │  │ Card C   │
    │          │  │          │  │          │
    │ texto    │  │ mucho     │  │ texto    │
    │          │  │ más texto │  │          │
    └──────────┘  │          │  └──────────┘
                  │          │
                  └──────────┘
    ```

    El contenido de cada tarjeta tiene una altura diferente:

    ```text
    A = 150px
    B = 250px
    C = 150px
    ```

    Uno podría pensar:

    > "Entonces cada tarjeta debería conservar su propia altura."

    Pero Grid hace algo diferente.

    ---

    __2. Grid crea una fila__

    Cuando tienes:

    ```css
    .grid {
        display: grid;
    }
    ```

    y los elementos están en la misma fila, Grid determina una altura para esa **fila**.

    Por ejemplo:

    ```text
    Card A       Card B       Card C
      ↓            ↓            ↓
    150px        250px        150px
    ```

    La fila necesita medir:

    ```text
    250px
    ```

    porque esa es la tarjeta más alta.

    Entonces la fila termina teniendo:

    ```text
    250px
    ```

    ---

    __3. Aquí aparece `align-items: stretch`__

    El valor predeterminado es:

    ```css
    align-items: stretch;
    ```

    `stretch` significa:

    > **Si el elemento no tiene una altura explícita, estíralo para ocupar todo el espacio disponible en el eje correspondiente.**

    Por eso:

    ```text
    altura de la fila = 250px

    ┌──────────┐ ┌──────────┐ ┌──────────┐
    │          │ │          │ │          │
    │  Card A  │ │  Card B  │ │  Card C  │
    │          │ │          │ │          │
    │          │ │          │ │          │
    └──────────┘ └──────────┘ └──────────┘
        250px        250px        250px
    ```

    Aunque A y C tengan menos contenido.

    ---

    __4. ¿Por qué es útil?__

    Porque imagina lo contrario:

    ```text
    ┌──────────┐  ┌──────────┐  ┌──────────┐
    │ Card A   │  │ Card B   │  │ Card C   │
    │          │  │          │  │          │
    │ texto    │  │ mucho     │  │ texto    │
    └──────────┘  │ más texto │  └──────────┘
                  │          │
                  └──────────┘
    ```

    Visualmente aparecen huecos.

    Especialmente en una interfaz de productos:

    ```text
    ┌────────────┐ ┌────────────┐ ┌────────────┐
    │ Producto A │ │ Producto B │ │ Producto C │
    │            │ │            │ │            │
    │ $20        │ │ $30        │ │ $15        │
    │            │ │            │ │            │
    │ Comprar    │ │ Comprar    │ │ Comprar    │
    └────────────┘ └────────────┘ └────────────┘
    ```

    Tener las tarjetas alineadas da una sensación mucho más ordenada.

    ---

    __5. Ojo: no significa que Grid esté cambiando el contenido__

    Esto es importante.

    Supongamos:

    ```text
    Card A → contenido real: 120px
    Card B → contenido real: 200px
    ```

    Grid **no hace que el contenido de A mágicamente mida 200px**.

    Lo que ocurre es:

    ```text
    fila
    ┌───────────────────────────────┐
    │                               │
    │            200px              │
    │                               │
    └───────────────────────────────┘
    ↑              ↑              ↑
    A              B              C
    ```

    Los elementos A, B y C se estiran para ocupar la altura disponible de la fila.

    El contenido interno sigue siendo el mismo.

    ---

    __6. `align-items` aquí trabaja en el eje vertical__

    Esto conecta con tu pregunta anterior sobre `align-items: stretch`.

    En Grid:

    ```css
    align-items
    ```

    controla cómo se alinean los elementos **dentro de sus celdas en el eje vertical** cuando estás trabajando con el flujo normal de filas.

    Por defecto:

    ```css
    align-items: stretch;
    ```

    Por eso las tarjetas se estiran.

    Si hicieras:

    ```css
    .grid {
        align-items: start;
    }
    ```

    ya no tendrías ese estiramiento.

    Podrías terminar con:

    ```text
    ┌──────────┐  ┌──────────┐  ┌──────────┐
    │ Card A   │  │ Card B   │  │ Card C   │
    │          │  │          │  │          │
    │ texto    │  │ mucho     │  │ texto    │
    └──────────┘  │ más texto │  └──────────┘
                  │          │
                  └──────────┘
    ```

    Cada tarjeta conservaría su altura de contenido.

    ---

    __7. Hay otra cosa interesante__

    La frase:

    > "Esto es fortuito..."

    significa que **no tuvieron que hacer nada especial para conseguir tarjetas de igual altura**.

    No necesitaron:

    ```css
    height: 300px;
    ```

    ni:

    ```css
    min-height: 300px;
    ```

    ni JavaScript.

    Simplemente:

    ```css
    .grid {
        display: grid;
    }
    ```

    y Grid ya tiene:

    ```css
    align-items: stretch;
    ```

    como comportamiento predeterminado.

    Eso es precisamente lo que *Every Layout* intenta aprovechar:

    > **Deja que el algoritmo de layout del navegador haga el trabajo siempre que sea posible.**

    ---

    __Qué debes recordar__

    Cuando veas:

    ```css
    align-items: stretch;
    ```

    piensa:

    > **"Los elementos se estiran para ocupar el espacio disponible en su celda."**

    Y en esta Grid de tarjetas:

    ```text
    contenido diferente
          ↓
    fila determina altura
          ↓
    align-items: stretch
          ↓
    tarjetas ocupan esa altura
          ↓
    ┌───────┐ ┌───────┐ ┌───────┐
    │       │ │       │ │       │
    │ Card  │ │ Card  │ │ Card  │
    │       │ │       │ │       │
    └───────┘ └───────┘ └───────┘
      misma altura
    ```

    **No es que Grid obligue a todos los contenidos a tener la misma altura; hace que las cajas de las tarjetas ocupen la misma altura de la fila.** Esa distinción es importante.

## El generador

Usa esta herramienta para generar CSS y HTML básicos de Grid.

La herramienta generadora de código solo está disponible en el *sitio de documentación adjunto* ↗. Aquí está la solución básica, con comentarios:

**CSS**

```css linenums="1"
.grid {
  /* ↓ Establece un contexto grid */
  display: grid;
  /* ↓ Establece un gap entre los elementos grid */
  grid-gap: 1rem;
  /* ↓ Establece el ancho mínimo de columna */
  --minimum: 20ch;
}
@supports (width: min(var(--minimum), 100%)) {
  .grid {
    /* ↓ Mejora con la función min()
    en múltiples columnas */
    grid-template-columns: repeat(auto-fit, minmax(min(var(--minimum), 100%),
    1fr));
  }
}
```

**Layout implícito de una sola columna**

Nota que `grid-template-columns` no se establece excepto en el bloque de mejora (`@supports`). Implícitamente, es una grilla de una sola columna a menos que `min()` sea compatible.

**HTML**

```html linenums="1"
<div class="grid">
  <div><!-- elemento hijo --></div>
  <div><!-- otro elemento hijo --></div>
  <div><!-- etc --></div>
</div>
```

??? info "Explicacion"

    Sí. Esta parte reúne prácticamente todo lo que acabamos de estudiar sobre **Grid + `min()` + progressive enhancement**, pero ahora lo convierte en un patrón reutilizable.

    Vamos a desarmarlo.

    ---

    __1. La configuración básica__

    Tenemos:

    ```css id="w2v7xj"
    .grid {
      display: grid;
      grid-gap: 1rem;
      --minimum: 20ch;
    }
    ```

    Hay tres cosas aquí.

    __`display: grid`__

    ```css id="o2w6c0"
    display: grid;
    ```

    Convierte `.grid` en un **contenedor Grid**.

    Sus hijos directos:

    ```html id="5l4q1q"
    <div class="grid">
      <div>...</div>
      <div>...</div>
      <div>...</div>
    </div>
    ```

    se convierten en elementos Grid.

    ---

    __`grid-gap: 1rem`__

    ```css id="t4iwm8"
    grid-gap: 1rem;
    ```

    establece separación entre las celdas.

    Visualmente:

    ```text id="1tvl73"
    ┌──────────┐   ┌──────────┐
    │ elemento │   │ elemento │
    └──────────┘   └──────────┘
          ↑
        1rem
    ```

    Hoy también es muy habitual escribir simplemente:

    ```css id="xwq2q5"
    gap: 1rem;
    ```

    `gap` es más general porque funciona tanto con Grid como con Flexbox.

    ---

    __2. ¿Qué es `--minimum: 20ch`?__

    Aquí aparece una **custom property**:

    ```css id="6zypfq"
    --minimum: 20ch;
    ```

    Esto es una variable CSS.

    Por lo tanto, después puedes escribir:

    ```css id="xv5z8q"
    min(var(--minimum), 100%)
    ```

    y el navegador sustituirá:

    ```text id="kz3m0f"
    var(--minimum)
          ↓
        20ch
    ```

    Así que conceptualmente:

    ```css id="4g3m5r"
    min(20ch, 100%)
    ```

    ---

    __3. ¿Por qué `20ch` y no `250px`?__

    Esto es bastante interesante.

    `ch` es una unidad relativa relacionada con el ancho del carácter `0` de la fuente utilizada.

    Por eso:

    ```css id="m7dx5s"
    20ch
    ```

    puede entenderse aproximadamente como:

    > "Un ancho equivalente a unos 20 caracteres."

    No es exactamente "20 letras" universales, pero sirve muy bien para establecer **anchos relacionados con la legibilidad del texto**.

    Por ejemplo:

    ```text id="xj76zq"
    ┌───────────────────────────────┐
    │ Lorem ipsum dolor sit amet... │
    │                               │
    └───────────────────────────────┘
    ```

    En una grilla de tarjetas, `20ch` puede ser más interesante que decir:

    ```css id="v2n6pl"
    300px
    ```

    porque el ancho mínimo está relacionado con el contenido textual.

    ---

    __4. Ahora llega la parte importante__

    Tenemos:

    ```css id="1s0g9g"
    @supports (width: min(var(--minimum), 100%)) {
    ```

    Esto pregunta:

    > **"¿Este navegador entiende la función CSS `min()`?"**

    Si la respuesta es **no**, no entra.

    Si la respuesta es **sí**, aplica las reglas que están dentro.

    ---

    __5. ¿Qué pasa si NO soporta `min()`?__

    Entonces solo tenemos:

    ```css id="1b1w4k"
    .grid {
        display: grid;
        gap: 1rem;
    }
    ```

    Y como no hemos definido:

    ```css id="22rxgq"
    grid-template-columns
    ```

    Grid utiliza su comportamiento implícito.

    El resultado básico es:

    ```text id="22qjj8"
    ┌──────────────────┐
    │       A          │
    ├──────────────────┤
    │       B          │
    ├──────────────────┤
    │       C          │
    ├──────────────────┤
    │       D          │
    └──────────────────┘
    ```

    **Una columna.**

    Esto es lo que el texto llama:

    > "Layout implícito de una sola columna."

    ---

    __6. ¿Por qué una sola columna?__

    Porque no le has dicho:

    ```css
    grid-template-columns
    ```

    Por lo tanto, Grid crea filas implícitas para los elementos.

    Algo conceptualmente parecido a:

    ```css id="j1p8du"
    grid-template-columns: 1fr;
    ```

    aunque técnicamente no es que el navegador haya insertado literalmente esa declaración.

    Es el comportamiento automático de Grid.

    ---

    __7. Ahora imagina un navegador moderno__

    Supongamos que sí soporta:

    ```css id="1s1u0u"
    min()
    ```

    Entonces entra aquí:

    ```css id="8e8i5y"
    .grid {
      grid-template-columns:
        repeat(
          auto-fit,
          minmax(
            min(var(--minimum), 100%),
            1fr
          )
        );
    }
    ```

    Como:

    ```css id="fgy3s8"
    --minimum: 20ch;
    ```

    podemos leerlo como:

    ```css id="2d3e9p"
    grid-template-columns:
        repeat(
            auto-fit,
            minmax(
                min(20ch, 100%),
                1fr
            )
        );
    ```

    ---

    __8. Vamos de dentro hacia fuera__

    Esta es la mejor forma de leer CSS complejo.

    __Primero:__

    ```css id="8cn0s0"
    var(--minimum)
    ```

    ↓

    ```text id="1z2o8x"
    20ch
    ```

    __Después:__

    ```css id="4lqckv"
    min(20ch, 100%)
    ```

    ↓

    > Escoge el menor entre `20ch` y el ancho disponible.

    __Después:__

    ```css id="9o0j9j"
    minmax(min(20ch, 100%), 1fr)
    ```

    ↓

    > La columna tiene ese valor como mínimo y puede crecer.

    __Finalmente:__

    ```css id="r5x4o9"
    repeat(auto-fit, ...)
    ```

    ↓

    > Mete tantas columnas como puedan caber.

    ---

    __9. Visualicemos diferentes tamaños__

    Supongamos que:

    ```css id="y4k78t"
    --minimum: 20ch;
    ```

    __Contenedor pequeño__

    ```text id="6v7kws"
    ┌──────────────────┐
    │        A         │
    ├──────────────────┤
    │        B         │
    ├──────────────────┤
    │        C         │
    └──────────────────┘
    ```

    No cabe más de una columna.

    ---

    __Contenedor mediano__

    Ahora hay suficiente espacio:

    ```text id="k7ay8h"
    ┌────────────┐   ┌────────────┐
    │     A      │   │     B      │
    ├────────────┤   ├────────────┤
    │     C      │   │     D      │
    └────────────┘   └────────────┘
    ```

    Aparecen dos.

    ---

    __Contenedor grande__

    ```text id="sg37n5"
    ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
    │   A    │ │   B    │ │   C    │ │   D    │
    └────────┘ └────────┘ └────────┘ └────────┘
    ```

    Puede haber cuatro.

    Y no has escrito:

    ```css
    @media (min-width: 600px)
    ```

    ni:

    ```css
    @media (min-width: 900px)
    ```

    ni:

    ```css
    @media (min-width: 1200px)
    ```

    **La Grid calcula todo automáticamente.**

    ---

    __10. Esto es lo que hace tan interesante el patrón__

    El desarrollador solamente configura:

    ```css id="8lyl2d"
    --minimum: 20ch;
    ```

    Y dice:

    > "No quiero que las columnas sean más estrechas que esto."

    El navegador se encarga del resto.

    Es casi como darle una regla:

    ```text id="3b0gj4"
    "Cada tarjeta necesita aproximadamente este espacio."
    ```

    Y Grid responde:

    ```text id="2fb0mp"
    "Perfecto. Yo calculo cuántas caben."
    ```

    ---

    __11. ¿Por qué utilizar `auto-fit`?__

    Porque:

    ```css id="wj8xvb"
    repeat(auto-fit, ...)
    ```

    permite que Grid ajuste automáticamente la cantidad de columnas según el espacio disponible.

    No tienes que saber de antemano si serán:

    ```text id="s05r8r"
    1
    2
    3
    4
    5
    ```

    columnas.

    Grid lo calcula.

    Esto es lo que hace que el componente sea **intrínsecamente responsive**.

    ---

    __12. Mira la filosofía completa__

    El código entero:

    ```css id="6w2i5a"
    .grid {
      display: grid;
      gap: 1rem;
      --minimum: 20ch;
    }

    @supports (width: min(var(--minimum), 100%)) {
      .grid {
        grid-template-columns:
          repeat(
            auto-fit,
            minmax(
              min(var(--minimum), 100%),
              1fr
            )
          );
      }
    }
    ```

    puedes traducirlo a lenguaje humano:

    > "Soy una Grid y quiero 1rem de separación. Cada elemento debería tener aproximadamente 20ch como ancho mínimo. Si el navegador entiende `min()`, coloca tantas columnas como puedan caber. Si no entiende `min()`, no pasa nada: mantengo una columna."

    Eso es **progressive enhancement** en estado puro.

    ---

    __13. Y el HTML es deliberadamente aburrido__

    ```html id="a2f8h1"
    <div class="grid">
      <div>...</div>
      <div>...</div>
      <div>...</div>
    </div>
    ```

    Y eso es bueno.

    No necesitas meter clases como:

    ```html
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4">
    ```

    ni:

    ```html
    <div class="grid-responsive-small">
    ```

    ni:

    ```html
    <div class="grid-responsive-large">
    ```

    El HTML simplemente dice:

    > "Estos elementos forman una colección."

    Y CSS decide cómo distribuirlos.

    ---

    __14. Esto resume muy bien todo el capítulo de Grid__

    Puedes pensar en el patrón como:

    ```text id="7ahp6k"
                      GRID
                        │
                        ↓
                display: grid
                        │
                        ↓
                  gap: 1rem
                        │
                        ↓
              ¿soporta min()?
                /          \
              NO            SÍ
              │              │
              ↓              ↓
          1 columna       auto-fit
                              │
                              ↓
                        minmax(...)
                              │
                              ↓
                          min(...)
                              │
                              ↓
                    tantas columnas
                      como quepan
    ```

    Y aquí hay una idea que conviene que te quede grabada porque aparece una y otra vez en *Every Layout*:

    > **No le dices al navegador exactamente cuándo debe pasar de 1 a 2, de 2 a 3, etc. Le das las restricciones y dejas que el algoritmo de layout encuentre la solución.**

    Eso es justamente lo contrario de construir un responsive basado en una colección interminable de breakpoints.

## El componente

Una implementación de elemento personalizado del `Grid` está disponible para descargar ↗.

**API de Props**

Las siguientes props (atributos) harán que el componente se renderice nuevamente cuando se alteren. Pueden ser alterados a mano — en las herramientas de desarrollo del navegador — o como sujetos del estado de la aplicación heredada.

| Nombre | Tipo | Default | Descripción |
|---|---|---|---|
| `min` | string | `"250px"` | Un valor CSS de longitud que representa x en `minmax(min(x, 100%), 1fr)` |
| `space` | string | `"var(--s1)"` | El espacio entre las celdas de la grilla |

## Ejemplos

### Cards

El código para el ejemplo de cards de *Casos de uso*. Nota que el valor `min` es una fracción del *measure* estándar. Hay más sobre la medida tipográfica en *Axioms* (el rudimento).

```html linenums="1"
<grid-l min="calc(var(--measure) / 3)">
  <box-l>
    <stack-l>
      <!-- contenido de la tarjeta -->
    </stack-l>
  </box-l>
  <box-l>
    <stack-l>
      <!-- contenido de la tarjeta -->
    </stack-l>
  </box-l>
  <box-l>
    <stack-l>
      <!-- contenido de la tarjeta -->
    </stack-l>
  </box-l>
  <box-l>
    <stack-l>
      <!-- contenido de la tarjeta -->
    </stack-l>
  </box-l>
  <!-- etc -->
</grid-l>
```
