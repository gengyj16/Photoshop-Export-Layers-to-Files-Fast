# Photoshop-Export-Layers-to-Files-Fast（简体中文）

> 🌐 English documentation: [README.md](README.md)
>
> 本 fork 在原项目基础上对脚本界面与提示信息进行了**简体中文本地化**，并新增了 X/Y 双向 padding 与裁切方向选择等功能。

本脚本可将 Photoshop 文档中的图层批量导出为独立文件，速度远快于 Adobe 自带的同类脚本。

本脚本并不追求与 Adobe 内置脚本完全功能对齐，而是提供更精简、更高效的体验。如有功能建议，欢迎[提交 Issue](https://github.com/antipalindrome/Photoshop-Export-Layers-to-Files-Fast/issues/new)，也欢迎参与贡献，让它变得更强大！

## 目录

- [使用方法](#使用方法)
- [示例截图](#示例截图)
- [功能特性](#功能特性)
- [字段补充说明](#字段补充说明)
- [批处理 / 动作](#批处理--动作)
- [运行环境](#运行环境)
- [参与贡献](#参与贡献)
- [反馈 / Bug](#反馈--bug)


## 使用方法

_免责声明：_ 本项目与 Adobe 没有任何关联。任何 Adobe 产品或 Adobe 脚本相关的问题，请直接联系 Adobe。我们使用过程中未遇到任何问题，但**请自行承担使用本脚本的风险**。我们不对任何数据丢失或 PSD 损坏负责，请务必先做好备份。

- 前往[发布页面](https://github.com/antipalindrome/Photoshop-Export-Layers-to-Files-Fast/releases)下载[最新发布版本](https://github.com/antipalindrome/Photoshop-Export-Layers-to-Files-Fast/releases/latest)。
- 在 Photoshop 中依次选择 `文件 -> 脚本 -> 浏览...`，并选择 `Export Layers To Files (Fast).jsx` 文件。
  - 注意：脚本运行时还需要 `Export Layers To Files (Fast)-progress_bar.json` 文件，否则会出现 “进度条资源已损坏” 的错误。请确保 `.jsx` 与 `.json` 文件位于同一目录。
- 你也可以将脚本文件全部复制到 `Photoshop > Presets > Scripts` 目录下，将其加入到脚本菜单：
  - Windows：`/Program Files/Adobe/Adobe Photoshop VERSION/Presets/Scripts`
  - macOS：`/Applications/Adobe Photoshop VERSION/Presets/Scripts`

## 示例截图

![脚本对话框截图](example.png)

## 功能特性

部分功能包括：

- 支持的导出格式：
  - PNG（8 位与 24 位）
  - JPEG
  - TIFF
  - PDF
  - Targa
  - BMP
  - PSD
- 支持嵌套图层组
- 可选择导出全部图层或仅导出可见图层
- 文件命名方式：图层名、图层 + 组名、自动图层索引等
- 最底层可作为公共背景处理
- 导出图像可使用图层尺寸或画布尺寸（裁切选项）
- 记忆上次使用的对话框设置
- 可单独导出选中图层组（其他内容保持不变）。借此可在固定背景与前景之上导出可变内容。
- 可将组以文件夹层级结构导出，名称冲突时会自动重命名

## 字段补充说明

### Selected Group（仅导出选中组）

仅导出当前选中的图层组。注意：必须在启动脚本前选中组，否则该选项不可用。以此方式运行时，其他图层不会被改动，因此最上层或最下层的可见图层依然会出现在导出结果中。

### Ignore Layers Starting With（忽略指定前缀的图层）

勾选该项后，可以指定一个前缀，用于匹配图层名。所有匹配的图层在导出时将被忽略。

例如，输入 `x` 时，所有以字母 `x` 开头的图层都不会被导出。

### Filenames（文件命名）

| 选项                                 | 示例                                                          |
| ------------------------------------ | ------------------------------------------------------------- |
| `Use Layer Name (strip extension)`   | `layer-3.png` => `layer-3.png`                                |
| `Use Layer Name (keep extension)`    | `layer-3.png` => `layer-3.png.png`                            |
| `Use layer and parent group names`   | `group 1 > layer-3` => `group-1-layer-3.png`                  |
| `Use index descending`               | 顶层为 `layer-10` 时 => `01`                                  |
| `Use index ascending`                | 顶层为 `layer-5` 时 => `5`                                    |

### Use Custom Delimiter（自定义分隔符）

`Use Custom Delimiter` 启用后，可使用自定义分隔符替换图层名以及前缀/后缀字段中的空格。Photoshop 不支持文件名中包含空格，因此未自定义时默认使用 `-` 作为分隔符。

例如：
`my layer 1` 将被导出为 `my-layer-1`

分隔符字段**不支持**以下字符：
`\`、`/`、`*`、`?`、`|`、`.`、`:`、`"`、`<`、`>`、`%`、`,`、`;`、`=`

### Prefix/Suffix（前缀 / 后缀）

`Prefix` 与 `Suffix` 会分别在导出文件名前后添加前缀与后缀，且与上文的所有命名选项兼容。

前缀或后缀字段**不支持**以下字符：
`\`、`/`、`*`、`?`、`|`、`:`、`"`、`<`、`>`

| 前缀      | 后缀         | 输出示例                       |
| --------- | ------------ | ------------------------------ |
| `test-`   | 无           | `test-layer-3.png`             |
| 无        | `.scale-100` | `layer-3.scale-100.png`        |
| `test-`   | `.scale-100` | `test-layer-3.scale-100.png`   |
| `{ii}-`   | 无           | `04-layer-3.png`               |
| `{YYYY}-` | 无           | `2021-layer-3.png`             |

#### 变量替换

前缀和后缀字段支持以下变量替换：

| 变量     | 说明                                                       |
| -------- | ---------------------------------------------------------- |
| `{i}`    | 替换为图层索引                                             |
| `{ii}`   | 替换为图层索引，前置补零至两位                             |
| `{iii}`  | 替换为图层索引，前置补零至三位                             |
| `{iiii}` | 替换为图层索引，前置补零至四位                             |
| `{ln}`   | 替换为图层名（在以索引命名时尤其有用）                     |
| `{dn}`   | 替换为文档名                                               |
| `{M}`    | 替换为月份                                                 |
| `{MM}`   | 替换为月份，前置补零                                       |
| `{D}`    | 替换为日期                                                 |
| `{DD}`   | 替换为日期，前置补零                                       |
| `{YY}`   | 替换为年份后两位                                           |
| `{YYYY}` | 替换为四位年份                                             |
| `{HH}`   | 替换为小时（带前置零）                                     |
| `{mm}`   | 替换为分钟（带前置零）                                     |
| `{ss}`   | 替换为秒（带前置零）                                       |
| `{sss}`  | 替换为毫秒（带前置零）                                     |

### 已知不足

- 暂不支持多画板（artboards）导出

## 批处理 / 动作

不少用户希望先将脚本配置好，再通过批处理或动作自动运行脚本。

按以下步骤即可：

1. 运行脚本，在对话框中设置好所需选项，然后点击 “保存并关闭”。
2. 用 TextEdit（Mac）、记事本（Windows）或代码 IDE 打开脚本文件。
3. 在脚本顶部找到 `var BATCH_OPERATION = false;` 这一行。
4. 将其改为 `var BATCH_OPERATION = true;`。
5. 重新运行脚本，便会以上次保存的设置自动执行。

如需再次修改设置，请将 `BATCH_OPERATION` 改回 `false` 后重新运行脚本。


## 运行环境

我们尽量让脚本向后兼容（最早支持 Adobe Photoshop CS2），但能够测试到的 Photoshop 版本与操作系统组合有限。如果当前版本出现问题，可以尝试从[历史发布版本](https://github.com/antipalindrome/Photoshop-Export-Layers-to-Files-Fast/releases)中下载旧版本。

## 参与贡献

非常欢迎贡献，感谢支持！

需要注意：

- 修改对话框 UI 时，请参考 `dev/dialog.js` 并将其导入 https://scriptui.joonas.me/。完成 UI 调整后，导出对话框，将顶部注释块复制回 `dev/dialog.js`，剩余部分复制到 `Export Layers To Files (Fast).jsx` 中的 `showDialog` 函数。
- 请确保新增 UI 在脚本运行之间能保留状态。例如，用户勾选某个复选框后，下次运行脚本时该复选框依然保持勾选。

## 反馈 / Bug

请通过 GitHub [提交 Issue](https://github.com/antipalindrome/Photoshop-Export-Layers-to-Files-Fast/issues) 反馈意见、Bug 或建议。

如反馈 Bug，请附带以下信息：

- 操作系统及版本
- Photoshop 版本及内部版本号
- 截图（如适用）
- 可复现该问题的 `.psd` 文件（越简单越好）
