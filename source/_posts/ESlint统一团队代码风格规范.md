---
title: ESlint 统一团队代码风格规范
tags: 
- tools
- eslint
date: 2022-01-04
---

## 统一团队代码风格规范

1 安装 eslint
输入命令 `yarn add eslint -D`,
项目中输入命令 `npx eslint --init`
然后开始自己选择项目所需要的内容。

笔者这里选择的是 "React" + "TypeScript" + "Airbnb: https://github.com/airbnb/javascript"

然后系统就自动下载安装了以下的安装包并创建了一个 .eslintrc的配置文件
<!--more-->
@typescript-eslint/parser@latest
@typescript-eslint/eslint-plugin@latest

eslint-plugin-react@^7.27.1
eslint-plugin-react-hooks@^4.3.0

eslint-config-airbnb@latest eslint@^7.32.0 || ^8.2.0
eslint-plugin-jsx-a11y@^6.5.1

eslint-plugin-import@^2.25.3

1. @typescript-eslint/parser 是ESLint的解析器，用于解析typescript，从而检查和规范Typescript代码，ts项目中必须执行解析器为@typescript-eslint/parser，才能正确的检测和规范TS代码
2. @typescript-eslint/eslint-plugin：这是一个ESLint插件，包含了各类定义好的检测Typescript代码的规范
3. eslint-plugin-react 是React专用的校验规则插件，使用的时候也可以简写成 react
4. eslint-plugin-react-hooks 是React Hooks的规范 [参见这里](https://react.docschina.org/docs/hooks-rules.html)
5. eslint-config-airbnb  提供了所有的Airbnb的ESLint配置，作为一种扩展的共享配置，该工具包包含了react的相关Eslint规则(eslint-plugin-react与eslint-plugin-jsx-a11y)
6. eslint-plugin-jsx-a11y 该依赖包专注于检查JSX元素的可访问性。
7. eslint-plugin-import 该插件想要支持对ES2015+ (ES6+) import/export语法的校验, 并防止一些文件路径拼错或者是导入名称错误的情况

2 安装 prettier

`yarn add prettier -D`

`yarn add eslint-plugin-prettier eslint-config-prettier -D`

1. eslint-plugin-prettier 该插件辅助Eslint可以平滑地与Prettier一起协作，并将Prettier的解析作为Eslint的一部分
2. eslint-config-prettier 关闭一些不必要的或者是与prettier冲突的lint选项。这样我们就不会看到一些error同时出现两次

如果你同时使用了上述的两种配置，那么你可以通过如下方式，简化你的配置。

```json
{
  "extends": ["plugin:prettier/recommended"]
}
```


3 优化

`yarn add eslint-import-resolver-alias -D` 路径别名

4 完整配置

```json
{
    "env": {
        "browser": true,
        "es2021": true,
        "commonjs": true
    },
    "extends": ["plugin:react/recommended", "airbnb", "plugin:prettier/recommended"],
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "plugins": ["react", "@typescript-eslint", "prettier"],
    "rules": {
        // prettier 输出错误
        "prettier/prettier": "error",
        // 解决LF 与 CRLF的问题
        "linebreak-style": [0],
        // double quotes
        "quotes": [2, "double", "avoid-escape"],
        // JSX not allowed in files with extension '.tsx'eslint
        "react/jsx-filename-extension": [2, { "extensions": ["tsx", "js", "jsx"] }],
        // Function component is not a function declaration eslint
        "react/function-component-definition": [
            2,
            {
                "namedComponents": "arrow-function",
                "unnamedComponents": "arrow-function"
            }
        ],
        // Missing file extension "ts" for "xxxxxxxxx"
        "import/extensions": [
            "error",
            "ignorePackages",
            {
                "js": "never",
                "jsx": "never",
                "ts": "never",
                "tsx": "never",
                "": "never"
            }
        ],

        "import/no-unresolved": [2, { "caseSensitive": false }],

        //   Static HTML elements with event handlers require a role.   或者解决：  标签   role="button"  tabIndex={0} 增强语义化
        // "jsx-a11y/no-static-element-interactions": 0,

        //  Visible, non-interactive elements with click handlers must have at least one keyboard listener. 为了残障人士 onClick 建议这样写 onkeyDown
        "jsx-a11y/click-events-have-key-events": 0,
        // 解决ts声明中参数未使用报错
        "@typescript-eslint/no-unused-vars": [2],

        // Expected 'this' to be used by class method 'xxxx'.
        "class-methods-use-this": [0, { "enforceForClassFields": false }],
        // Unary operator '++' used.   我还是想用 ++
        "no-plusplus": ["error", { "allowForLoopAfterthoughts": true }],
        // Non-interactive elements should not be assigned mouse or keyboard event listeners.
        "jsx-a11y/no-noninteractive-element-interactions": [
            "off",
            {
                "handlers": ["onClick", "onMouseDown", "onMouseUp", "onKeyPress", "onKeyDown", "onKeyUp"]
            }
        ],
        "import/no-extraneous-dependencies": ["error", { "devDependencies": true }],
        "no-unused-expressions": ["error", { "allowShortCircuit": true }],
        "react/jsx-props-no-spreading": [
            "off",
            {
                "html": "ignore",
                "custom": "ignore",
                "explicitSpread": "ignore",
                "exceptions": []
            }
        ],
        "react/jsx-boolean-value": ["off"]
    },
    "settings": {
        "import/resolver": {
            "node": {
                "extensions": [".js", ".jsx", ".ts", ".tsx"]
            },
            "alias": {
                "map": [
                    ["@", "./src"],
                    ["@/models", "./src/models"],
                    ["@/pages", "./src/pages"],
                    ["@/components", "./src/components"],
                    ["@/assets", "./src/assets"]
                ],
                "extensions": [".ts", ".tsx"]
            }
        }
    }
}

```

参考资料：
[深入理解ESLint](https://zhuanlan.zhihu.com/p/75531199)
[Eslint生态依赖包介绍](https://juejin.cn/post/6844903859488292871)
[npm包与VS Code 插件的关系](https://juejin.cn/post/6990929456382607374)

