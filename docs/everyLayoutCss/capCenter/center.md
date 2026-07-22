# The Center

## El problema

En los primeros días de HTML, existían varios elementos de presentación; elementos diseñados puramente para afectar la apariencia de su contenido. El [`<center>` ↗](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/center) era uno de esos elementos, pero hace tiempo que se considera obsoleto. Curiosamente, todavía es compatible en algunos navegadores, incluido Google Chrome. Presumiblemente esto se debe a que la página de búsqueda de Google todavía usa un `<center>` para centrar su famoso input de búsqueda.

Dejando de lado el uso caprichoso de elementos obsoletos por parte de los gigantes tecnológicos, en su mayoría nos alejamos del uso de marcado de presentación en los años 2000. Al hacer que el estilo sea responsabilidad de una tecnología separada —CSS— pudimos gestionar el estilo y la estructura por separado. En consecuencia, un cambio en la dirección de arte ya no significaría reconstituir nuestro contenido.

Más tarde descubrimos que estilizar HTML puramente en términos de semántica y contexto era bastante ambicioso, y llevó a selectores poco manejables como:

```css linenums="1"
body > div > div > a {
  /* Estilos de enlace específicamente para enlaces
     anidados dos <div>s dentro del elemento body */
}
```

Por el bien de un mantenimiento de CSS más fácil y la modularidad del estilo, muchos de nosotros adoptamos una posición de compromiso usando clases. Debido a que las clases se pueden colocar en cualquier elemento, somos libres de estilizar, por ejemplo, un `<div>` no semántico o un `<nav>` reconocido por lectores de pantalla exactamente de la misma manera, usando el mismo token, pero sin comprometer la accesibilidad.

```html linenums="1"
<div class="text-align:center"></div>
<nav class="text-align:center"></nav>
```

??? info "Explicacion"

    Claro. Este texto está hablando de **cómo evolucionó la forma de separar HTML y CSS**, y por qué aparecieron las **clases CSS** como una solución práctica.

    Vamos por partes.

    ---

    __1. Antes, HTML se usaba también para controlar la apariencia__

    En los primeros tiempos de HTML, existían elementos que no describían el significado del contenido, sino **cómo debía verse**.

    Por ejemplo:

    ```html
    <center>
      Hola mundo
    </center>
    ```

    `<center>` significaba básicamente:

    > "Pon este contenido en el centro".

    El problema es que HTML debería encargarse principalmente de **la estructura y el significado**, mientras que CSS debería encargarse de **la apariencia**.

    Hoy escribiríamos:

    ```html
    <p>Hola mundo</p>
    ```

    Y en CSS:

    ```css
    p {
      text-align: center;
    }
    ```

    Entonces tenemos una separación:

    ```text
    HTML → ¿Qué es esto?
    CSS  → ¿Cómo se ve?
    ```

    Por ejemplo:

    ```html
    <nav>...</nav>
    ```

    HTML dice:

    > Esto es una navegación.

    CSS dice:

    > Quiero que esta navegación tenga fondo azul, esté centrada, tenga cierto tamaño, etc.

    ---

    __2. ¿Por qué fue importante separar HTML y CSS?__

    Imagina que tienes:

    ```html
    <h1>Mi página</h1>
    <p>Este es mi contenido.</p>
    <nav>...</nav>
    ```

    Y mezclas la presentación con HTML:

    ```html
    <center>
      <h1><font color="red">Mi página</font></h1>
    </center>
    ```

    Si mañana decides cambiar el diseño, tendrías que modificar el HTML.

    En cambio, si haces:

    ```html
    <h1>Mi página</h1>
    ```

    y:

    ```css
    h1 {
      color: red;
      text-align: center;
    }
    ```

    puedes cambiar todo el diseño desde CSS.

    Por eso el texto dice:

    > "Al hacer que el estilo sea responsabilidad de una tecnología separada —CSS— pudimos gestionar el estilo y la estructura por separado."

    La idea fundamental es:

    ```text
    HTML
    └── Estructura y significado

    CSS
    └── Presentación y diseño
    ```

    ---

    __3. Entonces apareció otro problema__

    Una vez que dejamos de usar elementos como `<center>`, empezamos a intentar seleccionar los elementos utilizando su **contexto o estructura**.

    Por ejemplo:

    ```css
    body > div > div > a {
      color: red;
    }
    ```

    Esto significa:

    ```text
    body
    └── div
        └── div
            └── a
    ```

    El selector está diciendo:

    > "Busca un `<a>` que esté dentro de un `<div>`, que a su vez esté dentro de otro `<div>`, que esté dentro de `<body>`."

    Esto puede funcionar, pero es bastante frágil.

    Imagina este HTML:

    ```html
    <body>
      <div>
        <div>
          <a href="#">Enlace</a>
        </div>
      </div>
    </body>
    ```

    Tu CSS funciona:

    ```css
    body > div > div > a {
      color: red;
    }
    ```

    Pero mañana alguien cambia la estructura:

    ```html
    <body>
      <main>
        <div>
          <div>
            <a href="#">Enlace</a>
          </div>
        </div>
      </main>
    </body>
    ```

    Ahora:

    ```css
    body > div > div > a
    ```

    ya no funciona.

    ¿Por qué?

    Porque el `<a>` ya no está directamente dentro de la estructura que esperabas.

    ---

    __4. Aquí entran las clases__

    Las clases permiten decir directamente:

    > "Este elemento tiene este propósito de diseño."

    Por ejemplo:

    ```html
    <a class="text-align-center">Hola</a>
    ```

    Y:

    ```css
    .text-align-center {
      text-align: center;
    }
    ```

    Ahora da igual dónde esté el elemento:

    ```html
    <body>
      <div>
        <div>
          <a class="text-align-center">Hola</a>
        </div>
      </div>
    </body>
    ```

    O:

    ```html
    <body>
      <main>
        <section>
          <nav>
            <a class="text-align-center">Hola</a>
          </nav>
        </section>
      </main>
    </body>
    ```

    La clase sigue funcionando:

    ```css
    .text-align-center {
      text-align: center;
    }
    ```

    La estructura puede cambiar y el estilo sigue siendo aplicable.

    ---

    __5. Pero hay algo interesante en el ejemplo__

    El texto pone:

    ```html
    <div class="text-align:center"></div>
    <nav class="text-align:center"></nav>
    ```

    Aquí hay que entender una cosa importante.

    El HTML permite que pongas una clase llamada:

    ```text
    text-align:center
    ```

    aunque normalmente sería más habitual escribir:

    ```html
    <div class="text-align-center"></div>
    <nav class="text-align-center"></nav>
    ```

    Y CSS:

    ```css
    .text-align-center {
      text-align: center;
    }
    ```

    La idea del ejemplo es mostrar que **la misma clase puede aplicarse a elementos completamente diferentes**.

    Por ejemplo:

    ```html
    <div class="text-align-center">
      Contenido
    </div>

    <nav class="text-align-center">
      <a href="#">Inicio</a>
    </nav>
    ```

    Y:

    ```css
    .text-align-center {
      text-align: center;
    }
    ```

    La clase no está diciendo:

    > "Esto es un `<div>`".

    Ni:

    > "Esto es un `<nav>`".

    La clase está diciendo:

    > "Este elemento debe tener este comportamiento visual."

    ---

    __6. ¿Y por qué menciona la accesibilidad?__

    Esta parte es importante.

    Supongamos que quieres que un bloque y una navegación se vean igual:

    ```html
    <div class="text-align-center">
      Hola
    </div>

    <nav class="text-align-center">
      <a href="/">Inicio</a>
    </nav>
    ```

    Visualmente:

    ```text
    ┌─────────────────────┐
    │       Hola          │
    │                     │
    │       Inicio        │
    └─────────────────────┘
    ```

    Pero semánticamente son cosas diferentes.

    El `<div>` es simplemente un contenedor genérico.

    El `<nav>` tiene un significado especial:

    > "Aquí hay navegación."

    Un lector de pantalla puede interpretar `<nav>` de manera especial.

    Entonces puedes hacer:

    ```css
    .text-align-center {
      text-align: center;
    }
    ```

    y compartir el mismo estilo **sin convertir todo en `<div>`**.

    Eso es lo que significa:

    > "sin comprometer la accesibilidad."

    La clase permite reutilizar el estilo sin sacrificar la semántica del HTML.

    ---

    __7. La idea central del texto__

    El texto está describiendo aproximadamente esta evolución:

    __Etapa 1: HTML controla estructura y presentación__

    ```html
    <center>
      Hola
    </center>
    ```

    ❌ HTML mezcla estructura y apariencia.

    ---

    __Etapa 2: CSS se encarga de la presentación__

    ```html
    <p>Hola</p>
    ```

    ```css
    p {
      text-align: center;
    }
    ```

    ✅ Mejor separación entre HTML y CSS.

    ---

    __Etapa 3: Selectores basados en estructura__

    ```css
    body > div > div > a {
      color: red;
    }
    ```

    ⚠️ Puede volverse difícil de mantener.

    ---

    __Etapa 4: Clases reutilizables__

    ```html
    <a class="text-align-center">Hola</a>
    ```

    ```css
    .text-align-center {
      text-align: center;
    }
    ```

    ✅ Más modular.

    Y esa es la idea que está defendiendo el texto:

    > **Las clases permiten aplicar un estilo o comportamiento visual a diferentes elementos HTML sin depender de la estructura exacta del documento y sin tener que sacrificar la semántica del HTML.**

    ---

    __Una analogía sencilla__

    Piensa en HTML como los **planos de un edificio**.

    ```html
    <nav>...</nav>
    <main>...</main>
    <footer>...</footer>
    ```

    Eso dice qué habitaciones existen y cuál es su función.

    CSS es el **diseño interior**:

    ```css
    .text-align-center {
      text-align: center;
    }
    ```

    La clase es como una **etiqueta de diseño reutilizable**:

    ```html
    <div class="text-align-center"></div>
    <nav class="text-align-center"></nav>
    <footer class="text-align-center"></footer>
    ```

    Los tres elementos siguen teniendo funciones diferentes, pero comparten una característica visual.

    **HTML dice qué es.
    CSS dice cómo se ve.
    La clase conecta ambos de forma reutilizable.**

    Ese es el corazón del texto.

## Convenciones de nomenclatura

Notarás mi convención de nomenclatura *on the nose* (directa) en el ejemplo anterior. Mi elección de nomenclatura para las clases de utilidad se cubre en la sección *Measure*. En resumen, la estructura `property-name:value` está diseñada para ayudar con el recuerdo.

Todo lo que `<center>` hacía, y todo lo que `text-align: center` hace, es centrar texto. Y para la mayoría del contenido —especialmente contenido que incluye texto de párrafo— querrás evitarlo. [*Es terrible para la legibilidad* ↗](https://uxmovement.com/content/why-you-should-never-center-align-paragraph-text/).

Pero lo que sería útil es un componente que pueda crear una columna centrada horizontalmente. Con tal componente, podríamos crear una 'franja' centrada de contenido dentro de cualquier contenedor, limitando su ancho para preservar una *medida* razonable.

??? info "Explicacion"

    Sí. Aquí el autor está continuando con la idea anterior, pero ahora habla de **cómo nombrar las clases CSS** y después introduce una idea muy importante de *Every Layout*: **crear una columna de contenido con un ancho máximo razonable**.

    Vamos por partes.

    ---

    __1. "Convenciones de nomenclatura"__

    El autor dice que utiliza una convención de nombres **muy directa** (*on the nose*).

    En el ejemplo anterior tenía algo como:

    ```html
    <div class="text-align:center"></div>
    ```

    La clase prácticamente describe exactamente la propiedad CSS:

    ```text
    text-align:center
        │       │
        │       └── valor
        └────────── propiedad
    ```

    Es decir:

    ```text
    property-name:value
    ```

    Por ejemplo:

    ```text
    text-align:center
    ```

    Que corresponde a:

    ```css
    text-align: center;
    ```

    La idea es que el nombre de la clase te ayude a **recordar qué hace**.

    Es como si vieras:

    ```html
    <div class="text-align:center">
    ```

    y automáticamente piensas:

    ```css
    text-align: center;
    ```

    Por eso dice:

    > "La estructura `property-name:value` está diseñada para ayudar con el recuerdo."

    No es que CSS obligue a nombrar las clases así. Es simplemente **la convención que utiliza el autor**.

    ---

    __2. Pero hay un problema con `text-align: center`__

    Aquí el autor hace una aclaración importante.

    Cuando escribes:

    ```css
    text-align: center;
    ```

    estás centrando **el texto dentro de un elemento**.

    Por ejemplo:

    ```html
    <p class="text-align:center">
      Este es un párrafo de texto.
    </p>
    ```

    Visualmente:

    ```text
    ┌──────────────────────────────┐
    │                              │
    │     Este es un párrafo.      │
    │                              │
    └──────────────────────────────┘
    ```

    El texto queda centrado.

    Pero el autor dice:

    > "Y para la mayoría del contenido —especialmente contenido que incluye texto de párrafo— querrás evitarlo."

    ¿Por qué?

    Porque para leer textos largos, normalmente es mejor que el texto esté alineado a la izquierda.

    Por ejemplo:

    ```text
    Este es un párrafo largo que
    continúa de izquierda a derecha
    y resulta más fácil seguir cada
    línea visualmente.
    ```

    En cambio:

    ```text
          Este es un párrafo largo
      que continúa de izquierda a
          derecha y resulta más
      difícil seguir cada línea.
    ```

    El segundo puede resultar más incómodo de leer.

    Por eso el autor dice:

    > "Es terrible para la legibilidad."

    No significa que **centrar texto siempre sea malo**.

    Para títulos, botones, mensajes cortos, etc., puede estar perfectamente bien:

    ```html
    <h1 class="text-align:center">
      Bienvenido
    </h1>
    ```

    El problema es usar `text-align: center` indiscriminadamente en grandes cantidades de texto.

    ---

    __3. Entonces, ¿qué quiere hacer realmente?__

    Aquí viene la parte importante.

    El autor dice:

    > "Pero lo que sería útil es un componente que pueda crear una columna centrada horizontalmente."

    Observa la diferencia:

    __Centrar el texto__

    ```css
    text-align: center;
    ```

    Centra **el contenido textual**.

    ---

    __Centrar una columna__

    Lo que quiere es centrar **el propio bloque de contenido**.

    Imagina esto:

    ```text
    ┌─────────────────────────────────────────────┐
    │                                             │
    │          ┌───────────────────────┐          │
    │          │                       │          │
    │          │   Contenido           │          │
    │          │                       │          │
    │          └───────────────────────┘          │
    │                                             │
    └─────────────────────────────────────────────┘
    ```

    El contenedor exterior ocupa todo el espacio.

    Pero la columna interior tiene un ancho limitado y está centrada.

    Esto **no significa que el texto esté centrado**.

    El texto puede seguir estando alineado a la izquierda:

    ```text
    ┌─────────────────────────────────────────────┐
    │                                             │
    │        ┌───────────────────────────┐        │
    │        │ Este es un párrafo largo  │        │
    │        │ que permanece alineado a  │        │
    │        │ la izquierda para facilitar│       │
    │        │ su lectura.               │        │
    │        └───────────────────────────┘        │
    │                                             │
    └─────────────────────────────────────────────┘
    ```

    La **columna** está centrada.

    El **texto** no.

    Esta diferencia es fundamental.

    ---

    __4. ¿Cómo se hace esto en CSS?__

    Una forma clásica sería:

    ```css
    .center {
      max-width: 60rem;
      margin-inline: auto;
    }
    ```

    Y:

    ```html
    <main class="center">
      <h1>Mi artículo</h1>

      <p>
        Este texto está dentro de una columna de ancho limitado.
      </p>
    </main>
    ```

    El resultado sería aproximadamente:

    ```text
    Pantalla completa
    ┌──────────────────────────────────────────────┐
    │                                              │
    │      ┌──────────────────────────────┐        │
    │      │ Mi artículo                  │        │
    │      │                              │        │
    │      │ Este texto está alineado     │        │
    │      │ a la izquierda y tiene una   │        │
    │      │ longitud razonable por línea.│        │
    │      └──────────────────────────────┘        │
    │                                              │
    └──────────────────────────────────────────────┘
    ```

    La magia está principalmente aquí:

    ```css
    margin-inline: auto;
    ```

    Esto permite que los márgenes izquierdo y derecho se distribuyan automáticamente, dejando el elemento centrado horizontalmente.

    Y:

    ```css
    max-width: 60rem;
    ```

    dice:

    > "Esta columna no debe crecer más allá de este ancho."

    ---

    __5. ¿Por qué limitar el ancho?__

    Esto conecta directamente con el concepto de **Measure** que menciona el autor.

    Imagina que tienes una pantalla gigantesca:

    ```text
    ┌─────────────────────────────────────────────────────────────────────────────┐
    │ Este es un párrafo que ocupa toda la pantalla y tiene líneas extremadamente │
    │ largas que dificultan mucho la lectura porque tus ojos tienen que viajar    │
    │ desde un extremo hasta el otro para seguir cada línea.                      │
    └─────────────────────────────────────────────────────────────────────────────┘
    ```

    Es incómodo.

    En cambio:

    ```text
    ┌─────────────────────────────────────────────────────────────────────────────┐
    │                                                                             │
    │           ┌─────────────────────────────────────────────┐                   │
    │           │ Este es un párrafo con un ancho razonable. │                   │
    │           │ Las líneas no son demasiado largas y los   │                   │
    │           │ ojos pueden recorrerlas fácilmente.        │                   │
    │           └─────────────────────────────────────────────┘                   │
    │                                                                             │
    └─────────────────────────────────────────────────────────────────────────────┘
    ```

    Mucho mejor.

    La idea es:

    ```text
    Pantalla grande
          ↓
    No hagas crecer infinitamente el texto
          ↓
    Limita el ancho
          ↓
    Centra la columna
          ↓
    Mantén una medida cómoda de lectura
    ```

    ---

    __6. Aquí está la diferencia que debes guardar__

    Creo que esta es la parte que más te conviene recordar:

    __`text-align: center`__

    Afecta **la alineación del contenido textual**.

    ```css
    text-align: center;
    ```

    Resultado:

    ```text
          Hola mundo
    ```

    ---

    __`margin-inline: auto`__

    Afecta **la posición del elemento como bloque**.

    ```css
    max-width: 60rem;
    margin-inline: auto;
    ```

    Resultado:

    ```text
          ┌──────────────────┐
          │ Hola mundo       │
          └──────────────────┘
    ```

    El bloque está centrado, pero el texto puede seguir estando a la izquierda:

    ```text
          ┌──────────────────┐
          │ Hola mundo       │
          │ Este es un texto │
          │ bastante largo.  │
          └──────────────────┘
    ```

    ---

    __En resumen__

    El autor está diciendo algo así:

    > "`text-align: center` puede centrar texto, pero centrar grandes cantidades de texto suele perjudicar la lectura. Lo que necesitamos normalmente no es centrar el texto, sino crear una **columna de contenido de ancho limitado y centrar esa columna dentro del espacio disponible**."

    Y esto es justamente la base del patrón **Center** de *Every Layout*.

    La idea visual es:

    ```text
    ┌───────────────────────────────────────┐
    │                                       │
    │       ┌───────────────────────┐       │
    │       │                       │       │
    │       │  CONTENIDO            │       │
    │       │  alineado normalmente │       │
    │       │  a la izquierda       │       │
    │       │                       │       │
    │       └───────────────────────┘       │
    │                                       │
    └───────────────────────────────────────┘
            ↑                       ↑
          auto                    auto
    ```

    Es decir, **no estás centrando el texto; estás centrando el contenedor que contiene el texto**. Esa distinción es una de las piezas clave para entender bien CSS moderno.

## La solución

Una de las formas más fáciles de resolver una columna centrada es usar márgenes `auto`. La palabra clave `auto`, como su nombre lo sugiere, le indica al navegador que calcule el margen por ti. Es quizás uno de los ejemplos más rudimentarios de una técnica CSS *algorithmica*: una que difiere a la lógica del navegador para determinar el layout en lugar de 'codificar' un valor específico.

![](centers.png)

Mis primeras columnas centradas usaban el shorthand `margin`, a menudo en el elemento `<body>`.

```css linenums="1"
.center {
  max-width: 60ch;
  margin: 0 auto;
}
```

El problema con el shorthand `margin` — aunque ahorra unos bytes — es que tienes que declarar ciertos valores, incluso cuando no son aplicables. Es importante establecer solo los valores CSS necesarios para lograr el layout específico que intentas. Nunca sabes qué valores inferidos o heredados podrías estar deshaciendo.

Por ejemplo, podría querer colocar mi elemento personalizado `<center-l>` dentro de un contexto `Stack`. `Stack` establece `margin-top` en sus hijos, y cualquier `<center-l>` con `margin: 0 auto` desharía eso.

![](margins.png)

En su lugar, podría usar las propiedades explícitas `margin-left` y `margin-right`. De esta forma, cualquier margen vertical aplicado contextualmente se preservaría, y el componente estaría preparado para la composición/anidación entre otros componentes de layout.

```css linenums="1"
.center {
  max-width: 60ch;
  margin-left: auto;
  margin-right: auto;
}
```

??? info "Explicacion"

    Sí. Esta parte es muy importante porque aquí *Every Layout* empieza a enseñarte una filosofía de CSS: **no solo hacer que algo funcione, sino hacer que los componentes sean resistentes cuando los combines con otros componentes**.

    Vamos paso a paso.

    ---

    __1. La solución: `margin: auto`__

    El objetivo era crear una columna centrada:

    ```text
    ┌──────────────────────────────────────────────┐
    │                                              │
    │        ┌────────────────────────────┐        │
    │        │                            │        │
    │        │        Contenido           │        │
    │        │                            │        │
    │        └────────────────────────────┘        │
    │                                              │
    └──────────────────────────────────────────────┘
    ```

    Una forma sencilla es:

    ```css
    .center {
      max-width: 60ch;
      margin: 0 auto;
    }
    ```

    Aquí tenemos dos cosas.

    __Primera:__

    ```css
    max-width: 60ch;
    ```

    Significa:

    > El elemento no crecerá más allá de aproximadamente 60 caracteres por línea.

    `ch` es una unidad relacionada con el ancho del carácter `0` de la fuente actual, y se usa mucho para controlar la longitud de líneas de texto.

    ---

    __Segunda:__

    ```css
    margin: 0 auto;
    ```

    Aquí ocurre algo interesante.

    `margin` es un shorthand.

    ```css
    margin: 0 auto;
    ```

    equivale aproximadamente a:

    ```css
    margin-top: 0;
    margin-right: auto;
    margin-bottom: 0;
    margin-left: auto;
    ```

    Visualmente:

    ```text
                margin-left: auto
                        ↓
          ┌──────────────────────────┐
          │                          │
          │        CONTENIDO         │
          │                          │
          └──────────────────────────┘
                        ↑
                margin-right: auto
    ```

    El navegador calcula automáticamente cuánto espacio libre debe colocar a izquierda y derecha.

    Por eso:

    ```css
    margin-left: auto;
    margin-right: auto;
    ```

    termina centrando el elemento horizontalmente.

    ---

    __2. ¿Qué quiere decir "algorithmic CSS"?__

    El autor dice que `auto` es un ejemplo de una técnica CSS "algorítmica".

    Esto significa que tú **no le dices al navegador exactamente cuánto espacio debe usar**.

    Por ejemplo, podrías hacer:

    ```css
    margin-left: 300px;
    margin-right: 300px;
    ```

    Pero eso sería una solución rígida.

    En cambio:

    ```css
    margin-left: auto;
    margin-right: auto;
    ```

    es como decir:

    > "Navegador, tú calcula cuánto espacio debe haber a cada lado para conseguir el resultado correcto."

    Por ejemplo:

    ```text
    Pantalla de 1200px

    ┌──────────────────────────────────────────────┐
    │              300px    600px    300px        │
    │              ←────→ ┌──────┐ ←────→         │
    │                     │      │                 │
    │                     └──────┘                 │
    └──────────────────────────────────────────────┘
    ```

    Si la pantalla cambia:

    ```text
    Pantalla de 1600px

    ┌──────────────────────────────────────────────────────────────┐
    │             500px       600px       500px                   │
    │             ←────→     ┌──────┐     ←────→                  │
    │                        │      │                              │
    │                        └──────┘                              │
    └──────────────────────────────────────────────────────────────┘
    ```

    No necesitas cambiar el CSS.

    El navegador calcula:

    ```text
    espacio disponible
            ↓
    lo distribuye
            ↓
    margin-left: auto
    margin-right: auto
            ↓
    elemento centrado
    ```

    Esto es una idea fundamental del CSS moderno:

    > **Deja que el navegador resuelva el layout cuando pueda, en lugar de intentar controlar cada píxel manualmente.**

    ---

    __3. El primer problema: `margin: 0 auto`__

    Ahora viene la parte más interesante.

    El autor inicialmente hacía:

    ```css
    .center {
      max-width: 60ch;
      margin: 0 auto;
    }
    ```

    Esto funciona perfectamente para centrar el elemento.

    Pero hay un problema:

    ```css
    margin: 0 auto;
    ```

    no solamente centra horizontalmente.

    También está diciendo:

    ```css
    margin-top: 0;
    margin-bottom: 0;
    ```

    Es decir:

    > "Elimina cualquier margen vertical."

    Y eso puede ser un problema si el elemento está dentro de otro componente.

    ---

    __4. El ejemplo con `Stack`__

    Imagina que tienes un componente llamado `Stack`.

    Supongamos que `Stack` hace algo como:

    ```css
    .stack > * {
      margin-top: 1rem;
    }
    ```

    Su objetivo es separar verticalmente sus hijos:

    ```text
    ┌────────────────────────────┐
    │ Elemento 1                 │
    │                            │
    │                            │
    │ Elemento 2                 │
    │                            │
    │                            │
    │ Elemento 3                 │
    └────────────────────────────┘
    ```

    El `Stack` controla el espacio vertical.

    Ahora metes dentro un elemento:

    ```html
    <Stack>
      <center-l>
        Contenido
      </center-l>
    </Stack>
    ```

    Y `center-l` tiene:

    ```css
    .center {
      max-width: 60ch;
      margin: 0 auto;
    }
    ```

    El `Stack` dice:

    ```css
    margin-top: 1rem;
    ```

    Pero `.center` dice:

    ```css
    margin-top: 0;
    ```

    Entonces `.center` puede terminar anulando el margen que el `Stack` quería aplicar.

    La idea visual sería:

    ```text
    Stack quiere:

    Elemento A
        ↓ 1rem
    Elemento B
        ↓ 1rem
    Elemento C
    ```

    Pero el `.center` dice:

    ```text
    margin-top: 0
    margin-bottom: 0
    ```

    Y básicamente estás diciendo:

    > "Yo, componente `.center`, voy a redefinir también los márgenes verticales."

    Aunque **no era necesario**.

    ---

    __5. Aquí aparece una regla muy importante__

    El autor dice:

    > "Es importante establecer solo los valores CSS necesarios para lograr el layout específico que intentas."

    Esta es una idea que te recomiendo guardar.

    Si tu objetivo es:

    > Centrar horizontalmente un elemento.

    Entonces necesitas:

    ```css
    margin-left: auto;
    margin-right: auto;
    ```

    No necesitas:

    ```css
    margin-top: 0;
    margin-bottom: 0;
    ```

    Por lo tanto, no deberías tocar esos valores.

    Es mejor escribir:

    ```css
    .center {
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
    }
    ```

    Ahora `.center` solo controla lo que necesita controlar.

    ---

    __6. La diferencia__

    __Primera versión__

    ```css
    .center {
      max-width: 60ch;
      margin: 0 auto;
    }
    ```

    Esto equivale a:

    ```css
    .center {
      max-width: 60ch;

      margin-top: 0;
      margin-right: auto;
      margin-bottom: 0;
      margin-left: auto;
    }
    ```

    Estás modificando **los cuatro lados**.

    ---

    __Segunda versión__

    ```css
    .center {
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
    }
    ```

    Aquí solo modificas:

    ```text
    izquierda → auto
    derecha   → auto
    ```

    Y dejas intactos:

    ```text
    arriba
    abajo
    ```

    Esto permite que otro componente pueda controlar el espacio vertical.

    ---

    __7. ¿Por qué esto ayuda a la composición?__

    Esta palabra es clave:

    > **Composición**

    En *Every Layout*, la idea es construir pequeños componentes de layout que puedan combinarse.

    Por ejemplo:

    ```text
    Stack
      │
      ├── Header
      │
      ├── Center
      │
      ├── Article
      │
      └── Footer
    ```

    Cada componente tiene una responsabilidad.

    Podríamos imaginar:

    ```css
    .stack > * {
      margin-top: 1rem;
    }
    ```

    Y:

    ```css
    .center {
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
    }
    ```

    Ahora:

    * `Stack` controla el **espaciado vertical**.
    * `Center` controla el **ancho máximo y el centrado horizontal**.

    Cada uno hace su trabajo.

    ```text
            STACK
        ┌───────────────┐
        │               │
        │    CENTER     │ ← max-width
        │               │ ← margin-left/right auto
        │               │
        └───────────────┘
              ↑
        Stack controla
        espacio vertical
    ```

    No se pisan entre ellos.

    ---

    __8. La filosofía detrás de todo esto__

    Esta parte del libro te está enseñando algo más profundo que simplemente:

    > "Usa `margin-left: auto`."

    La verdadera enseñanza es:

    > **Un componente de CSS debe modificar únicamente las propiedades que necesita para cumplir su función.**

    Si creas un componente para centrar:

    ```css
    .center {
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
    }
    ```

    Su responsabilidad es:

    ```text
    CENTER
    ├── Limitar ancho
    └── Centrar horizontalmente
    ```

    No debería decidir:

    ```text
    CENTER
    ├── Limitar ancho
    ├── Centrar horizontalmente
    ├── Eliminar margen superior
    ├── Eliminar margen inferior
    ├── Cambiar colores
    ├── Cambiar tipografía
    └── Cambiar padding
    ```

    Eso lo haría demasiado invasivo.

    ---

    __La idea en una frase__

    **`margin: 0 auto` funciona, pero `margin-left: auto` y `margin-right: auto` son más precisos porque el componente `Center` solo hace lo que necesita: controlar el centrado horizontal, dejando que otros componentes controlen el espacio vertical.**

    Y esto conecta muchísimo con lo que venías estudiando de CSS: **cascada, herencia y composición**. La mentalidad que propone *Every Layout* es que en lugar de construir una página con reglas gigantes y específicas, construyas piezas pequeñas que puedan convivir sin destruir el trabajo de las demás.


## Medida

El `max-width` debería típicamente — como en el ejemplo de código anterior — establecerse en `ch`, ya que lograr una medida razonable es primordial. La sección *Axioms* detalla cómo establecer una medida razonable.

??? info "Explicacion"

    Claro. Esta parte es **corta**, pero tiene una idea fundamental: **el ancho de una columna de texto importa muchísimo para que el contenido sea cómodo de leer**.

    Vamos a desmontarla.

    ---

    __1. ¿Qué significa "Medida"?__

    Aquí `Measure` no significa "medir" en el sentido de:

    > "¿Cuánto mide este elemento?"

    En el contexto de *Every Layout*, **Measure** se refiere a la **longitud de una línea de texto**.

    Por ejemplo:

    ```text
    Esta es una línea de texto que tiene una longitud razonable
    y resulta cómoda de leer porque el ojo puede recorrerla
    sin tener que desplazarse demasiado de izquierda a derecha.
    ```

    Eso es la **medida** (*measure*) del texto.

    En tipografía y diseño editorial, se suele llamar también **longitud de línea**.

    ---

    __2. ¿Por qué es importante?__

    Imagina que tienes una pantalla muy grande.

    Si haces:

    ```css
    .article {
      width: 100%;
    }
    ```

    Tu texto podría ocupar prácticamente todo el ancho:

    ```text
    ┌─────────────────────────────────────────────────────────────────────────────┐
    │ Este es un párrafo muy largo que ocupa toda la pantalla y hace que tus ojos │
    │ tengan que recorrer una distancia enorme desde el principio de la línea    │
    │ hasta el final y luego regresar nuevamente al comienzo de la siguiente.     │
    └─────────────────────────────────────────────────────────────────────────────┘
    ```

    Esto puede ser incómodo de leer.

    El ojo tiene que hacer:

    ```text
    INICIO ────────────────────────────────→ FINAL
                                            ↓
    INICIO ←────────────────────────────────
    ```

    Cuanto más larga es la línea, más difícil puede resultar seguir el texto.

    ---

    __3. Por eso usamos `max-width`__

    En lugar de permitir que el texto crezca indefinidamente:

    ```css
    .article {
      width: 100%;
    }
    ```

    puedes decir:

    ```css
    .article {
      max-width: 60ch;
    }
    ```

    Esto significa:

    > "Aunque haya espacio disponible, este contenido no debe superar este ancho máximo."

    Visualmente:

    ```text
    Pantalla
    ┌─────────────────────────────────────────────────────────────┐
    │                                                             │
    │       ┌─────────────────────────────────────────┐           │
    │       │ Este es un párrafo con una longitud    │           │
    │       │ de línea razonable para facilitar la   │           │
    │       │ lectura del contenido.                 │           │
    │       └─────────────────────────────────────────┘           │
    │                                                             │
    └─────────────────────────────────────────────────────────────┘
    ```

    El contenido queda limitado.

    ---

    __4. ¿Por qué `ch`?__

    El autor dice:

    > "`max-width` debería típicamente establecerse en `ch`."

    Por ejemplo:

    ```css
    .center {
      max-width: 60ch;
    }
    ```

    `ch` es una unidad CSS relacionada con el ancho del carácter `0` de la fuente utilizada.

    Entonces:

    ```css
    max-width: 60ch;
    ```

    puede entenderse aproximadamente como:

    > "Permite que el contenido tenga una medida equivalente a unas 60 unidades basadas en el ancho del carácter `0`."

    No significa literalmente:

    > "Siempre habrá exactamente 60 caracteres por línea."

    Eso dependerá de la fuente y de los caracteres utilizados.

    Pero es una **aproximación útil para controlar la longitud de las líneas de texto**.

    ---

    __5. ¿Por qué no usar `px`?__

    Podrías hacer:

    ```css
    max-width: 600px;
    ```

    Pero el problema es que `px` representa una dimensión física/visual fija.

    En cambio:

    ```css
    max-width: 60ch;
    ```

    está relacionado con la tipografía.

    Esto tiene mucho sentido cuando estás diseñando **contenido textual**.

    Por ejemplo:

    ```css
    .article {
      max-width: 60ch;
    }
    ```

    Si cambias el tamaño de la fuente, la relación entre el texto y el ancho también cambia de manera más natural.

    Por eso `ch` es especialmente interesante para artículos, párrafos y contenido de lectura.

    ---

    __6. ¿Cómo se relaciona con el componente `Center`?__

    En la sección anterior teníamos:

    ```css
    .center {
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
    }
    ```

    Ahora podemos entender exactamente qué hace cada línea:

    ```css
    .center {
      max-width: 60ch;
      /* Limita la longitud de la línea */

      margin-left: auto;
      /* Centra horizontalmente */

      margin-right: auto;
      /* Centra horizontalmente */
    }
    ```

    Visualmente:

    ```text
    ┌──────────────────────────────────────────────────────────────┐
    │                                                              │
    │        ┌──────────────────────────────────────────┐          │
    │        │                                          │          │
    │        │  Este texto tiene una medida razonable. │          │
    │        │  Las líneas no se extienden demasiado.  │          │
    │        │                                          │          │
    │        └──────────────────────────────────────────┘          │
    │                                                              │
    └──────────────────────────────────────────────────────────────┘
    ```

    Tienes dos objetivos:

    ```text
                  CENTER
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
      max-width: 60ch    margin auto
          │                   │
          ▼                   ▼
      Controla la          Centra la
      longitud             columna
      de línea             horizontalmente
    ```

    ---

    __7. ¿Qué significa "una medida razonable"?__

    El texto dice:

    > "ya que lograr una medida razonable es primordial."

    Quiere decir que **la longitud de las líneas debe ser cómoda para la lectura**.

    No quieres esto:

    ```text
    Texto extremadamente largo ──────────────────────────────────────────────────→
    ```

    Pero tampoco algo demasiado estrecho:

    ```text
    Este
    es
    un
    texto
    muy
    estrecho
    y
    aburrido
    de
    leer.
    ```

    Buscas un punto intermedio:

    ```text
    Este es un texto con una longitud
    de línea razonable. El lector puede
    seguir fácilmente cada línea y pasar
    a la siguiente sin perderse.
    ```

    Por eso el autor habla de una **medida razonable**.

    ---

    __8. La relación con la sección `Axioms`__

    El texto dice:

    > "La sección *Axioms* detalla cómo establecer una medida razonable."

    Eso significa que en otra parte del libro explican con más detalle **qué longitud de línea es recomendable** y cómo establecerla.

    La idea general es:

    ```text
    Texto
      ↓
    Necesita una longitud de línea cómoda
      ↓
    Usamos max-width
      ↓
    Para texto, usamos frecuentemente ch
      ↓
    Ejemplo:
    max-width: 60ch
    ```

    ---

    __La idea clave que debes llevarte__

    Cuando leas:

    ```css
    max-width: 60ch;
    ```

    no pienses solamente:

    > "El elemento tiene un ancho máximo."

    Piensa:

    > **"Estoy controlando la medida de lectura para que las líneas de texto no sean demasiado largas."**

    Y cuando lo combinas con:

    ```css
    margin-left: auto;
    margin-right: auto;
    ```

    obtienes:

    > **Una columna de contenido que tiene una longitud de línea razonable y que además está centrada dentro del espacio disponible.**

    Por eso el patrón completo de `Center` tiene tanto sentido:

    ```css
    .center {
      max-width: 60ch;
      margin-inline: auto;
    }
    ```

    En esencia:

    **`max-width` cuida la lectura.**
    **`margin-inline: auto` cuida la posición.**

    Esa es la idea central de esta sección.


## Margen mínimo

En un contexto más estrecho que `60ch`, el contenido actualmente quedará pegado a cualquiera de los lados del elemento padre o viewport. En lugar de permitir que esto suceda, deberíamos asegurar un espacio *mínimo* a cada lado.

Necesito abordar esto de una manera que preserve el centrado y el ancho máximo. Como no podemos entrar en un cálculo con `auto` (como `calc(auto + 1rem)`), probablemente deberíamos delegar en `padding`.

![](centercontent.png)

Pero tengo que tener cuidado con el modelo de caja. Si, como se sugirió en *Boxes*, he configurado todos los elementos para adoptar `box-sizing: border-box`, cualquier `padding` agregado a mi `<center-l>` contribuirá al total de `60ch`. En otras palabras, agregar `padding` hará que el *contenido* de mi elemento sea más estrecho. Sin embargo, como se cubre en *Axioms*, CSS está diseñado para excepciones. Solo necesito sobrescribir con `box-sizing: content-box`, y permitir que el `padding` 'crezca hacia afuera' desde el umbral de contenido de `60ch`.

![](Borderbox.png)

Aquí hay una versión que preserva el `max-width` de `60ch`, pero asegura que haya, al menos, "márgenes" a cada lado (`var(--s1)` es el primer punto en la escala modular basada en propiedades personalizadas).

```css linenums="1"
.center {
  box-sizing: content-box;
  max-width: 60ch;
  margin-left: auto;
  margin-right: auto;
  padding-left: var(--s1);
  padding-right: var(--s1);
}
```

??? info "Explicacion"

    Esta parte es bastante importante porque aquí el autor está resolviendo un problema que aparece cuando usamos:

    ```css
    .center {
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
    }
    ```

    Hasta ahora todo parecía perfecto. Pero aparece un caso especial: **¿qué pasa cuando la pantalla es más estrecha que `60ch`?**

    Vamos paso a paso.

    ---

    __1. El problema: una pantalla pequeña__

    Tenemos:

    ```css
    .center {
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
    }
    ```

    En una pantalla grande:

    ```text
    ┌─────────────────────────────────────────────────────────┐
    │                                                         │
    │      ┌───────────────────────────────────────────┐      │
    │      │                                           │      │
    │      │              Contenido                    │      │
    │      │                                           │      │
    │      └───────────────────────────────────────────┘      │
    │                                                         │
    └─────────────────────────────────────────────────────────┘
    ```

    Perfecto.

    El contenido tiene un máximo de `60ch` y está centrado.

    Pero imagina ahora una pantalla pequeña:

    ```text
    ┌────────────────────────────┐
    │Contenido pegado al borde   │
    │y esto puede resultar       │
    │incómodo de leer.           │
    └────────────────────────────┘
    ```

    El problema es que `max-width: 60ch` significa:

    > "Nunca seas más ancho que `60ch`."

    Pero **no significa**:

    > "Deja siempre espacio a los lados."

    Por eso, cuando la pantalla es más pequeña que esos `60ch`, el elemento simplemente ocupa el espacio disponible:

    ```text
    ┌──────────────────────────┐
    │Contenido                 │
    │                          │
    │                          │
    └──────────────────────────┘
    ↑                          ↑
    pegado                     pegado
    ```

    No hay espacio entre el contenido y los bordes.

    ---

    __2. ¿Qué quiere el autor?__

    Quiere conseguir esto:

    ```text
    ┌──────────────────────────┐
    │                          │
    │  Contenido con espacio   │
    │  a ambos lados           │
    │                          │
    └──────────────────────────┘
    ```

    Es decir:

    > Incluso cuando la pantalla sea muy pequeña, quiero que exista un espacio mínimo entre el contenido y los bordes.

    Por ejemplo:

    ```text
    ┌──────────────────────────┐
    │  ┌────────────────────┐  │
    │  │                    │  │
    │  │     Contenido      │  │
    │  │                    │  │
    │  └────────────────────┘  │
    └──────────────────────────┘
      ↑                    ↑
    espacio mínimo     espacio mínimo
    ```

    ---

    __3. ¿Por qué no usar `margin`?__

    Podrías pensar:

    ```css
    margin-left: 1rem;
    margin-right: 1rem;
    ```

    Pero recuerda que queremos mantener el centrado automático:

    ```css
    margin-left: auto;
    margin-right: auto;
    ```

    No podemos hacer algo como:

    ```css
    margin-left: calc(auto + 1rem);
    ```

    Eso no funciona.

    CSS no permite hacer operaciones matemáticas con `auto` de esa forma.

    No puedes decir:

    ```text
    auto + 1rem
    ```

    porque `auto` no es un valor numérico.

    `auto` significa más bien:

    > "Navegador, calcula tú el valor según las reglas de layout."

    No es un número que puedas sumar.

    ---

    __4. Entonces usamos `padding`__

    Aquí entra:

    ```css
    padding-left: var(--s1);
    padding-right: var(--s1);
    ```

    La idea es:

    ```text
    ┌───────────────────────────────┐
    │ ← padding →  CONTENIDO  ← padding → │
    └───────────────────────────────┘
    ```

    El `padding` crea espacio **dentro del elemento**.

    Entonces el componente puede seguir teniendo:

    ```css
    margin-left: auto;
    margin-right: auto;
    ```

    para centrarse.

    Y además:

    ```css
    padding-left: var(--s1);
    padding-right: var(--s1);
    ```

    para evitar que el contenido toque los bordes.

    ---

    __5. Pero aparece un nuevo problema: `box-sizing`__

    Aquí está la parte más complicada.

    El autor dice:

    > "Tengo que tener cuidado con el modelo de caja."

    Esto se refiere a cómo CSS calcula el tamaño total de un elemento.

    Supongamos:

    ```css
    .center {
      max-width: 60ch;
      padding-left: 1rem;
      padding-right: 1rem;
    }
    ```

    Dependiendo de `box-sizing`, esos `padding` pueden afectar el tamaño que estás limitando con `max-width`.

    ---

    __6. `box-sizing: border-box`__

    Supongamos que anteriormente configuraste globalmente:

    ```css
    * {
      box-sizing: border-box;
    }
    ```

    Esto es muy común.

    Con:

    ```css
    box-sizing: border-box;
    ```

    el navegador interpreta:

    ```text
    width / max-width
            ↓
    ┌───────────────────────────────┐
    │ padding │ contenido │ padding │
    └───────────────────────────────┘
          todo entra en 60ch
    ```

    Es decir:

    ```text
    60ch
    ┌──────────────────────────────────┐
    │ padding │ contenido │ padding    │
    └──────────────────────────────────┘
    ```

    Los `60ch` representan **todo el ancho de la caja**, incluyendo el padding.

    Entonces si haces:

    ```css
    max-width: 60ch;
    padding-left: 1rem;
    padding-right: 1rem;
    ```

    el contenido real disponible será menor.

    Conceptualmente:

    ```text
    60ch TOTAL
    ┌────────────────────────────────┐
    │ 1rem │      CONTENIDO     │1rem│
    └────────────────────────────────┘
          ↑                      ↑
          padding              padding
    ```

    El contenido ya no puede utilizar los `60ch` completos.

    ---

    __7. ¿Qué quiere el autor?__

    El autor quiere esto:

    ```text
    60ch de contenido
    ┌────────────────────────────────────────┐
    │                                        │
    │             CONTENIDO                  │
    │                                        │
    └────────────────────────────────────────┘
    ← padding →                    ← padding →
    ```

    Es decir:

    > Quiero que `60ch` sea el ancho máximo del **contenido**, y que el padding se añada fuera de esos `60ch`.

    Visualmente:

    ```text
    ┌── padding ──┌──────────────────┐── padding ──┐
    │             │                  │              │
    │             │    60ch          │              │
    │             │    contenido     │              │
    │             │                  │              │
    │             └──────────────────┘              │
    └───────────────────────────────────────────────┘
    ```

    Para conseguirlo utiliza:

    ```css
    box-sizing: content-box;
    ```

    ---

    __8. `content-box`__

    Con:

    ```css
    box-sizing: content-box;
    ```

    el `max-width` se aplica al **contenido**, y el padding se agrega aparte.

    Entonces:

    ```css
    .center {
      box-sizing: content-box;
      max-width: 60ch;
      padding-left: var(--s1);
      padding-right: var(--s1);
    }
    ```

    Conceptualmente:

    ```text
              60ch
    ┌──────────────────────────────┐
    │                              │
    │          CONTENIDO           │
    │                              │
    └──────────────────────────────┘
    ↑                              ↑
    padding                        padding
    ←───────┐                ┌───────→
            │                │
            └────────────────┘
    ```

    La caja total será:

    ```text
    padding + 60ch + padding
    ```

    Por eso el autor dice que el `padding` puede:

    > "crecer hacia afuera" desde el umbral de contenido de `60ch`.

    ---

    __9. El CSS completo__

    El código final es:

    ```css
    .center {
      box-sizing: content-box;
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
      padding-left: var(--s1);
      padding-right: var(--s1);
    }
    ```

    Cada propiedad tiene una misión:

    __`box-sizing: content-box`__

    ```text
    60ch = solo contenido
    ```

    El padding queda fuera.

    ---

    __`max-width: 60ch`__

    ```text
    El contenido no crece más allá de 60ch.
    ```

    Controla la medida de lectura.

    ---

    __`margin-left: auto`__

    ```text
    Centra horizontalmente.
    ```

    ---

    __`margin-right: auto`__

    ```text
    Centra horizontalmente.
    ```

    ---

    __`padding-left: var(--s1)`__

    ```text
    Crea un espacio mínimo a la izquierda.
    ```

    ---

    __`padding-right: var(--s1)`__

    ```text
    Crea un espacio mínimo a la derecha.
    ```

    ---

    __10. ¿Qué significa `var(--s1)`?__

    En tu conversación anterior vimos que estás trabajando con una escala modular.

    Probablemente tienes algo como:

    ```css
    :root {
      --ratio: 1.3;

      --s0: 1rem;
      --s1: calc(var(--s0) * var(--ratio));
    }
    ```

    Entonces:

    ```css
    var(--s1)
    ```

    representa un espacio de tu escala modular.

    Por ejemplo, si:

    ```css
    --s0: 1rem;
    --ratio: 1.3;
    ```

    entonces:

    ```text
    --s1 = 1.3rem
    ```

    Así, el componente usa una distancia coherente con el resto de tu sistema de diseño.

    ---

    __11. El resultado final__

    El componente ahora tiene tres comportamientos:

    __Pantalla grande__

    ```text
    ┌────────────────────────────────────────────────────────────┐
    │                                                            │
    │       padding ┌──────────────────────────┐ padding         │
    │               │                          │                 │
    │               │      max 60ch            │                 │
    │               │                          │                 │
    │               └──────────────────────────┘                 │
    │                                                            │
    └────────────────────────────────────────────────────────────┘
    ```

    El contenido se mantiene limitado a `60ch` y centrado.

    ---

    __Pantalla pequeña__

    ```text
    ┌──────────────────────────────┐
    │  ┌────────────────────────┐  │
    │  │                        │  │
    │  │       Contenido        │  │
    │  │                        │  │
    │  └────────────────────────┘  │
    └──────────────────────────────┘
      ↑                        ↑
      padding mínimo
    ```

    El contenido ya no toca los bordes.

    ---

    __Pantalla extremadamente pequeña__

    El contenido se adapta:

    ```text
    ┌──────────────────┐
    │  ┌────────────┐  │
    │  │ Contenido  │  │
    │  │ que se     │  │
    │  │ adapta     │  │
    │  │ al espacio │  │
    │  └────────────┘  │
    └──────────────────┘
    ```

    El `max-width` funciona como **límite superior**, mientras que el `padding` garantiza un **espacio mínimo lateral**.

    ---

    __La idea más importante__

    Esta sección está construyendo poco a poco un componente `Center` realmente robusto:

    ```text
                      CENTER
                        │
          ┌─────────────┼──────────────┐
          │             │              │
          ▼             ▼              ▼
        max-width     margin auto     padding
          │             │              │
          ▼             ▼              ▼
      No crecer       Centrar       No pegarse
      demasiado      horizontal     a los bordes
          │                            │
          └──────────────┬─────────────┘
                          ▼
                Layout adaptable
    ```

    Y el detalle de `box-sizing` es el remate importante:

    > **Si quieres que `60ch` represente realmente la medida máxima del contenido, debes evitar que el padding se coma parte de esos `60ch`.**

    Por eso el autor hace esta excepción:

    ```css
    box-sizing: content-box;
    ```

    aunque globalmente pueda estar usando:

    ```css
    * {
      box-sizing: border-box;
    }
    ```

    Es un buen ejemplo de la filosofía que recorre *Every Layout*: **primero defines qué responsabilidad tiene tu componente; después escribes el CSS mínimo necesario para que funcione correctamente en distintos contextos, incluso cuando lo anidas dentro de otros layouts.**
      

## Centrado intrínseco

La solución de `margin: auto` es consagrada y perfectamente funcional. Pero hay una oportunidad usando el módulo de layout Flexbox para soportar el centrado *intrínseco*. Esto es, centrar elementos basándose en sus anchos naturales basados en el contenido. Considera el siguiente código.

```css linenums="1"
.center {
  box-sizing: content-box;
  max-width: 60ch;
  margin-left: auto;
  margin-right: auto;
  display: flex;
  flex-direction: column;
  align-items: center;
}
```

Dentro de un componente `<center-l>`, esperaría que los contenidos se organizaran verticalmente, como una columna, de ahí `flex-direction: column`. Esto me permite establecer `align-items: center`, que centrará cualquier hijo *independientemente* de su ancho.

El resultado es que cualquier elemento que sea más estrecho que `60ch` se centrará automáticamente dentro del área de `60ch`. Estos elementos pueden incluir elementos naturalmente pequeños como botones, o elementos con su propio `max-width` establecido bajo `60ch`.

![](pressme.png)

> Los párrafos ilustrados están sujetos a `align-items: center`, pero naturalmente ocupan todo el espacio disponible (son elementos `block` sin ancho establecido).

??? info "Explicacion"

    Aquí el autor está introduciendo una **mejora opcional** al componente `Center`. Hasta ahora habíamos aprendido a centrar **la propia columna**, pero ahora quiere que **los elementos que están dentro de esa columna también puedan centrarse según su propio ancho natural**.

    Esta diferencia es sutil, pero muy importante.

    ---

    __1. La solución que ya conocemos__

    Tenemos:

    ```css 
    .center {
      box-sizing: content-box;
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
    }
    ```

    Esto centra el elemento `.center`:

    ```text 
    ┌───────────────────────────────────────────────┐
    │                                               │
    │       ┌───────────────────────────────┐       │
    │       │                               │       │
    │       │        .center                │       │
    │       │                               │       │
    │       └───────────────────────────────┘       │
    │                                               │
    └───────────────────────────────────────────────┘
    ```

    Pero ahora imagina que dentro tienes:

    ```html 
    <center-l>
      <h1>Mi página</h1>

      <p>
        Este es un párrafo bastante largo...
      </p>

      <button>Presionar</button>
    </center-l>
    ```

    El `.center` tiene un máximo de `60ch`.

    Pero sus hijos tienen comportamientos diferentes.

    ---

    __2. El problema: los hijos no necesariamente tienen el mismo ancho__

    Un párrafo:

    ```html
    <p>
      Este es un texto largo...
    </p>
    ```

    Normalmente, como elemento `block`, ocupa todo el ancho disponible:

    ```text 
    ┌──────────────────────────────────┐
    │ Este es un párrafo largo...      │
    │ que ocupa todo el espacio.      │
    └──────────────────────────────────┘
    ```

    Pero un botón:

    ```html 
    <button>Presionar</button>
    ```

    tiene un ancho natural pequeño:

    ```text 
    ┌──────────────────────────────────┐
    │                                │
    │          ┌───────────┐         │
    │          │ Presionar │         │
    │          └───────────┘         │
    │                                │
    └──────────────────────────────────┘
    ```

    El botón tiene un ancho determinado por su contenido.

    Eso es lo que el autor llama:

    > **ancho intrínseco**

    Es decir, el tamaño natural que tiene un elemento según su contenido.

    ---

    __3. ¿Qué significa "centrado intrínseco"?__

    La idea es:

    > No quiero decirle al botón exactamente cuánto debe medir. Quiero que conserve su tamaño natural y simplemente quiero centrarlo.

    Por ejemplo:

    ```text 
    Ancho del Center: 60ch

    ┌───────────────────────────────────────┐
    │                                       │
    │             ┌──────────┐              │
    │             │ Presionar│              │
    │             └──────────┘              │
    │                                       │
    └───────────────────────────────────────┘
    ```

    El botón sigue midiendo lo que necesita.

    No hacemos:

    ```css 
    button {
      width: 200px;
    }
    ```

    Simplemente decimos:

    > "Botón, conserva tu tamaño natural, pero colócate en el centro."

    Eso es el **centrado intrínseco**.

    ---

    __4. Aquí entra Flexbox__

    El autor añade:

    ```css 
    display: flex;
    flex-direction: column;
    align-items: center;
    ```

    Ahora tenemos:

    ```css 
    .center {
      box-sizing: content-box;
      max-width: 60ch;

      margin-left: auto;
      margin-right: auto;

      display: flex;
      flex-direction: column;
      align-items: center;
    }
    ```

    Vamos a analizar las tres propiedades nuevas.

    ---

    __5. `display: flex`__

    Esto convierte `.center` en un **contenedor Flexbox**.

    ```css 
    display: flex;
    ```

    Los hijos directos de `.center` pasan a ser **flex items**.

    Por ejemplo:

    ```html 
    <div class="center">
      <h1>Título</h1>
      <p>Texto...</p>
      <button>Presionar</button>
    </div>
    ```

    Ahora:

    ```text 
    .center
      │
      ├── h1 ← flex item
      ├── p  ← flex item
      └── button ← flex item
    ```

    ---

    __6. `flex-direction: column`__

    Por defecto, Flexbox organiza los elementos horizontalmente:

    ```text 
    ┌───────┐ ┌───────┐ ┌───────┐
    │  h1   │ │   p   │ │button │
    └───────┘ └───────┘ └───────┘
    ```

    Pero nosotros queremos que el contenido se apile verticalmente:

    ```css 
    flex-direction: column;
    ```

    Entonces:

    ```text 
    ┌───────────────┐
    │      h1       │
    ├───────────────┤
    │      p        │
    ├───────────────┤
    │    button     │
    └───────────────┘
    ```

    Por eso el autor dice:

    > "Dentro de un componente `<center-l>`, esperaría que los contenidos se organizaran verticalmente, como una columna."

    Es decir:

    ```text 
    CENTER-L
      │
      ├── Título
      │
      ├── Párrafo
      │
      └── Botón
    ```

    ---

    __7. Ahora viene la propiedad importante: `align-items: center`__

    ```css 
    align-items: center;
    ```

    Esta propiedad centra los **hijos del contenedor Flexbox en el eje transversal**.

    Como tenemos:

    ```css 
    flex-direction: column;
    ```

    el eje principal es vertical:

    ```text 
          eje principal
                ↓
            ┌────────┐
            │   h1   │
            │        │
            │   p    │
            │        │
            │ button │
            └────────┘
    ```

    El eje transversal es horizontal:

    ```text 
    ←──────── eje transversal ────────→
    ```

    Por lo tanto:

    ```css 
    align-items: center;
    ```

    centra los hijos horizontalmente.

    ---

    __8. El resultado__

    Ahora tenemos:

    ```text 
    ┌──────────────────────────────────────────┐
    │                                          │
    │               ┌──────────┐               │
    │               │  Título  │               │
    │               └──────────┘               │
    │                                          │
    │        ┌───────────────────────┐         │
    │        │ Este es un párrafo    │         │
    │        │ que ocupa bastante    │         │
    │        │ espacio horizontal.  │         │
    │        └───────────────────────┘         │
    │                                          │
    │                 ┌────────┐               │
    │                 │ Presionar│              │
    │                 └────────┘               │
    │                                          │
    └──────────────────────────────────────────┘
    ```

    El botón:

    ```text 
    ┌────────┐
    │Presionar│
    └────────┘
    ```

    conserva su ancho natural.

    El párrafo, en cambio, sigue ocupando prácticamente todo el ancho disponible.

    ---

    __9. Aquí está el detalle que menciona la nota final__

    El autor dice:

    > "Los párrafos ilustrados están sujetos a `align-items: center`, pero naturalmente ocupan todo el espacio disponible (son elementos `block` sin ancho establecido)."

    Esto puede parecer contradictorio.

    Si tenemos:

    ```css 
    align-items: center;
    ```

    ¿por qué el párrafo no se hace pequeño y se centra?

    La respuesta está en el **ancho intrínseco del elemento**.

    Un `<p>` normalmente es un elemento `block`.

    Si no le especificas un `width`, su comportamiento natural es ocupar el espacio disponible.

    Por ejemplo:

    ```html 
    <p>
      Lorem ipsum dolor sit amet...
    </p>
    ```

    Su ancho natural dentro del contenedor será algo parecido a:

    ```text 
    ┌──────────────────────────────┐
    │                              │
    │  Párrafo ocupa todo esto     │
    │                              │
    └──────────────────────────────┘
    ```

    Entonces `align-items: center` intenta centrarlo, pero...

    ```text 
    Ancho disponible: 60ch
    Ancho del párrafo: 60ch

    60ch - 60ch = 0
    ```

    No existe espacio sobrante para moverlo.

    Por eso parece que no se ha centrado.

    **Ya está ocupando todo el ancho.**

    ---

    __10. Pero un botón es diferente__

    Un botón:

    ```html 
    <button>Presionar</button>
    ```

    podría tener:

    ```text
    Ancho disponible: 60ch
    Ancho del botón: 10ch
    ```

    Entonces:

    ```text 
    60ch - 10ch = 50ch
    ```

    Hay espacio sobrante.

    `align-items: center` distribuye ese espacio:

    ```text 
    25ch       10ch       25ch
    ←────→ ┌──────────┐ ←────→
          │ Presionar│
          └──────────┘
    ```

    Resultado:

    ```text
    ┌───────────────────────────────────────┐
    │                                       │
    │             ┌───────────┐             │
    │             │ Presionar  │             │
    │             └───────────┘             │
    │                                       │
    └───────────────────────────────────────┘
    ```

    Eso es exactamente lo que el autor quiere conseguir.

    ---

    __11. ¿Qué pasa con un elemento que tiene su propio `max-width`?__

    Supongamos:

    ```html 
    <center-l>
      <p class="narrow">
        Texto...
      </p>

      <button>Presionar</button>
    </center-l>
    ```

    Y:

    ```css
    .narrow {
      max-width: 40ch;
    }
    ```

    El `.center` tiene:

    ```text
    max-width: 60ch
    ```

    Pero el párrafo tiene:

    ```text 
    max-width: 40ch
    ```

    Ahora el párrafo puede ocupar solo `40ch`:

    ```text 
                60ch
    ┌────────────────────────────────────────┐
    │                                        │
    │       ┌────────────────────────┐       │
    │       │                        │       │
    │       │       40ch             │       │
    │       │       contenido        │       │
    │       │                        │       │
    │       └────────────────────────┘       │
    │                                        │
    └────────────────────────────────────────┘
    ```

    Y `align-items: center` lo centra.

    ---

    __12. Entonces, ¿qué problema está resolviendo exactamente?__

    Sin Flexbox:

    ```css 
    .center {
      max-width: 60ch;
      margin-inline: auto;
    }
    ```

    Esto centra **la columna `.center`**.

    Pero no necesariamente centra los elementos más pequeños que están dentro.

    Con Flexbox:

    ```css 
    .center {
      max-width: 60ch;
      margin-inline: auto;

      display: flex;
      flex-direction: column;
      align-items: center;
    }
    ```

    Ahora tienes dos niveles de centrado:

    ```text 
                    viewport
                        │
                        ▼
                  CENTRADO 1
                        │
                        ▼
                    .center
                    max 60ch
                        │
                        ▼
                  CENTRADO 2
                        │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
            Título    Párrafo     Botón
              │          │          │
              └──────────┼──────────┘
                        │
                align-items: center
    ```

    El primer centrado:

    ```css 
    margin-inline: auto;
    ```

    centra la **columna `.center` dentro de su padre**.

    El segundo:

    ```css 
    align-items: center;
    ```

    centra los **hijos más estrechos dentro de esa columna**.

    ---

    __13. La idea más importante__

    Hay que distinguir entre:

    __Centrar el contenedor__

    ```css 
    margin-inline: auto;
    ```

    ```text 
    Viewport
    ┌───────────────────────────────────────┐
    │       ┌─────────────────────┐         │
    │       │      .center        │         │
    │       └─────────────────────┘         │
    └───────────────────────────────────────┘
    ```

    ---

    __Centrar los hijos__

    ```css 
    align-items: center;
    ```

    ```text 
    .center
    ┌───────────────────────────────────────┐
    │                                       │
    │          ┌───────────────┐            │
    │          │    button     │            │
    │          └───────────────┘            │
    │                                       │
    └───────────────────────────────────────┘
    ```

    ---

    __En resumen__

    El CSS completo:

    ```css 
    .center {
      box-sizing: content-box;
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    ```

    hace esto:

    ```text 
                        CENTER
                          │
                          ▼
                  max-width: 60ch
                          │
                          ▼
                ┌─────────────────┐
                │                 │
                │  ┌───────────┐  │ ← Elemento pequeño
                │  │  Button   │  │   centrado
                │  └───────────┘  │
                │                 │
                │  ┌────────────┐ │ ← Elemento con max-width
                │  │  Texto     │ │   propio, centrado
                │  └────────────┘ │
                │                 │
                │  Párrafo        │ ← Elemento block que
                │  ocupa todo     │   naturalmente ocupa
                │  el ancho       │   todo el espacio
                │                 │
                └─────────────────┘
    ```

    La palabra clave aquí es **intrínseco**: el componente no obliga a todos los hijos a tener un ancho determinado. **Cada hijo conserva su tamaño natural o sus propias restricciones, y Flexbox centra aquellos que son más estrechos que el espacio disponible.**

    Y aquí aparece una de las grandes ventajas de CSS: en lugar de decirle al botón *"mide 200px y ponte aquí"*, le dices al sistema de layout *"organiza estos elementos y céntralos según sus propias dimensiones"*. El navegador hace el trabajo pesado.


## ⚠ Accesibilidad

Ten en cuenta que, cada vez que mueves contenido lejos del borde izquierdo (en una dirección de escritura de izquierda a derecha), hay un posible problema de accesibilidad. Cuando un usuario ha hecho zoom en la interfaz, es posible que el contenido centrado se haya movido fuera del viewport. Puede que nunca se den cuenta de que está ahí.

Mientras tu interfaz sea flexible y responsiva, y no se establezca un ancho fijo en el contenedor, el contenido centrado debería ser visible en la mayoría de las circunstancias.

??? info "Explicacion"

    Sí. Esta advertencia es importante porque demuestra que **un layout que visualmente se ve bien puede tener problemas cuando el usuario modifica la forma de visualizar la página**.

    Vamos paso a paso.

    ---

    __1. ¿Cuál es el problema?__

    El autor dice:

    > "cada vez que mueves contenido lejos del borde izquierdo [...] hay un posible problema de accesibilidad."

    Esto se refiere principalmente a contenido **centrado horizontalmente**.

    Por ejemplo:

    ```css 
    .center {
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
    }
    ```

    En una pantalla normal:

    ```text
    ┌───────────────────────────────────────────────┐
    │                                               │
    │        ┌─────────────────────────────┐        │
    │        │         CONTENIDO           │        │
    │        └─────────────────────────────┘        │
    │                                               │
    └───────────────────────────────────────────────┘
    ```

    Todo perfecto.

    Pero el usuario puede hacer **zoom**.

    Por ejemplo, en el navegador:

    ```text 
    Ctrl + +
    ```

    o:

    ```text 
    Ctrl + rueda del mouse
    ```

    Y ahora la página puede comportarse de manera diferente.

    ---

    __2. ¿Qué ocurre cuando hacemos zoom?__

    Supongamos que tu página tiene un viewport de:

    ```text
    1000px de ancho
    ```

    Y tienes un contenido centrado:

    ```text 
    ┌──────────────────────────────────────────┐
    │                                          │
    │        ┌──────────────────────┐          │
    │        │      Contenido       │          │
    │        └──────────────────────┘          │
    │                                          │
    └──────────────────────────────────────────┘
    ```

    Ahora el usuario aumenta el zoom.

    El contenido visualmente se hace más grande.

    Puede ocurrir algo así:

    ```text 
                contenido
          ┌───────────────────┐
          │                   │
          │                   │
          └───────────────────┘
                ↑
            fuera del viewport
    ```

    Si el navegador mantiene el contenido centrado, parte del elemento puede terminar fuera del área visible.

    El problema es especialmente delicado si el contenido se desplaza **hacia el lado izquierdo** y queda fuera de la zona que el usuario puede ver.

    ---

    __3. ¿Por qué puede ser un problema de accesibilidad?__

    Imagina que tienes un botón:

    ```html 
    <button>Continuar</button>
    ```

    Normalmente:

    ```text 
    ┌──────────────────────────────┐
    │                              │
    │         [ Continuar ]        │
    │                              │
    └──────────────────────────────┘
    ```

    El usuario hace mucho zoom.

    Ahora el botón podría quedar fuera del viewport:

    ```text 
    ← Fuera de pantalla

    [ Continuar ] │ ┌───────────────────────┐
                  │ │                       │
                  │ │   parte visible       │
                  │ │                       │
                  │ └───────────────────────┘
    ```

    El usuario no necesariamente sabe que existe contenido fuera de la pantalla.

    Podría pensar:

    > "No hay ningún botón para continuar."

    Cuando en realidad sí existe, pero está fuera del área visible.

    Por eso el autor dice:

    > "Puede que nunca se den cuenta de que está ahí."

    ---

    __4. Esto es especialmente importante para personas que necesitan zoom__

    No todo el mundo navega con el zoom al 100%.

    Algunos usuarios aumentan el zoom porque:

    * tienen dificultades visuales;
    * necesitan texto más grande;
    * usan una pantalla pequeña;
    * tienen una discapacidad visual;
    * simplemente prefieren interfaces con mayor escala.

    Por eso una interfaz accesible debe funcionar correctamente también con zoom.

    La accesibilidad no significa únicamente:

    > "Que funcione con un lector de pantalla."

    También incluye:

    > "Que el contenido siga siendo usable cuando el usuario cambia el tamaño de visualización."

    ---

    __5. Entonces, ¿centrar contenido es malo?__

    No.

    El autor **no está diciendo que nunca debas centrar contenido**.

    Está diciendo:

    > "Ten cuidado cuando centras elementos, porque existe un caso extremo donde el contenido podría quedar fuera del viewport."

    La solución no es:

    ```text
    ❌ Nunca centres nada
    ```

    La solución es:

    ```text
    ✅ Centra de forma flexible y responsiva.
    ```

    ---

    __6. Aquí entra la parte más importante del texto__

    El autor dice:

    > "Mientras tu interfaz sea flexible y responsiva, y no se establezca un ancho fijo en el contenedor..."

    Esto significa que debes evitar cosas como:

    ```css 
    .center {
      width: 1000px;
    }
    ```

    Porque estás diciendo:

    > "Este elemento siempre mide 1000px."

    En una pantalla pequeña:

    ```text 
    Viewport: 400px

    ┌──────────────────────┐
    │  ┌───────────────────┼───────────────
    │  │                   │
    │  │     1000px        │
    │  │                   │
    │  └───────────────────┼───────────────
    └──────────────────────┘
    ```

    El elemento es mucho más ancho que la pantalla.

    Esto puede provocar problemas de desplazamiento horizontal y contenido inaccesible.

    ---

    __7. Mejor usar `max-width`__

    Por eso es mejor:

    ```css 
    .center {
      max-width: 60ch;
      margin-inline: auto;
    }
    ```

    La diferencia es fundamental.

    __`width`__

    ```css
    width: 60ch;
    ```

    Dice:

    > "Mide 60ch."

    ---

    __`max-width`__

    ```css
    max-width: 60ch;
    ```

    Dice:

    > "Como máximo mide 60ch, pero si no hay suficiente espacio, puedes hacerte más pequeño."

    Es decir:

    ```text 
    Pantalla grande
          ↓
        60ch

    Pantalla pequeña
          ↓
      menos de 60ch
    ```

    El elemento se adapta.

    ---

    __8. El patrón completo que has aprendido__

    Hasta ahora, el componente `Center` ha ido evolucionando:

    __Primera versión__

    ```css 
    .center {
      max-width: 60ch;
      margin-inline: auto;
    }
    ```

    Problema:

    ```text 
    En pantallas pequeñas:
    contenido pegado a los bordes
    ```

    ---

    __Segunda versión__

    ```css
    .center {
      box-sizing: content-box;
      max-width: 60ch;
      margin-inline: auto;
      padding-inline: var(--s1);
    }
    ```

    Ahora:

    ```text 
    Pantalla pequeña

    ┌──────────────────────────┐
    │  Contenido con espacio   │
    │  mínimo a los lados      │
    └──────────────────────────┘
    ```

    ---

    __Tercera versión__

    ```css 
    .center {
      box-sizing: content-box;
      max-width: 60ch;
      margin-inline: auto;
      padding-inline: var(--s1);

      display: flex;
      flex-direction: column;
      align-items: center;
    }
    ```

    Ahora:

    ```text 
    ┌──────────────────────────────────┐
    │                                  │
    │        ┌──────────────┐          │
    │        │    Button    │          │
    │        └──────────────┘          │
    │                                  │
    │  Este es un párrafo que ocupa    │
    │  todo el espacio disponible.     │
    │                                  │
    └──────────────────────────────────┘
    ```

    Y además el componente es flexible.

    ---

    __9. La idea final__

    El autor básicamente está diciendo:

    > **Centrar contenido puede ser seguro siempre que no construyas un layout rígido.**

    Un buen componente `Center` debería:

    ```text 
                        CENTER
                          │
            ┌──────────────┼──────────────┐
            │              │              │
            ▼              ▼              ▼
        max-width       padding       flexbox
            │              │              │
            ▼              ▼              ▼
      Limita el       Evita que      Centra hijos
      crecimiento     toque bordes   pequeños
            │              │              │
            └──────────────┼──────────────┘
                          ▼
                  Layout flexible
                          │
                          ▼
                  Mejor accesibilidad
    ```

    La clave es **no usar un ancho fijo**:

    ```css 
    width: 1000px; /* ❌ rígido */
    ```

    y preferir:

    ```css 
    max-width: 60ch; /* ✅ flexible */
    ```

    El navegador podrá reducir el tamaño del componente cuando el espacio disponible sea menor.

    ---

    __Una frase para recordar__

    > **El contenido centrado no es el problema; el problema es el contenido centrado dentro de un layout rígido que no sabe adaptarse.**

    Y esta es una idea que se repite muchísimo en *Every Layout*: **no diseñes pensando únicamente en el viewport perfecto que tienes delante; diseña para las condiciones cambiantes que el navegador y el usuario pueden imponer.**


## Casos de uso

Siempre que desees centrar algo horizontalmente, el `Center` es tu amigo. En el siguiente ejemplo, estoy emulando el layout básico para el sitio de documentación de *Every Layout* (que puedes estar viendo ahora, a menos que estés leyendo el EPUB). Comprende un `Sidebar`, con un `Center` al lado derecho. Los elementos están separados verticalmente tanto en la barra lateral como en el `Center` usando `Stack`. Un `Center` anidado con el booleano `intrinsic` aplicado centra el botón 'Launch demo'.

(Puede que necesites abrirlo en su propia ventana (de escritorio) para ver el centrado en acción.) 

??? example "Demo"

    [This interactive demo is only available on the Every Layout site ↗.](https://every-layout.dev/demos/center-documentation-layout/)

??? info "Explicacion"

    Aquí el autor está mostrando **cómo se combinan los layouts que ha explicado hasta ahora**. Es una especie de ejemplo práctico de cómo construir una página real usando pequeños componentes independientes.

    La idea central es que `Center` no es un componente que usas una sola vez. Puedes combinarlo con `Sidebar`, `Stack` y otros layouts.

    ---

    __1. El escenario__

    El autor dice que está simulando el layout básico de la documentación de *Every Layout*.

    Visualmente sería algo así:

    ```text
    ┌──────────────────────────────────────────────────────────────┐
    │                                                              │
    │  ┌──────────────┐    ┌──────────────────────────────────┐   │
    │  │              │    │                                  │   │
    │  │   SIDEBAR    │    │             CENTER               │   │
    │  │              │    │                                  │   │
    │  │  Introducción│    │    Título                        │   │
    │  │              │    │                                  │   │
    │  │  Layout      │    │    Contenido                     │   │
    │  │              │    │                                  │   │
    │  │  Center      │    │                                  │   │
    │  │              │    │         [Launch demo]            │   │
    │  │  Stack       │    │                                  │   │
    │  │              │    │                                  │   │
    │  └──────────────┘    └──────────────────────────────────┘   │
    │                                                              │
    └──────────────────────────────────────────────────────────────┘
    ```

    Tenemos dos grandes zonas:

    ```text 
    Página
    │
    ├── Sidebar
    │
    └── Center
    ```

    La barra lateral está a la izquierda.

    El contenido principal está a la derecha.

    ---

    __2. `Sidebar`__

    El `Sidebar` se encarga de crear una estructura como:

    ```text
    ┌──────────────┐
    │ Sidebar      │
    │              │
    │ Elemento 1   │
    │              │
    │ Elemento 2   │
    │              │
    │ Elemento 3   │
    └──────────────┘
    ```

    Normalmente tendrías algo como:

    ```html 
    <aside>
      ...
    </aside>

    <main>
      ...
    </main>
    ```

    El layout `Sidebar` se ocupa de colocar ambos elementos uno al lado del otro:

    ```text 
    ┌──────────┬─────────────────────────┐
    │          │                         │
    │ Sidebar  │       Main              │
    │          │                         │
    └──────────┴─────────────────────────┘
    ```

    ---

    __3. `Center` está al lado derecho__

    El autor dice:

    > "Comprende un `Sidebar`, con un `Center` al lado derecho."

    Es decir, la composición sería algo parecido a:

    ```text 
            Sidebar Layout
                  │
          ┌──────┴──────┐
          │             │
          ▼             ▼
        Sidebar        Center
    ```

    El `Center` controla el contenido principal.

    Por ejemplo:

    ```html 
    <aside>
      <!-- navegación -->
    </aside>

    <main class="center">
      <!-- contenido -->
    </main>
    ```

    El `Center` podría limitar la medida de lectura:

    ```css 
    .center {
      max-width: 60ch;
      margin-inline: auto;
    }
    ```

    Entonces tienes:

    ```text 
    ┌─────────────┬─────────────────────────────────────┐
    │             │                                     │
    │  Sidebar    │      ┌───────────────────────┐      │
    │             │      │                       │      │
    │             │      │     Main Content      │      │
    │             │      │                       │      │
    │             │      └───────────────────────┘      │
    │             │                                     │
    └─────────────┴─────────────────────────────────────┘
    ```

    ---

    __4. `Stack` aparece otra vez__

    El autor dice:

    > "Los elementos están separados verticalmente tanto en la barra lateral como en el `Center` usando `Stack`."

    Esto significa que `Stack` se ocupa de crear el espacio vertical entre los elementos.

    Por ejemplo:

    ```text 
    Sidebar

    Elemento 1
        ↓
      espacio
        ↓
    Elemento 2
        ↓
      espacio
        ↓
    Elemento 3
    ```

    En lugar de escribir manualmente:

    ```css 
    .item1 {
      margin-bottom: 1rem;
    }

    .item2 {
      margin-bottom: 1rem;
    }

    .item3 {
      margin-bottom: 1rem;
    }
    ```

    tienes un componente que gestiona el espacio:

    ```text 
    Stack
      │
      ├── Elemento 1
      │
      ├── Elemento 2
      │
      └── Elemento 3
    ```

    La ventaja es que `Stack` puede encargarse de la relación entre los elementos sin que cada elemento tenga que conocer a sus hermanos.

    ---

    __5. Entonces tenemos una composición de layouts__

    La estructura conceptual podría ser:

    ```text 
    Página
    │
    ├── Sidebar
    │    │
    │    └── Stack
    │         ├── Link
    │         ├── Link
    │         └── Link
    │
    └── Center
        │
        └── Stack
              ├── Heading
              ├── Paragraph
              └── Center (intrinsic)
                        │
                        └── Button
    ```

    Mira qué interesante.

    El autor está combinando componentes:

    ```text 
    Sidebar
        +
    Center
        +
    Stack
        +
    Center intrinsic
    ```

    Y cada uno tiene una responsabilidad diferente.

    ---

    __6. El `Center` anidado__

    Esta es probablemente la parte más interesante.

    El autor dice:

    > "Un `Center` anidado con el booleano `intrinsic` aplicado centra el botón 'Launch demo'."

    Tenemos algo como:

    ```html 
    <center-l intrinsic>
      <button>Launch demo</button>
    </center-l>
    ```

    El `Center` externo controla:

    ```text 
    Ancho máximo del contenido
    ```

    Mientras que el `Center` interno con `intrinsic` controla:

    ```text 
    Centrado del botón según su ancho natural
    ```

    Visualmente:

    ```text 
    CENTER EXTERNO
    ┌─────────────────────────────────────────┐
    │                                         │
    │  Texto de documentación                 │
    │  Texto de documentación                 │
    │                                         │
    │              ┌──────────────┐           │
    │              │ Launch demo  │           │
    │              └──────────────┘           │
    │                                         │
    └─────────────────────────────────────────┘
    ```

    El botón no necesita ocupar todo el ancho.

    Tiene su tamaño natural:

    ```text 
    ┌──────────────┐
    │ Launch demo  │
    └──────────────┘
    ```

    Y el `Center` con `intrinsic` lo centra.

    ---

    __7. ¿Por qué `intrinsic`?__

    Recuerda el CSS que vimos:

    ```css 
    .center {
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    ```

    Con `intrinsic`, el componente aplica esta lógica.

    Entonces:

    ```text 
                  .center
    ┌─────────────────────────────────────────┐
    │                                         │
    │          ┌────────────────┐             │
    │          │ Launch demo    │             │
    │          └────────────────┘             │
    │                                         │
    └─────────────────────────────────────────┘
    ```

    El botón conserva su ancho natural y se centra.

    Sin el centrado intrínseco, el botón podría ocupar el ancho disponible dependiendo del contexto y las reglas del layout.

    ---

    __8. Lo interesante: puedes anidar componentes__

    Esto es exactamente lo que el autor quiere demostrar.

    No estás construyendo toda la página con un CSS gigantesco:

    ```css 
    /* 500 líneas de CSS específicas para esta página */
    ```

    Estás componiendo pequeñas piezas:

    ```text 
                    Página
                      │
              ┌────────┴────────┐
              │                 │
          Sidebar            Main
              │                 │
            Stack             Center
              │                 │
          enlaces            Stack
                                │
                        ┌───────┼───────┐
                        │       │       │
                      título  texto   Center
                                        │
                                      intrinsic
                                        │
                                      botón
    ```

    Cada layout resuelve un problema concreto.

    ```text 
    Sidebar
    → coloca contenido lateralmente

    Stack
    → separa elementos verticalmente

    Center
    → limita y centra una columna

    Center + intrinsic
    → centra elementos según su tamaño natural
    ```

    ---

    __9. Esto es lo que *Every Layout* llama composición__

    La filosofía es muy parecida a construir con piezas de LEGO.

    En lugar de diseñar:

    ```text 
    "Esta página específica debe tener exactamente
    este margen de 23px aquí y 37px allá."
    ```

    piensas:

    ```text 
    Necesito:
    - una barra lateral
    - una columna de contenido
    - separación vertical
    - un botón centrado
    ```

    Entonces compones:

    ```text 
    Sidebar
      +
    Center
      +
    Stack
      +
    Center intrinsic
    ```

    Y obtienes la página.

    ---

    __La idea principal de esta sección__

    El autor está mostrando que `Center` no es solamente:

    > "Un CSS para centrar una cosa."

    Es un **componente de layout reutilizable** que puede combinarse con otros:

    ```text 
              Sidebar
                │
          ┌─────┴─────┐
          │           │
        Stack       Center
          │           │
          │         Stack
          │           │
          │        Center
          │        intrinsic
          │           │
          │         Button
    ```

    Y esa es una de las ideas más importantes de *Every Layout* hasta este punto:

    > **No construyas cada página desde cero. Construye pequeñas reglas de layout que hagan una sola cosa bien y después combínalas.**

    En este ejemplo, `Center` se encarga de la **anchura y centrado**, `Stack` del **espaciado vertical**, y `Sidebar` de la **distribución lateral**. Cada componente conoce su responsabilidad y no intenta controlar las demás. Esa separación es lo que permite que el sistema sea flexible y que los componentes puedan anidarse sin convertirse en una maraña de CSS.
    
## El generador

Usa esta herramienta para generar CSS y HTML básicos de Center.

La herramienta generadora de código solo está disponible en el [*sitio de documentación adjunto* ↗.](https://every-layout.dev/layouts/center/#the-generator) Aquí está la solución básica, con comentarios (omitiendo el código de centrado intrínseco):

**CSS**

```css linenums="1"
.center {
  /* ↓ Elimina el padding del cálculo del ancho */
  box-sizing: content-box;
  /* ↓ El ancho máximo es la medida máxima */
  max-width: 60ch;
  /* ↓ Solo afecta los márgenes horizontales */
  margin-left: auto;
  margin-right: auto;
  /* ↓ Aplica el espacio horizontal mínimo */
  padding-left: var(--s1);
  padding-right: var(--s1);
}
```

**HTML**

```html linenums="1"
<div class="center">
  <!-- contenido centrado -->
</div>
```

??? info "Explicacion"

    Aquí el autor simplemente está mostrando **la implementación básica y definitiva del componente `Center`** que ha ido construyendo paso a paso durante toda la explicación.

    Vamos a entender cada parte.

    ---

    __1. El CSS completo__

    ```css 
    .center {
      box-sizing: content-box;
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
      padding-left: var(--s1);
      padding-right: var(--s1);
    }
    ```

    Este componente tiene **tres responsabilidades principales**:

    ```text 
    .center
      │
      ├── max-width
      │      ↓
      │   Limitar la medida
      │
      ├── margin-left/right: auto
      │      ↓
      │   Centrar horizontalmente
      │
      └── padding-left/right
              ↓
          Crear espacio mínimo
          en pantallas pequeñas
    ```

    Y además:

    ```css 
    box-sizing: content-box;
    ```

    garantiza que el `padding` no reduzca la medida máxima de `60ch`.

    ---

    __2. `box-sizing: content-box`__

    ```css id="s1pz9w"
    box-sizing: content-box;
    ```

    Esta propiedad es necesaria porque el autor quiere que:

    ```text 
    60ch = ancho máximo del contenido
    ```

    Y que el `padding` se agregue fuera.

    Conceptualmente:

    ```text 
          padding           padding
            ↓                 ↓
        ┌───────┌────────────────────┐───────┐
        │       │                    │       │
        │       │     60ch máximo    │       │
        │       │     de contenido   │       │
        │       │                    │       │
        └───────└────────────────────┘───────┘
    ```

    Si estuvieras usando `border-box`, el `padding` formaría parte de esos `60ch`.

    ---

    __3. `max-width: 60ch`__

    ```css 
    max-width: 60ch;
    ```

    Esto establece la **medida máxima del contenido**.

    La idea es evitar líneas de texto excesivamente largas.

    En una pantalla grande:

    ```text 
    ┌──────────────────────────────────────────────────────────┐
    │                                                          │
    │      ┌──────────────────────────────────────────┐        │
    │      │                                          │        │
    │      │            Contenido ≤ 60ch              │        │
    │      │                                          │        │
    │      └──────────────────────────────────────────┘        │
    │                                                          │
    └──────────────────────────────────────────────────────────┘
    ```

    La columna no seguirá creciendo indefinidamente.

    ---

    __4. `margin-left: auto`__

    ```css 
    margin-left: auto;
    ```

    Le dice al navegador:

    > "Calcula automáticamente el espacio que debe quedar a la izquierda."

    ---

    __5. `margin-right: auto`__

    ```css 
    margin-right: auto;
    ```

    Lo mismo para la derecha.

    Juntas:

    ```css 
    margin-left: auto;
    margin-right: auto;
    ```

    producen:

    ```text 
    ┌──────────────────────────────────────────┐
    │                                          │
    │       ┌──────────────────────────┐       │
    │       │                          │       │
    │       │         .center          │       │
    │       │                          │       │
    │       └──────────────────────────┘       │
    │                                          │
    └──────────────────────────────────────────┘
    ```

    El elemento queda centrado.

    ---

    __6. `padding-left: var(--s1)`__

    ```css 
    padding-left: var(--s1);
    ```

    Crea un espacio mínimo a la izquierda.

    ---

    __7. `padding-right: var(--s1)`__

    ```css 
    padding-right: var(--s1);
    ```

    Crea un espacio mínimo a la derecha.

    Esto evita que en pantallas estrechas el contenido toque directamente los bordes.

    Por ejemplo:

    ```text 
    Sin padding:

    ┌──────────────────────┐
    │Contenido pegado      │
    │al borde.             │
    └──────────────────────┘
    ```

    Con padding:

    ```text 
    ┌──────────────────────┐
    │  Contenido con       │
    │  espacio lateral.    │
    └──────────────────────┘
    ```

    ---

    __8. El HTML__

    El HTML es extremadamente simple:

    ```html 
    <div class="center">
      <!-- contenido centrado -->
    </div>
    ```

    Por ejemplo:

    ```html 
    <div class="center">
      <h1>Mi artículo</h1>

      <p>
        Este es el contenido de mi página.
      </p>

      <p>
        Otro párrafo de información.
      </p>
    </div>
    ```

    El CSS se encarga de todo:

    ```text 
                    viewport
    ┌──────────────────────────────────────────────┐
    │                                              │
    │       ┌──────────────────────────────┐       │
    │       │ Mi artículo                  │       │
    │       │                              │       │
    │       │ Este es el contenido de mi   │       │
    │       │ página.                      │       │
    │       │                              │       │
    │       │ Otro párrafo de información. │       │
    │       └──────────────────────────────┘       │
    │                                              │
    └──────────────────────────────────────────────┘
    ```

    ---

    __9. ¿Por qué el autor dice "solución básica"?__

    Porque esta es la versión que **solo resuelve el centrado de la columna**.

    No incluye la variante que vimos antes:

    ```css 
    display: flex;
    flex-direction: column;
    align-items: center;
    ```

    Por lo tanto, esta versión:

    ```css
    .center {
      box-sizing: content-box;
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
      padding-left: var(--s1);
      padding-right: var(--s1);
    }
    ```

    se encarga de:

    ```text 
    1. Limitar el ancho del contenido
    2. Centrar la columna horizontalmente
    3. Mantener espacio mínimo a los lados
    4. Funcionar de forma responsiva
    ```

    Pero **no centra automáticamente los elementos pequeños que estén dentro**.

    Para eso necesitas la variante con `intrinsic` y Flexbox.

    ---

    __10. Si lo conectamos con todo lo que hemos visto__

    El componente `Center` ha evolucionado así:

    ```text 
    margin: 0 auto
          │
          ▼
    Centra la columna
          │
          ▼
    margin-left/right: auto
          │
          ▼
    No modifica márgenes verticales
          │
          ▼
    max-width: 60ch
          │
          ▼
    Controla la medida de lectura
          │
          ▼
    padding-inline: var(--s1)
          │
          ▼
    Evita que el contenido toque los bordes
          │
          ▼
    box-sizing: content-box
          │
          ▼
    El padding no reduce los 60ch
    ```

    Y finalmente obtenemos:

    ```css 
    .center {
      box-sizing: content-box;
      max-width: 60ch;
      margin-left: auto;
      margin-right: auto;
      padding-left: var(--s1);
      padding-right: var(--s1);
    }
    ```

    __En una sola frase:__

    > **`Center` crea una columna que nunca supera una medida de lectura razonable (`60ch`), se centra horizontalmente cuando hay espacio, y mantiene una separación mínima de los bordes cuando la pantalla se hace pequeña.**

    Eso es todo el patrón básico. El resto del capítulo ha sido, en esencia, ir descubriendo **por qué cada línea existe y qué problema específico resuelve**.

## El componente

Una implementación de elemento personalizado del `Center` está disponible para descargar ↗.

**API de Props**

Las siguientes props (atributos) harán que el componente se renderice nuevamente cuando se alteren. Pueden ser alterados a mano — en las herramientas de desarrollo del navegador — o como sujetos del estado de la aplicación heredada.

| Nombre | Tipo | Default | Descripción |
|---|---|---|---|
| `max` | string | `"var(--measure)"` | Un valor CSS de `max-width` |
| `andText` | boolean | `false` | Centrar el texto también (`text-align: center`) |
| `gutters` | string | `"0"` | El espacio mínimo a cada lado del contenido |
| `intrinsic` | boolean | `false` | Centrar elementos hijos según su ancho de contenido |

## Ejemplos

### Básico

Puedes crear una página web de una sola columna simplemente anidando un `Stack` dentro de un `Center` dentro de un `Box`. El `Box` teniendo `padding` por defecto significa que proporcionar padding a los lados del `Center` usando la prop `gutters` no es necesario.

```html linenums="1"
<box-l>
  <center-l>
    <stack-l>
      <!-- Cualquier contenido de flujo aquí (encabezados, párrafos, etc.) -->
    </stack-l>
  <center-l>
</box-l>
```

??? info "Explicacion"

    Aquí el autor está mostrando cómo construir una **página sencilla de una sola columna** combinando tres componentes de layout que ya hemos visto:

    ```text
    Box
      │
      └── Center
            │
            └── Stack
                  │
                  ├── Título
                  ├── Párrafo
                  ├── Imagen
                  └── Otro contenido
    ```

    La idea importante es que **cada componente tiene una responsabilidad diferente**.

    ---

    __1. `Box`: crea el espacio alrededor__

    Tenemos:

    ```html 
    <box-l>
      ...
    </box-l>
    ```

    El `Box` es un contenedor que normalmente aplica `padding`.

    Visualmente:

    ```text 
    ┌────────────────────────────────────────────┐
    │                                            │
    │   padding                                  │
    │    ↓                                       │
    │    ┌──────────────────────────────────┐    │
    │    │                                  │    │
    │    │           CONTENIDO              │    │
    │    │                                  │    │
    │    └──────────────────────────────────┘    │
    │                                            │
    └────────────────────────────────────────────┘
    ```

    Por eso el autor dice:

    > "El `Box` teniendo `padding` por defecto..."

    El `Box` ya está proporcionando espacio alrededor de todo.

    ---

    __2. `Center`: limita y centra la columna__

    Dentro ponemos:

    ```html 
    <center-l>
      ...
    </center-l>
    ```

    El `Center` se encarga de:

    ```text 
    ┌───────────────────────────────────────────────┐
    │                                               │
    │       ┌─────────────────────────────┐         │
    │       │                             │         │
    │       │         CENTER              │         │
    │       │         max 60ch            │         │
    │       │                             │         │
    │       └─────────────────────────────┘         │
    │                                               │
    └───────────────────────────────────────────────┘
    ```

    Es decir:

    ```text
    Center
    ├── max-width
    ├── margin-inline: auto
    └── opcionalmente gutters
    ```

    Su función principal es crear una **columna centrada con una medida máxima razonable**.

    ---

    __3. `Stack`: organiza verticalmente__

    Dentro de `Center` ponemos:

    ```html 
    <stack-l>
      ...
    </stack-l>
    ```

    El `Stack` se encarga del **espaciado vertical entre los elementos**.

    Por ejemplo:

    ```html 
    <stack-l>
      <h1>Mi página</h1>

      <p>Primer párrafo...</p>

      <p>Segundo párrafo...</p>

      <button>Continuar</button>
    </stack-l>
    ```

    Visualmente:

    ```text 
    ┌──────────────────────────────┐
    │                              │
    │  Mi página                   │
    │                              │
    │  Primer párrafo...           │
    │                              │
    │  Segundo párrafo...          │
    │                              │
    │  [ Continuar ]               │
    │                              │
    └──────────────────────────────┘
    ```

    El `Stack` se preocupa de mantener una separación vertical consistente.

    ---

    __4. Entonces, ¿qué hace cada uno?__

    Tenemos:

    ```html 
    <box-l>
      <center-l>
        <stack-l>
          <!-- contenido -->
        </stack-l>
      </center-l>
    </box-l>
    ```

    La jerarquía sería:

    ```text 
    ┌─────────────────────────────────────┐
    │ BOX                                 │
    │                                     │
    │   ┌─────────────────────────────┐   │
    │   │ CENTER                      │   │
    │   │                             │   │
    │   │   ┌─────────────────────┐   │   │
    │   │   │ STACK               │   │   │
    │   │   │                     │   │   │
    │   │   │ Título              │   │   │
    │   │   │                     │   │   │
    │   │   │ Párrafo             │   │   │
    │   │   │                     │   │   │
    │   │   │ Párrafo             │   │   │
    │   │   │                     │   │   │
    │   │   └─────────────────────┘   │   │
    │   │                             │   │
    │   └─────────────────────────────┘   │
    │                                     │
    └─────────────────────────────────────┘
    ```

    Cada componente tiene una responsabilidad:

    | Componente | Responsabilidad                     |
    | ---------- | ----------------------------------- |
    | `Box`      | Espacio exterior mediante `padding` |
    | `Center`   | Centrar y limitar el ancho          |
    | `Stack`    | Separar elementos verticalmente     |

    Esto es **composición de layouts**.

    ---

    __5. ¿Qué significa que `gutters` no sea necesario?__

    Esta es la parte que puede confundirte.

    El `Center` puede tener una opción llamada:

    ```text 
    gutters
    ```

    que básicamente sirve para agregar espacio lateral:

    ```text 
    ┌──────────────────────────────┐
    │ ← gutters →                 │
    │     CONTENIDO               │
    │                 ← gutters → │
    └──────────────────────────────┘
    ```

    Pero en este caso tenemos:

    ```html 
    <box-l>
      <center-l>
        ...
      </center-l>
    </box-l>
    ```

    El `Box` ya proporciona `padding`.

    Por tanto:

    ```text 
    Box
    │
    └── padding lateral
          │
          ▼
        Center
          │
          ▼
        Stack
    ```

    No necesitas decirle también al `Center`:

    ```text 
    gutters
    ```

    Porque podrías terminar aplicando espacio dos veces.

    Por ejemplo, conceptualmente:

    ```text 
    ┌─────────────────────────────────────────┐
    │                                        │
    │  Box padding                            │
    │    ↓                                    │
    │    ┌──────────────────────────────┐    │
    │    │                              │    │
    │    │ Center padding (gutters)     │    │
    │    │                              │    │
    │    │       CONTENIDO              │    │
    │    │                              │    │
    │    └──────────────────────────────┘    │
    │                                        │
    └─────────────────────────────────────────┘
    ```

    Tendrías un espacio lateral acumulado innecesariamente.

    ---

    __6. ¿Por qué es una página de una sola columna?__

    Porque no tienes un `Sidebar` ni una distribución horizontal.

    La estructura es simplemente:

    ```text 
    ┌────────────────────────────┐
    │            Box             │
    │                            │
    │          Center            │
    │                            │
    │          Stack             │
    │                            │
    │          Título            │
    │                            │
    │          Párrafo           │
    │                            │
    │          Imagen            │
    │                            │
    │          Párrafo           │
    │                            │
    └────────────────────────────┘
    ```

    Todo fluye verticalmente.

    Por eso es una **single-column page**.

    ---

    __7. La idea más importante: composición__

    Lo interesante aquí es que el autor no está creando un componente gigante llamado:

    ```text
    SingleColumnPage
    ```

    con 200 reglas CSS.

    En cambio, compone:

    ```text 
    Box
      +
    Center
      +
    Stack
    ```

    Y cada uno resuelve un problema:

    ```text 
    BOX
    ↓
    "Necesito espacio alrededor"

    CENTER
    ↓
    "Necesito controlar el ancho y centrar el contenido"

    STACK
    ↓
    "Necesito separar verticalmente los elementos"
    ```

    Juntos:

    ```text 
    ┌─────────────────────────────────────┐
    │             BOX                     │
    │                                     │
    │      ┌─────────────────────┐        │
    │      │       CENTER        │        │
    │      │                     │        │
    │      │    ┌───────────┐    │        │
    │      │    │  STACK    │    │        │
    │      │    │           │    │        │
    │      │    │ Heading   │    │        │
    │      │    │           │    │        │
    │      │    │ Paragraph │    │        │
    │      │    │           │    │        │
    │      │    │ Paragraph │    │        │
    │      │    └───────────┘    │        │
    │      └─────────────────────┘        │
    │                                     │
    └─────────────────────────────────────┘
    ```

    __En una frase:__

    > **`Box` da espacio, `Center` controla la medida y centra la columna, y `Stack` organiza el contenido verticalmente.**

    Y fíjate en algo que conecta con todo lo anterior: **no necesitas que `Center` haga todo**. Si ya tienes un `Box` que proporciona los márgenes internos, no añades otros gutters al `Center`. Cada componente hace su trabajo y se complementan. Esa es exactamente la filosofía de *Every Layout*.


### Layout de documentación

El marcado del ejemplo en *Casos de uso*. En el ejemplo, se han agregado roles de landmarks WAI-ARIA para soporte de lectores de pantalla. Nota que el `Center` se ha envuelto en un contenedor `<div>` genérico. Este `<div>` está sujeto a la lógica del layout `Sidebar`, liberando al `Center` para aplicar su propia lógica dentro de él. El `Sidebar` envuelve cuando el `<div>` comienza a ocupar menos del 66.666% del espacio horizontal disponible. Lee *Sidebar* para una explicación completa.

```html linenums="1"
<sidebar-l contentMin="66.666%" sideWidth="10rem">
  <stack-l role="navigation">
    <!-- elementos de navegación (API refs) -->
  </stack-l>
  <div>
    <center-l role="main">
      <!-- contenido principal de la página -->
    </center-l>
  </div>
</sidebar-l>
```

??? info "Explicacion"

    Aquí el autor está mostrando cómo construir un **layout de documentación real**, parecido al sitio de *Every Layout*, combinando los componentes que ya conoces:

    ```text 
    Sidebar
      │
      ├── Stack
      │     └── Navegación
      │
      └── div
            └── Center
                  └── Contenido principal
    ```

    La clave de este ejemplo está en entender **por qué existe ese `<div>` entre `Sidebar` y `Center`**.

    ---

    __1. La estructura completa__

    Tenemos:

    ```html 
    <sidebar-l contentMin="66.666%" sideWidth="10rem">

      <stack-l role="navigation">
        <!-- navegación -->
      </stack-l>

      <div>
        <center-l role="main">
          <!-- contenido principal -->
        </center-l>
      </div>

    </sidebar-l>
    ```

    Visualmente:

    ```text 
    ┌───────────────────────────────────────────────────────┐
    │                                                       │
    │  ┌─────────────┐    ┌────────────────────────────┐    │
    │  │             │    │                            │    │
    │  │  NAVEGACIÓN │    │           CENTER           │    │
    │  │             │    │                            │    │
    │  │  Inicio     │    │  Contenido principal       │    │
    │  │             │    │                            │    │
    │  │  Layout     │    │  Título                     │    │
    │  │             │    │                            │    │
    │  │  Center     │    │  Párrafos                   │    │
    │  │             │    │                            │    │
    │  └─────────────┘    └────────────────────────────┘    │
    │                                                       │
    └───────────────────────────────────────────────────────┘
    ```

    Tenemos:

    * **Sidebar** → coloca la navegación y el contenido principal.
    * **Stack** → organiza verticalmente la navegación.
    * **`div`** → sirve como contenedor para que `Sidebar` pueda controlar su espacio.
    * **Center** → controla el ancho y centrado del contenido principal.

    ---

    __2. ¿Por qué el `Stack` tiene `role="navigation"`?__

    Tenemos:

    ```html
    <stack-l role="navigation">
    ```

    `Stack` es un componente de layout.

    Su función principal es:

    > "Separar verticalmente los elementos."

    Pero en este caso, ese contenido también tiene un significado semántico:

    > "Esta zona contiene la navegación."

    Por eso se añade:

    ```html 
    role="navigation"
    ```

    Para un lector de pantalla, esto indica:

    > "Esta sección es una zona de navegación."

    Por ejemplo:

    ```text
    Página
    │
    ├── Navegación
    │    ├── Inicio
    │    ├── Center
    │    ├── Stack
    │    └── Sidebar
    │
    └── Contenido principal
    ```

    El usuario de un lector de pantalla puede identificar rápidamente las diferentes regiones de la página.

    ---

    __3. ¿Por qué `role="main"` está en `center-l`?__

    Tenemos:

    ```html 
    <center-l role="main">
    ```

    El `Center` tiene dos responsabilidades aquí:

    __Layout__

    ```text 
    max-width
    margin auto
    padding
    ```

    Semántica

    ```html 
    role="main"
    ```

    El `role="main"` indica:

    > "Esta es la parte principal del contenido de la página."

    Así tenemos:

    ```text 
    Página
    │
    ├── role="navigation"
    │
    └── role="main"
    ```

    Es una forma de ayudar a las tecnologías de asistencia a entender la estructura de la página.

    ---

    __4. Ahora viene la parte importante: ¿por qué un `<div>`?__

    Observa esto:

    ```html 
    <sidebar-l>
      <stack-l>
        ...
      </stack-l>

      <div>
        <center-l>
          ...
        </center-l>
      </div>
    </sidebar-l>
    ```

    Podrías preguntarte:

    > ¿Por qué no poner directamente el `center-l`?

    Es decir:

    ```html 
    <sidebar-l>
      <stack-l>
        ...
      </stack-l>

      <center-l>
        ...
      </center-l>
    </sidebar-l>
    ```

    La razón es que **`Sidebar` necesita controlar el layout de sus hijos directos**.

    Y ahí aparece una idea fundamental:

    > **Los componentes de layout pueden interferirse entre sí si intentan controlar el mismo elemento.**

    ---

    __5. El `Sidebar` controla sus hijos directos__

    Conceptualmente, `Sidebar` funciona con dos elementos:

    ```text 
    Sidebar
    │
    ├── Hijo 1 → Sidebar
    │
    └── Hijo 2 → Main
    ```

    En este caso:

    ```html 
    <sidebar-l>
      <stack-l>...</stack-l>
      <div>...</div>
    </sidebar-l>
    ```

    Los hijos directos son:

    ```text 
    Sidebar
    │
    ├── Stack
    │
    └── div
    ```

    Entonces `Sidebar` puede decidir:

    > "Estos dos elementos forman mis dos zonas."

    La primera:

    ```text 
    Stack
    ↓
    Navegación
    ```

    La segunda:

    ```text 
    div
    ↓
    Contenedor del contenido
    ```

    Y dentro de ese `div` vive:

    ```text 
    Center
    ↓
    Contenido principal
    ```

    ---

    __6. El `div` actúa como una frontera__

    Esto es muy importante.

    Tenemos:

    ```text 
    Sidebar
    │
    │   ← controla esto
    │
    ├── Stack
    │
    └── div
          │
          │   ← aquí termina la responsabilidad
          │     directa de Sidebar
          │
          └── Center
                │
                │   ← ahora Center controla esto
                │
                └── Contenido
    ```

    El `<div>` crea una especie de **capa de aislamiento de layout**.

    El `Sidebar` dice:

    > "Yo organizo el `Stack` y el `div`."

    El `Center` dice:

    > "Yo organizo lo que está dentro de mí."

    Cada uno puede trabajar sin pisar la lógica del otro.

    ---

    __7. ¿Qué significa `contentMin="66.666%"`?__

    Tenemos:

    ```html 
    <sidebar-l contentMin="66.666%">
    ```

    Esto define cuándo el `Sidebar` debe dejar de intentar mantener los elementos uno al lado del otro.

    La idea es aproximadamente:

    > "Mientras el contenido principal pueda ocupar al menos el 66.666% del espacio disponible, mantenemos el layout lateral."

    Por ejemplo:

    ```text 
    Pantalla ancha

    ┌──────────────┬──────────────────────────────┐
    │              │                              │
    │  Sidebar     │       Contenido              │
    │              │       ≥ 66.666%              │
    │              │                              │
    └──────────────┴──────────────────────────────┘
    ```

    Pero cuando el espacio disponible se vuelve demasiado pequeño:

    ```text 
    Pantalla estrecha

    ┌────────────────────────────────┐
    │                                │
    │ Sidebar                        │
    │                                │
    ├────────────────────────────────┤
    │                                │
    │ Contenido                      │
    │                                │
    └────────────────────────────────┘
    ```

    El `Sidebar` **hace wrap**.

    Es decir, deja de poner los elementos uno al lado del otro y los coloca uno debajo del otro.

    ---

    __8. ¿Qué significa `sideWidth="10rem"`?__

    Tenemos:

    ```html 
    sideWidth="10rem"
    ```

    Esto indica aproximadamente el ancho ideal de la barra lateral.

    Por ejemplo:

    ```text 
    ┌───────────┬─────────────────────────────────┐
    │           │                                 │
    │  10rem    │          Contenido              │
    │           │                                 │
    └───────────┴─────────────────────────────────┘
    ```

    El `Sidebar` intenta mantener la barra lateral alrededor de `10rem`, mientras que el resto del espacio queda disponible para el contenido.

    La combinación sería:

    ```text 
    contentMin="66.666%"
              +
    sideWidth="10rem"
              │
              ▼
    ┌──────────────┬──────────────────────────────┐
    │   Sidebar    │          Main                │
    │   ~10rem     │          flexible            │
    └──────────────┴──────────────────────────────┘
    ```

    Si ya no hay espacio suficiente:

    ```text 
    ┌─────────────────────────────────────┐
    │ Sidebar                             │
    ├─────────────────────────────────────┤
    │ Main                                │
    └─────────────────────────────────────┘
    ```

    El layout se adapta.

    ---

    __9. La arquitectura completa__

    Ahora podemos entender toda la composición:

    ```text 
                        SIDEBAR-L
                          │
                ┌─────────┴──────────┐
                │                    │
                ▼                    ▼
            STACK-L                  DIV
                │                    │
                ▼                    ▼
          NAVIGATION             CENTER-L
                                      │
                                      ▼
                                    MAIN
                                      │
                                      ▼
                                CONTENIDO
    ```

    Cada componente tiene una responsabilidad:

    | Elemento            | Responsabilidad                        |
    | ------------------- | -------------------------------------- |
    | `sidebar-l`         | Distribuir navegación + contenido      |
    | `stack-l`           | Espaciar verticalmente la navegación   |
    | `div`               | Aislar el layout del contenido         |
    | `center-l`          | Limitar y centrar la columna principal |
    | `role="navigation"` | Identificar la navegación              |
    | `role="main"`       | Identificar el contenido principal     |

    ---

    __10. Lo más importante que debes entender__

    Este ejemplo resume muy bien la filosofía de *Every Layout*.

    No tienes un componente gigante que controle todo:

    ```text 
    ❌ DocumentationPage.css
      800 líneas de CSS
      márgenes específicos
      posiciones específicas
      media queries específicas
    ```

    En cambio, tienes:

    ```text 
    Sidebar
      │
      ├── Stack
      │
      └── div
            │
            └── Center
    ```

    Y cada componente tiene una responsabilidad limitada.

    El `Sidebar` decide **cómo se relacionan lateralmente las dos grandes zonas**.

    El `div` evita que el `Sidebar` se meta en la lógica interna del contenido.

    El `Center` decide **qué tan ancho debe ser el contenido y cómo centrarlo**.

    El `Stack` decide **cómo separar verticalmente los elementos**.

    Y los `roles` añaden la **semántica de accesibilidad**.

    La idea que debes llevarte es esta:

    > **El `<div>` no está ahí porque visualmente necesitemos otro contenedor. Está ahí para separar responsabilidades entre layouts. `Sidebar` controla sus hijos directos; `Center` controla su propio contenido.**

    Ese pequeño `<div>` es, en esencia, una **frontera entre dos sistemas de layout**.


### Centrado vertical y horizontal

Usando composición y el componente `Cover`, es trivial centrar un elemento horizontal *y* verticalmente. El booleano `intrinsic` se usa aquí para centrar el párrafo independientemente del ancho de su contenido.

```html linenums="1"
<cover-l centered="center-l">
  <center-l intrinsic>
    <p>Estoy en el centro absoluto.</p>
  </center-l>
</cover-l>
```

??? info "Explicacion"

    Aquí el autor está mostrando una combinación muy potente de los layouts de *Every Layout*: **`Cover` + `Center` + `intrinsic`**.

    El objetivo es conseguir esto:

    ```text 
    ┌─────────────────────────────────────────────┐
    │                                             │
    │                                             │
    │                                             │
    │                  ┌───────────────┐          │
    │                  │ Estoy en el   │          │
    │                  │ centro        │          │
    │                  │ absoluto.     │          │
    │                  └───────────────┘          │
    │                                             │
    │                                             │
    │                                             │
    └─────────────────────────────────────────────┘
    ```

    Es decir:

    * centrado **horizontalmente**
    * centrado **verticalmente**
    * y el párrafo conserva su **ancho natural**

    Vamos por partes.

    ---

    __1. El código__

    ```html 
    <cover-l centered="center-l">
      <center-l intrinsic>
        <p>Estoy en el centro absoluto.</p>
      </center-l>
    </cover-l>
    ```

    Tenemos tres niveles:

    ```text 
    Cover
      │
      └── Center
            │
            └── Párrafo
    ```

    Cada uno hace algo diferente.

    ```text 
    Cover
    ↓
    Centrado vertical

    Center
    ↓
    Centrado horizontal

    intrinsic
    ↓
    El elemento conserva su ancho natural
    ```

    ---

    __2. ¿Qué hace `Cover`?__

    `Cover` es un layout pensado para ocupar una zona de la pantalla y posicionar un elemento en el centro.

    Conceptualmente:

    ```text 
    ┌─────────────────────────────────────┐
    │                                     │
    │                                     │
    │                                     │
    │               CENTER                │
    │                                     │
    │                                     │
    │                                     │
    └─────────────────────────────────────┘
    ```

    El `Cover` se encarga de la dimensión vertical.

    En otras palabras:

    > "Tengo una superficie disponible y quiero colocar un elemento en el centro de ella."

    Por eso `Cover` es ideal para cosas como:

    * pantallas de bienvenida;
    * mensajes de "No hay resultados";
    * páginas de error;
    * diálogos;
    * estados vacíos;
    * contenido que debe quedar visualmente centrado.

    ---

    __3. ¿Qué significa `centered="center-l"`?__

    Tenemos:

    ```html 
    <cover-l centered="center-l">
    ```

    Esto le está diciendo al `Cover`:

    > "El elemento que quiero centrar es este `center-l`."

    Es decir:

    ```text
    Cover
    │
    │    ┌─────────────────┐
    │    │                 │
    │    │    center-l     │ ← elemento centrado
    │    │                 │
    │    └─────────────────┘
    │
    └───────────────────────┘
    ```

    El `Cover` se ocupa de llevar ese elemento al centro **verticalmente**.

    ---

    __4. Luego aparece `Center`__

    Dentro tenemos:

    ```html 
    <center-l intrinsic>
      <p>Estoy en el centro absoluto.</p>
    </center-l>
    ```

    Ahora el `Center` se ocupa del centrado horizontal.

    Así que tenemos:

    ```text 
                Cover
                  │
                  │ centra verticalmente
                  ▼
              Center
                  │
                  │ centra horizontalmente
                  ▼
              Contenido
    ```

    El resultado:

    ```text 
    ┌──────────────────────────────────────────┐
    │                                          │
    │                                          │
    │                                          │
    │            ┌────────────────┐            │
    │            │ Estoy en el    │            │
    │            │ centro         │            │
    │            │ absoluto.      │            │
    │            └────────────────┘            │
    │                                          │
    │                                          │
    │                                          │
    └──────────────────────────────────────────┘
    ```

    ---

    __5. ¿Por qué `intrinsic`?__

    Esta es la parte que conecta con lo que ya aprendiste sobre `Center`.

    Tenemos:

    ```html
    <center-l intrinsic>
    ```

    El `intrinsic` hace que los hijos se centren según su **tamaño natural**.

    Internamente, conceptualmente, se consigue mediante algo parecido a:

    ```css 
    display: flex;
    flex-direction: column;
    align-items: center;
    ```

    Por lo tanto, el párrafo:

    ```html 
    <p>Estoy en el centro absoluto.</p>
    ```

    queda centrado horizontalmente según su ancho.

    ---

    __6. ¿Qué pasa si quitamos `intrinsic`?__

    Sin:

    ```html 
    <center-l>
    ```

    el párrafo podría comportarse como un elemento `block` normal.

    Los elementos `block`, por defecto, tienden a ocupar el espacio disponible.

    Visualmente:

    ```text 
    ┌─────────────────────────────────────────┐
    │                                         │
    │  ┌───────────────────────────────────┐  │
    │  │ Estoy en el centro absoluto.     │  │
    │  └───────────────────────────────────┘  │
    │                                         │
    └─────────────────────────────────────────┘
    ```

    El párrafo podría ocupar todo el ancho disponible.

    Con:

    ```html
    <center-l intrinsic>
    ```

    la idea es:

    ```text 
    ┌─────────────────────────────────────────┐
    │                                         │
    │          ┌─────────────────┐            │
    │          │ Estoy en el     │            │
    │          │ centro absoluto.│            │
    │          └─────────────────┘            │
    │                                         │
    └─────────────────────────────────────────┘
    ```

    El contenido se centra según su tamaño natural.

    ---

    __7. La composición completa__

    Este ejemplo es muy bueno para entender la filosofía de *Every Layout*:

    ```text
    ┌─────────────────────────────────────────────┐
    │                                             │
    │                   COVER                     │
    │                                             │
    │              ┌───────────────┐              │
    │              │    CENTER     │              │
    │              │   intrinsic   │              │
    │              │               │              │
    │              │   <p>...</p>  │              │
    │              │               │              │
    │              └───────────────┘              │
    │                                             │
    └─────────────────────────────────────────────┘
    ```

    Las responsabilidades están separadas:

    __`Cover`__

    ```text
    Se ocupa del eje vertical
    ```

    ↓

    __`Center`__

    ```text 
    Se ocupa del eje horizontal
    ```

    ↓

    __`intrinsic`__

    ```text 
    Centra el contenido según su tamaño natural
    ```

    Y así consigues:

    ```text
                    X
                    │
                    │
                    ▼
                horizontal
                    +
                    │
                    │
                    ▼
                vertical
                    │
                    │
                    ▼
                  centro
    ```

    ---

    __La idea que debes recordar__

    > **`Cover` te permite centrar verticalmente; `Center` te permite centrar horizontalmente; `intrinsic` hace que el elemento interior se centre según su ancho natural.**

    Por eso:

    ```html 
    <cover-l centered="center-l">
      <center-l intrinsic>
        <p>Estoy en el centro absoluto.</p>
      </center-l>
    </cover-l>
    ```

    es una composición elegante:

    ```text 
    Cover
      │
      │  vertical
      ▼
    Center
      │
      │  horizontal
      ▼
    Intrinsic
      │
      ▼
    Contenido natural
    ```

    Y eso resume muy bien la filosofía de *Every Layout*: **en lugar de crear una regla CSS especial para "centrar absolutamente este párrafo", compones layouts pequeños que ya saben resolver cada parte del problema.**
