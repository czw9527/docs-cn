# 从 v3 迁移 {#migration-from-v3}

## Rollup 3 {#rollup-3}

Vite 现在正式启用 [Rollup 3](https://github.com/vitejs/vite/issues/9870)，这使得我们可以简化 Vite 内部的资源处理并同时拥有许多改进。详情请查看 [Rollup 3 版本记录](https://github.com/rollup/rollup/releases)。

Rollup 3 尽最大可能兼容了 Rollup 2。如果你在项目中使用了自定义的 [`rollupOptions`](../config/build-options.md#rollup-options) 并（升级后）遇到了问题，请先查看 [Rollup 迁移指南](https://rollupjs.org/guide/en/#migration) 来更新升级你的配置。

## 现代浏览器基准线变化 {#modern-browser-baseline-change}

当前对于现代浏览器的构建目标及现调整为了默认 `safari14` 以求更广的 ES2020 兼容性（从 `safari13` 升级）。这意味着现代化构建现在可以使用 [`BigInt`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)，同时 [空值合并运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing) 将不再被转译。如果你需要支持更旧版本的浏览器，你可以照常添加 [`@vitejs/plugin-legacy`](https://github.com/vitejs/vite/tree/main/packages/plugin-legacy)。

## 其他一般性变化 {#general-changes}

### 编码 {#encoding}

构建的默认字符集现在是 utf8（查看 [#10753](https://github.com/vitejs/vite/issues/10753) 了解更多细节）。

### 以字符串形式导入 CSS {#importing-css-as-a-string}

在过往的 Vite 3 之中，以默认导入形式导入一个 `.css` 文件的可能会造成对 CSS 的双重加载。

```ts
import cssString from './global.css'
```

这种双重加载出现的原因是 `.css` 文件是将会被释放（emit）到最终产物的，并且很可能 CSS 字符串将会在应用代码中被使用到，就比如被框架运行时注入的时候。对于现在的 Vite 4，`.css` 默认导出 [已经被废弃](https://github.com/vitejs/vite/issues/11094)。在这种情况下你将需要使用 `?inline` 这个查询参数后缀，而这时将不会将导入的 `.css` 样式文件释放到最终产物。

```ts
import stuff from './global.css?inline'
```

### 环境变量 {#environment-variables}

Vite 现在使用 `dotenv` 16 和 `dotenv-expand` 9（之前是 `dotenv` 14 和 `dotenv-expand` 5）如果你有一个包含 `#` 或者 `` ` `` 的值，你将需要将它们以双引号包裹起来。

```diff
-VITE_APP=ab#cd`ef
+VITE_APP="ab#cd`ef"
```

了解更多详情，请查看 [`dotenv`](https://github.com/motdotla/dotenv/blob/master/CHANGELOG.md) 和 [`dotenv-expand` 更新日志](https://github.com/motdotla/dotenv-expand/blob/master/CHANGELOG.md)。

## 进阶 {#advanced}

下列改动仅会影响到插件/工具的作者：

- [[#11036] feat(client)!: remove never implemented hot.decline](https://github.com/vitejs/vite/issues/11036)
  - 使用 `hot.invalidate` 来代替
- [[#9669] feat: align object interface for `transformIndexHtml` hook](https://github.com/vitejs/vite/issues/9669)
  - 使用 `order` 来代替 `enforce`

此外，还有其他一些只影响少数用户的破坏性变化。

- [[#11101] feat(ssr)!: remove dedupe and mode support for CJS](https://github.com/vitejs/vite/pull/11101)
  - 您应该迁移到 SSR 的默认 ESM 模式，CJS SSR 支持可能会在下一个 Vite 主要版本删除。
- [[#10475] feat: handle static assets in case-sensitive manner](https://github.com/vitejs/vite/pull/10475)
  - 您的项目不应该依赖于会被不同操作系统忽略大小写的文件名。
- [[#10996] fix!: make `NODE_ENV` more predictable](https://github.com/vitejs/vite/pull/10996)
  - 有关此更改的解释，请参阅 PR。
- [[#10903] refactor(types)!: remove facade type files](https://github.com/vitejs/vite/pull/10903)

## 从 v2 迁移 {#migration-from-v2}

请先查看之前 Vite v3 文档中的 [Migration from v2 Guide](https://cn.vitejs.dev/guide/migration-from-v2.html) 了解迁移到 v3 所需要的更改，然后再继续执行本页提到的相关更改。
