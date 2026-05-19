FutbolWebApp — Rama eventos-navegacion

Implementación de las tareas de navegación entre vistas en DataFlex 2025.

Basado en los puntos definidos en la presentación DF9 (Slides 7 y 8).

ShowInfoBox según tipo de navegación

Implementado en ZoomLiga.wo, ZoomEquipo.wo, ZoomJugador.wo, SelectLiga.wo y SelectPosicion.wo, dentro de su OnNavigateForward.

Se distinguen los 4 tipos con Send ShowInfoBox:



nfFromMain: llegada desde el Select de la misma tabla.

nfFromParent: llegada desde una tabla padre.

nfFromChild: llegada desde una tabla hija.

nfUndefined: navegación sin relación entre tablas.



From Main — Select-Zoom

Caso cubierto por la navegación estándar de DataFlex: desde SelectLiga se pulsa el icono de detalle de una liga y se entra en ZoomLiga. El OnNavigateForward detecta nfFromMain y se muestra el mensaje correspondiente.

El hoInvokingObject que llega como parámetro es el control concreto que disparó la navegación (en este caso el cWebColumnButton de la lista de SelectLiga).



From Parent — Botón "Ver equipos" con Constrain\_File

Implementado en ZoomLiga.wo y SelectEquipo.wo.



En ZoomLiga.wo se añade el cWebMenuItem oVerEquiposBtn con WebRegisterPath ntNavigateForward oSelectEquipo.

En SelectEquipo.wo, dentro del caso nfFromParent del OnNavigateForward, se aplica:

Set Constrain\_File of oequipo\_DD to liga.File\_Number

Esto filtra la tabla equipo mostrando solo los registros relacionados con la liga del registro actual. Probado: desde LaLiga se ven Real Madrid, FC Barcelona y Atlético de Madrid; desde Premier League se ven los equipos ingleses.

From Child — Botón "Ver liga" en ZoomEquipo

Implementado en ZoomEquipo.wo y SelectLiga.wo.

Se añade el cWebMenuItem oVerLigaBtn que navega a oSelectLiga desde un Zoom de la tabla hija (equipo). DataFlex detecta esta navegación como nfFromChild al llegar a SelectLiga, lo que se confirma con el ShowInfoBox correspondiente.

Undefined — Paso de información con NamedValues

Implementado en ZoomJugador.wo y SelectPosicion.wo.

Como entre jugador y posicion no existe relación padre-hija en los DataDictionaries, la navegación se detecta como nfUndefined. Para pasar información personalizada se usan los NamedValues del struct tWebNavigateData.

Envío (ZoomJugador.wo, dentro de OnGetNavigateForwardData del botón "Ver posiciones"):

Get NamedValueAdd NavigateData.NamedValues "NombreJugador" (jugador.Nombre) to NavigateData.NamedValues

Get NamedValueAdd NavigateData.NamedValues "ApellidoJugador" (jugador.Apellido) to NavigateData.NamedValues

Recepción (SelectPosicion.wo, dentro del caso nfUndefined del OnNavigateForward):

Get NamedValueGet NavigateData.NamedValues "NombreJugador" to sNombreJugador

Get NamedValueGet NavigateData.NamedValues "ApellidoJugador" to sApellidoJugador

Probado con el jugador Federico Valverde: el popup muestra "Vengo desde Jugador: Federico Valverde".

Lista de eventos en una navegación

Navegación hacia adelante (Forward):



OnGetNavigateForwardData -> en el objeto que invoca la navegación (botón, item de menú, columna...)

OnNavigateForward -> en la vista destino

OnBeforeShow -> en la vista destino

OnShow -> en la vista destino



Navegación hacia atrás (Back):



OnGetNavigateBackData -> en la vista que se está cerrando

OnBeforeShow -> en la vista a la que se vuelve

OnNavigateBack -> en el objeto invocador original

OnShow -> en la vista a la que se vuelve



La navegación Back solo existe como parte de una navegación Forward previa.

Pendiente

La variante de mostrar la lista de equipos integrada DENTRO de ZoomLiga (Slide 7, punto 2 del PPT) no ha sido implementada en esta entrega. 
Como alternativa se ha implementado el botón "Ver equipos" descrito en el apartado "From Parent".



