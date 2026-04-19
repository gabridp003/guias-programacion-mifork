<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

### Respuesta

El polimorfismo es un principio de la programación orientada a objetos que permite tratar objetos de distintas clases relacionadas por herencia como si fueran instancias de un mismo tipo base. Gracias a este mecanismo, una referencia de una clase padre puede apuntar a objetos de distintas clases hijas, y el comportamiento que se ejecuta dependerá del tipo concreto del objeto en tiempo de ejecución. Esto permite escribir código más general, flexible y reutilizable, reduciendo el acoplamiento entre las distintas partes del programa.

El polimorfismo se utiliza principalmente para extender el comportamiento de un programa sin modificar el código existente. Al programar contra clases base o interfaces, se facilita la incorporación de nuevas clases derivadas que implementen comportamientos específicos, manteniendo el mismo punto de acceso. Este enfoque es especialmente útil cuando se trabaja con jerarquías de herencia y colecciones de objetos heterogéneos que comparten una misma interfaz común.

La sobreescritura de métodos es el mecanismo que hace posible el polimorfismo en lenguajes como Java. Consiste en que una clase hija redefine un método heredado de su clase padre, manteniendo la misma firma (nombre, parámetros y tipo de retorno), pero proporcionando una implementación distinta. De este modo, cuando un método es invocado a través de una referencia al tipo padre, se ejecuta la versión correspondiente a la clase real del objeto.

Este proceso está ligado a la ligadura dinámica, ya que la decisión de qué método se ejecuta no se toma en tiempo de compilación, sino en tiempo de ejecución. La sobreescritura permite especializar el comportamiento de las clases hijas respetando el contrato definido por la clase base, lo que refuerza la extensibilidad y coherencia del diseño orientado a objetos.


## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

### Respuesta

La ligadura dinámica, también llamada enlace tardío, es el mecanismo mediante el cual la decisión de qué método concreto se ejecuta se pospone hasta el tiempo de ejecución, en lugar de resolverse en tiempo de compilación. Esto significa que, cuando se invoca un método a través de una referencia a una clase base, el método que finalmente se ejecuta depende del tipo real del objeto al que apunta dicha referencia en ese momento. Este comportamiento contrasta con la ligadura estática, donde la llamada al método se decide en compilación.

La ligadura dinámica está estrechamente relacionada con el polimorfismo, ya que es el mecanismo que permite que una misma llamada a un método produzca comportamientos distintos según el objeto concreto involucrado. Sin ligadura dinámica, la sobreescritura de métodos no tendría efecto práctico cuando se utilizan referencias a clases base. Por tanto, el polimorfismo en lenguajes orientados a objetos se apoya directamente en la existencia de enlace tardío para funcionar correctamente.

En C++, la ligadura dinámica no es el comportamiento por defecto. Para que un método sea enlazado dinámicamente, debe declararse explícitamente como virtual en la clase base. Si no se hace, el enlazado será estático, incluso aunque exista herencia. En cambio, en Java, todos los métodos no estáticos son virtuales por defecto, salvo que se declaren como final o static, por lo que la ligadura dinámica se aplica automáticamente sin necesidad de indicarlo explícitamente al programar.

En Python, el enlace tardío es inherente al propio lenguaje, ya que es dinámico y no realiza comprobaciones de tipos en tiempo de compilación. La resolución de métodos se hace siempre en tiempo de ejecución siguiendo el tipo real del objeto, sin que el programador tenga que indicar nada de forma explícita. De este modo, Python soporta polimorfismo y ligadura dinámica de forma natural, incluso sin necesidad de jerarquías de herencia estrictas, lo que refuerza su flexibilidad pero reduce el control estático presente en lenguajes como C++ o Java.


## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

### Respuesta

```java
// Clase base
class Soldado {
    public void saluda() {
        System.out.println("Soldado: a sus órdenes.");
    }
}

// Subclase Zapador que sobreescribe completamente el método
class Zapador extends Soldado {
    @Override
    public void saluda() {
        System.out.println("Zapador: listo para tareas de demolición.");
    }
}

// Subclase Artillero
class Artillero extends Soldado {
    // Hereda el comportamiento de Soldado
}

// Clase de prueba
public class PruebaPolimorfismo {
    public static void main(String[] args) {
        Soldado[] ejercito = new Soldado[2];
        ejercito[0] = new Zapador();
        ejercito[1] = new Artillero();

        for (Soldado s : ejercito) {
            s.saluda(); // Ejemplo de polimorfismo
        }
    }
}
```


## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

### Respuesta

Cuando se sobreescribe un método en una subclase, es posible invocar la versión definida en la clase base para reutilizar su comportamiento y ampliarlo o modificarlo parcialmente. De este modo, no es necesario reimplementar toda la funcionalidad desde cero, sino que se puede partir del resultado del método original y añadir lógica adicional. Este enfoque es habitual cuando se desea mantener un comportamiento común y especializar solo ciertos aspectos en las subclases.

En Java, esta invocación se realiza mediante la palabra clave super, que permite acceder explícitamente a los métodos y atributos de la clase padre. Al utilizar super.nombreMetodo(), se garantiza que se ejecuta la versión del método definida en la clase base, incluso aunque exista una versión sobreescrita en la subclase. Esto resulta especialmente útil en jerarquías de herencia donde se quiere preservar el comportamiento general definido en la superclase.

Aplicado al ejemplo, la clase Zapador puede sobreescribir el método saluda de forma que primero se ejecute el saludo estándar del Soldado y, a continuación, se añada un mensaje específico del zapador. De este modo, se mantiene el saludo común a todos los soldados y se introduce una especialización sin romper la coherencia del diseño.

Este mecanismo refuerza la reutilización de código y facilita el mantenimiento, ya que cualquier cambio en el método base se propaga automáticamente a las subclases que lo invoquen mediante super. Además, permite combinar herencia, sobreescritura y polimorfismo de una forma controlada y clara.

```java
class Soldado {
    public void saluda() {
        System.out.println("Soldado: a sus órdenes.");
    }
}

class Zapador extends Soldado {
    @Override
    public void saluda() {
        super.saluda(); // Invocación del método de la clase base
        System.out.println("ZAPADOR A SUS ÓRDENES");
    }
}
```


## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

### Respuesta

Al sobreescribir un método en Java, deben cumplirse ciertas restricciones para que el lenguaje considere válida la redefinición. En primer lugar, el método de la subclase debe tener la misma firma que el método de la clase base, es decir, el mismo nombre y la misma lista de parámetros en tipo y orden. No es posible cambiar los tipos de los parámetros, ya que en ese caso no se trataría de una sobreescritura, sino de un método distinto. Además, el método sobreescrito no puede reducir la visibilidad del método original; por ejemplo, un método public en la clase base no puede convertirse en protected o private en la subclase.

En cuanto al tipo de retorno, Java permite cierta flexibilidad mediante los llamados tipos de retorno covariantes. Esto significa que el método sobreescrito puede devolver un tipo más específico que el del método original, siempre que exista una relación de herencia entre ambos tipos. No obstante, no se permite cambiar el tipo de retorno por uno incompatible, ya que rompería el contrato definido por la clase base y el uso polimórfico del método.

La sobreescritura (overriding) no debe confundirse con la sobrecarga (overloading). La sobreescritura se da entre una clase base y una subclase, y está relacionada con el polimorfismo y la ligadura dinámica, ya que el método que se ejecuta se decide en tiempo de ejecución. En cambio, la sobrecarga consiste en definir varios métodos con el mismo nombre pero con diferentes parámetros dentro de una misma clase (o jerarquía), y su resolución se realiza en tiempo de compilación. La sobrecarga no implica polimorfismo en el sentido estricto.

La anotación @Override se utiliza para indicar explícitamente que un método pretende sobreescribir un método de la clase base. Su uso no es obligatorio, pero es altamente recomendable, ya que permite al compilador detectar errores comunes, como cambios accidentales en la firma del método o intentos de sobreescritura que en realidad no lo son. De este modo, @Override mejora la claridad del código y ayuda a evitar errores difíciles de detectar, reforzando la corrección y el mantenimiento del programa.

## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

### Respuesta

Al estudiar Java, el polimorfismo se utiliza desde etapas muy tempranas, aunque no siempre se identifique explícitamente como tal al principio. Cuando se trabaja con clases que heredan de Object, como ocurre con todas las clases en Java, y se sobreescriben métodos como toString o equals, ya se está haciendo uso del polimorfismo. Esto sucede porque estos métodos son invocados normalmente a través de referencias del tipo Object, y el método que se ejecuta depende del tipo real del objeto en tiempo de ejecución.

Por ejemplo, al sobrescribir toString, se permite que una llamada genérica a este método produzca un resultado distinto según la clase concreta del objeto. Aunque la llamada pueda parecer simple, el mecanismo que decide qué versión del método se ejecuta es la ligadura dinámica, que es uno de los pilares del polimorfismo. De este modo, sin necesidad de crear jerarquías complejas ni arrays de objetos de distintos tipos, ya se está aplicando este principio fundamental de la orientación a objetos.

Algo similar ocurre con la sobreescritura de equals, que permite definir cuándo dos objetos deben considerarse iguales según su contenido y no solo por referencia. Cuando este método se invoca desde código genérico, como colecciones o comparaciones estándar, el comportamiento específico depende de la implementación concreta de la clase, lo que constituye otro ejemplo claro de polimorfismo en uso cotidiano.

Por tanto, aunque al inicio del aprendizaje de Java el polimorfismo no se presente de forma teórica o explícita, en la práctica se está utilizando desde el primer momento en que se sobrescriben métodos heredados. Esto facilita una transición progresiva desde conceptos básicos como clases y herencia hacia ideas más avanzadas, reforzando la comprensión del diseño orientado a objetos sin necesidad de introducir complejidad innecesaria desde el principio.


## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

### Respuesta

Una clase abstracta es una clase que se utiliza como base para otras clases, pero que no está pensada para ser instanciada directamente. Su función principal es definir una estructura común y un comportamiento parcial que las subclases deben completar. En Java, una clase se declara abstracta mediante la palabra clave abstract, lo que indica que la clase representa un concepto general y no un objeto concreto del programa.

Un método abstracto es un método que no tiene implementación, es decir, solo se declara su firma y se delega su definición a las clases hijas. La existencia de métodos abstractos obliga a que cualquier clase no abstracta que herede de la clase base proporcione una implementación concreta de dichos métodos. De esta forma, se garantiza que todas las subclases cumplen un determinado contrato común.

No es posible crear instancias de una clase abstracta, ya que no representa un comportamiento completamente definido. Sin embargo, sí es posible utilizar referencias del tipo de la clase abstracta para apuntar a objetos de sus subclases, lo que permite combinar clases abstractas con polimorfismo. Este enfoque es muy habitual cuando se quiere trabajar con conjuntos de objetos relacionados que comparten una interfaz común pero implementan comportamientos distintos.

En el ejemplo propuesto, Soldado se redefine como una clase abstracta que contiene un método concreto saluda y un método abstracto atacar. La palabra clave abstract debe colocarse tanto en la declaración de la clase como en la declaración del método abstracto. Cada tipo concreto de soldado (Zapador, Artillero, etc.) está obligado a implementar el método atacar, definiendo su comportamiento específico.

```java
// Clase abstracta
abstract class Soldado {

    public void saluda() {
        System.out.println("Soldado: a sus órdenes.");
    }

    // Método abstracto
    public abstract void atacar();
}

// Subclase Zapador
class Zapador extends Soldado {
    @Override
    public void atacar() {
        System.out.println("Zapador: colocando explosivos.");
    }
}

// Subclase Artillero
class Artillero extends Soldado {
    @Override
    public void atacar() {
        System.out.println("Artillero: disparando la artillería.");
    }
}
```


## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

### Respuesta

La palabra clave final en Java se utiliza para restringir la modificación o extensión de ciertos elementos del lenguaje. Cuando se aplica a un método, indica que dicho método no puede ser sobreescrito en ninguna subclase. Cuando se aplica a una clase, significa que esa clase no puede ser heredada, es decir, no pueden definirse subclases a partir de ella. De este modo, final se emplea para fijar comportamientos y evitar cambios no deseados en jerarquías de clases.

La relación entre final y el polimorfismo es directa, ya que esta palabra clave limita o impide su uso. El polimorfismo en Java se basa en la posibilidad de sobreescribir métodos y de que, mediante ligadura dinámica, se ejecute la versión correspondiente al tipo real del objeto. Si un método se declara como final, se elimina la posibilidad de sobreescritura y, por tanto, ese método no puede participar en el polimorfismo. Del mismo modo, una clase final no puede formar parte de una jerarquía de herencia, lo que impide cualquier comportamiento polimórfico basado en ella.

El uso de final resulta útil cuando se desea garantizar la estabilidad del diseño, ya sea por razones de seguridad, corrección o rendimiento. Al impedir la herencia o la sobreescritura, se evita que una subclase modifique comportamientos críticos que podrían romper invariantes o contratos definidos por la clase original. En este sentido, final actúa como una herramienta de control dentro del diseño orientado a objetos.

En la API estándar de Java existen numerosos ejemplos de clases declaradas como final. Un caso muy conocido es la clase String, que es final para garantizar la inmutabilidad de los objetos de tipo cadena y evitar modificaciones inesperadas mediante herencia. Otros ejemplos incluyen clases como Integer, Double o Math. Estas decisiones de diseño muestran cómo el uso de final puede ser fundamental para preservar la coherencia y seguridad del comportamiento de las bibliotecas estándar.


## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta

En Java, una interfaz es un tipo de referencia que define un conjunto de métodos que una clase se compromete a implementar, pero sin proporcionar su implementación (salvo excepciones modernas como métodos default, que no son el foco inicial). Una interfaz establece un contrato: cualquier clase que la implemente debe definir el comportamiento de todos sus métodos. A diferencia de las clases, las interfaces no representan objetos, sino capacidades o comportamientos que una clase puede ofrecer.

Las interfaces se parecen a las clases abstractas, pero no son lo mismo. Ambas permiten definir métodos sin implementación y trabajar con referencias a un tipo común para aplicar polimorfismo. Sin embargo, una clase abstracta puede contener estado (atributos), métodos implementados y métodos abstractos, mientras que una interfaz se centra únicamente en qué se puede hacer, no en cómo se hace. Además, una clase abstracta representa una relación de tipo “es un” más fuerte, mientras que una interfaz expresa que una clase “cumple un comportamiento”.

Una diferencia clave es que en Java una clase solo puede heredar de una única clase, abstracta o no, pero puede implementar múltiples interfaces. Esto permite resolver la limitación de la herencia simple y facilita el diseño de sistemas más flexibles, donde una clase puede adoptar varios roles distintos sin quedar acoplada a una jerarquía rígida de herencia.

Desde el punto de vista del polimorfismo, las interfaces son especialmente potentes, ya que permiten tratar objetos de clases no relacionadas entre sí de forma uniforme, siempre que implementen la misma interfaz. Este enfoque es muy utilizado en la API estándar de Java y en el diseño de software orientado a objetos, ya que fomenta el desacoplamiento, la reutilización de código y la extensibilidad del sistema.


## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta

En este diseño se define una clase abstracta Punto que representa el concepto general de punto, independientemente de sus dimensiones. Esta clase declara el método calcularDistanciaA, que se define como abstracto, ya que la forma de calcular la distancia depende del tipo concreto de punto (2D o 3D). De este modo, se fuerza a que cada subtipo proporcione su propia implementación, manteniendo un contrato común que permite el uso polimórfico.

Las clases Punto2D y Punto3D heredan de Punto e implementan el método calcularDistanciaA. En estas implementaciones se emplea instanceof para comprobar que el punto recibido es del mismo subtipo, y se realiza un downcasting seguro para poder acceder a sus coordenadas específicas. Si se recibe un punto incompatible, se lanza una excepción, evitando cálculos incorrectos entre dimensiones distintas. Este enfoque muestra cómo el polimorfismo puede combinarse con comprobaciones dinámicas de tipo cuando el diseño lo requiere.

A partir de este diseño se define una clase Linea, que recibe dos referencias de tipo Punto. La clase Linea no conoce si los puntos son 2D o 3D, ni necesita saberlo. Para calcular su longitud, simplemente invoca el método calcularDistanciaA sobre uno de los puntos, delegando el cálculo en la implementación concreta correspondiente. Esto demuestra un uso claro del polimorfismo: el código de Linea es genérico y funciona correctamente para cualquier subtipo de Punto.

Este ejemplo ilustra cómo el uso de clases abstractas, métodos abstractos y polimorfismo permite desacoplar el código cliente de las implementaciones concretas. Al mismo tiempo, el uso controlado de instanceof y downcasting garantiza la corrección del cálculo cuando existen restricciones semánticas, como la necesidad de operar únicamente con puntos del mismo tipo.

```java
// Clase abstracta
abstract class Punto {
    public abstract double calcularDistanciaA(Punto otro);
}

// Punto en dos dimensiones
class Punto2D extends Punto {
    private double x, y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double calcularDistanciaA(Punto otro) {
        if (otro instanceof Punto2D) {
            Punto2D p = (Punto2D) otro;
            return Math.sqrt(
                Math.pow(this.x - p.x, 2) +
                Math.pow(this.y - p.y, 2)
            );
        }
        throw new IllegalArgumentException("Puntos incompatibles");
    }
}

// Punto en tres dimensiones
class Punto3D extends Punto {
    private double x, y, z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double calcularDistanciaA(Punto otro) {
        if (otro instanceof Punto3D) {
            Punto3D p = (Punto3D) otro;
            return Math.sqrt(
                Math.pow(this.x - p.x, 2) +
                Math.pow(this.y - p.y, 2) +
                Math.pow(this.z - p.z, 2)
            );
        }
        throw new IllegalArgumentException("Puntos incompatibles");
    }
}

// Clase Linea que usa polimorfismo
class Linea {
    private Punto a, b;

    public Linea(Punto a, Punto b) {
        this.a = a;
        this.b = b;
    }

    public double longitud() {
        return a.calcularDistanciaA(b);
    }
}
```


## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta

La herencia de interfaces en Java consiste en que una interfaz puede extender otra interfaz, heredando así todos sus métodos abstractos. De este modo, se pueden construir contratos más específicos a partir de otros más generales, ampliando las funcionalidades que deben implementar las clases. A diferencia de las clases, las interfaces no heredan implementación (salvo métodos default), sino únicamente la obligación de definir determinados métodos.

En Java sí existe herencia múltiple de interfaces, lo que significa que una interfaz puede extender varias interfaces a la vez, y una clase puede implementar múltiples interfaces simultáneamente. Esto no provoca los problemas clásicos de la herencia múltiple de clases, ya que no hay ambigüedad en la implementación: las interfaces solo definen qué métodos deben existir, no cómo se implementan. Este mecanismo permite combinar distintos comportamientos de forma flexible y desacoplada.

Desde el punto de vista del polimorfismo, las interfaces son especialmente útiles porque permiten trabajar con referencias a un tipo común sin importar la clase concreta que lo implementa. Una clase que implemente una interfaz puede ser tratada como instancia de dicha interfaz, y el comportamiento concreto dependerá de la implementación proporcionada por la clase. Esto favorece diseños más extensibles y modulares.

En el ejemplo, se define una interfaz Fichero que representa la capacidad de leer contenido, y otra interfaz FicheroEscribible que extiende a Fichero, añadiendo operaciones de escritura y eliminación. De este modo, cualquier clase que implemente FicheroEscribible estará obligada a implementar todos los métodos definidos en ambas interfaces.

```java
// Interfaz base
public interface Fichero {
    String leerContenido();
}

// Interfaz que extiende a Fichero
public interface FicheroEscribible extends Fichero {
    void escribirContenido(String contenido);
    void eliminar();
}
```
