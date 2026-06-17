# Estilos globales y locales

En la sección _Composición_ cubrimos cómo componentes pequeños y _no léxicos_ para layout pueden usarse para crear composites más grandes, pero no todos los estilos dentro de un sistema de diseño eficiente y consistente basado en CSS deberían ser estrictamente basados en componentes. Esta sección contextualizará los componentes de layout en un sistema más grande que incluye estilos globales.

## ¿Qué son los estilos globales?

Cuando la gente habla sobre la naturaleza _global_ de CSS, pueden referirse a una de varias cosas diferentes. Pueden estar refiriéndose a reglas en los selectores `:root` o `<body>` que se _heredan_ globalmente (con solo algunas excepciones).

```css linenums="1"
:root {
  /* ↓ Ahora (casi) todos los elementos muestran una fuente sans-serif */
  font-family: sans-serif;
}
```

Alternativamente, pueden referirse a usar el [_selector universal_ `*` ↗](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/Universal_selectors)  para estilizar todos los elementos _directamente_.

```css linenums="1"
* {
  /* ↓ Ahora literalmente todos los elementos muestran una fuente sans-serif */
  font-family: sans-serif;
}
```

Los selectores de elemento son más específicos, y solo apuntan a los elementos que nombran. Pero siguen siendo "globales" porque pueden _alcanzar_ esos elementos dondequiera que estén situados.

```css linenums="1"
p {
  /* ↓ Dondequiera que pongas un párrafo, será sans-serif */
  font-family: sans-serif;
}
```

Un uso liberal de selectores de elemento es la característica distintiva de un sistema de diseño integral. Los selectores de elemento se encargan de  [_átomos_ ↗](https://bradfrost.com/blog/post/atomic-web-design/#atoms) genéricos como encabezados, párrafos, enlaces y botones. A diferencia de cuando se usan clases (ver más abajo), los selectores de elemento pueden apuntar al contenido arbitrario y no atribuido producido por editores [_WYSIWYG_↗](https://en.wikipedia.org/wiki/WYSIWYG)  y [_markdown_ ↗](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

Los layouts de _Every Layout_ no exploran ni prescriben estilos para elementos simples; eso es para que tú lo decidas. Es la _imbricación_ de elementos simples en layouts compuestos lo que nos interesa aquí.

![](layout.png)


> Cada layout requiere un elemento contenedor que establece un _contexto de formato_ para sus hijos. Los elementos simples, sin hijos para los cuales establecen un contexto, pueden considerarse como 'nodos finales' en la jerarquía de layout.

Finalmente, los estilos basados en clases, una vez definidos, pueden adherirse a cualquier elemento HTML, en cualquier lugar de un documento. Estos son más portátiles y componibles que los estilos de elemento, pero requieren que el autor afecte el marcado directamente.

```css linenums="1"
.sans-serif {
  font-family: sans-serif;
}
```

```html
<div class="sans-serif">...</div>
<small class="sans-serif">...</small>
<h2 class="sans-serif">...</h2>
```

Debe apreciarse lo importante que es aprovechar el alcance global de las reglas CSS. CSS te permite aplicar estilos de HTML globalmente, y por categoría _(por tipo de elemento, clase, atributo, estado, etc..)_, en lugar de elemento por elemento. Cuando se utiliza correctamente, es la forma más eficiente de crear cualquier tipo de layout o estética en la web. Cuando las técnicas de estilo global (como las anteriores) se usan apropiadamente, es mucho más fácil separar la marca/estética del layout, y tratar los dos como [_concerns separados_ ↗](https://es.wikipedia.org/wiki/Separaci%C3%B3n_de_intereses).

!!! Example

    __Ejemplo de seccion [Estilo Global↗](../../examples/Global&Local/estiloglobal.html)__

    ```html linenums="1"
    <h1 class="gill-sans">Title News</h1>
    <p class="gill-sans">Lorem ipsum dolor sit amet consectetur adipisicing elit. Quod at esse reprehenderit quo ipsa dolor, qui architecto veritatis optio, praesentium sunt ut rerum assumenda dolorem nostrum culpa eos consequatur inventore?</p>
    <button class="gill-sans">See news</button>
    ```
    ```css linenums="1"
    .gill-sans {
            font-family:'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
        }
    ```




??? info "Explicacion"

    Ese párrafo está hablando de una de las ideas más importantes de CSS: **aprovechar que una sola regla puede afectar a muchos elementos al mismo tiempo**.

    Por ejemplo, en lugar de hacer esto:

    ```html
    <h1 style="color: blue;">Título 1</h1>
    <h1 style="color: blue;">Título 2</h1>
    <h1 style="color: blue;">Título 3</h1>
    ```

    CSS permite hacer:

    ```css
    h1 {
        color: blue;
    }
    ```

    Y automáticamente **todos los `<h1>` serán azules**. Eso es lo que llaman *alcance global*.

    ---

    __Aplicar estilos por categoría__

    También puedes agrupar elementos mediante clases:

    ```css
    .boton {
        background-color: blue;
        color: white;
        padding: 1rem;
    }
    ```

    ```html
    <button class="boton">Guardar</button>
    <a class="boton">Descargar</a>
    <input class="boton" type="submit">
    ```

    Una sola regla sirve para muchos elementos.

    ---

    __Separar estética y layout__

    La frase:

    > separar la marca/estética del layout

    significa que puedes tratar por separado:

    __1. Layout (estructura)__

    Cómo se acomodan los elementos:

    ```css
    .container {
        display: flex;
        gap: 1rem;
    }
    ```

    Esto controla la disposición.

    ---

    __2. Estética o marca__

    Cómo se ven:

    ```css
    .card {
        background: white;
        border-radius: 10px;
        color: #333;
    }
    ```

    Esto controla la apariencia.

    ---

    De esta forma, si mañana quieres cambiar los colores de toda tu página, no necesitas modificar cada elemento uno por uno. Simplemente cambias:

    ```css
    body {
        color: #222;
        background: #fafafa;
    }
    ```

    o

    ```css
    .boton {
        background-color: green;
    }
    ```

    y todo el sitio cambia.

    ---

    Cuando el texto dice que ambos son **concerns separados**, quiere decir que:

    * **Layout** → cómo se organizan los elementos.
    * **Estética (branding)** → cómo lucen los elementos.

    Mantener estas responsabilidades separadas hace que el CSS sea más limpio, reutilizable y fácil de mantener.

    Por ejemplo, una tarjeta podría tener:

    ```css
    .card {
        display: flex;          /* Layout */
        flex-direction: column; /* Layout */

        background: white;      /* Estética */
        border-radius: 8px;     /* Estética */
        box-shadow: 0 0 10px #0002; /* Estética */
    }
    ```

    Aunque muchos desarrolladores prefieren incluso separar esas responsabilidades en clases distintas:

    ```css
    .flex-column {
        display: flex;
        flex-direction: column;
    }

    .surface {
        background: white;
        border-radius: 8px;
        box-shadow: 0 0 10px #0002;
    }
    ```

    Así puedes combinar estructura y apariencia según sea necesario, aprovechando al máximo la naturaleza global y reutilizable de CSS.

![](separete.png)

## Clases de utilidad

Como ya dijimos, las clases difieren de los otros métodos de estilo global en términos de su portabilidad: puedes usar clases entre diferentes elementos HTML y sus tipos. Esto nos permite _diverger_ de los estilos heredados, universales y de elemento _globalmente_.

Por ejemplo, todos nuestros elementos `<h2>` pueden estar estilizados, por defecto, con un `font-size` de `2.25rem`:

```css linenums="1"
h2 {
  font-size: 2.25rem;
}
h3 {
  font-size: 1.75rem;
}
```

Sin embargo, puede haber casos específicos donde queramos que ese `font-size` se reduzca ligeramente (quizás el espacio horizontal es limitado, o el encabezado está en un lugar donde debería tener menos peso visual). Si cambiáramos a un elemento `<h3>` para afectar este cambio visual, [_haríamos una tontería de la estructura del documento_ ↗](https://webaim.org/techniques/semanticstructure/#correctly).

En su lugar, podríamos construir un selector más complejo que pertenezca al contexto del `<h2>` más pequeño:

```css linenums="1"
.sidebar h2 {
  font-size: 1.75rem;
}
```

Si bien esto es mejor que desordenar la estructura del documento, hemos cometido el error de no tener en cuenta el sistema emergente: Hemos resuelto el problema para un elemento específico, en un contexto específico, cuando deberíamos estar resolviendo el problema general (necesidad de ajustar/reducir `font-size`) para _cualquier_ elemento en _cualquier_ contexto. Aquí es donde entran las clases de utilidad.

```css linenums="1"
/* ↓ Barra invertida para escapar los dos puntos */
.font-size\:base {
  font-size: 1rem;
}
.font-size\:biggish {
  font-size: 1.75rem;
}
.font-size\:big {
  font-size: 2.25rem;
}
```

Usamos una convención de nomenclatura muy _precisa_, que emula la estructura de declaración CSS: `property-name:value`. Esto ayuda con el recuerdo de los nombres de las clases de utilidad, especialmente donde el valor se hace eco del valor real, como `.text-align:center`.

Compartir valores entre elementos y utilidades es un trabajo para las propiedades personalizadas [(custom properties_ ↗)](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/--*). Observa que hemos hecho las propiedades personalizadas mismas globalmente disponibles adjuntándolas al elemento `:root` (`<html>`):

```css linenums="1"
:root {
  --font-size-base: 1rem;
  --font-size-biggish: 1.75rem;
  --font-size-big: 2.25rem;
}
/* elementos */
h3 {
  font-size: var(--font-size-biggish);
}
h2 {
  font-size: var(--font-size-big);
}
/* utilidades */
.font-size\:base {
  font-size: var(--font-size-base) !important;
}
.font-size\:biggish {
  font-size: var(--font-size-biggish) !important;
}
.font-size\:big {
  font-size: var(--font-size-big) !important;
}
```

Cada clase de utilidad tiene un sufijo `!important` para maximizar su especificidad. Las clases de utilidad son para ajustes finales, y no deberían ser anuladas por nada que venga antes que ellas.

![](reach.png)

> Una arquitectura CSS sensata tiene "alcance" (cuántos elementos se ven afectados) inversamente proporcional a la especificidad (qué tan complejos son los selectores). Esto fue formalizado por Harry Roberts como ITCSS, donde IT significa _Inverted Triangle_ (Triángulo Invertido).

Los valores en el ejemplo anterior son solo para ilustración. Para consistencia en todo el diseño, tus tamaños deberían derivarse probablemente de una _escala modular_. Consulta _Modular scale_ para más información.

??? info "Explicacion del texto"

    Este fragmento está explicando una idea importante de arquitectura CSS: **las clases de utilidad existen para hacer pequeños ajustes reutilizables sin depender del contexto ni alterar la semántica del HTML**.

    Voy por partes.

    ---

    __1. Estilos globales para elementos__

    Por defecto podrías tener:

    ```css
    h2 {
        font-size: 2.25rem;
    }

    h3 {
        font-size: 1.75rem;
    }
    ```

    Así, todos los `<h2>` serán grandes y todos los `<h3>` un poco más pequeños.

    ---

    __2. El problema__

    Imagina que tienes un `<h2>` en una barra lateral (sidebar) y allí se ve demasiado grande.

    Podrías pensar:

    ```html
    <h3>Título</h3>
    ```

    para hacerlo más pequeño.

    Pero eso es incorrecto.

    Un `<h2>` y un `<h3>` no existen para cambiar el tamaño visual, sino para indicar la jerarquía del documento.

    Por ejemplo:

    ```html
    <h1>Capítulo 1</h1>
    <h2>Sección A</h2>
    <h3>Subsección</h3>
    ```

    Cambiar un `<h2>` por un `<h3>` solamente para que se vea más pequeño rompería la estructura lógica del documento.

    ---

    __3. La solución tradicional__

    Podrías hacer:

    ```css
    .sidebar h2 {
        font-size: 1.75rem;
    }
    ```

    Es decir:

    > "Si un h2 está dentro de .sidebar, entonces será más pequeño."

    Funciona.

    Pero ahora el tamaño depende del contexto:

    ```
    sidebar → h2 pequeño
    fuera de sidebar → h2 grande
    ```

    Si mañana aparece:

    * `.footer`
    * `.card`
    * `.modal`

    tendrías que escribir:

    ```css
    .footer h2 { ... }
    .card h2 { ... }
    .modal h2 { ... }
    ```

    Y el CSS empieza a crecer.

    ---

    __4. Resolver el problema general__

    En realidad el problema no era:

    > "Un h2 dentro de sidebar."

    Sino:

    > "Necesito reducir el tamaño de algo."

    Eso puede ocurrir con:

    * un h2
    * un h3
    * un párrafo
    * un botón
    * un span

    Por eso se crean clases de utilidad.

    ```css
    .font-size\:base {
        font-size: 1rem;
    }

    .font-size\:biggish {
        font-size: 1.75rem;
    }

    .font-size\:big {
        font-size: 2.25rem;
    }
    ```

    Luego las aplicas donde quieras:

    ```html
    <h2 class="font-size:biggish">
        Título
    </h2>
    ```

    (En el CSS se escapan los dos puntos con `\`, pero en HTML se escriben normalmente.)

    ---

    __5. ¿Por qué son portables?__

    Porque ya no dependen de:

    ```css
    .sidebar h2
    ```

    ni de:

    ```css
    .card p
    ```

    Simplemente:

    ```css
    .font-size:biggish
    ```

    puede usarse en cualquier elemento:

    ```html
    <h2 class="font-size:biggish">
    <p class="font-size:biggish">
    <button class="font-size:biggish">
    ```

    La misma clase sirve para todos.

    ---

    __6. Custom Properties__

    En vez de repetir números:

    ```css
    h2 {
        font-size: 2.25rem;
    }

    .font-size\:big {
        font-size: 2.25rem;
    }
    ```

    se guarda el valor una sola vez:

    ```css
    :root {
        --font-size-base: 1rem;
        --font-size-biggish: 1.75rem;
        --font-size-big: 2.25rem;
    }
    ```

    y luego:

    ```css
    h2 {
        font-size: var(--font-size-big);
    }

    h3 {
        font-size: var(--font-size-biggish);
    }

    .font-size\:big {
        font-size: var(--font-size-big);
    }
    ```

    Si mañana cambias:

    ```css
    --font-size-big: 2.5rem;
    ```

    automáticamente cambian:

    * todos los h2;
    * todas las utilidades `.font-size:big`.

    ---

    __7. ¿Por qué usan `!important`?__

    ```css
    .font-size\:big {
        font-size: var(--font-size-big) !important;
    }
    ```

    Porque las clases de utilidad están pensadas como el último ajuste.

    Ejemplo:

    ```css
    h2 {
        font-size: 2.25rem;
    }
    ```

    ```html
    <h2 class="font-size:base">
    ```

    Gracias al `!important`, el resultado será:

    ```css
    font-size: 1rem
    ```

    La utilidad gana sobre el estilo normal.

    ---

    __8. La idea de ITCSS (Triángulo invertido)__

    La filosofía es:

    __Mucho alcance → poca especificidad__

    Arriba del triángulo:

    ```css
    body {}
    p {}
    h1 {}
    h2 {}
    ```

    Afectan a muchos elementos y son selectores simples.

    ---

    __Poco alcance → mucha especificidad__

    Abajo del triángulo:

    ```css
    .card .sidebar h2.active {}
    ```

    Afectan muy pocos elementos y son más específicos.

    ---

    Y las utilidades están al final:

    ```css
    .font-size\:base {
        font-size: 1rem !important;
    }
    ```

    Porque son modificaciones puntuales.

    ---

    __La idea más importante de todo el texto__

    El autor está diciendo:

    > No resuelvas un problema particular ("hacer más pequeño un h2 en una sidebar"). Resuelve el problema general ("necesito una forma de cambiar tamaños") y crea algo reutilizable para cualquier elemento y cualquier contexto.

    Esa filosofía es la misma que siguen frameworks como:

    * Tailwind CSS
    * UnoCSS
    * Windi CSS

    donde gran parte del CSS se construye precisamente mediante **clases de utilidad**.

??? Info "Utilidades vs  Valores Globales"

    > Pero para que se crean la clases de utiliad si ya tenemos los valores globales ?

    Esa es una muy buena pregunta. De hecho, las **variables globales (`:root`) y las clases de utilidad cumplen funciones diferentes**. Las variables no sustituyen a las utilidades.

    Piensa así:

    __1. Las variables almacenan valores__

    ```css
    :root {
      --font-size-base: 1rem;
      --font-size-biggish: 1.75rem;
      --font-size-big: 2.25rem;
    }
    ```

    Aquí no estás aplicando nada a ningún elemento. Solo estás guardando números con nombres.

    Es como tener una caja con etiquetas:

    ```
    --font-size-base → 1rem
    --font-size-biggish → 1.75rem
    --font-size-big → 2.25rem
    ```

    ---

    __2. Los estilos globales usan esos valores__

    ```css
    h2 {
      font-size: var(--font-size-big);
    }

    h3 {
      font-size: var(--font-size-biggish);
    }
    ```

    Ahora todos los `<h2>` y `<h3>` tienen tamaños por defecto.

    ---

    __3. ¿Qué ocurre si un h2 debe ser más pequeño?__

    Supón:

    ```html
    <h2>Productos</h2>
    ```

    Por defecto será:

    ```css
    font-size: 2.25rem;
    ```

    Pero para un caso particular quieres que sea de `1rem`.

    __Sin utilidades__

    Tendrías que crear:

    ```css
    .sidebar h2 {
      font-size: var(--font-size-base);
    }
    ```

    o

    ```css
    .card h2 {
      font-size: var(--font-size-base);
    }
    ```

    o

    ```css
    .footer h2 {
      font-size: var(--font-size-base);
    }
    ```

    Empiezas a llenar el CSS de excepciones.

    ---

    __4. Con una utilidad__

    Simplemente:

    ```html
    <h2 class="font-size:base">
        Productos
    </h2>
    ```

    La clase:

    ```css
    .font-size\:base {
      font-size: var(--font-size-base) !important;
    }
    ```

    sobrescribe el valor global.

    ---

    __La clave__

    Las variables solo responden a:

    > ¿Cuáles son mis tamaños?

    Las utilidades responden a:

    > ¿Cómo aplico uno de esos tamaños a un elemento específico?

    ---

    Podrías incluso eliminar las utilidades y hacer:

    ```css
    .sidebar h2 {
        font-size: var(--font-size-base);
    }
    ```

    porque las variables siguen existiendo.

    Y también podrías eliminar las variables y escribir:

    ```css
    .font-size\:base {
        font-size: 1rem;
    }
    ```

    porque las utilidades seguirían funcionando.

    **Las variables y las utilidades son independientes.**

    * `:root` → almacena valores reutilizables.
    * `h2`, `h3`, etc. → establecen los estilos por defecto.
    * `.font-size:base`, `.font-size:big` → permiten modificar esos estilos cuando un caso concreto lo requiere.

    Por eso se usan juntas: una guarda los valores y la otra los aplica cuando necesitas desviarte del comportamiento normal.

??? info "Clases de utilidad directo en HTML"

    > Veo que las clases de utilidad sirven para aplicar estilos en elementos html directamente, bueno algo asi lo voy entendiendo.

    Sí, vas por buen camino. Esa es precisamente la idea principal.

    Las **clases de utilidad** son pequeñas clases que representan una sola propiedad (o muy pocas) y que se aplican directamente en el HTML para modificar o complementar los estilos por defecto.

    Por ejemplo, supongamos que tienes:

    ```css
    h2 {
        font-size: 2.25rem;
    }
    ```

    Entonces todos los `<h2>` serán grandes:

    ```html
    <h2>Título principal</h2>
    ```

    Pero uno de ellos necesita ser más pequeño. En lugar de crear:

    ```css
    .sidebar h2 {
        font-size: 1rem;
    }
    ```

    puedes crear una utilidad:

    ```css
    .font-size\:base {
        font-size: 1rem !important;
    }
    ```

    y usarla directamente:

    ```html
    <h2 class="font-size:base">
        Título principal
    </h2>
    ```

    Así, el HTML mismo expresa:

    > "Este elemento es un h2, pero quiero que use el tamaño base."

    ---

    La filosofía es:

    * **Los selectores globales** (`h1`, `p`, `button`) definen la apariencia normal.
    * **Las clases de utilidad** hacen ajustes puntuales.
    * **Las variables (`:root`)** almacenan los valores reutilizables.

    ---

    Por eso frameworks como Tailwind CSS están construidos casi por completo con clases de utilidad:

    ```html
    <h2 class="text-sm text-center mt-4">
        Hola
    </h2>
    ```

    donde:

    * `text-sm` → tamaño pequeño.
    * `text-center` → centrar texto.
    * `mt-4` → margen superior.

    Sin embargo, el autor del texto que estás leyendo no está proponiendo un enfoque "todo utilidades" como Tailwind, sino una combinación:

    1. **Estilos globales para los elementos**.
    2. **Variables globales para los valores**.
    3. **Clases de utilidad para excepciones y ajustes finales**.

    Eso produce un CSS bastante ordenado y escalable.

    En otras palabras, las utilidades no vienen a reemplazar los estilos globales, sino a decir:

    > "Para este elemento en particular, necesito una pequeña variación sin tener que escribir nuevos selectores complejos."

!!! example

    Ejemplo de seccion [Clases de utilidad ↗](../../examples/Global&Local/claseUtilidad.html)

    ```css linenums="1"
    .font-size\:minbase { font-size: 0.8rem; }
    .font-size\:base { font-size: 1rem; }
    .font-size\:medium { font-size: 1.75rem; }
    .font-size\:big{ font-size: 2.5rem; }
    .background-color\:violet{ background-color: rgb(16, 14, 27); }
    .background-color\:gray{ background-color: rgb(192, 192, 192); }
    .color-letter\:white{ color: white;}
    .padding\:xs { padding: 0.5em; }
    .display\:inline-block{ display: inline-block; } 
    .width\:medium { width: 45vw;}
    .text-align\:auto{text-align: center;}

    ```

    ```html linenums="1"
    <div id="contenedor">
        <h1>E-COOMERCE</h1>
        <div id="card"  
            class="padding:xs 
                   background-color:gray 
                   display:inline-block
                   width:medium">
            <h2>Nissan Tsru 2002</h2>
            <p>Lorem ipsum, dolor sit amet consectetur adipisicing elit. Maxime provident at, molestiae quam fuga deserunt nobis iusto laboriosam suscipit quia hic maiores omnis excepturi quas consequatur voluptas alias libero optio?</p>
            <p>4500$</p>
        </div>

        <div id="footer" 
            class="background-color:violet 
                   padding:xs color-letter:white 
                   display:inline-block
                   width:medium">
            <h2 class="font-size:base" >Bussiness A & G</h2>
            <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Quaerat, nihil! Dolorum quod magni architecto, assumenda soluta nobis facere minus cumque, fuga animi quia nostrum possimus? Eius facilis reprehenderit animi sit!</p>
            <p class="font-size:minbase"> Edisson Guerrero &copy; 2026</p>
        </div>
    </div>
    ```
    ??? note "Explicacion"

        > Le puse esta propiedad: width: 20vw; con inline-block pero no se baja a una nueva linea.

        Eso es completamente normal. De hecho, **darle un `width` más pequeño a un `inline-block` hace más probable que quepan varios en la misma línea**, no que se bajen.

        Por ejemplo, si tienes:

        ```css
        .card {
            display: inline-block;
            width: 20vw;
        }
        ```

        y la ventana mide 1000 px de ancho:

        ```text
        1vw = 10px
        20vw = 200px
        ```

        Entonces cada caja mide aproximadamente 200 px.

        Si tienes dos cajas:

        ```text
        [ Caja 1 200px ][ Caja 2 200px ]
        ```

        y el contenedor mide 1000 px, ambas caben perfectamente y no hay razón para bajar una a la siguiente línea.

        ---

        __Solo se bajarán si no caben__

        Por ejemplo, si tienes seis cajas de 20vw:

        ```text
        [1][2][3][4][5]
        [6]
        ```

        porque:

        ```text
        6 × 20vw = 120vw
        ```

        y eso excede el ancho disponible.

        ---

        __Una pregunta importante__

        Cuando dices:

        > "le puse `width: 20vw;` con `inline-block` pero no se baja a una nueva línea"

        ¿esperabas que **cada elemento estuviera en una línea diferente**?

        Si es así, entonces `inline-block` no es el comportamiento adecuado, porque precisamente está diseñado para:

        > "Colocarse uno al lado del otro mientras haya espacio."

        Si quieres que cada caja esté debajo de la anterior, entonces usarías:

        ```css
        display: block;
        ```

        o más adelante, cuando llegues a ello, un contenedor con Flexbox en dirección columna (`Stack` en Every Layout).

        Si quieres, puedes mostrar el HTML y CSS completos y vemos exactamente qué comportamiento esperas y por qué no está ocurriendo.




## ⚠ Demasiadas clases de utilidad

Algo que recomendamos encarecidamente es no incluir clases de utilidad hasta que las necesites. No quieres enviar a los usuarios datos no utilizados o redundantes. Para este proyecto, mantenemos un archivo `helpers.css` y agregamos utilidades a medida que encontramos que las necesitamos. Si encontramos que la clase `text-align:center` no está teniendo efecto, es que no la hemos agregado en el CSS aún — así que la ponemos en nuestro archivo `helpers.css` para uso presente y futuro.

En un enfoque [_utility first_ ↗](https://tailwindcss.com/docs/styling-with-utility-classes) de CSS, los estilos heredados, universales y de elemento no se aprovechan realmente. En su lugar, se aplican combinaciones de estilos individuales caso por caso a elementos individuales e independientes. Usando el _framework utility-first Tailwind_, esto podría resultar — como ejemplifica la propia documentación de Tailwind — en valores de clase como los siguientes:

```css
class="rounded-lg px-4 md:px-5 xl:px-4 py-3 md:py-4 xl:py-3 bg-teal-500 hover:bg-teal-600 md:text-lg xl:text-base text-white font-semibold leading-tight shadow-md"
```

Puede haber razones, bajo circunstancias específicas, por las que uno querría ir por este camino. Quizás hay una gran cantidad de detalle y disparidad en el diseño visual que se beneficia de tener este tipo de control granular, o quizás quieres prototipar algo rápidamente sin cambiar de contexto entre CSS y HTML.

El enfoque de _Every Layout_ asume que quieres crear robustez y consistencia con el mínimo de intervención manual. Por lo tanto, los conceptos y técnicas aquí expuestos aprovechan _axiomas_, primitivas y los algoritmos de estilo que se extrapolan de ellos.

??? info "Explicacion"

    Este apartado está advirtiendo sobre un peligro: **abusar de las clases de utilidad**. La idea no es llenar el HTML con veinte clases para cada elemento.

    __1. Crea utilidades solo cuando realmente las necesites__

    Por ejemplo, imagina que nunca has necesitado centrar texto. Entonces no tiene sentido tener esto:

    ```css
    .text-align\:center {
        text-align: center;
    }
    ```

    porque nadie lo está usando.

    Cuando un día lo necesites:

    ```html
    <p class="text-align:center">
        Hola
    </p>
    ```

    y veas que no funciona porque la clase aún no existe, recién entonces la agregas a:

    ```css
    /* helpers.css */

    .text-align\:center {
        text-align: center;
    }
    ```

    La filosofía es:

    > No escribas código para problemas que todavía no tienes.

    ---

    __2. El enfoque "utility first"__

    Frameworks como Tailwind CSS llevan esta idea al extremo.

    En lugar de:

    ```css
    .card {
        background: teal;
        color: white;
        padding: 1rem;
        border-radius: 0.5rem;
    }
    ```

    escribes directamente:

    ```html
    <div class="
    rounded-lg
    px-4
    py-3
    bg-teal-500
    text-white
    font-semibold
    shadow-md">
    ```

    Cada clase hace una sola cosa:

    * `rounded-lg` → borde redondeado.
    * `px-4` → padding horizontal.
    * `py-3` → padding vertical.
    * `bg-teal-500` → color de fondo.
    * `text-white` → color del texto.
    * `shadow-md` → sombra.

    ---

    __3. ¿Cuál es el problema?__

    El HTML puede terminar pareciendo esto:

    ```html
    <button class="
    rounded-lg
    px-4
    md:px-5
    xl:px-4
    py-3
    md:py-4
    xl:py-3
    bg-teal-500
    hover:bg-teal-600
    md:text-lg
    xl:text-base
    text-white
    font-semibold
    leading-tight
    shadow-md">
    ```

    Aquí el HTML está lleno de estilos.

    Ya no lees:

    ```html
    <button class="primary-button">
    ```

    sino una lista enorme de clases.

    ---

    __4. Every Layout propone otro enfoque__

    Ellos prefieren:

    __1. Axiomas__

    Reglas generales.

    Por ejemplo:

    ```css
    body {
        line-height: 1.5;
    }
    ```

    ---

    __2. Primitivas__

    Componentes reutilizables.

    Por ejemplo:

    ```css
    .stack {
        display: flex;
        flex-direction: column;
    }
    ```

    ---

    __3. Algoritmos de layout__

    Que los elementos se adapten solos.

    Por ejemplo:

    ```css
    .cluster {
        display: flex;
        flex-wrap: wrap;
        gap: 1rem;
    }
    ```

    ---

    __Entonces, en vez de esto:__

    ```html
    <button class="
    rounded-lg
    px-4
    py-3
    bg-teal-500
    text-white
    font-semibold">
    ```

    prefieren algo parecido a:

    ```html
    <button class="button">
    ```

    y en CSS:

    ```css
    .button {
        background: teal;
        color: white;
        padding: 1rem;
        border-radius: .5rem;
        font-weight: 600;
    }
    ```

    o incluso aprovechar estilos globales:

    ```css
    button {
        padding: 1rem;
        border-radius: .5rem;
    }
    ```

    ---

    __La idea principal de todo este apartado es:__

    > Las clases de utilidad son útiles, pero deben ser una herramienta, no la base de todo el CSS.

    Primero aprovecha:

    1. La herencia.
    2. Los estilos globales.
    3. Los selectores de elementos.
    4. Las primitivas y componentes.

    Y solo cuando necesites una excepción o un ajuste puntual, añade una clase de utilidad.

    En otras palabras, **Every Layout busca que el navegador haga la mayor parte del trabajo y que el desarrollador intervenga lo menos posible**. Esa es la filosofía que están tratando de transmitir.

## Estilos locales o con ámbito (*scoped*)

Notablemente, el atributo/propiedad `id` (por razones de [_accesibilidad_ ↗](https://www.deque.com/blog/unique-id-attributes-matter/), sobre todo) solo puede usarse en un elemento HTML por documento. Estilizar a través del selector `id` está por lo tanto limitado a una instancia.

```css linenums="1"
#unique {
  /* ↓ Solo estiliza id="unique" */
  font-family: sans-serif;
}
```

El selector `id` tiene una [_especificidad_ ↗](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Cascade/Specificity) muy alta porque se asume que los estilos únicos están destinados a anular estilos genéricos competidores en todos los casos.

Por supuesto, no hay nada más "local" o _específico de instancia_ que aplicar estilos directamente a los elementos usando el atributo/propiedad `style`:

```html
<p style="font-family: sans-serif">...</p>
```

El único estándar restante para localizar estilos es dentro de Shadow DOM. Al hacer que un elemento sea un `shadowRoot`, se pueden usar selectores de baja especificidad que solo afectan a los elementos dentro de ese padre.

```js
const elem = document.querySelector('div');
const shadowRoot = elem.attachShadow({mode: 'open'});
shadowRoot.innerHTML = `
  <style>
    p {
      /* ↓ Solo estiliza los <p> dentro del Shadow DOM del elemento */
      font-family: sans-serif;
    }
  </style>
  <p>Un párrafo sans-serif</p>
`;
```

## Inconvenientes

El selector `id`, los estilos inline y Shadow DOM tienen todos inconvenientes:

- **Selectores `id`:** Muchos encuentran que la alta especificidad causa problemas sistémicos. También está la necesidad de inventar el nombre del `id` en cada caso. Generar dinámicamente una cadena única es a menudo preferible.

- **Estilos inline:** Una pesadilla de mantenimiento, que es la razón por la que CSS fue concebido antídoto en primer lugar.

- **Shadow DOM:** No solo los estilos evitan "filtrarse" fuera de la raíz del Shadow DOM, sino que (la mayoría) de los estilos no pueden _entrar_ tampoco — lo que significa que ya no puedes aprovechar el estilo global.

![](shwdo.png)

Lo que necesitamos es una forma de aprovechar simultáneamente el estilo global, pero aplicar estilos locales y específicos de instancia de manera controlada.

??? info "Explicación"

    Esta sección está hablando de **estilos locales**, es decir, estilos que afectan a **un elemento específico o a una pequeña parte del documento**, en contraste con los estilos globales que afectan a muchos elementos.

    La pregunta que intenta responder es:

    > **¿Cómo puedo estilizar algo concreto sin perder las ventajas del CSS global?**

    Veamos las opciones que menciona.

    ---

    __1. Selectores `id`__

    ```html
    <p id="unique">Hola</p>
    ```

    ```css
    #unique {
        font-family: sans-serif;
    }
    ```

    Este estilo solamente afecta a:

    ```html
    <p id="unique">Hola</p>
    ```

    y a ningún otro elemento.

    Por eso se considera un estilo muy local.

    ---

    __¿Por qué tiene tanta especificidad?__

    Porque un `id` debe ser único en toda la página.

    El navegador asume:

    > "Si alguien escribió un id, seguramente quiere que estos estilos ganen sobre los demás."

    Por eso:

    ```css
    p {
        color: blue;
    }

    #unique {
        color: red;
    }
    ```

    produce:

    ```html
    <p id="unique">
    ```

    en color rojo.

    ---

    __Problema__

    Los `id` tienen demasiada fuerza.

    Con el tiempo aparecen cosas como:

    ```css
    #header {}
    #footer {}
    #sidebar {}
    #login {}
    #profile {}
    ```

    y empiezan las guerras de especificidad.

    Además, para cada elemento tienes que inventar un nombre nuevo.

    ---

    __2. Estilos inline__

    ```html
    <p style="font-family:sans-serif">
        Hola
    </p>
    ```

    Aquí el CSS está directamente dentro del HTML.

    Esto es todavía más local.

    ---

    __Problema__

    Mezcla estructura y presentación.

    Terminas con:

    ```html
    <p
    style="
    color:red;
    font-size:2rem;
    font-weight:bold;
    margin-top:20px;">
    ```

    Si tienes 50 párrafos iguales y quieres cambiar el color, tendrás que modificar 50 veces.

    Por eso el CSS nació precisamente para evitar esto.

    ---

    __3. Shadow DOM__

    Supongamos:

    ```javascript
    const elem = document.querySelector("div");

    const shadowRoot = elem.attachShadow({ mode: "open" });
    ```

    Dentro del Shadow DOM:

    ```html
    <style>
    p {
        font-family:sans-serif;
    }
    </style>

    <p>Un párrafo</p>
    ```

    Ese selector:

    ```css
    p {
        font-family:sans-serif;
    }
    ```

    solo afecta a los `<p>` que están dentro del Shadow DOM.

    No afecta al resto del documento.

    ---

    __Ventaja__

    Puedes usar selectores simples:

    ```css
    p {
        ...
    }
    ```

    sin preocuparte de que modifiquen otros párrafos de la página.

    ---

    __Problema__

    El aislamiento es de doble sentido.

    Los estilos internos no salen:

    ```
    Shadow DOM
    │
    ├── p
    └── button
    ```

    pero los estilos globales tampoco entran.

    Por ejemplo:

    ```css
    body {
        font-family: Arial;
    }
    ```

    puede que no llegue a los elementos dentro del Shadow DOM.

    Entonces pierdes las ventajas del sistema global.

    ---

    __Resumen de los problemas__

    | Método     | Problema               |
    | ---------- | ---------------------- |
    | id         | Especificidad muy alta |
    | inline     | Difícil de mantener    |
    | Shadow DOM | Aislamiento excesivo   |

    ---

    __¿Qué está diciendo realmente el autor?__

    El autor ama los estilos globales porque permiten:

    ```css
    body {
        line-height: 1.5;
    }

    h1 {
        font-size: 3rem;
    }

    p {
        max-width: 60ch;
    }
    ```

    y automáticamente toda la página hereda esas reglas.

    Pero a veces necesitas modificar una sola instancia.

    Por ejemplo:

    ```html
    <h2>Productos</h2>
    ```

    y solo este encabezado debe ser azul.

    No quiere:

    ```css
    #titulo-57 {
        color: blue;
    }
    ```

    ni:

    ```html
    <h2 style="color:blue">
    ```

    ni meterlo en un Shadow DOM.

    Lo que busca es algo como:

    ```html
    <h2 class="text-color:blue">
    ```

    o

    ```html
    <h2 style="--color:blue">
    ```

    es decir:

    * seguir aprovechando el sistema global;
    * pero poder hacer ajustes locales y controlados.

    ---

    __En una frase__

    Esta sección está preparando el terreno para la idea central de Every Layout:

    > **Queremos conservar todas las ventajas del CSS global, pero disponer de una forma elegante de personalizar una instancia concreta sin recurrir a ids, estilos inline o Shadow DOM.**

    Y justamente la solución que presentarán más adelante es el uso inteligente de **propiedades personalizadas (custom properties)** y componentes de layout.



??? info "¿Que es Shadow DOM?"

    Tienes razón. El texto asume que ya sabes qué es el **Shadow DOM**, pero es un concepto importante y merece una explicación desde cero.

    ---

    __¿Qué es el Shadow DOM?__

    El Shadow DOM es una característica de los **Web Components** que permite crear una especie de **miniárbol HTML privado** dentro de un elemento.

    Por ejemplo, normalmente tienes un DOM así:

    ```html
    <body>
        <div>
            <p>Primer párrafo</p>
        </div>

        <p>Segundo párrafo</p>
    </body>
    ```

    Si escribes:

    ```css
    p {
        color: red;
    }
    ```

    los dos párrafos se vuelven rojos.

    ---

    __Con Shadow DOM__

    Supón este HTML:

    ```html
    <div id="mi-componente"></div>

    <p>Párrafo exterior</p>
    ```

    y este JavaScript:

    ```javascript
    const elem = document.querySelector("#mi-componente");

    const shadowRoot = elem.attachShadow({ mode: "open" });

    shadowRoot.innerHTML = `
        <p>Párrafo interior</p>
    `;
    ```

    Ahora el navegador ve algo parecido a:

    ```
    DOM principal
    │
    ├── div#mi-componente
    │      │
    │      └── Shadow DOM
    │            └── p
    │
    └── p
    ```

    El párrafo interior pertenece al Shadow DOM y el otro al DOM normal.

    ---

    __El aislamiento__

    Si tienes:

    ```css
    p {
        color: red;
    }
    ```

    el párrafo exterior se vuelve rojo.

    Pero el interior normalmente no.

    ¿Por qué?

    Porque el Shadow DOM está aislado del resto de la página.

    ---

    __También ocurre al revés__

    Si dentro del Shadow DOM escribes:

    ```html
    <style>
    p {
        color: blue;
    }
    </style>

    <p>Párrafo interior</p>
    ```

    solo el párrafo interior será azul.

    El párrafo exterior ni se entera.

    ---

    __¿Por qué existe?__

    Para crear componentes que no interfieran con el resto de la página.

    Por ejemplo, un calendario:

    ```
    Mi página
    │
    ├── Menú
    ├── Artículo
    ├── Footer
    └── Calendario
          └── Shadow DOM
    ```

    Los estilos del calendario no afectan al menú ni al artículo, y los estilos de la página tampoco rompen el calendario.

    ---

    __Ejemplo real__

    Cuando usas algo como:

    ```html
    <video controls></video>
    ```

    los botones de reproducción, volumen y pantalla completa están construidos con Shadow DOM.

    Aunque inspecciones el elemento:

    ```html
    <video></video>
    ```

    dentro hay un montón de HTML oculto y estilos privados.

    ---

    __¿Por qué Every Layout menciona sus inconvenientes?__

    Porque el Shadow DOM aísla demasiado.

    Los estilos globales:

    ```css
    body {
        font-family: Arial;
    }

    p {
        line-height: 1.5;
    }
    ```

    ya no llegan a los elementos internos.

    Entonces pierdes muchas ventajas del CSS global.

    ---

    __Una analogía__

    Imagina una ciudad.

    __CSS normal__

    Todos los edificios siguen las mismas leyes:

    * mismo sistema eléctrico;
    * mismas tuberías;
    * mismas señales de tráfico.

    Eso serían los estilos globales.

    ---

    __Shadow DOM__

    Cada edificio se convierte en un pequeño país independiente.

    Tiene:

    * sus propias leyes;
    * sus propias carreteras;
    * su propia electricidad.

    Eso evita conflictos, pero también hace más difícil compartir recursos.

    ---

    Por eso el autor dice:

    > El Shadow DOM es una forma de hacer estilos locales, pero el precio es perder parte de los beneficios de los estilos globales.

    Y por eso Every Layout prefiere otra solución: **usar estilos globales y modificar instancias concretas mediante propiedades personalizadas y clases**, sin aislar completamente los elementos.
## Primitivas y props

Como se estableció en _Composición_, el enfoque principal de _Every Layout_ son las [_primitivas de layout_ ](../capPreface/preface.md#layouts)simples que ayudan a organizar elementos/cajas juntos. Estas son las herramientas principales para generar layout y ocupan su lugar entre los estilos globales genéricos y las utilidades.

1. Estilos universales (incluyendo heredados)
2. Primitivas de layout
3. Clases de utilidad

Manifestados como componentes reutilizables, usando la [_especificación de custom elements_ ↗](https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_custom_elements), estos layouts pueden usarse globalmente. Pero _configuraciones_ únicas de estos layouts son posibles usando props (propiedades).

## Interoperabilidad

Los custom elements son usados en lugar de componentes de React, Preact o Vue (que también usan props) en _Every Layout_ porque son nativos y pueden usarse en _diferentes_ frameworks de aplicaciones web. Cada layout también viene con un generador de código para producir solo el código CSS necesario para lograr el layout. Puedes usar esto para crear una primitiva de layout específica para Vue (por ejemplo).

## Valores por defecto

Cada componente de layout tiene una hoja de estilo adjunta que define sus estilos básicos y por defecto. Por ejemplo, la hoja de estilo del _Stack_ (`Stack.css`) se ve de la siguiente manera.

```css linenums="1"
stack-l {
  display: block;
}
stack-l > * + * {
  margin-top: var(--s1);
}
```

Algunas cosas a tener en cuenta:

- La declaración `display: block` es necesaria ya que los custom elements se renderizan como elementos inline por defecto. Consulta [_Boxes_](../capBoxes/boxes.md) para más información sobre el comportamiento de elementos block e inline.

- El valor `margin-top` es lo que hace que el _Stack_ sea un _stack_: inserta margen entre una pila vertical de elementos. Por defecto, ese valor de margen coincide con el primer punto en nuestra _escala modular_: `--s1`.

- El selector `*` se aplica a cualquier elemento, pero nuestro uso de `+` (combinador de [_hermano adyacente_ ↗](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/Next-sibling_combinator)) está calificado para coincidir con cualquier elemento hijo sucesivo de un `<stack-l>` (observa el `> *`). La primitiva de layout es una composición en abstracto, y no debería prescribir el contenido, así que uso `*` para coincidir con cualquier tipo de elemento hijo que se le dé.

![](otherlayaout.png)

En la definición del custom element en sí, aplicamos el valor por defecto a la propiedad `space`:

```js linenums="1"
get space() {
  return this.getAttribute('space') || 'var(--s1)';
}
```

Cada custom element de _Every Layout_ construye una hoja de estilo incrustada basada en los valores de propiedad de la instancia. Es decir, esto…

```html linenums="1"
<stack-l space="var(--s3)">
  <div>...</div>
  <div>...</div>
  <div>...</div>
</stack-l>
```

…se convertiría en esto…

```html  linenums="1"
<stack-l data-i="Stack-var(--s3)" space="var(--s3)">
  <div>...</div>
  <div>...</div>
  <div>...</div>
</stack-l>
```

…y generaría esto:

```html  linenums="1"
<style id="Stack-var(--s3)">
  [data-i='Stack-var(--s3)'] > * + * {
    margin-top: var(--s3);
  }
</style>
```

Sin embargo — y esta parte es importante — la cadena `Stack-var(--s3)` solo representa una _configuración_ única para un layout, no una instancia única. Un elemento `<style>` con `id="Stack-var(--s3)"` se usa para servir _todas_ las instancias de `<stack-l>` que compartan la configuración representada por la cadena `Stack-var(--s3)`. Entre instancias de la _misma_ configuración, lo único que las diferencia es su _contenido_.

Si bien cada elemento de contenido dentro de una página web debería generalmente ofrecer información única, el _layout_ debería ser consistente y regular, usando patrones, motivos y disposiciones familiares y repetidos. Aprovechar los estilos globales junto con configuraciones de layout controladas resulta en consistencia y cohesión, y con un código mínimo.


