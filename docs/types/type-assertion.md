## Type Assertion
TypeScript дозволяє вам будь-яким способом перевизначати його виведене та проаналізоване представлення типів. Це робиться за допомогою механізму під назвою  "type assertion"(затвердження типу). Затвердження типу TypeScript полягає в тому, що ви просто повідомляєте компілятору, що ви знаєте про типи краще, ніж він сам, і що він не повинен здогадуватися про вас.

Загальним випадком використання підтвердження типу є перенесення коду з JavaScript на TypeScript. Для прикладу розглянемо такий шаблон:

```ts
var foo = {};
foo.bar = 123; // Error: property 'bar' does not exist on `{}`
foo.bas = 'hello'; // Error: property 'bas' does not exist on `{}`
```

Тут помилки коду, оскільки *визначений* тип `foo` є `{}`, тобто об’єкт із нульовими властивостями. Тому вам не дозволяється додавати до нього `bar` або `bas`. Ви можете виправити це просто за допомогою твердження типу `as Foo`:

```ts
interface Foo {
    bar: number;
    bas: string;
}
var foo = {} as Foo;
foo.bar = 123;
foo.bas = 'hello';
```

### `as foo` vs. `<foo>`
Спочатку було додано синтаксис `<foo>`. Це показано нижче:

```ts
var foo: any;
var bar = <string> foo; // bar is now of type "string"
```

Однак існує неоднозначність у граматиці мови під час використання тверджень стилю `<foo>` в JSX:

```ts
var foo = <string>bar;
</string>
```

Тому тепер для узгодженості рекомендується використовувати просто `as foo`.

### Type Assertion vs. Casting
Причина, чому це не називається "приведенням типів", полягає в тому, що *casting* зазвичай передбачає певну підтримку часу виконання. Однак *type assertions* — це суто конструкція під час компіляції та спосіб для вас надати підказки компілятору щодо того, як ви хочете, щоб ваш код аналізувався.

### Assertion considered harmful
У багатьох випадках твердження дозволить вам легко перенести застарілий код (і навіть скопіювати та вставити інші зразки коду у свою кодову базу). Однак ви повинні бути обережними з використанням тверджень. Візьміть наш оригінальний код як зразок, компілятор не захистить вас від того, щоб ви забули *фактично додати властивості, які ви обіцяли*:

```ts
interface Foo {
    bar: number;
    bas: string;
}
var foo = {} as Foo;
// ahhhh .... forget something?
```

Ще одна поширена думка полягає в тому, що твердження використовують як засіб *автодоповнення*, наприклад:

```ts
interface Foo {
    bar: number;
    bas: string;
}
var foo = <Foo>{
   // компілятор забезпечить автозаповнення властивостей Foo
   // Але розробнику легко забути додати всі властивості
   // Крім того, цей код може зламатись, якщо Foo буде рефакторинговано (наприклад, додано нову властивість)
};
```

але небезпека тут та сама, якщо ви забудете властивість, компілятор не скаржиться. Краще, якщо ви зробите наступне:

```ts
interface Foo {
    bar: number;
    bas: string;
}
var foo: Foo = {
    // компілятор забезпечить автозаповнення властивостей Foo
};
```

У деяких випадках вам може знадобитися створити тимчасову змінну, але принаймні ви не будете давати (можливо, помилкових) обіцянок, а натомість покладатиметеся на висновок типу, щоб перевірити за вас.

### Double assertion
Затвердження типу, незважаючи на те, що воно дещо небезпечне, як ми показали, не є *повністю відкритим*. наприклад наступне є дуже дійсним випадком використання (наприклад, користувач вважає, що передана подія буде більш конкретним випадком події), а твердження типу працює, як очікувалося:

```ts
function handler (event: Event) {
    let mouseEvent = event as MouseEvent;
}
```

Однак наступне, швидше за все, є помилкою, і TypeScript скаржиться, як показано, незважаючи на твердження користувача про тип:

```ts
function handler(event: Event) {
    let element = event as HTMLElement; // Error: Ні «Event», ні тип «HTMLElement» не можна призначити один одному
}
```

Якщо ви *все ще бажаєте цей тип, ви можете використати подвійне твердження*, але спочатку стверджуючи `unknown` (або `any`), який сумісний з усіма типами, і тому компілятор більше не скаржиться:

```ts
function handler(event: Event) {
    let element = event as unknown as HTMLElement; // Okay!
}
```

#### How TypeScript determines if a single assertion is not enough
Загалом, твердження від типу `S` до `T` є успішним, якщо або `S` є підтипом `T`, або `T` є підтипом `S`. Це робиться для забезпечення додаткової безпеки під час виконання тверджень типу ... абсолютно дикі твердження можуть бути дуже небезпечними, і вам потрібно використовувати `unknown` (або `any`), щоб бути такими небезпечними.

#### `as any as` vs `as unknown as`
Обидва *equally unsafe* з точки зору TypeScript. Використовуйте те, що робить вас щасливими. Міркування:

* Лінтери віддають перевагу `unknown` (з правилом `no-explicit-any`)
* `any` — це менше символів для введення, ніж `unknown`
