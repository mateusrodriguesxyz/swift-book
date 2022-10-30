

# Initialization

*Initialization* is the process of preparing an instance of
a class, structure, or enumeration for use.
This process involves setting an initial value for each stored property on that instance
and performing any other setup or initialization that's required
before the new instance is ready for use.

You implement this initialization process by defining *initializers*,
which are like special methods that can be called
to create a new instance of a particular type.
Unlike Objective-C initializers, Swift initializers don't return a value.
Their primary role is to ensure that new instances of a type
are correctly initialized before they're used for the first time.

Instances of class types can also implement a *deinitializer*,
which performs any custom cleanup just before an instance of that class is deallocated.
For more information about deinitializers, see <doc:Deinitialization>.

## Setting Initial Values for Stored Properties

Classes and structures *must* set all of their stored properties
to an appropriate initial value by the time
an instance of that class or structure is created.
Stored properties can't be left in an indeterminate state.

You can set an initial value for a stored property within an initializer,
or by assigning a default property value as part of the property's definition.
These actions are described in the following sections.

> Note: When you assign a default value to a stored property,
> or set its initial value within an initializer,
> the value of that property is set directly,
> without calling any property observers.

### Inicializadores

*Inicializadores* são chamados para criar uma nova instância de um tipo específico.
Em sua forma mais simples, um inicializador é como um método de instância sem parâmetros,
escrito usando a palavra-chave `init`:

```swift
init() {
   // perform some initialization here
}
```




O exemplo abaixo define uma nova estrutura chamada `Fahrenheit`
para armazenar temperaturas expressas na escala Fahrenheit.
A estrutura `Fahrenheit` tem uma propriedade armazenada,
`temperature`, que é do tipo `Double`:

```swift
struct Fahrenheit {
   var temperature: Double
   init() {
      temperature = 32.0
   }
}
var f = Fahrenheit()
print("The default temperature is \(f.temperature)° Fahrenheit")
// Prints "The default temperature is 32.0° Fahrenheit"
```




A estrutura define um único inicializador, `init`, sem parâmetros,
que inicializa a temperatura armazenada com um valor de `32.0`
(o ponto de congelamento da água em graus Fahrenheit).

### Default Property Values

You can set the initial value of a stored property from within an initializer,
as shown above.
Alternatively, specify a *default property value*
as part of the property's declaration.
You specify a default property value by assigning an initial value to the property
when it's defined.

> Note: If a property always takes the same initial value,
> provide a default value rather than setting a value within an initializer.
> The end result is the same,
> but the default value ties the property's initialization more closely to its declaration.
> It makes for shorter, clearer initializers
> and enables you to infer the type of the property from its default value.
> The default value also makes it easier for you to take advantage of
> default initializers and initializer inheritance,
> as described later in this chapter.

You can write the `Fahrenheit` structure from above in a simpler form
by providing a default value for its `temperature` property
at the point that the property is declared:

```swift
struct Fahrenheit {
   var temperature = 32.0
}
```




## Customizing Initialization

You can customize the initialization process
with input parameters and optional property types,
or by assigning constant properties during initialization,
as described in the following sections.

### Parâmetros de Inicialização

Você pode fornecer *parâmetros de inicialização* como parte da definição de um inicializador,
para definir os tipos e nomes de valores que personalizam o processo de inicialização.
Os parâmetros de inicialização têm os mesmos recursos e sintaxe
de parâmetros de funções e métodos.

O exemplo a seguir define uma estrutura chamada `Celsius`,
que armazena temperaturas expressas em graus Celsius.
A estrutura `Celsius` implementa dois inicializadores personalizados chamados
`init(fromFahrenheit:)` e `init(fromKelvin:)`,
que inicializam uma nova instância da estrutura
com um valor de uma escala de temperatura diferente:

```swift
struct Celsius {
   var temperatureInCelsius: Double
   init(fromFahrenheit fahrenheit: Double) {
      temperatureInCelsius = (fahrenheit - 32.0) / 1.8
   }
   init(fromKelvin kelvin: Double) {
      temperatureInCelsius = kelvin - 273.15
   }
}
let boilingPointOfWater = Celsius(fromFahrenheit: 212.0)
// boilingPointOfWater.temperatureInCelsius is 100.0
let freezingPointOfWater = Celsius(fromKelvin: 273.15)
// freezingPointOfWater.temperatureInCelsius is 0.0
```




O primeiro inicializador tem um único parâmetro de inicialização
com argumento de título `fromFahrenheit` e um nome de parâmetro `fahrenheit`.
O segundo inicializador tem um único parâmetro de inicialização
com argumento de título `fromKelvin` e um nome de parâmetro  `kelvin`.
Os dois inicializadores convertem seus únicos argumentos no
valor Celsius correspondente
e armazenam esse valor numa propriedade chamada `temperatureInCelsius`.



### Parameter Names and Argument Labels

As with function and method parameters,
initialization parameters can have both a parameter name
for use within the initializer's body
and an argument label for use when calling the initializer.

However, initializers don't have an identifying function name before their parentheses
in the way that functions and methods do.
Therefore, the names and types of an initializer's parameters
play a particularly important role in identifying which initializer should be called.
Because of this, Swift provides an automatic argument label
for *every* parameter in an initializer if you don't provide one.

The following example defines a structure called `Color`,
with three constant properties called `red`, `green`, and `blue`.
These properties store a value between `0.0` and `1.0`
to indicate the amount of red, green, and blue in the color.

`Color` provides an initializer with
three appropriately named parameters of type `Double`
for its red, green, and blue components.
`Color` also provides a second initializer with a single `white` parameter,
which is used to provide the same value for all three color components.

```swift
struct Color {
   let red, green, blue: Double
   init(red: Double, green: Double, blue: Double) {
      self.red   = red
      self.green = green
      self.blue  = blue
   }
   init(white: Double) {
      red   = white
      green = white
      blue  = white
   }
}
```




Both initializers can be used to create a new `Color` instance,
by providing named values for each initializer parameter:

```swift
let magenta = Color(red: 1.0, green: 0.0, blue: 1.0)
let halfGray = Color(white: 0.5)
```




Note that it isn't possible to call these initializers
without using argument labels.
Argument labels must always be used in an initializer if they're defined,
and omitting them is a compile-time error:

```swift
let veryGreen = Color(0.0, 1.0, 0.0)
// this reports a compile-time error - argument labels are required
```




### Initializer Parameters Without Argument Labels

If you don't want to use an argument label for an initializer parameter,
write an underscore (`_`) instead of an explicit argument label for that parameter
to override the default behavior.

Here's an expanded version of the `Celsius` example
from <doc:Initialization#Initialization-Parameters> above,
with an additional initializer to create a new `Celsius` instance
from a `Double` value that's already in the Celsius scale:

```swift
struct Celsius {
   var temperatureInCelsius: Double
   init(fromFahrenheit fahrenheit: Double) {
      temperatureInCelsius = (fahrenheit - 32.0) / 1.8
   }
   init(fromKelvin kelvin: Double) {
      temperatureInCelsius = kelvin - 273.15
   }
   init(_ celsius: Double) {
      temperatureInCelsius = celsius
   }
}
let bodyTemperature = Celsius(37.0)
// bodyTemperature.temperatureInCelsius is 37.0
```




The initializer call `Celsius(37.0)` is clear in its intent
without the need for an argument label.
It's therefore appropriate to write this initializer as `init(_ celsius: Double)`
so that it can be called by providing an unnamed `Double` value.

### Optional Property Types

If your custom type has a stored property that's logically allowed to have “no value” ---
perhaps because its value can't be set during initialization,
or because it's allowed to have “no value” at some later point ---
declare the property with an *optional* type.
Properties of optional type are automatically initialized with a value of `nil`,
indicating that the property is deliberately intended to have “no value yet”
during initialization.

The following example defines a class called `SurveyQuestion`,
with an optional `String` property called `response`:

```swift
class SurveyQuestion {
   var text: String
   var response: String?
   init(text: String) {
      self.text = text
   }
   func ask() {
      print(text)
   }
}
let cheeseQuestion = SurveyQuestion(text: "Do you like cheese?")
cheeseQuestion.ask()
// Prints "Do you like cheese?"
cheeseQuestion.response = "Yes, I do like cheese."
```




The response to a survey question can't be known until it's asked,
and so the `response` property is declared with a type of `String?`,
or “optional `String`”.
It's automatically assigned a default value of `nil`, meaning “no string yet”,
when a new instance of `SurveyQuestion` is initialized.

### Assigning Constant Properties During Initialization

You can assign a value to a constant property
at any point during initialization,
as long as it's set to a definite value by the time initialization finishes.
Once a constant property is assigned a value,
it can't be further modified.





> Note: For class instances,
> a constant property can be modified during initialization
> only by the class that introduces it.
> It can't be modified by a subclass.

You can revise the `SurveyQuestion` example from above to use
a constant property rather than a variable property for the `text` property of the question,
to indicate that the question doesn't change once an instance of `SurveyQuestion` is created.
Even though the `text` property is now a constant,
it can still be set within the class's initializer:

```swift
class SurveyQuestion {
   let text: String
   var response: String?
   init(text: String) {
      self.text = text
   }
   func ask() {
      print(text)
   }
}
let beetsQuestion = SurveyQuestion(text: "How about beets?")
beetsQuestion.ask()
// Prints "How about beets?"
beetsQuestion.response = "I also like beets. (But not with cheese.)"
```




## Default Initializers

Swift provides a *default initializer*
for any structure or class
that provides default values for all of its properties
and doesn't provide at least one initializer itself.
The default initializer simply creates a new instance
with all of its properties set to their default values.



This example defines a class called `ShoppingListItem`,
which encapsulates the name, quantity, and purchase state
of an item in a shopping list:

```swift
class ShoppingListItem {
   var name: String?
   var quantity = 1
   var purchased = false
}
var item = ShoppingListItem()
```




Because all properties of the `ShoppingListItem` class have default values,
and because it's a base class with no superclass,
`ShoppingListItem` automatically gains a default initializer implementation
that creates a new instance with all of its properties set to their default values.
(The `name` property is an optional `String` property,
and so it automatically receives a default value of `nil`,
even though this value isn't written in the code.)
The example above uses the default initializer for the `ShoppingListItem` class
to create a new instance of the class with initializer syntax,
written as `ShoppingListItem()`,
and assigns this new instance to a variable called `item`.

### Inicializadores Membro a Membro para Tipos de Estrutura

Tipos de estrutura recebem automaticamente um *inicializador membro a membro*
se eles não definirem nenhum de seus próprios inicializadores personalizados.
Ao contrário de um inicializador padrão,
a estrutura recebe um inicializador de membro
mesmo que tenha propriedades armazenadas que não tenham valores padrão.



O inicializador membro a membro é uma forma abreviada
para inicializar as propriedades de membro de novas instâncias de estrutura.
Valores iniciais para as propriedades da nova instância
pode ser passado para o inicializador membro a membro por nome.

O exemplo abaixo define uma estrutura chamada `Size`
com duas propriedades chamadas `width` e `height`.
Ambas as propriedades são inferidas como sendo do tipo `Double`
atribuindo um valor padrão de `0.0`.

A estrutura `Size` recebe automaticamente um `init(width:height:)`
inicializador de membro,
que você pode usar para inicializar uma nova instância `Size`:

```swift
struct Size {
   var width = 0.0, height = 0.0
}
let twoByTwo = Size(width: 2.0, height: 2.0)
```




Quando você chama um inicializador de membro a membro,
você pode omitir valores para qualquer propriedade
que possuem valores padrão.
No exemplo acima,
a estrutura `Size` tem um valor padrão
para suas propriedades `height` e `width`.
Você pode omitir uma propriedade ou ambas as propriedades,
e o inicializador usa o valor padrão para qualquer coisa que você omitir.
Por exemplo:

```swift
let zeroByTwo = Size(height: 2.0)
print(zeroByTwo.width, zeroByTwo.height)
// Imprime "0.0 2.0"

let zeroByZero = Size()
print(zeroByZero.width, zeroByZero.height)
// Imprime "0.0 0.0"
```




## Initializer Delegation for Value Types

Initializers can call other initializers to perform part of an instance's initialization.
This process, known as *initializer delegation*,
avoids duplicating code across multiple initializers.

The rules for how initializer delegation works,
and for what forms of delegation are allowed,
are different for value types and class types.
Value types (structures and enumerations) don't support inheritance,
and so their initializer delegation process is relatively simple,
because they can only delegate to another initializer that they provide themselves.
Classes, however, can inherit from other classes,
as described in <doc:Inheritance>.
This means that classes have additional responsibilities for ensuring that
all stored properties they inherit are assigned a suitable value during initialization.
These responsibilities are described in
<doc:Initialization#Class-Inheritance-and-Initialization> below.

For value types, you use `self.init` to refer to other initializers
from the same value type when writing your own custom initializers.
You can call `self.init` only from within an initializer.

Note that if you define a custom initializer for a value type,
you will no longer have access to the default initializer
(or the memberwise initializer, if it's a structure) for that type.
This constraint prevents a situation in which additional essential setup
provided in a more complex initializer
is accidentally circumvented by someone using one of the automatic initializers.

> Note: If you want your custom value type to be initializable with
> the default initializer and memberwise initializer,
> and also with your own custom initializers,
> write your custom initializers in an extension
> rather than as part of the value type's original implementation.
> For more information, see <doc:Extensions>.

The following example defines a custom `Rect` structure to represent a geometric rectangle.
The example requires two supporting structures called `Size` and `Point`,
both of which provide default values of `0.0` for all of their properties:

```swift
struct Size {
   var width = 0.0, height = 0.0
}
struct Point {
   var x = 0.0, y = 0.0
}
```




You can initialize the `Rect` structure below in one of three ways ---
by using its default zero-initialized `origin` and `size` property values,
by providing a specific origin point and size,
or by providing a specific center point and size.
These initialization options are represented by
three custom initializers that are part of the `Rect` structure's definition:

```swift
struct Rect {
   var origin = Point()
   var size = Size()
   init() {}
   init(origin: Point, size: Size) {
      self.origin = origin
      self.size = size
   }
   init(center: Point, size: Size) {
      let originX = center.x - (size.width / 2)
      let originY = center.y - (size.height / 2)
      self.init(origin: Point(x: originX, y: originY), size: size)
   }
}
```




The first `Rect` initializer, `init()`,
is functionally the same as the default initializer that the structure would have received
if it didn't have its own custom initializers.
This initializer has an empty body,
represented by an empty pair of curly braces `{}`.
Calling this initializer returns a `Rect` instance whose
`origin` and `size` properties are both initialized with
the default values of `Point(x: 0.0, y: 0.0)`
and `Size(width: 0.0, height: 0.0)`
from their property definitions:

```swift
let basicRect = Rect()
// basicRect's origin is (0.0, 0.0) and its size is (0.0, 0.0)
```




The second `Rect` initializer, `init(origin:size:)`,
is functionally the same as the memberwise initializer that the structure would have received
if it didn't have its own custom initializers.
This initializer simply assigns the `origin` and `size` argument values to
the appropriate stored properties:

```swift
let originRect = Rect(origin: Point(x: 2.0, y: 2.0),
   size: Size(width: 5.0, height: 5.0))
// originRect's origin is (2.0, 2.0) and its size is (5.0, 5.0)
```




The third `Rect` initializer, `init(center:size:)`, is slightly more complex.
It starts by calculating an appropriate origin point based on
a `center` point and a `size` value.
It then calls (or *delegates*) to the `init(origin:size:)` initializer,
which stores the new origin and size values in the appropriate properties:

```swift
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
   size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```




The `init(center:size:)` initializer could have assigned
the new values of `origin` and `size` to the appropriate properties itself.
However, it's more convenient (and clearer in intent)
for the `init(center:size:)` initializer to take advantage of an existing initializer
that already provides exactly that functionality.

> Note: For an alternative way to write this example without defining
> the `init()` and `init(origin:size:)` initializers yourself,
> see <doc:Extensions>.

## Herança e Inicialização de Classe

Todas as propriedades armazenadas de uma classe ---
incluindo quaisquer propriedades que a classe herda de sua superclasse ---
*deve* ser atribuído um valor inicial durante a inicialização.

Swift define dois tipos de inicializadores para tipos de classe
para ajudar a garantir que todas as propriedades armazenadas recebam um valor inicial.
Eles são conhecidos como inicializadores designados e inicializadores de conveniência.

### Inicializadores designados e inicializadores de conveniência

*Inicializadores designados* são os inicializadores primários de uma classe.
Um inicializador designado inicializa todas as propriedades introduzidas por essa classe
e chama um inicializador de superclasse apropriado
para continuar o processo de inicialização na cadeia de superclasses.

As classes tendem a ter muito poucos inicializadores designados,
e é bastante comum que uma classe tenha apenas um.
Inicializadores designados são pontos de “funil” através dos quais a inicialização ocorre,
e através do qual o processo de inicialização continua na cadeia da superclasse.

Toda classe deve ter pelo menos um inicializador designado.
Em alguns casos, este requisito é satisfeito
herdando um ou mais inicializadores designados de uma superclasse,
conforme descrito em <doc:Initialization#Automatic-Initializer-Inheritance> abaixo.

*Inicializadores de conveniência* são secundários, fornecendo suporte para os inicializadores para uma classe.
Você pode definir um inicializador de conveniência para chamar um inicializador designado
da mesma classe que o inicializador de conveniência
com alguns dos parâmetros do inicializador designados definidos com valores padrão.
Você também pode definir um inicializador de conveniência para criar
uma instância dessa classe para um caso de uso específico ou tipo de valor de entrada.

Você não precisa fornecer inicializadores de conveniência se sua classe não os exigir.
Crie inicializadores de conveniência quando um atalho para um padrão de inicialização comum
fará você economizar tempo ou tornará a inicialização da classe mais clara na intenção.

### Sintaxe para inicializadores designados e de conveniência

Inicializadores designados para classes são escritos da mesma maneira que
inicializadores simples para tipos de valor:

```
init(<#parameters#>) {
   <#statements#>
}
```


Os inicializadores de conveniência são escritos no mesmo estilo,
mas com o modificador `_convenience_` colocado antes da palavra-chave `_init_`,
separados por um espaço:

```
convenience init(<#parameters#>) {
   <#statements#>
}
```


### Initializer Delegation for Class Types

To simplify the relationships between designated and convenience initializers,
Swift applies the following three rules for delegation calls between initializers:

- term **Rule 1**: A designated initializer must call a designated initializer from its immediate superclass.
- term **Rule 2**: A convenience initializer must call another initializer from the *same* class.
- term **Rule 3**: A convenience initializer must ultimately call a designated initializer.

A simple way to remember this is:

- Designated initializers must always delegate *up*.
- Convenience initializers must always delegate *across*.

These rules are illustrated in the figure below:

![](initializerDelegation01)


Here, the superclass has a single designated initializer and two convenience initializers.
One convenience initializer calls another convenience initializer,
which in turn calls the single designated initializer.
This satisfies rules 2 and 3 from above.
The superclass doesn't itself have a further superclass, and so rule 1 doesn't apply.

The subclass in this figure has two designated initializers and one convenience initializer.
The convenience initializer must call one of the two designated initializers,
because it can only call another initializer from the same class.
This satisfies rules 2 and 3 from above.
Both designated initializers must call the single designated initializer
from the superclass, to satisfy rule 1 from above.

> Note: These rules don't affect how users of your classes *create* instances of each class.
> Any initializer in the diagram above can be used to create
> a fully initialized instance of the class they belong to.
> The rules only affect how you write the implementation of the class's initializers.

The figure below shows a more complex class hierarchy for four classes.
It illustrates how the designated initializers in this hierarchy
act as “funnel” points for class initialization,
simplifying the interrelationships among classes in the chain:

![](initializerDelegation02)


### Two-Phase Initialization

Class initialization in Swift is a two-phase process.
In the first phase, each stored property is assigned an initial value
by the class that introduced it.
Once the initial state for every stored property has been determined,
the second phase begins,
and each class is given the opportunity to customize its stored properties further
before the new instance is considered ready for use.

The use of a two-phase initialization process makes initialization safe,
while still giving complete flexibility to each class in a class hierarchy.
Two-phase initialization prevents property values
from being accessed before they're initialized,
and prevents property values from being set to a different value
by another initializer unexpectedly.

> Note: Swift's two-phase initialization process is similar to initialization in Objective-C.
> The main difference is that during phase 1,
> Objective-C assigns zero or null values (such as `0` or `nil`) to every property.
> Swift's initialization flow is more flexible
> in that it lets you set custom initial values,
> and can cope with types for which `0` or `nil` isn't a valid default value.

Swift's compiler performs four helpful safety-checks to make sure that
two-phase initialization is completed without error:

- term **Safety check 1**: A designated initializer must ensure that all of the properties introduced by its class
are initialized before it delegates up to a superclass initializer.

As mentioned above,
the memory for an object is only considered fully initialized
once the initial state of all of its stored properties is known.
In order for this rule to be satisfied, a designated initializer must make sure that
all of its own properties are initialized before it hands off up the chain.

- term **Safety check 2**: A designated initializer must delegate up to a superclass initializer
before assigning a value to an inherited property.
If it doesn't, the new value the designated initializer assigns
will be overwritten by the superclass as part of its own initialization.
- term **Safety check 3**: A convenience initializer must delegate to another initializer
before assigning a value to *any* property
(including properties defined by the same class).
If it doesn't, the new value the convenience initializer assigns
will be overwritten by its own class's designated initializer.
- term **Safety check 4**: An initializer can't call any instance methods,
read the values of any instance properties,
or refer to `self` as a value
until after the first phase of initialization is complete.

The class instance isn't fully valid until the first phase ends.
Properties can only be accessed, and methods can only be called,
once the class instance is known to be valid at the end of the first phase.

Here's how two-phase initialization plays out, based on the four safety checks above:

**Phase 1**

- A designated or convenience initializer is called on a class.
- Memory for a new instance of that class is allocated.
  The memory isn't yet initialized.
- A designated initializer for that class confirms that
  all stored properties introduced by that class have a value.
  The memory for these stored properties is now initialized.
- The designated initializer hands off to a superclass initializer to perform the same task
  for its own stored properties.
- This continues up the class inheritance chain until the top of the chain is reached.
- Once the top of the chain is reached,
  and the final class in the chain has ensured that all of its stored properties have a value,
  the instance's memory is considered to be fully initialized, and phase 1 is complete.

**Phase 2**

- Working back down from the top of the chain,
  each designated initializer in the chain has the option to customize the instance further.
  Initializers are now able to access `self`
  and can modify its properties, call its instance methods, and so on.
- Finally, any convenience initializers in the chain have the option
  to customize the instance and to work with `self`.

Here's how phase 1 looks for an initialization call for a hypothetical subclass and superclass:

![](twoPhaseInitialization01)


In this example, initialization begins with a call to
a convenience initializer on the subclass.
This convenience initializer can't yet modify any properties.
It delegates across to a designated initializer from the same class.

The designated initializer makes sure that all of the subclass's properties have a value,
as per safety check 1. It then calls a designated initializer on its superclass
to continue the initialization up the chain.

The superclass's designated initializer makes sure that
all of the superclass properties have a value.
There are no further superclasses to initialize,
and so no further delegation is needed.

As soon as all properties of the superclass have an initial value,
its memory is considered fully initialized, and phase 1 is complete.

Here's how phase 2 looks for the same initialization call:

![](twoPhaseInitialization02)


The superclass's designated initializer now has an opportunity
to customize the instance further
(although it doesn't have to).

Once the superclass's designated initializer is finished,
the subclass's designated initializer can perform additional customization
(although again, it doesn't have to).

Finally, once the subclass's designated initializer is finished,
the convenience initializer that was originally called
can perform additional customization.

### Herança e sobrescrita do inicializador

Ao contrário das subclasses em Objective-C,
As subclasses Swift não herdam seus inicializadores de superclasse por padrão.
A abordagem do Swift evita a situação em que um inicializador simples de uma superclasse
é herdado por uma subclasse mais especializada
e é usado para criar uma nova instância da subclasse
que não foi inicializado completa ou corretamente.

> Nota: Inicializadores de superclasse *são* herdados em certas circunstâncias,
> mas apenas quando for seguro e apropriado fazê-lo.
> Para obter mais informações, acesse <doc:Initialization#Automatic-Initializer-Inheritance> below.

Se você quiser que uma subclasse personalizada apresente
um ou mais dos mesmos inicializadores que sua superclasse,
você pode fornecer uma implementação personalizada desses inicializadores dentro da subclasse.

Quando você escreve um inicializador de subclasse que corresponde a um inicializador *designado* da superclasse,
você está efetivamente fornecendo uma sobrescrita desse inicializador designado.
Portanto, você deve escrever o modificador `override` antes da definição do inicializador da subclasse.
Isso é verdade mesmo se você estiver sobrescrevendo um inicializador padrão fornecido automaticamente,
conforme descrito em <doc:Initialization#Default-Initializers>.

Tal como acontece com uma propriedade, método ou subscript sobrescrito
a presença do modificador `override` solicita ao Swift que verifique se
a superclasse tem um inicializador designado correspondente a ser sobrescrito,
e valida se os parâmetros do inicializador sobrescrito foram especificados conforme o esperado.

> Nota: Você sempre escreve o modificador `override` ao sobrescrever um inicializador designado de superclasse,
> mesmo que a implementação do inicializador de sua subclasse seja um inicializador de conveniência.

Por outro lado, se você escrever um inicializador de subclasse que corresponda a um inicializador de superclasse *conveniência*,
esse inicializador de conveniência da superclasse nunca pode ser chamado diretamente por sua subclasse,
conforme as regras descritas acima em <doc:Initialization#Initializer-Delegation-for-Class-Types>.
Portanto, sua subclasse não está (estritamente falando) fornecendo uma sobrescrita do inicializador da superclasse.
Como resultado, você não escreve o modificador `override` ao fornecer
uma implementação correspondente de um inicializador de conveniência de superclasse.

O exemplo abaixo define uma classe base chamada `Vehicle`.
Esta classe base declara uma propriedade armazenada chamada `numberOfWheels`,
com um valor padrão de `Int` de `0`.
A propriedade `numberOfWheels` é usada por uma propriedade computada chamada `description`
para criar uma descrição `String` das características do veículo:


```swift
class Vehicle {
   var numberOfWheels = 0
   var description: String {
      return "\(numberOfWheels) wheel(s)"
   }
}
```


A classe `Vehicle` fornece um valor padrão para sua única propriedade armazenada,
e não fornece nenhum inicializador personalizado.
Como resultado, ele recebe automaticamente um inicializador padrão,
conforme descrito em <doc:Initialization#Default-Initializers>.
O inicializador padrão (quando disponível) é sempre um inicializador designado para uma classe,
e pode ser usado para criar uma nova instância `Vehicle` com um `numberOfWheels` de `0`:


```swift
let vehicle = Vehicle()
print("Vehicle: \(vehicle.description)")
// Vehicle: 0 wheel(s)
```

O próximo exemplo define uma subclasse de `Vehicle` chamada `Bicycle`:


```swift
class Bicycle: Vehicle {
   override init() {
      super.init()
      numberOfWheels = 2
   }
}
```
A subclasse `Bicycle` define um inicializador designado personalizado, `init()`.
Este inicializador designado corresponde a um inicializador designado da superclasse de `Bicycle`,
e assim a versão `Bicycle` deste inicializador é marcada com o modificador `override`.

O inicializador `init()` para `Bicycle` começa chamando `super.init()`,
que chama o inicializador padrão para a superclasse da classe `Bicycle`, `Vehicle`.
Isso garante que a propriedade herdada `numberOfWheels` seja inicializada por `Vehicle`
antes que `Bicycle` tenha a oportunidade de modificar a propriedade.
Depois de chamar `super.init()`,
o valor original de `numberOfWheels` é substituído por um novo valor de `2`.


Se você criar uma instância de `Bicycle`,
você pode chamar sua propriedade computada `description` herdada
para ver como sua propriedade `numberOfWheels` foi atualizada:

```swift
let bicycle = Bicycle()
print("Bicycle: \(bicycle.description)")
// Bicycle: 2 wheel(s)
```

Se um inicializador de subclasse não executa nenhuma personalização
na fase 2 do processo de inicialização,
e a superclasse tem um inicializador designado síncrono, com argumento zero,
você pode omitir uma chamada para `super.init()`
depois de atribuir valores a todas as propriedades armazenadas da subclasse.
Se o inicializador da superclasse for assíncrono,
você precisa escrever `await super.init()` explicitamente.


Este exemplo define outra subclasse de `Vehicle`, chamada `Hoverboard`.
Em seu inicializador, a classe `Hoverboard` define apenas sua propriedade `color`.
Em vez de fazer uma chamada explícita para `super.init()`,
este inicializador depende de uma chamada implícita ao inicializador de sua superclasse
para concluir o processo.

```swift
class Hoverboard: Vehicle {
    var color: String
    init(color: String) {
        self.color = color
        // super.init() implicitly called here
    }
    override var description: String {
        return "\(super.description) in a beautiful \(color)"
    }
}
```

Uma instância de `Hoverboard` usa o número padrão de rodas
fornecido pelo inicializador `Vehicle`.

```swift
let hoverboard = Hoverboard(color: "silver")
print("Hoverboard: \(hoverboard.description)")
// Hoverboard: 0 wheel(s) in a beautiful silver
```


> Nota: Subclasses podem modificar propriedades de variáveis herdadas durante a inicialização,
> mas não pode modificar propriedades constantes herdadas.

### Automatic Initializer Inheritance

Como mencionado acima,
subclasses não herdam seus inicializadores de superclasse por padrão.
No entanto, inicializadores de superclasses *são* herdados automaticamente se certas condições forem atendidas.
Na prática, isso significa que
você não precisa escrever substituições de inicializador na maioria dos cenários,
e pode herdar seus inicializadores de superclasse com esforço mínimo sempre que for seguro fazê-lo.

Supondo que você forneça valores padrão para quaisquer novas propriedades introduzidas em uma subclasse,
aplicam-se as duas regras seguintes:

- termo **Regra 1**: Se sua subclasse não definir nenhum inicializador designado,
ela herda automaticamente todos os inicializadores designados pela superclasse.
- termo **Regra 2**: Se sua subclasse fornece uma implementação de
*todos* os inicializadores designados de superclasse ---
herdando-os de acordo com a regra 1,
ou fornecendo uma implementação personalizada como parte de sua definição ---
então ele herda automaticamente todos os inicializadores de conveniência da superclasse.

These rules apply even if your subclass adds further convenience initializers.

> Nota: Uma subclasse pode implementar um inicializador designado por superclasse
> como um inicializador de conveniência de subclasse como parte do cumprimento da regra 2.





### Designated and Convenience Initializers in Action

The following example shows designated initializers, convenience initializers,
and automatic initializer inheritance in action.
This example defines a hierarchy of three classes called
`Food`, `RecipeIngredient`, and `ShoppingListItem`,
and demonstrates how their initializers interact.

The base class in the hierarchy is called `Food`,
which is a simple class to encapsulate the name of a foodstuff.
The `Food` class introduces a single `String` property called `name`
and provides two initializers for creating `Food` instances:

```swift
class Food {
   var name: String
   init(name: String) {
      self.name = name
   }
   convenience init() {
      self.init(name: "[Unnamed]")
   }
}
```




The figure below shows the initializer chain for the `Food` class:

![](initializersExample01)


Classes don't have a default memberwise initializer,
and so the `Food` class provides a designated initializer
that takes a single argument called `name`.
This initializer can be used to create a new `Food` instance with a specific name:

```swift
let namedMeat = Food(name: "Bacon")
// namedMeat's name is "Bacon"
```




The `init(name: String)` initializer from the `Food` class
is provided as a *designated* initializer,
because it ensures that all stored properties of
a new `Food` instance are fully initialized.
The `Food` class doesn't have a superclass,
and so the `init(name: String)` initializer doesn't need to call `super.init()`
to complete its initialization.

The `Food` class also provides a *convenience* initializer, `init()`, with no arguments.
The `init()` initializer provides a default placeholder name for a new food
by delegating across to the `Food` class's `init(name: String)` with
a `name` value of `[Unnamed]`:

```swift
let mysteryMeat = Food()
// mysteryMeat's name is "[Unnamed]"
```




The second class in the hierarchy is a subclass of `Food` called `RecipeIngredient`.
The `RecipeIngredient` class models an ingredient in a cooking recipe.
It introduces an `Int` property called `quantity`
(in addition to the `name` property it inherits from `Food`)
and defines two initializers for creating `RecipeIngredient` instances:

```swift
class RecipeIngredient: Food {
   var quantity: Int
   init(name: String, quantity: Int) {
      self.quantity = quantity
      super.init(name: name)
   }
   override convenience init(name: String) {
      self.init(name: name, quantity: 1)
   }
}
```




The figure below shows the initializer chain for the `RecipeIngredient` class:

![](initializersExample02)


The `RecipeIngredient` class has a single designated initializer,
`init(name: String, quantity: Int)`,
which can be used to populate all of the properties of a new `RecipeIngredient` instance.
This initializer starts by assigning
the passed `quantity` argument to the `quantity` property,
which is the only new property introduced by `RecipeIngredient`.
After doing so, the initializer delegates up to
the `init(name: String)` initializer of the `Food` class.
This process satisfies safety check 1
from <doc:Initialization#Two-Phase-Initialization> above.

`RecipeIngredient` also defines a convenience initializer, `init(name: String)`,
which is used to create a `RecipeIngredient` instance by name alone.
This convenience initializer assumes a quantity of `1`
for any `RecipeIngredient` instance that's created without an explicit quantity.
The definition of this convenience initializer makes
`RecipeIngredient` instances quicker and more convenient to create,
and avoids code duplication when creating
several single-quantity `RecipeIngredient` instances.
This convenience initializer simply delegates across to the class's designated initializer,
passing in a `quantity` value of `1`.

The `init(name: String)` convenience initializer provided by `RecipeIngredient`
takes the same parameters as the `init(name: String)` *designated* initializer from `Food`.
Because this convenience initializer overrides a designated initializer from its superclass,
it must be marked with the `override` modifier
(as described in <doc:Initialization#Initializer-Inheritance-and-Overriding>).

Even though `RecipeIngredient` provides
the `init(name: String)` initializer as a convenience initializer,
`RecipeIngredient` has nonetheless provided an implementation of
all of its superclass's designated initializers.
Therefore, `RecipeIngredient` automatically inherits
all of its superclass's convenience initializers too.

In this example, the superclass for `RecipeIngredient` is `Food`,
which has a single convenience initializer called `init()`.
This initializer is therefore inherited by `RecipeIngredient`.
The inherited version of `init()` functions in exactly the same way as the `Food` version,
except that it delegates to the `RecipeIngredient` version of `init(name: String)`
rather than the `Food` version.

All three of these initializers can be used to create new `RecipeIngredient` instances:

```swift
let oneMysteryItem = RecipeIngredient()
let oneBacon = RecipeIngredient(name: "Bacon")
let sixEggs = RecipeIngredient(name: "Eggs", quantity: 6)
```




The third and final class in the hierarchy is
a subclass of `RecipeIngredient` called `ShoppingListItem`.
The `ShoppingListItem` class models a recipe ingredient as it appears in a shopping list.

Every item in the shopping list starts out as “unpurchased”.
To represent this fact,
`ShoppingListItem` introduces a Boolean property called `purchased`,
with a default value of `false`.
`ShoppingListItem` also adds a computed `description` property,
which provides a textual description of a `ShoppingListItem` instance:

```swift
class ShoppingListItem: RecipeIngredient {
   var purchased = false
   var description: String {
      var output = "\(quantity) x \(name)"
      output += purchased ? " ✔" : " ✘"
      return output
   }
}
```




> Note: `ShoppingListItem` doesn't define an initializer to provide
> an initial value for `purchased`,
> because items in a shopping list (as modeled here) always start out unpurchased.

Because it provides a default value for all of the properties it introduces
and doesn't define any initializers itself,
`ShoppingListItem` automatically inherits
*all* of the designated and convenience initializers from its superclass.

The figure below shows the overall initializer chain for all three classes:

![](initializersExample03)


You can use all three of the inherited initializers
to create a new `ShoppingListItem` instance:

```swift
var breakfastList = [
   ShoppingListItem(),
   ShoppingListItem(name: "Bacon"),
   ShoppingListItem(name: "Eggs", quantity: 6),
]
breakfastList[0].name = "Orange juice"
breakfastList[0].purchased = true
for item in breakfastList {
   print(item.description)
}
// 1 x Orange juice ✔
// 1 x Bacon ✘
// 6 x Eggs ✘
```




Here, a new array called `breakfastList` is created from
an array literal containing three new `ShoppingListItem` instances.
The type of the array is inferred to be `[ShoppingListItem]`.
After the array is created,
the name of the `ShoppingListItem` at the start of the array
is changed from `"[Unnamed]"` to `"Orange juice"`
and it's marked as having been purchased.
Printing the description of each item in the array
shows that their default states have been set as expected.







## Failable Initializers

It's sometimes useful to define a class, structure, or enumeration
for which initialization can fail.
This failure might be triggered by invalid initialization parameter values,
the absence of a required external resource,
or some other condition that prevents initialization from succeeding.

To cope with initialization conditions that can fail,
define one or more failable initializers as part of
a class, structure, or enumeration definition.
You write a failable initializer
by placing a question mark after the `init` keyword (`init?`).

> Note: You can't define a failable and a nonfailable initializer
> with the same parameter types and names.



A failable initializer creates an *optional* value of the type it initializes.
You write `return nil` within a failable initializer
to indicate a point at which initialization failure can be triggered.

> Note: Strictly speaking, initializers don't return a value.
> Rather, their role is to ensure that `self` is fully and correctly initialized
> by the time that initialization ends.
> Although you write `return nil` to trigger an initialization failure,
> you don't use the `return` keyword to indicate initialization success.

For instance, failable initializers are implemented for numeric type conversions.
To ensure conversion between numeric types maintains the value exactly,
use the `init(exactly:)` initializer.
If the type conversion can't maintain the value,
the initializer fails.

```swift
let wholeNumber: Double = 12345.0
let pi = 3.14159

if let valueMaintained = Int(exactly: wholeNumber) {
    print("\(wholeNumber) conversion to Int maintains value of \(valueMaintained)")
}
// Prints "12345.0 conversion to Int maintains value of 12345"

let valueChanged = Int(exactly: pi)
// valueChanged is of type Int?, not Int

if valueChanged == nil {
    print("\(pi) conversion to Int doesn't maintain value")
}
// Prints "3.14159 conversion to Int doesn't maintain value"
```




The example below defines a structure called `Animal`,
with a constant `String` property called `species`.
The `Animal` structure also defines a failable initializer
with a single parameter called `species`.
This initializer checks if the `species` value passed to the initializer is an empty string.
If an empty string is found, an initialization failure is triggered.
Otherwise, the `species` property's value is set, and initialization succeeds:

```swift
struct Animal {
   let species: String
   init?(species: String) {
      if species.isEmpty { return nil }
      self.species = species
   }
}
```




You can use this failable initializer to try to initialize a new `Animal` instance
and to check if initialization succeeded:

```swift
let someCreature = Animal(species: "Giraffe")
// someCreature is of type Animal?, not Animal

if let giraffe = someCreature {
   print("An animal was initialized with a species of \(giraffe.species)")
}
// Prints "An animal was initialized with a species of Giraffe"
```




If you pass an empty string value to the failable initializer's `species` parameter,
the initializer triggers an initialization failure:

```swift
let anonymousCreature = Animal(species: "")
// anonymousCreature is of type Animal?, not Animal

if anonymousCreature == nil {
   print("The anonymous creature couldn't be initialized")
}
// Prints "The anonymous creature couldn't be initialized"
```




> Note: Checking for an empty string value (such as `""` rather than `"Giraffe"`)
> isn't the same as checking for `nil` to indicate the absence of an *optional* `String` value.
> In the example above, an empty string (`""`) is a valid, non-optional `String`.
> However, it's not appropriate for an animal
> to have an empty string as the value of its `species` property.
> To model this restriction,
> the failable initializer triggers an initialization failure if an empty string is found.

### Failable Initializers for Enumerations

You can use a failable initializer to select an appropriate enumeration case
based on one or more parameters.
The initializer can then fail if the provided parameters
don't match an appropriate enumeration case.

The example below defines an enumeration called `TemperatureUnit`,
with three possible states (`kelvin`, `celsius`, and `fahrenheit`).
A failable initializer is used to find an appropriate enumeration case
for a `Character` value representing a temperature symbol:

```swift
enum TemperatureUnit {
   case kelvin, celsius, fahrenheit
   init?(symbol: Character) {
      switch symbol {
         case "K":
            self = .kelvin
         case "C":
            self = .celsius
         case "F":
            self = .fahrenheit
         default:
            return nil
      }
   }
}
```




You can use this failable initializer to choose
an appropriate enumeration case for the three possible states
and to cause initialization to fail if the parameter doesn't match one of these
states:

```swift
let fahrenheitUnit = TemperatureUnit(symbol: "F")
if fahrenheitUnit != nil {
   print("This is a defined temperature unit, so initialization succeeded.")
}
// Prints "This is a defined temperature unit, so initialization succeeded."

let unknownUnit = TemperatureUnit(symbol: "X")
if unknownUnit == nil {
   print("This isn't a defined temperature unit, so initialization failed.")
}
// Prints "This isn't a defined temperature unit, so initialization failed."
```




### Failable Initializers for Enumerations with Raw Values

Enumerations with raw values automatically receive a failable initializer,
`init?(rawValue:)`,
that takes a parameter called `rawValue` of the appropriate raw-value type
and selects a matching enumeration case if one is found,
or triggers an initialization failure if no matching value exists.

You can rewrite the `TemperatureUnit` example from above
to use raw values of type `Character`
and to take advantage of the `init?(rawValue:)` initializer:

```swift
enum TemperatureUnit: Character {
   case kelvin = "K", celsius = "C", fahrenheit = "F"
}

let fahrenheitUnit = TemperatureUnit(rawValue: "F")
if fahrenheitUnit != nil {
   print("This is a defined temperature unit, so initialization succeeded.")
}
// Prints "This is a defined temperature unit, so initialization succeeded."

let unknownUnit = TemperatureUnit(rawValue: "X")
if unknownUnit == nil {
   print("This isn't a defined temperature unit, so initialization failed.")
}
// Prints "This isn't a defined temperature unit, so initialization failed."
```




### Propagation of Initialization Failure

A failable initializer of a class, structure, or enumeration
can delegate across to another failable initializer from the same class, structure, or enumeration.
Similarly, a subclass failable initializer can delegate up to a superclass failable initializer.

In either case, if you delegate to another initializer that causes initialization to fail,
the entire initialization process fails immediately,
and no further initialization code is executed.







> Note: A failable initializer can also delegate to a nonfailable initializer.
> Use this approach if you need to add a potential failure state
> to an existing initialization process that doesn't otherwise fail.

The example below defines a subclass of `Product` called `CartItem`.
The `CartItem` class models an item in an online shopping cart.
`CartItem` introduces a stored constant property called `quantity`
and ensures that this property always has a value of at least `1`:

```swift
class Product {
   let name: String
   init?(name: String) {
      if name.isEmpty { return nil }
      self.name = name
   }
}

class CartItem: Product {
   let quantity: Int
   init?(name: String, quantity: Int) {
      if quantity < 1 { return nil }
      self.quantity = quantity
      super.init(name: name)
   }
}
```




The failable initializer for `CartItem` starts by
validating that it has received a `quantity` value of `1` or more.
If the `quantity` is invalid,
the entire initialization process fails immediately
and no further initialization code is executed.
Likewise, the failable initializer for `Product`
checks the `name` value,
and the initializer process fails immediately
if `name` is the empty string.

If you create a `CartItem` instance with a nonempty name and a quantity of `1` or more,
initialization succeeds:

```swift
if let twoSocks = CartItem(name: "sock", quantity: 2) {
   print("Item: \(twoSocks.name), quantity: \(twoSocks.quantity)")
}
// Prints "Item: sock, quantity: 2"
```




If you try to create a `CartItem` instance with a `quantity` value of `0`,
the `CartItem` initializer causes initialization to fail:

```swift
if let zeroShirts = CartItem(name: "shirt", quantity: 0) {
   print("Item: \(zeroShirts.name), quantity: \(zeroShirts.quantity)")
} else {
   print("Unable to initialize zero shirts")
}
// Prints "Unable to initialize zero shirts"
```




Similarly, if you try to create a `CartItem` instance with an empty `name` value,
the superclass `Product` initializer causes initialization to fail:

```swift
if let oneUnnamed = CartItem(name: "", quantity: 1) {
   print("Item: \(oneUnnamed.name), quantity: \(oneUnnamed.quantity)")
} else {
   print("Unable to initialize one unnamed product")
}
// Prints "Unable to initialize one unnamed product"
```




### Overriding a Failable Initializer

You can override a superclass failable initializer in a subclass,
just like any other initializer.
Alternatively, you can override a superclass failable initializer
with a subclass *nonfailable* initializer.
This enables you to define a subclass for which initialization can't fail,
even though initialization of the superclass is allowed to fail.

Note that if you override a failable superclass initializer with a nonfailable subclass initializer,
the only way to delegate up to the superclass initializer
is to force-unwrap the result of the failable superclass initializer.

> Note: You can override a failable initializer with a nonfailable initializer
> but not the other way around.



The example below defines a class called `Document`.
This class models a document that can be initialized with
a `name` property that's either a nonempty string value or `nil`,
but can't be an empty string:

```swift
class Document {
   var name: String?
   // this initializer creates a document with a nil name value
   init() {}
   // this initializer creates a document with a nonempty name value
   init?(name: String) {
      if name.isEmpty { return nil }
      self.name = name
   }
}
```




The next example defines a subclass of `Document` called `AutomaticallyNamedDocument`.
The `AutomaticallyNamedDocument` subclass overrides
both of the designated initializers introduced by `Document`.
These overrides ensure that an `AutomaticallyNamedDocument` instance has
an initial `name` value of `"[Untitled]"`
if the instance is initialized without a name,
or if an empty string is passed to the `init(name:)` initializer:

```swift
class AutomaticallyNamedDocument: Document {
   override init() {
      super.init()
      self.name = "[Untitled]"
   }
   override init(name: String) {
      super.init()
      if name.isEmpty {
         self.name = "[Untitled]"
      } else {
         self.name = name
      }
   }
}
```




The `AutomaticallyNamedDocument` overrides its superclass's
failable `init?(name:)` initializer with a nonfailable `init(name:)` initializer.
Because `AutomaticallyNamedDocument` copes with the empty string case
in a different way than its superclass,
its initializer doesn't need to fail,
and so it provides a nonfailable version of the initializer instead.

You can use forced unwrapping in an initializer
to call a failable initializer from the superclass
as part of the implementation of a subclass's nonfailable initializer.
For example, the `UntitledDocument` subclass below is always named `"[Untitled]"`,
and it uses the failable `init(name:)` initializer
from its superclass during initialization.

```swift
class UntitledDocument: Document {
   override init() {
      super.init(name: "[Untitled]")!
   }
}
```




In this case, if the `init(name:)` initializer of the superclass
were ever called with an empty string as the name,
the forced unwrapping operation would result in a runtime error.
However, because it's called with a string constant,
you can see that the initializer won't fail,
so no runtime error can occur in this case.

### The init! Failable Initializer

You typically define a failable initializer
that creates an optional instance of the appropriate type
by placing a question mark after the `init` keyword (`init?`).
Alternatively, you can define a failable initializer that creates
an implicitly unwrapped optional instance of the appropriate type.
Do this by placing an exclamation point after the `init` keyword (`init!`)
instead of a question mark.

You can delegate from `init?` to `init!` and vice versa,
and you can override `init?` with `init!` and vice versa.
You can also delegate from `init` to `init!`,
although doing so will trigger an assertion
if the `init!` initializer causes initialization to fail.





























## Required Initializers

Write the `required` modifier before the definition of a class initializer
to indicate that every subclass of the class must implement that initializer:

```swift
class SomeClass {
   required init() {
      // initializer implementation goes here
   }
}
```








You must also write the `required` modifier before
every subclass implementation of a required initializer,
to indicate that the initializer requirement applies to further subclasses in the chain.
You don't write the `override` modifier when overriding a required designated initializer:

```swift
class SomeSubclass: SomeClass {
   required init() {
      // subclass implementation of the required initializer goes here
   }
}
```






> Note: You don't have to provide an explicit implementation of a required initializer
> if you can satisfy the requirement with an inherited initializer.







## Setting a Default Property Value with a Closure or Function

If a stored property's default value requires some customization or setup,
you can use a closure or global function to provide
a customized default value for that property.
Whenever a new instance of the type that the property belongs to is initialized,
the closure or function is called,
and its return value is assigned as the property's default value.

These kinds of closures or functions typically create
a temporary value of the same type as the property,
tailor that value to represent the desired initial state,
and then return that temporary value to be used as the property's default value.

Here's a skeleton outline of how a closure can be used
to provide a default property value:

```swift
class SomeClass {
   let someProperty: SomeType = {
      // create a default value for someProperty inside this closure
      // someValue must be of the same type as SomeType
      return someValue
   }()
}
```




Note that the closure's end curly brace is followed by an empty pair of parentheses.
This tells Swift to execute the closure immediately.
If you omit these parentheses,
you are trying to assign the closure itself to the property,
and not the return value of the closure.



> Note: If you use a closure to initialize a property,
> remember that the rest of the instance hasn't yet been initialized
> at the point that the closure is executed.
> This means that you can't access any other property values from within your closure,
> even if those properties have default values.
> You also can't use the implicit `self` property,
> or call any of the instance's methods.

The example below defines a structure called `Chessboard`,
which models a board for the game of chess.
Chess is played on an 8 x 8 board,
with alternating black and white squares.

![](chessBoard)


To represent this game board,
the `Chessboard` structure has a single property called `boardColors`,
which is an array of 64 `Bool` values.
A value of `true` in the array represents a black square
and a value of `false` represents a white square.
The first item in the array represents the top left square on the board
and the last item in the array represents the bottom right square on the board.

The `boardColors` array is initialized with a closure to set up its color values:

```swift
struct Chessboard {
   let boardColors: [Bool] = {
      var temporaryBoard: [Bool] = []
      var isBlack = false
      for i in 1...8 {
         for j in 1...8 {
            temporaryBoard.append(isBlack)
            isBlack = !isBlack
         }
         isBlack = !isBlack
      }
      return temporaryBoard
   }()
   func squareIsBlackAt(row: Int, column: Int) -> Bool {
      return boardColors[(row * 8) + column]
   }
}
```




Whenever a new `Chessboard` instance is created, the closure is executed,
and the default value of `boardColors` is calculated and returned.
The closure in the example above calculates and sets
the appropriate color for each square on the board
in a temporary array called `temporaryBoard`,
and returns this temporary array as the closure's return value
once its setup is complete.
The returned array value is stored in `boardColors`
and can be queried with the `squareIsBlackAt(row:column:)` utility function:

```swift
let board = Chessboard()
print(board.squareIsBlackAt(row: 0, column: 1))
// Prints "true"
print(board.squareIsBlackAt(row: 7, column: 7))
// Prints "false"
```






