## TP4 Exercices d’optimisation

PENG Hanyuan & YAN Wenli

---

### Exercice1
#### 1.Changement de type de données
##### Problème et modification
Dans Exercice 1, on a trouvé qu'il mélange de différents types de données, `int`, `float` qui sont primitifs, et `Integer` et `Float` qui sont les classes de wrapper d'objet correspondante.
Ceci provoque des occurrences fréquentes de ces phénomènes: `Boxing` et `Unboxing` lors de l'exécution de l'opération.

Donc pour éviter d'utiliser de différents types de données et augmenter la performance pendant le calcul, on a changé tous les `Integer` et `Float` aux types primitifs `ìnt` et `float`. Comme indiqué ci-dessous:
```java
// my code
private static final float[][] MATRIX_B = {
        {1 / 42f, 1 / 42f, 2 / 42f, 2 / 42f, 2 / 42f, 1 / 42f, 1 / 42f},
        {1 / 42f, 2 / 42f, 3 / 42f, 4 / 42f, 3 / 42f, 2 / 42f, 1 / 42f},
        {2 / 42f, 3 / 42f, 4 / 42f, 5 / 42f, 4 / 42f, 3 / 42f, 2 / 42f},
        {2 / 42f, 4 / 42f, 5 / 42f, 8 / 42f, 5 / 42f, 4 / 42f, 2 / 42f},
        {2 / 42f, 3 / 42f, 4 / 42f, 5 / 42f, 4 / 42f, 3 / 42f, 2 / 42f},
        {1 / 42f, 2 / 42f, 3 / 42f, 4 / 42f, 3 / 42f, 2 / 42f, 1 / 42f},
        {1 / 42f, 1 / 42f, 2 / 42f, 2 / 42f, 2 / 42f, 1 / 42f, 1 / 42f}
};
```
```java
public static float[][] my_multiply2(final int[][] matrix) {
    final float[][] result = new float[7][7];

    for (int i = 0; i < 7; i = i + 1)
        for (int j = 0; j < 7; j = j + 1) {
            float currentValue = 0F;

            for (int k = 0; k < 7; k = k + 1) {
                currentValue = currentValue + matrix[i][k] * MATRIX_B[k][j];
            }
            result[i][j] = currentValue;
        }
    return result;
}
```

##### Boxing, Unboxing
Le `Boxing` est la conversion automatique effectuée par le compilateur Java entre les types primitifs et les classes de wrapper d'objets correspondantes, par exemple, la conversion d'un `int` en un `Integer`, d'un `double` en un `Double`, etc.

Si la conversion va dans l'autre sens, cela s'appelle `Unboxing`.


##### Impact sur la performance
Quand le type `int` et `Integer` sont utilisés dans un même calcul (c'est-à-dire contient des opérations arithmétiques), en fait，ils comparent par la valeur, donc le processus déclenche le `Boxing` et `Unboxing` qui ajoute la complexité du processus et réduit les performances.
Donc c'est mieux d'utiliser des mêmes types de données. Et en plus, pendant le calcul, c'est l'opération sur des valeurs, c'est mieux d'utiliser des types primitifs.


#### 2. Déclaration de variable dans la boucle
##### Problème et modification
```java
for (Integer i = 0; i < 7; i = i + 1) {
    for (Integer j = 0; j < 7; j = j + 1) {
        Float currentValue = 0F;
```
On a trouvé que la éclaration de variable `float currentValue` est faite réculierement dans les deux boucles for. On pense qu'ici on a pas besoin de faire la déclaration chaque fois car cette variable n'est pas une variable temporaire.

Donc on extrait la déclaration dehors les boucles for.

```java
public static float[][] my_multiply2_out(final int[][] matrix) {
        final float[][] result = new float[7][7];
        float currentValue;
        for (int i = 0; i < 7; i = i + 1)
            for (int j = 0; j < 7; j = j + 1) {
                currentValue = 0F;

                for (int k = 0; k < 7; k = k + 1) {
                    currentValue = currentValue + matrix[i][k] * MATRIX_B[k][j];
                }

                result[i][j] = currentValue;
            }

        return result;
    }
```

##### Impact sur la performance
En fait, ça augmente pas beaucoup la performance, mais c'est toujours mieux.

##### Benchmark pour Ex1
![resultat_ex1](images/2018/11/resultat-ex1.png)


### Exercice2 Fibonacci
Code original:
```java
public static int fibonacci(final Integer i) {
    if (i < 0) {
        throw new IllegalArgumentException("Invalid input value");
    }
    return (i < 3) ? i : fibonacci(i - 1) + fibonacci(i - 2);
}
```
#### 1. Changement de type de données
##### Problème et modification
```java
public static int my_fibonacci(final int i) {
    if (i < 0) {
        throw new IllegalArgumentException("Invalid input value");
    }
    return (i < 3) ? i : fibonacci(i - 1) + fibonacci(i - 2);
}
```

Comme avant, il existe des problèmes sur les types de données. Dans cette méthode, le type de retour est `int` mais le type de paramètre est `Integer`. Cela provoque une augmentation du coût en temps des dépenses en `Boxing` et `Unboxing`.

#### 2. Mot clé `final`
```java
public static final int my_fibonacci_final(final int i) {
    if (i < 0) {
        throw new IllegalArgumentException("Invalid input value");
    }
    return (i < 3) ? i : fibonacci(i - 1) + fibonacci(i - 2);
}
```
##### Modification
On a ajouté le mot clé `final` sur la méthode.
##### Impact sur la performance

#### 3. Utilisation de la méthode récursive ou boucle for

##### Problème et modification
La méthode récursive est une manière d'écrire succincte, mais ce type d'imbrication conduit à plusieurs appels de la même partie.
Si on ne stocke pas les résultats précédents, cela entraînera beaucoup de doubles comptages.

Donc on a modifié l'algorithme en utilisant la boucle `for` et en stockant les résultats précédents.
```java
public static int my_fibonacci_for(final int i) {
    if (i < 0) {
        throw new IllegalArgumentException("Invalid input value");
    }
    int result = 1;
    int[] temp = new int[100];
    temp[0] = 1;
    temp[1] = 1;
    for (int j = 2; j <= i; j++) {
        temp[j] = temp[j - 2] + temp[j - 1];
    }
    return temp[i];
}
```
##### Impact sur la performance
Après la modification, la performance est significativement améliorée.

##### Benchmark pour Ex2
![resultat_ex2](images/2018/11/resultat-ex2.png)


### Exercice3a/3b
Exercice 3a et 3b sont similaires. On parle d'abord Exercice 3a.
#### 1. Changement de type de données
```java
//ex3a_int
public int exercice3a_int(final Integer nbThreads, final Integer nbIterationByThread)
```
```java
//ex3a_int
for (int i = 0; i < nbThreads; i++) {
    final Future future = service.submit(() -> iterate(nbIterationByThread));
    futures.add(future);
}
```
Dans cettes méthode, on a modifié aussi (Comme ex1)le type de donnée de retour `count` et de l'itérateur `i` dans la boucle `for`.
Ça augmente un peu la performance car `int` est le type primitif, mais `Integer` est la classe de wrapper d'objet correspondante de `int`.


#### 2. Utilisation de mots-clés `synchronised`
```java
//3a_synchronised_variable
private void incrementCounter() {
    synchronized (MUTEX) {
        count++;
    }
}
```
Dans cette méthode, on a utilisé qu'une fois le mot clé `synchronized`, sur le block intérieur.
La partie synchronisation du programme Java affecte beaucoup l’efficacité de l’exécution. En général, une méthode crée d’abord des variables locales, puis effectue certaines opérations sur ces variables, telles que les calculations, l’affichage, etc. Et le plus de code couvert par la synchronisation, l'impact sur l’efficacité est plus grave. Donc, nous essayons généralement de réduire l'influence de la synchronisation en utilisant ce mot clé `synchronized` sur le block code qu'on veut synchroniser entre les threads.

Mais en fait dans ce cas là si on met le mot clé `synchronised` sur la méthode, la performance sera augmented aussi, car ici il y a qu'une ligne de code. L'impact est pareil.


#### 3. Détermination de la fin d'exécution de tous les threads
```java
//ex3a excute(), isTerminated()
public Integer exercice3a_excute(final Integer nbThreads, final Integer nbIterationByThread) throws ExecutionException, InterruptedException {
    final ExecutorService service = Executors.newFixedThreadPool(nbThreads);

    for (Integer i = 0; i < nbThreads; i++) {
        service.execute(()->iterate(nbIterationByThread));
    }

    service.shutdown();
    while(true){
        if(service.isTerminated()){
            return count;
        }
    }
}
```
Dans le code source, pour assurer que le processus retourne la valeur après tous les threads sont terminés, il utilise le `futur.get()`.
On a essayé de changer la façon à déterminer la fin d'exécution de tous les threads en utilisant `excute()` et `isTerminated()`. Cette ligne fait la même chose.

Ça n'augmente pas de performance car en fait il faut attendre et vérifier que tous les threads sont terminés en utilisant n'importe quelle méthode.

```java
//ex3a nolist_futures
public Integer exercice3a_nolistfuture(final Integer nbThreads, final Integer nbIterationByThread) throws ExecutionException, InterruptedException {
    final ExecutorService service = Executors.newFixedThreadPool(nbThreads);

    for (Integer i = 0; i < nbThreads; i++) {
        final Future future = service.submit(() -> iterate_1(nbIterationByThread));
        future.get();
    }

    // Wait for it...
//        for (final Future<Runnable> future : futures) {
//            future.get();
//        }
    service.shutdown();
    return count;
}
```
Cette façon augmente la performance de plus de 4 fois.
En fait la méthode `service.shutdown()` peut faire terminer tous les threads à la fin des tâches, exécute des tâches précédemment soumise, mais n'accepte pas de nouvelles tâches. S'il est déjà fermé, l'appel n'a pas d'autre effet.

#### Benchmark pour Ex3a
![resultat_3a](images/2018/11/resultat-3a.png)


#### Exercice 3b
Exercice 3b est similaire à exercice 3a, on choisit le mot clé `synchronised` à modifier:
```java
//3b_synchronised_variable

private void incrementCounter_synchro_variable(final Integer modulo) {
   synchronized (MUTEX) {
       count = (count + 1) % modulo;
   }
}
private void iterate_synchro_variable(final Integer nbIteration, final Integer modulo) {
    for(Integer i = 0; i < nbIteration; i++) {
        this.incrementCounter_synchro_variable(modulo);
    }
}
public Integer exercice3b_synchro_varible(final Integer nbThreads, final Integer nbIterationByThread, final Integer modulo) throws ExecutionException, InterruptedException {
    final ExecutorService service = Executors.newFixedThreadPool(nbThreads);
    final List<Future<Runnable>> futures = new ArrayList<>();

    for (Integer i = 0; i < nbThreads; i++) {
        final Future future = service.submit(() -> iterate_synchro_variable(nbIterationByThread, modulo));
        futures.add(future);
    }

    for (final Future<Runnable> future : futures) {
        future.get();
    }

    service.shutdown();
    return count;
}


//3b_synchronised_méthode
private synchronized void incrementCounter_synchro_methode(final Integer modulo) {
        count = (count + 1) % modulo;
}
private void iterate_synchro_methode(final Integer nbIteration, final Integer modulo) {
    for(Integer i = 0; i < nbIteration; i++) {
        this.incrementCounter_synchro_methode(modulo);
    }
}
public Integer exercice3b_synchro_methode(final Integer nbThreads, final Integer nbIterationByThread, final Integer modulo) throws ExecutionException, InterruptedException {
    final ExecutorService service = Executors.newFixedThreadPool(nbThreads);
    final List<Future<Runnable>> futures = new ArrayList<>();

    for (Integer i = 0; i < nbThreads; i++) {
        final Future future = service.submit(() -> iterate_synchro_methode(nbIterationByThread, modulo));
        futures.add(future);
    }

    for (final Future<Runnable> future : futures) {
        future.get();
    }
    service.shutdown();
    return count;
}
```
#### Benchmark pour Ex3b
![resultat_3b_synchro](images/2018/11/resultat-3b-synchro.png)

Selon le résultat de benchmark, la performance est augmentée si on utilise une seule fois le mot clé `synchronised`. Car ici on n'a pas besoin de le faire deux fois et il va prendre de plus de temps.
Ici, la performance entre les deux cas: synchroniser la méthode ou synchroniser la variable est presque pareille car il y a qu'une ligne de code.

### Exercice4
#### 1. LinkedList to ArrayList
```java
 public static byte[] exercice4_array(final byte[]... bytes)
```
Dans cette méthode, nous avons utilisé le `ArrayList`. Pour la partie l'insertion dans tableau, il n'y pas grande différence entre ces deux type car nous insérons les élément à la fin de arrayList (linkedlist).
Nous avons pensé que pour la partie lecture:
```java
for(Integer i = 0; i < list.size(); i++) {
           result[i] = list.get(i);
       }
```
Comme c'est l'accès aléatoire. Donc `ArrayList` est plus rapide que `LinkedList`.
![resultat_4_array](images/2018/11/resultat-4-array.png)


#### 2. "No more List"
```java
public static byte[] exercice4_nolist(final byte[]... bytes){
      int count = 0;
      for(final byte[] byteArray : bytes) {
           for(final Byte currentByte : byteArray) {
               count++;
           }
       }

       final byte[] result = new byte[count];

       ...
}
```

Dans cette méthode, nous avons enlevé la liste, et nous avons fait une autre boucle pour compter le nombre total de byte.
![resultat_4_nolist](images/2018/11/resultat-4-nolist.png)

#### 3. Integer to int

```java
public static byte[] exercice4_nolist_int
```
Dans cette méthode, nous avons changé le type `Integer` au type `int`. Il y a l'accès aléatoire donc il va faire `unboxing` quand il accède au mémoire.
La version `int` marche plus rapide qu'avant.

![resultat_4_nolist_int](images/2018/11/resultat-4-nolist-int.png)

#### 4. Byte to byte

```java
public static byte[] exercice4_nolist_int_byte(final byte[]... bytes) {

        int count = 0;
        for(final byte[] byteArray : bytes) {
            for(int j = 0;j<byteArray.length;j++) {
                count++;
            }
        }

        final byte[] result = new byte[count];

        int i = 0;
        for(final byte[] byteArray : bytes) {
            for(final byte currentByte : byteArray) {
                result[i] = currentByte;
                i++;
            }
        }

        return result;
    }
```

Dans cette méthode, nous avons utilisé que le type `byte` pour qu'il ne fait plus de `unboxing` pendant l'exécution.

![resultat_4_byte](images/2018/11/resultat-4-byte.png)

### Exercice5
#### 1. Désactiver la vérification de permission
```java
public static String getName_2(final Guy guy) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
    Method m = guy.getClass().getMethod("getName");
    m.setAccessible(true);
    return (String)m.invoke(guy);
}
```
Dans cette méthode `getName_2`, nous avons ajouté `m.setAccessible(true);` pour désactiver la vérification de permission du JVM. Mais après le benchmark nous avons trouvé que l'exécution est plus lent qu'avant.

![resultat_5_1](images/2018/11/resultat-5-1.png)

#### 2. L'appelle direct de la méthode `.getName`

```java
public static String getName_1(final Guy guy) {
        return guy.getName();
    }
```

Dans cette méthode, nous avons appelé directement la méthode `getName`. Elle exécute plus rapide qu'avant.

![resultat_5_2](images/2018/11/resultat-5-2.png)

On pense que la méthode `getMethod` et `invoke` prennent beaucoup de temps à vérifier la permission, chercher la méthode etc.

### Test unitaire
![Test_unitaire](images/2018/11/test-unitaire.png)

Toutes les méthodes ont passés correctement des tests unitaires.
