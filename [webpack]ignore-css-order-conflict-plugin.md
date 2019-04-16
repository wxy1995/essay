正如 [mini-css-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin/issues/250) 出现的问题。

作者解释为：我们项目的各个模块在引用组件的时候，使用了不同的顺序引入。例如，
```javascript
// A.js
import { Table, Button } from 'xxx';

// B.js
import { Button, Table } from 'xxx';
```
这种情况，最终打包的顺序是 Table Button。

这个问题并不会影响代码的执行效果，只是build时会出现大量warning message，看着非常恼人。

作者也给出了两种方案：
1. 所有引入固定一个顺序
2. `stats.warningFilter`

目前已有PR在跟进解决，我在项目中写了个插件来忽略这个问题导致的 warning message，兼容 webpack v3 & v4。

```javascript
module.exports = class IgnoreCssOrderConflictPlugin {
    apply(compiler) {
        const messageRegExp = /chunk \w+ \[mini-css-extract-plugin[^]*Conflicting order between:/
        function doneHook(stats) {
            stats.compilation.warnings = stats.compilation.warnings.filter(function(warn) {
                if (messageRegExp.test(warn.message)) {
                    return false
                }
                return true;
            })
        }
        if (compiler.hooks) {
            compiler.hooks.done.tap("IgnoreCssOrderConflictPlugin", doneHook)
        } else {
            compiler.plugin("done", doneHook)
        }
    }
}
```
