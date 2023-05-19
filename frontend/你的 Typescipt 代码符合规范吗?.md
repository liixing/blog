## 前言

此文仅限于项目原来规范有区别的地方，完整规范请参考
[eslint-config-standard-with-typescript](https://github.com/standard/eslint-config-standard-with-typescript)  

主体思路就是代码风格交给 `prettier`，代码规范交给 `eslint`，`eslint` 和 `prettier` 之间冲突，如果 prettier 能解决就改 `prettier` 的规则，如果 `prettier` 不能解决就改 `eslint` 的规则。

## 代码风格

### 去除无用的分号

```typescript
// bad
const foo = 1;
const bar = 2;

// good
const foo = 1
const bar = 2
```
### 去除无用的逗号
```typescript
// bad
const foo ={
    name: 'foo',
    age: 18,
}

// good
const foo ={
    name: 'foo',
    age: 18
}
```
标准写法不含分号和多余的逗号，所以我们需要在 `prettier` 中配置 `semi: false` 和 `trailingComma: none`


### 缩进 @typescript-eslint/indent

这方面我比较喜欢 `prettier` 的规则，所以我把 `eslint` 的缩进规则关掉了，只保留了 `prettier` 的缩进规则。

```js
  rules: { indent: 0, '@typescript-eslint/indent': 0 }
```

### 三元表达式 multiline-ternary
```typescript
// bad
const foo = a ? b : c

// good
const foo = a
    ? b
    : c
```
这个 `eslint`规则大体没问题，但在 `react` 组件中会和 `prettier` 冲突，所以我也把这个规则关掉了。

```js
  rules: { 'multiline-ternary': 0 }
```

### 函数名与括号前要添加一个空格 @typescript-eslint/space-before-function-paren
```typescript
// bad
function foo() {
    // ...
}

// good
function foo () {
    // ...
}
```
这个规则也是和 `prettier` 冲突，所以我也把这个规则关掉了。

```js
  rules: { '@typescript-eslint/space-before-function-paren': 0 }
```

### 成员分隔符风格 @typescript-eslint/member-delimiter-style

```typescript
// bad
{ min: number; max: number }

// good
{ min: number, max: number }
```
![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4fa337d9c39a4097a4dd07ea51de7daa~tplv-k3u1fbpfcp-watermark.image?)

这条规则官网不推荐使用，所以我也把这个规则关掉了,使用 `prettier` 的规则。

```js
  rules: { '@typescript-eslint/member-delimiter-style': 0 }
```

## 代码规范

### 函数必须要定义返回类型 explicit-function-return-type

```typescript
// bad
function foo (a: number, b: number) {
    return a + b
}

// good
function foo (a: number, b: number): number {
    return a + b
}
```
这条规则导致改动很多，大多是加 `void` 和 `JSX.Element`, 但是这个规则对于代码规范是有好处的

### 严格的布尔表达式 @typescript-eslint/strict-boolean-expressions
```typescript
// bad
// nullable booleans are considered unsafe by default
function foo(bool?: boolean) {
  if (bool) {
    bar();
  }
}
// good
function foo(bool?: boolean) {
  if (bool ?? false) {
    bar();
  }
}
```
这个规则要求必须是布尔值 true 或 false,而不是 truthy 或 falsy 的值。 

感觉有点过于严格了，所以我也把这个规则关掉了。

```js
rules: { '@typescript-eslint/strict-boolean-expressions': 0 }
```

### 限制模板字符串 @typescript-eslint/restrict-template-expressions

```typescript
// bad
const arg1 = [1, 2];
const msg1 = `arg1 = ${arg1}`;

const arg2 = { name: 'Foo' };
const msg2 = `arg2 = ${arg2 || null}`;

// good
const arg = 'foo';
const msg1 = `arg = ${arg}`;
const msg2 = `arg = ${arg || 'default'}`;

const stringWithKindProp: string & { _kind?: 'MyString' } = 'foo';
const msg3 = `stringWithKindProp = ${stringWithKindProp}`;
```
这个规则建议在模板字符串中只使用简单的标识符和表达式,而不要用户输入的潜在恶意内容,以避免产生安全隐患。  

大致没有问题，但是在日志或者错误信息中，我们需要输出一些 `any` 类型的数据，所以我也把这个规则 `allowAny` 给打开了

```js
 '@typescript-eslint/restrict-template-expressions': ['error', { allowAny: true} ]
```

### 禁止禁止滥用 Promise  @typescript-eslint/no-misused-promises

```typescript
// bad
const promise = Promise.resolve('value');

if (promise) {
  // Do something
}

const val = promise ? 123 : 456;

while (promise) {
  // Do something
}

// good
const promise = Promise.resolve('value');

// Always `await` the Promise in a conditional
if (await promise) {
  // Do something
}

const val = (await promise) ? 123 : 456;

while (await promise) {
  // Do something
}
```
这条规则也大致没什么问题，但是 `async` 函数即使返回 `void` 也必须返回 `Promise<void>`，所以我也把这个规则中的 `checksVoidReturn` 给关掉了。
```js
   '@typescript-eslint/no-misused-promises': ['error',{ checksVoidReturn: false} ]
```

### 禁止未处理的 Promise @typescript-eslint/no-floating-promises

```typescript
// bad
 message.info('1')

// good
void message.info('1')
```
这个规则也是导致项目改动很大，所有的 `message` 都要加上 `void`，然后没有 `catch` 的 `promise` 也要加上 `catch`


## 背景
最近在做一个 `devops` 运维平台 项目，采用的是 `monorepo` 的形式，前后端都在一个仓库里，还有一些额外的包。

由于都是使用 `typescript` 开发，所以我们自己维护了一个  `eslint` 规则的包。主体大概就是 recommended 标准。但是得到评价如下：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/82773c9a36644db7b6346edc908c2609~tplv-k3u1fbpfcp-watermark.image?)

所以我们项目在上线后，`eslint` 规则就全面转向了 [eslint-config-standard-with-typescript](https://github.com/standard/eslint-config-standard-with-typescript)，就写了此文记录一下。