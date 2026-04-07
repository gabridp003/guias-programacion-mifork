<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

### Respuesta

En C, un ejemplo claro de composición se da cuando una estructura se define utilizando otras estructuras más simples. Este tipo de relación suele expresarse como “A tiene un B” o “A tiene varios B”. En este caso, una línea puede entenderse como una entidad que tiene dos puntos, y cada punto, a su vez, se define mediante dos coordenadas (x, y). La composición permite modelar este tipo de relaciones de forma natural, reutilizando estructuras ya definidas y manteniendo el código organizado.

Para representar este ejemplo, se define primero una estructura Punto que contiene las coordenadas x e y. A partir de ella, se construye una estructura Linea que incluye dos variables de tipo Punto, representando el origen y el final de la línea. De este modo, la estructura más compleja (Linea) se compone directamente de otras más simples (Punto), sin usar orientación a objetos ni mecanismos avanzados del lenguaje.

Además de las estructuras, se pueden definir funciones que trabajen con ellas. Una función puede calcular la distancia entre dos puntos aplicando la fórmula matemática de la distancia euclídea, usando las coordenadas almacenadas en la estructura. A partir de esta función, se puede definir otra que calcule la longitud de una línea, reutilizando el cálculo de distancia entre los dos puntos que la componen. Esto favorece la reutilización del código y la claridad del programa.

Este enfoque muestra cómo en C es posible modelar relaciones entre entidades mediante estructuras compuestas, manteniendo el código modular y cercano al problema real que se quiere representar. Aunque no se utilicen clases ni objetos, la idea de “tiene-un” se puede aplicar de forma efectiva mediante struct y funciones asociadas.

```java
struct Punto {
    float x;
    float y;
};

struct Linea {
    struct Punto p1;
    struct Punto p2;
};

float distancia(struct Punto a, struct Punto b) {
    float dx = b.x - a.x;
    float dy = b.y - a.y;
    return sqrt(dx * dx + dy * dy);
}

float longitud_linea(struct Linea l) {
    return distancia(l.p1, l.p2);
}
```


## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 

### Respuesta

Al crear un objeto de una clase concreta que hereda de otra, siempre se ejecutan varios constructores. En concreto, se ejecuta un constructor por cada clase de la jerarquía de herencia, comenzando por el constructor de la clase base y continuando hacia abajo hasta la clase más derivada (la del objeto que se está creando). Este orden garantiza que los atributos heredados se inicialicen correctamente antes de que se inicialicen los propios de la clase hija.

Dentro de un constructor, la palabra clave super se utiliza para llamar explícitamente a un constructor de la clase base. Su función principal es indicar cómo debe inicializarse la parte heredada del objeto. Por defecto, si no se escribe nada, Java intenta llamar automáticamente al constructor sin parámetros de la clase base (super()). Esta llamada implícita ocurre siempre al comienzo del constructor, incluso aunque no se vea en el código.

Si la clase base no tiene un constructor visible sin parámetros, entonces Java no puede realizar esa llamada implícita. En ese caso, es obligatoroio llamar explícitamente a super(...) indicando alguno de los constructores disponibles en la clase base y pasando los parámetros necesarios. Si no se hace, el código no compilará, ya que Java no sabrá cómo inicializar la parte heredada del objeto.

En resumen, super representa una llamada directa al constructor de la clase padre y asegura una inicialización correcta de la jerarquía de clases. Su uso explícito es obligatorio cuando el constructor por defecto de la clase base no existe o no es accesible, y siempre debe aparecer como la primera instrucción dentro del constructor de la clase derivada.

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### Respuesta

Cuando se crea un objeto de una subclase, en memoria se reserva espacio también para todos los atributos definidos en la superclase, incluidos los atributos declarados como private. Por tanto, sí forman parte de la instancia completa del objeto, ya que el objeto de la subclase es, conceptualmente y en memoria, un objeto “extendido” que incluye tanto los datos heredados como los propios. Esto es necesario para que la subclase pueda comportarse correctamente como una especialización de la superclase.

Sin embargo, que los atributos privados de la superclase existan en memoria no implica que puedan usarse directamente desde el código de la subclase. El modificador private impide el acceso directo fuera de la propia clase donde se declara, incluso aunque se trate de una subclase. Esta restricción forma parte del principio de encapsulación, cuyo objetivo es proteger el estado interno del objeto y evitar accesos o modificaciones no controladas.

Por ejemplo, si existe una clase Soldado con un atributo privado como vida, y una subclase SoldadoRaso, el atributo vida estará almacenado dentro del objeto SoldadoRaso en memoria. No obstante, desde el código de SoldadoRaso no se podrá acceder directamente a vida. En su lugar, se deberá interactuar con ese atributo mediante métodos públicos o protegidos definidos en la clase Soldado, como getters o setters, que actúan como una interfaz controlada.

En resumen, los atributos privados de una superclase sí pertenecen a la instancia de la subclase, pero no son accesibles directamente desde ella. Esta separación entre existencia en memoria y accesibilidad en el código es fundamental en la programación orientada a objetos y permite mantener un diseño más seguro, modular y fácil de mantener.

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### Respuesta

Que las clases sean compatibles a nivel de tipos implica que un objeto de una subclase puede ser tratado como si fuese una instancia de su superclase. Esto tiene un impacto directo en la extensibilidad del código, ya que permite añadir nuevos tipos derivados sin necesidad de modificar el código existente que trabaja con la clase base. El comportamiento adicional o especializado se introduce creando nuevas subclases, manteniendo intacta la lógica general del programa.

Desde el punto de vista del diseño, esta compatibilidad permite aplicar el principio de abierto/cerrado: el código está abierto a extensiones, pero cerrado a modificaciones. Si un sistema trabaja con referencias del tipo Soldado, cualquier nueva clase que herede de Soldado podrá integrarse automáticamente en ese sistema, siempre que respete la interfaz común (por ejemplo, un método saludar). El código que usa soldados no necesita saber qué tipo concreto de soldado está manejando.

Por ejemplo, supóngase que ya existen varias subclases de Soldado y un fragmento de código encargado de pedir el saludo a todos ellos. Al añadir una nueva subclase, como SoldadoMedico, no es necesario modificar ese fragmento: basta con crear la nueva clase y añadir instancias de ella a la colección de soldados. El método que recorre la colección y llama a saludar() seguirá funcionando sin cambios, demostrando la extensibilidad del diseño.

En consecuencia, la compatibilidad de tipos favorece sistemas más flexibles y mantenibles. Permite añadir nuevas funcionalidades mediante herencia y polimorfismo, reduciendo la dependencia entre el código que usa los objetos y las implementaciones concretas de dichos objetos.

```java
// Clase base
public class Soldado {
    public void saludar() {
        System.out.println("El soldado saluda.");
    }
}

// Subclase existente
public class SoldadoRaso extends Soldado {
    @Override
    public void saludar() {
        System.out.println("El soldado raso saluda.");
    }
}

// Nueva subclase añadida sin tocar el código existente
public class SoldadoMedico extends Soldado {
    @Override
    public void saludar() {
        System.out.println("El soldado médico saluda.");
    }
}

// Código que no se modifica
public class Cuartel {
    public static void pedirSaludos(Soldado[] soldados) {
        for (Soldado s : soldados) {
            s.saludar();
        }
    }
}
```

## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### Respuesta

En Java es posible que una referencia del supertipo apunte a un objeto real de un subtipo, siempre que exista una relación de herencia entre ambas clases. Esto significa que una variable declarada como Soldado puede referenciar en tiempo de ejecución a un objeto cuya clase real sea, por ejemplo, Artillero. Esta característica es fundamental para el uso del polimorfismo, ya que permite trabajar con familias de clases usando un tipo común, sin depender de las implementaciones concretas.

No obstante, al usar una referencia del supertipo, solo se pueden invocar directamente los métodos públicos definidos en el supertipo, aunque el objeto real sea de un subtipo. Si el método está sobrescrito en el subtipo, se ejecutará la versión correspondiente al objeto real (ligado dinámico). Sin embargo, no es posible invocar métodos exclusivos del subtipo usando únicamente una referencia del supertipo, ya que el compilador no tiene certeza estática de que ese método exista para todos los objetos compatibles con la referencia.

El upcasting consiste en tratar un objeto de un subtipo como si fuese del supertipo. Es una conversión segura, implícita y no requiere ninguna instrucción especial, ya que siempre es válida desde el punto de vista del sistema de tipos. Por el contrario, el downcasting implica convertir una referencia del supertipo a una referencia de un subtipo concreto. Esta conversión requiere una comprobación explícita y puede producir errores en tiempo de ejecución si el objeto real no es del tipo esperado.

Para evitar errores al realizar downcasting, Java proporciona el operador instanceof, que permite comprobar en tiempo de ejecución si un objeto es instancia de una clase concreta. De este modo, es posible recorrer una colección de referencias del tipo Soldado y, solo cuando el objeto real sea un Artillero, realizar la conversión y acceder a sus métodos específicos de forma segura.

```java
public class Soldado {
    public void saludar() {
        System.out.println("El soldado saluda.");
    }
}

public class Artillero extends Soldado {
    private int cohetes;

    public Artillero(int cohetes) {
        this.cohetes = cohetes;
    }

    public int getCohetes() {
        return cohetes;
    }
}

public class Ejemplo {
    public static void main(String[] args) {
        Soldado[] soldados = {
            new Soldado(),
            new Artillero(5),
            new Soldado()
        };

        for (Soldado s : soldados) {
            s.saludar();
            if (s instanceof Artillero) {
                Artillero a = (Artillero) s; // downcasting seguro
                System.out.println("Número de cohetes: " + a.getCohetes());
            }
        }
    }
}
```


## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### Respuesta

El acceso protegido es un nivel intermedio de visibilidad que permite equilibrar la ocultación de información con la reutilización mediante herencia. Un método o atributo declarado como protected no es público, por lo que no puede ser accedido libremente desde cualquier clase, pero sí puede ser utilizado por las subclases, incluso aunque estén definidas en otro paquete. De este modo, se protege el estado interno frente a usos externos indebidos, manteniendo a la vez la posibilidad de reutilización en clases derivadas.

En Java, el acceso protegido se implementa mediante la palabra clave protected, que puede aplicarse tanto a atributos como a métodos. Desde el punto de vista de la encapsulación, protected indica que el miembro forma parte del comportamiento interno de la jerarquía de clases, y no de la interfaz pública de la clase base. Esto resulta especialmente útil cuando las subclases necesitan acceder o modificar ciertos datos heredados para implementar comportamientos específicos.

Por ejemplo, si la clase Soldado define un atributo nombre como protegido, dicho atributo será accesible directamente desde cualquier subclase, como Zapador, pero no desde código externo. De esta forma, el Zapador puede utilizar el nombre del soldado para implementar su lógica particular (por ejemplo, al colocar bombas), sin romper la encapsulación ni exponer el atributo como público.

En resumen, el acceso protected permite diseñar jerarquías de herencia más flexibles y expresivas. Facilita la reutilización de información entre clases relacionadas, mientras se mantiene el control sobre qué partes del estado pueden ser visibles y desde dónde pueden utilizarse.

```java
public class Soldado {
    protected String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }
}

public class Zapador extends Soldado {

    public Zapador(String nombre) {
        super(nombre);
    }

    public void ponerBomba() {
        System.out.println("El zapador " + nombre + " ha colocado una bomba.");
    }
}
```


## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### Respuesta

En los lenguajes orientados a objetos no existe necesariamente una clase base común para todos los objetos; depende del lenguaje y de su diseño. Algunos lenguajes definen explícitamente una superclase raíz de la que heredan todas las clases, mientras que otros no imponen esta estructura y permiten jerarquías sin una raíz única. Por tanto, no es un rasgo universal de la orientación a objetos, sino una decisión del lenguaje.

En Java, sí existe una clase base común para todos los objetos: la clase Object. Todas las clases, de forma directa o indirecta, heredan de java.lang.Object, incluso aunque no se indique explícitamente con extends. Esto implica que cualquier objeto en Java dispone, al menos, de los métodos definidos en Object, como toString(), equals() o hashCode(). Esta característica simplifica el tratamiento uniforme de objetos y facilita mecanismos como el polimorfismo y el uso de colecciones genéricas.

En otros lenguajes el comportamiento es diferente. Por ejemplo, en C++ no existe una clase base universal obligatoria; una clase puede no heredar de ninguna otra. En Python y C#, en cambio, sí existe una clase raíz común (object en Python y System.Object en C#), de forma similar a Java. Estas diferencias muestran que la existencia de una clase base común no define por sí sola a un lenguaje orientado a objetos, pero en Java es un elemento clave de su modelo de herencia y de su sistema de tipos.


## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### Respuesta

La herencia múltiple es un concepto de los lenguajes orientados a objetos que permite que una clase herede directamente de más de una clase base. Esto significa que una clase puede adquirir atributos y métodos de varias superclases al mismo tiempo. La herencia múltiple busca facilitar la reutilización de código, pero introduce problemas importantes, como ambigüedades cuando dos superclases definen métodos con el mismo nombre, lo que complica la comprensión y el mantenimiento del código.

No todos los lenguajes orientados a objetos permiten herencia múltiple de clases. Por ejemplo, C++ sí permite que una clase herede de varias clases base, aunque esto requiere mecanismos adicionales para resolver conflictos y puede aumentar la complejidad del diseño. Por este motivo, muchos lenguajes modernos han optado por restringir o eliminar la herencia múltiple directa entre clases.

En Java no existe herencia múltiple de clases. Una clase solo puede extender (extends) una única clase base. Esta decisión de diseño evita problemas clásicos como el problema del diamante y contribuye a que el modelo de herencia sea más claro y seguro. De este modo, Java fuerza jerarquías más simples y fáciles de mantener.

No obstante, Java ofrece una alternativa a la herencia múltiple mediante el uso de interfaces. Una clase puede implementar (implements) varias interfaces, heredando así múltiples contratos de comportamiento sin heredar estado. Esta solución permite combinar funcionalidades de diferentes orígenes sin los inconvenientes tradicionales de la herencia múltiple de clases, manteniendo un modelo robusto y coherente.


## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

### Respuesta

En los lenguajes orientados a objetos, las excepciones son objetos, lo que implica que pueden diseñarse mediante clases y formar parte de una jerarquía de herencia. Esto permite crear excepciones personalizadas que se adapten al dominio del problema que se está modelando. En Java, una excepción personalizada se crea definiendo una clase que hereda de alguna de las clases base de excepciones proporcionadas por el lenguaje.

Para que una excepción sea no controlada (unchecked), debe heredar de RuntimeException. Este tipo de excepciones no obliga a que el método que las lanza las declare en su firma ni a que el código que las usa las capture explícitamente. Este comportamiento es adecuado para errores de programación o situaciones excepcionales que no siempre pueden gestionarse de forma razonable en tiempo de compilación.

Además, al ser objetos, las excepciones pueden componerse con otros objetos. Por ejemplo, una excepción UsuarioNoEncontradoException puede incluir un objeto Usuario como atributo interno, permitiendo identificar con precisión qué usuario provocó el error. Esto resulta muy útil para depuración, registro de errores o generación de mensajes más informativos, sin necesidad de exponer directamente la lógica interna al exterior.

Por último, es habitual permitir que una excepción personalizada incluya la causa subyacente del error. En Java esto se consigue sobrecargando el constructor para aceptar un objeto de tipo Throwable. De este modo, se puede encadenar excepciones, conservando el rastro completo del error desde su origen hasta el punto donde se gestiona.

```java
public class Usuario {
    private String nombre;

    public Usuario(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }
}

public class UsuarioNoEncontradoException extends RuntimeException {

    private Usuario usuario;

    public UsuarioNoEncontradoException(Usuario usuario) {
        super("Usuario no encontrado: " + usuario.getNombre());
        this.usuario = usuario;
    }

    public UsuarioNoEncontradoException(Usuario usuario, Throwable causa) {
        super("Usuario no encontrado: " + usuario.getNombre(), causa);
        this.usuario = usuario;
    }

    public Usuario getUsuario() {
        return usuario
    }
}
```


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta

No se recomienda emplear herencia únicamente con el objetivo de reutilizar código porque la herencia establece una relación muy fuerte de tipo “es-un” entre clases. Al heredar, la subclase queda acoplada de forma directa a la superclase, dependiendo no solo de su implementación actual, sino también de sus cambios futuros. Esto puede provocar diseños rígidos, difíciles de mantener y propensos a errores cuando la clase base evoluciona o se modifica de una forma no prevista por las subclases.

La herencia debería reservarse para los casos en los que exista una relación conceptual clara entre las clases, es decir, cuando una subclase realmente sea una especialización del concepto representado por la superclase. Si solo se busca reutilizar ciertos métodos o comportamientos, forzar esa relación semántica puede llevar a jerarquías artificiales, donde las subclases heredan métodos o atributos que no tienen sentido para ellas o que deben sobrescribirse constantemente.

En cambio, la composición permite reutilizar código de forma más flexible y segura. Al componer, una clase tiene-un objeto de otra clase y delega en él parte de su funcionalidad, sin quedar ligada a su jerarquía de herencia. Este enfoque reduce el acoplamiento, facilita los cambios y mejora la extensibilidad del sistema, ya que los componentes pueden intercambiarse o modificarse sin afectar al resto del diseño.

Por este motivo, en el diseño orientado a objetos se suele recomendar “preferir composición frente a herencia” cuando el objetivo principal es la reutilización de código. La herencia aporta potencia y polimorfismo, pero debe utilizarse con cuidado y solo cuando la relación entre clases lo justifique claramente desde el punto de vista conceptual.


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta

Se recomienda favorecer la composición frente a la herencia porque la composición introduce un menor acoplamiento entre las clases. Al usar herencia, la subclase queda fuertemente ligada a la implementación de la superclase, dependiendo de su estructura interna y de su evolución futura. Esto hace que pequeños cambios en la clase base puedan afectar de forma indirecta y no deseada a todas las subclases, reduciendo la flexibilidad y aumentando el riesgo de errores.

La composición, en cambio, permite construir clases a partir de otras mediante relaciones del tipo “tiene-un”, delegando responsabilidades en objetos internos. Este enfoque facilita el reemplazo o modificación de comportamientos sin necesidad de alterar la jerarquía de clases. Al no depender de la herencia, los componentes pueden cambiarse, ampliarse o reutilizarse en otros contextos con mayor libertad, lo que mejora la modularidad del diseño.

Además, la composición evita jerarquías complejas y profundas, que suelen ser difíciles de entender y mantener. En muchos casos, el uso excesivo de herencia conduce a diseños rígidos donde las clases acumulan responsabilidades que no siempre les corresponden conceptualmente. La composición permite combinar comportamientos de forma más clara y explícita, haciendo que las dependencias entre clases sean visibles y controlables.

Por todo ello, favorecer la composición frente a la herencia conduce a sistemas más flexibles, extensibles y mantenibles. La herencia sigue siendo una herramienta útil cuando existe una relación clara de especialización (“es-un”), pero la composición suele ser una mejor elección como mecanismo principal para estructurar el código y reutilizar funcionalidades.


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta

Se dice que la herencia rompe la encapsulación porque una subclase pasa a depender no solo de la interfaz pública de la superclase, sino también de detalles internos de su implementación. Aunque estos detalles estén marcados como protected o se utilicen de forma indirecta, la subclase queda acoplada a cómo funciona internamente la clase base. Esto debilita la idea de encapsulación, que busca ocultar la implementación y exponer únicamente un comportamiento bien definido.

En una relación de herencia, la subclase conoce demasiado sobre la superclase: sabe qué métodos llamar, en qué orden, qué datos heredados existen y cómo se usan. Como consecuencia, si la implementación interna de la superclase cambia (por ejemplo, se modifica un método protegido o se altera la forma de mantener el estado interno), la subclase puede dejar de funcionar correctamente, aunque la interfaz pública de la superclase no haya cambiado. Esto genera un acoplamiento fuerte y frágil entre ambas clases.

Además, la herencia permite a las subclases alterar comportamientos internos mediante la sobrescritura de métodos, lo que puede romper invariantes que la superclase daba por garantizados. Esto dificulta razonar sobre el comportamiento del sistema, ya que el comportamiento real de una clase base puede depender de cómo la modifiquen sus subclases, incluso sin que el código que usa la superclase sea consciente de ello.

Por este motivo se afirma que la herencia debilita la encapsulación: la frontera entre “lo que una clase hace” y “cómo lo hace” se vuelve difusa para las subclases. La composición evita este problema, ya que los objetos colaboran entre sí únicamente a través de interfaces bien definidas, manteniendo mejor el aislamiento y el control sobre los detalles internos de cada clase.


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta

Una primera forma de modelar la situación es mediante herencia, creando una superclase Persona que contenga los datos comunes (DNI y nombre), y dos subclases Estudiante y Trabajador que heredan dichos atributos. Este enfoque se basa en una relación conceptual de tipo “es-un”, ya que un estudiante o un trabajador son personas. La herencia permite reutilizar directamente los atributos y métodos comunes, evitando duplicación de código.

En este modelo, los datos compartidos se almacenan en la superclase y se inicializan a través de su constructor, que es invocado desde los constructores de las subclases usando super. De este modo, tanto Estudiante como Trabajador reciben automáticamente los datos personales definidos en Persona, y solo necesitan añadir su comportamiento o información específica.

Una alternativa es emplear composición, separando los datos comunes en una clase independiente llamada DatosPersonales. En este caso, ni Estudiante ni Trabajador heredan de una clase común, sino que contienen un objeto DatosPersonales. Esta relación es de tipo “tiene-un” y permite reutilizar los mismos datos sin establecer una jerarquía de herencia. Según el enunciado, ambos reciben una instancia de DatosPersonales en su constructor.

Este segundo enfoque reduce el acoplamiento entre clases y suele ser más flexible, ya que los datos personales pueden reutilizarse en otros contextos sin forzar una relación de herencia. Ambos modelos son válidos, pero muestran claramente la diferencia entre reutilizar mediante herencia y reutilizar mediante composición.

✅ Opción 1: Modelado mediante herencia

```java
public class Persona {
    protected String dni;
    protected String nombre;

    public Persona(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }
}

public class Estudiante extends Persona {

    public Estudiante(String dni, String nombre) {
        super(dni, nombre);
    }
}

public class Trabajador extends Persona {

    public Trabajador(String dni, String nombre) {
        super(dni, nombre);
    }
}
```

✅ Opción 2: Modelado mediante composición

```java
public class DatosPersonales {
    private String dni;
    private String nombre;

    public DatosPersonales(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() {
        return dni;
    }

    public String getNombre() {
        return nombre;
    }
}

public class Estudiante {
    private DatosPersonales datos;

    public Estudiante(DatosPersonales datos) {
        this.datos = datos;
    }
}

public class Trabajador {
    private DatosPersonales datos;

    public Trabajador(DatosPersonales datos) {
        this.datos = datos;
    }
}
```
