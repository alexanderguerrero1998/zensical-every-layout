# The Imposter

## El problema

El posicionamiento en CSS, usando una o más instancias de los valores `relative`, `absolute`, `fixed` de la propiedad `position`, es como anular manualmente el layout web. Es *desactivar* el layout automático y tomar el asunto en tus propias manos. Como con pilotar un avión comercial, esta no es una responsabilidad que desearías asumir excepto en circunstancias raras y extremas.

En la documentación de *Frame*, se te advirtió sobre los peligros de evitar los algoritmos de layout estándar del navegador:

![](absolute.png)

> Cuando le das a un elemento `position: absolute`, lo eliminas del flujo natural del documento. Se coloca como si los elementos a su alrededor no existieran. En la mayoría de las circunstancias, esto es altamente indeseable, y puede llevar fácilmente a problemas como superposición y contenido oscurecido.

Pero, ¿qué pasa si *querías* ocultar contenido, colocando otro contenido sobre él? Si has estado trabajando en desarrollo web por más de 23 minutos, es probable que ya hayas hecho esto, en la incorporación de un elemento de diálogo, "popup" o menú desplegable personalizado.

El propósito del elemento `Imposter` es agregar un elemento de *superposición* de propósito general a tu suite de layouts. Permitirá al autor posicionar centralmente un elemento sobre el viewport, el documento, o un elemento "contenedor de posicionamiento" seleccionado.


??? info "Explicaciom"

    Claro. Esta primitiva **Imposter** de *Every Layout* parece complicada por el tema de `position`, pero la idea central es bastante sencilla:

    > **Imposter sirve para poner temporalmente un elemento encima de otro contenido, normalmente centrado, sin que ese elemento participe en el layout normal.**

    La clave es entender **cuándo está justificado romper el flujo normal**.

    ---

    __1. Primero: ¿qué problema intenta resolver?__

    Normalmente, CSS funciona así:

    ```html
    <div>Contenido A</div>
    <div>Contenido B</div>
    <div>Contenido C</div>
    ```

    El navegador coloca los elementos siguiendo el flujo:

    ```text
    Contenido A
    Contenido B
    Contenido C
    ```

    Cada elemento ocupa su espacio y los demás saben que existe.

    Eso es lo que *Every Layout* intenta aprovechar: **dejar que los algoritmos del navegador hagan la mayor parte del trabajo**.

    Pero hay situaciones donde **precisamente quieres que algo ignore el flujo**.

    Por ejemplo:

    ```text
    ┌─────────────────────────────┐
    │                             │
    │       CONTENIDO             │
    │                             │
    │          ┌───────┐          │
    │          │ MODAL │          │
    │          └───────┘          │
    │                             │
    └─────────────────────────────┘
    ```

    El modal tiene que estar **encima** del contenido.

    No quieres esto:

    ```text
    Contenido
    Modal
    Más contenido
    ```

    Quieres esto:

    ```text
    Contenido
      ↑
    Modal encima
    ```

    Ahí entra **Imposter**.

    ---

    __2. ¿Por qué se llama "Imposter"?__

    La metáfora es bastante buena.

    Un *impostor* es algo que **se coloca en el lugar de otra cosa** o aparenta estar donde no debería.

    En CSS, el `Imposter` se mete visualmente **encima de otro contenido**, como si fuera una capa independiente.

    Por ejemplo:

    * un modal
    * un diálogo
    * un menú desplegable
    * un tooltip
    * un botón flotante
    * un indicador
    * un overlay
    * un mensaje temporal

    Todos tienen algo en común:

    > **No quieres que su existencia cambie el layout del contenido que está debajo.**

    ---

    __3. ¿Por qué `position: absolute`?__

    Supongamos que tienes:

    ```html
    <div class="contenido">
        Texto
    </div>

    <div class="modal">
        Hola
    </div>
    ```

    Normalmente:

    ```text
    ┌───────────────┐
    │ Texto         │
    ├───────────────┤
    │ Hola          │
    └───────────────┘
    ```

    Pero si haces:

    ```css
    .modal {
        position: absolute;
    }
    ```

    el modal sale del flujo normal.

    Ahora el navegador piensa aproximadamente:

    > "Este elemento ya no participa en cómo se distribuyen los demás elementos. Yo me encargo de colocarlo por separado."

    Entonces puedes conseguir:

    ```text
    ┌──────────────────────────┐
    │ Texto                    │
    │                          │
    │       ┌──────────┐       │
    │       │  Hola    │       │
    │       └──────────┘       │
    │                          │
    └──────────────────────────┘
    ```

    Eso es exactamente lo que **sí queremos** en este caso.

    Por eso el texto dice:

    > "¿Qué pasa si querías ocultar contenido colocando otro contenido sobre él?"

    Ahí está la excepción.

    ---

    __4. El problema con `absolute`__

    Aquí está la parte importante de *Every Layout*.

    No está diciendo:

    > "`position: absolute` es malo."

    Está diciendo:

    > **No uses `absolute` para resolver problemas que el layout normal puede resolver.**

    Por ejemplo, esto sería mala idea:

    ```css
    .card-title {
        position: absolute;
        top: 20px;
        left: 30px;
    }
    ```

    si simplemente querías colocar un título dentro de una tarjeta.

    Porque probablemente podrías resolverlo con:

    ```css
    .card {
        display: flex;
        flex-direction: column;
    }
    ```

    o Grid, Stack, Center, etc.

    Pero para un modal:

    ```css
    .modal {
        position: absolute;
    }
    ```

    tiene muchísimo sentido.

    Porque **el modal realmente debe estar fuera del flujo**.

    ---

    __5. ¿Qué significa "centralmente"?__

    El `Imposter` normalmente busca algo como esto:

    ```text
    ┌──────────────────────────────┐
    │                              │
    │                              │
    │          ┌────────┐          │
    │          │        │          │
    │          │Imposter│          │
    │          │        │          │
    │          └────────┘          │
    │                              │
    │                              │
    └──────────────────────────────┘
    ```

    Es decir:

    **centrar un elemento superpuesto dentro de algún contexto.**

    Y ese contexto puede ser:

    __El viewport__

    La ventana del navegador:

    ```text
    ┌──────────────────────────────┐
    │                              │
    │                              │
    │          ┌────────┐          │
    │          │ MODAL  │          │
    │          └────────┘          │
    │                              │
    └──────────────────────────────┘
    ```

    Perfecto para un modal.

    ---

    __El documento__

    También puede posicionarse respecto al documento.

    Esto puede ser útil cuando el elemento debe superponerse pero seguir perteneciendo a una determinada zona de la página.

    ---

    __Un contenedor__

    Esta es una idea muy importante.

    Imagina:

    ```html
    <div class="card">
        <img>
        <div class="imposter">
            ...
        </div>
    </div>
    ```

    Quieres que el `Imposter` se posicione respecto a `.card`.

    Entonces `.card` se convierte en el **contenedor de posicionamiento**.

    Conceptualmente:

    ```text
    ┌──────────────────────────────┐
    │ CARD                         │
    │                              │
    │       ┌────────────┐         │
    │       │ IMPOSTER   │         │
    │       └────────────┘         │
    │                              │
    └──────────────────────────────┘
    ```

    Esto normalmente se consigue haciendo que el padre establezca el contexto de posicionamiento:

    ```css
    .card {
        position: relative;
    }
    ```

    y el hijo:

    ```css
    .imposter {
        position: absolute;
    }
    ```

    Entonces:

    ```text
    .card
      │
      └── establece el contexto
              │
              ▼
          .imposter
          se posiciona
          respecto a .card
    ```

    ---

    __6. Esto conecta directamente con lo que viste en Frame__

    Recuerda la idea de **Frame** que estabas estudiando.

    Frame intenta conseguir algo parecido a:

    ```text
    ┌────────────────────────────┐
    │                            │
    │                            │
    │         CONTENIDO          │
    │                            │
    │                            │
    └────────────────────────────┘
    ```

    pero **sin sacar elementos del flujo**.

    Por eso utiliza cosas como:

    ```css
    display: flex;
    flex-direction: column;
    ```

    y:

    ```css
    margin-block: auto;
    ```

    para distribuir el espacio.

    Eso es layout normal.

    ---

    __7. Imposter es casi lo contrario__

    Puedes verlo así:

    __Frame__

    > "Navegador, organiza los elementos por mí."

    ```text
    Layout automático
          ↓
    ┌──────────────┐
    │ elemento     │
    │ elemento     │
    │ elemento     │
    └──────────────┘
    ```

    __Imposter__

    > "Navegador, este elemento quiero colocarlo yo por encima."

    ```text
    Layout normal
          +
    elemento superpuesto
    ```

    ```text
    ┌──────────────────────┐
    │ contenido             │
    │       ┌────────┐      │
    │       │ overlay│      │
    │       └────────┘      │
    │ contenido             │
    └──────────────────────┘
    ```

    Por eso en *Every Layout* estas primitivas se complementan.

    No son recetas aisladas.

    ---

    __8. Un ejemplo real: modal__

    Supongamos:

    ```html
    <main>
        <h1>Mi página</h1>
        <p>Muchísimo contenido...</p>
    </main>

    <div class="modal">
        <h2>¿Eliminar elemento?</h2>
        <button>Cancelar</button>
        <button>Eliminar</button>
    </div>
    ```

    El contenido de `main` debe seguir existiendo debajo.

    El modal no debe empujar el contenido:

    ❌

    ```text
    Página
    Página
    Página
    Modal
    Página
    ```

    Debe aparecer **encima**:

    ✅

    ```text
    Página
    Página
      ┌───────────────┐
      │ ¿Eliminar?    │
      │               │
      │ Cancelar      │
      │ Eliminar      │
      └───────────────┘
    Página
    Página
    ```

    Ahí `Imposter` es exactamente la herramienta adecuada.

    ---

    __9. La frase más importante del texto__

    Esta:

    > "El propósito del elemento `Imposter` es agregar un elemento de superposición de propósito general a tu suite de layouts."

    Traducido a lenguaje más directo:

    > **Imposter es la primitiva que debes usar cuando necesitas romper deliberadamente el flujo para colocar una cosa encima de otra.**

    Ese es el concepto que debes llevarte.

    ---

    __10. ¿Cuándo usarlo?__

    Piensa en esta pregunta:

    > **¿Quiero que este elemento ocupe espacio dentro del layout o quiero que flote encima de él?**

    Si quieres que ocupe espacio:

    ```text
    Stack
    Cluster
    Switcher
    Sidebar
    Grid
    Frame
    Center
    ...
    ```

    Si quieres que esté **encima**:

    ```text
    Imposter
    ```

    Por ejemplo:

    | Situación                           | Primitiva    |
    | ----------------------------------- | ------------ |
    | Elementos uno debajo de otro        | Stack        |
    | Elementos agrupados horizontalmente | Cluster      |
    | Cambiar entre fila/columna          | Switcher     |
    | Imagen + contenido lateral          | Sidebar      |
    | Centrar contenido                   | Center       |
    | Distribuir contenido en una región  | Frame        |
    | **Poner algo encima de otra cosa**  | **Imposter** |

    Y ahí está la filosofía de *Every Layout*: **no memorizar trucos CSS, sino identificar qué problema de layout tienes y elegir la primitiva adecuada**.

    En tu caso, después de haber entendido `Frame`, `Sidebar`, `Switcher`, `Stack` y demás, **Imposter es una de las pocas primitivas donde realmente sí debes aceptar `position: absolute`**. No estás "haciendo trampa"; estás usando una herramienta especializada para un problema que el flujo normal no puede resolver elegantemente.

## La solución

Hay muchas formas de posicionar elementos centralmente verticalmente, y muchas más para posicionarlos horizontalmente (algunas alternativas se mencionaron como parte del layout `Center`). Sin embargo, solo hay algunas formas de posicionar elementos centralmente *sobre* otros elementos/contenido.

El enfoque contemporáneo es *usar CSS Grid* ↗. Una vez que tu grilla está establecida, puedes organizar el contenido por número de línea de grilla. El concepto de *flujo* se vuelve irrelevante, y la superposición es eminentemente alcanzable donde sea deseada.

![](gridarea.png)

??? info "Explicacion"

    Exactamente. Aquí *Every Layout* está dando un paso más interesante: **ya no está hablando simplemente de `position: absolute`**, sino de una forma moderna de conseguir superposición usando **CSS Grid**.

    La idea fundamental es esta:

    > **Grid permite que varios elementos ocupen la misma celda.**

    Y eso hace que puedan quedar **uno encima del otro sin necesidad de sacarlos del flujo mediante `position: absolute`**.

    ---

    __1. Primero: ¿qué significa "superponer"?__

    Imagina esto:

    ```text
    ┌──────────────────────────────┐
    │                              │
    │        IMAGEN                │
    │                              │
    │          ┌──────────┐        │
    │          │  TEXTO   │        │
    │          └──────────┘        │
    │                              │
    └──────────────────────────────┘
    ```

    Tenemos:

    * una imagen
    * texto encima de la imagen

    Con el layout tradicional, los elementos normalmente se colocan:

    ```text
    Imagen

    Texto
    ```

    Uno después del otro.

    Pero nosotros queremos:

    ```text
    Imagen
      ↑
    Texto encima
    ```

    Eso es **superposición**.

    ---

    __2. ¿Cómo lo hacemos con Grid?__

    La magia está en esto:

    ```css
    .imposter {
        display: grid;
    }
    ```

    Y luego hacemos que los elementos ocupen la misma área:

    ```css
    .imposter > * {
        grid-area: 1 / 1;
    }
    ```

    Ahora imagina:

    ```html
    <div class="imposter">
        <img src="imagen.jpg">
        <div class="texto">
            Hola
        </div>
    </div>
    ```

    Normalmente pensarías:

    ```text
    grid
    ├── imagen
    └── texto
    ```

    Pero ambos tienen:

    ```css
    grid-area: 1 / 1;
    ```

    por lo que Grid dice:

    > "Los dos van a la misma área."

    Resultado:

    ```text
    ┌──────────────────────┐
    │                      │
    │       IMAGEN         │
    │                      │
    │      ┌────────┐      │
    │      │  Hola  │      │
    │      └────────┘      │
    │                      │
    └──────────────────────┘
    ```

    ---

    __3. ¿Qué quiere decir el texto con "el concepto de flujo se vuelve irrelevante"?__

    Esta parte es importante.

    En un layout normal tienes algo como:

    ```text
    Elemento A
        ↓
    Elemento B
        ↓
    Elemento C
    ```

    El orden importa muchísimo.

    El navegador dice:

    > A ocupa este espacio, después pongo B, después C.

    Eso es el **flujo**.

    ---

    Con Grid puedes decir:

    ```text
    A → área 1
    B → área 1
    C → área 2
    ```

    Por ejemplo:

    ```text
    ┌──────────────────────┐
    │                      │
    │       A + B          │
    │       superpuestos   │
    │                      │
    ├──────────────────────┤
    │          C           │
    └──────────────────────┘
    ```

    A y B **no necesitan estar uno después del otro**.

    Ambos pueden estar en la misma celda.

    Por eso el texto dice:

    > "El concepto de flujo se vuelve irrelevante"

    No significa literalmente que CSS deje de tener flujo. Significa que **para decidir dónde se colocan esos elementos, ya no dependes de que uno tenga que ir después del otro**.

    Grid te permite declarar:

    > "Estos dos pertenecen a esta misma área."

    ---

    __4. ¿Qué significa "por número de línea de grilla"?__

    Aquí entra la parte que probablemente estás viendo en la imagen.

    Una grid tiene líneas:

    ```text
        línea 1       línea 2       línea 3
          ↓             ↓             ↓
          │             │             │
          │             │             │
          │             │             │
          │             │             │
    ```

    Por ejemplo:

    ```css
    .container {
        display: grid;
    }
    ```

    Creamos una grid de una columna:

    ```text
    línea 1
      │
      │
      │
    línea 2
    ```

    Entonces:

    ```css
    .elemento {
        grid-column: 1;
    }
    ```

    significa:

    > ocupa la columna que está entre las líneas 1 y 2.

    ---

    Pero puedes hacer algo mucho más interesante.

    Supongamos una grid:

    ```text
          1       2       3
          │       │       │
          │       │       │
          │       │       │
          │       │       │
    ```

    Puedes decir:

    ```css
    .imagen {
        grid-area: 1 / 1 / 3 / 3;
    }
    ```

    Y:

    ```css
    .texto {
        grid-area: 1 / 1 / 3 / 3;
    }
    ```

    Ambos ocupan **exactamente la misma área**.

    Por tanto:

    ```text
    ┌───────────────────────┐
    │                       │
    │       IMAGEN          │
    │                       │
    │        TEXTO          │
    │                       │
    └───────────────────────┘
    ```

    ---

    __5. Aquí aparece algo muy importante de Grid__

    Probablemente ya has visto Grid pensando:

    ```css
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    ```

    y lo has asociado con:

    ```text
    ┌──────┬──────┬──────┐
    │      │      │      │
    ├──────┼──────┼──────┤
    │      │      │      │
    └──────┴──────┴──────┘
    ```

    Pero Grid también permite esto:

    ```text
    ┌──────────────────────┐
    │                      │
    │    elemento A        │
    │       +              │
    │    elemento B        │
    │                      │
    └──────────────────────┘
    ```

    Es decir:

    **Grid no solamente sirve para repartir elementos. También sirve para hacerlos compartir espacio.**

    Y esto es justamente lo que aprovecha `Imposter`.

    ---

    __6. ¿Y por qué es mejor que `absolute`?__

    Porque con `absolute` tienes que pensar en coordenadas:

    ```css
    .elemento {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }
    ```

    Estás básicamente diciendo:

    > "Quiero que este elemento se coloque aquí."

    Eso es **posicionamiento manual**.

    Con Grid dices:

    ```css
    .container {
        display: grid;
    }

    .elemento {
        grid-area: 1 / 1;
    }
    ```

    Y estás diciendo:

    > "Este elemento pertenece a esta misma área que el otro."

    Es una diferencia conceptual importante.

    __`absolute`__

    ```text
    "Ponlo aquí."
            ↓
      coordenadas
    ```

    __Grid__

    ```text
    "Ponlo en esta área."
            ↓
      algoritmo de layout
    ```

    Y *Every Layout* prefiere la segunda filosofía cuando es posible.

    ---

    __7. Entonces, ¿Imposter ya no necesita `absolute`?__

    Aquí está el matiz.

    **No necesariamente.**

    El patrón `Imposter` tradicional puede implementarse con `position: absolute`, porque ese es precisamente el mecanismo clásico para crear una superposición.

    Pero el texto está diciendo:

    > **Hoy CSS Grid nos ofrece otra herramienta para conseguir el mismo tipo de composición.**

    Y para determinados casos puede ser incluso más elegante.

    Por ejemplo:

    ```css
    .imposter {
        display: grid;
    }

    .imposter > * {
        grid-area: 1 / 1;
    }
    ```

    Eso crea una especie de "escenario":

    ```text
                GRID
                  │
          ┌───────┴───────┐
          │               │
        imagen           texto
          │               │
          └───────┬───────┘
                  ↓
            misma área
                  ↓
          SUPERPOSICIÓN
    ```

    ---

    __8. Y aquí hay una conexión brutal con diseño__

    Esta técnica aparece muchísimo en interfaces modernas.

    Por ejemplo:

    __Imagen + degradado + texto__

    ```text
    ┌─────────────────────────┐
    │                         │
    │       FOTO              │
    │                         │
    │     ┌──────────────┐    │
    │     │ Título       │    │
    │     │ Descripción  │    │
    │     └──────────────┘    │
    └─────────────────────────┘
    ```

    Puedes tener:

    ```text
    Grid area 1
    ├── imagen
    ├── overlay
    └── texto
    ```

    Todos en la misma área.

    ---

    __Botón sobre imagen__

    ```text
    ┌─────────────────────────┐
    │                         │
    │         FOTO            │
    │                         │
    │        [ PLAY ]         │
    │                         │
    └─────────────────────────┘
    ```

    ---

    __Icono sobre avatar__

    ```text
          ┌──────────┐
          │          │
          │   FOTO   │
          │          │
          └──────────┘
                ●
    ```

    ---

    __Modal__

    ```text
    ┌─────────────────────────────┐
    │                             │
    │      página normal          │
    │                             │
    │       ┌────────────┐        │
    │       │   MODAL    │        │
    │       └────────────┘        │
    │                             │
    └─────────────────────────────┘
    ```

    Todos son casos de **elementos que comparten una región espacial**.

    ---

    __9. La idea que debes quedarte__

    No memorices:

    ```css
    grid-area: 1 / 1;
    ```

    como un truco mágico.

    Quédate con este modelo mental:

    ```text
    LAYOUT NORMAL

    A
    ↓
    B
    ↓
    C

    cada uno ocupa su espacio
    ```

    Mientras que:

    ```text
    GRID

    A ─────┐
          ├── misma área
    B ─────┘

          ↓

    A está encima de B
    ```

    Y por eso CSS Grid es tan potente para `Imposter`.

    **El navegador sigue haciendo el layout por ti; simplemente le estás diciendo que dos elementos pertenecen a la misma región.**

    Esa es justamente la filosofía de *Every Layout*: **no pelearte con el navegador para colocar píxeles; describe la relación espacial que quieres y deja que CSS resuelva el resto.**


## Orden de fuente y capas

Ya sea que estés posicionando contenido según las líneas de Grid o haciéndolo con la propiedad `position`, qué elementos aparecen *sobre* cuáles es, por defecto, una cuestión de orden de fuente. Esto es: si dos elementos comparten el mismo espacio, el que aparece *después* del otro será el que venga último en la fuente.

![](othergridarea.png)

> *Dado que puedes colocar cualquier elemento a lo largo de cualquier línea de grilla que desees, un elemento superpuesto último en la fuente puede aparecer primero en el eje vertical*

Esto a menudo se pasa por alto, y algunos creen que se necesita `z-index` para acompañar a `position: absolute` en todos los casos para determinar las "capas". De hecho, `z-index` solo es necesario donde quieres poner en capas elementos posicionados independientemente de su orden de fuente. Es otro tipo de anulación, y debe evitarse siempre que sea posible.

Una carrera armamentista de valores `z-index` cada vez más altos se cita a menudo como una de esas cosas irritantes pero necesarias que tienes que manejar con CSS. Raramente tengo problemas, porque raramente uso posicionamiento, y soy consciente del orden de fuente cuando lo hago.

CSS Grid no precipita una solución general, porque solo funcionaría donde tu elemento de posicionamiento está configurado con `display: grid` de antemano, y el conteo de columnas/filas es adecuado. Necesitamos algo más flexible.

## Posicionamiento

Puedes posicionar un elemento en relación a una de tres cosas ("contextos de posicionamiento" de aquí en adelante):

1. El viewport
2. El documento
3. Un elemento ancestro

Para posicionar un elemento en relación al viewport, usarías `position: fixed`. Para posicionarlo en relación al documento, usas `position: absolute`.

Posicionarlo en relación a un elemento ancestro es posible cuando ese elemento (el "contenedor de posicionamiento" de aquí en adelante) también está explícitamente posicionado. La forma más fácil de hacerlo es darle al ancestro `position: relative`. Esto establece el contexto de posicionamiento localizado *sin* mover la posición del elemento ancestro, o sacarlo del flujo del documento.

![](positionelement.png)

> *El valor `static` para la propiedad `position` es el predeterminado, por lo que raramente lo verás o usarás excepto para restablecer el valor.*

## Centrado

¿Cómo posicionamos el elemento `Imposter` en el *centro* del documento, viewport o contenedor de posicionamiento? Para elementos posicionados, técnicas como `margin: auto` o `place-items: center` no funcionan. En la *anulación manual*, tenemos que usar una combinación de las propiedades `top`, `left`, `bottom` y/o `right`. Importantemente, los valores para cada una de estas propiedades se relacionan con el contexto de posicionamiento — no con el elemento padre inmediato.

![](centring.png)

Hasta ahora, mal: queremos que el elemento en sí mismo esté centrado, no su esquina superior. Donde conocemos el *ancho* del elemento, podemos compensar usando márgenes negativos. Por ejemplo, `margin-left: -20rem` y `margin-top: -10rem` volverán a centrar un elemento que es `40rem` de ancho y `20rem` de alto (el valor negativo es siempre la mitad de la dimensión).

![](posiontext.png)

Evitamos codificar dimensiones porque, como el posicionamiento, prescinde de los algoritmos del navegador para organizar elementos según el espacio disponible. Cada vez que codificas un ancho fijo en un elemento, las posibilidades de que ese elemento o sus contenidos se vuelvan oscurecidos en el dispositivo de alguien en algún lugar son casi inevitables.

No solo eso, sino que podríamos no conocer el ancho o la altura del elemento de antemano. Por lo tanto, no sabríamos qué valores de margen negativo usar para complementarlo.

En lugar de diseñar *para* layout, diseñamos *para* layout, permitiendo que el navegador tenga la última palabra. En este caso, es cuestión de usar transformaciones. La propiedad `transform` organiza los elementos según sus *propias* dimensiones, sean las que sean en el momento dado. En resumen: `transform: translate(-50%, -50%)` traducirá la posición del elemento en -50% de su ancho y altura respectivamente. No necesitamos conocer las dimensiones del elemento de antemano, porque el navegador puede calcularlas y actuar sobre ellas por nosotros.

Centrar el elemento sobre su contenedor de posicionamiento, sin importar sus dimensiones, es por lo tanto bastante simple:

```css linenums="1"
.imposter {
  /* ↓ Posicionar la esquina superior izquierda en el centro */
  position: absolute;
  top: 50%;
  left: 50%;
  /* ↓ Reposicionar para que el centro del elemento
  sea el centro del contenedor de posicionamiento */
  transform: translate(-50%, -50%);
}
```

Debe notarse en este punto que un elemento a nivel de bloque configurado con `position: absolute` ya no ocupa el espacio disponible a lo largo de la dirección de escritura del elemento (generalmente horizontal; izquierda a derecha). En su lugar, el elemento "envuelve" su contenido como si fuera inline.

![](Helloword.png)

Por defecto, el ancho del elemento será el 50%, o menos si su contenido ocupa menos del 50% del contenedor de posicionamiento. Si agregas un `width` o `height` explícito, se respetará y el elemento continuará centrado dentro del contenedor de posicionamiento — el algoritmo de traducción interna se encarga de eso.

## Desbordamiento

¿Qué sucede si el elemento `Imposter` se vuelve más ancho o más alto que su contenedor de posicionamiento? Con un diseño cuidadoso y una curación de contenido, deberías poder crear las tolerancias generosas que eviten que esto suceda en la mayoría de las circunstancias. Pero aún puede suceder.

Por defecto, el efecto verá al `Imposter` *asomando* por los bordes del contenedor de posicionamiento — y puede estar en peligro de oscurecer el contenido alrededor de ese contenedor.

![](imposterposition.png)

Dado que `max-width` y `max-height` anulan `width` y `height` respectivamente, podemos permitir a los autores establecer dimensiones — o dimensiones mínimas — pero aún así asegurar que el elemento esté contenido. Todo lo que queda es agregar `overflow: auto` para asegurar que los contenidos del elemento restringido puedan desplazarse para verse.

```css linenums="1"
.imposter {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  max-width: 100%;
  max-height: 100%;
}
```

## Margen

En algunos casos, será deseable tener un espacio mínimo (gap; space; margin; como quieras llamarlo) entre el elemento `Imposter` y los bordes interiores de su contenedor de posicionamiento. Por dos razones, no podemos lograr esto agregando padding al contenedor de posicionamiento:

1. Insertaría cualquier contenido estático del contenedor, que probablemente no sea un efecto visual deseable
2. El posicionamiento absoluto no respeta el padding: nuestro elemento `Imposter` lo ignoraría y se superpondría

La respuesta, en su lugar, es ajustar los valores de `max-width` y `max-height`. La función `calc()` es especialmente útil para hacer este tipo de ajustes.

```css linenums="1"
.imposter {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  max-width: calc(100% - 2rem);
  max-height: calc(100% - 2rem);
}
```

El ejemplo anterior crearía un espacio mínimo de `1rem` en todos los lados: el valor `2rem` se elimina como `1rem` para cada extremo.

![](unerem.png)

## Posicionamiento fijo

Cuando desees que el `Imposter` esté fijo en relación al *viewport*, en lugar del documento o un elemento (léase: contenedor de posicionamiento) dentro del documento, debes reemplazar `position: absolute` con `position: fixed`. Esto es a menudo deseable para diálogos, que deberían seguir al usuario mientras desplaza el documento y permanecer a la vista hasta que se atiendan.

En el siguiente ejemplo, el elemento tiene una propiedad personalizada `--positioning` con un valor predeterminado de `absolute`.

```css linenums="1"
.imposter {
  position: var(--positioning, absolute);
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  max-width: calc(100% - 2rem);
  max-height: calc(100% - 2rem);
}
```

Como se describe en el artículo *Every Layout Dynamic CSS Components Without JavaScript* ↗, puedes anular este valor predeterminado en línea, dentro de un atributo `style` para casos especiales:

```html linenums="1"
<div class="imposter" style="--positioning: fixed">
  <!-- contenido del imposter -->
</div>
```

En la implementación del componente personalizado a seguir (bajo *El componente*), un mecanismo equivalente toma la forma de una prop booleana. Agregar el atributo `fixed` anula el posicionamiento `absolute` que es predeterminado.

## ⚠ Posicionamiento fijo y Shadow DOM

En la mayoría de los casos, usar un valor `fixed` para `position` posicionará el elemento en relación al viewport. Esto es, cualquier candidato a contenedor de posicionamiento (elementos ancestros posicionados) serán ignorados.

Sin embargo, un host `shadowRoot` ↗ será tratado como el elemento exterior de un documento anidado. Por lo tanto, cualquier elemento con `position: fixed` encontrado dentro de Shadow DOM se posicionará en su lugar en relación al host (el elemento que alberga el Shadow DOM). En efecto, se convierte en un contenedor de posicionamiento como en ejemplos anteriores.

![](normalelement.png)

## Casos de uso

Donde sea que el contenido necesite ser deliberadamente oscurecido, el patrón `Imposter` es tu amigo. Puede ser que el contenido aún no esté disponible. En cuyo caso, el `Imposter` puede consistir en una llamada a la acción para desbloquear ese contenido.

*Esta demostración interactiva solo está disponible en el sitio de Every Layout* ↗.

Puede ser que los artefactos oscurecidos por el `Imposter` sean más decorativos y no necesiten ser revelados en su totalidad.

Al crear un diálogo usando un `Imposter`, ten cuidado con las consideraciones de accesibilidad que deben incluirse — especialmente las relacionadas con la gestión del foco del teclado. *Inclusive Components* ↗ tiene un capítulo sobre diálogos que describe estas consideraciones en detalle.

## El generador

Usa esta herramienta para generar CSS y HTML básicos de Imposter.

La herramienta generadora de código solo está disponible en el *sitio de documentación adjunto* ↗. Aquí está la solución básica, con comentarios. La versión `.contain` contiene el elemento dentro de su contenedor de posicionamiento y maneja el desbordamiento.

**CSS**

```css linenums="1"
.imposter {
  /* ↓ Elegir el elemento de posicionamiento */
  position: var(--positioning, absolute);
  /* ↓ Posicionar la esquina superior izquierda en el centro */
  top: 50%;
  left: 50%;
  /* ↓ Reposicionar para que el centro del elemento
  sea el centro del contenedor */
  transform: translate(-50%, -50%);
}
.imposter.contain {
  /* ↓ Incluir una unidad, o la función calc será inválida */
  --margin: 0px;
  /* ↓ Proporcionar barras de desplazamiento para que el contenido no se oculte */
  overflow: auto;
  /* ↓ Restringir la altura y el ancho, incluyendo el espaciado/margen
  opcional entre el elemento y el contenedor de posicionamiento */
  max-width: calc(100% - (var(--margin) * 2));
  max-height: calc(100% - (var(--margin) * 2));
}
```

**HTML**

Debe proporcionarse un ancestro con un valor de posicionamiento `relative` o `absolute`. Este se convierte en el "contenedor de posicionamiento" sobre el cual se posiciona el elemento. En el siguiente ejemplo, se usa un simple `<div>` con el `style` inline.

```html linenums="1"
<div style="position: relative">
  <p>Contenido estático</p>
  <div class="imposter">
    <p>Contenido superpuesto</p>
  </div>
</div>
```

## El componente

Una implementación de elemento personalizado del `Imposter` está disponible para descargar ↗.

**API de Props**

Las siguientes props (atributos) harán que el componente se renderice nuevamente cuando se alteren. Pueden ser alterados a mano — en las herramientas de desarrollo del navegador — o como sujetos del estado de la aplicación heredada.

| Nombre | Tipo | Default | Descripción |
|---|---|---|---|
| `breakout` | boolean | `false` | Si se permite que el elemento salga del contenedor sobre el cual está posicionado |
| `margin` | string | `0` | El espacio mínimo entre el elemento y los bordes interiores del contenedor de posicionamiento sobre el cual está colocado (cuando breakout no está aplicado) |
| `fixed` | boolean | `false` | Si posicionar el elemento en relación al viewport |

## Ejemplos

### Ejemplo de demostración

El código para la demo en la sección *Casos de uso*. Nota el uso de `aria-hidden="true"` en el contenido hermano superpuesto. Es probable que el contenido superpuesto no esté disponible para los lectores de pantalla, ya que no está disponible (o al menos mayormente oscurecido) visualmente.

```html linenums="1"
<div style="position: relative">
  <text-l words="150" aria-hidden="true"></text-l>
  <imposter-l>
    <box-l style="background-color: var(--color-light)">
      <p class="h4"><strong>No puedes ver todo el contenido, debido a esta caja.</strong></p>
    </box-l>
  </imposter-l>
</div>
```

### Diálogo

El elemento `Imposter` podría tomar el atributo ARIA `role="dialog"` para ser comunicado como un diálogo en los lectores de pantalla. O, más simplemente, podrías simplemente colocar un `<dialog>` dentro del `Imposter`. Nota que el `Imposter` toma `fixed` aquí, para cambiar de una posición `absolute` a `fixed`. Esto significa que el diálogo permanecería centrado en el viewport mientras se desplaza el documento.

```html linenums="1"
<imposter-l fixed>
  <dialog aria-labelledby="message">
    <p id="message">¡Es hora de decidir, sol!</p>
    <button type="button">Sí</button>
    <button type="button">No</button>
  </dialog>
</imposter-l>
```
