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

**Distribución de Popularidad (por valoraciones)**

<img width="600" alt="distribucion_libros_popularidad_valoraciones" src="https://github.com/user-attachments/assets/05f4a230-9789-41f6-baaf-b6fd9973a7a6" />

Solo 289 libros superan los 500.000–5.000.000 de valoraciones, por lo que pueden considerarse best-sellers históricos. El máximo de valoraciones es 6.801.077, correspondiente a Harry Potter and the Philosopher’s Stone. La distribución de rating_count está fuertemente sesgada a la derecha. Aunque la media (18.737) es alta, la mediana (2.310) indica que la mayoría de los libros tienen un número moderado de valoraciones. La mayor concentración se encuentra entre 1.000 y 99.000 valoraciones (3.908 libros), seguida por el rango de 100 a 999 (2.148 libros), mientras que los libros con menos de 100 valoraciones son minoritarios (1.551)

**Distribución de Popularidad (por reseñas)**

<img width="600" alt="distribucion_libros_popularidad_resenias" src="https://github.com/user-attachments/assets/9f5cd9d1-e617-4396-9330-069c280af729" />

La mayoría de los libros del conjunto presenta pocas reseñas: casi 4.100 tienen entre 100 y 999, y más de 3.800 cuentan con menos de 100. En contraste, solo 3 libros superan las 100.000 reseñas (The Fault in Our Stars, The Girl on the Train y Harry Potter and the Philosopher’s Stone), lo que evidencia una alta concentración de popularidad en pocos títulos. Este patrón refleja una distribución altamente desigual, donde la atención de los lectores se focaliza en unos pocos libros muy populares, mientras que la mayoría recibe una visibilidad limitada. Además, 289 libros no registran ninguna reseña.

**Correlación Rating vs Review**

<img width="600" alt="heatmap_valor_rese" src="https://github.com/user-attachments/assets/efdb1a6d-db2a-4dfc-91d8-599c57be45cb" />

Los libros con muchas valoraciones suelen tener también muchas reseñas, aunque estos casos son excepcionales. Solo 1 libro supera los 5 millones de valoraciones y 100.000 reseñas, mientras que 2 libros tienen entre 1 y 5 millones de valoraciones con más de 100.000 reseñas, sumando un total de 3 títulos con máxima popularidad en ambas métricas. La mayoría se concentra en rangos medios o bajos: 3.126 libros tienen entre 1.000 y 9.900 valoraciones y entre 100 y 999 reseñas, y 1.279 libros se sitúan en el nivel más bajo para ambas métricas. Además, 13 libros no registran ninguna valoración ni reseña.
# Modelo Dimensional
- Tipo de Tabla de Hechos
Snapshot (Instantánea): Captura el estado de los libros en un momento específico.
- Granularidad
Nivel libro-snapshot: Cada registro representa un libro con sus métricas acumuladas.
- Tasa de Refresco
Trimestral: Para seguir evolución del mercado.
- Métricas
- Diseño de dimensiones
# Cuadro de Mando
- Usuario final: Responsable del departamento de Marketing
- Propósito: Facilita la identificación de libros con alto potencial comercial, géneros preferidos y autores prometedores. Esto permite optimizar campañas, reducir riesgos en inversiones promocionales y anticiparse a las tendencias del mercado.
- Frecuencia: Actualización trimestral (push) con alertas adicionales si se detectan cambios significativos. También disponible bajo demanda (pull) para consultas específicas.
- Adopción: Se integrará como herramienta clave en la planificación de campañas, apoyando decisiones estratégicas.
--------

**Visión General**

<img width="700" alt="Cuadro de mando1" src="https://github.com/user-attachments/assets/ba205221-1bd8-4c90-8af6-5c3efcdc37da" />

**Tendencias de reseñas y valoraciones**

<img width="700" alt="Cuadro de mando2" src="https://github.com/user-attachments/assets/b5a69a54-c2a0-4710-bfab-9ece8d8e7fcb" />

**Editoriales Líderes**

<img width="700" alt="Cuadro de mando3" src="https://github.com/user-attachments/assets/4c0f76ef-2431-45b9-ba2b-94ac43f7e0ec" />

**Preferencias Literarias**

<img width="700" alt="Cuadro de mando4" src="https://github.com/user-attachments/assets/a0b1f108-1734-4feb-bc89-09100ed13ae4" />

**Segmentación Popularidad-Satisfacción**

<img width="700" alt="Cuadro de mando5" src="https://github.com/user-attachments/assets/eb9626b4-9d3d-41dc-b79e-c497f1c2cb95" />

**Caso de éxito**

<img width="700" alt="Cuadro de mand6" src="https://github.com/user-attachments/assets/726d843b-cf6e-4b6c-ac02-76d80859bf8e" />

#
Puedes consultar el notebook en Google Colab para más detalles [aquí](https://colab.research.google.com/drive/1qGrlCj0gDfoIv6kngkFtkCD6-ryCg0tK?usp=sharing).

