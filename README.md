# clean-code-javascript

## 目录

1. [介绍](#介绍)
2. [变量](#变量)
3. [函数](#函数)
4. [对象和数据结构](#对象和数据结构)
5. [类](#类)
6. [原则](#原则)
7. [测试](#测试)
8. [并发编程](#并发编程)
9. [错误处理](#错误处理哦)
10. [格式化](#格式化)
11. [注释](#注释)
12. [翻译](#翻译)

## 介绍

这是从 Robert C. Martin 的书[《Clean Code》](https://book.douban.com/subject/4199741/)中提取出的软件工程原则，适用于 JavaScript。它不是一份风格指南，而是一个关于在 JavaScript 中生成[可读、可复用和可重构](https://github.com/ryanmcdermott/3rs-of-software-architecture)软件的指南。

这里的每个原则都不必严格遵循，甚至更少的原则会被普遍认同。这些只是指南，但它们是在 Clean Code 作者多年的集体经验中被编码的。

我们的软件工程工艺刚刚超过50年，我们仍在学习很多东西。当软件架构与建筑本身一样古老时，也许我们将有更严格的规则要遵循。现在，让这些准则作为一个基准，来评估您和您的团队生成的 JavaScript 代码的质量。

还有一件事：知道这些不会立刻让你成为一个更好的软件开发人员，而且与它们一起工作多年并不意味着你不会犯错误。每一段代码都是一个初稿，就像湿粘土被塑造成最终形态一样。最后，当我们与同事一起审核时，我们会切削掉不完美之处。不要因为需要改进的初稿而自责。相反，应该改进代码本身！

## 变量

### 使用有意义且可读的变量名称

**不好的：**

```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD')
```

**好的：**

```javascript
const currentDate = moment().format('YYYY/MM/DD')
```

**[⬆ 回到顶部](#目录)**

### 使用相同的词汇来表示相同类型的变量

**不好的：**

```javascript
getUserInfo()
getClientData()
getCustomerRecord()
```

**好的：**

```javascript
getUser()
```

**[⬆ 回到顶部](#目录)**

### 使用可搜索的名称

我们将阅读比编写更多的代码。编写易于阅读和搜索的代码非常重要。如果没有为我们程序理解起到重要作用的变量命名，将会给读者带来困扰。请确保您的变量名是可搜索的。像 [buddy.js](https://github.com/danielstjules/buddy.js) 和 [ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md) 这样的工具可以帮助识别未命名的常量。

**不好的：**

```javascript
// 86400000 代表什么意思？
setTimeout(blastOff, 86400000)
```

**好的：**

```javascript
// 将它们声明为大写的常量名称
const MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000 // 86400000;

setTimeout(blastOff, MILLISECONDS_PER_DAY)
```

**[⬆ 回到顶部](#目录)**

### 使用解释性变量

**不好的：**

```javascript
const address = 'One Infinite Loop, Cupertino 95014'
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
)
```

**好的：**

```javascript
const address = 'One Infinite Loop, Cupertino 95014'
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
const [_, city, zipCode] = address.match(cityZipCodeRegex) || []
saveCityZipCode(city, zipCode)
```

**[⬆ 回到顶部](#目录)**

### Avoid Mental Mapping

明确比隐含更好。

**不好的：**

```javascript
const locations = ['Austin', 'New York', 'San Francisco']
locations.forEach((l) => {
  doStuff()
  doSomeOtherStuff()
  // ...
  // ...
  // ...
  // l 是什么意思？
  dispatch(l)
})
```

**好的：**

```javascript
const locations = ['Austin', 'New York', 'San Francisco']
locations.forEach((location) => {
  doStuff()
  doSomeOtherStuff()
  // ...
  // ...
  // ...
  dispatch(location)
})
```

**[⬆ 回到顶部](#目录)**

### 不要添加不必要的上下文

如果你的类/对象名称已经能表达一些信息，就不要在变量名中重复这些信息。

**不好的：**

```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
}

function paintCar(car, color) {
  car.carColor = color
}
```

**好的：**

```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
}

function paintCar(car, color) {
  car.color = color
}
```

**[⬆ 回到顶部](#目录)**

### 使用默认参数而不是短路或条件语句

Default parameters are often cleaner than short circuiting. Be aware that if you
use them, your function will only provide default values for `undefined`
arguments. Other "falsy" values such as `''`, `""`, `false`, `null`, `0`, and
`NaN`, will not be replaced by a default value.

默认参数通常比短路语句更清晰简洁。请注意，如果使用默认参数，函数只会为 `undefined` 参数提供默认值。其他 "falsy" 值，例如 `''`、`""`、`false`、`null`、`0` 和 `NaN`，将不会被替换为默认值。

**不好的：**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.'
  // ...
}
```

**好的：**

```javascript
function createMicrobrewery(name = 'Hipster Brew Co.') {
  // ...
}
```

**[⬆ 回到顶部](#目录)**

## **函数**

### 函数参数最好不要超过两个

限制函数参数的数量非常重要，因为这会使函数的测试更容易。超过三个参数会导致组合爆炸，需要测试许多带有每个单独参数的不同情况。

一个或两个参数是理想情况，如果可能的话应该避免三个参数。如果有超过两个参数，那么你的函数通常会尝试做太多事情。在不是这种情况的情况下，大多数情况下高级对象就足够作为参数。

由于 JavaScript 允许您在不使用大量类模板的情况下即时创建对象，因此如果发现需要大量参数，则可以使用对象。

为了明确函数期望的属性，可以使用 ES2015/ES6 解构语法。这样做有几个优点：

1. 当有人查看函数签名时，立即清楚正在使用哪些属性。
2. 它可用于模拟命名参数。
3. 解构还会克隆传递到函数的参数对象的指定原始值。这有助于防止副作用。注意：从参数对象解构的对象和数组不会被克隆。
4. Linters 可以警告您未使用的属性，而没有解构会很难实现。

**不好的：**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu('Foo', 'Bar', 'Baz', true)
```

**好的：**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
})
```

**[⬆ 回到顶部](#目录)**

### 函数应该只做一件事

这是软件工程中最重要的规则。当函数做多个事情时，它们更难以组合、测试和理解。当你可以将一个函数隔离到一个动作时，它可以很容易地重构，你的代码将会更清晰。如果你从本指南中只学到了这一点，那么你就领先了许多开发者。

**不好的：**

```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client)
    if (clientRecord.isActive()) {
      email(client)
    }
  })
}
```

**好的：**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email)
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client)
  return clientRecord.isActive()
}
```

**[⬆ 回到顶部](#目录)**

### 函数名称应该表达函数的作用

**不好的：**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date()

// 从函数名称很难看出来添加了什么东西
addToDate(date, 1)
```

**好的：**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date()
addMonthToDate(1, date)
```

**[⬆ 回到顶部](#目录)**

### 函数的抽象级别应该只有一层

如果你的函数抽象级别超过一层，通常说明它做的事情太多了。将函数拆分成多个子函数可以提高代码的可重用性和易于测试。

**不好的：**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ]

  const statements = code.split(' ')
  const tokens = []
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    })
  })

  const ast = []
  tokens.forEach((token) => {
    // lex...
  })

  ast.forEach((node) => {
    // parse...
  })
}
```

**好的：**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code)
  const syntaxTree = parse(tokens)
  syntaxTree.forEach((node) => {
    // parse...
  })
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ]

  const statements = code.split(' ')
  const tokens = []
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push(/* ... */)
    })
  })

  return tokens
}

function parse(tokens) {
  const syntaxTree = []
  tokens.forEach((token) => {
    syntaxTree.push(/* ... */)
  })

  return syntaxTree
}
```

**[⬆ 回到顶部](#目录)**

### 删除重复代码

尽你最大的努力避免重复代码。重复代码是不好的，因为它意味着如果你需要改变某些逻辑，就有不止一个地方可以更改。

想象一下，如果你经营一家餐厅，并且要追踪你的库存：所有的番茄、洋葱、大蒜、香料等等。如果你有多个列表来保存这些，那么每次你服务一道番茄菜肴时，所有的列表都必须更新。如果你只有一个列表，只需要更新一个地方！

通常，你有重复代码，是因为你有两个或更多稍微不同的东西，它们有很多共同点，但它们的不同之处迫使你有两个或更多单独的函数，这些函数大部分是相同的。删除重复代码意味着创建一个抽象，可以用一个函数/模块/类来处理这组不同的事物。

正确的抽象很关键，这就是为什么你应该遵循 _Classes_ 部分中列出的 SOLID 原则。不好的抽象可能比重复的代码更糟糕，所以要小心！话虽如此，如果你可以创建一个好的抽象，请这么做！不要重复自己，否则你会发现每次你想改变一件事情时，需要更新多个地方。

**不好的：**

```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary()
    const experience = developer.getExperience()
    const githubLink = developer.getGithubLink()
    const data = {
      expectedSalary,
      experience,
      githubLink
    }

    render(data)
  })
}

function showManagerList(managers) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary()
    const experience = manager.getExperience()
    const portfolio = manager.getMBAProjects()
    const data = {
      expectedSalary,
      experience,
      portfolio
    }

    render(data)
  })
}
```

**好的：**

```javascript
function showEmployeeList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary()
    const experience = employee.getExperience()

    const data = {
      expectedSalary,
      experience
    }

    switch (employee.type) {
      case 'manager':
        data.portfolio = employee.getMBAProjects()
        break
      case 'developer':
        data.githubLink = employee.getGithubLink()
        break
    }

    render(data)
  })
}
```

**[⬆ 回到顶部](#目录)**

### 使用 `Object.assign` 设置默认对象

**不好的：**

```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
}

function createMenu(config) {
  config.title = config.title || 'Foo'
  config.body = config.body || 'Bar'
  config.buttonText = config.buttonText || 'Baz'
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true
}

createMenu(menuConfig)
```

**好的：**

```javascript
const menuConfig = {
  title: 'Order',
  // 用户没有包括 body 属性
  buttonText: 'Send',
  cancellable: true
}

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: 'Foo',
      body: 'Bar',
      buttonText: 'Baz',
      cancellable: true
    },
    config
  )
  return finalConfig
  // 现在 config 等于：{title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig)
```

**[⬆ 回到顶部](#目录)**

### 不要在函数参数中使用标志

标志告诉用户这个函数做了不止一件事情。函数应该只做一件事情。如果函数基于布尔值遵循不同的代码路径，应该将它们拆分成不同的函数。

**不好的：**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`)
  } else {
    fs.create(name)
  }
}
```

**好的：**

```javascript
function createFile(name) {
  fs.create(name)
}

function createTempFile(name) {
  createFile(`./temp/${name}`)
}
```

**[⬆ 回到顶部](#目录)**

### 避免副作用：Part 1

如果一个函数除了接受一个值并返回另一个值或多个值之外，还做了其他事情，那么它就产生了副作用。副作用可以是写入文件、修改某个全局变量或意外地把你所有的钱转给陌生人。

当然，在程序中确实需要副作用。就像前面的例子，你可能需要写入文件。你需要做的是把它们集中到一个地方。不要让几个函数和类都写入同一个文件。只要有一个服务写入就行了。

主要的问题在于要避免共享对象之间的状态而没有任何结构，使用可被任何人写入的可变数据类型以及没有将副作用集中到一个地方。如果你能做到这一点，你会比大多数其他程序员更快乐。

**不好的：**

```javascript
// 在下面的函数中引用的全局变量。
// 如果我们有另一个使用这个变量名的函数，现在它就是一个数组了，它可能会破坏它。
let name = 'Ryan McDermott'

function splitIntoFirstAndLastName() {
  name = name.split(' ')
}

splitIntoFirstAndLastName()

console.log(name) // ['Ryan', 'McDermott'];
```

**好的：**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ')
}

const name = 'Ryan McDermott'
const newName = splitIntoFirstAndLastName(name)

console.log(name) // 'Ryan McDermott';
console.log(newName) // ['Ryan', 'McDermott'];
```

**[⬆ 回到顶部](#目录)**

### 避免副作用：Part 2

在 JavaScript 中，一些值是不可变的（immutable），一些是可变的（mutable）。对象和数组是两种可变的值，因此在将它们作为参数传递给函数时，需要小心处理。一个 JavaScript 函数可以更改对象的属性或者改变数组的内容，这可能会在其他地方导致错误。

假设有一个函数接受一个数组参数表示一个购物车。如果函数在这个购物车数组中做了任何修改，例如添加要购买的物品，那么使用相同 `cart` 数组的任何其他函数都会受到这个添加的影响。这可能很好，但也可能很糟糕。现在假设有一个不好的情况：

用户点击“购买”按钮，调用一个 `purchase` 函数，该函数产生一个网络请求并将 `cart` 数组发送到服务器。由于网络连接不好，`purchase` 函数必须一直重试请求。那么，在网络请求开始之前，如果用户不小心点击了一个他们实际上不想要的商品的“添加到购物车”按钮，那么如果发生这种情况并且网络请求开始，那么购买函数将发送这个错误添加的物品，因为 `cart` 数组被修改了。

一个好的解决方案是 `addItemToCart` 函数总是克隆 `cart`，对其进行修改并返回克隆。这将确保仍在使用旧购物车的函数不会受到更改的影响。

有两个注意事项要提到这种方法：

1. 有可能存在修改输入对象的情况，但当你采用这种编程实践时，你会发现这些情况非常少见。大多数东西可以重构以没有副作用！

2. 克隆大型对象可能非常昂贵，从性能的角度来看，这不是一个大问题。幸运的是，有很多[优秀的库](https://facebook.github.io/immutable-js/)可以让这种编程方法变得快速，并且不像手动克隆对象和数组那样消耗大量内存。

**不好的：**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() })
}
```

**好的：**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }]
}
```

**[⬆ 回到顶部](#目录)**

### 不要写全局函数

在 JavaScript 中污染全局变量是一种不好的做法，因为你可能会与另一个库产生冲突，而使用你的 API 的用户在生产环境中遇到异常之前都不会意识到这一点。让我们考虑一个例子：假设你想要扩展 JavaScript 的本地 Array 方法，添加一个 `diff` 方法，该方法可以显示两个数组之间的差异。你可以将新函数写到 `Array.prototype` 中，但这可能会与试图执行相同操作的另一个库产生冲突。如果那个库只是使用 `diff` 来查找数组的第一个和最后一个元素之间的差异呢？这就是为什么最好只使用ES2015/ES6类并简单地扩展全局 `Array` 的原因。

**不好的：**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray)
  return this.filter((elem) => !hash.has(elem))
}
```

**好的：**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray)
    return this.filter((elem) => !hash.has(elem))
  }
}
```

**[⬆ 回到顶部](#目录)**

### 推荐使用函数式编程而不是命令式编程

JavaScript 并不像 Haskell 一样是一种函数式语言，但它具有一定的函数式风格。函数式语言可以更清晰、更易于测试。在可以使用时，请使用这种编程风格。

**不好的：**

```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  },
  {
    name: 'Suzie Q',
    linesOfCode: 1500
  },
  {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  },
  {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
]

let totalOutput = 0

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode
}
```

**好的：**

```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  },
  {
    name: 'Suzie Q',
    linesOfCode: 1500
  },
  {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  },
  {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
]

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
)
```

**[⬆ 回到顶部](#目录)**

### 封装条件语句

**不好的：**

```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**好的：**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode)
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ 回到顶部](#目录)**

### 避免使用否定条件语句

**不好的：**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**好的：**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ 回到顶部](#目录)**

### 避免条件语句

这似乎是一项不可能的任务。听到这个建议，大多数人都会问：“没有 `if` 语句我该如何完成任务？” 答案是，在许多情况下，您可以使用多态性来完成相同的任务。第二个问题通常是：“好的，但我为什么要那样做？”答案是我们之前学过的一个清晰的代码概念：一个函数应该只做一件事情。当你的类和函数有 `if` 语句时，你告诉用户你的函数做了不止一件事。记住，只做一件事情。

**不好的：**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount()
      case 'Air Force One':
        return this.getMaxAltitude()
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure()
    }
  }
}
```

**好的：**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount()
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude()
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure()
  }
}
```

**[⬆ 回到顶部](#目录)**

### 避免类型检查（part 1）

JavaScript 是一种无类型语言，这意味着你的函数可以接受任何类型的参数。有时候，这种自由会让你遇到麻烦，让你想在函数中进行类型检查。有许多方法可以避免这种情况。首先要考虑的是一致的 API。

**不好的：**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location('texas'))
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'))
  }
}
```

**好的：**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'))
}
```

**[⬆ 回到顶部](#目录)**

### 避免类型检查（part 2）

如果你正在处理基本的原始值，比如字符串和整数，并且无法使用多态，但你仍然觉得需要进行类型检查，那么你应该考虑使用 TypeScript。它是正常 JavaScript 的一个很好的替代品，因为它在标准 JavaScript 语法之上提供了静态类型。手动进行 JavaScript 类型检查的问题在于，它需要很多额外的冗长代码，以至于你所获得的“类型安全”并不足以弥补代码可读性的损失。保持你的 JavaScript 代码简洁，编写好的测试，进行良好的代码审查。否则，使用 TypeScript 进行所有这些操作（正如我所说，TypeScript 是一个很好的替代品！）。

**不好的：**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === 'number' && typeof val2 === 'number') ||
    (typeof val1 === 'string' && typeof val2 === 'string')
  ) {
    return val1 + val2
  }

  throw new Error('Must be of type String or Number')
}
```

**好的：**

```javascript
function combine(val1, val2) {
  return val1 + val2
}
```

**[⬆ 回到顶部](#目录)**

### 不要过度优化

现代浏览器在运行时会进行许多优化。很多时候，如果你在进行优化，你只是在浪费时间。有一些[很好的资源](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)可以看到哪些地方需要进行优化。在此期间，针对这些问题进行优化，直到它们被修复（如果可以修复）。

**不好的：**

```javascript
// 在旧版浏览器上，每次迭代时未缓存的 `list.length` 会重新计算，这样很耗费性能。
// 但是在现代浏览器上，已经进行了优化。
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**好的：**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ 回到顶部](#目录)**

### 删除死代码

死代码和重复代码一样糟糕。没有理由保留它在你的代码库中。如果它没有被调用，那就把它删除！如果你仍然需要它，它仍然会在你的版本历史中保留。

**不好的：**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule
inventoryTracker('apples', req, 'www.inventory-awesome.io')
```

**好的：**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule
inventoryTracker('apples', req, 'www.inventory-awesome.io')
```

**[⬆ 回到顶部](#目录)**

## 对象和数据结构

### 使用 getters 和 setters

使用 getter 和 setter 来访问对象中的数据可能比仅仅查找对象上的属性更好。你可能会问：“为什么呢？”好吧，这里是一些无组织的理由：

- 当你想要在获取对象属性之外做更多事情时，你不必查找并更改代码库中的每个访问器。
- 当进行 `set` 时，使添加验证变得简单。
- 封装内部表示。
- 在获取和设置时易于添加日志记录和错误处理。
- 你可以懒加载你的对象属性，比如从服务器获取。

**不好的：**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  }
}

const account = makeBankAccount()
account.balance = 100
```

**好的：**

```javascript
function makeBankAccount() {
  // 这个是私有变量
  let balance = 0

  // "getter"，通过下面返回的对象公开
  function getBalance() {
    return balance
  }

  // "setter"，通过下面返回的对象公开
  function setBalance(amount) {
    // ...在更新余额之前进行验证
    balance = amount
  }

  return {
    // ...
    getBalance,
    setBalance
  }
}

const account = makeBankAccount()
account.setBalance(100)
```

**[⬆ 回到顶部](#目录)**

### 让对象拥有私有成员

这可以通过闭包（对于 ES5 及以下版本）来实现。

**不好的：**

```javascript
const Employee = function (name) {
  this.name = name
}

Employee.prototype.getName = function getName() {
  return this.name
}

const employee = new Employee('John Doe')
console.log(`Employee name: ${employee.getName()}`) // Employee name: John Doe
delete employee.name
console.log(`Employee name: ${employee.getName()}`) // Employee name: undefined
```

**好的：**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name
    }
  }
}

const employee = makeEmployee('John Doe')
console.log(`Employee name: ${employee.getName()}`) // Employee name: John Doe
delete employee.name
console.log(`Employee name: ${employee.getName()}`) // Employee name: John Doe
```

**[⬆ 回到顶部](#目录)**

## Classes

### 使用 ES2015/ES6 类而不是 ES5 普通函数

对于 ES5 的类来说，要让继承、构造和方法定义可读性很高是非常困难的。如果您需要继承（请注意，您可能不需要），则更推荐使用 ES2015/ES6 类。但是，在需要更大更复杂的对象之前，请使用小函数代替类。

**不好的：**

```javascript
const Animal = function (age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`')
  }

  this.age = age
}

Animal.prototype.move = function move() {}

const Mammal = function (age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`')
  }

  Animal.call(this, age)
  this.furColor = furColor
}

Mammal.prototype = Object.create(Animal.prototype)
Mammal.prototype.constructor = Mammal
Mammal.prototype.liveBirth = function liveBirth() {}

const Human = function (age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`')
  }

  Mammal.call(this, age, furColor)
  this.languageSpoken = languageSpoken
}

Human.prototype = Object.create(Mammal.prototype)
Human.prototype.constructor = Human
Human.prototype.speak = function speak() {}
```

**好的：**

```javascript
class Animal {
  constructor(age) {
    this.age = age
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age)
    this.furColor = furColor
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor)
    this.languageSpoken = languageSpoken
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ 回到顶部](#目录)**

### 使用方法链式调用

这种模式在 JavaScript 中非常有用，你可以在许多库中看到它，比如 jQuery 和 Lodash。它让你的代码更加表达性，更简洁。因此，我建议使用方法链式调用，看看你的代码会变得多么简洁。在你的类函数中，只需要在每个函数的结尾返回 `this`，就可以在其上链接更多的类方法。

**不好的：**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make
    this.model = model
    this.color = color
  }

  setMake(make) {
    this.make = make
  }

  setModel(model) {
    this.model = model
  }

  setColor(color) {
    this.color = color
  }

  save() {
    console.log(this.make, this.model, this.color)
  }
}

const car = new Car('Ford', 'F-150', 'red')
car.setColor('pink')
car.save()
```

**好的：**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make
    this.model = model
    this.color = color
  }

  setMake(make) {
    this.make = make
    // 注意：返回 this 以进行链式调用
    return this
  }

  setModel(model) {
    this.model = model
    // 注意：返回 this 以进行链式调用
    return this
  }

  setColor(color) {
    this.color = color
    // 注意：返回 this 以进行链式调用
    return this
  }

  save() {
    console.log(this.make, this.model, this.color)
    // 注意：返回 this 以进行链式调用
    return this
  }
}

const car = new Car('Ford', 'F-150', 'red').setColor('pink').save()
```

**[⬆ 回到顶部](#目录)**

### 偏向使用组合而非继承

正如[《设计模式》](https://book.douban.com/subject/1052241/)中所述，应该在可以使用组合的情况下偏向使用组合而非继承。使用继承和使用组合都有很好的理由。这条原则的主要点在于，如果你的思维本能地想到继承，请尝试思考一下组合是否更能描述你的问题。在某些情况下，确实是这样的。

那么你可能会想，“我什么时候应该使用继承？”这取决于你手头的问题，但下面是一个比较好的继承比组合更合理的列表：

1. 继承代表“是一个”关系而不是“有一个”关系（Human->Animal 比 User->UserDetails 更合理）。
2. 你可以从基类中重用代码（Human 可以像所有动物一样移动）。
3. 你希望通过更改基类对派生类进行全局更改（当所有动物移动时，改变它们的卡路里消耗）。

**不好的：**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name
    this.email = email
  }

  // ...
}

// 不好的原因是因为 Employees 有 tax 数据。EmployeeTaxData 不是 Employee 的类型。
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super()
    this.ssn = ssn
    this.salary = salary
  }

  // ...
}
```

**好的：**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn
    this.salary = salary
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name
    this.email = email
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary)
  }
  // ...
}
```

**[⬆ 回到顶部](#目录)**

## 设计模式 SOLID

### 单一职责原则 (SRP)

如《Clean Code》所述，“一个类变化的原因不应该超过一个”。让一个类充满很多功能，就像你只能带一个行李箱上飞机一样，很诱人。问题是，你的类在概念上不会紧密相连，而且会给它很多变化的理由。尽量减少修改类的次数是重要的。这很重要，因为如果一个类中有太多的功能，而你修改其中一部分，很难理解它将如何影响代码库中其他依赖模块。

**不好的：**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**好的：**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user
    this.auth = new UserAuth(user)
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ 回到顶部](#目录)**

### 开闭原则 (OCP)

如 Bertrand Meyer 所述，“软件实体（类、模块、函数等）应该对扩展开放，对修改关闭。”不过这是什么意思呢？这个原则基本上是指你应该允许用户在不改变现有代码的情况下添加新的功能。

**不好的：**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super()
    this.name = 'ajaxAdapter'
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super()
    this.name = 'nodeAdapter'
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter
  }

  fetch(url) {
    if (this.adapter.name === 'ajaxAdapter') {
      return makeAjaxCall(url).then((response) => {
        // 转换 response 并返回
      })
    } else if (this.adapter.name === 'nodeAdapter') {
      return makeHttpCall(url).then((response) => {
        // 转换 response 并返回
      })
    }
  }
}

function makeAjaxCall(url) {
  // 请求并返回 promise
}

function makeHttpCall(url) {
  // 请求并返回 promise
}
```

**好的：**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super()
    this.name = 'ajaxAdapter'
  }

  request(url) {
    // 请求并返回 promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super()
    this.name = 'nodeAdapter'
  }

  request(url) {
    // 请求并返回 promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter
  }

  fetch(url) {
    return this.adapter.request(url).then((response) => {
      // 转换 response 并返回
    })
  }
}
```

**[⬆ 回到顶部](#目录)**

### 里氏替换原则 (LSP)

这是一个看起来可怕的术语，但实际上是一个非常简单的概念。它被正式定义为“如果 S 是 T 的子类型，则类型 T 的对象可以被类型 S 的对象替换（即类型 S 的对象可以替换类型 T 的对象），而不会改变该程序的任何期望属性（正确性、完成的任务等）。”这个定义甚至更加可怕。

这个原则最好的解释是，如果你有一个父类和一个子类，那么基类和子类可以互换使用，而不会得到错误的结果。这可能仍然令人困惑，所以让我们来看一下经典的正方形-矩形示例。数学上，正方形是一个矩形，但如果你使用继承的 "is-a" 关系进行建模，你很快就会遇到麻烦。

**不好的：**

```javascript
class Rectangle {
  constructor() {
    this.width = 0
    this.height = 0
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width
  }

  setHeight(height) {
    this.height = height
  }

  getArea() {
    return this.width * this.height
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width
    this.height = width
  }

  setHeight(height) {
    this.width = height
    this.height = height
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4)
    rectangle.setHeight(5)
    const area = rectangle.getArea() // 错误的：正方形返回25。应该是20。
    rectangle.render(area)
  })
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()]
renderLargeRectangles(rectangles)
```

**好的：**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super()
    this.width = width
    this.height = height
  }

  getArea() {
    return this.width * this.height
  }
}

class Square extends Shape {
  constructor(length) {
    super()
    this.length = length
  }

  getArea() {
    return this.length * this.length
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach((shape) => {
    const area = shape.getArea()
    shape.render(area)
  })
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)]
renderLargeShapes(shapes)
```

**[⬆ 回到顶部](#目录)**

### 接口隔离原则 (ISP)

JavaScript 没有接口，因此该原则不像其他原则那样严格适用。但即使在 JavaScript 缺乏类型系统的情况下，它仍然很重要和相关。

ISP 规定“客户端不应被迫依赖于他们不使用的接口”。由于鸭子类型，接口在 JavaScript 中是隐式的合同。

在 JavaScript 中，可以看一个很好的示例来展示这个原则的类，它需要大量的设置对象。不要求客户端设置大量的选项是有益的，因为大多数时候他们不需要所有的设置。将它们设置为可选的有助于避免出现“肥大的接口”。

**不好的：**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings
    this.setup()
  }

  setup() {
    this.rootNode = this.settings.rootNode
    this.settings.animationModule.setup()
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  animationModule() {} // 大多数情况下，当遍历时我们不需要进行动画处理。
  // ...
})
```

**好的：**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings
    this.options = settings.options
    this.setup()
  }

  setup() {
    this.rootNode = this.settings.rootNode
    this.setupOptions()
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule() {}
  }
})
```

**[⬆ 回到顶部](#目录)**

### 依赖反转原则 (DIP)

该原则阐述了两个关键点：

1. 高层模块不应该依赖于低层模块。两者应该依赖于抽象。
2. 抽象不应该依赖于具体实现细节。具体实现细节应该依赖于抽象。

起初，这可能很难理解，但如果您曾经使用过 AngularJS，您已经在依赖注入（DI）的形式中看到了该原则的实现。虽然它们不是完全相同的概念，但 DIP 使得高层模块不会了解其低层模块的详细信息并进行设置。它可以通过依赖注入实现。这样做的巨大好处是减少了模块之间的耦合。耦合是一种非常糟糕的开发模式，因为它使您的代码难以重构。

如先前所述，JavaScript 没有接口，因此所依赖的抽象是隐式的合同。也就是说，一个对象/类对另一个对象/类暴露的方法和属性。在下面的示例中，隐式合同是对于 `InventoryTracker` 的任何 Request 模块都有一个 `requestItems` 方法。

**不好的：**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP']
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items

    // BAD: 我们已经创建了一个依赖于特定请求实现的依赖关系。
    // 我们应该只让 requestItems 依赖于一个请求方法：request
    this.requester = new InventoryRequester()
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item)
    })
  }
}

const inventoryTracker = new InventoryTracker(['apples', 'bananas'])
inventoryTracker.requestItems()
```

**好的：**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items
    this.requester = requester
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item)
    })
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP']
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS']
  }

  requestItem(item) {
    // ...
  }
}

// 通过在外部构建并注入我们的依赖项，我们可以轻松地用一个使用 WebSockets 的新模块替换我们的请求模块。
const inventoryTracker = new InventoryTracker(
  ['apples', 'bananas'],
  new InventoryRequesterV2()
)
inventoryTracker.requestItems()
```

**[⬆ 回到顶部](#目录)**

## 测试

测试比交付更重要。如果您没有测试或测试不足，则每次发布代码时，您都无法确定是否破坏了任何功能。确定什么构成足够的测试量取决于您的团队，但是具有100％的覆盖率（所有语句和分支）是如何实现非常高的信心和开发人员内心平静的。这意味着除了拥有出色的测试框架外，您还需要使用一个[良好的覆盖工具](https://gotwarlost.github.io/istanbul/)。

没有理由不编写测试。有很多好的 [JS 测试框架](https://jstherightway.org/#testing-tools)，因此找到团队喜欢的框架。当您找到适合团队的测试框架后，请始终为引入的每个新功能/模块编写测试。如果您喜欢的方法是测试驱动开发（TDD），那很好，但是重要的是要确保在启动任何新功能或重构现有功能之前达到覆盖率目标。

### 单测一个概念

**不好的：**

```javascript
import assert from 'assert'

describe('MomentJS', () => {
  it('handles date boundaries', () => {
    let date

    date = new MomentJS('1/1/2015')
    date.addDays(30)
    assert.equal('1/31/2015', date)

    date = new MomentJS('2/1/2016')
    date.addDays(28)
    assert.equal('02/29/2016', date)

    date = new MomentJS('2/1/2015')
    date.addDays(28)
    assert.equal('03/01/2015', date)
  })
})
```

**好的：**

```javascript
import assert from 'assert'

describe('MomentJS', () => {
  it('handles 30-day months', () => {
    const date = new MomentJS('1/1/2015')
    date.addDays(30)
    assert.equal('1/31/2015', date)
  })

  it('handles leap year', () => {
    const date = new MomentJS('2/1/2016')
    date.addDays(28)
    assert.equal('02/29/2016', date)
  })

  it('handles non-leap year', () => {
    const date = new MomentJS('2/1/2015')
    date.addDays(28)
    assert.equal('03/01/2015', date)
  })
})
```

**[⬆ 回到顶部](#目录)**

## 并发编程

### 使用 Promise，而不是回调函数

回调函数不够清晰，并且会导致过多的嵌套。在 ES2015/ES6 中，Promise 是内置的全局类型。使用它们！

**不好的：**

```javascript
import { get } from 'request'
import { writeFile } from 'fs'

get(
  'https://en.wikipedia.org/wiki/Robert_Cecil_Martin',
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr)
    } else {
      writeFile('article.html', body, (writeErr) => {
        if (writeErr) {
          console.error(writeErr)
        } else {
          console.log('File written')
        }
      })
    }
  }
)
```

**好的：**

```javascript
import { get } from 'request-promise'
import { writeFile } from 'fs-extra'

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((body) => {
    return writeFile('article.html', body)
  })
  .then(() => {
    console.log('File written')
  })
  .catch((err) => {
    console.error(err)
  })
```

**[⬆ 回到顶部](#目录)**

### Async/Await 比 Promise 更简洁

Promises 是一种比回调函数更干净的替代方案，但 ES2017/ES8 引入了 async 和 await，提供了更简洁的解决方案。你只需要在一个函数前加上 `async` 关键字，然后就可以编写你的逻辑，而不需要使用 `then` 函数链。如果你今天可以利用 ES2017/ES8 特性，就使用它！

**不好的：**

```javascript
import { get } from 'request-promise'
import { writeFile } from 'fs-extra'

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((body) => {
    return writeFile('article.html', body)
  })
  .then(() => {
    console.log('File written')
  })
  .catch((err) => {
    console.error(err)
  })
```

**好的：**

```javascript
import { get } from 'request-promise'
import { writeFile } from 'fs-extra'

async function getCleanCodeArticle() {
  try {
    const body = await get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
    await writeFile('article.html', body)
    console.log('File written')
  } catch (err) {
    console.error(err)
  }
}

getCleanCodeArticle()
```

**[⬆ 回到顶部](#目录)**

## 错误处理

抛出错误是一件好事！它意味着运行时已经成功地确定了程序中出现了错误，并通过停止当前栈上的函数执行、终止进程（在Node中），并在控制台中使用堆栈跟踪来通知您。

### 不要忽略捕获的错误

什么也不做不会让您有能力修复或处理错误。将错误记录到控制台(`console.log`)也不太好，因为通常它会在控制台输出的一堆信息中丢失。如果您在 `try/catch` 块中包装了任何代码，则表示您认为可能会出现错误，因此您应该有一个计划或创建一个代码路径来处理它。

**不好的：**

```javascript
try {
  functionThatMightThrow()
} catch (error) {
  console.log(error)
}
```

**好的：**

```javascript
try {
  functionThatMightThrow()
} catch (error) {
  // 一种选项（比 console.log 更明显）：
  console.error(error)
  // 另一个选项：
  notifyUserOfError(error)
  // 另一个选项：
  reportErrorToService(error)
  // 或者都做！
}
```

### 不要忽略失败的 promise

出于与 `try/catch` 中不忽略捕获的错误相同的原因，您也不应忽略来自拒绝的 Promise 的错误。

**不好的：**

```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data)
  })
  .catch((error) => {
    console.log(error)
  })
```

**好的：**

```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data)
  })
  .catch((error) => {
    // error 比 log 更明显
    console.error(error)
    // 另一个选项：通知用户错误
    notifyUserOfError(error)
    // 另一个选项：报错错误到服务端
    reportErrorToService(error)
    // 或者上面的都做！
  })
```

**[⬆ 回到顶部](#目录)**

## 格式化

格式化是主观的。像这里的许多规则一样，没有硬性规定必须遵循。主要的要点是不要为格式化而争论。有[大量的工具]((https://standardjs.com/rules.html))可以自动化此过程。使用其中一个！工程师为格式化争论是浪费时间和金钱。

对于不属于自动格式化范围的事情（缩进、制表符与空格、双引号与单引号等），请查看此处以获取一些指导。

### 使用一致的大小写

JavaScript 是无类型的，因此大小写告诉您很多关于变量、函数等的信息。这些规则是主观的，因此您的团队可以选择任何想要的。重要的是，无论您选择什么，只要保持一致即可。

**不好的：**

```javascript
const DAYS_IN_WEEK = 7
const daysInMonth = 30

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude']
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles']

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**好的：**

```javascript
const DAYS_IN_WEEK = 7
const DAYS_IN_MONTH = 30

const SONGS = ['Back In Black', 'Stairway to Heaven', 'Hey Jude']
const ARTISTS = ['ACDC', 'Led Zeppelin', 'The Beatles']

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ 回到顶部](#目录)**

### 函数的调用者和被调用者应该靠近

如果一个函数调用了另一个函数，在源文件中应该将这些函数垂直地靠近。理想情况下，将调用者放在被调用者的正上方。我们倾向于从上到下阅读代码，就像读报纸一样。因此，使你的代码也这样读起来。

**不好的：**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers')
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager')
  }

  getPeerReviews() {
    const peers = this.lookupPeers()
    // ...
  }

  perfReview() {
    this.getPeerReviews()
    this.getManagerReview()
    this.getSelfReview()
  }

  getManagerReview() {
    const manager = this.lookupManager()
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee)
review.perfReview()
```

**好的：**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee
  }

  perfReview() {
    this.getPeerReviews()
    this.getManagerReview()
    this.getSelfReview()
  }

  getPeerReviews() {
    const peers = this.lookupPeers()
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers')
  }

  getManagerReview() {
    const manager = this.lookupManager()
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager')
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee)
review.perfReview()
```

**[⬆ 回到顶部](#目录)**

## 注释

### 只有具有业务逻辑复杂性的内容才需要注释

注释是一种道歉，而不是一种要求。好的代码_大多数时候_都能自己解释清楚。

**不好的：**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0

  // Length of string
  const length = data.length

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i)
    // Make the hash
    hash = (hash << 5) - hash + char
    // Convert to 32-bit integer
    hash &= hash
  }
}
```

**好的：**

```javascript
function hashIt(data) {
  let hash = 0
  const length = data.length

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i)
    hash = (hash << 5) - hash + char

    // Convert to 32-bit integer
    hash &= hash
  }
}
```

**[⬆ 回到顶部](#目录)**

### 不要在代码库中保留已注释的代码

版本控制是存在的原因。将旧代码留在历史记录中。

**不好的：**

```javascript
doStuff()
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**好的：**

```javascript
doStuff()
```

**[⬆ 回到顶部](#目录)**

### 不要有日志式注释

记住，要使用版本控制！不需要死代码、注释掉的代码，尤其是日志注释。使用 `git log` 获取历史记录！

**不好的：**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b
}
```

**好的：**

```javascript
function combine(a, b) {
  return a + b
}
```

**[⬆ 回到顶部](#目录)**

### 避免使用位置标记

它们通常只会增加代码的噪音。让函数和变量名以及适当的缩进和格式化为您的代码提供视觉结构。

**不好的：**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: 'foo',
  nav: 'bar'
}

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function () {
  // ...
}
```

**好的：**

```javascript
$scope.model = {
  menu: 'foo',
  nav: 'bar'
}

const actions = function () {
  // ...
}
```

**[⬆ 回到顶部](#目录)**
