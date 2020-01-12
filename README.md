<div align="center">
  <a href="http://json-schema.org">
    <img width="160" height="160"
      src="https://raw.githubusercontent.com/webpack-contrib/schema-utils/master/.github/assets/logo.png">
  </a>
  <a href="https://github.com/webpack/webpack">
    <img width="200" height="200"
      src="https://webpack.js.org/assets/icon-square-big.svg">
  </a>
</div>

[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![coverage][cover]][cover-url]
[![chat][chat]][chat-url]
[![size][size]][size-url]

# schema-utils

Package for validate options in loaders and plugins.

## Getting Started

To begin, you'll need to install `schema-utils`:

```console
npm install schema-utils
```

## API

**schema.json**

```json
{
  "type": "object",
  "properties": {
    "option": {
      "type": ["boolean"]
    }
  },
  "additionalProperties": false
}
```

```js
import schema from './path/to/schema.json';
import validate from 'schema-utils';

const options = { option: true };
const configuration = { name: 'Loader Name/Plugin Name/Name' };

validate(schema, options, configuration);
```

### `schema`

Type: `String`

JSON schema.

Simple example of schema:

```json
{
  "type": "object",
  "properties": {
    "name": {
      "description": "This is description of option.",
      "type": "string"
    }
  },
  "additionalProperties": false
}
```

### `options`

Type: `Object`

Object with options.

```js
validate(
  schema,
  {
    name: 123,
  },
  { name: 'MyPlugin' }
);
```

### `configuration`

Allow to configure validator.

There is an alternative method to configure the `name` and`baseDataPath` options via the `title` property in the schema.
For example:

```json
{
  "title": "My Loader options",
  "type": "object",
  "properties": {
    "name": {
      "description": "This is description of option.",
      "type": "string"
    }
  },
  "additionalProperties": false
}
```

The last word used for the `baseDataPath` option, other words used for the `name` option.
Based on the example above the `name` option equals `My Loader`, the `baseDataPath` option equals `options`.

#### `name`

Type: `Object`
Default: `"Object"`

Allow to setup name in validation errors.

```js
validate(schema, options, { name: 'MyPlugin' });
```

```shell
Invalid configuration object. MyPlugin has been initialised using a configuration object that does not match the API schema.
 - configuration.optionName should be a integer.
```

#### `baseDataPath`

Type: `String`
Default: `"configuration"`

Allow to setup base data path in validation errors.

```js
validate(schema, options, { name: 'MyPlugin', baseDataPath: 'options' });
```

```shell
Invalid options object. MyPlugin has been initialised using an options object that does not match the API schema.
 - options.optionName should be a integer.
```

#### `postFormatter`

Type: `Function`
Default: `undefined`

Allow to reformat errors.

```js
validate(schema, options, {
  name: 'MyPlugin',
  postFormatter: (formattedError, error) => {
    if (error.keyword === 'type') {
      return `${formattedError}\nAdditional Information.`;
    }

    return formattedError;
  },
});
```

```shell
Invalid options object. MyPlugin has been initialised using an options object that does not match the API schema.
 - options.optionName should be a integer.
   Additional Information.
```

*`configuration` 参数部分的中文意译：*

*第三个参数 `configuration` 是用来配置校验失败时打印的错误信息中显示的内容。*

*错误信息的内容格式一般如下：*

```shell
Invalid options object. [name] has been initialised using an options object that does not match the API schema.
 - [baseDataPath].optionName should be a integer.
```

*其中，name 是 loader 的名字，baseDataPath 是 `options` 对象的表示。*

*如果不配置这个参数，那么打印错误信息时会从传入的 schema 中 `title` 获取 loader 的名字和 loader 的配置 `options` 对象的表示。这里会取 `title` 中除最后一个单词的剩余单词作为 loader 的名字，取最后一个单词作为 `options` 对象的表示。*

*配置这个参数的话，就传入一个对象，里面定义 name 和 baseDataPath 就好了。*

*postFormatter 可以自定义日志显示的内容。*

## Examples

**schema.json**

```json
{
  "type": "object",
  "properties": {
    "name": {
      "type": "string"
    },
    "test": {
      "anyOf": [
        { "type": "array" },
        { "type": "string" },
        { "instanceof": "RegExp" }
      ]
    },
    "transform": {
      "instanceof": "Function"
    },
    "sourceMap": {
      "type": "boolean"
    }
  },
  "additionalProperties": false
}
```

### `Loader`

```js
import { getOptions } from 'loader-utils';
import validateOptions from 'schema-utils';

import schema from 'path/to/schema.json';

function loader(src, map) {
  const options = getOptions(this) || {};

  validateOptions(schema, options, {
    name: 'Loader Name',
    baseDataPath: 'options',
  });

  // Code...
}

export default loader;
```

### `Plugin`

```js
import validateOptions from 'schema-utils';

import schema from 'path/to/schema.json';

class Plugin {
  constructor(options) {
    validateOptions(schema, options, {
      name: 'Plugin Name',
      baseDataPath: 'options',
    });

    this.options = options;
  }

  apply(compiler) {
    // Code...
  }
}

export default Plugin;
```

## Contributing

Please take a moment to read our contributing guidelines if you haven't yet done so.

[CONTRIBUTING](./.github/CONTRIBUTING.md)

## License

[MIT](./LICENSE)

[npm]: https://img.shields.io/npm/v/schema-utils.svg
[npm-url]: https://npmjs.com/package/schema-utils
[node]: https://img.shields.io/node/v/schema-utils.svg
[node-url]: https://nodejs.org
[deps]: https://david-dm.org/webpack/schema-utils.svg
[deps-url]: https://david-dm.org/webpack/schema-utils
[tests]: https://dev.azure.com/webpack/schema-utils/_apis/build/status/webpack.schema-utils?branchName=master
[tests-url]: https://dev.azure.com/webpack/schema-utils/_build/latest?definitionId=9&branchName=master
[cover]: https://codecov.io/gh/webpack/schema-utils/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack/schema-utils
[chat]: https://badges.gitter.im/webpack/webpack.svg
[chat-url]: https://gitter.im/webpack/webpack
[size]: https://packagephobia.now.sh/badge?p=schema-utils
[size-url]: https://packagephobia.now.sh/result?p=schema-utils
