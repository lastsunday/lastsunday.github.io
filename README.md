# lastsunday.github.io

## Chrome extension

### Chrome extension develop hotload in vscode

结合VSCode插件[Run on Save](https://marketplace.visualstudio.com/items?itemName=emeraldwalk.RunOnSave)和Chrome插件[Extensions Reloader](https://chromewebstore.google.com/detail/extensions-reloader/fimgfedafeadlieiabdeeaodndnlbhid)实现保存源码文件时自动重新加载拓展程序（Hot Reload）

>当前在Windows环境下测试通过，其他系统未测试

### 1.插件的安装和配置

1. 在Chrome浏览器安装插件[Extensions Reloader](https://chromewebstore.google.com/detail/extensions-reloader/fimgfedafeadlieiabdeeaodndnlbhid)

1. 在Chrome浏览器地址栏上填入`chrome://extensions/shortcuts`跳转到快捷键设置页面，保持默认的`Alt+Shift+R`不变，并将Extensions Reloader下设置`Reload all extensions in development`为`Global`

1. 在Vscode安装插件[Run on Save](https://marketplace.visualstudio.com/items?itemName=emeraldwalk.RunOnSave)

1. 新建.vscode/settings.json，新增如下内容
```json
{
    //see https://solomonvictorino.com/reload-chrome-extensions-on-save-vs-code/
    //下面为优化方案，重新加载所有扩展,不会失去当前编辑窗口的焦点，
    //在Chrome安装插件Extensions Reloader(https://chromewebstore.google.com/detail/extensions-reloader/fimgfedafeadlieiabdeeaodndnlbhid)
    //执行Alt+Shift+R（效果：重新加载chrome扩展）【前提跳转到chrome://extensions/shortcuts的Extensions Reloader下设置Reload all extensions in development为Global】
    //下面为Windows下的设置（其他系统未测试）
    "emeraldwalk.runonsave": {
        "commands": [
            {
                "match": "src\\\\.*",
                "cmd": "powershell -Command \"$wshell = New-Object -ComObject wscript.shell;$wshell.SendKeys('%+R')\""
            }
        ]
    }
}
```

### 2.开发中的使用步骤

1. `npm run watch`

1. 打开Chrome浏览器页面

1. 修改并保存源代码文件（当前监听的文件夹为src，如需自定义，请编辑`.vscode/settings.json`下的emeraldwalk.runonsave->commands->match）

1. [Extensions Reloader](https://chromewebstore.google.com/detail/extensions-reloader/fimgfedafeadlieiabdeeaodndnlbhid)插件图标会显示OK字样，Chrome插件自动刷新完成

