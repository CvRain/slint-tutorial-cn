---
title: Slint 语言翻译
date: 2024-01-09 12:07:45
tags:
  - Slint
---
# Slint 语言翻译
使用Slint自带基本翻译功能可以让你的程序实现切换不同的语言

完成下面几个步骤便可以完成程序的翻译工作：
  
1. 表明所有对用户可见且需要翻译的字符串并使用`@tr()`宏进行注释
2. 使用 `slint-tr-extractor` 工具提取带注释的字符串并生成 .pot 文件。  
3. 使用第三方工具将字符串翻译为目标语言，如 .po 文件。  
4. 使用 gettext 的 msgfmt 工具将 .po 文件转换为运行时可加载的 .mo 文件。  
5. 根据用户的区域设置，使用 Slint 的 API 在运行时选择并加载 .mo 文件。此时，所有标记为翻译的字符串将自动以目标语言呈现。

# 注释可翻译字符串
在 .slint 文件中使用 @tr 宏来标记要翻译的字符串。该宏将通过替换 {} 占位符来处理翻译和格式设置。
第一个参数必须是纯字符串文字，后跟参数(这个写法好像printf)：
```Slint
export component Example {
    property <string> name;
    Text {
        text: @tr("Hello, {}", name);
    }
}
```

## 格式化
`@tr `宏将标记为翻译的字符串中的每个` {} `占位符替换为相应的参数。还可以使用 `{0}、{1} `等对参数重新排序。即使原始字符串没有，翻译人员也可以使用有序占位符。

## 复数格式
当涉及可变数量元素的文本翻译应根据是单个元素还是多个元素而变化时，请使用复数格式。
在计算某些东西的个数时，使用`count`和表达式来表示，用 `| `形成复数形式和 `% `符号如下：
`@tr("I have {n} item" | "I have {n} items" % count)`.
（也就是说slint会根据数字的单数还是复数来选择使用什么格式的翻译，比如这里就会存在 "I have a/one item" 和 "I have two/three items"的情况）

在格式字符串中使用 {n} 来访问 % 之后的表达式。
```Slint
export component Example inherits Text {
    in property <int> score;
    in property <int> name;
    text: @tr("Hello {0}, you have one point" | "Hello {0}, you have {n} point" % score, name);
}
```

## 语境
通过使用 "..." => 语法向 @tr(...) 宏添加语境，消除具有相同源文本但上下文语境含义不同的字符串的翻译歧义。
使用上下文为译者提供额外的上下文信息，确保翻译准确且符合当前语境。
上下文必须是纯字符串文字，并且在 .pot 文件中显示为 msgctx。如果未指定，上下文默认为周围组件的名称。
```Slint
export component MenuItem {
    property <string> name : @tr("Name" => "Default Name"); // Default: `MenuItem` will be the context.
    property <string> tooltip : @tr("ToolTip" => "ToolTip for {}", name); // Specified: The context will be `ToolTip`.
}
```
## 提取可翻译字符串
使用`slint-tr-extractor` 工具从`.slint`文件生成出`.pot`文件，可以这样运行指令
```bash
find -name \*.slint | xargs slint-tr-extractor -o MY_PROJECT.pot
```
这将创建一个名为 MY_PROJECT.pot 的文件。将 MY_PROJECT 替换为您的实际项目名称。要了解项目名称如何影响翻译查找，请参阅以下部分。
`.pot` files are [Gettext](https://www.gnu.org/software/gettext/) template files.

# 翻译字符串
通过从 .pot 文件创建 .po 文件来开始新翻译。两种文件格式相同。您可以手动复制文件，也可以使用 Gettext 的 msginit 等工具来启动新的 .po 文件。
.po 文件将包含目标语言的字符串。
.po 和 .pot 文件是纯文本文件，您可以使用文本编辑器进行编辑。我们建议使用专用翻译工具来处理它们，例如：
- [poedit](https://poedit.net/)
- [OmegaT](https://omegat.org/)
- [Lokalize](https://userbase.kde.org/Lokalize)
- [Transifex](https://www.transifex.com/) (web interface)

## 转换`.po`文件到`.mo`文件
人类可读的 .po 文件需要转换为机器友好的 .mo 文件，这是一种读取效率非常高的二进制表示形式。
使用 [Gettext](https://www.gnu.org/software/gettext/)的`msgfmt` 命令行工具以将`.po`文件转换到`.mo`文件
```Bash
msgfmt translation.po -o translation.mo
```

## 在运行时选择并加载`.mo`文件
Slint 使用 Gettext 库在运行时加载翻译。 Gettext 将翻译文件生成在以下指定位置下：
`dir_name/locale/LC_MESSAGES/domain_name.mo`
- dir_name: 你自由原则的根目录文件夹
- locale: 给定目标语言的用户区域设置名称，例如 fr 代表法语，de 代表德语。区域设置通常是使用操作系统设置的环境变量来确定的。
- domain_name: 根据您使用 Slint 的编程语言进行选择。
# 在Rust中选择并加载翻译文件
首先，启用 `slint create` 的 `gettext `功能来访问翻译 API 并激活运行时翻译支持。
接下来，使用 `slint::init_translations！`指定 `.mo` 文件的基本位置。这是上一节方案中的 dir_name。 .mo 文件应位于相应的子目录中，并且它们的文件名 - 域名 - 必须与 Cargo.toml 中的包名称匹配。这通常与crate名称相同。
举个🌰:
```Rust
slint::init_translations!(concat!(env!("CARGO_MANIFEST_DIR"), "/lang/"));
```

假设您的 Cargo.toml 包含以下行，并且用户的区域设置为 fr：
```toml
[package]
name = "gallery"
```
通过这些设置，Slint 将在 lang/fr/LC_MESSAGES/gallery.mo 中查找 gallery.mo。

# 在C++中选择并加载翻译文件
首先，在编译 Slint 时启用 `SLINT_FEATURE_GETTEXT` cmake 选项，以访问翻译 API 并激活运行时翻译支持。
在C++应用中使用CMake，用`domain_name`作为cmake目标名称

接下来，使用标准 gettext 库将文本域绑定到路径。
```CMake
find_package(Intl)
if(Intl_FOUND)
    target_compile_definitions(gallery PRIVATE HAVE_GETTEXT SRC_DIR="${CMAKE_CURRENT_SOURCE_DIR}")
    target_link_libraries(gallery PRIVATE Intl::Intl)
endif()
```
(可以通过vcpkg安装第三方库，非常的方便)

然后可以设置区域设置和文本域
```C++
#ifdef HAVE_GETTEXT
#    include <locale>
#    include <libintl.h>
#endif

int main()
{
#ifdef HAVE_GETTEXT
    bindtextdomain("my_application", SRC_DIR "/lang/");
    std::locale::global(std::locale(""));
#endif
   //...
}
```
假设您正在使用上述内容，并且用户的区域设置设置为 fr，Slint 将在 lang/fr/LC_MESSAGES/gallery.mo 中查找 gallery.mo。

# 使用 slint-viewer 预览翻译
预览` .slint` 文件时使用 `slint-viewer `预览翻译：
1. 启用`gettext`功能在编译slint-viewer时
2. 使用`--translation-domain`和`translation-dir`选项加载并展示本地化翻译文件，语言的选择基于当前系统设定的语言。