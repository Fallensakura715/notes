# Markdown Syntax

## 目录
1.  [标题 (Headers)](#1-标题-headers)
2.  [文本样式 (Text Formatting)](#2-文本样式-text-formatting)
3.  [引用 (Blockquotes)](#3-引用-blockquotes)
4.  [列表 (Lists)](#4-列表-lists)
5.  [代码 (Code)](#5-代码-code)
6.  [链接 (Links)](#6-链接-links)
7.  [图片 (Images)](#7-图片-images)
8.  [水平分割线 (Horizontal Rules)](#8-水平分割线-horizontal-rules)
9.  [表格 (Tables)](#9-表格-tables)
10. [任务列表 (Task Lists)](#10-任务列表-task-lists)
11. [转义字符 (Escaping Characters)](#11-转义字符-escaping-characters)

---
### 1. Headers

使用 `#` 号可以创建标题，`#` 的数量代表了标题的级别。总共有六级标题。

```markdown
# 这是一级标题
## 这是二级标题
### 这是三级标题
#### 这是四级标题
##### 这是五级标题
###### 这是六级标题
```

**Example:**
# 这是一级标题
## 这是二级标题
### 这是三级标题
#### 这是四级标题
##### 这是五级标题
###### 这是六级标题

---

### 2. Text

你可以轻松地为文本添加粗体、斜体或删除线效果。

**Example:**

| 样式 | 语法 | 示例 | 渲染效果 |
| --- | --- | --- | --- |
| 斜体 | `*文字*` 或 `_文字_` | `*这是斜体*` | *这是斜体* |
| 粗体 | `**文字**` 或 `__文字__` | `**这是粗体**` | **这是粗体** |
| 粗斜体 | `***文字***` 或 `___文字___` | `***这是粗斜体***` | ***这是粗斜体*** |
| 删除线 | `~~文字~~` | `~~这是删除线~~` | ~~这是删除线~~ |

---

### 3. Quotes

在段落前添加 `>` 可以创建引用块。多个 `>` 可以创建嵌套引用。

```markdown
> 这是一个引用。
>
> > 这是一个嵌套的引用。
```

**Example:**
> 这是一个引用。
>
> > 这是一个嵌套的引用。


>[!NOTE]

>[!TIP]

>[!IMPORTANT]

> [!WARNING]

> [!CAUTION]
---

### 4. Lists

#### 无序列表 (Unordered Lists)

使用 `*`、`+` 或 `-` 来创建无序列表。

```markdown
* 苹果
* 香蕉
	* 黄色的香蕉(按了tab)
- 橙子
+ 葡萄
```

**Example:**
* 苹果
* 香蕉
	* 黄色的香蕉
- 橙子
+ 葡萄

#### Ordered Lists

使用数字加英文句点来创建有序列表。

```markdown
1. 第一步
2. 第二步
3. 第三步
   4. 步骤三的子步骤一
   5. 步骤三的子步骤二
```

**Example:**
1. 第一步
2. 第二步
3. 第三步
   4. 步骤三的子步骤一
   5. 步骤三的子步骤二

---

### 5. Code

#### 行内

使用反引号 `` ` `` 来包裹行内代码。

```markdown
在你的代码中调用 `printf()` 函数。
```

**Example:**
在你的代码中调用 `printf()` 函数。

#### 代码块

使用三个反引号 
```language
``` 
---
### 6. Links

```markdown
<!-- 内联链接 -->
[访问我的 GitHub 主页](https://github.com/Fallensakura715)

<!-- 带标题的链接 (鼠标悬停时显示) -->
[GitHub](https://github.com "前往 GitHub 官网")
```

**渲染效果:**

[访问我的 GitHub 主页](https://github.com/Fallensakura715)

[GitHub](https://github.com "前往 GitHub 官网")

---

### 7. Images

图片语法与链接非常相似，只是在前面多了一个感叹号 `!`。

```markdown
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png "GitHub 的 Logo")
```

**渲染效果:**
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png "GitHub 的 Logo")

---

### 8. 分割线

在一行中使用三个或更多的 `*`、`-` 或 `_` 可以创建一条水平分割线。

```markdown
---
***
___
```

**渲染效果:**
---
***
___
---

### 9. Tables

使用 `|` 来分隔列，使用 `-` 来分隔表头和表体。冒号 `:` 可以控制对齐方式。

```markdown
| 左对齐 | 居中对齐 | 右对齐 |
| :--- | :---: | ---: |
| 文本 | 文本 | 文本 |
| 内容 | 内容 | 内容 |
```

**渲染效果:**
| 左对齐 | 居中对齐 | 右对齐 |
| :--- | :---: | ---: |
| 文本 | 文本 | 文本 |
| 内容 | 内容 | 内容 |

---

### 10. Task

在列表项前添加 `[ ]` 或 `[x]` 可以创建任务列表。

```markdown
- [x] 完成 Markdown 教学文档
- [ ] 学习更多编程知识
- [ ] 休息一下
```

**渲染效果:**
- [x] 完成 Markdown 教学文档
- [ ] 学习更多编程知识
- [ ] 休息一下

---

### 11. Characters

如果你想显示在 Markdown 中有特殊含义的字符，可以在该字符前添加一个反斜杠 `\`。

```markdown
我想在这里显示一个星号 \*，而不是让它变成斜体。
我也想显示反引号 \`。
```

**渲染效果:**
我想在这里显示一个星号 \*，而不是让它变成斜体。
我也想显示反引号 \`。

---
