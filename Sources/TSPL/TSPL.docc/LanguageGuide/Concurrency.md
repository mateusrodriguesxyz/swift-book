

# Concorrência

Swift tem suporte por padrão para escrita de código assíncrono e paralelo
de forma estruturada.
*Código assíncrono* pode ser suspenso e retomado posteriormente,
embora apenas uma parte do programa seja executada por vez.
Suspender e retomar código em seu programa
permite que ele continue a progredir
em operações de curto prazo, como atualizar a interface do usuário,
enquanto continua a trabalhar em operações de longa duração
como buscar dados pela rede ou analisar arquivos.
*Código paralelo* significa vários pedaços de código executando simultaneamente ---
por exemplo, um computador com um processador de quatro núcleos
pode executar quatro pedaços de código ao mesmo tempo,
com cada núcleo realizando uma das tarefas.
Um programa que usa código paralelo e assíncrono
realiza várias operações ao mesmo tempo;
suspende as operações que estão à espera de um sistema externo,
e torna mais fácil escrever esse código de maneira segura para a memória (_memory-safe_).

A flexibilidade adicional de agendamento de código paralelo ou assíncrono
também vem com um custo de maior complexidade.
Swift permite que você expresse sua intenção
de uma forma que permite que haja verificação em tempo de compilação ---
por exemplo, você pode usar _actors_ para acessar com segurança um estado mutável.
No entanto, adicionar concorrência a um código lento ou com bugs
não é uma garantia de que se tornará rápido ou correto.
Na verdade, adicionar concorrência pode até tornar seu código mais difícil de depurar.
No entanto, usar o suporte da linguagem para concorrência
em um código que precisa ser concorrente
permite que o Swift te ajude a detectar problemas em tempo de compilação.

O resto desse capitulo usa o termo *concorrência*
para se referir a essa combinação frequente de código assícnrono e paralelo.

> Nota: Se você já escreveu código concorrente antes,
> você pode ter o costume de trabalhar com threads.
> O modelo de concorrência em Swift é construído em cima de threads,
> mas você não interage diretamente com elas.
> Uma função assíncrona em Swift
> pode desistir da thread em que está sendo executada,
> o que permite que outra função assíncrona seja executada nessa thread
> enquanto a primeira função está bloqueada.
> Quando uma função assíncrona é retomada,
> Swift não garante qual thread
> essa função será executada.

Embora seja possível escrever código concorrente
sem usar o suporte nativo do Swift,
o código tende a ser mais difícil de ler.
Por exemplo, o código a seguir baixa uma lista de nomes de fotos,
baixa a primeira foto dessa lista,
e mostra essa foto para o usuário:

```swift
listPhotos(inGallery: "Summer Vacation") { photoNames in
    let sortedNames = photoNames.sorted()
    let name = sortedNames[0]
    downloadPhoto(named: name) { photo in
        show(photo)
    }
}
```




Mesmo neste caso simples,
porque o código deve ser escrito como uma série de _completion handlers_,
você acaba escrevendo _closures_ aninhadas.
Neste estilo,
código mais complexo com muitas closures aninhadas pode rapidamente se tornar inviável, ou difícil de manter.

## Definindo e Chamando Funções Assíncronas

Uma *função assíncrona* ou *método assíncrono*
é um tipo especial de função ou método
que pode ser suspenso enquanto está no meio da execução.
Isso está em contraste com funções e métodos síncronos comuns,
que são executados até a conclusão, geram um erro ou nunca retornam.
Uma função ou método assíncrono ainda faz uma dessas três coisas,
mas também pode pausar no meio quando está esperando por algo.
Dentro do corpo de uma função ou método assíncrono,
você marca cada um desses lugares onde a execução pode ser suspensa.

Para indicar que uma função ou método é assíncrono,
você escreve a palavra-chave `async` em sua declaração após seus parâmetros,
semelhante a como você usa `throws` para marcar uma função que pode lançar um erro.
Se a função ou método retornar um valor,
você escreve `async` antes da seta de retorno (`->`).
Por exemplo,
veja como você pode buscar os nomes das fotos em uma galeria:

```swift
func listPhotos(inGallery name: String) async -> [String] {
    let result = // ... código de requisição web assíncrono ...
    return result
}
```




Para uma função ou método que é assíncrono e também pode lançar erros,
você deve escrever `async` antes de `throws`.



Ao chamar um método assíncrono,
a execução é suspensa até que o método retorne.
Você escreve `await` na frente da chamada
para marcar o possível ponto de suspensão.
Isso é como escrever `try` ao chamar uma função que lança um erro,
para marcar a possível mudança no fluxo do programa se houver um erro.
Dentro de um método assíncrono,
o fluxo de execução é suspenso *somente* quando você chama outro método assíncrono ---
a suspensão nunca é implícita ou preventiva ---
o que significa que todos os pontos de suspensão possíveis são marcados com `await`.

Por exemplo,
O código abaixo busca os nomes de todas as imagens que estão na galeria
e após isso mostra a primeira imagem:

```swift
let photoNames = await listPhotos(inGallery: "Summer Vacation")
let sortedNames = photoNames.sorted()
let name = sortedNames[0]
let photo = await downloadPhoto(named: name)
show(photo)
```




Porque as funções `listPhotos(inGallery:)` e `downloadPhoto(named:)`
ambas precisam fazer requisicoes web,
elas podem levar um tempo significativo para serem finalizadas.
Tornando ambas funções assíncronas escrevendo `async` antes da seta de retorno
permite que o resto do código do App continue executando
enquanto esse código assíncrono espera até que a imagem esteja pronta.

Para entender a natureza concorrente do exemplo acima, aqui está uma possível ordem de execução:

- O código começa a ser executado a partir da primeira linha
   e vai até o primeiro `await`.
   Ele chama a função `listPhotos(inGallery:)`
   e suspende a execução enquanto aguarda o retorno dessa função.
- Enquanto a execução deste código estiver suspensa,
   algum outro código concorrente no mesmo programa é executado.
   Por exemplo, talvez uma tarefa em segundo plano de longa duração
   continua atualizando uma lista de novas galerias de fotos.
   Esse código também é executado até o próximo ponto de suspensão, marcado por `await`,
   ou até terminar.
- Após o retorno de `listPhotos(inGallery:)`,
   esse código continua a execução a partir desse ponto.
   Ele atribui o valor que foi retornado para `photoNames`.
- As linhas que definem `sortedNames` e `name`
   são códigos regulares e síncronos.
   Como nada está marcado como `await` nessas linhas,
   não há pontos de suspensão possíveis.
- O próximo `await` marca a chamada para a função `downloadPhoto(named:)`.
   Este código pausa a execução novamente até que a função retorne,
   dando a outro código concorrente a oportunidade de ser executado.
- Após o retorno de `downloadPhoto(named:)`,
   seu valor de retorno é atribuído a `photo`
   e então passado como argumento ao chamar a função `show(_:)`.

Os possíveis pontos de suspensão em seu código marcados com `await`
indicam que o trecho de código atual pode pausar a execução
enquanto aguarda o retorno da função ou método assíncrono.
Isso também é chamado de *yielding the thread*
porque, nos bastidores,
Swift suspende a execução do seu código na thread atual
e executa algum outro código nessa thread.
Como o código com `await` precisa ser capaz de suspender a execução,
apenas alguns lugares em seu programa podem chamar funções ou métodos assíncronos:

- Código no corpo de uma função, método ou propriedade assíncrona.
- Código no método estático `main()` de
  uma estrutura, classe ou enumeração marcada com `@main`.
- Código em uma tarefa filho não estruturada,
  conforme mostrado em <doc:Concurrency#Unstructured-Concurrency> abaixo.



O código entre os possíveis pontos de suspensão é executado sequencialmente,
sem a possibilidade de interrupção de outro código concorrente.
Por exemplo, o código abaixo move uma imagem de uma galeria para outra.

```swift
let firstPhoto = await listPhotos(inGallery: "Summer Vacation")[0]
add(firstPhoto toGallery: "Road Trip")
// Nesse ponto, firstPhoto está temporariamente em ambas galerias.
remove(firstPhoto fromGallery: "Summer Vacation")
```


Não há como outro código ser executado no meio
da chamada para `add(_:toGallery:)` e `remove(_:fromGallery:)`.
Durante esse tempo, a primeira foto aparece em ambas as galerias,
quebrando temporariamente uma das invariantes do aplicativo.
Para deixar ainda mais claro que este pedaço de código
não deve ter `await` adicionado a ele no futuro,
você pode refatorar esse código em uma função síncrona:

```swift
func move(_ photoName: String, from source: String, to destination: String) {
    add(photoName, to: destination)
    remove(photoName, from: source)
}
// ...
let firstPhoto = await listPhotos(inGallery: "Summer Vacation")[0]
move(firstPhoto, from: "Summer Vacation", to: "Road Trip")
```


No exemplo acima,
porque a função `move(_:from:to:)` é síncrona,
você garante que nunca poderá conter possíveis pontos de suspensão.
No futuro,
se você tentar adicionar código concorrente a esta função,
introduzindo um possível ponto de suspensão,
você verá um erro em tempo de compilação em vez de introduzir um bug.





> Nota: o método [Task.sleep(until:tolerance:clock:)](https://developer.apple.com/documentation/swift/task/sleep(until:tolerance:clock:))
> é útil ao escrever código simples
> para saber como concorrencia funciona.
> Este método não faz nada,
> mas espera pelo menos o número determinado de nanossegundos antes de retornar.
> Aqui está uma versão da função `listPhotos(inGallery:)`
> que usa `sleep(until:tolerance:clock:)` para simular a espera por uma operação de rede:
> 
> ```swift
> func listPhotos(inGallery name: String) async throws -> [String] {
>     try await Task.sleep(until: .now + .seconds(2), clock: .continuous)
>     return ["IMG001", "IMG99", "IMG0404"]
> }
> ```
> 
> 
> 



## Asynchronous Sequences

The `listPhotos(inGallery:)` function in the previous section
asynchronously returns the whole array at once,
after all of the array's elements are ready.
Another approach
is to wait for one element of the collection at a time
using an *asynchronous sequence*.
Here's what iterating over an asynchronous sequence looks like:

```swift
import Foundation

let handle = FileHandle.standardInput
for try await line in handle.bytes.lines {
    print(line)
}
```




Instead of using an ordinary `for`-`in` loop,
the example above writes `for` with `await` after it.
Like when you call an asynchronous function or method,
writing `await` indicates a possible suspension point.
A `for`-`await`-`in` loop potentially suspends execution
at the beginning of each iteration,
when it's waiting for the next element to be available.



In the same way that you can use your own types in a `for`-`in` loop
by adding conformance to the [Sequence](https://developer.apple.com/documentation/swift/sequence) protocol,
you can use your own types in a `for`-`await`-`in` loop
by adding conformance to the
[AsyncSequence](https://developer.apple.com/documentation/swift/asyncsequence) protocol.



## Calling Asynchronous Functions in Parallel



Calling an asynchronous function with `await`
runs only one piece of code at a time.
While the asynchronous code is running,
the caller waits for that code to finish
before moving on to run the next line of code.
For example,
to fetch the first three photos from a gallery,
you could await three calls to the `downloadPhoto(named:)` function
as follows:

```swift
let firstPhoto = await downloadPhoto(named: photoNames[0])
let secondPhoto = await downloadPhoto(named: photoNames[1])
let thirdPhoto = await downloadPhoto(named: photoNames[2])

let photos = [firstPhoto, secondPhoto, thirdPhoto]
show(photos)
```




This approach has an important drawback:
Although the download is asynchronous
and lets other work happen while it progresses,
only one call to `downloadPhoto(named:)` runs at a time.
Each photo downloads completely before the next one starts downloading.
However, there's no need for these operations to wait ---
each photo can download independently, or even at the same time.

To call an asynchronous function
and let it run in parallel with code around it,
write `async` in front of `let` when you define a constant,
and then write `await` each time you use the constant.

```swift
async let firstPhoto = downloadPhoto(named: photoNames[0])
async let secondPhoto = downloadPhoto(named: photoNames[1])
async let thirdPhoto = downloadPhoto(named: photoNames[2])

let photos = await [firstPhoto, secondPhoto, thirdPhoto]
show(photos)
```




In this example,
all three calls to `downloadPhoto(named:)` start
without waiting for the previous one to complete.
If there are enough system resources available, they can run at the same time.
None of these function calls are marked with `await`
because the code doesn't suspend to wait for the function's result.
Instead, execution continues
until the line where `photos` is defined ---
at that point, the program needs the results from these asynchronous calls,
so you write `await` to pause execution
until all three photos finish downloading.

Here's how you can think about the differences between these two approaches:

- Call asynchronous functions with `await`
  when the code on the following lines depends on that function's result.
  This creates work that is carried out sequentially.
- Call asynchronous functions with `async`-`let`
  when you don't need the result until later in your code.
  This creates work that can be carried out in parallel.
- Both `await` and `async`-`let`
  allow other code to run while they're suspended.
- In both cases, you mark the possible suspension point with `await`
  to indicate that execution will pause, if needed,
  until an asynchronous function has returned.

You can also mix both of these approaches in the same code.

## Tasks and Task Groups

A *task* is a unit of work
that can be run asynchronously as part of your program.
All asynchronous code runs as part of some task.
The `async`-`let` syntax described in the previous section
creates a child task for you.
You can also create a task group
and add child tasks to that group,
which gives you more control over priority and cancellation,
and lets you create a dynamic number of tasks.

Tasks are arranged in a hierarchy.
Each task in a task group has the same parent task,
and each task can have child tasks.
Because of the explicit relationship between tasks and task groups,
this approach is called *structured concurrency*.
Although you take on some of the responsibility for correctness,
the explicit parent-child relationships between tasks
lets Swift handle some behaviors like propagating cancellation for you,
and lets Swift detect some errors at compile time.

```
await withTaskGroup(of: Data.self) { taskGroup in
    let photoNames = await listPhotos(inGallery: "Summer Vacation")
    for name in photoNames {
        taskGroup.addTask { await downloadPhoto(named: name) }
    }
}
```




For more information about task groups,
see [TaskGroup](https://developer.apple.com/documentation/swift/taskgroup).





### Unstructured Concurrency

In addition to the structured approaches to concurrency
described in the previous sections,
Swift also supports unstructured concurrency.
Unlike tasks that are part of a task group,
an *unstructured task* doesn't have a parent task.
You have complete flexibility to manage unstructured tasks
in whatever way your program needs,
but you're also completely responsible for their correctness.
To create an unstructured task that runs on the current actor,
call the [Task.init(priority:operation:)](https://developer.apple.com/documentation/swift/task/3856790-init) initializer.
To create an unstructured task that's not part of the current actor,
known more specifically as a *detached task*,
call the [Task.detached(priority:operation:)](https://developer.apple.com/documentation/swift/task/3856786-detached) class method.
Both of these operations return a task that you can interact with ---
for example, to wait for its result or to cancel it.

```
let newPhoto = // ... some photo data ...
let handle = Task {
    return await add(newPhoto, toGalleryNamed: "Spring Adventures")
}
let result = await handle.value
```


For more information about managing detached tasks,
see [Task](https://developer.apple.com/documentation/swift/task).



### Task Cancellation

Swift concurrency uses a cooperative cancellation model.
Each task checks whether it has been canceled
at the appropriate points in its execution,
and responds to cancellation in whatever way is appropriate.
Depending on the work you're doing,
that usually means one of the following:

- Throwing an error like `CancellationError`
- Returning `nil` or an empty collection
- Returning the partially completed work

To check for cancellation,
either call [Task.checkCancellation()](https://developer.apple.com/documentation/swift/task/3814826-checkcancellation),
which throws `CancellationError` if the task has been canceled,
or check the value of [Task.isCancelled](https://developer.apple.com/documentation/swift/task/3814832-iscancelled)
and handle the cancellation in your own code.
For example,
a task that's downloading photos from a gallery
might need to delete partial downloads and close network connections.

To propagate cancellation manually,
call [Task.cancel()](https://developer.apple.com/documentation/swift/task/3851218-cancel).



## Actors

You can use tasks to break up your program into isolated, concurrent pieces.
Tasks are isolated from each other,
which is what makes it safe for them to run at the same time,
but sometimes you need to share some information between tasks.
Actors let you safely share information between concurrent code.

Like classes, actors are reference types,
so the comparison of value types and reference types
in <doc:ClassesAndStructures#Classes-Are-Reference-Types>
applies to actors as well as classes.
Unlike classes,
actors allow only one task to access their mutable state at a time,
which makes it safe for code in multiple tasks
to interact with the same instance of an actor.
For example, here's an actor that records temperatures:

```swift
actor TemperatureLogger {
    let label: String
    var measurements: [Int]
    private(set) var max: Int

    init(label: String, measurement: Int) {
        self.label = label
        self.measurements = [measurement]
        self.max = measurement
    }
}
```




You introduce an actor with the `actor` keyword,
followed by its definition in a pair of braces.
The `TemperatureLogger` actor has properties
that other code outside the actor can access,
and restricts the `max` property so only code inside the actor
can update the maximum value.

You create an instance of an actor
using the same initializer syntax as structures and classes.
When you access a property or method of an actor,
you use `await` to mark the potential suspension point.
For example:

```
let logger = TemperatureLogger(label: "Outdoors", measurement: 25)
print(await logger.max)
// Prints "25"
```


In this example,
accessing `logger.max` is a possible suspension point.
Because the actor allows only one task at a time to access its mutable state,
if code from another task is already interacting with the logger,
this code suspends while it waits to access the property.

In contrast,
code that's part of the actor doesn't write `await`
when accessing the actor's properties.
For example,
here's a method that updates a `TemperatureLogger` with a new temperature:

```
extension TemperatureLogger {
    func update(with measurement: Int) {
        measurements.append(measurement)
        if measurement > max {
            max = measurement
        }
    }
}
```


The `update(with:)` method is already running on the actor,
so it doesn't mark its access to properties like `max` with `await`.
This method also shows one of the reasons
why actors allow only one task at a time to interact with their mutable state:
Some updates to an actor's state temporarily break invariants.
The `TemperatureLogger` actor keeps track of
a list of temperatures and a maximum temperature,
and it updates the maximum temperature when you record a new measurement.
In the middle of an update,
after appending the new measurement but before updating `max`,
the temperature logger is in a temporary inconsistent state.
Preventing multiple tasks from interacting with the same instance simultaneously
prevents problems like the following sequence of events:

- Your code calls the `update(with:)` method.
   It updates the `measurements` array first.
- Before your code can update `max`,
   code elsewhere reads the maximum value and the array of temperatures.
- Your code finishes its update by changing `max`.

In this case,
the code running elsewhere would read incorrect information
because its access to the actor was interleaved
in the middle of the call to `update(with:)`
while the data was temporarily invalid.
You can prevent this problem when using Swift actors
because they only allow one operation on their state at a time,
and because that code can be interrupted
only in places where `await` marks a suspension point.
Because `update(with:)` doesn't contain any suspension points,
no other code can access the data in the middle of an update.

If you try to access those properties from outside the actor,
like you would with an instance of a class,
you'll get a compile-time error.
For example:

```
print(logger.max)  // Error
```


Accessing `logger.max` without writing `await` fails because
the properties of an actor are part of that actor's isolated local state.
Swift guarantees that
only code inside an actor can access the actor's local state.
This guarantee is known as *actor isolation*.





## Sendable Types

Tasks and actors let you divide a program
into pieces that can safely run concurrently.
Inside of a task or an instance of an actor,
the part of a program that contains mutable state,
like variables and properties,
is called a *concurrency domain*.
Some kinds of data can't be shared between concurrency domains,
because that data contains mutable state,
but it doesn't protect against overlapping access.

A type that can be shared from one concurrency domain to another
is known as a *sendable* type.
For example, it can be passed as an argument when calling an actor method
or be returned as the result of a task.
The examples earlier in this chapter didn't discuss sendability
because those examples use simple value types
that are always safe to share
for the data being passed between concurrency domains.
In contrast,
some types aren't safe to pass across concurrency domains.
For example, a class that contains mutable properties
and doesn't serialize access to those properties
can produce unpredictable and incorrect results
when you pass instances of that class between different tasks.

You mark a type as being sendable
by declaring conformance to the `Sendable` protocol.
That protocol doesn't have any code requirements,
but it does have semantic requirements that Swift enforces.
In general, there are three ways for a type to be sendable:

- The type is a value type,
  and its mutable state is made up of other sendable data ---
  for example, a structure with stored properties that are sendable
  or an enumeration with associated values that are sendable.
- The type doesn't have any mutable state,
  and its immutable state is made up of other sendable data ---
  for example, a structure or class that has only read-only properties.
- The type has code that ensures the safety of its mutable state,
  like a class that's marked `@MainActor`
  or a class that serializes access to its properties
  on a particular thread or queue.



For a detailed list of the semantic requirements,
see the [Sendable](https://developer.apple.com/documentation/swift/sendable) protocol reference.

Some types are always sendable,
like structures that have only sendable properties
and enumerations that have only sendable associated values.
For example:

```swift
struct TemperatureReading: Sendable {
    var measurement: Int
}

extension TemperatureLogger {
    func addReading(from reading: TemperatureReading) {
        measurements.append(reading.measurement)
    }
}

let logger = TemperatureLogger(label: "Tea kettle", measurement: 85)
let reading = TemperatureReading(measurement: 45)
await logger.addReading(from: reading)
```




Because `TemperatureReading` is a structure that has only sendable properties,
and the structure isn't marked `public` or `@usableFromInline`,
it's implicitly sendable.
Here's a version of the structure
where conformance to the `Sendable` protocol is implied:

```swift
struct TemperatureReading {
    var measurement: Int
}
```










