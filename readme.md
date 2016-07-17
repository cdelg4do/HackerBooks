# Práctica Fundamentos iOS de Carlos Delgado Andrés

**HackerBooks** es un prototipo de aplicación para iOS realizada en Swift 2.2.

Se trata de un lector de libros en PDF para iPad. La interfaz de la app está formada por un *Split View Controller* que divide la pantala en dos partes diferenciadas, conteniendo los siguientes elementos:

* **Tabla de libros**: a la izquierda, consistente en un *Table View Controller* que muestra todo el catálogo de libros dsponibles agrupados por categorías. Incluye también un apartado para los libros favoritos del usuario.

* **Detalle del libro**: a la derecha, se muestra cuando el usuario selecciona un libro en la tabla, y contiene los datos del libro seleccionado, así como un botón para visualizar el PDF correspondiente.

* **Visor de PDF**: también a la derecha, se muestra cuando el usuario pulsa el botón de visualizar el PDF de un libro, en lugar del detalle del libro. Consiste en un *Web View Controller*.

Los datos acerca del catálogo de libros se descargan de *internet* la primera vez que se ejecuta la aplicación, y quedan disponibles localmente para posteriores ejecuciones.

.
### Cuestiones sobre la práctica:

**1- Procesado del fichero JSON de datos: ¿De qué formas se puede determinar si a través de la clase *NSJSONSerialization* se obtiene un objeto *Dictionary* o un *Array de Dictionary*?**

Aplicando un cast (as?) a uno de esos tipos de datos al resultado devuelto por la función JSONObjectWithData(). Si el cast falla, entonces es del otro tipo.

**2- Las imágenes de portada y los PDFs se deben almacenar localmente. ¿Dónde guardarías esos datos?**

Dentro de la carpeta Documents de la app. Concretamente, se crean dos subcarpetas para cachear estos recursos: /Images y /Pdf.

**3- La información sobre los favoritos del usuario debe ser persistente entre una ejecución y otra. ¿De qué formas se podría hacer? Explicar la decisión de diseño tomada.**

Se ha decidido que, cada vez que cambia el status de favorito de un libro, envíe una notificación al TableViewController para que almacene en disco el modelo actualizado (esto es, el listado JSON con todos los libros de la librería). Para ello, se serializa el modelo en un fichero Documents/books.json idéntico al original descargado, pero que además incluye para cada libro un nuevo campo "favorite" que será true o false.

**4- ¿Cómo enviar la información de un libro al controlador de la tabla de libros, cada vez que el usuario marca o desmarca un libro como favorito? ¿Se te ocurren más formas de hacerlo? ¿Cuál te parece mejor y por qué?**

Se puede hacer mediante notificaciones, indicando en los datos de usuario de la notificación qué datos se han enviado. Otra forma sería que el controlador de la tabla actuase de delegado del controlador del libro que se ha cambiado a favorito/no favorito.

Me parece mejor el uso de notificaciones, ya que ello evita tener que implementar un nuevo protocolo de delegado solo para esta función, y además permitiría fácilmente que otro controlador más respondiese a esa acción, en caso de que fuese necesario en el futuro.

**5- Para actualizar la información de la tabla reflejando el cambio de un favorito, se usa el método *reloadData* que vuelve a pedir datos del dataSource del *Table View*. ¿Es esto muy costoso en términos de rendimiento? ¿Por qué? ¿Qué otras alternativas habría y cuándo sería recomendable usarlas?**

No es costoso, ya que el método reloadData() solo refresca el contenido de las filas que sean visibles en ese momento.

Como alternativas, se podrían usar los métodos reloadRows() para actualizar una fila concreta o reloadSections() para una sección concreta.

**6- Cuando el usuario selecciona otro libro en la tabla, el visor de PDF debe actualizarse con el libro correspondiente. ¿Cómo lo harías?**

En este caso, el controlador de tabla envía una notificación a la cuál está suscrito el WebViewController que muestra los PDFs. Este obtiene de la notificación los datos acerca del libro seleccionado y actualiza la vista de acuerdo con esa información.

