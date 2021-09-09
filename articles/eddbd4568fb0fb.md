---
title: "よく使うESLint設定まとめ"
emoji: "🙆"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["eslint", "Nextjs", "typescript"]
published: false
---

自分が使用する ESLint の設定メモです。

基本的に採用している技術

- Next.js
- TypeScript
- Tailwind CSS

# 参考ページ

https://qiita.com/mysticatea/items/f523dab04a25f617c87d
https://zenn.dev/ken505/articles/c049a64f3a2989

# ESLint 本体

- [ルール一覧(公式)](https://eslint.org/docs/rules/)
- [ルール一覧(日本語訳)](https://garafu.blogspot.com/2017/02/eslint-rules-jp.html)

## インストール

Prettier も同時に使用するため合わせてインストールする。
create-next-app を使用して Next.js の環境構築した場合は ESLint がインストールされている。

```
> yarn add -D eslint
> yarn add -D prettier eslint-config-prettier
```

## ルールセット:recommended

[設定されるルール](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/src/configs/recommended.ts)

### 設定方法

```js:.eslintrc.js
  extends: [
    'eslint:recommended',
  ],
```

## ルール

✓：eslint:recommended にて設定される

|     | ルール名                                        | 内容                                                                |
| :-: | :---------------------------------------------- | :------------------------------------------------------------------ |
|     | [no-console](#no-console)                       | console の使用をしないこと                                          |
|     | [no-restricted-syntax](#no-restricted-syntax)   | function 宣言や with 句など指定された記述方法は行わないこと         |
|     | [prefer-arrow-callback](#prefer-arrow-callback) | コールバックにはアロー関数を利用すること                            |
|     | [prefer-const](#prefer-const)                   | 再代入を行わない変数は const を利用すること                         |
|     | [func-style](#func-style)                       | 関数を定義する際は変数代入による定義(function expression)を行うこと |
|     | [arrow-body-style](#arrow-body-style)           | アロー関数の波括弧は必要に応じて記載すること                        |
|     | [no-restricted-imports](#no-restricted-imports) | 指定されたモジュールの import は行わないこと                        |
|     | [array-bracket-spacing](#array-bracket-spacing) | 配列を示す角括弧の直ぐ内側に空白を作らないこと                      |
|     | [prefer-template](#prefer-template)             | 文字列結合の代わりにテンプレートリテラルが利用すること              |
|  ✓  | no-unreachable                                  | 到達不可能なコードを記述しないこと                                  |
|  ✓  | no-redeclare                                    | 同じ変数名を再定義しないこと                                        |

## 設定方法

### [no-console](https://eslint.org/docs/rules/no-console)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    'no-console': ['error', { allow: ['warn', 'info', 'error'] }],
  }
```

### [no-restricted-syntax](https://eslint.org/docs/rules/no-restricted-syntax)

- selector に指定された際の制御

| selector               | 処理内容                                            |
| :--------------------- | :-------------------------------------------------- |
| TSEnumDeclaration      | enum 使わなくて union type を指定する               |
| TSInterfaceDeclaration | typescript の型定義を基本的に Type alias で統一する |
| ForInStatement         | for (xx in xx) 構文を使用できなくする               |
| ForOfStatement         | for (xx of xx) 構文を使用できなくする               |
| LabeledStatement       | label を使用しない(goto 文は不可)                   |
| WithStatement          | with を使用しない                                   |

- [selector に指定可能な値](https://github.com/eslint/espree/blob/main/lib/ast-node-types.js)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    'no-restricted-syntax': [
      'error',
      { selector: 'TSEnumDeclaration', message: 'Don't declare enums' },
      {
        selector: 'TSInterfaceDeclaration',
        message: 'Don't declare Interface',
      },
      {
        selector: 'ForInStatement',
        message:
          'for..in loops iterate over the entire prototype chain, which is virtually never what you want. Use Object.{keys,values,entries}, and iterate over the resulting array.',
      },
      {
        selector: 'ForOfStatement',
        message:
          'iterators/generators require regenerator-runtime, which is too heavyweight for this guide to allow them. Separately, loops should be avoided in favor of array iterations.',
      },
      {
        selector: 'LabeledStatement',
        message:
          'Labels are a form of GOTO; using them makes code confusing and hard to maintain and understand.',
      },
      {
        selector: 'WithStatement',
        message:
          '`with` is disallowed in strict mode because it makes code impossible to predict and optimize.',
      },
    ],
  }
```

### [prefer-arrow-callback](https://eslint.org/docs/rules/prefer-arrow-callback)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    'prefer-arrow-callback': 'error',
  }
```

### [prefer-const](https://eslint.org/docs/rules/prefer-const)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    'prefer-const': 'error',
  }
```

### [func-style](https://eslint.org/docs/rules/func-style)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    'func-style': ['error', 'expression'],
  }
```

- チェック結果のサンプル

```js
// エラーあり
function foo() {
  // ...
}

// エラーなし
const foo = function () {
  // ...
};

const foo = () => {};
```

### [arrow-body-style](https://eslint.org/docs/rules/arrow-body-style)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    'arrow-body-style': ['error', 'always'],
  }
```

### [no-restricted-imports](https://eslint.org/docs/rules/no-restricted-imports)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    'no-restricted-imports': [
      'error',
      { paths: [{ name: 'react', importNames: ['default'] }] },
    ],
  }
```

### [array-bracket-spacing](https://eslint.org/docs/rules/array-bracket-spacing)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    'array-bracket-spacing': ['error', 'never'],
  }
```

### [prefer-template](https://eslint.org/docs/rules/prefer-template)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    'prefer-template': 'error',
  }
```

- チェック結果のサンプル

```js
// エラーあり
const str = "Hello, " + name + "!";

// エラーなし
const str = `Hello, ${name}!`;
```

# プラグイン：[typescript-eslint](https://github.com/typescript-eslint/typescript-eslint)

## インストール

`yarn add -D @typescript-eslint/parser @typescript-eslint/eslint-plugin`

## ルールセット:@typescript-eslint/recommended

[設定されるルール](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/src/configs/recommended.ts)

合わせて設定されるルールセットは[base](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/src/configs/base.ts)と、[eslint-recommended](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/src/configs/eslint-recommended.ts)

### 設定方法

```js:.eslintrc.js
  extends: [
    'plugin:@typescript-eslint/recommended',
  ],
```

### 設定内容

```js
  parser: '@typescript-eslint/parser',
  parserOptions: { sourceType: 'module' },
  plugins: ['@typescript-eslint'],
  rules: {
    '@typescript-eslint/adjacent-overload-signatures': 'error',
    '@typescript-eslint/ban-ts-comment': 'error',
    '@typescript-eslint/ban-types': 'error',
    '@typescript-eslint/explicit-module-boundary-types': 'warn',
    'no-array-constructor': 'off',
    '@typescript-eslint/no-array-constructor': 'error',
    'no-empty-function': 'off',
    '@typescript-eslint/no-empty-function': 'error',
    '@typescript-eslint/no-empty-interface': 'error',
    '@typescript-eslint/no-explicit-any': 'warn',
    '@typescript-eslint/no-extra-non-null-assertion': 'error',
    'no-extra-semi': 'off',
    '@typescript-eslint/no-extra-semi': 'error',
    '@typescript-eslint/no-inferrable-types': 'error',
    '@typescript-eslint/no-misused-new': 'error',
    '@typescript-eslint/no-namespace': 'error',
    '@typescript-eslint/no-non-null-asserted-optional-chain': 'error',
    '@typescript-eslint/no-non-null-assertion': 'warn',
    '@typescript-eslint/no-this-alias': 'error',
    'no-unused-vars': 'off',
    '@typescript-eslint/no-unused-vars': 'warn',
    '@typescript-eslint/no-var-requires': 'error',
    '@typescript-eslint/prefer-as-const': 'error',
    '@typescript-eslint/prefer-namespace-keyword': 'error',
    '@typescript-eslint/triple-slash-reference': 'error',
  },
  overrides: [
    {
      files: ['*.ts', '*.tsx'],
      rules: {
        'constructor-super': 'off', // ts(2335) & ts(2377)
        'getter-return': 'off', // ts(2378)
        'no-const-assign': 'off', // ts(2588)
        'no-dupe-args': 'off', // ts(2300)
        'no-dupe-class-members': 'off', // ts(2393) & ts(2300)
        'no-dupe-keys': 'off', // ts(1117)
        'no-func-assign': 'off', // ts(2539)
        'no-import-assign': 'off', // ts(2539) & ts(2540)
        'no-new-symbol': 'off', // ts(2588)
        'no-obj-calls': 'off', // ts(2349)
        'no-redeclare': 'off', // ts(2451)
        'no-setter-return': 'off', // ts(2408)
        'no-this-before-super': 'off', // ts(2376)
        'no-undef': 'off', // ts(2304)
        'no-unreachable': 'off', // ts(7027)
        'no-unsafe-negation': 'off', // ts(2365) & ts(2360) & ts(2358)
        'no-var': 'error', // ts transpiles let/const to var, so no need for vars any more
        'prefer-const': 'error', // ts provides better types with const
        'prefer-rest-params': 'error', // ts provides better types with rest args over arguments
        'prefer-spread': 'error', // ts transpiles spread to apply, so no need for manual apply
        'valid-typeof': 'off', // ts(2367)
      },
    },
  ],
```

## ルール

✓：@typescript-eslint/recommended にて設定される

|     | ルール                                                            | 内容                                                                                   |
| :-: | :---------------------------------------------------------------- | :------------------------------------------------------------------------------------- |
|     | [naming-convention](#naming-convention)                           | 変数名や関数名のルールを定義する。                                                     |
|  ✓  | [explicit-module-boundary-types](#explicit-module-boundary-types) | エクスポートしたパブリックメソッドの関数とクラスには、明示的な戻り値と引数を要求しない |
|  ✓  | [no-explicit-any](#no-explicit-any)                               | any 型の使用は制限しない                                                               |
|  ✓  | [no-var-requires](#no-var-requires)                               | require 文の使用は制限しない                                                           |
|     | [consistent-type-imports](#consistent-type-imports)               |                                                                                        |
|  ✓  | adjacent-overload-signatures                                      |                                                                                        |
|  ✓  | ban-ts-comment                                                    |                                                                                        |
|  ✓  | ban-types                                                         |                                                                                        |
|  ✓  | no-array-constructor                                              |                                                                                        |
|  ✓  | no-empty-function                                                 |                                                                                        |
|  ✓  | no-empty-interface                                                |                                                                                        |
|  ✓  | no-extra-non-null-assertion                                       |                                                                                        |
|  ✓  | no-extra-semi                                                     |                                                                                        |
|  ✓  | no-inferrable-types                                               |                                                                                        |
|  ✓  | no-misused-new                                                    |                                                                                        |
|  ✓  | no-namespace                                                      |                                                                                        |
|  ✓  | no-non-null-asserted-optional-chain                               |                                                                                        |
|  ✓  | no-non-null-assertion                                             |                                                                                        |
|  ✓  | no-this-alias                                                     |                                                                                        |
|  ✓  | no-unused-vars                                                    |                                                                                        |
|  ✓  | prefer-as-const                                                   |                                                                                        |
|  ✓  | prefer-namespace-keyword                                          |                                                                                        |
|  ✓  | triple-slash-reference                                            |                                                                                        |

eslint:recommended の設定を変更する。

|     | ルール                                                                     | 設定値 |
| :-: | :------------------------------------------------------------------------- | :----- |
|  ✓  | [no-array-constructor](https://eslint.org/docs/rules/no-array-constructor) | off    |
|  ✓  | [no-empty-function](https://eslint.org/docs/rules/no-empty-function)       | off    |
|  ✓  | [no-extra-semi](https://eslint.org/docs/rules/no-extra-semi)               | off    |
|  ✓  | [no-unused-vars](https://eslint.org/docs/rules/no-unused-vars)             | off    |

拡張子が"ts"、"tsx"の場合のみ、eslint:recommended の設定を変更する。
| | ルール | 設定値 |
| :-: | :------------------------------------------------------------------------- | :----- |
| ✓ | [constructor-super](https://eslint.org/docs/rules/constructor-super) | off |
| ✓ | [getter-return](https://eslint.org/docs/rules/getter-return) | off |
| ✓ | [no-const-assign](https://eslint.org/docs/rules/no-const-assign) | off |
| ✓ | [no-dupe-args](https://eslint.org/docs/rules/no-dupe-args) | off |
| ✓ | [no-dupe-class-members](https://eslint.org/docs/rules/no-dupe-class-members) | off |
| ✓ | [no-dupe-keys](https://eslint.org/docs/rules/no-dupe-keys) | off |
| ✓ | [no-func-assign](https://eslint.org/docs/rules/no-func-assign) | off |
| ✓ | [no-import-assign](https://eslint.org/docs/rules/no-import-assign) | off |
| ✓ | [no-new-symbol](https://eslint.org/docs/rules/no-new-symbol) | off |
| ✓ | [no-obj-calls](https://eslint.org/docs/rules/no-obj-calls) | off |
| ✓ | [no-redeclare](https://eslint.org/docs/rules/no-redeclare) | off |
| ✓ | [no-setter-return](https://eslint.org/docs/rules/no-setter-return) | off |
| ✓ | [no-this-before-super](https://eslint.org/docs/rules/no-this-before-super) | off |
| ✓ | [no-undef](https://eslint.org/docs/rules/no-undef) | off |
| ✓ | [no-unreachable](https://eslint.org/docs/rules/no-unreachable) | off |
| ✓ | [no-unsafe-negation](https://eslint.org/docs/rules/no-unsafe-negation) | off |
| ✓ | [no-var](https://eslint.org/docs/rules/no-var) | error |
| ✓ | [prefer-const](https://eslint.org/docs/rules/prefer-const) | error |
| ✓ | [prefer-rest-params](https://eslint.org/docs/rules/prefer-rest-params) | error |
| ✓ | [prefer-spread](https://eslint.org/docs/rules/prefer-spread) | error |
| ✓ | [valid-typeof](https://eslint.org/docs/rules/valid-typeof) | off |

### [naming-convention](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/naming-convention.md)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    '@typescript-eslint/naming-convention': [
      'error',
      /* 1 */ { selector: 'default', format: ['camelCase'] },
      /* 2 */ { selector: 'variable', format: ['snake_case'] },
      /* 3 */ { selector: 'variable', types: ['boolean'], format: ['UPPER_CASE'] },
      /* 4 */ { selector: 'variableLike', format: ['PascalCase'] },
    ];
  }
```

### [explicit-module-boundary-types](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/explicit-module-boundary-types.md)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    '@typescript-eslint/explicit-module-boundary-types': 'off',
  }
```

### [no-explicit-any](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/no-explicit-any.md)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    '@typescript-eslint/no-explicit-any': 'off',
  }
```

### [no-var-requires](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/no-var-requires.md)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    '@typescript-eslint/no-var-requires': 'off',
  }
```

### [consistent-type-imports](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/consistent-type-imports.md)

- 設定サンプル

```js:.eslintrc.js
  'rules': {
    '@typescript-eslint/consistent-type-imports': [
      'warn',
      { prefer: 'type-imports' },
    ],
  }
```

- チェック結果のサンプル

```js
// エラーあり
import { Foo } from "Foo";
import Bar from "Bar";
type T = Foo;
const x: Bar = 1;

// エラーなし
import type { Foo } from "Foo";
import type Bar from "Bar";
type T = Foo;
const x: Bar = 1;
```

# ルールセット：[eslint-config-next](https://nextjs.org/docs/basic-features/eslint#eslint-config)

[github](https://github.com/vercel/next.js/blob/canary/packages/eslint-config-next/index.js)

次のプラグインを使用して、設定している

- eslint-plugin-react
- eslint-plugin-react-hooks
- eslint-plugin-next
- eslint-plugin-import
- eslint-plugin-jsx-a11y

## 設定方法

```js:.eslintrc.js
  extends: [
    'next',
  ],
```

## 設定される内容

[github](https://github.com/vercel/next.js/blob/canary/packages/eslint-config-next/index.js)

```js
  extends: [
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'plugin:@next/next/recommended',
  ],
  plugins: ['import', 'react', 'jsx-a11y'],
  rules: {
    'import/no-anonymous-default-export': 'warn',
    'react/react-in-jsx-scope': 'off',
    'react/prop-types': 'off',
    'jsx-a11y/alt-text': [
      'warn',
      {
        elements: ['img'],
        img: ['Image'],
      },
    ],
    'jsx-a11y/aria-props': 'warn',
    'jsx-a11y/aria-proptypes': 'warn',
    'jsx-a11y/aria-unsupported-elements': 'warn',
    'jsx-a11y/role-has-required-aria-props': 'warn',
    'jsx-a11y/role-supports-aria-props': 'warn',
  },
  parser: './parser.js',
  parserOptions: {
    requireConfigFile: false,
    sourceType: 'module',
    allowImportExportEverywhere: true,
    babelOptions: {
      presets: ['next/babel'],
      caller: {
        // Eslint supports top level await when a parser for it is included. We enable the parser by default for Babel.
        supportsTopLevelAwait: true,
      },
    },
  },
  overrides: [
    {
      files: ['**/*.ts?(x)'],
      parser: '@typescript-eslint/parser',
      parserOptions: {
        sourceType: 'module',
        ecmaFeatures: {
          jsx: true,
        },
        warnOnUnsupportedTypeScriptVersion: true,
      },
    },
  ],
  settings: {
    react: {
      version: 'detect',
    },
    'import/parsers': {
      [require.resolve('@typescript-eslint/parser')]: ['.ts', '.tsx', '.d.ts'],
    },
    'import/resolver': {
      [require.resolve('eslint-import-resolver-node')]: {
        extensions: ['.js', '.jsx', '.ts', '.tsx'],
      },
      [require.resolve('eslint-import-resolver-typescript')]: {
        alwaysTryTypes: true,
      },
    },
  },
  env: {
    browser: true,
    node: true,
  },
```

# プラグイン：[eslint-plugin-next](https://nextjs.org/docs/basic-features/eslint#eslint-plugin)

eslint-config-next にて定義されている

## ルールセット：@next/next/recommended

### 設定方法

```js:.eslintrc.js
extends: [
  'plugin:@next/next/recommended',
],
```

### 設定される内容

[github](https://github.com/vercel/next.js/blob/canary/packages/eslint-plugin-next/lib/index.js)

```js
  plugins: ['@next/next'],
  rules: {
    '@next/next/no-css-tags': 1,
    '@next/next/no-sync-scripts': 1,
    '@next/next/no-html-link-for-pages': 1,
    '@next/next/no-img-element': 1,
    '@next/next/no-unwanted-polyfillio': 1,
    '@next/next/no-page-custom-font': 1,
    '@next/next/no-title-in-document-head': 1,
    '@next/next/google-font-display': 1,
    '@next/next/google-font-preconnect': 1,
    '@next/next/link-passhref': 1,
    '@next/next/next-script-for-ga': 1,
    '@next/next/no-document-import-in-page': 2,
    '@next/next/no-head-import-in-document': 2,
    '@next/next/no-script-in-document': 2,
    '@next/next/no-script-in-head': 2,
    '@next/next/no-typos': 1,
    '@next/next/no-duplicate-head': 2,
    '@next/next/inline-script-id': 2,
  },
```

[github](https://github.com/vercel/next.js/tree/canary/packages/eslint-plugin-next)

## ルールセット：[next/core-web-vitals](https://nextjs.org/docs/basic-features/eslint#core-web-vitals)

### 設定方法

```js:.eslintrc.js
  extends: [
    'next/core-web-vitals',
  ],
```

### 設定される内容

[github](https://github.com/vercel/next.js/blob/canary/packages/eslint-plugin-next/lib/index.js)

```js
  plugins: ['@next/next'],
  extends: ['plugin:@next/next/recommended'],
  rules: {
    '@next/next/no-sync-scripts': 2,
    '@next/next/no-html-link-for-pages': 2,
  },
```

# プラグイン：[eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react)

eslint-config-next にて定義されている

## インストール

eslint-config-next を使用する場合はインストール不要
`yarn add -D eslint-plugin-react`

## ルールセット：react/recommended

[github](https://github.com/yannickcr/eslint-plugin-react/blob/master/index.js)

### 設定方法

```js:.eslintrc.js
  extends: [
    'plugin:react/recommended',
  ],
```

### 設定される内容

```js
  plugins: [
    'react'
  ],
  parserOptions: {
    ecmaFeatures: {
      jsx: true
    }
  },
  rules: {
    'react/display-name': 2,
    'react/jsx-key': 2,
    'react/jsx-no-comment-textnodes': 2,
    'react/jsx-no-duplicate-props': 2,
    'react/jsx-no-target-blank': 2,
    'react/jsx-no-undef': 2,
    'react/jsx-uses-react': 2,
    'react/jsx-uses-vars': 2,
    'react/no-children-prop': 2,
    'react/no-danger-with-children': 2,
    'react/no-deprecated': 2,
    'react/no-direct-mutation-state': 2,
    'react/no-find-dom-node': 2,
    'react/no-is-mounted': 2,
    'react/no-render-return-value': 2,
    'react/no-string-refs': 2,
    'react/no-unescaped-entities': 2,
    'react/no-unknown-property': 2,
    'react/no-unsafe': 0,
    'react/prop-types': 2,
    'react/react-in-jsx-scope': 2,
    'react/require-render-return': 2
  }
```

## ルール

✓：react/recommended にて設定される
💡：eslint-config-next にて設定される

|     | ルール名                                              | 内容                                         |
| :-: | :---------------------------------------------------- | :------------------------------------------- |
|  ✓  | [react-in-jsx-scope](#react-in-jsx-scope)             | jsx で react をインポートを必須にする        |
|  ✓  | [display-name](#display-name)                         |                                              |
| 💡  | [prop-types](#prop-types)                             |                                              |
|     | [jsx-handler-names](#jsx-handler-names)               | JSX でイベントハンドラーの命名規則を適用する |
|     | [destructuring-assignment](#destructuring-assignment) |                                              |

### [react-in-jsx-scope](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/react-in-jsx-scope.md)

Next.js や react v17 以降の場合は、react をインポートする必要ため off にする。

```js
  "rules": {
    "react/react-in-jsx-scope": "off",
  }
```

### [display-name](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/display-name.md)

```js
  "rules": {
    "react/display-name": "error",
  }
```

### [prop-types](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prop-types.md)

typescript を導入する場合は不要なので、off にする。
※react/recommended でエラーになるように設定されている。

```js
  "rules": {
    "react/prop-types": "off",
  }
```

### [jsx-handler-names](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-handler-names.md)

```js
  "rules": {
    "react/jsx-handler-names": [
      "error",
        {
          eventHandlerPrefix: "handle",
          eventHandlerPropPrefix: "on",
          checkLocalVariables: true,
          checkInlineFunction: true,
        },
    ],
  }
```

- チェック結果のサンプル

```js
// エラーあり
<MyComponent onChange={componentChanged} />
// エラーなし
<MyComponent onChange={handleChange} />
<MyComponent onChange={props.onFoo} />
```

### [destructuring-assignment](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/destructuring-assignment.md)

分割代入を禁止する

```js
  "rules": {
    "react/destructuring-assignment": ["error", "never"],
  }
```

- チェック結果のサンプル

```js
// エラーあり
const MyComponent = ({ id }) => {
  return <div id={id} />;
};
const MyComponent = (props, context) => {
  const { id } = props;
  return <div id={id} />;
};
const Foo = class extends React.PureComponent {
  render() {
    const { title } = this.context;
    return <div>{title}</div>;
  }
};

// エラーなし
const MyComponent = (props) => {
  return <div id={props.id} />;
};
const Foo = class extends React.PureComponent {
  render() {
    return <div>{this.context.foo}</div>;
  }
};
```

### [jsx-filename-extension](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-filename-extension.md)

jsx の拡張子を"tsx"のみにする

```js
  "rules": {
    "react/jsx-filename-extension": ["error", { "extensions": [".tsx"] }],
  }
```

# プラグイン：[eslint-plugin-react-hooks](https://github.com/facebook/react/tree/main/packages/eslint-plugin-react-hooks)

eslint-config-next にて定義されている

## ルールセット：react-hooks/recommended

### 設定方法

```js:.eslintrc.js
  extends: [
    'plugin:react-hooks/recommended',
  ],
```

### 設定される内容

[github](https://github.com/facebook/react/blob/main/packages/eslint-plugin-react-hooks/src/index.js)

```js
  plugins: ['react-hooks'],
  rules: {
    'react-hooks/rules-of-hooks': 'error',
    'react-hooks/exhaustive-deps': 'warn',
  },
```

# プラグイン： eslint-plugin-import

eslint-config-next にて定義されている

```js:eslint-config-next設定値
  rules: {
    'import/no-anonymous-default-export': 'warn',
  }
```

# プラグイン： eslint-plugin-jsx-a11y

eslint-config-next にて定義されている

```js:eslint-config-next設定値
  rules: {
    'jsx-a11y/alt-text': [
      'warn',
      {
        elements: ['img'],
        img: ['Image'],
      },
    ],
    'jsx-a11y/aria-props': 'warn',
    'jsx-a11y/aria-proptypes': 'warn',
    'jsx-a11y/aria-unsupported-elements': 'warn',
    'jsx-a11y/role-has-required-aria-props': 'warn',
    'jsx-a11y/role-supports-aria-props': 'warn',
  }
```

# プラグイン： [eslint-plugin-import](https://github.com/import-js/eslint-plugin-import)

# インストール

`yarn add -D eslint-plugin-import`

## 設定方法

```js:.eslintrc.js
  plugins: [
     "import",
  ],
```

### [no-default-export](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/no-default-export.md)

default export を使用禁止にして named export のみ利用可能にする。
[なぜ default export を使うべきではないのか？](https://engineering.linecorp.com/ja/blog/you-dont-need-default-export/)

```js:.eslintrc.js
  rules: {
    "import/no-default-export" : "error"
  }
  overrides: [
    {
      files: ["src/pages/**/*.tsx"],
      rules: { "import/no-default-export": "off" },
      parserOptions: {
        project: ["./tsconfig.json"],
      },
    }
  ]
```
