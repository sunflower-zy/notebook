# Map「映射」

**介绍**：

类似于 Object，最大区别是键可以为任何类型，包括对象，是一个值到另一个值的 `映射`

普通对象将对象设置为键时，对象会被隐式转为字符串`"[object Object]"`

```javascript
let map = new Map();

map.set("1", "str1"); // 字符串键
map.set(1, "num1"); // 数字键
map.set(true, "bool1"); // 布尔值键

// 还记得普通的 Object 吗? 它会将键转化为字符串
// Map 则会保留键的类型，所以下面这两个结果不同：
alert(map.get(1)); // 'num1'
alert(map.get("1")); // 'str1'

alert(map.size); // 3
```

**方法与属性**

-   `new Map()` —— 创建 map。
-   `map.set(key, value)` —— 根据键存储值。
-   `map.get(key)` —— 根据键来返回值，如果 `map` 中不存在对应的 `key`，则返回 `undefined`。
-   `map.has(key)` —— 如果 `key` 存在则返回 `true`，否则返回 `false`。
-   `map.delete(key)` —— 删除指定键的值。
-   `map.clear()` —— 清空 map。
-   `map.size` —— 返回当前元素个数。

> `Map` 使用 [SameValueZero](https://tc39.github.io/ecma262/#sec-samevaluezero) 算法来比较键是否相等。它和严格等于 `===` 差不多，但区别是 `NaN` 被看成是等于 `NaN`。所以 `NaN` 也可以被用作键。

> 每一次 `map.set` 调用都会返回 map 本身，所以我们可以进行“链式”调用：
>
> ```javascript
> map.set("1", "str1").set(1, "num1").set(true, "bool1");
> ```

**Map 迭代**

-   `map.keys()` —— 遍历并返回所有的键（returns an iterable for keys），
-   `map.values()` —— 遍历并返回所有的值（returns an iterable for values），
-   `map.entries()` —— 遍历并返回所有的实体（returns an iterable for entries）`[key, value]`，`for..of` 在默认情况下使用的就是这个。

```js
let recipeMap = new Map([
    ["cucumber", 500],
    ["tomatoes", 350],
    ["onion", 50],
]);

// 遍历所有的键（vegetables）
for (let vegetable of recipeMap.keys()) {
    alert(vegetable); // cucumber, tomatoes, onion
}

// 遍历所有的值（amounts）
for (let amount of recipeMap.values()) {
    alert(amount); // 500, 350, 50
}

// 遍历所有的实体 [key, value]
for (let entry of recipeMap) {
    // 与 recipeMap.entries() 相同
    alert(entry); // cucumber,500 (and so on)
}
```

> 迭代的顺序与插入值的顺序相同。与普通的 `Object` 不同，`Map` 保留了此顺序。

> `Map` 有内置的 `forEach` 方法，与 `Array` 类似：
>
> ```javascript
> // 对每个键值对 (key, value) 运行 forEach 函数
> recipeMap.forEach((value, key, map) => {
>     alert(`${key}: ${value}`); // cucumber: 500 etc
> });
> ```

**从对象创建 Map**

当创建一个 `Map` 后，我们可以传入一个带有键值对的数组（或其它可迭代对象）来进行初始化，如下所示：

```javascript
// 键值对 [key, value] 数组
let map = new Map([
    ["1", "str1"],
    [1, "num1"],
    [true, "bool1"],
]);

alert(map.get("1")); // str1
```

如果我们想从一个已有的普通对象（plain object）来创建一个 `Map`，那么我们可以使用内建方法 [Object.entries(obj)](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)，该方法返回对象的键/值对数组，该数组格式完全按照 `Map` 所需的格式。

```js
let obj = {
    name: "John",
    age: 30,
};

let map = new Map(Object.entries(obj));

alert(map.get("name")); // John
```

**从 Map 创建对象**

`Object.fromEntries` 方法的作用是相反的：给定一个具有 `[key, value]` 键值对的数组，它会根据给定数组创建一个对象：

```javascript
let prices = Object.fromEntries([
    ["banana", 1],
    ["orange", 2],
    ["meat", 4],
]);

// 现在 prices = { banana: 1, orange: 2, meat: 4 }

alert(prices.orange); // 2
```

我们可以使用 `Object.fromEntries` 从 `Map` 得到一个普通对象（plain object）。

在 `Map` 中存储一些数据，把这些数据传给需要普通对象（plain object）

```javascript
let map = new Map();
map.set("banana", 1);
map.set("orange", 2);
map.set("meat", 4);

let obj = Object.fromEntries(map.entries()); // 创建一个普通对象（plain object）(*)

// 完成了！
// obj = { banana: 1, orange: 2, meat: 4 }

alert(obj.orange); // 2
```

调用 `map.entries()` 将返回一个可迭代的键/值对，这刚好是 `Object.fromEntries` 所需要的格式。

可以把带 `(*)` 这一行写得更短：

```javascript
let obj = Object.fromEntries(map); // 省掉 .entries()
```

上面的代码作用也是一样的，因为 `Object.fromEntries` 期望得到一个可迭代对象作为参数，而不一定是数组。并且 `map` 的标准迭代会返回跟 `map.entries()` 一样的键/值对。因此，我们可以获得一个普通对象（plain object），其键/值对与 `map` 相同。

# Set「集合」

`Set` 是一个特殊的类型集合 —— “值的集合”（没有键），它的每一个值只能出现一次。

**主要方法如下：**

-   `new Set(iterable)` —— 创建一个 `set`，如果提供了一个 `iterable` 对象（通常是数组），将会从数组里面复制值到 `set` 中。
-   `set.add(value)` —— 添加一个值，返回 set 本身
-   `set.delete(value)` —— 删除值，如果 `value` 在这个方法调用的时候存在则返回 `true` ，否则返回 `false`。
-   `set.has(value)` —— 如果 `value` 在 set 中，返回 `true`，否则返回 `false`。
-   `set.clear()` —— 清空 set。
-   `set.size` —— 返回元素个数。

它的主要特点是，重复使用同一个值调用 `set.add(value)` 并不会发生什么改变。这就是 `Set` 里面的每一个值只出现一次的原因。

**Set‘迭代**

我们可以使用 `for..of` 或 `forEach` 来遍历 Set：

```javascript
let set = new Set(["oranges", "apples", "bananas"]);

for (let value of set) alert(value);

// 与 forEach 相同：
set.forEach((value, valueAgain, set) => {
    alert(value);
});
```

注意一件有趣的事儿。`forEach` 的回调函数有三个参数：一个 `value`，然后是 **同一个值** `valueAgain`，最后是目标对象。没错，同一个值在参数里出现了两次。

`forEach` 的回调函数有三个参数，是为了与 `Map` 兼容。当然，这看起来确实有些奇怪。但是这对在特定情况下轻松地用 `Set` 代替 `Map` 很有帮助，反之亦然。

`Map` 中用于迭代的方法在 `Set` 中也同样支持：

-   `set.keys()` —— 遍历并返回所有的值（returns an iterable object for values），
-   `set.values()` —— 与 `set.keys()` 作用相同，这是为了兼容 `Map`，
-   `set.entries()` —— 遍历并返回所有的实体（returns an iterable object for entries）`[value, value]`，它的存在也是为了兼容 `Map`。

# Map 与 Set 总结

`Map` —— 是一个带键的数据项的集合。

方法和属性如下：

-   `new Map([iterable])` —— 创建 map，可选择带有 `[key,value]` 对的 `iterable`（例如数组）来进行初始化。
-   `map.set(key, value)` —— 根据键存储值。
-   `map.get(key)` —— 根据键来返回值，如果 `map` 中不存在对应的 `key`，则返回 `undefined`。
-   `map.has(key)` —— 如果 `key` 存在则返回 `true`，否则返回 `false`。
-   `map.delete(key)` —— 删除指定键的值。
-   `map.clear()` —— 清空 map 。
-   `map.size` —— 返回当前元素个数。

与普通对象 `Object` 的不同点：

-   任何键、对象都可以作为键。
-   有其他的便捷方法，如 `size` 属性。

`Set` —— 是一组唯一值的集合。

方法和属性：

-   `new Set([iterable])` —— 创建 set，可选择带有 `iterable`（例如数组）来进行初始化。
-   `set.add(value)` —— 添加一个值（如果 `value` 存在则不做任何修改），返回 set 本身。
-   `set.delete(value)` —— 删除值，如果 `value` 在这个方法调用的时候存在则返回 `true` ，否则返回 `false`。
-   `set.has(value)` —— 如果 `value` 在 set 中，返回 `true`，否则返回 `false`。
-   `set.clear()` —— 清空 set。
-   `set.size` —— 元素的个数。

在 `Map` 和 `Set` 中迭代总是按照值插入的顺序进行的，所以我们不能说这些集合是无序的，但是我们不能对元素进行重新排序，也不能直接按其编号来获取元素。

# WeakMap「弱映射」

## 与 Map 不同

1. `WeakMap` 的键必须是对象，不能是原始值：

    ```javascript
    let weakMap = new WeakMap();

    let obj = {};

    weakMap.set(obj, "ok"); // 正常工作（以对象作为键）

    // 不能使用字符串作为键
    weakMap.set("test", "Whoops"); // Error，因为 "test" 不是一个对象
    ```

在 weakMap 中使用一个对象作为键，并且没有其他对这个对象的引用 —— 该对象将会被从内存（和 map）中自动清除。

```javascript
let john = { name: "John" };

let weakMap = new WeakMap();
weakMap.set(john, "...");

john = null; // 覆盖引用

// john 被从内存中删除了！
```

2. `WeakMap` 不支持迭代以及 `keys()`，`values()` 和 `entries()` 方法。所以没有办法获取 `WeakMap` 的所有键或值。

`WeakMap` 只有以下的方法：

-   `weakMap.get(key)`
-   `weakMap.set(key, value)`
-   `weakMap.delete(key)`
-   `weakMap.has(key)`

为什么会有这种限制呢？这是技术的原因。如果一个对象丢失了其它所有引用（就像上面示例中的 `john`），那么它就会被垃圾回收机制自动回收。但是在从技术的角度并不能准确知道 **何时会被回收**。

这些都是由 JavaScript 引擎决定的。JavaScript 引擎可能会选择立即执行内存清理，如果现在正在发生很多删除操作，那么 JavaScript 引擎可能就会选择等一等，稍后再进行内存清理。因此，从技术上讲，`WeakMap` 的当前元素的数量是未知的。JavaScript 引擎可能清理了其中的垃圾，可能没清理，也可能清理了一部分。因此，暂不支持访问 `WeakMap` 的所有键/值的方法。

## **应用**

`WeakMap` 的主要应用场景是 **额外数据的存储**。

假如我们正在处理一个“属于”另一个代码的一个对象，也可能是第三方库，并想存储一些与之相关的数据，那么这些数据就应该与这个对象共存亡 —— 这时候 `WeakMap` 正是我们所需要的利器。

我们将这些数据放到 `WeakMap` 中，并使用该对象作为这些数据的键，那么当该对象被垃圾回收机制回收后，这些数据也会被自动清除。

# WeakSet「弱集合」

`WeakSet` 的表现类似：

-   与 `Set` 类似，但是我们只能向 `WeakSet` 添加对象（而不能是原始值）。
-   对象只有在其它某个（些）地方能被访问的时候，才能留在 set 中。
-   跟 `Set` 一样，`WeakSet` 支持 `add`，`has` 和 `delete` 方法，但不支持 `size` 和 `keys()`，并且不可迭代。

变“弱（weak）”的同时，它也可以作为额外的存储空间。但并非针对任意数据，而是针对“是/否”的事实。`WeakSet` 的元素可能代表着有关该对象的某些信息。

`WeakMap` 和 `WeakSet` 最明显的局限性就是不能迭代，并且无法获取所有当前内容。那样可能会造成不便，但是并不会阻止 `WeakMap/WeakSet` 完成其主要工作 — 成为在其它地方管理/存储“额外”的对象数据。

# WeakMap 与 WeakSet 总结

`WeakMap` 是类似于 `Map` 的集合，它仅允许对象作为键，并且一旦通过其他方式无法访问它们，便会将它们与其关联值一同删除。

`WeakSet` 是类似于 `Set` 的集合，它仅存储对象，并且一旦通过其他方式无法访问它们，便会将其删除。

它们都不支持引用所有键或其计数的方法和属性。仅允许单个操作。

`WeakMap` 和 `WeakSet` 被用作“主要”对象存储之外的“辅助”数据结构。一旦将对象从主存储器中删除，如果该对象仅被用作 `WeakMap` 或 `WeakSet` 的键，那么它将被自动清除。
