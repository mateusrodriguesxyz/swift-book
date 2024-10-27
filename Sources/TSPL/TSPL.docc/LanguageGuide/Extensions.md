

# Extensões

Adicione funcionalidade a um tipo existente.

*Extensões* adicionam novas funcionalidades a 
uma classe, estrutura, enumeração ou tipo de protocolo existente.
Isso inclui a capacidade de estender tipos
para os quais você não tem acesso ao código-fonte original
(conhecido como **modelagem retroativa**).
As extensões são semelhantes às categorias em Objective-C. 
(Diferente das categorias de Objective-C, as extensões em Swift não têm nomes.)

Extensões em Swift podem:

- Adicionar propriedades computadas de instância e propriedades computados de tipo 
- Definir métodos de instância e métodos de tipo
- Fornecer novos inicializadores
- Definir subscritos
- Definir e usar novos tipos aninhados
- Deixar um tipo existente em conformidade com um protocolo

Em Swift, você pode, inclusive, estender um protocolo para fornecer implementações de seus requisitos
ou adicionar funcionalidades extras que os tipos em conformidade podem aproveitar.
Para mais detalhes, consulte <doc:Protocols#Protocol-Extensions>.

> Nota: As extensões podem adicionar novas funcionalidades a um tipo, 
> mas eles não podem substituir a funcionalidade existente.


## Sintaxe de Extensão

Declare extensões com a palavra-chave `extension`:

```swift
extension SomeType {
   // nova funcionalidade para adicionar a 'SomeType' vai aqui
}
```

Uma extensão pode estender um tipo existente para que ele adote um ou mais protocolos. 
Para adicionar conformidade de protocolo, 
você escreve os nomes dos protocolos da mesma maneira que 
você os escreve para uma classe ou estrutura:

```swift
extension SomeType: SomeProtocol, AnotherProtocol {
   // implementação de requisitos do protocolo vai aqui
}
```

A adição de conformidade de protocolo dessa maneira 
é descrita em <doc:Protocols#Adding-Protocol-Conformance-with-an-Extension>.

Uma extensão pode ser usada para estender um tipo genérico existente,
como descrito em <doc:Generics#Extending-a-Generic-Type>.
Você também pode estender um tipo genérico para adicionar funcionalidade condicionalmente, 
conforme descrito em <doc:Generics#Extensions-with-a-Generic-Where-Clause>.

> Nota: Se você definir uma extensão para adicionar uma nova funcionalidade a um tipo existente, 
> a nova funcionalidade estará disponível em todas as instâncias existentes desse tipo,
> mesmo que tenham sido criadas antes da definição da extensão.

## Propriedades Computadas

Extensões podem adicionar propriedades computadas de instância e propriedades computadas de tipo a tipos existentes. 
Este exemplo adiciona cinco propriedades computadas de instância ao tipo primitivo `_Double_`,
para fornecer suporte básico para trabalhar com unidades de distância:

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
// Imprime "One inch is 0.0254 meters"
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// Imprime "Three feet is 0.914399970739201 meters"
```

Essas propriedades computadas expressam que o valor de um `_Double_`
deve ser considerado como uma certa unidade de comprimento.
Apesar de serem implementadas como propriedades computadas,
os nomes destas propriedades podem ser anexados a um
literal de ponto flutuante com sintaxe de ponto,
como uma forma de usar aquele valor literal para realizar conversões de distância.

Neste exemplo, um `Double` de valor `1.0` é coonsiderado para representar "um metro".
E é por isso que a propriedade computada `m` retorna `self` ---
a expressão `1.m` é considerada para calcular um `Double` de valor `1.0`.

Outras unidades requerem alguma conversão para serem expressas como um valor medido em metros. 
Um quilometro é mesma coisa de 1.000 metros,
então a propriedade computada `km` multiplica o valor por `1_000.00`
para convertê-lo em um numero expresso em metros. 
Similarmente, temos 3,28084 pés em metros,
e então a propriedade computada `ft` divide o valor `Double` subjacente
por `3.28084` para convertê-lo de pés para metros. 

Essas propriedades são propriedades computadas apenas para leitura,
então elas são expressadas sem a palavra-chave `_get_`, para fins de abreviação. 
Seu retorno é do tipo `_Double_` e pode ser
usado dentro de cálculos matemáticos sempre que um `Double` é aceito:

```swift
let aMarathon = 42.km + 195.m
print("A marathon is \(aMarathon) meters long")
// Imprime "A marathon is 42195.0 meters long"
```

> Nota: Extensões podem adicionar novas propriedades computadas, mas não podem adicionar 
> propriedades armazenadas, ou adicionar observadores em propriedades existentes.


## Inicializadores

Extensões podem adicionar novos inicializadores a tipos existentes.
Isso permite você estender outros tipos para aceitar
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


Você pode estender a estrutura `Rect` para prover um inicializador adicional
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



