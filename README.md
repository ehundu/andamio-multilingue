# Andamio multilingüe para Ehundu

Una web completa en dos idiomas, castellano y euskera, hecha con [Ehundu](https://ehundu.com), el generador de sitios estáticos en PHP. Es un andamio: la estructura de partida de una web en la que cada idioma tiene sus páginas, su menú, sus direcciones y sus textos, y en la que todo está bien enlazado entre idiomas: el selector de la cabecera, las etiquetas `hreflang` para los buscadores, un feed por idioma y una página 404 en cada uno. Cambias los textos, los datos y el CSS, y es tuyo.

Puedes verlo funcionando, sin instalar nada, en [andamio-multilingue.ehundu.com](https://andamio-multilingue.ehundu.com/).

Necesitas PHP 8.4 o superior y [Composer](https://getcomposer.org).

**Los textos en euskera son de ejemplo**, una traducción de partida. Si vas a usarlos, que los revise alguien que hable euskera.

## Empezar

Si lo has creado con `composer create-project`, ya está todo instalado. Si has usado el botón «Use this template» de GitHub, o has descargado el zip, instala el motor:

```
composer install
```

Las tres órdenes que vas a usar:

```
composer servir      # lo previsualiza en http://127.0.0.1:8000/ y rehace solo lo que cambia
composer compilar    # deja el sitio terminado en la carpeta salida/
composer desplegar   # lo compila y lo sube al servidor que digas en sitio.yml
```

## Cómo funcionan los dos idiomas

Los idiomas se declaran en `sitio.yml`. El primero es el predeterminado: sus páginas no llevan prefijo en la dirección, y las de los demás llevan el suyo.

```yaml
idiomas:
  - codigo: es
    nombre: Castellano
  - codigo: eu
    nombre: Euskara
```

**Cada página se escribe una vez por idioma, y el idioma va en el nombre del fichero.** No hace falta declarar que dos ficheros son la misma página: lo son porque se llaman igual.

```
contenido/
  contacto.md             →  /contacto/
  contacto.eu.md          →  /eu/kontaktua/        (con url: /kontaktua/ en su front matter)
  noticias/
    estreno-web.md        →  /noticias/estreno-web/
    estreno-web.eu.md     →  /eu/berriak/webgune-berria/
    jornada-abierta.md    →  /noticias/jornada-abierta/   (solo en castellano)
```

- **Las direcciones se traducen** escribiendo `url` en el front matter de la traducción, sin el prefijo `/eu/`, que pone Ehundu.
- **Lo que no está traducido no existe.** `jornada-abierta.md` no tiene su `.eu.md`, así que no se publica nada en euskera con su texto. El selector de idioma de esa página lleva a la portada en euskera, y el enlace lo dice con un texto para los lectores de pantalla. Ehundu no te avisa de lo que falta traducir.
- **Las colecciones son de un solo idioma.** La lista de noticias en euskera saca solo las noticias en euskera: por eso hay una más en castellano.
- **Lo que no cambia de idioma** (el orden de un servicio, la fecha de una noticia) va en un `.yml` con el nombre de la página, como `servicios/diseno-web.yml`, y vale para todas sus traducciones.

## Los datos de cada idioma

Los ficheros de `datos/` siguen la misma regla que las páginas, con una diferencia: **uno sin código de idioma es común a todos.**

```
datos/estudio.yml         el nombre, el correo y el teléfono, iguales en todos los idiomas
datos/estudio.es.yml      lo que cambia en castellano (el lema, la dirección)
datos/estudio.eu.yml      lo que cambia en euskera
datos/textos.es.yml       los textos de las plantillas en castellano
datos/textos.eu.yml       los mismos textos, en euskera
datos/menus.es.yml        el menú en castellano, con sus direcciones
datos/menus.eu.yml        el menú en euskera
```

En una página, `datos.estudio` es el fichero común con el de su idioma encima. `textos` y `menus` son uno por idioma, con las mismas claves: si una falta en un idioma, sale vacía en sus páginas. El menú de cada idioma no es la traducción uno a uno del otro: si una sección solo existe en un idioma, su menú no la lleva.

## El selector de idioma y los buscadores

Las plantillas escriben los dos, el motor no. El selector está en `parciales/cabecera.twig`: para cada idioma, lleva a la traducción de la página y, si no la tiene, a su portada. Los `hreflang` están en `plantillas/base.twig`: cada página dice en qué idiomas existe, ella misma incluida, y cuál es la de «cualquier idioma» (`x-default`, la del predeterminado). Los dos se hacen con `pagina.traducciones` y `sitio.idiomas`, así que **añadir un idioma no obliga a tocarlos**.

## Las fechas

La plantilla escribe las fechas con `|fecha`, que usa los meses del idioma de la página: `1 de octubre de 2026` en castellano y `2026ko urriaren 1a` en euskera. Ehundu trae castellano, euskera, inglés y alemán.

## Añadir un tercer idioma

1. En `sitio.yml`, una entrada nueva en `idiomas` (`codigo: en`, `nombre: English`) y su aviso en `enlacesExternos.aviso`.
2. Los ficheros de datos del idioma: `textos.en.yml`, `menus.en.yml` y `estudio.en.yml`, con las mismas claves.
3. La página raíz, `contenido/index.en.md`, y el resto de páginas que quieras traducir, con su `url` traducida. No hace falta traducirlas todas.
4. Su página de error, `contenido/404.en.md`.

El selector y los `hreflang` salen solos, y también el feed (`/en/feed.xml`) en cuanto haya noticias en ese idioma. Para cambiar el euskera por otro idioma, cambia `eu` por su código, renombra los ficheros `.eu.md` y `.eu.yml` y reescribe sus textos.

## Lo primero que cambiar

1. En `sitio.yml`, el nombre y la dirección del sitio, y el título del feed.
2. Los datos del estudio, en `datos/estudio.yml` y sus versiones por idioma.
3. Los textos de las plantillas y los menús, en `datos/`.
4. Las páginas de `contenido/`, en cada idioma: la portada, los servicios, las noticias, «Sobre nosotros» y el contacto.
5. En `publico/css/estilos.css`, los colores: están al principio, en variables, con su pareja para el modo oscuro.

## Lo que no hace

No detecta el idioma del navegador ni redirige: quien llega a la portada ve el castellano, y cambia de idioma con el selector. Decidir por quien visita necesita JavaScript o configuración del servidor, y el andamio no quiere decidirlo por ti. Tampoco traduce nada: cada texto lo escribe una persona en cada idioma.

## Qué hay en cada carpeta

```
sitio.yml                  el nombre, la dirección, los idiomas, el feed y adónde se publica
contenido/                 las páginas, en Markdown, una por idioma: contacto.md y contacto.eu.md
  servicios/               un fichero por servicio y por idioma; su _datos.yml les da lo que comparten
  noticias/                las noticias; una solo existe en castellano
datos/                     los datos de cada idioma y los comunes
plantillas/                el HTML de cada tipo de página, con Twig
parciales/                 trozos de HTML que reutilizan las plantillas (la cabecera con el selector, la tarjeta…)
publico/                   el CSS y el icono: se copia tal cual a la raíz
```

`salida/` (el sitio compilado) y `.ehundu/` (lo que recuerda el motor) los crea Ehundu y no se versionan.

## Publicarlo

Descomenta la sección `despliegue` de `sitio.yml` y rellénala. La contraseña no va ahí, porque `sitio.yml` se comparte: crea un fichero `.secretos.yml` en la raíz del proyecto, entre comillas simples:

```yaml
despliegue:
  clave: 'la-contraseña'
```

Antes de subir nada, mira qué haría:

```
composer desplegar -- --simular
```

Y cuando todo cuadre, `composer desplegar`. La guía [Desplegar el sitio](https://ehundu.com/documentacion/guias/desplegar/) explica los destinos (carpeta, FTP, SFTP y S3) y cómo decide Ehundu qué sube y qué borra.

## Para seguir

- [Varios idiomas](https://ehundu.com/documentacion/guias/varios-idiomas/), la guía de Ehundu sobre todo esto.
- [Tu primer sitio](https://ehundu.com/documentacion/empezar/tu-primer-sitio/), un recorrido paso a paso.
- [La hoja de referencia rápida](https://ehundu.com/documentacion/empezar/hoja-rapida/), con todo lo que vas a necesitar cada día.

Licencia MIT.
