<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

### Respuesta

La composición en C puede modelarse anidando estructuras: una Linea tiene dos Punto. Cada Punto almacena sus coordenadas x e y, y la Linea simplemente contiene dos campos de tipo Punto. Para calcular la distancia entre dos puntos se usa la fórmula euclídea; en C conviene emplear hypot(dx, dy) de <math.h> por estabilidad numérica. La longitud de una línea se reduce a la distancia entre sus dos extremos.

A continuación se muestra una implementación compacta con dos struct y dos funciones. Se añaden calificadores const para dejar claro que las funciones no modifican los argumentos. En sistemas tipo Unix, al compilar puede ser necesario enlazar la biblioteca matemática: gcc ejemplo.c -o ejemplo -lm.

```java
#include <math.h>   // hypot
#include <stdio.h>

typedef struct {
    double x;
    double y;
} Punto;

typedef struct {
    Punto a;  // punto inicial
    Punto b;  // punto final
} Linea;

// Distancia euclídea entre dos puntos
double distancia_puntos(const Punto* p, const Punto* q) {
    double dx = q->x - p->x;
    double dy = q->y - p->y;
    return hypot(dx, dy); // equivalente a sqrt(dx*dx + dy*dy), pero más estable
}

// Longitud de una línea = distancia entre sus extremos
double longitud_linea(const Linea* L) {
    return distancia_puntos(&L->a, &L->b);
}

/* Ejemplo de uso
int main(void) {
    Punto p1 = {0.0, 0.0}, p2 = {3.0, 4.0};
    Linea L = {p1, p2};
    printf("dist(p1,p2)=%.2f\n", distancia_puntos(&p1, &p2)); // 5.00
    printf("longitud(L)=%.2f\n", longitud_linea(&L));         // 5.00
}
*/
```
Este ejemplo ilustra la relación tiene-un (una Linea tiene dos Punto). En C, al no existir clases, la composición se logra por contención de struct. Pasar punteros constantes evita copias innecesarias y documenta la inmutabilidad de los datos dentro de las funciones.


## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.  

### Respuesta

La composición en Java se expresa haciendo que una clase contenga instancias de otras. Aquí, Linea tiene dos Punto. Para garantizar ocultación de información e inmutabilidad, conviene: hacer las clases final, declarar los campos private final, no exponer setters, y devolver vistas inmutables (aquí no hace falta copia defensiva porque no hay colecciones ni arrays). La distancia entre puntos se calcula con Math.hypot(dx, dy) y la longitud de una línea es la distancia entre sus extremos. Además, se valida que los puntos no sean null al construir la línea.

Se muestra a continuación una implementación sencilla y segura. Punto ofrece un método de instancia distanciaA(Punto otro) que no modifica estado; Linea almacena dos puntos inmutables y expone longitud(). Se añaden toString() para depuración y getters de solo lectura por si la API los requiere (manteniendo la inmutabilidad).

```java
// Punto.java
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    /** Distancia euclídea a otro punto (no modifica estado). */
    public double distanciaA(Punto otro) {
        if (otro == null) throw new IllegalArgumentException("otro no puede ser null");
        double dx = otro.x - this.x;
        double dy = otro.y - this.y;
        return Math.hypot(dx, dy); // estable numéricamente
    }

    @Override
    public String toString() {
        return "Punto(" + x + ", " + y + ")";
    }
}

// Linea.java
public final class Linea {
    private final Punto a; // extremo inicial (inmutable)
    private final Punto b; // extremo final   (inmutable)

    public Linea(Punto a, Punto b) {
        if (a == null || b == null) throw new IllegalArgumentException("Los extremos no pueden ser null");
        this.a = a;
        this.b = b;
    }

    /** Acceso de solo lectura (la inmutabilidad se mantiene porque Punto también es inmutable). */
    public Punto getA() { return a; }
    public Punto getB() { return b; }

    /** Longitud de la línea: distancia entre sus extremos. */
    public double longitud() {
        return a.distanciaA(b);
    }

    @Override
    public String toString() {
        return "Linea[" + a + " -> " + b + "]";
    }
}

// Ejemplo de uso
public class Main {
    public static void main(String[] args) {
        Punto p1 = new Punto(0.0, 0.0);
        Punto p2 = new Punto(3.0, 4.0);
        Linea L = new Linea(p1, p2);

        System.out.println("dist(p1,p2) = " + p1.distanciaA(p2)); // 5.0
        System.out.println("longitud(L) = " + L.longitud());       // 5.0
        // No existen setters: ni los puntos ni la línea pueden cambiar tras construirse.
    }
}
```
Con este diseño, Java permite mejorar el enfoque de C: se fuerza por construcción que los extremos de la línea no cambien (no hay setters, los campos son final) y que un Punto sea un valor inmutable. Si más adelante se quisiera admitir “segmentos dirigidos” o añadir validaciones adicionales (por ejemplo, prohibir extremos coincidentes), bastaría con extender la lógica del constructor sin exponer detalles internos.


## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

### Respuesta

La multiplicidad indica cuántas instancias de una clase intervienen en la relación estructural con otra clase. En el contexto de la composición, esta multiplicidad define cuántas “partes” forman el “todo”, reflejando si la relación es obligatoria, opcional o repetida. Como se viene de C/C++, donde se usa la contención de estructuras de forma directa, la multiplicidad en orientación a objetos formaliza algo similar: especifica cuántos objetos de un tipo se incluyen dentro de otro como componentes esenciales.

En el ejemplo de Linea y Punto, una línea está compuesta por exactamente dos puntos, que son sus extremos. Por tanto, la multiplicidad desde Linea hacia Punto es 2, o escrito en notación UML: Linea → Punto : 2. Esto significa que una instancia de Linea no puede existir sin tener exactamente dos puntos internos, ya que forman su estructura fundamental.

En sentido inverso, un Punto individual no está necesariamente ligado a una línea concreta: puede existir como un valor independiente o ser usado en contextos distintos. Sin embargo, cuando un punto sí forma parte de una línea, pertenece a una única línea, ya que la composición implica exclusividad. Por ello, la multiplicidad desde Punto hacia Linea se expresa como 0..1, indicando que un punto puede no pertenecer a ninguna línea o pertenecer a una sola como máximo.
Resumiendo:

De Linea a Punto → multiplicidad 2 (una línea tiene exactamente dos puntos).
De Punto a Linea → multiplicidad 0..1 (un punto puede no pertenecer a ninguna línea o pertenecer a una sola).


## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

### Respuesta

En orientación a objetos, la diferencia entre composición fuerte y composición débil tiene que ver con el grado de dependencia entre un objeto y sus partes internas. La composición fuerte indica que las partes forman parte esencial del objeto: no pueden existir de manera independiente y su ciclo de vida está ligado al del “todo”. Si el objeto principal desaparece, las partes también deben hacerlo. Este enfoque recuerda a cuando, en C, una estructura contiene otras estructuras “dentro”, formando un único bloque que tiene sentido únicamente como unidad.

La composición débil, en cambio, representa una relación en la que un objeto utiliza a otros, pero sin poseerlos realmente. Las partes pueden existir sin el objeto que las referencia y pueden ser compartidas entre varias instancias. Desde la perspectiva de alguien acostumbrado a C/C++, esto se parece a guardar un puntero a datos externos cuyo ciclo de vida no se controla desde la estructura que los usa.

En cuanto a los nombres habituales, la composición débil es lo que se denomina normalmente asociación o agregación, ya que el objeto solo mantiene referencias sin controlar la vida de las partes. La composición fuerte es la que se llama composición propiamente dicha y representa la propiedad exclusiva y la dependencia total entre el todo y sus partes.


## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

### Respuesta

Cuando una clase utiliza otra únicamente dentro de un método —ya sea porque la recibe como parámetro, la devuelve como resultado, la crea temporalmente con new, o la usa como variable local— no se está formando una composición, sino una dependencia. Esta relación indica simplemente que una clase necesita a otra para realizar cierta operación, del mismo modo que una función en C necesita recibir ciertas estructuras o punteros para cumplir su propósito. No existe una relación estructural permanente entre los objetos, sino un uso puntual y limitado al ámbito del método.

La clave está en que la composición implica posesión y parte-todo, mientras que la dependencia implica solo uso. En composición, los objetos internos forman parte del estado propio de la clase y su ciclo de vida está ligado al del objeto contenedor. En cambio, en una dependencia, el objeto usado puede existir antes, después o completamente al margen de la clase que lo emplea. El método simplemente interactúa con él, pero no lo almacena ni lo incorpora como parte estable del estado interno.

Así, cuando el uso de otro objeto no afecta al ciclo de vida ni a la estructura interna de la clase, se habla de dependencia. Este tipo de relación es la más ligera y común en programación, y suele aparecer siempre que una clase “necesita” otra solo para realizar una tarea momentánea.


## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

### Respuesta

En composición fuerte, la línea es dueña de sus puntos: crea sus propias instancias o hace copia defensiva si recibe puntos externos, y no expone referencias internas (devuelve copias). Así, si la línea deja de tener referencias, también quedan recolectables sus puntos internos. En Java no se destruye manualmente; el recolector de basura libera objetos inaccesibles.

```java
public final class Punto {
    private final double x, y;
    public Punto(double x, double y) { this.x = x; this.y = y; }
    public double getX() { return x; }
    public double getY() { return y; }
    public double distanciaA(Punto otro) {
        if (otro == null) throw new IllegalArgumentException("null");
        return Math.hypot(otro.x - this.x, otro.y - this.y);
    }
}

public final class LineaFuerte {
    private final Punto a;
    private final Punto b;

    public LineaFuerte(double x1, double y1, double x2, double y2) {
        this.a = new Punto(x1, y1);
        this.b = new Punto(x2, y2);
    }
    public LineaFuerte(Punto p1, Punto p2) {
        if (p1 == null || p2 == null) throw new IllegalArgumentException("null");
        this.a = new Punto(p1.getX(), p1.getY()); // copia defensiva
        this.b = new Punto(p2.getX(), p2.getY());
    }
    public double longitud() { return a.distanciaA(b); }
    public Punto getA() { return new Punto(a.getX(), a.getY()); } // no se expone el interno
    public Punto getB() { return new Punto(b.getX(), b.getY()); }
}
```
En composición débil (agregación), la línea no es dueña de los puntos: mantiene referencias a objetos creados fuera, que pueden compartirse por varias líneas y cuyo ciclo de vida es independiente. En este caso, los getters pueden devolver directamente las referencias almacenadas sin romper inmutabilidad, ya que Punto es inmutable.

```java
public final class LineaDebil {
    private final Punto a;
    private final Punto b;

    public LineaDebil(Punto a, Punto b) {
        if (a == null || b == null) throw new IllegalArgumentException("null");
        this.a = a;   // sin copia: agregación
        this.b = b;
    }
    public double longitud() { return a.distanciaA(b); }
    public Punto getA() { return a; }  // se expone la misma referencia
    public Punto getB() { return b; }
}
```
Como ejemplo de uso, la longitud es la misma en ambos casos, pero el ciclo de vida difiere: al perderse la última referencia a la LineaFuerte, sus puntos internos quedan elegibles para GC; al perderse una LineaDebil, los puntos pueden seguir vivos si existen otras referencias externas a ellos.


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

### Respuesta

En composición fuerte de Java no se destruyen explícitamente los objetos porque el ciclo de vida está gestionado por el recolector de basura. La idea de “el contenedor destruye a las partes” no significa que se invoque manualmente una destrucción, como podría hacerse en C con free o con estructuras anidadas cuyo almacenamiento desaparece al salir de un ámbito. En Java, lo que ocurre es que cuando la instancia del contenedor deja de ser accesible, también dejan de serlo las partes que solo existían dentro de él.

En el ejemplo de Linea y Punto, la clase Linea no tiene que destruir los puntos; simplemente deja de haber referencias hacia ellos cuando la línea deja de usarse. Si ninguna otra parte del programa mantiene referencias a esos puntos, el recolector de basura podrá liberar su memoria automáticamente. Por tanto, la destrucción no es responsabilidad del programador ni aparece reflejada en el código, sino que se produce cuando los objetos se vuelven inalcanzables desde el programa.


## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

### Respuesta

En composición débil (agregación), el departamento mantiene referencias a profesores cuyo ciclo de vida es independiente: pueden existir fuera del departamento y ser compartidos. Se impone la invariante de que siempre debe existir un director desde el inicio y que dicho director forme parte de la lista de profesores. Cambiar el director solo es válido si el nuevo director ya pertenece al departamento; eliminar un profesor es posible salvo que se trate del director (en cuyo caso debe cambiarse el director antes).

Para no desvelar que se usa un array interno, se ofrece una API mínima y encapsulada: añadir al final, eliminar por posición, consultar cuántos profesores hay y obtener un profesor por posición. Se limita el tamaño a 50 y se lanzan excepciones si se viola la invariante (por ejemplo, intentar eliminar al director o establecer un director que no esté en la lista), si se excede la capacidad, si se pasan referencias nulas o índices inválidos.

```java
// Profesor.java
public final class Profesor {
    private final String id;      // podría ser DNI, código interno, etc.
    private final String nombre;

    public Profesor(String id, String nombre) {
        if (id == null || id.isBlank()) throw new IllegalArgumentException("id inválido");
        if (nombre == null || nombre.isBlank()) throw new IllegalArgumentException("nombre inválido");
        this.id = id;
        this.nombre = nombre;
    }

    public String getId() { return id; }
    public String getNombre() { return nombre; }

    @Override
    public String toString() {
        return "Profesor{id='" + id + "', nombre='" + nombre + "'}";
    }
}
// Departamento.java
public final class Departamento {
    private static final int CAPACIDAD_MAX = 50;

    private final Profesor[] profesores = new Profesor[CAPACIDAD_MAX];
    private int size = 0;
    private Profesor director;

    /** Siempre debe haber un director desde el inicio y debe formar parte del departamento. */
    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) throw new IllegalArgumentException("director inicial null");
        this.director = directorInicial;
        this.profesores[0] = directorInicial;
        this.size = 1;
    }

    /** Número de profesores en el departamento (incluye al director). */
    public int numeroProfesores() {
        return size;
    }

    /** Obtiene el profesor en la posición dada (0..size-1). */
    public Profesor profesorEn(int posicion) {
        validarPosicion(posicion);
        return profesores[posicion]; // agregación débil: devolver la referencia es válido
    }

    /** Añade un profesor al final de la lista. No permite duplicados por identidad. */
    public void anadirProfesor(Profesor p) {
        if (p == null) throw new IllegalArgumentException("profesor null");
        if (size >= CAPACIDAD_MAX) throw new IllegalStateException("capacidad máxima alcanzada");
        if (indexOf(p) != -1) throw new IllegalStateException("el profesor ya pertenece al departamento");
        profesores[size++] = p;
    }

    /**
     * Elimina el profesor en la posición dada.
     * Prohíbe eliminar al director para mantener la invariante:
     * cambiar el director primero si se desea eliminarlo.
     */
    public void eliminarProfesorEn(int posicion) {
        validarPosicion(posicion);
        Profesor objetivo = profesores[posicion];
        if (objetivo == director) {
            throw new IllegalStateException("no se puede eliminar al director; cámbiese el director primero");
        }
        // compactar el array
        for (int i = posicion; i < size - 1; i++) {
            profesores[i] = profesores[i + 1];
        }
        profesores[--size] = null;
    }

    /** Devuelve el director actual (siempre no nulo). */
    public Profesor getDirector() {
        return director;
    }

    /**
     * Cambia el director a otro profesor del departamento.
     * El nuevo director debe estar ya en la lista; en caso contrario, se lanza excepción.
     */
    public void cambiarDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) throw new IllegalArgumentException("nuevo director null");
        int idx = indexOf(nuevoDirector);
        if (idx == -1) {
            throw new IllegalStateException("el nuevo director debe pertenecer al departamento");
        }
        this.director = nuevoDirector;
    }

    // --- utilidades privadas ---

    private int indexOf(Profesor p) {
        for (int i = 0; i < size; i++) {
            if (profesores[i] == p) return i; // identidad de objeto; alternativa: equals() si se define
        }
        return -1;
    }

    private void validarPosicion(int posicion) {
        if (posicion < 0 || posicion >= size) {
            throw new IndexOutOfBoundsException("posición inválida: " + posicion);
        }
    }
}
```
Este diseño implementa agregación en ambas relaciones: el departamento agrega una colección de profesores y agrega también a su director, sin asumir propiedad exclusiva ni gestionar su ciclo de vida. La encapsulación se preserva al no exponer el array ni su capacidad; el cliente únicamente conoce el número de profesores y puede acceder a uno por posición, además de poder añadir al final o eliminar por posición con las restricciones impuestas por la invariante.


## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

### Respuesta

Con List se simplifica la gestión de la colección: no hay que desplazar elementos manualmente al eliminar, no se expone un array, y se evita llevar un contador size. Se mantiene la agregación (composición débil): el departamento guarda referencias a Profesor cuyo ciclo de vida es independiente. Se conserva la invariante: siempre hay director y el director debe pertenecer a la lista; no se permite eliminar al director y solo se puede establecer como director a alguien ya incluido. Además, se limita la capacidad a 50 y se evitan duplicados por identidad. Respecto a devolver “toda la lista”, el problema de exponer directamente la lista interna es que el código cliente podría mutarla (añadir/eliminar) rompiendo la invariante; se resuelve devolviendo una vista inmodificable o una copia defensiva.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public final class Profesor {
    private final String nombre;
    public Profesor(String n) {
        if (n == null || n.isBlank()) throw new IllegalArgumentException();
        this.nombre = n;
    }
    public String getNombre() { return nombre; }
}
public final class Departamento {
    private static final int MAX = 50;
    private final List<Profesor> profesores = new ArrayList<>();
    private Profesor director;

    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) throw new IllegalArgumentException();
        this.director = directorInicial;
        profesores.add(directorInicial);
    }

    public int numProfesores() { return profesores.size(); }

    public Profesor profesorEn(int pos) {
        return profesores.get(pos);
    }

    public void anadirProfesor(Profesor p) {
        if (p == null) throw new IllegalArgumentException();
        if (profesores.size() >= MAX) throw new IllegalStateException();
        if (profesores.contains(p)) throw new IllegalStateException();
        profesores.add(p);
    }

    public void eliminarProfesorEn(int pos) {
        Profesor p = profesores.get(pos);
        if (p == director) throw new IllegalStateException("No se puede eliminar al director");
        profesores.remove(pos);
    }

    public Profesor getDirector() { return director; }

    public void cambiarDirector(Profesor p) {
        if (!profesores.contains(p)) throw new IllegalStateException();
        director = p;
    }

    // Vista inmodificable (no romper invariantes)
    public List<Profesor> listaProfesores() {
        return Collections.unmodifiableList(profesores);
    }
}
```

En esta versión se elimina la gestión manual del tamaño, el desplazamiento de elementos al borrar y la necesidad de llevar un contador, que eran partes del código original con arrays. También desaparece la validación manual de posiciones porque List ya lanza excepciones adecuadas.

Si existiera un método que devolviera “todos los profesores”, no sería seguro devolver directamente la lista interna porque el código cliente podría modificarla (añadir, quitar, romper la invariante del director, exceder el máximo, etc.). Para evitarlo, se devuelve una vista inmodificable (Collections.unmodifiableList) o una copia defensiva (new ArrayList<>(profesores)), asegurando que el estado interno del departamento queda protegido.


## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

### Respuesta

La composición recursiva aparece cuando una clase contiene referencias a instancias de la misma clase. En Java, esto se modela haciendo que Persona tenga un campo madre de tipo Persona. Para mantener inmutabilidad, se usan campos private final, sin setters, y se valida en el constructor. Como en un árbol genealógico puede no conocerse la madre más arriba en la cadena, se permite madre == null en la raíz (por ejemplo, la abuela de la que no se dispone de más ascendencia).

A continuación, se muestra una Persona inmutable y un main que construye una pequeña familia (nieto → madre → abuela), y recorre la cadena de madres de forma recursiva/iterativa. Obsérvese que no hay mutaciones: una vez creada cada Persona, su identidad y su vínculo a madre no cambian.

```java
public final class Persona {
    private final String nombre;
    private final Persona madre; // puede ser null en la raíz de la ascendencia

    public Persona(String nombre, Persona madre) {
        if (nombre == null || nombre.isBlank()) throw new IllegalArgumentException("nombre inválido");
        this.nombre = nombre;
        this.madre = madre; // null permitido para la raíz (p. ej., abuela sin datos de su madre)
    }

    public String getNombre() { return nombre; }
    public Persona getMadre() { return madre; }

    @Override
    public String toString() {
        return "Persona{" + nombre + (madre != null ? ", madre=" + madre.nombre : ", madre=∅") + "}";
    }

    // Ejemplo de uso
    public static void main(String[] args) {
        Persona abuela = new Persona("Carmen", null);
        Persona madre  = new Persona("Laura", abuela);
        Persona nieto  = new Persona("Diego", madre);

        // Recorrido ascendente desde el nieto hasta la abuela
        Persona p = nieto;
        while (p != null) {
            System.out.println(p.getNombre());
            p = p.getMadre();
        }
        // Salida:
        // Diego
        // Laura
        // Carmen
    }
}
```
Otros ejemplos clásicos de composiciones recursivas incluyen: nodos de un árbol binario (cada nodo tiene referencia a hijo izquierdo y derecho), elementos del DOM/HTML (un nodo contiene hijos del mismo tipo), sistemas de ficheros (una carpeta contiene subcarpetas y archivos; las subcarpetas contienen más carpetas), expresiones aritméticas (una expresión compuesta contiene subexpresiones), listas enlazadas (cada nodo referencia al siguiente nodo), y estructuras de organigramas (un empleado referencia a su responsable, que a su vez es otro empleado).

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

### Respuesta

Una relación de composición bidireccional se produce cuando dos clases se “poseen” mutuamente mediante referencias en ambas direcciones. Esto implica que cada objeto mantiene un enlace hacia el otro, de modo que puede navegarse desde cualquiera de los dos extremos. En composición débil esto es habitual, pero requiere más cuidado porque las invariantes deben mantenerse en ambos sentidos. Este tipo de relación introduce la responsabilidad de mantener las dos referencias **coherentes**, evitando que un lado apunte al otro si el otro no apunta de vuelta, y viceversa.

En el ejemplo de `Profesor` y `Departamento`, la relación actual es **unidireccional**: el departamento conoce a los profesores, pero los profesores no conocen a su departamento. Para convertirla en bidireccional sería necesario añadir, dentro de `Profesor`, un campo con referencia al `Departamento` al que pertenece cada profesor. Esto implica actualizar el departamento cada vez que se añade o elimina un profesor, y actualizar también la referencia interna del profesor para que apunte al departamento correcto. En otras palabras, al añadir un profesor habría que establecer `profesor.setDepartamento(this)` y, al eliminarlo, habría que poner esa referencia a `null`.

Como consecuencia, el código de `Departamento` tendría que invocar métodos en `Profesor` para mantener ambos lados sincronizados, y `Profesor` tendría que ser mutable en ese aspecto concreto. También habría que aplicar la invariante del director en ambos sentidos: al cambiar de director, además de comprobar que pertenece al departamento, habría que asegurarse de que su referencia interna al departamento es correcta. Este tipo de relación aumenta la complejidad del diseño y exige validar que las dos direcciones de la relación se mantienen siempre consistentes.

