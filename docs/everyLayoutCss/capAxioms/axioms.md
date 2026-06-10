# Axiomas

Como _el matemático Euclides era consciente_ ↗, incluso las geometrías más complejas se fundamentan en axiomas (o _postulados_) simples e irreducibles. A menos que tu diseño se base en axiomas, tu resultado será inconsistente y malformado. El tema de esta sección es cómo honrar un sistema de axiomas de diseño en todo el sistema, usando la _medida tipográfica_ como ejemplo.

## Medida (*measure*)

El ancho de una línea de texto, en caracteres, se conoce como su _medida_ (_measure_). Elegir una medida es crítico para el escaneo cómodo de líneas sucesivas. _The Elements Of Typographic Style_ ↗ considera que cualquier valor entre 45 y 75 es razonable.

Establecer una medida para medios impresos es relativamente sencillo. Es simplemente el ancho del artefacto de papel dividido por el número de columnas de texto colocadas dentro de él — menos los _márgenes y canalones (gutters)_ ↗, por supuesto.

![](mesure.png)

La web no es estática o predecible como la impresión. Cada palabra está separada por un _espacio de ruptura (breaking space)_ (unicode point U+0020 ↗), liberando secuencias de texto para que se ajusten dinámicamente según el espacio disponible. La cantidad de espacio disponible está determinada por una serie de factores interrelacionados que incluyen el tamaño y la orientación del dispositivo, el tamaño del texto y el nivel de zoom.

Como diseñadores, buscamos controlar la experiencia de los usuarios. Pero, como John Allsopp escribió en _The Dao Of Web Design_ ↗ del año 2000, intentar el control _directo_ sobre la forma en que los usuarios consumen contenido en la web es temerario. Imponer una medida específica significaría establecer un ancho fijo. Muchos usuarios experimentarían desplazamiento horizontal y funcionalidad de zoom rota.

![](cellpage.png)

Para diseñar "páginas adaptables" (término de Allsopp), debemos ceder el control a los algoritmos (como el ajuste de texto) que los navegadores usan para maquetar páginas web automáticamente. Pero eso no significa que no haya lugar para el layout. Piensa en ti mismo como el _mentor_ del navegador, en lugar de su _microgestor_.

## El axioma de la medida

Es una buena práctica intentar enunciar un axioma de diseño en una frase u oración corta. En este caso, esa declaración podría ser: _"la medida nunca debería exceder 60ch"_.

¿La medida de _qué_? ¿Y _dónde_? No hay razón por la que _ninguna_ línea de texto deba volverse demasiado larga. Este axioma, como todos los axiomas, debería impregnar el diseño sin calificaciones ni excepciones. La verdadera pregunta es: ¿cómo? En _Global and local styling_ establecimos tres niveles principales de estilo:

1. Estilos universales (incluyendo heredados)
2. Primitivas de layout
3. Clases de utilidad

El axioma de la medida debería sembrarse tan pervasiveamente como sea posible en los estilos universales, pero también estar disponible para las primitivas de layout (ver _Composición_) y las clases de utilidad. Pero primero, ¿qué propiedad y qué valor deberían inscribir la regla?

## La declaración `max-width`

¡Los anchos (y alturas) fijos son anatema para el diseño responsivo, como establecimos en _Boxes_ y nuevamente aquí. En su lugar, deberíamos trabajar con _tolerancias_. La propiedad `max-width`, por ejemplo, tolera cualquier longitud de texto _hasta_ un cierto valor.

```css linenums="1"
p {
  max-width: 700px;
}
```

Esa es la propiedad cubierta. Sin embargo, la unidad `px` es problemática. Podemos juzgar, a simple vista, que `700px` crea una medida razonable para el `font-size` dado. Pero el `font-size` dado es realmente solo el `font-size` que nuestra pantalla resulta estar mostrando en ese momento — es nuestra visión _parroquial_ de nuestro propio diseño.

Cambiar `font-size` para los párrafos, o ajustar el tamaño de fuente del sistema por defecto, creará una medida (máxima) diferente. Debido a que no hay una _relación_ entre la longitud de caracteres y el ancho en píxeles, no tenemos un algoritmo que pueda garantizar el valor correcto de medida máxima.

![](fontsize.png)

Afortunadamente, CSS incluye la unidad `ch`. El valor de `1ch` se basa en el ancho del carácter `0` de la fuente. Importantemente, esto significa que cambiar el `font-size` cambia el valor de `1ch`, adaptando así la medida. Usar unidades `ch` es un enfoque innatamente algorítmico para la medida, porque el resultado se basa en un cálculo que permites que el navegador haga por ti.

Usar `ch` nos permite imponer el axioma independientemente del `font-size`, permitiendo que sea altamente pervasivo y sin peligro de "salir mal". Donde "_la medida nunca debería exceder 60ch_" podría haber sido una nota en alguna documentación, puede en su lugar ser una calidad directamente codificada en el carácter del diseño.

## Diseñar sin ver

Diseñar por axioma requiere un cambio mental. Los axiomas no crean directamente artefactos visuales, solo las _características_ de los artefactos que podrían emerger.

A veces los artefactos resultantes se ven y se comportan de maneras que podrías no haber previsto. Por ejemplo, en un contenedor que es más ancho que la medida acordada aplicada al tamaño de fuente base, los elementos con diferentes tamaños de fuente ocuparán diferentes proporciones del ancho de ese contenedor. Esto se debe a que `1ch` es más ancho para un tamaño de fuente más grande.

![](rayas.png)

Al momento de concebir el axioma, es posible que no hayas imaginado este efecto visual específico. Pero eso no significa que no sea sólido o deseable. De hecho, es tu CSS haciendo exactamente lo que pretendías: mantener una medida razonable independientemente del contexto.

Fundamentalmente, diseñar para la web es diseñar _sin ver_. Simplemente no puedes anticipar todas las combinaciones visuales producidas por:

1. La colocación modular de tus componentes de layout
2. Las circunstancias y configuraciones de cada usuario final

En lugar de pensar en diseñar para la web como crear artefactos visuales, piensa en ello como escribir _programas que generan_ artefactos visuales. Los axiomas son las reglas que influyen en cómo el navegador crea esos artefactos, y cuanto mejor pensados estén, mejor puede el navegador acomodar al usuario.

## Valores por defecto globales

Para realizar el axioma, necesitamos asegurar que todos los elementos aplicables estén sujetos a él. Esto es una cuestión de selectores. Podríamos crear un selector de clase…

```css linenums="1"
.measure-cap {
  max-width: 60ch;
}
```

…pero es un error pensar en términos de clases (de utilidad) demasiado pronto. Significaría aplicar el estilo manualmente, a elementos individuales en el HTML, dondequiera que sintiéramos que es aplicable. La intervención manual es laboriosa, propensa a errores (omitir elementos), y llevará a un marcado inflado.

En su lugar, deberíamos preguntarnos a qué _tipos_ de elementos podría aplicarse la regla. Ciertamente, los elementos de flujo diseñados para texto. Elementos inline como `<em>` y `<small>` no necesitarían ser incluidos, ya que ocuparían solo una parte de la medida total de sus elementos de flujo padre.

```css linenums="1"
p,
h1,
h2,
h3,
h4,
h5,
h6,
li,
figcaption {
  max-width: 60ch;
}
```

### Estilo basado en excepciones

Es difícil saber si nos hemos acordado de todo aquí. Un enfoque basado en excepciones es más inteligente, ya que solo tenemos que recordar qué elementos _no_ deberían estar sujetos a la regla. Observa que los elementos inline _no_ están incluidos en el siguiente ejemplo pero, dado que ocuparían un espacio horizontal igual o menor que sus padres, no surgirían efectos negativos.

```css linenums="1"
* {
  max-width: 60ch;
}
html,
body,
div,
header,
nav,
main,
footer {
  max-width: none;
}
```

El elemento `<div>` particularmente tiende a usarse como contenedor/envoltorio genérico. Es probable que algunos de estos elementos contengan múltiples cajas adyacentes, con una o más de cada una deseando ocupar los `60ch` completos. Esto hace que sus padres sean excepciones lógicas.

Un enfoque basado en excepciones para CSS nos permite hacer _la mayor parte_ de nuestro estilo con _la menor cantidad_ de nuestro código. Si no estás adoptando un enfoque basado en excepciones, puede ser porque hacer excepciones se siente como _corregir errores_. Pero esto está lejos de ser el caso. CSS, con su _cascada y otras características_ ↗, está diseñado para esto. En la tesis _ITCSS (Inverted Triangle CSS)_ ↗ de Harry Roberts, la especificidad (qué tan específicos son los selectores) es inversamente proporcional al alcance (a cuántos elementos deberían afectar).

## Un valor universal

Antes de empezar a usar el valor de medida en todas partes, es mejor definirlo como una custom property. De esa manera, cualquier cambio en el valor se propagará por todo el diseño.

Observa que no todas las custom properties tienen que ser globales, pero en este caso queremos que nuestros elementos, props y clases de utilidad estén de acuerdo. Por lo tanto, colocamos la custom property en el elemento `:root`.

```css linenums="1"
:root {
  --measure: 60ch;
}
```

Esto se pasa a nuestro bloque universal…

```css linenums="1"
* {
  max-width: var(--measure);
}
html,
body,
div,
header,
nav,
main,
footer {
  max-width: none;
}
```

…y a cualquier clase de utilidad que podamos encontrar que necesitemos.

```css linenums="1"
.max-width\:measure {
  max-width: var(--measure);
}
.max-width\:measure\/2 {
  max-width: calc(var(--measure) / 2);
}
```

### Escapado

Las barras invertidas son necesarias en el ejemplo anterior para escapar los caracteres especiales de barra diagonal y dos puntos.

## Medida en layouts compuestos

Ciertas _primitivas de layout_ inevitablemente aceptan props relacionadas con la medida, y algunas establecen valores por defecto para esas props usando `var(--measure)`. El _Switcher_ tiene una prop `threshold` que define el ancho del contenedor en el cual el layout cambia entre una configuración horizontal y vertical:

```js linenums="1"
get threshold() {
  return this.getAttribute('threshold') || 'var(--measure)';
}
set threshold(val) {
  return this.setAttribute('threshold', val);
}
```

Este es un valor por defecto sensato, pero puede anularse fácilmente con cualquier valor de cadena:

```html linenums="1"
<switcher-l threshold="20rem">...</switcher-l>
```

Si pasamos un valor ilegítimo a `threshold`, la declaración será descartada, y el _Switcher_ aplicará la hoja de estilo de respaldo (_fallback stylesheet_) con el valor por defecto de todos modos.

Nuestro enfoque de la medida es uno en el que asumimos el control, pero un tipo de control moderado que es deferente hacia la forma en que los navegadores funcionan y los usuarios los operan. Muchos de los 'axiomas' que gobiernan tu diseño, como _"la fuente del cuerpo será la Fuente X"_ o _"los encabezados serán azul oscuro"_, no tendrán un impacto en el layout como tal, lo que los hace mucho más simples de aplicar solo con estilos globales. Cuando el layout entra en la ecuación, ten cuidado con las diferentes configuraciones y orientaciones. Elige propiedades, valores y unidades que permitan al navegador calcular el layout más adecuado en tu nombre.

