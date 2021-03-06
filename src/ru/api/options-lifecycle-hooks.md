# Хуки жизненного цикла

:::warning Примечание
All lifecycle hooks automatically have their `this` context bound to the instance, so that you can access data, computed properties, and methods. This means **you should not use an arrow function to define a lifecycle method** (e.g. `created: () => this.fetchTodos()`). The reason is arrow functions bind the parent context, so `this` will not be the component instance as you expect and `this.fetchTodos` will be undefined.
:::

## beforeCreate

- **Тип:** `Function`

- **Подробности:**

  Called synchronously immediately after the instance has been initialized, before data observation and event/watcher setup.

- **См. также:** [Lifecycle Diagram](../guide/instance.md#lifecycle-diagram)

## created

- **Тип:** `Function`

- **Подробности:**

  Called synchronously after the instance is created. At this stage, the instance has finished processing the options which means the following have been set up: data observation, computed properties, methods, watch/event callbacks. However, the mounting phase has not been started, and the `$el` property will not be available yet.

- **См. также:** [Lifecycle Diagram](../guide/instance.md#lifecycle-diagram)

## beforeMount

- **Тип:** `Function`

- **Подробности:**

  Called right before the mounting begins: the `render` function is about to be called for the first time.

  **This hook is not called during server-side rendering.**

- **См. также:** [Lifecycle Diagram](../guide/instance.md#lifecycle-diagram)

## mounted

- **Тип:** `Function`

- **Подробности:**

  Called after the instance has been mounted, where element, passed to `Vue.createApp({}).mount()` is replaced by the newly created `vm.$el`. If the root instance is mounted to an in-document element, `vm.$el` will also be in-document when `mounted` is called.

  Note that `mounted` does **not** guarantee that all child components have also been mounted. If you want to wait until the entire view has been rendered, you can use [vm.$nextTick](../api/instance-methods.md#nexttick) inside of `mounted`:

  ```js
  mounted() {
    this.$nextTick(function () {
      // Code that will run only after the
      // entire view has been rendered
    })
  }
  ```

  **This hook is not called during server-side rendering.**

- **См. также:** [Lifecycle Diagram](../guide/instance.md#lifecycle-diagram)

## beforeUpdate

- **Тип:** `Function`

- **Подробности:**

  Called when data changes, before the DOM is patched. This is a good place to access the existing DOM before an update, e.g. to remove manually added event listeners.

  **This hook is not called during server-side rendering, because only the initial render is performed server-side.**

- **См. также:** [Lifecycle Diagram](../guide/instance.md#lifecycle-diagram)

## updated

- **Тип:** `Function`

- **Подробности:**

  Called after a data change causes the virtual DOM to be re-rendered and patched.

  The component's DOM will have been updated when this hook is called, so you can perform DOM-dependent operations here. However, in most cases you should avoid changing state inside the hook. To react to state changes, it's usually better to use a [computed property](options-data.md#computed) or [watcher](options-data.md#watch) instead.

  Note that `updated` does **not** guarantee that all child components have also been re-rendered. If you want to wait until the entire view has been re-rendered, you can use [vm.$nextTick](../api/instance-methods.md#nexttick) inside of `updated`:

  ```js
  updated() {
    this.$nextTick(function () {
      // Code that will run only after the
      // entire view has been re-rendered
    })
  }
  ```

  **This hook is not called during server-side rendering.**

- **См. также:** [Lifecycle Diagram](../guide/instance.md#lifecycle-diagram)

## activated

- **Тип:** `Function`

- **Подробности:**

  Called when a kept-alive component is activated.

  **This hook is not called during server-side rendering.**

- **См. также:**
  - [Dynamic Components - keep-alive](.../guide/component-dynamic-async.md#dynamic-components-with-keep-alive)

## deactivated

- **Тип:** `Function`

- **Подробности:**

  Called when a kept-alive component is deactivated.

  **This hook is not called during server-side rendering.**

- **См. также:**
  - [Dynamic Components - keep-alive](../guide/component-dynamic-async.md#dynamic-components-with-keep-alive)

## beforeUnmount

- **Тип:** `Function`

- **Подробности:**

  Called right before a component instance is unmounted. At this stage the instance is still fully functional.

  **This hook is not called during server-side rendering.**

- **См. также:** [Lifecycle Diagram](../guide/instance.md#lifecycle-diagram)

## unmounted

- **Тип:** `Function`

- **Подробности:**

  Called after a component instance has been unmounted. When this hook is called, all directives of the component instance have been unbound, all event listeners have been removed, and all child component instance have also been unmounted.

  **This hook is not called during server-side rendering.**

- **См. также:** [Lifecycle Diagram](../guide/instance.md#lifecycle-diagram)

## errorCaptured

- **Тип:** `(err: Error, instance: Component, info: string) => ?boolean`

- **Подробности:**

  Called when an error from any descendent component is captured. The hook receives three arguments: the error, the component instance that triggered the error, and a string containing information on where the error was captured. The hook can return `false` to stop the error from propagating further.

  :::tip Совет
  You can modify component state in this hook. However, it is important to have conditionals in your template or render function that short circuits other content when an error has been captured; otherwise the component will be thrown into an infinite render loop.
  :::

  **Error Propagation Rules**

  - By default, all errors are still sent to the global `config.errorHandler` if it is defined, so that these errors can still be reported to an analytics service in a single place.

  - If multiple `errorCaptured` hooks exist on a component's inheritance chain or parent chain, all of them will be invoked on the same error.

  - If the `errorCaptured` hook itself throws an error, both this error and the original captured error are sent to the global `config.errorHandler`.

  - An `errorCaptured` hook can return `false` to prevent the error from propagating further. This is essentially saying "this error has been handled and should be ignored." It will prevent any additional `errorCaptured` hooks or the global `config.errorHandler` from being invoked for this error.

## renderTracked

- **Тип:** `(e: DebuggerEvent) => void`

- **Подробности:**

  Called when virtual DOM re-render is tracked. The hook receives a `debugger event` as an argument. This event tells you what operation tracked the component and the target object and key of that operation.

- **Использование:**

  ```html
  <div id="app">
    <button v-on:click="addToCart">Add to cart</button>
    <p>Cart({{ cart }})</p>
  </div>
  ```

  ```js
  const app = Vue.createApp({
    data() {
      return {
        cart: 0
      }
    },
    renderTracked({ key, target, type }) {
      console.log({ key, target, type })
      /* This will be logged when component is rendered for the first time:
      {
        key: "cart",
        target: {
          cart: 0
        },
        type: "get"
      }
      */
    },
    methods: {
      addToCart() {
        this.cart += 1
      }
    }
  })

  app.mount('#app')
  ```

## renderTriggered

- **Тип:** `(e: DebuggerEvent) => void`

- **Подробности:**

  Called when virtual DOM re-render is triggered.Similarly to [`renderTracked`](#rendertracked), receives a `debugger event` as an argument. This event tells you what operation triggered the re-rendering and the target object and key of that operation.

- **Использование:**

  ```html
  <div id="app">
    <button v-on:click="addToCart">Add to cart</button>
    <p>Cart({{ cart }})</p>
  </div>
  ```

  ```js
  const app = Vue.createApp({
    data() {
      return {
        cart: 0
      }
    },
    renderTriggered({ key, target, type }) {
      console.log({ key, target, type })
    },
    methods: {
      addToCart() {
        this.cart += 1
        /* This will cause renderTriggered call
          {
            key: "cart",
            target: {
              cart: 1
            },
            type: "set"
          }
        */
      }
    }
  })

  app.mount('#app')
  ```
