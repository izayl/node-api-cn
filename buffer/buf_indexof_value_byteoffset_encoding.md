<!-- YAML
added: v1.5.0
changes:
  - version: v8.0.0
    pr-url: https://github.com/nodejs/node/pull/10236
    description: The `value` can now be a `Uint8Array`.
  - version: v5.7.0, v4.4.0
    pr-url: https://github.com/nodejs/node/pull/4803
    description: When `encoding` is being passed, the `byteOffset` parameter
                 is no longer required.
-->

* `value` {string|Buffer|Uint8Array|integer} 要查找的值。
* `byteOffset` {integer} `buf` 中开始查找的偏移量。**默认值:** `0`。
* `encoding` {string} 如果 `value` 是字符串，则指定 `value` 的字符编码。**默认值:** `'utf8'`。
* 返回: {integer} `buf` 中首次出现 `value` 的索引，如果 `buf` 没包含 `value` 则返回 `-1`。

如果 `value` 是：

  * 一个字符串，则 `value` 根据 `encoding` 的字符编码进行解析。
  * 一个 `Buffer` 或 [`Uint8Array`]，则 `value` 会整个进行对比。如果要对比部分 `Buffer`，可使用 [`buf.slice()`]。
  * 一个数值, 则 `value` 会被解析成 `0` 至 `255` 之间的无符号八位整数值。

```js
const buf = Buffer.from('this is a buffer');

console.log(buf.indexOf('this'));
// 打印: 0
console.log(buf.indexOf('is'));
// 打印: 2
console.log(buf.indexOf(Buffer.from('a buffer')));
// 打印: 8
console.log(buf.indexOf(97));
// 打印: 8（97 是 'a' 的十进制 ASCII 值）
console.log(buf.indexOf(Buffer.from('a buffer example')));
// 打印: -1
console.log(buf.indexOf(Buffer.from('a buffer example').slice(0, 8)));
// 打印: 8

const utf16Buffer = Buffer.from('\u039a\u0391\u03a3\u03a3\u0395', 'utf16le');

console.log(utf16Buffer.indexOf('\u03a3', 0, 'utf16le'));
// 打印: 4
console.log(utf16Buffer.indexOf('\u03a3', -4, 'utf16le'));
// 打印: 6
```

如果 `value` 不是一个字符串、数值、或 `Buffer`，则此方法将会抛出 `TypeError`。 
如果 `value` 是一个数值，则会被转换成介于 0 到 255 之间的整数值。

如果 `byteOffset` 不是一个数值，则会被转换成数值。
如果转换后的值为 `NaN` 或 `0`, 则会查找整个 buffer。
这与 [`String#indexOf()`] 是一致的。

```js
const b = Buffer.from('abcdef');

// 传入一个数值，但不是有效的字节。
// 打印：2，相当于查找 99 或 'c'。
console.log(b.indexOf(99.9));
console.log(b.indexOf(256 + 99));

// 传入被转换成 NaN 或 0 的 byteOffset。
// 打印：1，查找整个 buffer。
console.log(b.indexOf('b', undefined));
console.log(b.indexOf('b', {}));
console.log(b.indexOf('b', null));
console.log(b.indexOf('b', []));
```

如果 `value` 是一个空字符串或空 `Buffer`，且 `byteOffset` 小于 `buf.length`，则返回 `byteOffset`。
如果 `value` 是一个空字符串，且 `byteOffset` 大于或等于 `buf.length`，则返回 `buf.length`。

