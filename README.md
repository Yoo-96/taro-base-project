# 简介
本文讲述了基于 `Taro3.x`，使用 `ESLint`、`Prettier`、`husky`、`lint-staged` 等工具，对项目开发、代码提交中的进行规范的约束，避免不必要的错误。

[仓库地址 - github](https://github.com/Yoo-96/taro-base-project)
# 初始化项目
使用 [taro-cli](https://taro-docs.jd.com/docs/GETTING-STARTED/) 完成项目的初始化
# 配置ESLint和Prettier
## 安装插件
```bash
yarn add prettier eslint-plugin-prettier eslint-config-prettier eslint-plugin-jsx-a11y -D
```
## 配置.eslintrc文件
```json
{
  "extends": ["taro/react", "prettier", "plugin:prettier/recommended"],
  "parser": "@typescript-eslint/parser",
  "plugins": [
    "react",
    "jsx-a11y",
    "prettier",
    "@typescript-eslint"
  ],
  "rules": {
    // "@typescript-eslint/no-unused-vars": ["error", { "varsIgnorePattern": "Taro" }],
    "no-unused-vars": ["error", { "varsIgnorePattern": "Taro" }],
    "jsx-quotes": ["error", "prefer-double"],
    "quotes": [1, "single"],
    "class-methods-use-this": "off",
    "no-unused-expressions": "off",
    "linebreak-style": ["error", "unix"],
    "no-plusplus": ["error", { "allowForLoopAfterthoughts": true }],
    "no-use-before-define": ["off", { "functions": false }],
    "indent": ["error", 2, { "SwitchCase": 1 }],
    "semi": ["error", "always", { "omitLastInOneLineBlock": true }],
    "camelcase": 0,
    "no-irregular-whitespace": 0,
    "eol-last": 0,
    "no-const-assign": 2,
    "import/no-commonjs": "off",

    // react rules
    "react/jsx-uses-react": "off",
    "react/react-in-jsx-scope": "off",
    "react/jsx-indent": ["error", 2],
    "react/jsx-indent-props": ["error", 4],
    "react/static-property-placement": "off",
    "react/jsx-props-no-spreading": "off",
    "react/jsx-filename-extension": ["error", {
      "extensions": [".js", ".jsx", ".ts", ".tsx"] }
    ],
    "react/jsx-key": 2,
    "react/no-did-mount-set-state": 0,
    "react/no-did-update-set-state": 1,
    "react/no-direct-mutation-state": 2,
    "no-mixed-spaces-and-tabs": 0,

    // import rules
    "import/extensions": "off",
    "import/no-unresolved": "off",

    // prettier rules
    "prettier/prettier": ["error", {
      "printWidth": 200,
      "tabWidth": 2,
      "useTabs": false,
      "semi": true,
      "singleQuote": true,
      "quoteProps": "as-needed",
      "bracketSpacing": true,
      "trailingComma": "all",
      "jsxBracketSameLine": false,
      "jsxSingleQuote": false,
      "arrowParens": "always",
      "insertPragma": false,
      "requirePragma": false,
      "proseWrap": "never",
      "htmlWhitespaceSensitivity": "strict",
      "endOfLine": "lf"
    }]
  },
  "globals": {
    "definePageConfig": "readonly"
  }
}
```
## 增加 `lint` 命令
```json
// package.json

{
  ...
  "scripts": {
    ...
    "lint": "eslint --ext .js,.ts,.jsx,.tsx src --ignore-path .eslintignore"
  },
}

```
# 配置 Git Hooks
## 配置 husky

[husky 官方文档](https://typicode.github.io/husky/#/)
### 安装 `husky`
```bash
yarn add husky --dev
```
### 启用 `git hooks`
```bash
yarn husky install
```
### 增加 `pre-commit`
```bash
npx husky add .husky/pre-commit "npm run lint"
```
此时会在 `.husky/` 目录下创建 `pre-commit` 文件
### 配置 `package.json` 脚本
```javascript
{
    "scripts": {
        ...,
        "prepare": "husky install"
    }
}
```
### 添加 `commit` 提交内容规范 `git hooks`
```bash
npx husky add .husky/commit-msg 'yarn commitlint --edit "$1"'
```
此时会在 `.husky/` 目录下创建 `commit-msg` 文件
### 添加 `commitlint.config.js` 及相关依赖
[Git 提交的正确姿势：Commit message 编写指南](https://www.oschina.net/news/69705/git-commit-message-and-changelog-guide)
```bash
yarn add @commitlint/cli @commitlint/config-conventional -D
```
```javascript
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [2, 'always', ['feat', 'fix', 'docs', 'style', 'refactor', 'test', 'chore']],
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never'],
  },
};
```
## 配置 lint-staged
[lint-staged 官方文档](https://www.npmjs.com/package/lint-staged)
`lint-staged` 只会对提交的文件进行检查、修复处理，以保证代码是符合本项目 `eslint` 等规范的。
### 安装
```bash
yarn add lint-staged -D
```
### 添加 `.lintstagedrc` 配置文件及依赖
```json
{
  "*.{js,ts,vue}": ["npm run lint"],
  "*.{vue,css,scss,sass,less}": ["npm run lint:style"]
}
```
### 修改 `pre-commit` 文件
```
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

- npm run lint
+ yarn lint-staged --allow-empty "$1"
```
# 总结
到此讲完了 `Taro3.x` 如何配置 `ESLint`、`Prettier`、`husky`、`lint-staged` 等工具。

本文有错误的地方欢迎大家指正哈，谢谢大家的支持！
