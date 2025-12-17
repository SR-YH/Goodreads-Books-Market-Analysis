# Descripción

El dataset recoge información sobre los 10.000 mejores libros de todos los tiempos, recopilada a través de Goodreads. Permite analizar la calidad, popularidad y distribución de valoraciones de los libros, así como apoyar decisiones editoriales y de marketing.

El análisis se centra en el estudio de los libros más relevantes para entender tendencias y preferencias del mercado, apoyando decisiones estratégicas en áreas editoriales, marketing y ventas, como promoción de autores, reediciones, adquisición de derechos o predicción de tendencias literarias.

# Tipo de tabla de hecho

Se trata de una tabla de hechos tipo snapshot, donde cada fila representa el estado de un libro en el momento de la recopilación de datos. No se registran eventos individuales ni cambios históricos. Para analizar tendencias sería necesario contar con múltiples instantáneas.

# Granularidad

Cada registro representa un libro-snapshot, con su estado y métricas acumuladas en ese instante.  

# Tasa de refresco

La tabla se actualizará trimestralmente para seguir la evolución de tendencias del mercado.

# Medidas

| Medida | Columna | Descripción |
|--------|---------|------------|
| Calificación media | `average_rating` | Promedio ponderado de las calificaciones del libro. No aditiva entre libros. |
| Total de valoraciones | `rating_count` | Número total de valoraciones recibidas. Aditiva entre libros. |
| Conteo total de reseñas | `review_count` | Número total de reseñas de texto. Aditiva. |
| Valoraciones 5 estrellas | `five_star_ratings` | Cantidad de valoraciones de 5 estrellas. Aditiva. |
| Valoraciones 4 estrellas | `four_star_ratings` | Cantidad de valoraciones de 4 estrellas. Aditiva. |
| Valoraciones 3 estrellas | `three_star_ratings` | Cantidad de valoraciones de 3 estrellas. Aditiva. |
| Valoraciones 2 estrellas | `two_star_ratings` | Cantidad de valoraciones de 2 estrellas. Aditiva. |
| Valoraciones 1 estrella | `one_star_ratings` | Cantidad de valoraciones de 1 estrella. Aditiva. |

# Dimensiones

Se han definido varias dimensiones para agrupar las variables del dataset:

- **Dim_Book:** información bibliográfica esencial (título, editorial, serie, año).

<img width="500" alt="Dim_Book" src="https://github.com/user-attachments/assets/90d9a24b-bc1d-4f06-a7c8-4771a45b7f95" />
  
- **Dim_Author, Dim_Genre, Dim_Awards:** capturan autores, géneros y premios.
  - **Dim_Author**
    
  <img width="500" alt="Dim-Author" src="https://github.com/user-attachments/assets/36984db3-9075-4505-b816-1678e573b8d2" />

  - **Dim_Genre**
  
  <img width="500" alt="Dim-genre" src="https://github.com/user-attachments/assets/2b6c5549-354b-4cb9-a3ff-57852cd620cf" />
    
  - **Dim_Awards**
  
  <img width="500" alt="Dim_Awards" src="https://github.com/user-attachments/assets/f9609ebe-28cb-4438-872d-a699e376b7d2" />

- **Tablas puente:** permiten relaciones muchos-a-muchos entre Dim_Book y las demás dimensiones.

## Tablas principales

- **Tablas grupos**
  - **Dim_Grupo_Author**
  
  <img width="500" alt="Dim_Grupo_Author" src="https://github.com/user-attachments/assets/d6555d43-8b14-4989-b474-abca1d0e8501" />

  - **Dim_Grupo_Genre**
  <img width="500" alt="Dim_Grupo_Genre" src="https://github.com/user-attachments/assets/55ca6425-2830-439b-8535-af941ba9cbab" />

  - **Dim_Grupo_Awards**
  
  <img width="500" alt="Dim_Grupo_Awards" src="https://github.com/user-attachments/assets/e745c7b7-35d3-4f42-86bc-8659e88784fa" />

- **Tablas puentes**
  - **Dim_Puente_Author**
  
  <img width="500" alt="Dim_Puente_Author" src="https://github.com/user-attachments/assets/5658ed28-1772-46d2-b41c-308342398a9a" />

  - **Dim_Puente_Genre**
  
  <img width="500" alt="Dim_Puente_Genre" src="https://github.com/user-attachments/assets/e1d43ac5-7e33-4765-bada-39ddb97ab25b" />

  - **Dim_Puente_Awards**
  <img width="400" alt="Dim_Puente_Awards" src="https://github.com/user-attachments/assets/36d07e54-36cd-46cb-b002-b19eafddfdb1" />

#  Hecho

<img alt="hecho_analisis_mercado_libros" src="https://github.com/user-attachments/assets/3ce3e6a5-5401-44b5-b9e0-967b5fbb3cf2" />


# Tabla de Hechos

<img width="300" alt="hecho" src="https://github.com/user-attachments/assets/b9c8a00d-537f-4f4d-9773-72de49450c34" />

- Se aplican **SCD tipo 1** (slowly changing dimensions): se reemplazan valores antiguos por nuevos, reflejando siempre el estado actual de géneros y premios.  
- Permite generar reportes orientados a:
  - Popularidad y recepción de libros  
  - Comportamiento de valoraciones de usuarios  
  - Tendencias por género literario  
  - Desempeño de autores y editoriales  
  - Evolución temporal de la recepción literaria
