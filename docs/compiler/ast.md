## Вузол (Node)
Основним будівельним блоком Абстрактного Синтаксичного Дерева (AST) є Node. В загальному випадку, Node представляє нелематичні (non-terminals) символи в граматиці мови, проте деякі лематичні символи, такі як ідентифікатори і літерали, також зберігаються у дереві.

Документація вузла AST складається з двох основних елементів: `SyntaxKind`, який ідентифікує тип вузла в межах AST, і `interface`, API, яке вузол надає під час інстанціювання в AST.

Нижче наведені ключові елементи `interface Node`:
* `TextRange` визначають `початок` та `кінець` вузла в вихідному файлі.
* `parent?: Node` батьківський вузол в AST.

У інтерфейсі Node є й інші додаткові елементи, такі як прапорці (flags) та модифікатори (modifiers), які визначають додаткові властивості та характеристики вузлів абстрактного синтаксичного дерева (AST). Ці додаткові елементи надають більше інформації про вузли та їхні властивості, і можуть бути корисними для певних операцій і обробки AST. Докладнішу інформацію можна знайти за допомогою ключового слова `interface Node` в у вихідному коді. Однак, для навігації та обходу AST, основними та важливими членами Node є ті, які були згадані в початковому поясненні.

## Вихідний файл (SourceFile)

* `SyntaxKind.SourceFile`
* `interface SourceFile`.

Кожен `SourceFile` є верхнім вузлом абстрактного синтаксичного дерева (AST), який міститься в `Program`.
