<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

### Respuesta

Una forma habitual en C de indicar un error sin excepciones es separar “resultado” y “estado”. La función devuelve un código de estado (0 = OK, distinto de 0 = error) y entrega el valor correcto a través de un parámetro de salida (puntero). Así, no se usan valores centinela ambiguos y el llamador puede decidir cómo informar al usuario, manteniendo la notificación fuera de raiz.

```java
#include <math.h>
#include <stdio.h>

int raiz(double x, double *out) {
    if (x < 0.0) return 1;          // 1 = error dominio
    *out = sqrt(x);
    return 0;
}

int main(void) {
    double r;
    if (raiz(-9.0, &r) != 0) {
        // Decidir aquí cómo informar (fuera de raiz)
        printf("Error: argumento negativo.\n");
    } else {
        printf("Resultado: %.3f\n", r);
    }
}
```
Otra opción clásica es usar el canal de error global errno junto con un valor de retorno (por ejemplo, NAN o 0.0) que el llamador validará. En operaciones de dominio matemático, es idiomático establecer errno = EDOM y devolver NAN; el llamador limpia errno antes, invoca y lo comprueba después. De nuevo, la función no imprime ni notifica: solo señaliza el error; la presentación del error queda fuera.

```java
#include <math.h>
#include <errno.h>
#include <fenv.h>   // opcional si se quisieran banderas de coma flotante
#include <stdio.h>

double raiz(double x) {
    if (x < 0.0) {
        errno = EDOM;               // dominio matemático inválido
        return NAN;                 // señalización por valor centinela
    }
    errno = 0;                      // opcional: no siempre se limpia aquí
    return sqrt(x);
}

int main(void) {
    errno = 0;                      // limpiar antes de llamar
    double r = raiz(-9.0);
    if (errno == EDOM || isnan(r)) {
        // Decidir aquí cómo informar (fuera de raiz)
        printf("Error: argumento negativo.\n");
    } else {
        printf("Resultado: %.3f\n", r);
    }
}
```

Comentario: la primera solución (código de estado + parámetro de salida) evita ambigüedades con valores reales, mientras que errno + NAN es idiomática en funciones de estilo matemático. En ambos diseños, la responsabilidad de informar queda en el llamador, cumpliendo el requisito de no imprimir desde raiz.


## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

### Respuesta

Una excepción es un mecanismo de los lenguajes modernos que permite detener el flujo normal de ejecución cuando ocurre una situación anómala (un error, una condición inesperada o un caso que impide continuar) y transferir el control a un bloque encargado de gestionar ese problema. En lugar de devolver códigos de error como en C, la excepción “interrumpe” la ejecución y salta directamente a un manejador (catch), lo que separa claramente la lógica normal del tratamiento de errores.

El programador las utiliza al implementar funciones para señalar errores de forma fiable y estructurada, sin necesidad de llenar el código con comprobaciones manuales ni valores especiales de retorno. Esto hace que la función pueda centrarse en su comportamiento principal y delegar el manejo del error en quien la llame. Por otra parte, al llamar funciones, las excepciones permiten reaccionar únicamente cuando ocurre un fallo, manteniendo el código limpio y evitando comprobaciones constantes tras cada llamada. Con ello, se mejora la legibilidad, se reduce el riesgo de ignorar errores y se consigue un control más preciso sobre qué hacer ante cada situación excepcional.


## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

### Respuesta

Una traslación directa a Java consiste en señalizar el error lanzando una excepción desde Calculadora.raiz(double) cuando el argumento es negativo, y controlar la situación desde fuera (en main) con un bloque try/catch. De este modo, el método mantiene su responsabilidad (calcular la raíz o indicar que no puede) y la decisión sobre cómo informar al usuario queda en el llamador, tal como se pedía en C.

A continuación se muestra un ejemplo completo y mínimo. Se utiliza IllegalArgumentException (excepción no comprobada) porque el problema es un argumento inválido; alternativamente, podría definirse una excepción propia o usar una comprobada si se quiere forzar el catch en los llamadores.

```java
public class Calculadora {

    /**
     * Devuelve la raíz cuadrada de x.
     * Lanza IllegalArgumentException si x < 0.
     */
    public static double raiz(double x) {
        if (x < 0.0) {
            throw new IllegalArgumentException("El argumento debe ser no negativo: " + x);
        }
        return Math.sqrt(x);
    }

    public static void main(String[] args) {
        // Caso 1: argumento válido
        try {
            double r1 = Calculadora.raiz(9.0);
            System.out.println("raiz(9.0) = " + r1);
        } catch (IllegalArgumentException e) {
            // Control del error desde fuera del método raiz
            System.out.println("Error al calcular raiz(9.0): " + e.getMessage());
        }

        // Caso 2: argumento negativo (provoca excepción)
        try {
            double r2 = Calculadora.raiz(-4.0);
            System.out.println("raiz(-4.0) = " + r2); // no se ejecutará
        } catch (IllegalArgumentException e) {
            // Aquí se decide cómo informar (logging, mensaje al usuario, etc.)
            System.out.println("Error al calcular raiz(-4.0): " + e.getMessage());
        }
    }
}
```
Este diseño separa la lógica normal (cálculo con Math.sqrt) del manejo de errores. El método raiz no imprime ni interactúa con el usuario; simplemente lanza si el precondicionado (x ≥ 0) no se cumple. El main es quien decide cómo notificar (mensaje por consola, log, traducción del error, etc.). Para un estilo más parecido al de C, podría retornarse Double.NaN o un OptionalDouble, pero al usar excepciones se evita que el llamador olvide comprobar un valor centinela y se hace explícita la vía de error.


## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

### Respuesta

Una excepción se lanza cuando un método detecta una situación que impide continuar con su ejecución normal y utiliza throw para interrumpirla de inmediato. Lanzar la excepción significa que el método abandona por completo su trabajo actual y notifica a los niveles superiores de la llamada de que ha ocurrido un error. No se ejecuta ninguna instrucción posterior al throw, por lo que el método deja de producir un resultado y delega en el exterior la responsabilidad de decidir qué hacer.

Controlar o capturar una excepción consiste en envolver la llamada potencialmente problemática dentro de un bloque try y proporcionar uno o varios catch que indiquen cómo manejarla cuando ocurra. Capturar la excepción implica reconocerla y tratarla de manera explícita, ya sea mostrando un mensaje, registrando el error o tomando una acción alternativa. Mientras la excepción esté dentro de un bloque try/catch, el programa puede recuperarse sin interrumpirse abruptamente, separando así la lógica normal del tratamiento del error.

La propagación de una excepción ocurre cuando un método la lanza pero no la captura. En ese caso, la excepción sube al método que lo llamó. Si ese método tampoco la captura, vuelve a subir, y así sucesivamente hasta encontrar un catch adecuado. Durante este proceso, cada función de la pila que no controla la excepción se aborta completamente: sus variables locales se descartan y no se reanuda su ejecución en ningún punto. No existe la posibilidad de retomar la función tras el lugar donde falló; la llamada queda definitivamente eliminada de la pila.

En el ejemplo de la raíz cuadrada, si raiz(-4) lanza IllegalArgumentException, el método sale inmediatamente. Si esta llamada estuviera dentro de otros métodos intermedios que tampoco usen try/catch, todos ellos se irían abandonando en cadena hasta llegar a main. Solo si main dispone de un try/catch se controlará ahí; de lo contrario, la excepción seguirá hasta provocar la finalización del programa. De este modo, la única parte que puede continuar su ejecución es aquella que capture explícitamente la excepción, como en el siguiente fragmento:

```java
try {
    double r = Calculadora.raiz(-4);
    System.out.println(r);
} catch (IllegalArgumentException e) {
    System.out.println("Error: " + e.getMessage());
}
```
Aquí, main es quien controla la excepción, por lo que la propagación se detiene y el programa continúa normalmente después del bloque catch. Las funciones que no la capturaron no se reanudan en ningún caso.


## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

### Respuesta

La propagación natural de excepciones en Java aporta una ventaja fundamental frente al estilo de control de errores en C: permite separar de forma clara la lógica del programa del manejo de fallos, sin tener que comprobar manualmente el resultado de cada función. En C, cada llamada debe ir seguida de un chequeo del código de retorno, lo que genera código repetitivo y propenso a errores si alguna comprobación se olvida. Con excepciones, un método puede detener su ejecución mediante throw y dejar que el error ascienda automáticamente hasta encontrar un punto donde sea tratado, evitando que cada nivel intermedio tenga que participar explícitamente en la gestión del fallo.

Otra ventaja es que la propagación garantiza que el error no puede ser ignorado accidentalmente. En C, un programador puede recibir un valor centinela, como -1, NULL o un código de error, y olvidarse de comprobarlo; el programa continúa con datos inválidos y puede producir fallos más difíciles de rastrear. Con excepciones, si nadie captura el problema, la ejecución no sigue adelante, lo que ayuda a detectar errores temprano y a impedir estados inconsistentes. Esto hace que el diseño de las funciones sea más seguro, especialmente en aplicaciones grandes donde muchos módulos cooperan.

Además, la propagación natural mejora la limpieza y la legibilidad del código, ya que los niveles intermedios de una cadena de llamadas no necesitan gestionar errores que no les incumben. En C, cada función debe reenviar el error manualmente si no puede tratarlo, lo que obliga a propagar códigos de retorno y destruir la claridad del flujo principal. En cambio, en Java, las funciones intermedias simplemente se abandonan automáticamente cuando la excepción sube por la pila, y el manejo del error puede concentrarse en un único lugar apropiado del programa.

Por último, la propagación facilita que los métodos expresen sus precondiciones de forma más natural: si el argumento no cumple lo esperado, se lanza una excepción y el código normal se mantiene limpio y centrado en su propósito. Esto ofrece un estilo más declarativo y robusto que el enfoque tradicional de C, donde cada función debe validar manualmente cada parámetro y retornar códigos especiales que viajan por todo el programa.


## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

### Respuesta

En la mayoría de lenguajes orientados a objetos, incluidas Java y C++, las excepciones suelen representarse como objetos. Esto permite que cada excepción contenga información estructurada sobre el problema ocurrido: un mensaje descriptivo, el tipo concreto de error, la causa original e incluso la traza de la pila. Al ser objetos, pueden transportarse detalles relevantes sin necesidad de recurrir a códigos numéricos, valores centinela o estructuras globales como ocurre en C. Esto encaja de forma natural con el modelo orientado a objetos, donde los datos y el comportamiento se encapsulan juntos.

La encapsulación aporta una ventaja clara: una excepción puede ocultar su implementación interna y exponer únicamente la interfaz necesaria para que el código que la captura pueda entenderla y reaccionar. Por ejemplo, una excepción puede almacenar campos internos que describan en profundidad la causa del fallo, pero ofrecer métodos como getMessage() para acceder a lo esencial sin revelar detalles del funcionamiento interno de la clase. Además, al existir jerarquías de clases, cada tipo de excepción puede especializarse sin afectar al resto del sistema, manteniendo la modularidad.

El hecho de que las excepciones sean objetos facilita también la creación de excepciones personalizadas. Un programador puede definir su propia clase de excepción heredando de Exception o de RuntimeException, según el tipo de error que se quiera representar. Esto permite expresar problemas del dominio de la aplicación de forma más precisa que con las excepciones genéricas que proporciona la biblioteca estándar. Por ejemplo, en el caso de la raíz cuadrada, podría crearse una excepción ArgumentoNegativoException que represente específicamente ese fallo en lugar de usar IllegalArgumentException.
Un ejemplo sencillo de excepción personalizada sería el siguiente:

```java
public class ArgumentoNegativoException extends IllegalArgumentException {
    public ArgumentoNegativoException(double x) {
        super("El argumento no puede ser negativo: " + x);
    }
}
```

## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

### Respuesta

Cuando una excepción llega a un manejador, el hecho de que sea un objeto permite que transporte información esencial encapsulada, mucho más rica que la que podría pasarse mediante simples códigos numéricos como en C. Una pieza clave de información es el mensaje descriptivo, que explica la causa del fallo de forma humana y concreta. Este mensaje se conserva durante toda la propagación y puede consultarse con métodos como getMessage(), lo que permite comunicar al usuario o al programador el motivo exacto del error sin necesidad de prever mil códigos diferentes.

Además del mensaje, una excepción siempre lleva consigo la traza de la pila (stack trace), que describe la secuencia de llamadas que condujo al punto donde ocurrió el error. Esto es enormemente valioso: en C, rastrear un error hasta su lugar de origen suele requerir depuración manual, uso de herramientas externas o inserción de mensajes temporales; en Java, la excepción incluye automáticamente toda la información del recorrido previo. Con esa traza es posible saber qué métodos se llamaron, en qué orden y en qué línea exacta se produjo la situación anómala, lo que facilita mucho el diagnóstico.

Otra ventaja de que las excepciones sean objetos es que también incluyen el tipo concreto de error, expresado en la propia clase de excepción. Esto permite distinguir con precisión entre errores distintos sin depender de valores centinela ambiguos. En un manejador se puede atrapar un tipo específico de excepción y decidir acciones diferentes según el caso, aprovechando la jerarquía de clases. Por ejemplo, no es lo mismo atrapar una IllegalArgumentException que una IOException, y el propio tipo permite filtrar adecuadamente.

En conjunto, estos tres elementos —mensaje, tipo y traza de la pila— forman la información esencial que cualquier excepción en Java aporta de forma encapsulada. Frente al enfoque de C, donde la información sobre el error suele dispersarse o ser mínima, disponer de estos datos directamente en el objeto excepción hace que el manejo de errores sea más claro, seguro y fácil de depurar.


## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

### Respuesta

Sí, en Java es posible incluir más de un bloque catch después de un mismo try. Esto se hace para poder capturar distintos tipos de excepciones según su naturaleza. Cada bloque catch especifica un tipo concreto (o compatible por herencia), y se intenta emparejar la excepción lanzada con el primer catch cuyo tipo sea adecuado. Esto permite manejar situaciones distintas con comportamientos diferentes sin mezclar la lógica en un único bloque.

Cuando se lanza una excepción, solo se ejecuta un único bloque catch, concretamente el primero cuyo tipo coincida con la excepción que ha ocurrido. Después de ejecutarlo, no se evalúan los demás catch. El mecanismo funciona de forma similar a un encadenamiento ordenado: el sistema busca de arriba abajo el manejador más específico; una vez encontrado, se interrumpe la búsqueda y se transfiere el control al código dentro de ese catch. Esto obliga a colocar los manejadores más concretos antes que los más generales para evitar que estos últimos absorban también las excepciones específicas.
Un ejemplo de varios catch sería el siguiente:

```java
try {
    double r = Calculadora.raiz(-4);
    System.out.println(r);
} catch (IllegalArgumentException e) {
    System.out.println("Argumento inválido: " + e.getMessage());
} catch (Exception e) {
    System.out.println("Ha ocurrido un error genérico.");
}
```
En este caso, si raiz lanza IllegalArgumentException, solo se ejecuta el primer catch. El segundo no se evalúa porque el error ya está atendido. Esto permite un diseño ordenado y claro, donde cada tipo de excepción tiene un manejador distinto y se evita duplicar lógica innecesariamente en una única rama de control.


## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

### Respuesta

En Java se garantiza la ejecución de código de limpieza usando el bloque finally, que se ejecuta siempre tras el try (haya excepción o no), y también después de un catch si lo hay, incluso cuando la excepción sigue propagándose. Esto resulta clave para liberar recursos (cerrar ficheros, conexiones, liberar buffers) de forma segura, evitando fugas aunque ocurra un fallo en mitad del flujo normal. A diferencia de C, donde se depende de rutas de retorno y goto/cleanup manual, finally asegura un punto único y fiable de cierre.

Puede usarse try { … } catch { … } finally { … } cuando interesa capturar y tratar la excepción y además limpiar recursos; o try { … } finally { … } cuando no se desea capturar (la excepción se propaga) pero aun así se necesita limpiar antes de salir. En ambos casos, el código del finally se ejecuta incluso si dentro del try hay return, o si en el catch se relanza la excepción.

Ejemplo con catch + finally (se captura y se limpia):

```java
import java.io.*;

public class DemoCatchFinally {
    public static void main(String[] args) {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader("datos.txt"));
            String linea = br.readLine();
            System.out.println("Leído: " + linea);
        } catch (IOException e) {
            System.out.println("No se pudo leer el archivo: " + e.getMessage());
            // Aquí se trata el error (log, mensaje al usuario, etc.)
        } finally {
            // Se ejecuta siempre: éxito, error o return
            if (br != null) {
                try { br.close(); } catch (IOException ignored) {}
            }
            System.out.println("Recursos liberados en finally.");
        }
        System.out.println("El programa continúa.");
    }
}
```
Ejemplo sin catch (solo try + finally, la excepción se propaga pero se limpia):

```java
import java.io.*;

public class DemoTryFinally {
    static void procesar() throws IOException {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader("datos.txt"));
            String linea = br.readLine();
            // Si ocurre IOException aquí, no se captura en este método…
            System.out.println("Leído: " + linea);
        } finally {
            // …pero antes de propagarse, se cierran los recursos
            if (br != null) {
                try { br.close(); } catch (IOException ignored) {}
            }
            System.out.println("Cierre realizado en finally (antes de propagar).");
        }
        // Si hubo excepción, nunca se alcanza este punto
    }

    public static void main(String[] args) {
        try {
            procesar();
        } catch (IOException e) {
            System.out.println("Excepción capturada en main: " + e.getMessage());
        }
        System.out.println("El programa continúa tras manejar la propagación.");
    }
}
```
Nota práctica: en Java moderno suele preferirse try-with-resources para I/O, que cierra automáticamente los recursos y reduce errores; no obstante, finally sigue siendo la herramienta general para garantizar limpieza en cualquier situación y para recursos que no implementan AutoCloseable.


## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

### Respuesta

Sí, en Java el bloque finally puede aparecer sin ningún bloque catch, siempre que vaya acompañado al menos de un bloque try. Su función es garantizar que cierto código se ejecute pase lo que pase: tanto si el try finaliza con normalidad, como si ocurre una excepción, como si esa excepción se propaga hacia fuera sin capturarse en ese mismo nivel. Esto lo convierte en una herramienta fiable para liberar recursos o realizar operaciones que deben ejecutarse siempre, independientemente del flujo de control.

El bloque finally se ejecuta tanto si ocurre como si no ocurre una excepción. Si dentro del try no se lanza ninguna excepción, el flujo llega al final del bloque y pasa directamente al finally. Si se lanza una excepción y no existe un catch para capturarla en ese nivel, la excepción se propagará hacia arriba, pero antes de salir del método se ejecutará el finally. Es decir, el finally actúa como una garantía de limpieza incluso cuando la función termina de forma abrupta.

Incluso en el caso de que el try contenga un return, el código del finally también se ejecuta antes de que la función devuelva el valor. Esto es una característica importante: aunque el programa intente salir del método, Java fuerza la ejecución del bloque finally antes de completar el retorno. Únicamente si dentro del finally ocurre algo extraordinario (como lanzar otra excepción o ejecutar otro return) podría cambiarse el flujo esperado.

Un ejemplo mínimo con finally sin catch sería:

```java
public static int prueba() {
    try {
        return 10;  // El método intenta devolver aquí
    } finally {
        System.out.println("Esto se ejecuta siempre, incluso con return.");
    }
}
```
Y un ejemplo con excepción propagándose:

```java
public static void demo() {
    try {
        throw new RuntimeException("Fallo");
    } finally {
        System.out.println("Limpieza antes de propagar la excepción.");
    }
}
```
En ambos casos, el bloque finally se ejecuta siempre, cumpliendo su propósito de asegurar la liberación de recursos o la ejecución de acciones críticas sin depender del flujo normal del programa.


## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

### Respuesta

En Java, las excepciones controladas son aquellas que el compilador obliga a manejar explícitamente, bien capturándolas con un bloque catch o bien declarándolas con throws en el encabezado del método. Normalmente se usan para representar situaciones que forman parte del funcionamiento normal del programa y que pueden fallar por causas externas, como accesos a ficheros o comunicaciones. Al forzar este tratamiento, el compilador ayuda a evitar que dichas situaciones se ignoren accidentalmente. Este tipo de excepciones hereda de Exception, pero no de RuntimeException.

Por el contrario, las excepciones no controladas representan errores que se producen por un mal uso del código, por violar una precondición o por un fallo lógico. El compilador no obliga a capturarlas ni a declararlas, ya que se consideran fallos que debería corregir el programador durante el desarrollo. Estas excepciones derivan de RuntimeException. La clase RuntimeException, por tanto, define la base de todos los errores que indican un problema interno del código y no una condición externa esperable.

Algunos ejemplos típicos de excepciones controladas útiles en programas reales son IOException, que aparece al leer o escribir un fichero; SQLException, cuando ocurre un fallo en una consulta a base de datos; y FileNotFoundException, que indica que un archivo no existe. Del lado de las no controladas, son comunes NullPointerException, que surge al intentar usar una referencia nula; IllegalArgumentException, cuando un método recibe parámetros inválidos; o ArithmeticException, que aparece en operaciones como división entre cero. Estas últimas suelen reflejar errores lógicos del propio programa.

Respecto a cuándo elegir un tipo u otro, las excepciones controladas se prefieren cuando se trata con recursos externos cuyo fallo es normal y esperable, cuando se quiere obligar al llamador a reaccionar ante el error, cuando se trabaja con operaciones susceptibles de fallar por causas ajenas al programa, o cuando se requiere un tratamiento diferenciado en cada caso. Por otro lado, las excepciones no controladas son más apropiadas cuando el error proviene de un mal uso de la función, cuando capturar la excepción no aportaría nada útil, cuando una precondición simple no se cumple o cuando el error debe señalarse para que el desarrollador lo corrija, sin obligar a llenar el código de bloques try/catch innecesarios.


## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

### Respuesta

En Java, la palabra clave throws se utiliza para indicar que un método puede generar una excepción controlada y que no la gestiona internamente, sino que delega su tratamiento en el código que invoque ese método. Cuando se escribe throws en la firma de un método, se está declarando formalmente que dicho método no garantiza poder resolver ciertos fallos y que el llamador debe estar preparado para capturarlos o volver a declararlos. Esta declaración forma parte del contrato del método y permite saber, desde su propia cabecera, qué errores deben tratarse de forma obligatoria.

El uso de throws es una alternativa a capturar la excepción localmente porque las excepciones controladas requieren que el programador decida entre dos acciones: o bien se capturan con un bloque try/catch, o bien se propagan con throws. Declarar la excepción con throws permite que el método mantenga su código principal más limpio, sin mezclar la lógica con el manejo de errores que quizá no le corresponda. Esto es útil cuando el método no tiene suficiente información para decidir cómo reaccionar ante el fallo o cuando tiene sentido que la responsabilidad del tratamiento recaiga en niveles superiores del programa.

Esta estrategia suele usarse cuando un método actúa como intermediario en una cadena de llamadas y no desea asumir el control del error, especialmente en operaciones de entrada y salida donde los fallos son frecuentes y deben gestionarse desde un punto más central de la aplicación. De este modo, el código intermedio evita duplicar manejadores y deja la decisión final al llamador que tenga el contexto adecuado para tratar el problema. Así se mantiene la claridad del diseño y se evita capturar una excepción simplemente para volver a lanzarla.


## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

### Respuesta

Cuando un método abre un fichero pero no desea encargarse de gestionar la excepción en caso de que el archivo no exista, lo habitual es declararlo con throws IOException. Esta declaración permite que la excepción se propague hacia arriba en la pila de llamadas, dejando que otro método con mayor contexto (por ejemplo main) decida qué hacer. Esto evita mezclar la lógica del método con un manejo de errores que no le corresponde y mantiene la responsabilidad en el nivel adecuado del programa.

Sin embargo, aunque no se capture la excepción, puede existir la necesidad de realizar operaciones de limpieza, como cerrar el recurso si ya se había abierto. En estos casos, el bloque finally resulta útil, ya que se ejecuta siempre, tanto si no ha ocurrido ninguna excepción como si la excepción se ha lanzado y continúa propagándose. De esta forma, se garantiza que el recurso no queda abierto incluso cuando el método decide no capturar el error.

En el ejemplo siguiente se muestra un método cuyo propósito es abrir un archivo y hacer una lectura inicial, pero sin encargarse de manejar el posible error de apertura. La excepción se declara con throws, de modo que cualquier fallo se propagará al exterior, aunque el cierre del recurso queda asegurado mediante el bloque finally:

```java
import java.io.*;

public static void cargarDatos(String nombre) throws IOException {
    BufferedReader br = null;
    try {
        br = new BufferedReader(new FileReader(nombre));
        String linea = br.readLine();
        System.out.println("Primera línea: " + linea);
    } finally {
        if (br != null) {
            try { br.close(); } catch (IOException ignored) {}
        }
        System.out.println("Cierre garantizado en finally.");
    }
    // Si ocurrió IOException, no se llegará a esta línea
}
```

## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

### Respuesta

Sí, en Java es posible escribir en la firma de un método que este lanza (throws) una excepción no controlada como RuntimeException, aunque no es habitual ni aporta ninguna ventaja práctica. Las excepciones no controladas no necesitan aparecer en throws porque el compilador no exige que se declaren ni que se capturen. Añadirlas explícitamente solo sirve como documentación, ya que no cambia en absoluto el comportamiento del programa. La intención de las excepciones no controladas es que representen errores de programación o violaciones de precondiciones, y por eso no requieren mecanismos obligatorios de tratamiento.

Dado que las excepciones no controladas no están obligadas a capturarse, un método que invoque a otro con throws RuntimeException no necesita poner un bloque try-catch. El código puede compilar y ejecutarse sin incluir ningún manejador, porque este tipo de errores se consideran fallos lógicos que deberían corregirse en tiempo de desarrollo. En este sentido, un throws RuntimeException no obliga al llamador a nada, por lo que no fuerza ningún cambio en su estructura. El llamador puede usar un try-catch si desea tratar esa situación concreta, pero no tiene la obligación de hacerlo.

El sentido de añadir este tipo de excepciones al throws es puramente comunicativo. Puede ser útil para dejar explícito en la firma que un método puede lanzar cierto tipo de error relacionado con sus precondiciones o con un mal uso de la API, aunque no se pretenda que el llamador lo capture. Esta inclusión suele verse en bibliotecas muy grandes o en APIs públicas donde interesa documentar mejor los casos excepcionales. Sin embargo, en la práctica habitual, las excepciones no controladas simplemente se lanzan sin declararse en throws, siguiendo la idea de que el tratamiento de este tipo de errores queda en manos del desarrollador y no del flujo normal del programa.


## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

### Respuesta

Las excepciones controladas se recomiendan cuando la situación de error forma parte del funcionamiento normal del programa y es razonable esperar que ocurra, especialmente cuando depende de factores externos que el propio programa no controla. Un ejemplo típico es IOException, que surge al trabajar con ficheros, red o dispositivos externos. En estos casos, el método llamador suele tener opciones reales de recuperación, como reintentar, pedir otro archivo al usuario o mostrar un mensaje adecuado. Usar excepciones controladas obliga al programador a tratar esos casos de manera explícita, lo que ayuda a evitar que el error se ignore accidentalmente.

Por el contrario, las excepciones no controladas se suelen emplear cuando la causa del error es un fallo de programación o la violación de una precondición. IllegalArgumentException es el ejemplo más típico: indica que el método ha recibido un valor inválido que incumple las reglas establecidas por la propia función. En estas situaciones, capturar la excepción no tiene mucho sentido, ya que el problema debe corregirse en el código que llama, no gestionarse en tiempo de ejecución. Las no controladas no obligan a añadir bloques try-catch innecesarios, manteniendo el código más limpio y centrado en la lógica principal.

No todos los lenguajes distinguen entre excepciones controladas y no controladas. De hecho, la mayoría de lenguajes modernos de propósito general, como Python, JavaScript, C#, Swift o C++, solo implementan un único sistema de excepciones, equivalente al comportamiento de las no controladas de Java. La filosofía común en estos lenguajes es que el programador capture únicamente los errores que está dispuesto a tratar, sin que el compilador fuerce esa decisión. En estos entornos, resulta habitual lanzar excepciones cuando algo va mal, pero sin necesidad de declararlas ni capturarlas automáticamente.

Cuando solo existe una de las dos opciones, la más habitual es un modelo tipo “no controlado”, donde las excepciones se propagan libremente y el programador decide si quiere capturarlas o no. Este enfoque se considera más flexible y menos intrusivo, y evita el problema de llenar la cabecera de los métodos con largas listas de excepciones que deben declararse. Por esa razón, el sistema de excepciones controladas de Java es una característica relativamente rara en comparación con el diseño que adoptan la mayoría de otros lenguajes actuales.


## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

### Respuesta

Lanzar una excepción dentro de un bloque catch puede tener sentido cuando el código que captura la excepción actual no es el lugar adecuado para gestionarla completamente. En ocasiones, el catch puede encargarse de registrar el error, limpiar ciertos recursos o transformar la excepción en otra más adecuada al contexto, y después lanzar una nueva. Esto permite que otros niveles superiores de la aplicación se ocupen de tomar decisiones más importantes, sin perder información sobre el fallo original. De esta forma, el catch actúa como un punto intermedio, pero no como el manejador final.

También es posible relanzar la misma excepción que se acaba de capturar. Esto se usa cuando el bloque catch tiene que realizar alguna acción obligatoria, como registrar un mensaje, cerrar un recurso o actualizar algún estado interno, pero no quiere detener la propagación del error. El relanzamiento permite ejecutar esas tareas sin interferir en el flujo natural de propagación. Es importante tener en cuenta que, si se relanza la excepción tal cual, se conserva su traza original, lo cual es útil para depurar. Si se crea una nueva excepción, suele ponerse como causa la anterior mediante throw new X(e) para no perder información.

Un ejemplo de lanzar una nueva excepción dentro del catch sería transformar una excepción de E/S en otra más específica para el dominio de la aplicación:

```java
try {
    cargarArchivo("datos.txt");
} catch (IOException e) {
    System.out.println("No se pudo cargar el archivo: " + e.getMessage());
    throw new IllegalStateException("Error crítico inicializando el sistema", e);
}
```

Aquí el método decide que el error de archivo debe convertirse en un fallo más grave, por lo que lanza una excepción diferente que refleje el nuevo significado.

Por otro lado, un ejemplo de relanzar la misma excepción capturada sería:

```java
try {
    procesar();
} catch (IllegalArgumentException e) {
    System.out.println("Argumento inválido detectado: " + e.getMessage());
    throw e;  // se relanza la misma excepción
}
```

Este caso tiene sentido cuando la función debe registrar información o dejar el sistema en un estado estable antes de permitir que la excepción siga su curso natural. Con esto se garantiza que el error se documenta correctamente, pero sin impedir que el llamador superior tome la decisión final. Si quieres, puedo ayudarte con la siguiente pregunta o revisar el cuestionario completo.


## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

### Respuesta

En Java, que una excepción sea la causa de otra significa que el error original (de bajo nivel) se encapsula dentro de una excepción más adecuada al contexto (de alto nivel), preservando la información del fallo inicial. Esta técnica permite traducir excepciones técnicas (I/O, SQL, etc.) a excepciones del dominio de la aplicación sin perder la traza ni el mensaje del problema original. Para ello se usa el encadenamiento de excepciones (exception chaining), pasando la excepción capturada como “cause” en el constructor de la nueva excepción o mediante initCause.

A nivel práctico, se captura la excepción de bajo nivel en un catch, se realiza la limpieza necesaria y se lanza una excepción personalizada más expresiva para el llamador, adjuntando la causa. De este modo, el código de capas superiores no necesita conocer los detalles de la tecnología subyacente (p. ej., archivos o base de datos), pero si hace falta depurar, la “causa” conserva la traza completa. Cuando una excepción se imprime (por ejemplo con printStackTrace() o porque no se capturó), en la salida se ve la cadena con la anotación Caused by:, mostrando cada nivel del encadenamiento.

Ejemplo en Java con una excepción personalizada que envuelve una IOException:

```java
import java.io.*;

// Excepción de alto nivel (dominio de la app)
class CargaDatosException extends Exception {
    public CargaDatosException(String mensaje, Throwable causa) {
        super(mensaje, causa);  // encadena la causa
    }
}

public class ServicioDatos {

    // Método de alto nivel: no quiere exponer IOException
    public static void cargar(String ruta) throws CargaDatosException {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader(ruta));
            // ... lectura y parseo ...
            String linea = br.readLine();
            if (linea == null) {
                throw new IOException("Archivo vacío");
            }
        } catch (IOException e) {
            // Se transforma (encapsula) la excepción técnica en una de dominio
            throw new CargaDatosException("Error cargando datos desde: " + ruta, e);
        } finally {
            if (br != null) {
                try { br.close(); } catch (IOException ignored) {}
            }
        }
    }

    public static void main(String[] args) {
        try {
            cargar("inexistente.txt");
        } catch (CargaDatosException e) {
            // Al imprimir, se verá la cadena de causas
            e.printStackTrace();
        }
    }
}
```

Al ejecutarse este ejemplo, la salida de la traza mostrará la CargaDatosException y, a continuación, una línea Caused by: java.io.FileNotFoundException ..., seguida de su propia traza. Si existieran más niveles de encapsulación, se verían varias secciones Caused by, lo que facilita reconstruir el recorrido del error desde el punto más alto hasta la causa original. Esta práctica mejora la encapsulación (no se filtran detalles de bajo nivel) sin sacrificar la capacidad de diagnóstico.

