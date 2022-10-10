

# Extensões

*Extensões* adicionam novas funcionalidades a uma classe, estrutura, enumeração ou tipo de protocolo existente. Isso inclui a capacidade de estender tipos para os quais você não tem acesso ao código-fonte original (conhecido como *modelagem retroativa*). As extensões são semelhantes às categorias em Objective-C. (Ao contrário das categorias de Objective-C, as extensões Swift não têm nomes.)

Extensões no Swift podem:

- Adicionar propriedades de instância computadas e propriedades de tipo computado
- Definir métodos de instância e métodos de tipo
- Fornecer novos inicializadores
- Definir subscritos
- Definir e usar novos tipos aninhados
- Deixar um tipo existente em conformidade com um protocolo

Em Swift, você pode, inclusive, estender um protocolo para fornecer implementações de seus requisitos
ou adicionar funcionalidades extras que os tipos em conformidade podem aproveitar. Para mais detalhes, consulte <doc:Protocols#Protocol-Extensions>.

> Nota: As extensões podem adicionar novas funcionalidades a um tipo, mas eles não podem substituir a funcionalidade existente.


## Sintaxe de Extensão

Declare extensões com a palavra-chave `extension`:

```swift
extension SomeType {
   // nova funcionalidade para adicionar a 'SomeType' vai aqui
}
```

Uma extensão pode estender um tipo existente para que ele adote um ou mais protocolos. Para adicionar conformidade de protocolo, você escreve os nomes dos protocolos da mesma maneira que você os escreve para uma classe ou estrutura:

```swift
extension SomeType: SomeProtocol, AnotherProtocol {
   // implementação de requisitos do protocolo vai aqui
}
```

A adição de conformidade de protocolo dessa maneira é descrita em <doc:Protocols#Adding-Protocol-Conformance-with-an-Extension>.

Uma extensão pode ser usada para estender um tipo genérico existente, como descrito em <doc:Generics#Extending-a-Generic-Type>.
Você também pode estender um tipo genérico para adicionar funcionalidade condicionalmente, conforme descrito em <doc:Generics#Extensions-with-a-Generic-Where-Clause>.

> Nota: Se você definir uma extensão para adicionar uma nova funcionalidade a um tipo existente, a nova funcionalidade estará disponível em todas as instâncias existentes desse tipo, mesmo que tenham sido criadas antes da definição da extensão.

## Computed Properties

Extensions can add computed instance properties and computed type properties to existing types.
This example adds five computed instance properties to Swift's built-in `Double` type,
to provide basic support for working with distance units:

```swift
extension Double {
   var km: Double { return self * 1_000.0 }
   var m: Double { return self }
   var cm: Double { return self / 100.0 }
   var mm: Double { return self / 1_000.0 }
   var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// Prints "One inch is 0.0254 meters"
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// Prints "Three feet is 0.914399970739201 meters"
```




These computed properties express that a `Double` value
should be considered as a certain unit of length.
Although they're implemented as computed properties,
the names of these properties can be appended to
a floating-point literal value with dot syntax,
as a way to use that literal value to perform distance conversions.

In this example, a `Double` value of `1.0` is considered to represent “one meter”.
This is why the `m` computed property returns `self` ---
the expression `1.m` is considered to calculate a `Double` value of `1.0`.

Other units require some conversion to be expressed as a value measured in meters.
One kilometer is the same as 1,000 meters,
so the `km` computed property multiplies the value by `1_000.00`
to convert into a number expressed in meters.
Similarly, there are 3.28084 feet in a meter,
and so the `ft` computed property divides the underlying `Double` value
by `3.28084`, to convert it from feet to meters.

These properties are read-only computed properties,
and so they're expressed without the `get` keyword, for brevity.
Their return value is of type `Double`,
and can be used within mathematical calculations wherever a `Double` is accepted:

```swift
let aMarathon = 42.km + 195.m
print("A marathon is \(aMarathon) meters long")
// Prints "A marathon is 42195.0 meters long"
```




> Note: Extensions can add new computed properties, but they can't add stored properties,
> or add property observers to existing properties.





## Inicializadores

Extensões podem adicionar novos inicializadores a tipos existentes.
Isso permite você extender outros tipos para aceitar
seus próprios tipos personalizados como parâmetros de inicialização,
ou então para providenciar opções adicionais de inicialização
que antes não eram parte da implementação original do tipo. 

Extensões podem adicionar novos inicializadores de conveniência a uma classe,
mas eles não podem adicionar nosos inicializadores designados ou desinicializadores a uma classe.
Inicializadores designados e desinicializadores
devem sempre ser providenciados pela implementação original da classe. 

Se você usa uma extensão para adicionar um inicializador a um tipo de valor que 
provê valores padrões para todas as suas propriedades armazenadas 
e não define nenhum inicializador customizado,
você pode chamar o inicializador padrão e inicializador _memberwise_  para o
tipo de valor de dentro de seu inicializador da extensão. 

Este não seria o caso se você tivesse escrito o inicializador 
como parte da implementação original do tipo de valor,
como descrito em <doc:Initialization#Initializer-Delegation-for-Value-Types>.

Se você usa uma extensão para adicionar um inicializador a uma estrutura
que foi declarada em outro módulo,
o novo inicializador não pode acessar `self` até
ele chamar um inicializador do módulo definidor. 

O exemplo abaixo define uma estrutura `Rect` customizada para representar um retângulo geométrico.
O exemplo também define duas estrutura de suporte chamadas `Size` e `Point`,
Ambos provêm valores padrões de `0.0` para todas as suas propriedades: 


```swift
struct Size {
   var width = 0.0, height = 0.0
}
struct Point {
   var x = 0.0, y = 0.0
}
struct Rect {
   var origin = Point()
   var size = Size()
}
```

Por causa da estrutura `Rect` prover valores padrões para todas as suas propriedades,
ela recebe um inicializador padrão e um inicializador _memberwise_ automaticamente,
como descrito em <doc:Initialization#Default-Initializers>.
Estes inicializadores podem ser usados para criar novas instâncias `Rect`:


```swift
let defaultRect = Rect()
let memberwiseRect = Rect(origin: Point(x: 2.0, y: 2.0),
   size: Size(width: 5.0, height: 5.0))
```


Você pode extender a estrutura `Rect` para prover um inicializador adicional
que recebe um tamanho e ponto central específico:

```swift
extension Rect {
   init(center: Point, size: Size) {
      let originX = center.x - (size.width / 2)
      let originY = center.y - (size.height / 2)
      self.init(origin: Point(x: originX, y: originY), size: size)
   }
}
```



Esse novo inicializador inicia calculando um ponto de origem apropriados baseado 
no ponto `center` e valor `size` dados. 
O inicializador então chama o inicializador _memberwise_ automático da estrutura `init(origin:size:)`,
que armazena a nova origem e valor do tamanho nas propriedades adequadas:

```swift
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
   size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```


> Nome: se você provê um novo inicializardor com uma extensão,
> você ainda é responsável por certificar-se que cada instância foi completamente inicializada
> uma vez que o inicializador tenha completado. 

## Methods

Extensions can add new instance methods and type methods to existing types.
The following example adds a new instance method called `repetitions` to the `Int` type:

```swift
extension Int {
   func repetitions(task: () -> Void) {
      for _ in 0..<self {
         task()
      }
   }
}
```




The `repetitions(task:)` method takes a single argument of type `() -> Void`,
which indicates a function that has no parameters and doesn't return a value.

After defining this extension,
you can call the `repetitions(task:)` method on any integer
to perform a task that many number of times:

```swift
3.repetitions {
   print("Hello!")
}
// Hello!
// Hello!
// Hello!
```




### Mutating Instance Methods

Instance methods added with an extension can also modify (or *mutate*) the instance itself.
Structure and enumeration methods that modify `self` or its properties
must mark the instance method as `mutating`,
just like mutating methods from an original implementation.

The example below adds a new mutating method called `square` to Swift's `Int` type,
which squares the original value:

```swift
extension Int {
   mutating func square() {
      self = self * self
   }
}
var someInt = 3
someInt.square()
// someInt is now 9
```




## Subscripts

Extensions can add new subscripts to an existing type.
This example adds an integer subscript to Swift's built-in `Int` type.
This subscript `[n]` returns the decimal digit `n` places in
from the right of the number:

- `123456789[0]` returns `9`
- `123456789[1]` returns `8`

…and so on:

```swift
extension Int {
   subscript(digitIndex: Int) -> Int {
      var decimalBase = 1
      for _ in 0..<digitIndex {
         decimalBase *= 10
      }
      return (self / decimalBase) % 10
   }
}
746381295[0]
// returns 5
746381295[1]
// returns 9
746381295[2]
// returns 2
746381295[8]
// returns 7
```








If the `Int` value doesn't have enough digits for the requested index,
the subscript implementation returns `0`,
as if the number had been padded with zeros to the left:

```swift
746381295[9]
// returns 0, as if you had requested:
0746381295[9]
```








## Nested Types

Extensions can add new nested types to existing classes, structures, and enumerations:

```swift
extension Int {
   enum Kind {
      case negative, zero, positive
   }
   var kind: Kind {
      switch self {
         case 0:
            return .zero
         case let x where x > 0:
            return .positive
         default:
            return .negative
      }
   }
}
```




This example adds a new nested enumeration to `Int`.
This enumeration, called `Kind`,
expresses the kind of number that a particular integer represents.
Specifically, it expresses whether the number is
negative, zero, or positive.

This example also adds a new computed instance property to `Int`,
called `kind`,
which returns the appropriate `Kind` enumeration case for that integer.

The nested enumeration can now be used with any `Int` value:

```swift
func printIntegerKinds(_ numbers: [Int]) {
   for number in numbers {
      switch number.kind {
         case .negative:
            print("- ", terminator: "")
         case .zero:
            print("0 ", terminator: "")
         case .positive:
            print("+ ", terminator: "")
      }
   }
   print("")
}
printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
// Prints "+ + - 0 - 0 + "
```






This function, `printIntegerKinds(_:)`,
takes an input array of `Int` values and iterates over those values in turn.
For each integer in the array,
the function considers the `kind` computed property for that integer,
and prints an appropriate description.

> Note: `number.kind` is already known to be of type `Int.Kind`.
> Because of this, all of the `Int.Kind` case values
> can be written in shorthand form inside the `switch` statement,
> such as `.negative` rather than `Int.Kind.negative`.



