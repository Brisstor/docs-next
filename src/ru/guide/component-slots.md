# Слоты

> Подразумевается, что вы уже изучили и разобрались с разделом [Основы компонентов](component-basics.md). Если нет — прочитайте его сначала.

## Содержимое слота

Vue реализует API распределения контента, вдохновлённое текущим [черновиком спецификации веб-компонентов](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), используя элемент `<slot>` в качестве точек распространения контента.

Например, это позволит составлять такие компоненты:

```html
<todo-button>
  Добавить todo
</todo-button>
```

Для этого шаблон `<todo-button>` должен быть например таким:

```html
<!-- шаблон компонента todo-button -->
<button class="btn-primary">
  <slot></slot>
</button>
```

При отрисовке компонента `<slot></slot>` будет заменён на «Добавить todo».
	
```html
<!-- отрисованный HTML -->
<button class="btn-primary">
  Добавить todo
</button>
```

Строки — это только начало! Слоты могут содержать код любого шаблона, включая HTML:

```html
<todo-button>
  <!-- Добавим иконку Font Awesome -->
  <i class="fas fa-plus"></i>
  Добавить todo
</todo-button>
```

Или даже другие компоненты:

```html
<todo-button>
  <!-- Используем компонент для добавления иконки -->
  <font-awesome-icon name="plus"></font-awesome-icon>
  Добавить todo
</todo-button>
```

Если шаблон `<todo-button>` **не содержит** элемента `<slot>`, то любой переданный контент будет просто проигнорирован.

```html
<!-- шаблон компонента todo-button -->
<button class="btn-primary">
  Создать новый элемент
</button>
```

```html
<todo-button>
  <!-- Следующий текст НЕ БУДЕТ ОТОБРАЖЕН -->
  Добавить todo
</todo-button>
```

## Область видимости при отрисовке

Если необходимо использовать данные внутри слота, например:

```html
<todo-button>
  Удалить {{ item.name }}
</todo-button>
```

То этот слот имеет доступ к тем же свойствам экземпляра (т.е. к той же «области видимости»), что и остальная часть шаблона.

<img src="/images/slot.png" width="447" height="auto" style="display: block; margin: 0 auto; max-width: 100%" loading="lazy" alt="Slot explanation diagram">

Слот **не имеет доступа** к области видимости `<todo-button>`. Поэтому попытка получить `action` не сработает:

```html
<todo-button action="delete">
  Клик вызовет действие {{ action }} для элемента
  <!--
  Значение `action` будет undefined, потому что это содержимое передаётся
  ВНУТРЬ <todo-button>, а не определяется СНАРУЖИ компонента <todo-button>.
  -->
</todo-button>
```

Как правило, достаточно запомнить что:

> Всё в родительском шаблоне компилируется в области видимости родительского компонента; всё в дочернем шаблоне компилируется в области видимости дочернего компонента.

## Содержимое слота по умолчанию

Бывает полезным указать запасное содержимое слота (т.е. по умолчанию), которое будет отображаться только тогда, когда ничего не передавалось в слот. Например, в компоненте `<submit-button>`:

```html
<button type="submit">
  <slot></slot>
</button>
```

Было бы удобно если текст «Отправить» отображался внутри `<button>` большую часть времени. Чтобы сделать «Отправить» в качестве содержимого по умолчанию, необходимо поместить его между тегами `<slot>`:

```html
<button type="submit">
  <slot>Отправить</slot>
</button>
```

Теперь, при использовании `<submit-button>` в родительском компоненте и не указывая содержимое для слота:

```html
<submit-button></submit-button>
```

отобразится содержимое по умолчанию — «Отправить»:

```html
<button type="submit">
  Отправить
</button>
```

Но если указать содержимое:

```html
<submit-button>
  Сохранить
</submit-button>
```

Тогда оно будет использовано для отображения:

```html
<button type="submit">
  Сохранить
</button>
```

## Именованные слоты

Зачастую удобно иметь несколько слотов. К примеру, для компонента `<base-layout>` со следующим шаблоном:

```html
<div class="container">
  <header>
    <!-- Мы хотим отобразить контент заголовка здесь -->
  </header>
  <main>
    <!-- Мы хотим отобразить основной контент здесь -->
  </main>
  <footer>
    <!-- Мы хотим отобразить контент подвала здесь -->
  </footer>
</div>
```

В таких случаях элементу `<slot>` можно указать специальный атрибут `name`, который используется для присвоения уникального ID различным слотам, чтобы определить где какое содержимое необходимо отобразить:

```html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Обычный `<slot>` без `name` неявно имеет имя «default».

Для указания содержимого именованного слота, нужно использовать директиву `v-slot` на элементе `<template>`, передавая имя слота аргументом `v-slot`:

```html
<base-layout>
  <template v-slot:header>
    <h1>Здесь мог быть заголовок страницы</h1>
  </template>

  <template v-slot:default>
    <p>Параграф для основного контента.</p>
    <p>И ещё один.</p>
  </template>

  <template v-slot:footer>
    <p>Некая контактная информация</p>
  </template>
</base-layout>
```

Теперь всё внутри элементов `<template>` будет передано в соответствующие слоты.

Отрисованный HTML будет таким:

```html
<div class="container">
  <header>
    <h1>Здесь мог быть заголовок страницы</h1>
  </header>
  <main>
    <p>Параграф для основного контента.</p>
    <p>И ещё один.</p>
  </main>
  <footer>
    <p>Некая контактная информация</p>
  </footer>
</div>
```

Обратите внимание, что **`v-slot` можно добавлять только на `<template>`** (за [одним исключением](#abbreviated-syntax-for-lone-default-slots)).

## Слоты с ограниченной областью видимости

Иногда в содержимом слота полезно иметь доступ к данным, доступным только в дочернем компоненте. Это частый случай, когда компонент используется для отображения массива элементов, и требуется иметь возможность настройки отрисовки каждого элемента.

Например, есть компонент, содержащий список дел.

```js
app.component('todo-list', {
  data() {
    return {
      items: ['Feed a cat', 'Buy milk']
    }
  },
  template: `
    <ul>
      <li v-for="(item, index) in items">
        {{ item }}
      </li>
    </ul>
  `
})
```

Можно заменить <span v-pre>`{{ item }}`</span> на `<slot>`, чтобы настраивать отображение в родительском компоненте:

```html
<todo-list>
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```

Но это не сработает, потому что только компонент `<todo-list>` имеет доступ к `item`, а мы предоставляем содержимое слота из родительского.

Чтобы сделать `item` доступным для содержимого слота в родительском компоненте, необходимо добавить элемент `<slot>` и привязать к нему данные как атрибут:

```html
<ul>
  <li v-for="(item, index) in items">
    <slot :item="item"></slot>
  </li>
</ul>
```

Можно привязывать к `slot` только атрибутов, сколько нужно:

```html
<ul>
  <li v-for="(item, index) in items">
    <slot
      :item="item"
      :index="index"
      :another-attribute="anotherAttribute"
    ></slot>
  </li>
</ul>
```

Атрибуты, привязанные к элементу `<slot>`, называются **входными параметрами слота**. Теперь, в родительской области видимости, можно использовать `v-slot` со значением, чтобы указать имя для предоставленных слоту входных параметров:

```html
<todo-list>
  <template v-slot:default="slotProps">
    <i class="fas fa-check"></i>
    <span class="green">{{ slotProps.item }}</span>
  </template>
</todo-list>
```

<img src="/images/scoped-slot.png" width="611" height="auto" style="display: block; margin: 0 auto; max-width: 100%;" loading="lazy" alt="Scoped slot diagram">

В этом примере мы выбрали имя объекта `slotProps`, содержащего все входные параметры слота, но можно использовать любое другое, которое нравится.

### Сокращённый синтаксис для одиночного слота по умолчанию

В случаях, когда _только слоту по умолчанию_ предоставляется содержимое, тег компонента можно использовать в качестве шаблона слота. Это позволяет использовать `v-slot` непосредственно на компоненте:

```html
<todo-list v-slot:default="slotProps">
  <i class="fas fa-check"></i>
  <span class="green">{{ slotProps.item }}</span>
</todo-list>
```

Эту запись можно сократить ещё больше. Предполагается, что неуказанное явно содержимое относится к слоту по умолчанию, так и `v-slot` без аргумента означает слот по умолчанию:

```html
<todo-list v-slot="slotProps">
  <i class="fas fa-check"></i>
  <span class="green">{{ slotProps.item }}</span>
</todo-list>
```

Обратите внимание, что такой сокращённый синтаксис для слота по умолчанию **нельзя смешивать** с именованными слотами, потому что это приведёт к неоднозначности области видимости:

```html
<!-- НЕПРАВИЛЬНО, будет выкидывать предупреждение -->
<todo-list v-slot="slotProps">
  <i class="fas fa-check"></i>
  <span class="green">{{ slotProps.item }}</span>

  <template v-slot:other="otherSlotProps">
    slotProps НЕДОСТУПНЫ здесь
  </template>
</todo-list>
```

При наличии нескольких слотов лучше используйте полный синтаксис на основе `<template>` для _всех_ слотов:

```html
<todo-list>
  <template v-slot:default="slotProps">
    <i class="fas fa-check"></i>
    <span class="green">{{ slotProps.item }}</span>
  </template>

  <template v-slot:other="otherSlotProps">
    ...
  </template>
</todo-list>
```

### Деструктурирование входных параметров слота

Слоты с ограниченной областью видимости под капотом работают оборачивая содержимое слота в функцию, и передавая входные параметры одним аргументом:

```js
function(slotProps) {
  // ... содержимое слота ...
}
```

А значит, значение `v-slot` может принимать любое допустимое выражение JavaScript, которое может появиться в позиции аргумента определения функции. Поэтому можно использовать [деструктурирование ES2015](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#%D0%A0%D0%B0%D0%B7%D0%B1%D0%BE%D1%80_%D0%BE%D0%B1%D1%8A%D0%B5%D0%BA%D1%82%D0%BE%D0%B2) чтобы извлекать определённые входные параметры слота, например так:

```html
<todo-list v-slot="{ item }">
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```

Подобный подход делает шаблон намного чище, особенно если слот предоставляет множество входных параметров. Это также открывает и другие возможности, такие как переименование используемых входных параметров, например `item` в `todo`:

```html
<todo-list v-slot="{ item: todo }">
  <i class="fas fa-check"></i>
  <span class="green">{{ todo }}</span>
</todo-list>
```

Также можно определять значения по умолчанию, которые будут использоваться в случае, когда входной параметр слота не определён:

```html
<todo-list v-slot="{ item = 'Placeholder' }">
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```

## Динамическое имя слота

[Динамические аргументы директивы](template-syntax.md#dynamic-arguments) также работают и с `v-slot`, что позволяет указывать динамическое имя слота:

```html
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

## Сокращённая запись для именованных слотов

Аналогично `v-on` и `v-bind`, у `v-slot` есть своё сокращение, которое позволяет заменить всё перед аргументом (`v-slot:`) на специальный символ `#`. Например, можно записать `v-slot:header` как `#header`:

```html
<base-layout>
  <template #header>
    <h1>Здесь мог быть заголовок страницы</h1>
  </template>

  <template #default>
    <p>Параграф для основного контента.</p>
    <p>И ещё один.</p>
  </template>

  <template #footer>
    <p>Некая контактная информация</p>
  </template>
</base-layout>
```

Однако, как и в случае с другими директивами, сокращение можно использовать только при наличии аргумента. Это значит, что следующий синтаксис недопустим:

```html
<!-- НЕ ЗАРАБОТАЕТ и выкинет предупреждение -->
<todo-list #="{ item }">
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```

Необходимо всегда указывать имя слота, если хотите использовать сокращение:

```html
<todo-list #default="{ item }">
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```
