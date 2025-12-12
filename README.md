**by Yanxia Zou**
# Análisis del Dataset del Goodreads
Este proyecto contiene análisis exploratorio de datos (EDA) y diseño de cuadro de mando basado en los 10,000 mejores libros de todos los tiempos de la lista "Best Books Ever" de Goodreads. El objetivo es proporcionar perspectivas sobre tendencias literarias, comportamiento de usuarios y métricas de popularidad para apoyar decisiones estratégicas en marketing editorial.
# Objetivos del proyecto
- Realizar limpieza y preprocesamiento exhaustivo de datos literarios
- Diseñar un modelo dimensional para análisis de mercado
- Identificar patrones de popularidad y valoración de libros
- Crear visualizaciones que faciliten la toma de decisiones de marketing
- Analizar tendencias por género, autor y editorial
# Contextualización del dataset
- 10,000 registros de libros
- 31 variables originales (reducidas a 16 relevantes)
- Incluye valoraciones, reseñas, información bibliográfica y premios
# Descripción de las características
Las columnas del dataset son `id`, `title`, `link`, `series`, `cover_link`, `author`, `author_link`, `rating_count`, `review_count`, `average_rating`, `five_star_ratings`, `four_star_ratings`, `three_star_ratings`, `two_star_ratings`, `one_star_ratings`, `number_of_pages`, `date_published`, `publisher`, `original_title`, `genre_and_votes`, `isbn`, `isbn13`, `asin`, `settings`, `characters`, `awards`, `amazon_redirect_link`, `worldcat_redirect_link`, `recommended_books`, `books_in_series` y `description`.

De acuerdo con las columnas identificadas, podemos clasificarlas en las siguientes categorías: variables de identificación, variables de enlace, variables bibliográficas (que ofrecen información sobre el libro), variables de contenido, variables de métricas de valoración, y variables de información complementaria.

**Variables de identificación**
*   **`id`** : Identificador único asignado por Goodreads a cada libro
*   **`isbn`**: International Standard Book Number (formato antiguo de 10 dígitos)
*   **`isbn13`**:  International Standard Book Number (formato nuevo de 13 dígitos)
*   **`asin`**: Amazon Standard Identification Number

**Variables de enlace**
*   **`link`**: URL que dirige a la página del libro en Goodreads
*   **`cover_link`**: URL de la imagen de portada del libro
*   **`author_link`**: URL a la página del autor en Goodreads
*   **`amazon_redirect_link`**: URL para comprar el libro en Amazon
*   **`worldcat_redirect_link`**: URL a la página del libro en WorldCat (catálogo mundial de bibliotecas)

**Variables bibliográficas**
*   **`title`**: Título del libro
*   **`author`**: Nombre del autor o autores del libro
*   **`original_title`**: Título original del libro
*   **`number_of_pages`**: Número de páginas del libro
*   **`date_published`**: Fecha de publicación del libro
*   **`publisher`**: Editorial que publicó el libro
*   **`series`**:  Indica si el libro forma parte de una serie
*   **`genre_and_votes`**:  Géneros literarios asignados y número de votos para cada género

**Variables de contenido**
*   **`settings`**: Ubicación geográfica/contextos/ambientación donde se desarrolla la historia
*   **`characters`**: Personajes principales que aparecen en el libro
*   **`description`**: Breve sinopsis o descripción del contenido del libro.

**Variables de métricas de valoración**
*   **`average_rating`**: Valoración media obtenida
*   **`rating_count`**: Número total de valoraciones recibidas
*   **`review_count`**: Número total de reseñas escritas por los usuarios.
*   **`five_star_ratings`**: Cantidad de valoraciones de 5 estrellas
*   **`four_star_ratings`**: Cantidad de valoraciones de 4 estrellas
*   **`three_star_ratings`**: Cantidad de valoraciones de 3 estrellas
*   **`two_star_ratings`**: Cantidad de valoraciones de 2 estrellas
*   **`one_star_ratings`**: Cantidad de valoraciones de 1 estrella

**Variables de información complementaria**
*   **`awards`**: Premios literarios recibidos por el libro
*   **`recommended_books`**: Identificadores de los libros recomendados relacionados con este libro
*   **`books_in_series`**: Identificadores de otros libros que forman parte de la misma serie

# Selección de características relevantes
De acuerdo con la sección "descripción de variables", los valores nulos detectados, los tipos de datos de las variables identificados y la relevancia de las variables para el análisis en nuestro negocio, podemos descartar aquellas columnas que no aportan valor a nuestro análisis:
*   `id`, `isbn`, `isbn13` y `asin` : son variables que identifican de forma única los libros, pero no son relevantes para realizar análisis.
*   `link`, `cover_link`, `author_link`, `amazon_redirect_link` y `worldcat_redirect_link` : son variables que almacenan URLs, las cuales no aportan información útil para el análisis ni permiten extraer métricas.
- `number_of_pages`: La variable `number_of_pages `representa el número de páginas de un libro físico. Sin embargo, en el dataset se incluyen también audiolibros o versiones en audio CD, como por ejemplo *Dead Man's Folly* de Agatha Christie, narrado por David Suchet, que figura con solo 6 páginas. Por lo tanto, es más adecuado descartar esta variable.
*   `original_title` : La variable `original_title` nos lleva a pensar que contiene los títulos originales de los libros; sin embargo, los valores están en inglés, por lo que esta columna duplica la información de `title`. Además, presenta 2.460 valores faltantes, lo que representa un 24,6% del total de registros, lo que indica que se trata de una columna con datos de baja calidad.
*   `settings`, `characters` y `description` : son de tipo string y difíciles de analizar cuantitativamente. En la variable `description` ontiene texto extenso con comas y signos de puntuación que pueden generar problemas al procesar los datos. Estas variables requieren técnicas de procesamiento de lenguaje natural (NLP) para ser analizadas adecuadamente.
*   `recommended_books`, `books_in_series` : podrían parecer útiles, pero en realidad solo contienen identificadores de *Goodreads* que no aportan valor relevante para el análisis.

**Variables Conservadas**:
- **Variables bibliográficas**:
  *   `title`: Es de tipo string, pero debe ser recodificado porque contiene registros con caracteres especiales.
  *   `author`: Es un campo multivalor, por lo que debe ser gestionada adecuadamente. Puede transformarse en una *lista*, separarse en múltiples columnas (menos flexible) o dividirse en múltiples filas (una por cada autor y libro). En este caso, optamos por transformarla en una *lista*.
  * `date_published` : Es de tipo string y debe ser convertido a *datetime* y normalizado. Sin embargo, debido a que en muchos casos la informaciónn de mes y de día no está disponible, se ha optado por extraer únicamente el año como dato temporal principal que sería un tipo *int*. Y se cambiará el nombre de columna a `year_published`. Además, se eliminarán las filas que contienen valores nulos (1.66% del total).
  * `publisher`: Se sustituirá los valores faltantes por la cadena "**Unknown**".
  * `series` : Se sustituirá los valores faltantes por la cadena "**Standalone**" (Independiente).
  * `genre_and_votes` : Se transformará en dos columnas: una que contendrá el género del libro y otra que almacenará el número de votos. Al ser un campo multivalor, es necesario manejarlo adecuadamente;en este caso, transformamos en lista de género y votos. Y se sustituirá los valores nulos por "**No genre 0**" que significa sin género y 0 voto.
- **Variables de métricas de valoración**:
  *   `average_rating`
  *   `rating_count`
  *   `review_count`
  *   `five_star_ratings`
  *   `four_star_ratings`
  *   `three_star_ratings`
  *   `two_star_ratings`
  *   `one_star_ratings`
- **Variables de información complementaria**:
  *   `awards`: Es un campo multivalor. En este caso, se transformará en una *lista* y, para manejar los valores nulos, los sustituiremos por la cadena "**No awards**" (No premiado).
# Análisis exploratorio
- Detección y tratamiento de valores nulos
- Identificación de valores atípicos y bestsellers
-  Normalización de caracteres especiales en texto
-  Conversión y estandarización de tipos de datos

# Procesamiento de Datos
-  Extracción de años de publicación desde formatos inconsistentes
-  Separación de campos multivalor (autores, géneros, premios)
-  Creación de categorías de popularidad por niveles
-  Validación de consistencia entre métricas de valoración

# Visualizaciones
## Distribución de Popularidad (por valoraciones)

## Distribución de Popularidad (por reseñas)

## Correlación Rating vs Review
