# El Stack

## El problema

Los elementos de flujo requieren espacio (a veces denominado _espacio en blanco_) para separarlos física y conceptualmente de los elementos que vienen antes y después de ellos. Este es el propósito de la propiedad `margin`.

Sin embargo, los sistemas de diseño conciben elementos y componentes de forma aislada. Al momento de la concepción, no está establecido si habrá contenido circundante o cuál será la naturaleza de ese contenido. Un elemento o componente probablemente aparecerá en diferentes contextos, y el requisito de espaciado diferirá.

Estamos en la costumbre de estilizar elementos, o clases de elementos, directamente: hacemos declaraciones de estilo que _pertenecen_ a los elementos. Típicamente, esto no produce problemas, pero `margin` es realmente una propiedad de la _relación_ entre dos elementos próximos. Por lo tanto, el siguiente código es problemático:

```css linenums="1"
p {
  margin-bottom: 1.5rem;
}
```

Dado que la declaración no es sensible al contexto, cualquier aplicación correcta del margen es cuestión de suerte. Si el párrafo es sucedido por otro elemento, el efecto es deseable. Pero un párrafo `:last-child` produce un margen redundante. Dentro de un elemento padre con padding, este margen redundante se combina con el padding del padre para producir el doble del espacio previsto. Este es solo un problema que produce este enfoque.

![](produceline.png)

??? info "Explicacion"

    Este texto está explicando un problema muy común en CSS: **¿quién debería ser responsable del espacio entre los elementos?** Vamos paso por paso.

    ---

    __1. ¿Qué es el flujo?__

    Los elementos HTML normalmente se colocan uno debajo del otro. A eso se le llama **flujo normal del documento**.

    Por ejemplo:

    ```html
    <h1>Título</h1>
    <p>Primer párrafo.</p>
    <p>Segundo párrafo.</p>
    <button>Enviar</button>
    ```

    El navegador los acomoda así:

    ```
    Título

    Primer párrafo.

    Segundo párrafo.

    [Enviar]
    ```

    Pero si no hubiera márgenes, quedarían pegados.

    ---

    __2. ¿Para qué sirve `margin`?__

    El `margin` agrega espacio **por fuera** del elemento.

    ```css
    p {
        margin-bottom: 24px;
    }
    ```

    Ahora cada párrafo deja 24px debajo de él.

    ```
    Primer párrafo.
    ↓ 24px

    Segundo párrafo.
    ↓ 24px

    Botón
    ```

    Hasta aquí parece perfecto.

    ---

    __3. ¿Dónde está el problema?__

    El problema es que el párrafo **no sabe** qué viene después.

    Supón este HTML:

    ```html
    <div class="card">
        <p>Hola</p>
    </div>
    ```

    Y este CSS:

    ```css
    .card {
        padding: 20px;
    }

    p {
        margin-bottom: 20px;
    }
    ```

    Visualmente ocurre esto:

    ```
    +------------------------+
    | padding                |
    |                        |
    | Hola                   |
    |                        | ← margin-bottom
    |                        |
    | padding                |
    +------------------------+
    ```

    Hay:

    * 20px de margin del `<p>`
    * 20px de padding del `.card`

    Entonces abajo del texto hay **40px**.

    Cuando probablemente solo querías 20px.

    ---

    __4. El párrafo no sabe si es el último__

    Imagina:

    ```html
    <div>
        <p>Uno</p>
        <p>Dos</p>
    </div>
    ```

    Aquí sí tiene sentido:

    ```
    Uno
    ↓

    Dos
    ```

    Porque el margen separa dos párrafos.

    Pero ahora:

    ```html
    <div>
        <p>Dos</p>
    </div>
    ```

    Ese margen ya no separa nada.

    Solo deja un espacio vacío al final.

    ---

    __5. El problema de diseñar componentes__

    Imagina que creas un componente:

    ```html
    <Card>
        <p>Descripción</p>
    </Card>
    ```

    Mañana alguien usa el mismo componente así:

    ```html
    <Card>
        <p>Descripción</p>
        <button>Comprar</button>
    </Card>
    ```

    Y pasado mañana así:

    ```html
    <Card>
        <img>
        <p>Descripción</p>
        <ul>...</ul>
    </Card>
    ```

    El mismo `<p>` aparece en tres contextos distintos.

    Entonces...

    **¿Cuánto margen debería tener?**

    No existe una respuesta universal.

    ---

    __6. Lo que dice el texto__

    Cuando escribes:

    ```css
    p {
        margin-bottom: 1.5rem;
    }
    ```

    Estás diciendo:

    > "Todos los párrafos del mundo siempre tendrán espacio debajo."

    Pero eso no siempre es correcto.

    Lo correcto sería decir:

    > "Cuando un párrafo tiene otro elemento después, quiero que exista espacio entre ellos."

    Es decir, el espacio pertenece a **la relación entre dos elementos**, no al párrafo en sí.

    ---

    __7. Una mejor forma__

    En lugar de poner margen a todos los párrafos, muchos sistemas modernos hacen algo como:

    ```css
    .stack > * + * {
        margin-top: 1.5rem;
    }
    ```

    Este selector significa:

    > "Todo elemento que venga inmediatamente después de otro tendrá un margen arriba."

    Por ejemplo:

    ```html
    <div class="stack">
        <h1>Título</h1>
        <p>Texto</p>
        <button>Enviar</button>
    </div>
    ```

    Solo reciben margen los elementos **que tienen un hermano anterior**:

    ```
    Título

    Texto

    Botón
    ```

    Pero el último elemento **no deja un margen sobrante al final**, porque el espacio se añade únicamente **entre elementos consecutivos**, no al final del contenedor.

    ---

    __La idea principal__

    La enseñanza más importante de este apartado es esta:

    * ❌ `margin` no debería verse como una propiedad propia de un elemento ("todos los `<p>` tienen margen").
    * ✅ `margin` representa la **separación entre dos elementos vecinos**. Por eso, en sistemas de diseño modernos, suele controlarse desde el **contenedor** (como un `Stack`) o mediante selectores que aplican espacio solo entre hermanos, evitando márgenes redundantes y componentes que se comportan de forma distinta según el contexto.


## La solución

El truco está en estilizar el _contexto_, no el(los) elemento(s) individual(es). La primitiva de layout _Stack_ inyecta margen entre elementos a través de su padre común:

```css linenums="1"
.stack > * + * {
  margin-top: 1.5rem;
}
```

Usando el combinador de hermano adyacente (`+`), `margin-top` solo se aplica donde el elemento está precedido por otro elemento: no hay margen "sobrante". El selector universal (o _wildcard_) `*` asegura que cualquier y todos los elementos se vean afectados. Esta construcción clave se conoce como el [_* + * owl_ ↗](https://alistapart.com/article/axiomatic-css-and-lobotomized-owls/) (_búho_).

??? info "Explicacion"

    Esta es una de las ideas más importantes de los sistemas de diseño modernos. Vamos a desmenuzarla.

    ---

    __¿Qué problema intenta resolver?__

    Antes hacíamos esto:

    ```css
    p {
        margin-bottom: 1.5rem;
    }
    ```

    El problema era que **el párrafo siempre tenía margen**, aunque fuera el último elemento.

    Entonces el espacio sobraba.

    La solución consiste en **no darle margen al elemento**, sino **crear el espacio únicamente cuando existen dos elementos consecutivos**.

    ---

    __La solución__

    ```css
    .stack > * + * {
        margin-top: 1.5rem;
    }
    ```

    A primera vista parece un selector extraño, pero en realidad está formado por tres partes.

    ---

    __Primera parte__

    ```css
    .stack
    ```

    Selecciona un elemento que tenga la clase:

    ```html
    <div class="stack">
    ```

    Por ejemplo:

    ```html
    <div class="stack">
        ...
    </div>
    ```

    Todo lo que ocurra en el selector sucede dentro de ese contenedor.

    ---

    __Segunda parte__

    ```css
    >
    ```

    Es el **combinador de hijo directo**.

    Significa:

    > "Solo quiero los hijos directos."

    Por ejemplo:

    ```html
    <div class="stack">

        <h1></h1>

        <p></p>

    </div>
    ```

    Sí los selecciona.

    Pero:

    ```html
    <div class="stack">

        <section>

            <p></p>

        </section>

    </div>
    ```

    El `<p>` **no** es hijo directo.

    Solo lo es `<section>`.

    ---

    __Tercera parte__

    ```css
    *
    ```

    El asterisco significa:

    > "Cualquier elemento."

    Puede ser:

    * `h1`
    * `p`
    * `button`
    * `img`
    * `section`
    * `ul`

    Todos.

    ---

    Hasta ahora tenemos:

    ```css
    .stack > *
    ```

    Que significa:

    > "Todos los hijos directos de `.stack`."

    ---

    __La parte interesante__

    ```css
    +
    ```

    Es el **combinador de hermano adyacente**.

    Significa:

    > "Selecciona un elemento que esté inmediatamente después de otro."

    Ejemplo:

    ```html
    <h1></h1>
    <p></p>
    <button></button>
    ```

    Aquí:

    * el `<p>` está inmediatamente después del `<h1>`
    * el `<button>` está inmediatamente después del `<p>`

    Por lo tanto ambos cumplen la condición.

    ---

    __¿Y el segundo `*`?__

    ```css
    * + *
    ```

    Se lee así:

    > "Cualquier elemento que esté inmediatamente después de otro elemento."

    Eso significa que **el primer elemento nunca será seleccionado**.

    ---

    __Veámoslo con un ejemplo__

    ```html
    <div class="stack">

        <h1>Título</h1>

        <p>Texto</p>

        <button>Enviar</button>

    </div>
    ```

    El navegador analiza:

    ```
    h1
    ```

    ¿Tiene un hermano antes?

    No.

    No recibe margen.

    ---

    Luego analiza:

    ```
    p
    ```

    ¿Tiene un hermano inmediatamente antes?

    Sí.

    Es el `h1`.

    Entonces recibe:

    ```css
    margin-top: 1.5rem;
    ```

    ---

    Después analiza:

    ```
    button
    ```

    ¿Tiene un hermano antes?

    Sí.

    Es el `p`.

    También recibe margen.

    ---

    Resultado:

    ```
    Título

    Texto

    Botón
    ```

    Observa algo importante:

    ```
    Título
    ↑
    sin margen

    Texto
    ↑
    con margen

    Botón
    ↑
    con margen
    ```

    Solo los elementos que tienen un hermano anterior reciben espacio.

    ---

    __¿Por qué no sobra espacio al final?__

    Porque el margen pertenece al elemento siguiente.

    Visualmente ocurre esto:

    ```
    Título

    Texto

    Botón
    ```

    No ocurre esto:

    ```
    Título

    Texto

    Botón

    ← margen sobrante
    ```

    Nadie está agregando un margen después del botón.

    Simplemente el botón tiene un margen **encima**.

    Por eso nunca aparece espacio adicional debajo del último elemento.

    ---

    __¿Por qué usan `margin-top` y no `margin-bottom`?__

    Podrían hacerlo al revés, pero usar `margin-top` tiene una ventaja muy importante:

    Cada elemento (excepto el primero) es responsable de separarse del anterior.

    Así el espacio siempre está **entre dos elementos**, nunca después del último.

    ---

    __¿Qué es el `* + * owl`?__

    Esta construcción:

    ```css
    * + *
    ```

    es tan útil que tiene un nombre propio:

    > **The Owl Selector** (el selector búho).

    Se llama así porque los símbolos:

    ```css
    * + *
    ```

    recuerdan la cara de un búho:

    ```
    *   *
    \_/
      +
    ```

    Es una técnica muy utilizada en sistemas de diseño como **Every Layout**, porque permite separar automáticamente cualquier tipo de elementos sin importar si son títulos, párrafos, imágenes, botones o listas.

    ---

    __Idea principal__

    Antes pensábamos así:

    ```text
    El párrafo debe tener margen.
    ```

    Ahora pensamos así:

    ```text
    Dos elementos consecutivos deben tener espacio entre ellos.
    ```

    Ese cambio de mentalidad es fundamental. En lugar de asignar el espaciado a un elemento específico, se asigna a la **relación entre elementos hermanos**, logrando componentes más reutilizables, predecibles y sin márgenes sobrantes.

## Altura de línea y escala modular

En el ejemplo anterior, usamos un valor `margin-top` de `1.5rem`. Estamos en la costumbre de usar este valor porque refleja nuestra altura de línea (`line-height`) del texto del cuerpo (generalmente preferida) de `1.5`.

El espaciado vertical de tu diseño debería basarse en tu `line-height` estándar porque el texto domina el layout de la mayoría de las páginas, haciendo que una línea de texto sea un denominador natural.

Si el texto del cuerpo tiene un `line-height` de `1.5` (es decir, `1.5` × el `font-size`), tiene sentido usar `1.5` como la relación para tu escala modular. Lee la _introducción a la escala modular_, y cómo se puede expresar con custom properties de CSS.

![](scalemodular.png)

??? info "Explicacion"

    Este apartado ya no habla del **cómo agregar el espacio**, sino de **cuánto espacio usar**. Vamos paso por paso.

    ---

    __¿Qué dice el texto?__

    En el ejemplo anterior se utilizó:

    ```css
    margin-top: 1.5rem;
    ```

    Y el autor pregunta implícitamente:

    > **¿Por qué precisamente `1.5rem`?**

    La respuesta es que ese valor proviene de la **altura de línea (`line-height`)** del texto.

    ---

    __¿Qué es `line-height`?__

    La propiedad `line-height` controla la distancia entre una línea de texto y la siguiente.

    Por ejemplo:

    ```css
    p {
        font-size: 16px;
        line-height: 1.5;
    }
    ```

    Si el tamaño de letra es:

    ```
    16px
    ```

    Entonces:

    ```
    line-height = 16 × 1.5

    = 24px
    ```

    Cada línea ocupa 24px de alto.

    Visualmente:

    ```
    Primera línea
    ──────────────

    Segunda línea
    ──────────────

    Tercera línea
    ```

    No solo cuenta el tamaño de las letras, sino también el espacio vertical que existe entre ellas.

    ---

    __¿Por qué usar ese mismo valor para los márgenes?__

    Porque casi todas las páginas web están dominadas por texto.

    Piensa en una noticia.

    ```
    Título

    Párrafo

    Párrafo

    Imagen

    Párrafo
    ```

    O un blog.

    ```
    Título

    Subtítulo

    Texto

    Lista

    Texto
    ```

    O la documentación que estás leyendo.

    Todo gira alrededor del texto.

    Por eso el autor dice:

    > La altura de una línea de texto es una excelente unidad para construir todo el espaciado vertical.

    ---

    __Ejemplo__

    Si tu CSS es:

    ```css
    body {
        font-size: 16px;
        line-height: 1.5;
    }
    ```

    Entonces una línea mide:

    ```
    24px
    ```

    En lugar de inventarte valores como:

    ```css
    margin-top: 17px;
    ```

    o

    ```css
    margin-top: 21px;
    ```

    usas:

    ```css
    margin-top: 1.5rem;
    ```

    Así el espacio entre bloques coincide con la altura de una línea de texto.

    Todo se ve más equilibrado.

    ---

    __¿Qué es una escala modular?__

    Aquí aparece un concepto nuevo.

    Una **escala modular** es un conjunto de tamaños que mantienen una proporción constante entre sí.

    Por ejemplo:

    ```
    1
    1.5
    2.25
    3.375
    5.0625
    ...
    ```

    Observa que cada número se obtiene multiplicando el anterior por **1.5**.

    ```
    1 × 1.5 = 1.5

    1.5 × 1.5 = 2.25

    2.25 × 1.5 = 3.375
    ```

    Eso es una escala modular.

    ---

    __¿Para qué sirve?__

    En lugar de elegir tamaños al azar:

    ```
    12px

    19px

    31px

    47px

    83px
    ```

    Todos los tamaños siguen una misma relación.

    Por ejemplo:

    ```
    16px

    24px

    36px

    54px

    81px
    ```

    Todo mantiene armonía visual.

    ---

    __También sirve para los espacios__

    No solo para el texto.

    También puedes usar la misma escala para:

    * márgenes;
    * paddings;
    * separación entre secciones;
    * tamaños de títulos;
    * tamaños de iconos.

    Por ejemplo:

    ```
    1rem

    1.5rem

    2.25rem

    3.375rem
    ```

    Todos pertenecen a la misma familia de tamaños.

    ---

    __¿Qué son las *Custom Properties*?__

    En lugar de escribir los valores una y otra vez:

    ```css
    margin-top: 1.5rem;

    padding: 1.5rem;

    gap: 1.5rem;
    ```

    Puedes definirlos una sola vez.

    ```css
    :root {
        --space: 1.5rem;
    }
    ```

    Y luego reutilizarlos:

    ```css
    .stack > * + * {
        margin-top: var(--space);
    }

    .card {
        padding: var(--space);
    }

    .grid {
        gap: var(--space);
    }
    ```

    Si algún día decides que el espacio debe ser mayor:

    ```css
    --space: 2rem;
    ```

    Todo el sitio se actualiza automáticamente.

    ---

    __Idea principal__

    El autor propone una filosofía sencilla:

    * Como el **texto es el elemento predominante** en la mayoría de interfaces, la **altura de una línea de texto (`line-height`)** es una excelente unidad base para construir el espaciado vertical.
    * En lugar de elegir márgenes y tamaños de forma arbitraria, utiliza una **escala modular**, donde todos los valores mantienen una misma proporción.
    * Centraliza esos valores mediante **CSS Custom Properties**, para conseguir un diseño consistente, fácil de mantener y de ajustar en el futuro.

## Recursión

En el ejemplo anterior, el combinador de hijo (`>`) asegura que los márgenes solo se apliquen a los hijos del elemento `.stack`. Sin embargo, es posible inyectar márgenes recursivamente eliminando este combinador del selector.

```css linenums="1"
.stack * + * {
  margin-top: 1.5rem;
}
```

Esto puede ser útil donde quieras afectar elementos en cualquier nivel de anidamiento, manteniendo la regularidad del espacio en blanco. 

![](recursion.png)

En la siguiente demostración (usando el componente _Stack_ para seguir) hay un conjunto de elementos con forma de caja. Dos de estos están anidados dentro de otro. Debido a que se aplica recursión, cada caja está espaciada uniformemente usando solo un `Stack` padre.

!!! info 

    [This interactive demo is only available on the Every Layout site ↗.](https://every-layout.dev/demos/stack-recursion/)

    Es probable que encuentres que el modo recursivo afecta elementos no deseados. Por ejemplo, los elementos de lista genéricos que típicamente no están separados por márgenes se dispersarán inesperadamente.


??? info "Explicacion"

    Este apartado explica una variante del selector anterior. La idea principal es: **¿quieres aplicar el espaciado solo a los hijos directos o a todos los elementos, incluso los que están anidados?**

    ---

    __¿Qué cambia?__

    Antes teníamos:

    ```css
    .stack > * + * {
        margin-top: 1.5rem;
    }
    ```

    Ahora queda así:

    ```css
    .stack * + * {
        margin-top: 1.5rem;
    }
    ```

    Parece un cambio pequeño (desaparece el `>`), pero cambia completamente el comportamiento.

    ---

    __Recordemos qué hacía `>`__

    El símbolo:

    ```css
    >
    ```

    significa:

    > **Selecciona únicamente los hijos directos.**

    Por ejemplo:

    ```html
    <div class="stack">
        <h1>Título</h1>

        <section>
            <p>Texto</p>
        </section>

        <button>Enviar</button>
    </div>
    ```

    La estructura es:

    ```
    .stack
    ├── h1
    ├── section
    │   └── p
    └── button
    ```

    Con este selector:

    ```css
    .stack > * + *
    ```

    Solo se consideran:

    * `h1`
    * `section`
    * `button`

    El `<p>` queda fuera porque **no es hijo directo** de `.stack`.

    ---

    __¿Qué ocurre al quitar el `>`?__

    Ahora usamos:

    ```css
    .stack * + *
    ```

    El selector ya no dice:

    > "Hijos directos."

    Ahora dice:

    > **"Cualquier elemento que esté dentro de `.stack`."**

    Eso incluye:

    ```
    .stack
    ├── h1
    ├── section
    │   ├── p
    │   ├── img
    │   └── button
    └── footer
    ```

    Todos esos elementos pueden recibir el margen.

    ---

    __¿Por qué se llama recursión?__

    Porque el selector entra en **todos los niveles de anidamiento**.

    No importa cuántos niveles existan.

    Ejemplo:

    ```html
    <div class="stack">

        <section>

            <article>

                <div>

                    <p>Hola</p>

                </div>

            </article>

        </section>

    </div>
    ```

    Aunque el `<p>` esté muy profundo, el selector sigue alcanzándolo.

    Es como si recorriera todo el árbol del DOM.

    ---

    __Ejemplo visual__

    Sin recursión:

    ```
    .stack

    Caja 1

    Caja 2
        Caja 2.1
        Caja 2.2

    Caja 3
    ```

    Solo se separan:

    ```
    Caja 1

    Caja 2

    Caja 3
    ```

    Las cajas internas mantienen su propio comportamiento.

    ---

    Con recursión:

    ```
    Caja 1

    Caja 2

        Caja 2.1

        Caja 2.2

    Caja 3
    ```

    Ahora también existe separación entre:

    * Caja 2.1
    * Caja 2.2

    aunque estén dentro de otra caja.

    ---

    __¿Por qué puede ser útil?__

    Imagina que estás escribiendo un artículo.

    ```html
    <div class="stack">

        <h1>Título</h1>

        <section>

            <p>Texto...</p>

            <img>

            <blockquote>

                <p>Cita...</p>

            </blockquote>

        </section>

    </div>
    ```

    Gracias a la recursión, **todo el contenido mantiene un espaciado uniforme**, sin importar el nivel donde se encuentre.

    No necesitas crear reglas específicas para cada contenedor.

    ---

    __¿Cuál es el problema?__

    El propio texto advierte que esta técnica puede afectar elementos que **no quieres separar**.

    Por ejemplo, una lista.

    ```html
    <ul>

        <li>Uno</li>

        <li>Dos</li>

        <li>Tres</li>

    </ul>
    ```

    Con el selector recursivo:

    ```css
    .stack * + * {
        margin-top: 1.5rem;
    }
    ```

    Cada `<li>` recibe un margen superior.

    El resultado sería algo parecido a esto:

    ```
    • Uno


    • Dos


    • Tres
    ```

    Cuando normalmente esperamos una lista más compacta.

    Lo mismo puede ocurrir con:

    * elementos de un menú;
    * botones agrupados;
    * tablas;
    * formularios;
    * otros componentes que ya gestionan su propio espaciado.

    ---

    __¿Cuándo usar cada uno?__

    __Usa hijos directos (`>`)__

    ```css
    .stack > * + *
    ```

    Cuando quieras controlar únicamente el espaciado entre los elementos principales de un contenedor.

    Es la opción más segura y la que se utiliza con mayor frecuencia.

    ---

    __Usa el modo recursivo__

    ```css
    .stack * + *
    ```

    Cuando quieras que **todo el contenido**, sin importar su nivel de anidamiento, siga la misma regla de espaciado vertical.

    Es muy útil para documentos largos, artículos o contenido generado dinámicamente.

    ---

    __Idea principal__

    La diferencia entre ambos selectores es sencilla:

    * `> * + *` → aplica el margen **solo a los hijos directos** del contenedor.
    * `* + *` → aplica el margen **a cualquier elemento dentro del contenedor**, incluso si está anidado varios niveles.

    La versión recursiva consigue un espaciado uniforme en todo el árbol del DOM, pero puede introducir márgenes en elementos que normalmente no deberían tenerlos, como los elementos de una lista (`<li>`). Por eso debe usarse con cuidado.

## Variantes anidadas

La recursión aplica el mismo margen sin importar la profundidad de anidamiento. Un enfoque más deliberado sería configurar `Stacks` no recursivos alternativos con diferentes valores de margen, y anidarlos donde sea adecuado. Considera lo siguiente:

```css linenums="1"
[class^='stack'] > * {
  margin-top: 0;
  margin-bottom: 0;
}
.stack-large > * + * {
  margin-top: 3rem;
}
.stack-small > * + * {
  margin-top: 0.5rem;
}
```

!!! info

    [This interactive demo is only available on the Every Layout site ↗](https://every-layout.dev/demos/stack-variants/)

El selector del primer bloque de declaración restablece el margen vertical para todos los elementos similares a `stack` (coincidiendo con valores de clase que _comienzan_ con `stack`). Importantemente, solo se restablecen los márgenes verticales, porque el stack solo _afecta_ el margen vertical, y no queremos que alcance fuera de su competencia. Puede que no necesites este restablecimiento si un restablecimiento universal para `margin` ya está en su lugar (ver _Global and local styling_).

Los siguientes dos bloques configuran `Stacks` alternativos, con diferentes valores de margen. Estos pueden anidarse para producir — por ejemplo — el layout de formulario ilustrado. Ten en cuenta que los elementos `<label>` necesitarían tener `display: block` aplicado para aparecer arriba de los inputs, y para que sus márgenes realmente produzcan espacios (el margen vertical de los elementos inline no tiene efecto; ver _The display property_).

![](formito.png)

En _Every Layout_, se usan custom elements para implementar componentes/primitivas de layout como el _Stack_. En el componente _Stack_, la prop (propiedad; atributo) `space` se usa para definir el valor de espaciado. El ejemplo de clases modificadas anterior es solo para ilustración. Ver el _ejemplo anidado_.

??? info "Explicacion"

    Este apartado explica cómo resolver un problema de la recursión: **no todo el contenido necesita la misma separación**. En lugar de aplicar un único margen a todos los niveles, puedes crear **Stacks con diferentes tamaños de espaciado** y anidarlos según la necesidad.

    ---

    __¿Cuál es el problema de la recursión?__

    Recordemos el selector recursivo:

    ```css
    .stack * + * {
        margin-top: 1.5rem;
    }
    ```

    Este aplica **el mismo margen** a todos los elementos, sin importar dónde estén.

    Por ejemplo:

    ```html
    <div class="stack">

        <h1>Título</h1>

        <section>

            <p>Texto</p>

            <button>Aceptar</button>

        </section>

    </div>
    ```

    El espacio entre:

    * `Título` y `section`
    * `Texto` y `button`

    será exactamente el mismo.

    Pero muchas veces eso no es lo que queremos.

    ---

    __¿Por qué?__

    Porque distintos grupos de elementos necesitan distintos espacios.

    Por ejemplo:

    * Entre secciones de una página → mucho espacio.
    * Entre un `<label>` y su `<input>` → poco espacio.

    No tendría sentido usar el mismo margen para ambos casos.

    ---

    __La solución__

    Crear varios tipos de **Stack**.

    ```css
    .stack-large > * + * {
        margin-top: 3rem;
    }

    .stack-small > * + * {
        margin-top: 0.5rem;
    }
    ```

    Ahora existen dos variantes.

    __Stack grande__

    ```css
    .stack-large
    ```

    Espacio:

    ```
    3rem
    ```

    Ideal para separar secciones completas.

    ---

    __Stack pequeño__

    ```css
    .stack-small
    ```

    Espacio:

    ```
    0.5rem
    ```

    Ideal para elementos relacionados entre sí.

    ---

    __¿Qué hace este selector?__

    ```css
    [class^='stack'] > * {
        margin-top: 0;
        margin-bottom: 0;
    }
    ```

    Es probablemente la parte más extraña del ejemplo.

    ---

    __`[class^='stack']`__

    Significa:

    > Selecciona cualquier elemento cuya clase **empiece por** `"stack"`.

    Por ejemplo:

    ```html
    <div class="stack"></div>

    <div class="stack-small"></div>

    <div class="stack-large"></div>

    <div class="stack-form"></div>
    ```

    Todos coinciden.

    Pero esto:

    ```html
    <div class="card"></div>
    ```

    No coincide.

    ---

    __¿Qué hace el bloque?__

    ```css
    [class^='stack'] > * {

        margin-top: 0;

        margin-bottom: 0;

    }
    ```

    Elimina cualquier margen vertical que ya tengan los hijos.

    ¿Por qué?

    Porque muchos elementos HTML ya traen márgenes por defecto.

    Por ejemplo:

    ```html
    <h1></h1>

    <p></p>

    <ul></ul>
    ```

    Los navegadores les agregan márgenes automáticamente.

    Si no los eliminas, terminarías con:

    ```
    margen del navegador

    +

    margen del Stack
    ```

    Y el espacio sería impredecible.

    Por eso primero se reinician los márgenes y luego el Stack agrega exactamente el espacio que desea.

    ---

    __¿Por qué solo reinicia los márgenes verticales?__

    Observa:

    ```css
    margin-top: 0;

    margin-bottom: 0;
    ```

    No hace esto:

    ```css
    margin: 0;
    ```

    ¿Por qué?

    Porque el Stack solo controla el espaciado vertical.

    No quiere modificar:

    * `margin-left`
    * `margin-right`

    Eso pertenece a otros componentes o layouts.

    Cada componente debe responsabilizarse únicamente de aquello que controla.

    ---

    __Ejemplo práctico__

    Imagina un formulario.

    ```html
    <form class="stack-large">

        <section class="stack-small">

            <label>Nombre</label>

            <input>

        </section>

        <section class="stack-small">

            <label>Email</label>

            <input>

        </section>

        <button>Guardar</button>

    </form>
    ```

    Visualmente:

    ```
    Nombre
    [input]


    Email
    [input]



    [Guardar]
    ```

    Observa que:

    * Entre `label` e `input` hay poco espacio (`0.5rem`).
    * Entre cada grupo del formulario hay mucho más espacio (`3rem`).

    Todo esto se consigue simplemente anidando distintos Stacks.

    ---

    __¿Por qué menciona `display: block` en los `<label>`?__

    Los `<label>` son elementos **inline** por defecto.

    ```html
    <label>Nombre</label>
    <input>
    ```

    Se muestran así:

    ```
    Nombre [________]
    ```

    Si queremos que aparezcan uno encima del otro:

    ```
    Nombre

    [________]
    ```

    Debemos convertir el `label` en un elemento de bloque.

    ```css
    label {
        display: block;
    }
    ```

    Además, los elementos **inline** ignoran los márgenes verticales (`margin-top` y `margin-bottom`), por lo que convertirlos en `block` permite que el espaciado del Stack funcione correctamente.

    ---

    __¿Qué significa el último párrafo?__

    El autor aclara que este ejemplo usa clases como:

    ```css
    .stack-small

    .stack-large
    ```

    Solo para facilitar la explicación.

    En **Every Layout**, realmente se utiliza un componente llamado **Stack**, al que se le pasa una propiedad (`space`) para indicar cuánto espacio debe haber.

    La idea conceptual es la misma: definir el espaciado desde el contenedor, pero la implementación es más flexible.

    ---

    __Idea principal__

    En lugar de usar un único Stack recursivo para todo el documento, es mejor crear **variantes de Stack** con diferentes tamaños de espaciado (por ejemplo, grande y pequeño) y anidarlas según el contexto. De este modo, cada grupo de elementos recibe la separación adecuada, el diseño resulta más consistente y el control del espaciado sigue estando centralizado en los contenedores en lugar de en los elementos individuales.

## Excepciones

CSS funciona mejor como un lenguaje basado en excepciones. Escribes reglas de gran alcance, luego usas la cascada para anular estas reglas en casos especiales. Como está escrito en [_Managing Flow and Rhythm with CSS Custom Properties_ ↗](https://24ways.org/2018/managing-flow-and-rhythm-with-css-custom-properties/), puedes crear excepciones por elemento dentro de un solo contexto (es decir, en el mismo nivel de anidamiento).

```css linenums="1"
.stack > * + * {
  margin-top: var(--space, 1.5em);
}
.stack-exception,
.stack-exception + * {
  --space: 3rem;
}
```

Observa que estamos aplicando el espaciado aumentado _por encima y por debajo_ del elemento `.stack-exception`, donde sea aplicable. Si solo quisieras aumentar el espacio _por encima_, eliminarías `.stack-exception + *`.

Esto funciona porque `*` tiene especificidad _cero_, así que `.stack > * + *` y `.stack-exception` tienen la misma especificidad y `.stack-exception` anula en la cascada (al aparecer más abajo en la hoja de estilo).

??? info "Explicacion"

      Este apartado introduce una idea muy importante de CSS: **no escribas muchas reglas específicas desde el principio; escribe una regla general y crea excepciones cuando sean necesarias.**

      ---

      __¿Qué significa "CSS funciona mejor como un lenguaje basado en excepciones"?__

      La filosofía es esta:

      1. Escribes una regla que funcione para el **90% de los casos**.
      2. Solo cuando aparece un caso especial, lo modificas.

      Es mejor hacer esto:

      ```css
      .stack > * + * {
          margin-top: 1.5rem;
      }
      ```

      Que esto:

      ```css
      .titulo {
          margin-top: 3rem;
      }

      .parrafo {
          margin-top: 1.5rem;
      }

      .lista {
          margin-top: 2rem;
      }

      .imagen {
          margin-top: 4rem;
      }

      /* ...y así con decenas de reglas */
      ```

      La primera opción es mucho más fácil de mantener.

      ---

      __La regla general__

      ```css
      .stack > * + * {
          margin-top: var(--space, 1.5em);
      }
      ```

      Aquí aparece algo nuevo:

      ```css
      var(--space, 1.5em)
      ```

      ---

      __¿Qué significa `var()`?__

      `var()` obtiene el valor de una **Custom Property**.

      ```css
      --space
      ```

      Si existe:

      ```css
      --space: 3rem;
      ```

      Entonces:

      ```css
      margin-top: var(--space);
      ```

      equivale a:

      ```css
      margin-top: 3rem;
      ```

      ---

      __¿Y el `1.5em`?__

      Es el **valor por defecto**.

      ```css
      var(--space, 1.5em)
      ```

      Se lee así:

      > Usa `--space`.

      Pero...

      > Si `--space` no existe, usa `1.5em`.

      Es exactamente igual a decir:

      ```
      ¿Existe --space?

      Sí → úsalo.

      No → usa 1.5em.
      ```

      ---

      __Ahora aparece la excepción__

      ```css
      .stack-exception,
      .stack-exception + * {
          --space: 3rem;
      }
      ```

      Aquí no se modifica directamente el margen.

      Solo cambia el valor de:

      ```css
      --space
      ```

      Y como el margen depende de esa variable...

      ```css
      margin-top: var(--space);
      ```

      El margen cambia automáticamente.

      ---

      __Veamos un ejemplo__

      ```html
      <div class="stack">

          <h1>Título</h1>

          <p>Introducción</p>

          <hr class="stack-exception">

          <p>Nuevo capítulo</p>

      </div>
      ```

      Por defecto:

      ```
      Título

      Introducción

      <hr>

      Nuevo capítulo
      ```

      Todos tienen:

      ```
      1.5em
      ```

      de separación.

      ---

      Pero cuando el navegador encuentra:

      ```html
      <hr class="stack-exception">
      ```

      Sucede esto:

      ```css
      --space: 3rem;
      ```

      Entonces el espacio cambia.

      Resultado:

      ```
      Título

      Introducción


      ──────────────


      Nuevo capítulo
      ```

      El separador (`<hr>`) queda más aislado visualmente.

      ---

      __¿Por qué hay dos selectores?__

      ```css
      .stack-exception,
      .stack-exception + *
      ```

      El primero:

      ```css
      .stack-exception
      ```

      Afecta al propio elemento.

      El segundo:

      ```css
      .stack-exception + *
      ```

      Afecta al elemento inmediatamente siguiente.

      Entonces:

      ```html
      <p>Texto</p>

      <hr class="stack-exception">

      <p>Otro texto</p>
      ```

      Visualmente:

      ```
      Texto


      <hr>


      Otro texto
      ```

      Hay espacio:

      * antes del `<hr>`;
      * después del `<hr>`.

      ---

      __¿Y si solo quiero espacio arriba?__

      Entonces eliminarías:

      ```css
      .stack-exception + *
      ```

      Quedaría:

      ```css
      .stack-exception {
          --space: 3rem;
      }
      ```

      Ahora solo aumenta el espacio antes del elemento especial.

      ---

      __¿Por qué funciona?__

      El texto habla de la **especificidad**.

      Observa la regla principal:

      ```css
      .stack > * + *
      ```

      El selector universal:

      ```css
      *
      ```

      tiene **especificidad 0**.

      En cambio:

      ```css
      .stack-exception
      ```

      es una clase.

      Las clases tienen más peso que `*`.

      Además, la regla aparece **después** en la hoja de estilos.

      Cuando dos reglas tienen la misma prioridad, CSS aplica la última que encuentra.

      Por eso:

      ```css
      --space: 3rem;
      ```

      reemplaza el valor anterior.

      ---

      __¿Por qué modificar la variable y no el margen?__

      Podríamos hacer esto:

      ```css
      .stack-exception {
          margin-top: 3rem;
      }
      ```

      Pero eso rompe la filosofía del Stack.

      La idea es que **el Stack siga siendo quien controla el espaciado**.

      Las excepciones solo cambian el valor que usa el Stack.

      Es mucho más flexible.

      ---

      __Idea principal__

      En lugar de escribir reglas especiales para modificar directamente los márgenes, el Stack utiliza una **Custom Property** (`--space`) como fuente del espaciado. La regla general aplica ese valor a todos los elementos y, cuando aparece un caso especial, solo se cambia el valor de la variable para ese elemento (o para el siguiente). De esta manera, el mecanismo de espaciado sigue siendo el mismo y las excepciones se integran de forma limpia gracias a la cascada y la especificidad de CSS.

## Dividiendo el stack

Al hacer del `Stack` un contexto Flexbox, podemos darle un poder final: la capacidad de agregar un margen `auto` a un elemento elegido. De esta manera, podemos agrupar elementos en la parte superior e inferior del espacio vertical. Útil para componentes tipo tarjeta.

En el siguiente ejemplo, hemos elegido agrupar elementos _después_ del segundo elemento hacia la parte inferior del espacio.

```css linenums="1"
.stack {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}
.stack > * + * {
  margin-top: var(--space, 1.5rem);
}
.stack > :nth-child(2) {
  margin-bottom: auto;
}
```

## Colocación de la custom property

Importantemente, a pesar de que ahora establecemos algunas propiedades en el elemento padre, todavía estamos estableciendo el valor `--space` en los hijos, no "subiéndolo" al padre. Si el padre es donde se establece la propiedad, se anulará si el padre se convierte en hijo en anidamiento (ver _Variantes anidadas_, arriba).

Esto se puede ver funcionando en contexto en la siguiente demo que representa un editor de presentaciones/diapositivas. El elemento `Cover` a la derecha tiene una altura mínima de `66.666vh`, forzando la altura de la barra lateral izquierda a ser más alta que su contenido. Esto es lo que produce el espacio entre las imágenes de las diapositivas y el botón "Add slide".

Donde el `Stack` es el único hijo de su padre, nada lo fuerza a estirarse como en el último ejemplo/demo. Un `height` de `100%` asegura que la altura del `Stack` iguale la del padre y la división pueda ocurrir.

```css linenums="1"
.stack:only-child {
  height: 100%;
}
```

## Casos de uso

El posible alcance del layout `Stack` difícilmente puede sobreestimarse. En cualquier lugar donde los elementos se apilen uno encima de otro, es probable que un `Stack` debería estar en efecto. Solo los elementos adyacentes (como las celdas de cuadrícula) no deberían estar sujetos a un `Stack`. Las celdas de la cuadrícula _son_ probablemente `Stacks`, sin embargo, y la cuadrícula misma un miembro de un `Stack`.

![](grid.png)

## El generador

La herramienta generadora de código solo está disponible en _el sitio de documentación adjunto_ ↗. Aquí está la solución básica, con comentarios:

### CSS

```css linenums="1"
.stack {
  /* ↓ El contexto flex */
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}
.stack > * {
  /* ↓ Los márgenes verticales existentes se eliminan */
  margin-top: 0;
  margin-bottom: 0;
}
.stack > * + * {
  /* ↓ El margen superior solo se aplica a elementos sucesivos */
  margin-top: var(--space, 1.5rem);
}
```

### HTML

```html
<div class="stack">
  <div><!-- child --></div>
  <div><!-- child --></div>
  <div><!-- etc --></div>
</div>
```

### El componente

Una implementación de custom element del Stack está disponible para _descargar_ ↗.

### Props API

Las siguientes props (atributos) harán que el componente se vuelva a renderizar cuando se alteren. Pueden alterarse a mano — en las herramientas de desarrollo del navegador — o como sujetos del estado de la aplicación heredada.

![](table.png)

## Ejemplos

### Básico

```html
<stack-l>
  <h2><!-- algún texto --></h2>
  <img src="path/to/some/image.svg" />
  <p><!-- más texto --></p>
</stack-l>
```

### Anidado

```html
<stack-l space="3rem">
  <h2><!-- etiqueta de encabezado --></h2>
  <stack-l space="1.5rem">
    <p><!-- texto del cuerpo --></p>
    <p><!-- texto del cuerpo --></p>
    <p><!-- texto del cuerpo --></p>
  </stack-l>
  <h2><!-- etiqueta de encabezado --></h2>
  <stack-l space="1.5rem">
    <p><!-- texto del cuerpo --></p>
    <p><!-- texto del cuerpo --></p>
    <p><!-- texto del cuerpo --></p>
  </stack-l>
</stack-l>
```

### Recursivo

```html
<stack-l recursive>
  <div><!-- hijo de primer nivel --></div>
  <div><!-- hermano de primer nivel --></div>
  <div>
    <div><!-- hijo de segundo nivel --></div>
    <div><!-- hermano de segundo nivel --></div>
  </div>
</stack-l>
```

### Semántica de lista

En algunos casos, los navegadores deberían interpretar el `Stack` como una lista para el software de lector de pantalla. Puedes usar la siguiente atribución ARIA para lograr esto.

```html
<stack-l role="list">
  <div role="listitem"><!-- contenido del elemento 1 --></div>
  <div role="listitem"><!-- contenido del elemento 2 --></div>
  <div role="listitem"><!-- contenido del elemento 3 --></div>
</stack-l>
```
