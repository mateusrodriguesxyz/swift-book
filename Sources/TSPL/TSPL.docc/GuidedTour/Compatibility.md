# Compatibilidade de Versões

Saiba quais funcionalidades estão disponíveis em modos de linguagem mais antigos.

Este livro descreve Swift 6,
a versão padrão que está incluída no Xcode 16.
Você pode usar o compilador Swift 6 para escrever código
escrito em Swift 6, Swift 5, Swift 4.2 ou Swift 4.

Quando você usa o compilador Swift 6
para escrever código que usa o modo de linguagem Swift 5,
você pode usar os novos recursos de Swift 6 ---
eles são habilitados por padrão ou por uma _flag_ de funcionalidade futura.
No entanto, para habilitar a verificação de concorrência estrita,
você precisa atualizar para o modo de linguagem Swift 6.

Quando você usa o Xcode 14 para compilar o código Swift 4 e Swift 4.2,
a maioria das funcionalidades do Swift 5.7 está disponível.
Dito isto,
as seguintes alterações estão disponíveis apenas para código que usa Swift 5.7 ou posterior:

Além disso,
quando você usa o Xcode 15.3 para escrever código em Swift 4 e Swift 4.2,
a maioria das funcionalidades do Swift 5 ainda está disponível.
Dito isso,
as seguintes alterações estão disponíveis apenas para código
que usa o modo de linguagem Swift 5:

- As funções que retornam um tipo opaco requerem o _runtime_ do Swift 5.1.
- A expressão `try?` não introduz um nível extra de opcionalidade
para expressões que já retornam opcionais.
- Expressões de inicialização literais de inteiros grandes são inferidas
para ser do tipo inteiro correto.
Por exemplo, `UInt64(0xffff_ffff_ffff_ffff)` avalia o valor correto
ao invés de causar _overflow_.

Concorrência requer Swift 5.7 ou posterior,
e uma versão da biblioteca padrão Swift
que fornece os tipos de concorrência correspondentes.
Em plataformas Apple, defina um _target_ de _deployment_
de pelo menos iOS 13, macOS 10.15, tvOS 13 ou watchOS 6.

Um _target_ escrito em Swift 5.7 pode depender
de um _target_ escrito em Swift 4.2 ou Swift 4,
e vice-versa.
Isso significa que, se você tiver um grande projeto
que é dividido em várias _frameworks_,
você pode migrar seu código d Swift 4 para o Swift 5.7
um _framework_ de cada vez.

Isso significa que, se você tem um projeto grande
dividido em várias _frameworks_,
você pode migrar seu código para uma versão de linguagem mais recente
um _framework_ por vez.

<!--
This source file is part of the Swift.org open source project

Copyright (c) 2014 - 2022 Apple Inc. and the Swift project authors
Licensed under Apache License v2.0 with Runtime Library Exception

See https://swift.org/LICENSE.txt for license information
See https://swift.org/CONTRIBUTORS.txt for the list of Swift project authors
-->
