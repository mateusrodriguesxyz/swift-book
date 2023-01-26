

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

Para uma comparação mais detalhada, consulte [Choosing Between Structures and Classes](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes).

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

## Estruturas e Enumerações São Tipos de Valor

Um *tipo de valor* é um tipo cujo valor é *copiado* quando é atribuído a uma variável ou constante, ou quando é passado para uma função.

Na verdade, você usou tipos de valor extensivamente nos capítulos anteriores. Na verdade, todos os tipos básicos em Swift --- inteiros, números de ponto flutuante, booleanos, strings, arrays e dicionários --- são tipos de valor e são implementados como estruturas.

Todas as estruturas e enumerações são tipos de valor em Swift. Isso significa que qualquer instância de estrutura e enumeração que você criar --- e quaisquer tipos de valor que eles tenham como propriedades --- são sempre copiados quando são passados em seu código.

> Nota: as coleções definidas pela biblioteca padrão, como arrays, dicionários e strings, usam uma otimização para reduzir o custo de desempenho ao fazer cópias. Em vez de fazer uma cópia imediatamente, essas coleções compartilham a memória onde os elementos são armazenados entre a instância original e quaisquer cópias. Se uma das cópias da coleção for modificada, os elementos são copiados imediatamente antes da modificação. O comportamento que você vê em seu código é sempre como se uma cópia ocorresse imediatamente.

Considere este exemplo, que usa a estrutura `Resolution` do exemplo anterior:

```swift
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd
```

Este exemplo declara uma constante chamada `hd` e a define como uma instância `Resolution` inicializada com a largura e a altura do vídeo full HD (1920 pixels de largura por 1080 pixels de altura).

Em seguida, é declarada uma variável chamada `cinema` e definida com o valor atual de `hd`. Como `Resolution` é uma estrutura, uma *cópia* da instância existente é feita e esta nova cópia é atribuída a `cinema`. Mesmo que `hd` e `cinema` agora tenham a mesma largura e altura, eles são duas instâncias completamente diferentes nos bastidores.

Em seguida, a propriedade `width` de `cinema` é alterada para ser a largura do padrão 2K ligeiramente mais amplo usado para projeção de cinema digital (2048 pixels de largura e 1080 pixels de altura):

```swift
cinema.width = 2048
```

Verificar a propriedade `width` de `cinema` mostra que ela realmente mudou para `2048`:

```swift
print("cinema is now \(cinema.width) pixels wide")
// Imprime "cinema is now 2048 pixels wide"
```

No entanto, a propriedade `width` da instância `hd` original ainda tem o valor antigo de `1920`:

```swift
print("hd is still \(hd.width) pixels wide")
// Imprime "hd is still 1920 pixels wide"
```

Quando `cinema` recebeu o valor atual de `hd`, os *valores* armazenados em `hd` foram copiados para a nova instância `cinema`. O resultado final foram duas instâncias completamente separadas que continham os mesmos valores numéricos. No entanto, por serem instâncias separadas, definir a largura de `cinema` para `2048` não afeta a largura armazenada em `hd`, conforme mostrado na figura abaixo:

![](sharedStateStruct)


O mesmo comportamento se aplica a enumerações:

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
// Imprime "The current direction is north"
// Imprime "The remembered direction is west"
```

Quando `rememberedDirection` recebe o valor de `currentDirection`, ele é definido como uma cópia desse valor. Alterar o valor de `currentDirection` posteriormente não afeta a cópia do valor original que foi armazenado em `rememberedDirection`.

## Classes são Tipos de Referência

Ao contrário dos tipos de valor, os *tipos de referência* *não* são copiados quando são atribuídos a uma variável ou constante, ou quando são passados para uma função. Em vez de uma cópia, é usada uma referência à mesma instância existente.

Aqui está um exemplo, usando a classe `VideoMode` definida acima:

```swift
let tenEighty = VideoMode()
tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0
```

Esse exemplo declara uma nova constante chamada `tenEighty` e a define para se referir a uma nova instância da classe `VideoMode`. A propriedade `resolution` recebe uma cópia da instância `hd` de `1920` por `1080` definida anteriormente. A propriedade `interlaced` é configurada para `true`, `name` é definido como `"1080i"` e `frameRate` é definida como `25,0` quadros por segundo.

Em seguida, `tenEighty` é atribuído a uma nova constante, chamada `alsoTenEighty`, e a taxa de quadros de `alsoTenEighty` é modificada:

```swift
let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0
```

Como as classes são tipos de referência, `tenEighty` e `alsoTenEighty` na verdade se referem à *mesma* instância `VideoMode`. Efetivamente, eles são apenas dois nomes diferentes para a mesma instância, conforme mostrado na figura abaixo:

![](sharedStateClass)

Verificar a propriedade `frameRate` de `tenEighty` mostra que ela retorna corretamente a nova taxa de quadros de `30.0` da instância `VideoMode` subjacente:

```swift
print("The frameRate property of tenEighty is now \(tenEighty.frameRate)")
// Imprime "The frameRate property of tenEighty is now 30.0"
```


Esse exemplo também mostra como os tipos de referência podem ser mais difíceis de raciocinar. Se `tenEighty` e `alsoTenEighty` estiverem muito distantes no código do seu programa, pode ser difícil encontrar todas as maneiras pelas quais o modo de vídeo é alterado. Onde quer que você use `tenEighty`, você também deve pensar no código que usa `alsoTenEighty`, e vice versa. Em contraste, os tipos de valor são mais fáceis de raciocinar porque todo o código que interage com o mesmo valor está próximo em seus arquivos de origem.

Note que `tenEighty` e `alsoTenEighty` são declarados como *constantes*, em vez de variáveis. No entanto, você ainda pode alterar `tenEighty.frameRate` e `alsoTenEighty.frameRate` porque os valores das constantes `tenEighty` e `alsoTenEighty` não mudam. `tenEighty` e `alsoTenEighty` não “armazenam” a instância `VideoMode` --- em vez disso, ambos *referem-se* a uma instância `VideoMode` nos bastidores. É a propriedade `frameRate` do `VideoMode` subjacente que foi alterada, não os valores das referências constantes a esse `VideoMode`.


### Operadores de Identidade

Como as classes são tipos de referência, é possível que várias constantes e variáveis se refiram à mesma instância única de uma classe. (O mesmo não é verdade para estruturas e enumerações, porque elas sempre são copiadas quando são atribuídas a uma constante ou variável ou passadas para uma função.)


Às vezes pode ser útil descobrir se duas constantes ou variáveis se referem exatamente à mesma instância de uma classe. Para permitir isso, o Swift fornece dois operadores de identidade:

- Idêntico a (`===`)
- Não idêntico a (`!==`)

Use esses operadores para verificar se duas constantes ou variáveis se referem à mesma instância única:

```swift
if tenEighty === alsoTenEighty {
   print("tenEighty and alsoTenEighty refer to the same VideoMode instance.")
}
// Imprime "tenEighty and alsoTenEighty refer to the same VideoMode instance."
```

Note que *idêntico a* (representado por três sinais de igual, ou `===`) não significa a mesma coisa que *igual a* (representado por dois sinais de igual, ou `==`). *Idêntico a* significa que duas constantes ou variáveis do tipo de classe referem-se exatamente à mesma instância de classe. *Igual a* significa que duas instâncias são consideradas iguais ou equivalentes em valor, para algum significado apropriado de *igual*, conforme definido pelo designer do tipo.

Ao definir suas próprias estruturas e classes personalizadas, é sua responsabilidade decidir o que se qualifica como duas instâncias iguais. O processo de definir suas próprias implementações dos operadores `==` e `!=` é descrito em <doc:AdvancedOperators#Equivalence-Operators>.


### Ponteiros

Se você tiver experiência com C, C++ ou Objective-C, talvez saiba que essas linguagens usam *ponteiros* para se referir a endereços na memória. Uma constante ou variável Swift que se refere a uma instância de algum tipo de referência é semelhante a um ponteiro em C, mas não é um ponteiro direto para um endereço na memória e não exige que você escreva um asterisco (`*`) para indicar que você está criando uma referência. Em vez disso, essas referências são definidas como qualquer outra constante ou variável no Swift. A biblioteca padrão fornece tipos de ponteiro e buffer que você pode usar se precisar interagir diretamente com os ponteiros --- consulte [Manual Memory Management](https://developer.apple.com/documentation/swift/swift_standard_library/manual_memory_management).









