# Cuaderno de proyecto ***Gymkana Toledo***

## Resumen
Este proyecto es una pequeña y sencilla aplicación web que utiliza HTML, JS y CSS para permitir la realización de una Gymkana en la ciudad de Toledo. Fue un proyecto *"express"* creado en un tiempo corto (aproximadamente, un total de 16HH) para este fin.

## Contexto
La idea de este proyecto es de hecho de mi amiga Kaelamoran, quien por el entonces estaba realizando prácticas como profesora de secundaria. La clase en la que estaba trabajando iba a realizar una excursión a la histórica ciudad española de Toledo. Se comprometió a organizar una actividad de tipo *Gymkana* para la clase con el objetivo de hacer que la excursión fuese más amena para los alumnos al tiempo que les daba una excusa para explorar la ciudad más o menos a su propio ritmo. Esta *Gymkana* consistía en dirigir a los alumnos (separados en múltiples grupos) hacia diferentes lugares de importancia histórica de la ciudad a través de acertijos. Una vez localizado el lugar, debían sacarse una foto delante de la estructura mencionada en el acertijo para demostrar que habían llegado.

Un mes aproximadamente antes de la realización de la *Gynkana* se le informó que no se le iba a permitir ir a la excursión, por lo que le sería imposible dirigir la *Gymkana* desde el propio lugar. A esto se le sumaba el como coordinar un grupo medianamente grande de personas participando en el mismo evento sin dar demasiadas pistas acerca de la solución ni penalizar a diferentes grupos por tener que ir a buscarte tras resolver una prueba. De ahí se le ocurrió preguntarme si sería posible crear una aplicación que permitiese realizar fácilemte la *Gymkana* de una forma semi-automática, cosa que cobró incluso mayor importancia ante la imposibilidad ya mencionada de que ella estuviese presente durante su realización.

Tras hablar al respecto, y descubrir que *GitHub* permitía alojar pequeñas aplicaciones web sencillas en su página, se me ocurrió crear una sencilla aplicación web para permitir la realización de esta *Gymkana* dentro de los marcos que me había propuesto ella. El plan era construir una serie de pantallas que mostrasen un acertijo a los concursantes, les permitiera subir una imagen de la localización y permitiese a un profesor, en remoto, indicar si la respuesta era correcta o no.

## Desarrollo del proyecto
Este proyecto tuvo una pequeña fase de planificación con mi amiga Kaela, que me indicó sus intenciones con la aplicación. De esta fase identificamos las siguientes pantallas:

* Una pantalla para que profesores y alumnos iniciaran sesión
* Una pantalla para que los alumnos pudieran ver los acertijos y subir sus propuestas
* Una pantalla para que los profesores validasen estos resultados
* Una pantalla para que los alumnos pudiesen ver su clasificación en un marcador global

Poco después de esta conversación, se empezó a trabajar en una primera versión cruda preliminar de esta aplicación web. Esta fase avanzó lentamente al estar trabajando con tecnologías, por el entonces, nuevas para mí, en concreto, la publicación en *GitHub* de una aplicación web. Por el entonces estaba también pasando por un momento de transición laboral importante para mí por lo que no podía dedicarle todo el tiempo que quería. Esta situción no hizo más que agravarse cuando informaron a Kaela que no podía participar físicamente en la *Gynkana*. Durante este periodo recibí también los acertijos, pistas y soluciones para incluirlos en la base de datos en cuanto tuviese una.

Esta situación culminó en una "maratón de programación" donde se realizó el proyecto prácticamente en su totalidad en una noche el fin de semana antes de la fecha límite. La solución final fue la aplicación que tenemos delante. Hubo varios aspectos que se tuvieron que retocar para poder acelerar el proyecto y poder terminarlo a tiempo, especialmente al respecto de creación de cuentas y seguridad, debido a esta limitación de tiempo y la imposibilidad de explorar las soluciones de almacenamiento.

Se decidió obtar por una solución sencilla usando la base de datos de Google Firebase como solución de almacenamiento, en concreto el realtime-database, una solución más rápida a su versión actual, aunque también menos segura. En cuanto a seguridad, por tanto, se decidió lo siguiente:

* En vez de que cada equipo se creara una cuenta, se decidió predefinir una cuenta por grupo y una cuenta para los profesores con unas contraseñas predefinidas.
* La base de datos, almacenada en formato JSON, sería accesible mediante llamadas HTTP usando el servicio REST proporcionado por Firebase.
* La mayor parte de la seguridad sería gestionada por el frontend, almacenando enel *session storage* el token de usuario

Cuando un usuario tratase de acceder a una pantalla, se comprobaría si hay un token de usuario. Si no lo hay, lo lleva a iniciar sesión. Una vez iniciada, el token se guarda en el *session storage* para poder proseguir por las pantallas. Solo los tokens de alumnos tendrían acceso a las pantallas de acertijos y solo el de profesores tendría acceso al de validación. Las únicas pantallas "públicas" serían la de inicio y la del marcador (además, claro, de la de inicio de sesión).

Todas las páginas se realizaron a partir de estas especificaciones y, teniendo en cuenta la vulnerabilidad de seguridad que tenía la base de datos "pública" a través de REST, se obtó por almacenar las imágenes enviadas mediante *b64* y se prometió borrar estos datos personales (las imágenes) que se subiesen en menos de 24 horas tras la realización de la *Gymkana* y mantener el enlace en secreto (solo llamado desde la aplicación) hasta después de realizar la prueba.

## Vista conceptual del proyecto
En este apartado, explicamos el objetivo conceptual detrás de cada una de las pantallas. Esta vista explica el motivo detrás de la existencia de cada pantalla, abstrayéndose de los detalles técnicos de los mismos, los cuales veremos en el siguiente apartado de este documento. Iremos paso a paso, a través de las pantallas siguiendo el orden esperado por el usuario.

### Página de inicio
La página de inicio tiene una idea bastante sencilla de comprender. Esta pantalla es la primera que verán los usuarios al acceder a la página. En el caso de nuestra aplicación web, nuestro objetivo es, mediante una interfaz sencilla, dirigir a los usuarios fácil y rápidamente a las pantallas relevantes de la aplicación.

Esta pantalla tiene un simple mensaje de bienvenida y unos botones que permiten dirigir a los usuarios a las tres pantallas principales de la aplicación:

* **Gymkana:** la pantalla más importante para los alumnos, para poder participar en la *gymkana*.
* **Marcador:** la pantalla de marcador, para comprobar la clasificación de los equipos en el evento.
* **Validar:** un botón algo más apartado del resto para marcar una diferencia. Este es el botón que interesa a los profesores para validar las respuestas de los alumnos.

### Página de inicio de sesión
Esta página no es accesible "directamente" desde la página de inicio, pero es probablemente la primera página que se verá tras la página de inicio. Al intentar navegar a una pantalla protegida (**Gymkana** o **Validar**), se comprueba si el usuario tiene una sesión activa. Si no es el caso, se redireige a esta pantalla. En esta pantalla, el usuario inicia sesión con su usuario y contraseña con el objetivo de identificarse ante la aplicación. Según la identidad del usuario, esta página redirige automáticamente a la pantalla de **Gymkana** o de **Validar** según el tipo de usuario que sea (alumno o profesor respectivamente).

El diseño de esta página es lo más sencilla posible, con una caja para indicar el usuario, otra para la contraseña y un botón para realizar el inicio de sesión.

### Pantalla de Gymkana
Esta es la pantalla más importante de la aplicación, empleada por los alumnos para realizar la actividad como tal. En esta pantalla, se comprobaría primero que el usuario haya iniciado sesión y, en base a los datos obtenidos del mismo, automáticamente se accedería a la base de datos y se mostraría el acertijo en el que se encontrasen, que podía contener texto, imágenes o ambas cosas. También se incluye una caja con la que los usuarios podían subir una imagen como respuesta al acertijo. 

Una vez subida una respuesta, se notifica a los usuarios que recargen la página de vez en cuando para traer el nuevo acertijo una vez validada la respuesta, o la pista especial en caso de fallo. De esta forma, los alumnos pueden ir avanzando en la *Gymkana* a base de ir subiendo respuestas y recargando para ver el siguiente acertijo.

### Pantalla de Validación
Esta es la segunda pantalla más importante de la aplicación, prácticamente a la par con la de **Gymkana**. Solo tiene "menor importancia" por el número de usuarios que lo va a utilizar (uno o como mucho dos). En esta pantalla, una vez comprobado que el usuario ha iniciado sesión y que es profesor, se muestra un listado de las últimas respuestas de los alumnos, así como un indicador del grupo al que pertenecen y la solución del acertijo al que responden. Junto a cada imagen, se podrá obtar por validar positiva o negativamente la respuesta. Esto provoca, automáticamente, que el usuario pueda acceder al siguiente acertijo, o a la pista especial del acertijo en el que se encontrasen en el caso de que hubiesen fallado.

Al igual que la pantalla de **Gymnkana**, requiere que el profesor refresque la pantalla de vez en cuando para ver nuevas respuestas.

### Pantalla de marcador
Esta pantalla permite ver en una tabla la clasificación de los grupos participando en la *Gymkana*. El objetivo de esta pantalla era añadir un aspecto competitivo al evento para animar a los alumnos a participar y esforzarse más. Es una pantalla sencilla con una tabla de clasificación y, puesto que se espera que fuesen los alumnos quienes usaran esta pantalla, un botón que lleve de vuelta a la pantalla de **Gymkana**. Este marcador extrae los datos de la base de datos, por lo que mostrará los datos que hubiese en el momento de acceder a la página.

Siguiendo las pautas marcadas por Kaela, el discriminador principal era la cantidad de acertijos resueltos y, en caso de empate, quien hubiese tardado menos en resolver el los acertijos.

## Vista técnica del proyecto
El proyecto tiene la mayoría de sus componentes directamente en el directorio raíz. Estos son, en resumen, las páginas HTML, que representan los esqueletos de las vistas. Estas maquetas incluyen la maqueta principal de la pantalla y la definición de los controles con los que podían interactuar los usuarios.

En el directorio CSS se encuentran los archivos de estilo *CSS* que, añadidos a las pantallas diseñadas con HTML, da un aspecto "bonito" y amigable al usuario a la pantalla. Este directorio se mantiene prácticamente por convención, ya que, tratándose de una aplicación tan pequeña, hemos incluído todos los estilos en un único fichero CSS. En una aplicación más grande, aquí localizaríamos más ficheros de este tipo, pero en nuestro caso, con un solo fichero bastaba. Esto está relacionado con el directorio **img** que incluye imágenes para el fondo de pantalla (tanto para su visualización desde ordenador como desde teléfono móvil).

Finalmente, tenemos el directorio JS, que incluye el "músculo" de la aplicación, la parte funcional de la misma. Las funciones incluidas en los archivos JS son las comprobaciones automáticas realizadas al acceder a la pantalla (o recargarla), como puede ser el comprobar el usuario o llamar a la base de datos para obtener las especificaciones de un acertijo, y los controles de los botones que se ven por la pantalla. 

Originalmente, se había planeado crear un *trigger* automático que consultara automáticamente a la base de datos sin que el usuario tuviese que hacer nada. Sin embargo, esto requería un tiempo de estudio que, tal y como se explicó al inicio de este documento, no teníamos. Por ello, se decidió que el *trigger* fuese la recarga de la página, que añadía (por desgracia) la necesidad de que el usuario recargase la página de vez en cuando para asegurarse que la pantalla estaba actualizada.

Para mayor información acerca de cada uno de los métodos JS utilizados, puede inspeccionarse el código, el cual ha sido autodocumentado usando el estándar de documentación *Javadoc*, posterior a la finalización del proyecto, para facilitar su comprensión. Estas funciones se han mantenido intactas salvo por la eliminación del token de seguridad de la ya inexistente base de datos, que borramos por seguridad, como ya se ha mencionado.

Por último, indicamos que existen dos ficheros adicionales incluidos en el directorio raíz del proyecto:

* **_config.yml:** fichero generado automáticamente por GitHub para la configuración de la página web. Este fichero se ha dejado con su configuración por defecto.
* **gymkanatoledo-default-rtdb-export.json:** fichero extraido de la base de datos Firebase tras terminar la *Gymkana*. Se incluye en el proyecto para que se pueda ver la estructura de la base de datos y así entender mejor el código. Se trata de un archivo en formato *JSON* que inluye los acertijos, soluciones, pistas, el marcador, etc. Lo único que no se ha incluido son las respuestas subidas por los alumnos al considerarse datos personales en el marco del RGPD de la UE.

## Conclusión del proyecto
La vida útil de esta aplicación, de por sí, se esperaba que fuese corta, considerando que se iba a usar en un solo día. A esto, por desgracia, se le sumó el hecho de que la profesora responsable, durante los primeros momentos del evento, alegó que no funcionaba; a pesar de que los alumnos lo estaban usando, que Kaela validó varios resultados, y que yo (como técnico) detecto actualizaciones en la base de datos. Por ello, concluimos que el "fallo" era realmente por parte del usuario que ejercía el rol de profesor en la actividad; lo que acortó la ya breve vida de la aplicación.

El objetivo del proyecto, sin embargo, sí se alcanzó, ya que la aplicación fue creada dentro de los parámetros esperados, se entregó a Kaela, quien comprobó que funcionaba, y me sirvió para cumplir el objetivo personal de crear una pequeña aplicación web desde cero, que me había planteado.

Este proyecto fue, principalmente, una experiencia de aprendizaje muy positiva para mí y, como tal, quedará recogido en este archivo de proyectos creados.

## Lecciones aprendidas
En este proyecto, se pudo llevar a la práctica conocimientos teóricos acerca de diseño de aplicaciones web sencillas usando HTML, JavaScript y CSS. Concretamente, estaba interesado en el uso de JS y CSS para mejorar en estos dos lenguajes con sus diferentes usos.

También dí mis primeros pasos en *Google Firebase*, lo que me lleva a la lección de aprendida de la necesidad de aplicar algo más de tiempo en la investigación y experimentación de tecnologías con las que no haya trabajado antes (como esta) para evitar problemas e imprevistos más adelante. Por concretar, debido a la falta de tiempo con la que me encontraba al final del proyecto, tuve que tirar por tierra toda la estructura de seguridad que había divisado para proteger al máximo los datos personales de los usuarios y obtar por la solución menos segura de Firebase (incluso haciendo público el enlace HTTP de rest durante la duración de la Gymkana) para poder finalizar el proyecto. Con más tiempo y, especialmente, experimentación; podría haber usado una opción más moderna y segura de *Firebase* para este proyecto en vez de haber realizado soluciones más "caseras" para la seguridad de la aplicación.

Por último, también mencionar que esto me ha dado la idea de crear una aplicación movil que permita crear y realizar Gymkanas a través de códigos QR.
