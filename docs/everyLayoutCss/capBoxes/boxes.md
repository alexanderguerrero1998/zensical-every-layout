# Cajas

Como Rachel Andrew nos ha recordado, [*todo en el diseño web es una caja*↗ ](https://www.smashingmagazine.com/2019/05/display-box-generation/), o la ausencia de una caja. No todo se ve necesariamente como una caja — `border-radius`, `clip-path` y `transform` pueden ser engañosos — pero todo ocupa un espacio con forma de caja. El layout es, inevitablemente, la disposición de cajas.

Antes de embarcarse en combinar cajas para hacer *layouts compuestos*,  es importante familiarizarse con cómo las cajas mismas están diseñadas para comportarse por defecto.

## El modelo de caja (*box model*)

El  [*box model*](https://www.w3.org/TR/CSS2/box.html#box-dimensions) es la fórmula sobre la cual se basan las cajas de layout, y comprende contenido (*content*), relleno (*padding*), borde (*border*) y margen (*margin*). CSS nos permite alterar estos valores para cambiar el tamaño y la forma general de la visualización de los elementos.

![](parts.png)

Los navegadores web aplican estilos CSS por defecto (*user agent styles*) a algunos elementos, lo que significa que se distribuyen de una forma razonablemente legible, incluso donde no se ha aplicado CSS del autor.

En Chrome, los estilos de agente de usuario por defecto para los párrafos (`<p>`) se ven así:

```css linenums="1"
p {
  display: block;
  margin-block-start: 1em;
  margin-block-end: 1em;
  margin-inline-start: 0px;
  margin-inline-end: 0px;
}
```

... y los estilos para listas desordenadas (`<ul>`) se ven así:

```css linenums="1"
ul {
  display: block;
  list-style-type: disc;
  margin-block-start: 1em;
  margin-block-end: 1em;
  margin-inline-start: 0px;
  margin-inline-end: 0px;
  padding-inline-start: 40px;
}
```

## La propiedad `display`

En ambos ejemplos anteriores, la propiedad `display` del elemento está establecida como `block`. Los elementos en bloque asumen todo el espacio disponible en una dimensión. Típicamente, esta es la dimensión horizontal, porque `writing-mode` está establecido como `horizontal-tb` (horizontal; con una dirección de flujo de arriba a abajo). En algunos casos, y para algunos idiomas [como el japonés↗](https://w3c.github.io/i18n-drafts/articles/vertical-text/index.en) , `vertical-rl` es el modo de escritura apropiado.

![](inlinelements.png)

!!! example "`display`:`block` | `writing-mode`: `horizontal-tb`"

    Aqui afectas **unicamente** a los elementos que contiene la propiedad `writing-mode`: `horizontal-tb`. El contenedor padre no se ve afectador, es decir, que si aplica esta propiedad al contenedor padre esperando que sus hijos se vean afectados por la propiedad `writing-mode`: `horizontal-tb` , eso no va a  suceder. Por eso aqui aplicamos la propiedad directamete a los hijos. Ya que esta propiedad funciona directamente aplicado en los elementos hijos.
    
   
    <div class="contenedor-1">
          <div class="contenedor-1-display">Block Element</div>
          <div class="contenedor-1-display">Block Element</div>
          <div class="contenedor-1-display">Block Element</div>
    </div>

      ```css hl_lines="2" linenums="1"
      .contenedor-1 {
            writing-mode: horizontal-tb;
        }

      .contenedor-1-display {
            display: block;
            margin-bottom: 5px;
            border-radius: 3px;
            background-color:#1976d2;
            padding: 10px;
        }
      ```
      ```html linenums="1"
      <div class="contenedor-1">
          <div class="contenedor-1-display">Block Element</div>
          <div class="contenedor-1-display">Block Element</div>
          <div class="contenedor-1-display">Block Element</div>
      </div>

      ```
       

!!! example "`display`:`block` |`writing-mode`: `vertical-lr`"

    Aqui afectas **unicamente** a los elementos que contiene la propiedad `writing-mode`: `vertical-lr`. El contenedor padre no se ve afectador, es decir, que si aplicas esta propiedad al contenedor padre esperando que sus hijos se vean afectados por la propiedad `writing-mode`: `vertical-lr`, eso no va a  suceder. Por eso aqui aplicamos la propiedad directamete a los hijos. Ya que esta propiedad funciona directamente aplicado en los elementos hijos.

    <div class="containerr-2">
          <div class="containerr-2-display">Block Element</div>
          <div class="containerr-2-display">Block Element</div>
          <div class="containerr-2-display">Block Element</div>
    </div>

      ```css hl_lines="2" linenums="1"
      .contenedor-2 {
          writing-mode: vertical-lr;
        }

      .contenedor-2-display {
        display: block;
        margin: 5px;
        border-radius: 3px;
        background-color:#1976d2;
        padding: 10px;
        }
      ```
      ```html  linenums="1"
      <div class="container-2">
          <div class="container-2-display">Block Element</div>
          <div class="container-2-display">Block Element</div>
          <div class="container-2-display">Block Element</div>
      </div>

      ```
       
### Elementos inline

Los elementos *inline* (con el valor `display: inline`) se comportan de manera diferente. Se distribuyen dentro del contexto actual, siguiendo el modo de escritura y la dirección en línea (`direction`). Solo son tan anchos como su contenido y se colocan adyacentemente donde haya espacio para hacerlo. 

Los elementos en `bloque` siguen la *dirección de flujo* (**flow direction**), y los elementos `inline` siguen la *dirección de escritura* (**writing direction**). 

![](flowdirections.png)

Pensando tipográficamente, podría decirse que los elementos en bloque son como párrafos, y los elementos inline son como palabras.

**Los elementos en bloque** (también llamados elementos de [nivel de bloque↗](https://developer.mozilla.org/en-US/docs/Glossary/Block-level_content) ) te brindan control sobre ambas dimensiones (horizontal y vertical) de la caja. Es decir, puedes aplicar `width`, `height`, `margin` y `padding` a un elemento en bloque y tendrán efecto. Por otro lado, los elementos inline tienen un tamaño determinado por su contenido (los valores prescritos de `width` y `height` no tienen efecto) y solo se permiten valores de `margin` y `padding` en la dimensión inline. Los elementos inline están diseñados para conformarse al flujo de colocación horizontal entre otros elementos inline.

!!! example "`display`:`inline`"

    Las propiedades de `width` y `height` no funcionan en elementos `inline`.

    Aqui afectas **unicamente** a los elementos que contiene la propiedad `display`: `inline`. El contenedor padre no se ve afectador, es decir, que si aplicas esta propiedad al contenedor padre esperando que sus hijos se vean afectados por la propiedad `display`: `inline`, eso no va a  suceder. Por eso aqui aplicamos la propiedad directamete a los hijos. Ya que esta propiedad funciona directamente aplicado en los elementos hijos.

    <div class = "contenedor-3">
      <div class ="contenedor-3-inline">Element</div>
      <div class ="contenedor-3-inline"> Element</div>
      <div class ="contenedor-3-inline"> Element</div>
    </div>

    ```css hl_lines="5 6" linenums="1"
    .contenedor-3-inline{
      display: inline;
      background-color: #1976d2;

      width: 15px;       /* Esta propiedad no funciona */
      height: 20;        /* Esta propiedad no funciona */
    }
    ```
    ```html linenums="1"
    <div class = "contenedor-3">
      <div class ="contenedor-3-inline">Element</div>
      <div class ="contenedor-3-inline"> Element</div>
      <div class ="contenedor-3-inline"> Element</div>
    </div>

    ```


Una propiedad de visualización relativamente nueva, `inline-block` es un híbrido de `block` y `inline` . Puedes establecer propiedades verticales en los elementos `inline-block`,aunque esto no siempre es deseable, como se muestra en el siguiente ejemplo.

![](inlineblocks.png)


!!! example "`display`:`inline-block`"

    Adiferencia de los volores anteriores, aqui cuando usamos la propiedad `display`:`inline-block` se puede aplicar `width`, `height`, `margin` y `padding` a un elemento.

     Igualemnete, aqui afectas **unicamente** a los elementos que contiene la propiedad `display`: `inline-block`. El contenedor padre no se ve afectador, es decir, que si aplicas esta propiedad al contenedor padre esperando que sus hijos se vean afectados por la propiedad `display`: `inline-block`, eso no va a  suceder. Por eso aqui aplicamos la propiedad directamete a los hijos. Ya que esta propiedad funciona directamente aplicado en los elementos hijos.

    <div>
      <div class="contenedor-4-inline-block"> Element </div>
      <div class="contenedor-4-inline-block"> Element </div>
      <div class="contenedor-4-inline-block"> Element </div>
    </div>

    ```css linenums="1"
    .contenedor-4-inline-block {
      display: inline-block;

      width: 100px;  /* Esta propiedad si funciona aqui */
      height: 50px;  /* Esta propiedad si funciona aqui */
      padding: 20px; /* Esta propiedad si funciona aqui */
      margin: 10px;  /* Esta propiedad si funciona aqui */

      background-color: #1976d2;
    }
    ```
    ```html linenums="1"
    <div>
      <div class="contenedor-4-inline-block"> Element </div>
      <div class="contenedor-4-inline-block"> Element </div>
      <div class="contenedor-4-inline-block"> Element </div>
    </div>
    ```


De los tipos básicos, solo queda `display: none`. Este valor elimina el elemento del layout por completo. No tiene presencia visual ni impacto en el layout de los elementos circundantes. Es como si el elemento mismo hubiera sido eliminado del HTML. En consecuencia, los navegadores no comunican la presencia o el contenido de los elementos con `display: none` a las tecnologías de asistencia como los lectores de pantalla ([screen readers↗](https://en.wikipedia.org/wiki/Screen_reader) ).

!!! info

    De los tres tipos de valores de la propiedad `display`: `block` `inline` `inlone-block` se tienen que aplicar directamente en los elementos que se quieren afectar. El `none` oculta totalmente al elemento dentro del HTML

## Contextos de formato (*formatting contexts*)

Cuando aplicas `display: flex` o `display: grid` a un `<div>`, este continúa comportándose como un elemento en bloque, usando `display: block`. 

Sin embargo, cambia la forma en que se comportan sus elementos *hijos*. Por ejemplo, con solo `display: flex` (y sin otras propiedades relacionadas con Flexbox) aplicado al padre, sus hijos se distribuirán horizontalmente. O, dicho de otro modo, la *dirección de flujo* (*flow direction*) se cambia de vertical a horizontal.

Los contextos de formato son la base de muchos de los layouts documentados en este proyecto. Convierten los elementos en componentes de layout. En *Composición*, exploraremos cómo diferentes contextos de formato pueden anidarse para crear layouts compuestos.

!!! example 

    A diferencia de los valores anteriores (`block` `inline` `inlone-block`), cuando usamos `display:flex` lo aplicamos al contenedor padre para que los cambios se reflejen en los hijos.

    Con `display:flex` los hijo del contenedor pasan de una distribucion vertical a una distribucion horizontal.

    <div class="contenedor-5-padre">
      <div class="hijo-5"> Element </div>
      <div class="hijo-5"> Element </div>
      <div class="hijo-5"> Element </div>
    </div>
    
    ```css linenums="1" hl_lines="1"
    .contenedor-5-padre{ display: flex; }
    .hijo-5{
        background-color: #1976d2;
        padding: 5px;
        margin: 5px;
     }
    ```
    ```html linenums="1"
    <div class="contenedor-5-padre">
      <div class="hijo-5"> Element </div>
      <div class="hijo-5"> Element </div>
      <div class="hijo-5"> Element </div>
    </div>
    ```

## Contenido en cajas

La web es un conducto para información principalmente textual, complementada por medios como imágenes y videos, a menudo referidos colectivamente como *contenido*. Los navegadores incorporan algoritmos de ajuste de línea (*line wrapping*) y desplazamiento (*scrolling*) para asegurar que el contenido se transmita al usuario en su totalidad, independientemente del tamaño y dimensión de su pantalla, y de configuraciones como el nivel de zoom. La web es en gran medida [responsiva ↗](https://alistapart.com/article/responsive-web-design/) por defecto.

Sin intervención, es el contenido de un elemento lo que determina su tamaño y forma. El contenido hace que los elementos `inline` crezcan horizontalmente y los elementos `block` crezcan verticalmente. Si se deja a su suerte, el *tamaño* de una caja está determinado por el área del contenido que contiene. Debido a que el contenido web es *dinámico* (sujeto a cambios), las representaciones estáticas de los layouts web son extremadamente engañosas. Trabajar directamente con CSS y su flexibilidad desde el principio, como hacemos aquí, es altamente recomendable.

![](twhon.png)

> *Si reduces a la mitad la anchura de un elemento, tendrá que ser el doble de alto para que quepa la misma cantidad de contenido*

## La propiedad `box-sizing`

Por defecto, las dimensiones de una caja son las dimensiones del *contenido* de la caja *más* sus valores de `padding` y `border` (implícitamente: `box-sizing: content-box`). Es decir, si estableces que un elemento tenga `10rem` de ancho, luego agregas `1rem` de padding a cada lado, tendrá `12rem` de ancho: `10rem` más `1rem` de padding izquierdo y `1rem` de padding derecho.

Si optas por `box-sizing: border-box`, el área de contenido se reduce para acomodar el padding y el ancho total equivale al ancho prescrito de `10rem`.

![](boxsizing.png)

Generalmente, se considera preferible usar el modelo `border-box` para todas las cajas. Hace que calcular/anticipar las dimensiones de las cajas sea más fácil.

!!! example 

    Aqui el primer elemento mantiene sus dimenciones de `100px`, mientras que el segunda elemento trendra una dimencion de `120px`.

    <div class="contenedor-6">
      <div class="hijo-6-box-sizing-1">Element</div>
      <div class="hijo-6-box-sizing-2">Element</div>
    </div>

    ```css  linenums="1" hl_lines="2"
    .hijo-6-box-sizing-1{
          box-sizing: border-box;
          padding: 10px;
          width: 100px;
          background-color: #1976d2;
      }
      .hijo-6-box-sizing-2{
          box-sizing: content-box;
          padding: 10px;
          width: 100px;
          background-color: #1976d2;
      }
    ```
    ```html linenums="1" 
    <div class="contenedor-6">
      <div class="hijo-6-box-sizing-1">Element</div>
      <div class="hijo-6-box-sizing-2">Element</div>
    </div>

    ```

Cualquier estilo, como `box-sizing: border-box`, que sea aplicable a *todos* los elementos se aplica mejor usando el selector universal (`*`). Como se cubre en detalle en *Estilos globales y locales*, poder afectar el layout de múltiples elementos (en este caso, *todos* los elementos) simultáneamente es cómo CSS aporta eficiencia al diseño de layouts.

```css linenums="1"
* {
  box-sizing: border-box;
}
```

## Excepciones

Hay excepciones a esta regla general, como en el *Center layout* donde la medición del *contenido* es crítica. La [cascada ↗](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Cascade/Introduction) de CSS está diseñada para acomodar excepciones a las reglas generales.

```css linenums="1"
* {
  box-sizing: border-box;
}
.center-l {
  box-sizing: content-box;
}
```

Solo cuando la altura o el ancho de una caja está restringida, la diferencia entre `border-box` y `content-box` entra en juego. A modo de ilustración, considera un elemento en bloque colocado dentro de otro elemento en bloque. Usando el modelo `content-box` y un padding de `1rem`, el elemento hijo desbordará (*overflow*) por `2rem` cuando se aplique `width: 100%`.

![](witd100.png)

¿Por qué? Porque `width: 100%` significa "haz que el ancho de este elemento sea igual al del elemento padre". Ya que estamos usando el modelo `content-box`, el `content` se hace `100%` de ancho, luego el padding se suma a este valor.

!!! example 

    <div class="contendor-7">
      <div class="hijo-7"> Element</div>
    </div>

    ```cs linenums="1" hl_lines="9 10 11"
    .contendor-7 {
      padding-top: 10px;
      width: 300px;
      height: 50px;
      background-color: #be3333;
    }
    .hijo-7 {
      background-color: #1976d2;
      padding: 4px;
      width: 100%;
      box-sizing: content-box;
    }
    ```
    ```html linenums="1"
    <div class="contendor-7">
      <div class="hijo-7"> Element</div>
    </div>
    ```


Pero si usamos `width: auto` (podemos simplemente eliminar `width: 100%`, ya que `width: auto` es el valor por defecto) la caja hija encaja perfectamente dentro de la caja padre. E independientemente del valor `box-sizing`

![](witd100.png)

Implicitamente, el `height` también está configurado como `auto`, lo que significa que se deriva del contenido. Nuevamente, `box-sizing` no tiene efecto.

!!! example

    <div class="contendor-8">
      <div class="hijo-8">Element</div>
    </div>

    ```cs linenums="1" hl_lines="9 10 11"
    .contendor-8 {
      padding-top: 10px;
      width: 300px;
      height: 50px;
      background-color: #be3333;
    }
    .hijo-8 {
      background-color: #1976d2;
      padding: 4px;
      width: auto;
      box-sizing: content-box;
    }
    ```
    ```html linenums="1"
    <div class="contendor-8">
      <div class="hijo-8">Element</div>
    </div>
    ```


La lección aquí es que las dimensiones de nuestros elementos deberían derivarse en gran medida de su contenido interno y contexto externo. Cuando tratamos de prescribir dimensiones, las cosas tienden a salir mal. Todo lo que deberíamos hacer como diseñadores visuales es hacer sugerencias sobre cómo debería tomar forma el layout. Podríamos, por ejemplo, aplicar un `min-height` (como en el *Cover layout*) u ofrecer un `flex-basis` (como en el *Sidebar*).

!!! note 

    Esa frase quiere decir que el tamaño de los elementos no debería establecerse arbitrariamente, sino que debería depender principalmente de:

    - [x] Su contenido interno (texto, imágenes, botones, etc.).
    - [x] El espacio y la relación que tienen con los elementos que los rodean (su contexto externo).

El CSS de la sugerencia está en el corazón del diseño de layout algorítmico. En lugar de decirle a los navegadores qué hacer, permitimos que los navegadores hagan sus propios cálculos y saquen sus propias conclusiones, para adaptarse mejor al usuario, su pantalla y su dispositivo. Nadie debería experimentar contenido oscurecido bajo ninguna circunstancia.




