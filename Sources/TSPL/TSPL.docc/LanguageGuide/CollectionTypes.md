

# Tipos de Coleção

Swift oferece três *tipos de coleção* primários, conhecidos como arrays, sets e dicionários, para armazenar coleções de valores. Arrays são coleções ordenadas de valores. Sets são coleções não ordenadas de valores únicos. Os dicionários são coleções não ordenadas de associações chave-valor.

![](CollectionTypes_intro)

Arrays, sets e dicionários em Swift são sempre claros sobre os tipos de valores e chaves que podem armazenar. Isso significa que você não pode inserir um valor do tipo errado em uma coleção por engano. Isso também significa que você pode ter certeza sobre o tipo de valores que recuperará de uma coleção.

> Nota: Os tipos de array, sets e dicionário em Swift são implementados como *coleções genéricas*. Para saber mais sobre tipos genéricos e coleções, consulte <doc:Generics>.

## Mutabilidade de Coleções

Se você criar um array, um set ou um dicionário e atribuí-lo a uma variável, a coleção criada será *mutável*. Isso significa que você pode alterar (ou *mutar*) a coleção depois de criada adicionando, removendo ou alterando itens na coleção. Se você atribuir um array, um set ou um dicionário a uma constante,
essa coleção é *imutável* e seu tamanho e conteúdo não podem ser alterados.

> Nota: É uma boa prática criar coleções imutáveis em todos os casos em que a coleção não precisa ser alterada. Fazer isso torna mais fácil raciocinar sobre seu código e permite que o compilador Swift otimize o desempenho das coleções que você cria.

## Arrays

Um *array* armazena valores do mesmo tipo em uma lista ordenada. O mesmo valor pode aparecer em um array várias vezes em posições diferentes.

> Nota: o tipo `Array` de Swift é uma ponte para a classe `NSArray` do Foundation. Para obter mais informações sobre como usar `Array` com Foundation e Cocoa, consulte [Bridging Between Array and NSArray](https://developer.apple.com/documentation/swift/array#2846730).

### Sintaxe Abreviada de Array

O tipo de uma array Swift é escrito por completo como `Array<Element>`, onde `Element` é o tipo de valores que a array pode armazenar. Você também pode escrever o tipo de uma array de forma abreviada como `[Element]`. Embora as duas formas sejam funcionalmente idênticas, a forma abreviada é preferida e é usada ao longo deste guia ao se referir ao tipo de uma array.

### Criando uma Array vazia

Você pode criar uma matriz vazia de um determinado tipo usando a sintaxe do inicializador:

```swift
var someInts: [Int] = []
print("someInts is of type [Int] with \(someInts.count) items.")
// Imprime "someInts is of type [Int] with 0 items."
```

Note que o tipo da variável `someInts` é inferido como `[Int]` a partir do tipo do inicializador.

Alternativamente, se o contexto já fornece informações de tipo, como um argumento de função ou uma variável ou constante já digitada, você pode criar uma array vazia com um literal de array vazio, que é escrito como `[]` (um par vazio de colchetes):

```swift
someInts.append(3)
// someInts agora contém um valor do tipo Int
someInts = []
// someInts agora é uma array vazia, mas ainda é do tipo [Int]
```

### Criando um Array com um Valor Padrão

O tipo `Array` do Swift também fornece um inicializador para criar uma array de um determinado tamanho com todos os seus valores definidos como mesmo valor padrão. Você passa a esse inicializador um valor padrão do tipo apropriado (chamado `repeating`) e o número de vezes que esse valor é repetido (chamado `count`):

```swift
var threeDoubles = Array(repeating: 0.0, count: 3)
// threeDoubles é do tipo [Double], e igual a [0.0, 0.0, 0.0]
```

### Criando um Array para Somar Dois Arrays

Você pode criar um novo array adicionando doiss arrays existentes com tipos compatíveis com o operador de adição (`+`). O tipo do novo array é inferido a partir do tipo dos dois arrays que você adiciona:

```swift
var anotherThreeDoubles = Array(repeating: 2.5, count: 3)
// anotherThreeDoubles é do tipo [Double], e igual a [2.5, 2.5, 2.5]

var sixDoubles = threeDoubles + anotherThreeDoubles
// sixDoubles é inferido como [Double], e igual a [0.0, 0.0, 0.0, 2.5, 2.5, 2.5]
```

### Criando um Array com um Literal de Array 

Você também pode inicializar um array com um *literal de array*, que é uma forma abreviada de escrever um ou mais valores como uma coleção de array. Um literal de array é escrito como uma lista de valores, separados por vírgulas, entre colchetes:

```
[<#value 1#>, <#value 2#>, <#value 3#>]
```

O exemplo abaixo cria um array chamado `shoppingList` para armazenar valores `String`:

```swift
var shoppingList: [String] = ["Eggs", "Milk"]
// shoppingList inicializado com dois items iniciais
```

A variável `shoppingList` é declarada como “um array de valores de string”, escrita como `[String]`. Comoo esse array especificou um tipo de valor `String`, é permitido armazenar apenas valores `String`. Aqui, o array `shoppingList` é inicializado com dois valores `String` (`"Eggs"` e `"Milk"`), escritos dentro de um  literal de array.

> Nota: O array `shoppingList` é declarado como uma variável (com o introdutor `var`) e não uma constante (com o introdutor `let`) porque mais itens são adicionados à lista de compras nos exemplos abaixo.

Nesse caso, o literal de array contém dois valores `String` e nada mais. Isso corresponde ao tipo de declaração da variável `shoppingList` (um array que só pode conter valores `String`) e, portanto, a atribuição do literal do array é permitida como uma forma de inicializar `shoppingList` com dois itens iniciais.

Graças à inferência de tipo em Swift, você não precisa escrever o tipo do array se estiver inicializando-o com um literal de array contendo valores do mesmo tipo. A inicialização de `shoppingList` poderia ter sido escrita de uma forma mais curta:

```swift
var shoppingList = ["Eggs", "Milk"]
```

Como todos os valores no literal de array são do mesmo tipo, é possivel inferir que `[String]` é o tipo correto a ser usado para a variável `shoppingList`.

### Acessando e Modificando um Array

Você acessa e modifica um array por meio de seus métodos e propriedades ou usando a sintaxe de subscrito.

Para descobrir o número de itens em um array, verifique sua propriedade `count` que é somente de leitura:

```swift
print("The shopping list contains \(shoppingList.count) items.")
// Imprime "The shopping list contains 2 items."
```

Use a propriedade booleana `isEmpty` como um atalho para verificar se a propriedade `count` é igual a `0`:

```swift
if shoppingList.isEmpty {
   print("The shopping list is empty.")
} else {
   print("The shopping list isn't empty.")
}
// Imprime "The shopping list isn't empty."
```

Você pode adicionar um novo item ao final de um array chamando o método `append(_:)` do array:

```swift
shoppingList.append("Flour")
// shoppingList agora contém 3 itens e alguém está fazendo panquecas
```

Alternativamentea, anexe um array de um ou mais itens compatíveis com o operador de atribuição de adição (`+=`):

```swift
shoppingList += ["Baking Powder"]
// shoppingList agora contém 4 itens
shoppingList += ["Chocolate Spread", "Cheese", "Butter"]
// shoppingList agora contém 7 itens
```

Recupere um valor do array usando *sintaxe de subscrito*, passando o índice do valor que deseja recuperar entre colchetes imediatamente após o nome do array:

```swift
var firstItem = shoppingList[0]
// firstItem é igual a "Eggs"
```

> Nota: O primeiro item no array tem um índice de `0`, não `1`. Arrays em Swift são sempre indexados por zero.

Você pode usar a sintaxe de subscrito para alterar um valor existente em um determinado índice:

```swift
shoppingList[0] = "Six eggs"
// o primeiro item da lista agora é igual a "Six eggs" em vez de "Eggs"
```

Ao usar a sintaxe de subscrito, o índice especificado precisa ser válido. Por exemplo, escrever `shoppingList[shoppingList.count] = "Salt"` para tentar anexar um item ao final do array resulta em um erro de tempo de execução.

Você também pode usar a sintaxe de subscrito para alterar um intervalo de valores de uma só vez, mesmo se o conjunto de valores de substituição tiver um comprimento diferente do intervalo que você está substituindo. O exemplo a seguir substitui `"Chocolate Spread"`, `"Cheese"` e `"Butter"` por `"Bananas"` e `"Apples"`:

```swift
shoppingList[4...6] = ["Bananas", "Apples"]
// shoppingList agora contém 6 itens
```

Para inserir um item no array em um índice especificado, chame o método `insert(_:at:)` do array:

```swift
shoppingList.insert("Maple Syrup", at: 0)
// shoppingList agora contém 7 itens
// "Maple Syrup" é agora o primeiro item da lista
```

Essa chamada para o método `insert(_:at:)` insere um novo item com um valor de `"Maple Syrup"` bem no início da lista de compras, indicado por um índice de `0`.

Da mesma forma, você remove um item do array com o método `remove(at:)`. Este método remove o item no índice especificado e retorna o item removido (embora você possa ignorar o valor retornado se não precisar dele):

```swift
let mapleSyrup = shoppingList.remove(at: 0)
// o item que estava no índice 0 foi removido
// shoppingList agora contém 6 itens, sem Maple Syrup
// a constante mapleSyrup agora é igual a string "Maple Syrup" removida
```

> Nota: Se você tentar acessar ou modificar um valor para um índice que está fora dos limites existentes de um array, você irá disparar um erro de tempo de execução. Você pode verificar se um índice é válido antes de usá-lo, comparando-o com a propriedade `count` do array. O maior índice válido em uma matriz é `count - 1` porque arrays são indexados a partir de zero --- no entanto, quando `count` é `0` (o que significa que o array está vazio), não há índices válidos.

Quaisquer lacunas em um array são fechadas quando um item é removido e, portanto, o valor no índice `0` é novamente igual a `"Six eggs"`:

```swift
firstItem = shoppingList[0]
// firstItem é igual a "Six eggs"
```

Se você deseja remover o item final de um array, use o método `removeLast()` em vez do método `remove(at:)` para evitar a necessidade de consultar a propriedade `count` do array. Como o método `remove(at:)`, `removeLast()` retorna o item removido:

```swift
let apples = shoppingList.removeLast()
// O último item do array foi removido
// shoppingList agora contém 5 itens
// a constante apples é igual a string "Apples" removida
```

### Iterando em um Array

Você pode iterar sobre todo o conjunto de valores em um array com o loop `for`-`in`:

```swift
for item in shoppingList {
   print(item)
}
// Six eggs
// Milk
// Flour
// Baking Powder
// Bananas
```


Se você precisar do índice inteiro de cada item, bem como de seu valor, use o método `enumerated()` para iterar sobre o array. Para cada item no array, o método `enumerated()` retorna uma tupla composta por um inteiro e o item. Os inteiros começam em zero e contam para cima em um para cada item; se você enumerar sobre todo um array, esses inteiros corresponderão aos índices dos itens. Você pode decompor a tupla em constantes ou variáveis temporárias como parte da iteração:

```swift
for (index, value) in shoppingList.enumerated() {
   print("Item \(index + 1): \(value)")
}
// Item 1: Six eggs
// Item 2: Milk
// Item 3: Flour
// Item 4: Baking Powder
// Item 5: Bananas
```


Para saber mais sobre o loop `for`-`in`, consulte <doc:ControlFlow#For-In-Loops>.

## Sets

A *set* stores distinct values of the same type
in a collection with no defined ordering.
You can use a set instead of an array when the order of items isn't important,
or when you need to ensure that an item only appears once.

> Note: Swift's `Set` type is bridged to Foundation's `NSSet` class.For more information about using `Set` with Foundation and Cocoa,
> see [Bridging Between Set and NSSet](https://developer.apple.com/documentation/swift/set#2845530).



### Hash Values for Set Types

A type must be *hashable* in order to be stored in a set ---
that is, the type must provide a way to compute a *hash value* for itself.
A hash value is an `Int` value that's the same for all objects that compare equally,
such that if `a == b`,
the hash value of `a` is equal to the hash value of `b`.

All of Swift's basic types (such as `String`, `Int`, `Double`, and `Bool`)
are hashable by default, and can be used as set value types or dictionary key types.
Enumeration case values without associated values
(as described in <doc:Enumerations>)
are also hashable by default.

> Note: You can use your own custom types as set value types or dictionary key types
> by making them conform to the `Hashable` protocol
> from the Swift standard library.
> For information about implementing the required `hash(into:)` method,
> see [Hashable](https://developer.apple.com/documentation/swift/hashable).
> For information about conforming to protocols, see <doc:Protocols>.

### Set Type Syntax

The type of a Swift set is written as `Set<Element>`,
where `Element` is the type that the set is allowed to store.
Unlike arrays, sets don't have an equivalent shorthand form.

### Creating and Initializing an Empty Set

You can create an empty set of a certain type
using initializer syntax:

```swift
var letters = Set<Character>()
print("letters is of type Set<Character> with \(letters.count) items.")
// Prints "letters is of type Set<Character> with 0 items."
```




> Note: The type of the `letters` variable is inferred to be `Set<Character>`,
> from the type of the initializer.

Alternatively, if the context already provides type information,
such as a function argument or an already typed variable or constant,
you can create an empty set with an empty array literal:

```swift
letters.insert("a")
// letters now contains 1 value of type Character
letters = []
// letters is now an empty set, but is still of type Set<Character>
```




### Creating a Set with an Array Literal

You can also initialize a set with an array literal,
as a shorthand way to write one or more values as a set collection.

The example below creates a set called `favoriteGenres` to store `String` values:

```swift
var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
// favoriteGenres has been initialized with three initial items
```




The `favoriteGenres` variable is declared as
“a set of `String` values”, written as `Set<String>`.
Because this particular set has specified a value type of `String`,
it's *only* allowed to store `String` values.
Here, the `favoriteGenres` set is initialized with three `String` values
(`"Rock"`, `"Classical"`, and `"Hip hop"`), written within an array literal.

> Note: The `favoriteGenres` set is declared as a variable (with the `var` introducer)
> and not a constant (with the `let` introducer)
> because items are added and removed in the examples below.

A set type can't be inferred from an array literal alone,
so the type `Set` must be explicitly declared.
However, because of Swift's type inference,
you don't have to write the type of the set's elements
if you're initializing it with an array literal
that contains values of just one type.
The initialization of `favoriteGenres` could have been written in a shorter form instead:

```swift
var favoriteGenres: Set = ["Rock", "Classical", "Hip hop"]
```




Because all values in the array literal are of the same type,
Swift can infer that `Set<String>` is
the correct type to use for the `favoriteGenres` variable.

### Accessing and Modifying a Set

You access and modify a set through its methods and properties.

To find out the number of items in a set,
check its read-only `count` property:

```swift
print("I have \(favoriteGenres.count) favorite music genres.")
// Prints "I have 3 favorite music genres."
```




Use the Boolean `isEmpty` property
as a shortcut for checking whether the `count` property is equal to `0`:

```swift
if favoriteGenres.isEmpty {
   print("As far as music goes, I'm not picky.")
} else {
   print("I have particular music preferences.")
}
// Prints "I have particular music preferences."
```




You can add a new item into a set by calling the set's `insert(_:)` method:

```swift
favoriteGenres.insert("[Tool J]")
// favoriteGenres now contains 4 items
```




You can remove an item from a set by calling the set's `remove(_:)` method,
which removes the item if it's a member of the set,
and returns the removed value,
or returns `nil` if the set didn't contain it.
Alternatively, all items in a set can be removed with its `removeAll()` method.

```swift
if let removedGenre = favoriteGenres.remove("Rock") {
   print("\(removedGenre)? I'm over it.")
} else {
   print("I never much cared for that.")
}
// Prints "Rock? I'm over it."
```




To check whether a set contains a particular item, use the `contains(_:)` method.

```swift
if favoriteGenres.contains("Funk") {
    print("I get up on the good foot.")
} else {
    print("It's too funky in here.")
}
// Prints "It's too funky in here."
```




### Iterating Over a Set

You can iterate over the values in a set with a `for`-`in` loop.

```swift
for genre in favoriteGenres {
   print("\(genre)")
}
// Classical
// [Tool J]
// Hip hop
```




For more about the `for`-`in` loop, see <doc:ControlFlow#For-In-Loops>.

Swift's `Set` type doesn't have a defined ordering.
To iterate over the values of a set in a specific order,
use the `sorted()` method,
which returns the set's elements as an array
sorted using the `<` operator.

```swift
for genre in favoriteGenres.sorted() {
   print("\(genre)")
}
// Classical
// Hip hop
// [Tool J]
```




## Performing Set Operations

You can efficiently perform fundamental set operations,
such as combining two sets together,
determining which values two sets have in common,
or determining whether two sets contain all, some, or none of the same values.

### Fundamental Set Operations

The illustration below depicts two sets---`a` and `b`---
with the results of various set operations represented by the shaded regions.

![](setVennDiagram)


- Use the `intersection(_:)` method to create a new set with only the values common to both sets.
- Use the `symmetricDifference(_:)` method to create a new set with values in either set, but not both.
- Use the `union(_:)` method to create a new set with all of the values in both sets.
- Use the `subtracting(_:)` method to create a new set with values not in the specified set.

```swift
let oddDigits: Set = [1, 3, 5, 7, 9]
let evenDigits: Set = [0, 2, 4, 6, 8]
let singleDigitPrimeNumbers: Set = [2, 3, 5, 7]

oddDigits.union(evenDigits).sorted()
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
oddDigits.intersection(evenDigits).sorted()
// []
oddDigits.subtracting(singleDigitPrimeNumbers).sorted()
// [1, 9]
oddDigits.symmetricDifference(singleDigitPrimeNumbers).sorted()
// [1, 2, 9]
```






### Set Membership and Equality

The illustration below depicts three sets---`a`, `b` and `c`---
with overlapping regions representing elements shared among sets.
Set `a` is a *superset* of set `b`,
because `a` contains all elements in `b`.
Conversely, set `b` is a *subset* of set `a`,
because all elements in `b` are also contained by `a`.
Set `b` and set `c` are *disjoint* with one another,
because they share no elements in common.

![](setEulerDiagram)


- Use the “is equal” operator (`==`) to determine whether two sets contain all of the same values.
- Use the `isSubset(of:)` method to determine whether all of the values of a set are contained in the specified set.
- Use the `isSuperset(of:)` method to determine whether a set contains all of the values in a specified set.
- Use the `isStrictSubset(of:)` or `isStrictSuperset(of:)` methods to determine whether a set is a subset or superset, but not equal to, a specified set.
- Use the `isDisjoint(with:)` method to determine whether two sets have no values in common.

```swift
let houseAnimals: Set = ["🐶", "🐱"]
let farmAnimals: Set = ["🐮", "🐔", "🐑", "🐶", "🐱"]
let cityAnimals: Set = ["🐦", "🐭"]

houseAnimals.isSubset(of: farmAnimals)
// true
farmAnimals.isSuperset(of: houseAnimals)
// true
farmAnimals.isDisjoint(with: cityAnimals)
// true
```






## Dictionaries

A *dictionary* stores associations between
keys of the same type and values of the same type
in a collection with no defined ordering.
Each value is associated with a unique *key*,
which acts as an identifier for that value within the dictionary.
Unlike items in an array, items in a dictionary don't have a specified order.
You use a dictionary when you need to look up values based on their identifier,
in much the same way that a real-world dictionary is used to look up
the definition for a particular word.

> Note: Swift's `Dictionary` type is bridged to Foundation's `NSDictionary` class.For more information about using `Dictionary` with Foundation and Cocoa,
> see [Bridging Between Dictionary and NSDictionary](https://developer.apple.com/documentation/swift/dictionary#2846239).

### Dictionary Type Shorthand Syntax

The type of a Swift dictionary is written in full as `Dictionary<Key, Value>`,
where `Key` is the type of value that can be used as a dictionary key,
and `Value` is the type of value that the dictionary stores for those keys.

> Note: A dictionary `Key` type must conform to the `Hashable` protocol,
> like a set's value type.

You can also write the type of a dictionary in shorthand form as `[Key: Value]`.
Although the two forms are functionally identical,
the shorthand form is preferred
and is used throughout this guide when referring to the type of a dictionary.

### Creating an Empty Dictionary

As with arrays,
you can create an empty `Dictionary` of a certain type by using initializer syntax:

```swift
var namesOfIntegers: [Int: String] = [:]
// namesOfIntegers is an empty [Int: String] dictionary
```




This example creates an empty dictionary of type `[Int: String]`
to store human-readable names of integer values.
Its keys are of type `Int`, and its values are of type `String`.

If the context already provides type information,
you can create an empty dictionary with an empty dictionary literal,
which is written as `[:]`
(a colon inside a pair of square brackets):

```swift
namesOfIntegers[16] = "sixteen"
// namesOfIntegers now contains 1 key-value pair
namesOfIntegers = [:]
// namesOfIntegers is once again an empty dictionary of type [Int: String]
```




### Creating a Dictionary with a Dictionary Literal

You can also initialize a dictionary with a *dictionary literal*,
which has a similar syntax to the array literal seen earlier.
A dictionary literal is a shorthand way to write
one or more key-value pairs as a `Dictionary` collection.

A *key-value pair* is a combination of a key and a value.
In a dictionary literal,
the key and value in each key-value pair are separated by a colon.
The key-value pairs are written as a list, separated by commas,
surrounded by a pair of square brackets:

```
[<#key 1#>: <#value 1#>, <#key 2#>: <#value 2#>, <#key 3#>: <#value 3#>]
```


The example below creates a dictionary to store the names of international airports.
In this dictionary, the keys are three-letter International Air Transport Association codes,
and the values are airport names:

```swift
var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```




The `airports` dictionary is declared as having a type of `[String: String]`,
which means “a `Dictionary` whose keys are of type `String`,
and whose values are also of type `String`”.

> Note: The `airports` dictionary is declared as a variable (with the `var` introducer),
> and not a constant (with the `let` introducer),
> because more airports are added to the dictionary in the examples below.

The `airports` dictionary is initialized with
a dictionary literal containing two key-value pairs.
The first pair has a key of `"YYZ"` and a value of `"Toronto Pearson"`.
The second pair has a key of `"DUB"` and a value of `"Dublin"`.

This dictionary literal contains two `String: String` pairs.
This key-value type matches the type of the `airports` variable declaration
(a dictionary with only `String` keys, and only `String` values),
and so the assignment of the dictionary literal is permitted
as a way to initialize the `airports` dictionary with two initial items.

As with arrays,
you don't have to write the type of the dictionary
if you're initializing it with a dictionary literal whose keys and values have consistent types.
The initialization of `airports` could have been written in a shorter form instead:

```swift
var airports = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```




Because all keys in the literal are of the same type as each other,
and likewise all values are of the same type as each other,
Swift can infer that `[String: String]` is
the correct type to use for the `airports` dictionary.

### Accessing and Modifying a Dictionary

You access and modify a dictionary through its methods and properties,
or by using subscript syntax.

As with an array, you find out the number of items in a `Dictionary`
by checking its read-only `count` property:

```swift
print("The airports dictionary contains \(airports.count) items.")
// Prints "The airports dictionary contains 2 items."
```




Use the Boolean `isEmpty` property
as a shortcut for checking whether the `count` property is equal to `0`:

```swift
if airports.isEmpty {
   print("The airports dictionary is empty.")
} else {
   print("The airports dictionary isn't empty.")
}
// Prints "The airports dictionary isn't empty."
```




You can add a new item to a dictionary with subscript syntax.
Use a new key of the appropriate type as the subscript index,
and assign a new value of the appropriate type:

```swift
airports["LHR"] = "London"
// the airports dictionary now contains 3 items
```




You can also use subscript syntax to change the value associated with a particular key:

```swift
airports["LHR"] = "London Heathrow"
// the value for "LHR" has been changed to "London Heathrow"
```




As an alternative to subscripting,
use a dictionary's `updateValue(_:forKey:)` method
to set or update the value for a particular key.
Like the subscript examples above, the `updateValue(_:forKey:)` method
sets a value for a key if none exists,
or updates the value if that key already exists.
Unlike a subscript, however,
the `updateValue(_:forKey:)` method returns the *old* value after performing an update.
This enables you to check whether or not an update took place.

The `updateValue(_:forKey:)` method returns an optional value
of the dictionary's value type.
For a dictionary that stores `String` values, for example,
the method returns a value of type `String?`,
or “optional `String`”.
This optional value contains the old value for that key if one existed before the update,
or `nil` if no value existed:

```swift
if let oldValue = airports.updateValue("Dublin Airport", forKey: "DUB") {
   print("The old value for DUB was \(oldValue).")
}
// Prints "The old value for DUB was Dublin."
```




You can also use subscript syntax to retrieve a value from the dictionary for a particular key.
Because it's possible to request a key for which no value exists,
a dictionary's subscript returns an optional value of the dictionary's value type.
If the dictionary contains a value for the requested key,
the subscript returns an optional value containing the existing value for that key.
Otherwise, the subscript returns `nil`:

```swift
if let airportName = airports["DUB"] {
   print("The name of the airport is \(airportName).")
} else {
   print("That airport isn't in the airports dictionary.")
}
// Prints "The name of the airport is Dublin Airport."
```




You can use subscript syntax to remove a key-value pair from a dictionary
by assigning a value of `nil` for that key:

```swift
airports["APL"] = "Apple International"
// "Apple International" isn't the real airport for APL, so delete it
airports["APL"] = nil
// APL has now been removed from the dictionary
```




Alternatively, remove a key-value pair from a dictionary
with the `removeValue(forKey:)` method.
This method removes the key-value pair if it exists
and returns the removed value,
or returns `nil` if no value existed:

```swift
if let removedValue = airports.removeValue(forKey: "DUB") {
   print("The removed airport's name is \(removedValue).")
} else {
   print("The airports dictionary doesn't contain a value for DUB.")
}
// Prints "The removed airport's name is Dublin Airport."
```




### Iterating Over a Dictionary

You can iterate over the key-value pairs in a dictionary with a `for`-`in` loop.
Each item in the dictionary is returned as a `(key, value)` tuple,
and you can decompose the tuple's members into temporary constants or variables
as part of the iteration:

```swift
for (airportCode, airportName) in airports {
   print("\(airportCode): \(airportName)")
}
// LHR: London Heathrow
// YYZ: Toronto Pearson
```




For more about the `for`-`in` loop, see <doc:ControlFlow#For-In-Loops>.

You can also retrieve an iterable collection of a dictionary's keys or values
by accessing its `keys` and `values` properties:

```swift
for airportCode in airports.keys {
   print("Airport code: \(airportCode)")
}
// Airport code: LHR
// Airport code: YYZ

for airportName in airports.values {
   print("Airport name: \(airportName)")
}
// Airport name: London Heathrow
// Airport name: Toronto Pearson
```




If you need to use a dictionary's keys or values
with an API that takes an `Array` instance, initialize a new array
with the `keys` or `values` property:

```swift
let airportCodes = [String](airports.keys)
// airportCodes is ["LHR", "YYZ"]

let airportNames = [String](airports.values)
// airportNames is ["London Heathrow", "Toronto Pearson"]
```




Swift's `Dictionary` type doesn't have a defined ordering.
To iterate over the keys or values of a dictionary in a specific order,
use the `sorted()` method on its `keys` or `values` property.



