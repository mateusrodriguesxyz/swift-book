

# Estruturas e Classes

*Estruturas* e *classes* são construções flexíveis de propósito geral que se tornam os blocos de construção do código do seu programa. Você define propriedades e métodos para adicionar funcionalidade às suas estruturas e classes usando a mesma sintaxe usada para definir constantes, variáveis e funções.

Ao contrário de outras linguagens de programação, Swift não exige que você crie arquivos separados de interface e implementação para estruturas e classes personalizadas. Em Swift, você define uma estrutura ou classe em um único arquivo, e a interface externa para essa classe ou estrutura é automaticamente disponibilizada para outro código usar.

> Nota: Uma instância de uma classe é tradicionalmente conhecida como um *objeto*. No entanto, as estruturas e classes do Swift são muito mais próximas em funcionalidade do que em outras linguagens, e muito deste capítulo descreve a funcionalidade que se aplica a instâncias de um *tipo*, seja uma classe ou uma estrutura. Por causa disso, o termo mais geral *instância* é usado.

## Comparando Estruturas e Classes

Estruturas e classes em Swift têm muitas coisas em comum. Ambas podem:

- Definir propriedades para armazenar valores
- Definir métodos para fornecer funcionalidade
- Definir subscritos para fornecer acesso a seus valores usando a sintaxe de subscritos
- Definir inicializadores para configurar seu estado inicial
- Ser estendido para expandir sua funcionalidade além de uma implementação padrão
- Conformar com protocolos para fornecer funcionalidade padrão de um determinado tipo

Para mais informações, veja <doc:Properties>, <doc:Methods>, <doc:Subscripts>, <doc:Initialization>, <doc:Extensions>, e <doc:Protocols>.

As classes têm recursos adicionais que as estruturas não possuem:

- A herança permite que uma classe herde as características de outra.
- A conversão de tipo permite que você verifique e interprete o tipo de uma instância de classe em tempo de execução.
- Desinicializadores permitem que uma instância de uma classe libere todos os recursos atribuídos a ela.
- A contagem de referências permite mais de uma referência a uma instância de classe.

Para mais informações, veja <doc:Inheritance>, <doc:TypeCasting>, <doc:Deinitialization>, e <doc:AutomaticReferenceCounting>.

Os recursos adicionais que as classes suportam vêm com o custo de maior complexidade. Como diretriz geral, prefira estruturas porque são mais fáceis de raciocinar sobre elas e use classes quando forem apropriadas ou necessárias. Na prática, isso significa que a maioria dos tipos de dados personalizados que você definir serão estruturas e enumerações.

Para uma comparação mais detalhada, consulte [Choosing Between Structures and Classes (https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes).

> Nota: Classes e atores compartilham muitas das mesmas características e comportamentos. Para obter informações sobre atores, consulte <doc:Concurrency>.

### Sintaxe de Definição

Estruturas e classes têm uma sintaxe de definição semelhante. Você introduz estruturas com a palavra-chave `struct` e classes com a palavra-chave `class`. Ambas colocam toda a sua definição dentro de um par de chaves:

```swift
struct SomeStructure {
   // definição da estrutura vai aqui
}
class SomeClass {
   // definição de classe vai aqui
}
```

> Nota: Sempre que você define uma nova estrutura ou classe, você define um novo tipo de Swift. Dê nomes aos tipos `UpperCamelCase` (como `SomeStructure` e `SomeClass` aqui) para corresponder à capitalização dos tipos Swift padrão (como `String`, `Int` e `Bool`). Dê nomes às propriedades e métodos `lowerCamelCase` (como `frameRate` e `incrementCount`) para diferenciá-los dos nomes de tipo.

Aqui está um exemplo de uma definição de estrutura e uma definição de classe:

```swift
struct Resolution {
   var width = 0
   var height = 0
}
class VideoMode {
   var resolution = Resolution()
   var interlaced = false
   var frameRate = 0.0
   var name: String?
}
```

O exemplo acima define uma nova estrutura chamada `Resolution`, para descrever uma resolução de exibição baseada em pixels. Esta estrutura tem duas propriedades armazenadas chamadas `width` e `height`. Propriedades armazenadas são constantes ou variáveis agrupadas e armazenadas como parte da estrutura ou classe. Essas duas propriedades são inferidas como sendo do tipo `Int` definindo-as como um valor inteiro inicial de `0`.

O exemplo acima também define uma nova classe chamada `VideoMode`, para descrever um modo de vídeo específico para exibição de vídeo. Essa classe tem quatro propriedades armazenadas variáveis. A primeira, `resolution`, é inicializada com uma nova instância da estrutura `Resolution`, que infere um tipo de propriedade de `Resolution`. Para as outras três propriedades, as novas instâncias `VideoMode` serão inicializadas com uma configuração `interlaced` de `false` (que significa “vídeo não entrelaçado”), uma propriedade frameRate` de `0,0` e um valor `String` opcional chamado  `name`. A propriedade `name` recebe automaticamente um valor padrão de `nil`, ou “nenhum valor `name`”, porque é de um tipo opcional.

### Instâncias de Estruturas e Classes

A definição da estrutura `Resolution` e a definição da classe `VideoMode` descrevem apenas como será a aparência de um instancia do tipo `Resolution` ou `VideoMode`. Elas próprias não descrevem uma resolução específica ou modo de vídeo. Para fazer isso, você precisa criar uma instância da estrutura ou classe.

A sintaxe para criar instâncias é muito semelhante para estruturas e classes:

```swift
let someResolution = Resolution()
let someVideoMode = VideoMode()
```

Estruturas e classes usam sintaxe inicializadora para novas instâncias. A forma mais simples de sintaxe do inicializador usa o nome do tipo da classe ou estrutura
seguido por parênteses vazios, como `Resolution()` ou `VideoMode()`. Isso cria uma nova instância da classe ou estrutura, com todas as propriedades inicializadas com seus valores padrão. A inicialização de classe e estrutura é descrita com mais detalhes em <doc:Initialization>.


### Acessando Propriedades

Você pode acessar as propriedades de uma instância usando a *sintaxe de ponto*. Na sintaxe de ponto, você escreve o nome da propriedade imediatamente após o nome da instância, separado por um ponto (`.`), sem espaços:

```swift
print("The width of someResolution is \(someResolution.width)")
// Imprime "The width of someResolution is 0"
```

Neste exemplo, `someResolution.width` refere-se à propriedade `width` de `someResolution` e retorna seu valor inicial padrão de `0`.

Você pode acessar as subpropriedades, como a propriedade `width` na propriedade `resolution` de um `VideoMode`:

```swift
print("The width of someVideoMode is \(someVideoMode.resolution.width)")
// Imprime "The width of someVideoMode is 0"
```

Você também pode usar a sintaxe de ponto para atribuir um novo valor a uma propriedade variável:

```swift
someVideoMode.resolution.width = 1280
print("The width of someVideoMode is now \(someVideoMode.resolution.width)")
// Imprime "The width of someVideoMode is now 1280"
```


### Inicializadores de Membros para Tipos de Estrutura

Todas as estruturas têm um *inicializador de membros* gerado automaticamente, que você pode usar para inicializar as propriedades de membro de novas instâncias de estrutura. Os valores iniciais para as propriedades da nova instância podem ser passados para o inicializador de membro por nome:

```swift
let vga = Resolution(width: 640, height: 480)
```

Ao contrário das estruturas, as instâncias de classe não recebem um inicializador de membros padrão. Os inicializadores são descritos com mais detalhes em <doc:Initialization>.

## Structures and Enumerations Are Value Types

A *value type* is a type whose value is *copied*
when it's assigned to a variable or constant,
or when it's passed to a function.



You've actually been using value types extensively throughout the previous chapters.
In fact, all of the basic types in Swift ---
integers, floating-point numbers, Booleans, strings, arrays and dictionaries ---
are value types, and are implemented as structures behind the scenes.

All structures and enumerations are value types in Swift.
This means that any structure and enumeration instances you create ---
and any value types they have as properties ---
are always copied when they're passed around in your code.

> Note: Collections defined by the standard library
> like arrays, dictionaries, and strings
> use an optimization to reduce the performance cost of copying.
> Instead of making a copy immediately,
> these collections share the memory where the elements are stored
> between the original instance and any copies.
> If one of the copies of the collection is modified,
> the elements are copied just before the modification.
> The behavior you see in your code
> is always as if a copy took place immediately.

Consider this example, which uses the `Resolution` structure from the previous example:

```swift
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd
```




This example declares a constant called `hd`
and sets it to a `Resolution` instance initialized with
the width and height of full HD video
(1920 pixels wide by 1080 pixels high).

It then declares a variable called `cinema`
and sets it to the current value of `hd`.
Because `Resolution` is a structure,
a *copy* of the existing instance is made,
and this new copy is assigned to `cinema`.
Even though `hd` and `cinema` now have the same width and height,
they're two completely different instances behind the scenes.

Next, the `width` property of `cinema` is amended to be
the width of the slightly wider 2K standard used for digital cinema projection
(2048 pixels wide and 1080 pixels high):

```swift
cinema.width = 2048
```




Checking the `width` property of `cinema`
shows that it has indeed changed to be `2048`:

```swift
print("cinema is now \(cinema.width) pixels wide")
// Prints "cinema is now 2048 pixels wide"
```




However, the `width` property of the original `hd` instance
still has the old value of `1920`:

```swift
print("hd is still \(hd.width) pixels wide")
// Prints "hd is still 1920 pixels wide"
```




When `cinema` was given the current value of `hd`,
the *values* stored in `hd` were copied into the new `cinema` instance.
The end result was two completely separate instances
that contained the same numeric values.
However, because they're separate instances,
setting the width of `cinema` to `2048`
doesn't affect the width stored in `hd`,
as shown in the figure below:

![](sharedStateStruct)


The same behavior applies to enumerations:

```swift
enum CompassPoint {
   case north, south, east, west
   mutating func turnNorth() {
      self = .north
   }
}
var currentDirection = CompassPoint.west
let rememberedDirection = currentDirection
currentDirection.turnNorth()

print("The current direction is \(currentDirection)")
print("The remembered direction is \(rememberedDirection)")
// Prints "The current direction is north"
// Prints "The remembered direction is west"
```




When `rememberedDirection` is assigned the value of `currentDirection`,
it's actually set to a copy of that value.
Changing the value of `currentDirection` thereafter doesn't affect
the copy of the original value that was stored in `rememberedDirection`.



## Classes Are Reference Types

Unlike value types, *reference types* are *not* copied
when they're assigned to a variable or constant,
or when they're passed to a function.
Rather than a copy, a reference to the same existing instance is used.

Here's an example, using the `VideoMode` class defined above:

```swift
let tenEighty = VideoMode()
tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0
```




This example declares a new constant called `tenEighty`
and sets it to refer to a new instance of the `VideoMode` class.
The video mode is assigned a copy of the HD resolution of `1920` by `1080` from before.
It's set to be interlaced,
its name is set to `"1080i"`,
and its frame rate is set to `25.0` frames per second.

Next, `tenEighty` is assigned to a new constant, called `alsoTenEighty`,
and the frame rate of `alsoTenEighty` is modified:

```swift
let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0
```




Because classes are reference types,
`tenEighty` and `alsoTenEighty` actually both refer to the *same* `VideoMode` instance.
Effectively, they're just two different names for the same single instance,
as shown in the figure below:

![](sharedStateClass)


Checking the `frameRate` property of `tenEighty`
shows that it correctly reports the new frame rate of `30.0`
from the underlying `VideoMode` instance:

```swift
print("The frameRate property of tenEighty is now \(tenEighty.frameRate)")
// Prints "The frameRate property of tenEighty is now 30.0"
```




This example also shows how reference types can be harder to reason about.
If `tenEighty` and `alsoTenEighty` were far apart in your program's code,
it could be difficult to find all the ways that the video mode is changed.
Wherever you use `tenEighty`,
you also have to think about the code that uses `alsoTenEighty`,
and vice versa.
In contrast, value types are easier to reason about
because all of the code that interacts with the same value
is close together in your source files.

Note that `tenEighty` and `alsoTenEighty` are declared as *constants*,
rather than variables.
However, you can still change `tenEighty.frameRate` and `alsoTenEighty.frameRate` because
the values of the `tenEighty` and `alsoTenEighty` constants themselves don't actually change.
`tenEighty` and `alsoTenEighty` themselves don't “store” the `VideoMode` instance ---
instead, they both *refer* to a `VideoMode` instance behind the scenes.
It's the `frameRate` property of the underlying `VideoMode` that's changed,
not the values of the constant references to that `VideoMode`.





### Identity Operators

Because classes are reference types,
it's possible for multiple constants and variables to refer to
the same single instance of a class behind the scenes.
(The same isn't true for structures and enumerations,
because they're always copied when they're assigned to a constant or variable,
or passed to a function.)





It can sometimes be useful to find out whether two constants or variables refer to
exactly the same instance of a class.
To enable this, Swift provides two identity operators:

- Identical to (`===`)
- Not identical to (`!==`)

Use these operators to check whether two constants or variables refer to the same single instance:

```swift
if tenEighty === alsoTenEighty {
   print("tenEighty and alsoTenEighty refer to the same VideoMode instance.")
}
// Prints "tenEighty and alsoTenEighty refer to the same VideoMode instance."
```




Note that *identical to* (represented by three equals signs, or `===`)
doesn't mean the same thing as *equal to* (represented by two equals signs, or `==`).
*Identical to* means that
two constants or variables of class type refer to exactly the same class instance.
*Equal to* means that
two instances are considered equal or equivalent in value,
for some appropriate meaning of *equal*, as defined by the type's designer.

When you define your own custom structures and classes,
it's your responsibility to decide what qualifies as two instances being equal.
The process of defining your own implementations of the `==` and `!=` operators
is described in <doc:AdvancedOperators#Equivalence-Operators>.







### Pointers

If you have experience with C, C++, or Objective-C,
you may know that these languages use *pointers* to refer to addresses in memory.
A Swift constant or variable that refers to an instance of some reference type
is similar to a pointer in C,
but isn't a direct pointer to an address in memory,
and doesn't require you to write an asterisk (`*`)
to indicate that you are creating a reference.
Instead, these references are defined like any other constant or variable in Swift.
The standard library provides pointer and buffer types
that you can use if you need to interact with pointers directly ---
see [Manual Memory Management](https://developer.apple.com/documentation/swift/swift_standard_library/manual_memory_management).









