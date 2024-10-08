# Práctica 3 de Visión por Computador.

En esta práctica se hace uso de funciones para detectar contornos como **'threshold'** (para obtener una imagen binaria) y **'findContours'** (para detectar contornos en la imagen) con el fin de detectar objetos en una imagen y ser capaces de categorizarlos.

## 3.1. Objetivos

1. Aprender a **extraer información** geométrica de una imagen.
2. **Categorizar** los objetos a partir de dicha información.

## 3.2. Tarea 1: Capturar una o vacias imágenes con monedas no solapadas y visualizarla(s). 

Para realizar esta parte de la primera tarea se ha creado la función **'mostrar_imagenes'** que acepta como parámetros una lista con las rutas a las imágenes que se desean visualizar y otra lista con los títulos que se desea poner en cada imagen (este último parámetro, por defecto, se acepta vacío).

La función mencionada hace uso de un pequeño algoritmo para mostrar todas las imágenes en una sola con el módulo **'matplotlib'**.

Se hace uso de manejo de errores en caso de que el tamaño de las listas pasadas por parámetro no sean del mismo tamaño.

El resultado es algo similar a lo que vemos en la siguiente imagen:

![**Imagen 1.** Visualización de imágenes haciendo uso de la funcion creada.](/P3/assets/img1.png)

### 3.2.1. Identificar de forma interactica una moneda de un valor determinado en la imagen.

Primero se ha creado una función llamada **'detectar_monedas'** que acepta una imagen por parámetro. Dicha función hace lo siguiente:

1. Lee la imagen con **'imread'** de CV2 y la pasa de BGR a RGB.
2. Convierte dicha imagen a escala de grises haciendo uso de **'cvtColor'** con el segundo parámetro **'cv2.COLOR_RGB2GRAY'**.
3. Aplica thresholding para obtener una imagen binaria con **'threshold'** usando como cuarto parámetro **'cv2.THRESH_BINARY_INV + cv2.THRESH_OTSU'**.
4. Obtiene los contornos exteriores con **'findContours'** haciendo uso del parámetro **'RETR_EXTERNAL'** y dibuja los bordes en la imagen original a través de un bucle que aplica un filtro para evitar contornos muy pequeños que no nos interesan.
5. Llama a la función mencionada en 
aplica thresholding para obtener una imagen binaria, obtiene los contornos, dibuja los contornos en la imagen y la muestra con la función mencionada en el **apartado 3.2**.

Se ha creado una clase **Moneda** con el fin de reciclar código para las características de una moneda. Los atributos de la clase son los siguientes:

- **Nombre (str)**: Representa el nombre de la moneda.
- **Valor (int)**: Representa el valor de la moneda.
- **Cantidad (int)**: Cantidad de monedas con las mismas características encontradas.
- **Píxel Mínimo (float)**: Representa el número mínimo de píxeles que tiene una moneda en su diámetro.
- **Píxel Máximo (float)**: Representa el número máximo de píxeles que tiene una moneda en su diámetro.

También se han creado funciones pertenecientes a la clase mencionada con el fin de reciclar código. Las funciones son:

- **resetear_cantidad**: Esta función establece el valor del atributo **'cantidad'** a 0.
- **incrementar_cantidad**: Suma 1 al atributo **'cantidad'** cada vez que se llama.
- **total_valor**: Devuelve el valor total de la moneda en la imagen.
- **mostrar_monedas**: Muestra en forma de tabla toda la información sobre las monedas pasadas por parámetro.
- **calcular_rangos**: Calcula los rángos de píxeles máximo y mínimo que puede tener una moneda.

Cabe recalcar que la clase 'Moneda`está implementada de forma que sea posible usar cualquier tipo de moneda (por este motivo las funciones aceptan un parámetro), sin embargo, para esta práctica haremos uso de la moneda Europea.

Se ha creado una función llamada **click** para manejar los eventos de click del mouse que detecta cuando el usuario hace click en la imagen. Su objetivo es capturar las coordenadas del punto donde se realizó el click. Sus parametros son:
- event (int): Tipo de evento.
- x (int): Coordenada x del evento.
- y (int): Coordenada y del evento.
- flags (int): Flags del evento.
- param (object): Parámetro adicional del evento.

Esta función hace lo siguiente:
1. Cuando el evento detecta un click izquierdo (cv2.EVENT_LBUTTONDOWN) guarda las coordenadas (x, y) en la variable global point
2. Cierra la ventana que muestra la imagen usando cv2.destroyAllWindows().

También se ha creado una función llamada **interaccion** que se encarga de procesar la imagen y permitir la interacción del usuario. Permite que el usuario seleccione una moneda haciendo click en la imagen y luego clasifica las monedas detectadas. Esta función hace lo siguiente: 
1. Resetear la cantidad de monedas: Antes de procesar la imagen, resetea la cantidad de cada tipo de moneda usando el método **resetear_cantidad** de cada instancia de la clase Moneda.

2. Cargar la imagen y detectar monedas:
- La imagen es cargada desde la ruta proporcionada.
- Dependiendo de si se ha especificado tipo_conteo="mejorado", la imagen se procesa utilizando la función **detectar_monedas_mejorado** o la función estándar **detectar_monedas**.

3. Mostrar la imagen y esperar un click:
- Se muestra la imagen procesada usando **cv2.imshow()**.
- Se cambia el título de la ventana a "Haga click sobre la moneda de 1 EURO" para guiar al usuario.
- La función **cv2.setMouseCallback** asocia la ventana con el manejador de eventos click, que capturará el punto seleccionado por el usuario.
- La función espera hasta que se detecte un click con **cv2.waitKey(0)**.
4. Validar que se hizo un click: Si el usuario no hace click, se lanza un error. Se verifica que point no sea None mediante assert.
5. Determinar la moneda seleccionada:
- Si el conteo es mejorado: Se recorren los bordes de los círculos detectados, verificando si el punto seleccionado por el usuario está dentro de alguno de los círculos (monedas).
- Si el conteo no es mejorado: Se recorre cada contorno detectado, y se verifica si el punto seleccionado está dentro del radio del círculo que encierra el contorno. Luego se calcula el diámetro de la moneda seleccionada y se ajustan los rangos de tamaño de las monedas usando el método **calcular_rangos**.
6. Clasificar las monedas:Según si se usa la versión mejorada o estándar, las monedas se clasifican con **clasificar_monedas_mejorado** o **clasificar_monedas**.

### 3.2.2. Mostrar en pantalla el número de monedas y la cantidad de dinero presente en la imagen.
1. Mostrar el nombre de las monedas en la imagen:Se recorren las monedas clasificadas y se escribe su nombre y posición en la imagen usando **cv2.putText()**.
2. Mostrar los resultados:
- La función **Moneda.mostrar_monedas()** muestra una tabla con la información de cada tipo de moneda, incluyendo el valor y la cantidad detectada.
- La imagen final, con los nombres de las monedas sobrepuestas, se muestra usando **mostrar_imagenes().**
- Finalmente, se calcula el valor total de todas las monedas detectadas e imprime el resultado.

### 3.2.3. ¿Qué problemas se han encontrado?

ESTO LO HAGO YO

## 3.2.4. Extras: Considerar que la imagen pueda contener objetos que no son monedas y/o haya solape entre las monedas. Demo en vivo.

EXPLICAR LA FUNCIÓN 'detectar_monedas_mejorado'. DE FORMA DETALLADA.
