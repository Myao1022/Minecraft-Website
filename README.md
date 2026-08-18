# WinRAR 自解压 (SFX) 注释脚本命令速查表

> WinRAR 自解压文件中的“注释”不仅可以用作说明文字，更是一个功能强大的自动化脚本引擎。通过编写注释命令，可以实现自定义解压路径、自动运行程序、显示许可协议、静默安装等高级功能。

---

## 常用命令一览表

| 命令 | 作用与用法 | 示例 |
| :--- | :--- | :--- |
| **`Delete`** | **解压前清理**<br>在解压开始前，删除目标目录中已存在的指定文件或文件夹。可重复使用多次，适合软件升级时清理旧版本。 | `Delete="C:\Program Files\MyApp\old.dll"` |
| **`Presetup`** | **解压前执行**<br>在解压文件**之前**运行指定的外部程序（该程序需存在于用户系统中，而非压缩包内）。常用于执行卸载程序等预安装任务。 | `Presetup="C:\Windows\system32\uninstall.exe" /clean` |
| **`Setup`** | **解压后执行**<br>当所有文件解压完成后，运行指定的程序（通常为压缩包内的安装程序）。可重复使用，表示按顺序执行多个程序。 | `Setup="setup.exe"` |
| **`TempMode`** | **临时模式**<br>将所有文件解压到系统临时文件夹，启动 `Setup` 指定的程序，待该程序运行结束后自动删除临时文件夹及全部内容。 | `TempMode` |
| **`Path`** | **解压路径**<br>设置默认的解压目标路径。若只填写文件夹名（如 `MyApp`），则默认解压到 `Program Files` 目录下。 | `Path="C:\MyApp"` |
| **`SavePath`** | **保存解压路径**<br>将用户在解压界面选择的路径保存到注册表，下次运行时自动作为默认路径。**必须与 `Path` 命令配合使用。** | `Path="C:\MyApp"`<br>`SavePath` |
| **`Silent`** | **静默模式**<br>控制对话框和进度显示。<br>• `0` 或省略：显示所有对话框（默认）<br>• `1`：完全隐藏（无任何界面）<br>• `2`：隐藏启动对话框，但显示解压进度 | `Silent=1` |
| **`Overwrite`** | **文件覆盖**<br>处理已存在文件的方式。<br>• `0`：询问用户（默认）<br>• `1`：直接覆盖不询问<br>• `2`：跳过已存在的文件 | `Overwrite=1` |
| **`Title`** | **窗口标题**<br>设置自解压程序运行时的窗口标题。 | `Title="我的软件安装程序"` |
| **`Text`** | **对话框文本**<br>在解压对话框中添加说明文字。支持纯文本和 HTML 格式。使用 `{}` 包裹多行内容。 | `Text`<br>`{`<br>`欢迎使用本软件！`<br>`}` |
| **`License`** | **许可协议**<br>显示软件许可协议，用户必须接受才能继续解压。支持纯文本和 HTML 格式。 | `License="最终用户许可协议"`<br>`{`<br>`本软件为共享软件...`<br>`}` |
| **`Shortcut`** | **创建快捷方式**<br>解压后自动在指定位置创建快捷方式。具体参数见下表。 | `Shortcut=D, "myapp.exe", "My Company", "My Application", "MyApp"` |
| **`SetupCode`** | **返回安装程序退出码**<br>配合 `Setup` 命令使用，等待 `Setup` 指定的程序运行结束，并将该程序的退出代码作为自解压程序的退出代码返回。 | `SetupCode` |

---

## `Shortcut` 命令各参数详解

`Shortcut` 命令的完整格式为：  
`Shortcut=<DestType>, <SrcName>, <DestFolder>, <Description>, <ShortcutName>`

| 参数位置 | 名称 | 作用与用法 | 示例值 |
| :---: | :--- | :--- | :--- |
| 1 | **DestType** | 指定快捷方式创建的位置类型。<br>• `D` – 桌面<br>• `S` – 开始菜单程序组（需要配合第3参数）<br>• `P` – 开始菜单根目录<br>• `T` – 启动菜单<br>• `F` – “发送到”文件夹<br>• 也可以直接写绝对路径，如 `"C:\MyFolder"` | `D` |
| 2 | **SrcName** | 压缩包中的源文件名（要创建快捷方式的文件）。通常为 `.exe` 或文档文件。 | `"myapp.exe"` |
| 3 | **DestFolder** | 当 `DestType` 为 `S` 或 `T` 时，此项表示目标文件夹名称（快捷方式放在哪个子菜单下）。可以为空或 `""`。 | `"My Company"` |
| 4 | **Description** | 快捷方式的描述文字（鼠标悬停时显示的提示）。可以为空或 `""`。 | `"My Application"` |
| 5 | **ShortcutName** | 快捷方式的文件名（不含 `.lnk` 扩展名）。如果省略或为空，则使用源文件名（不含扩展名）。 | `"MyApp"` |

> **示例**：`Shortcut=D, "myapp.exe", "", "My Application", "MyApp"`  
> → 在**桌面**创建一个指向 `myapp.exe` 的快捷方式，名称为 `MyApp.lnk`，描述为 “My Application”。

---

## 提示

- **注释长度限制**：单个注释脚本的最大长度为 **62000 字节**。
- **HTML 支持**：`Text` 和 `License` 命令中可以使用 HTML 标签（如 `<b>`、`<br>`、`<font>` 等）来美化显示效果。
- **命令行集成**：
  - 使用 `-z<注释文件>` 参数指定注释文件：`rar a -sfx -zconfig.txt output.exe files`
  - 修改已有 SFX 文件的注释：`rar c -znewconfig.txt output.exe`
- **变量支持**：在 `Path`、`Delete` 等路径中可以使用系统环境变量，例如：
  - `%SystemRoot%` → Windows 系统目录（如 `C:\Windows`）
  - `%ProgramFiles%` → Program Files 目录
  - `%Temp%` → 临时文件夹

---

## 完整示例脚本

一个典型的软件安装自解压脚本：

```bash
;以下为 WinRAR 自解压注释脚本
Title=我的软件安装程序
Path="MyApp"
License=最终用户许可协议
{
  本软件受版权保护...
  请仔细阅读以下条款...
}
Text 
{
  欢迎使用我的软件！
  点击“安装”将把文件解压到以下目录。
}
Silent=2
Overwrite=1
Presetup="uninstaller.exe /clean"
Setup="setup.exe"
Shortcut=D, "myapp.exe", , , "MyApp"，