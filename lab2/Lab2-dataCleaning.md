# Lab 2 数据清洗

实验要求：完成第3部分的数据清洗任务并提交对应的脚本或者数据文件。
提交格式：提交一个压缩包命名为`学号_姓名_Lab2.zip`，里面包含一个文件夹`submission`，存放要求提交的脚本文件和处理后的数据文件。

注：如果你的Windows环境无法运行.sh脚本文件，你可能需要使用Linux环境，或者在Windows下安装[Git for Windows](https://git-scm.com/downloads/win)，并在Git Bash中执行shell脚本：

* 安装 Git 后，右键点击 `.sh` 文件所在目录，选择 **Git Bash Here**。
* 在打开的 Git Bash 窗口中，运行脚本：

```bash
bash your_script.sh
```

## Dataset

**lab2** 目录下包含 10 个数据集，位于 **data** 子目录中。以下是简要概述：

- **crime-clean.txt**：包含2004年至2008年期间，每个美国州和哥伦比亚特区的报告犯罪率数据。

- **crime-unclean.txt**： **crime-clean.txt**的一个版本，其中一些数据缺失。

- **labor.csv**：劳动力信息的小型数据集，每条记录的每个字段都显示在单独的行上。

- **lizzo_appearances.json**：从维基百科抓取的JSON文件，包含艺术家 Lizzo 在不同事件中的出场列表。示例片段：

  ```json
  {"Year":"2014","Title":"Made in Chelsea: NYC","Notes":"Season 1, episode 4"},{"Year":"2014","Title":"Late Show with David Letterman","Notes":"Season 22, episode 29"}
  ```

- **synsets.txt**：同义词及其含义的数据集。每行包含一个同义词集，格式如下：

  ```css
  ID,<synonyms separated by spaces>,<different meanings separated by semicolons>
  ```

- **top2018.csv**：一个包含2018年 Spotify 排行前100首歌曲的 CSV 文件，属性包括“舞蹈性”、“响度”、“时间签名”等等。

- **twitter.json.gz**：一个包含推文的数据集，已通过 gzip 压缩。

- **wmbr.txt**：一个文本文件，包含在 WMBR（MIT 学生电台）中播放的歌曲的日期，涵盖了部分 DJ 和艺术家的数据。示例片段：

  ```css
  Date:Nov 22, 2017
  Artist:Willow Smith
  Song:Wait a Minute [slowed]
  Album: 
  Label: 
  Show:[insert label here]
  DJ:Joana
  ...
  Date:Jun 14, 2019
  Artist:Billie Eilish
  Song:Bury A Friend
  Album: 
  Label: 
  Show:BABEs: The Radio Experience
  DJ:Claire Traweek
  ```

- **worldcup-semiclean.txt**：一个经过部分清理的版本，来自 **worldcup.txt**，去除了维基源关键词和其他格式问题。

- **worldcup.txt**：来自维基百科 FIFA（足球）世界杯页面的一个片段，对应页面末尾列出排名前四的球队的表格。

## 第1部分：在python中使用正则表达式对数据进行清洗。

**1. 什么是正则表达式？** 正则表达式（Regex）是一种强大的文本匹配工具，可以帮助我们在大量文本中提取特定的模式或内容，常用于数据清洗、文本分析等任务。

[正则表达式【详细解读】-CSDN博客](https://blog.csdn.net/weixin_47734224/article/details/142553961)

在 Python 中，我们使用 `re` 模块来操作正则表达式。

------

**2. 安装和导入 Python `re` 模块**

首先，确保你已经安装了 Python。`re` 模块是 Python 内置的，所以你不需要额外安装，只需要在你的 Python 代码中导入它：

```python
import re
```

------

**3. 正则表达式基本语法**

- `\d`：匹配任何数字（0-9）。
- `\w`：匹配任何字母或数字字符。
- `\s`：匹配任何空白字符（空格、制表符等）。
- `.`：匹配任何单个字符（除了换行符）。
- `^`：匹配字符串的开头。
- `$`：匹配字符串的结尾。
- `[]`：定义字符类，匹配方括号内的任何字符。
- `|`：表示“或”操作，匹配左边或右边的表达式。

------

**4. 常用操作**

- **查找匹配的内容**
  使用 `re.findall()` 查找所有符合正则表达式的部分：

  ```python
  import re
  text = "My phone number is 123-456-7890 and my friend's is 987-654-3210."
  phone_numbers = re.findall(r'\d{3}-\d{3}-\d{4}', text)
  print(phone_numbers)
  ```

  输出：
  `['123-456-7890', '987-654-3210']`

- **替换匹配的内容**
  使用 `re.sub()` 替换匹配的内容：

  ```python
  text = "My email is user@example.com"
  cleaned_text = re.sub(r'@\w+\.\w+', '@domain.com', text)
  print(cleaned_text)
  ```

  输出：
  `My email is user@domain.com`

- **提取特定部分**
  使用捕获组 `()` 来提取文本中的特定部分：

  ```python
  text = "Product ID: 12345, Price: $99.99"
  match = re.search(r'Product ID: (\d+), Price: \$(\d+\.\d+)', text)
  if match:
      print("Product ID:", match.group(1))  # 12345
      print("Price:", match.group(2))       # 99.99
  ```

------

**5. 数据清洗实例**

假设你有一个包含以下内容的文本文件（`data.txt`）：

```css
John, 2023-05-01, $50
Jane, 2023-05-02, $60
Bob, 2023-05-01, $40
```

你想提取姓名、日期和金额信息，并清理掉多余的空格或特殊字符。可以使用正则表达式进行提取：

```python
import re

# 打开文件读取数据
with open('data.txt', 'r') as file:
    lines = file.readlines()

# 使用正则表达式提取数据
for line in lines:
    match = re.match(r'(\w+),\s*(\d{4}-\d{2}-\d{2}),\s*\$(\d+)', line)
    if match:
        name = match.group(1)
        date = match.group(2)
        amount = match.group(3)
        print(f"Name: {name}, Date: {date}, Amount: ${amount}")
```

输出：

```css
Name: John, Date: 2023-05-01, Amount: $50
Name: Jane, Date: 2023-05-02, Amount: $60
Name: Bob, Date: 2023-05-01, Amount: $40
```

------

**6. 小技巧**

- **匹配多个空格或特殊字符**
  如果你想忽略多余的空格或其他分隔符，可以使用 `\s+`，它表示匹配一个或多个空白字符。例如：

  ```python
  text = "Name  :  John  ,  Age :  25"
  cleaned_text = re.sub(r'\s+', ' ', text)  # 将多个空格替换为一个空格
  print(cleaned_text)
  ```

  输出：
  `Name : John , Age : 25`

- **忽略大小写匹配**
  在正则表达式中加上 `(?i)`，可以忽略大小写：

  ```python
  text = "hello World"
  match = re.search(r'hello', text, re.IGNORECASE)
  if match:
      print("Match found!")
  ```

## 第2部分：使用 Unix 工具进行数据清洗

三种 `UNIX` 工具——`sed`、`awk` 和 `grep`，在快速清理和转换数据方面非常有用（这些工具自 UNIX 诞生以来就已经存在）。通过与其他 Unix 工具（如 `sort`、`uniq`、`tail`、`head`、`paste` 等）结合使用，你可以完成许多简单的数据解析和清洗任务。

### 实际样例

你可以通过这些工具来练习并熟悉它们的基本用法。

举个例子，以下命令序列可以用来回答“找出发推最多的五个 Twitter 用户 ID（uid）”这个问题。注意，在下面的示例中，我们使用的是 `zgrep` 变种，它允许我们在压缩数据（gzipped 数据）上进行操作。

```bash
$ zgrep "created_at" ../data/twitter.json.gz \
   | sed 's/"user":{"id":\([0-9]*\).*/XXXXX\1/' \
   | sed 's/.*XXXXX\([0-9]*\)$/\1/' \
   | sort \
   | uniq -c \
   | sort -n \
   | tail -5
```

第一阶段（`zgrep`）排除了被删除的推文，`sed` 命令提取每行中的第一个“用户 ID”，`sort` 对用户 ID 进行排序，`uniq -c` 统计唯一的条目（即用户 ID）。最后的 `sort -n | tail -5` 返回排名前 5 的用户 ID。

需要注意的是，以下两个 `sed` 命令虽然看起来可以合并，但实际上并不正确——你可以自己试试为什么。

```bash
$ zgrep "created_at" ../data/twitter.json.gz \
  | sed 's/.*"user":{"id":\([0-9]*\).*/\1/' \
  | sort \
  | uniq -c \
  | sort -n \
  | tail -5
```

#### 通用工具

- `cat` 用于列出文件内容：

```bash
$ cat ../data/worldcup-semiclean.txt
!Team!!Titles!!Runners-up!!Thirdplace!!Fourthplace!!|Top4Total
|-
BRA
|1958,1962,1970,1994,2002
|1950,1998
...
```

- `tail` 也是类似的功能，但提供了一个方便的选项来指定（1-indexed）开始的行数。可以很方便地帮你跳过 CSV 文件的表头：

```bash
$ tail +3 ../data/worldcup-semiclean.txt
BRA
|1958,1962,1970,1994,2002
|1950,1998
...
```

- 类似于 `tail` 用来跳过行，`cut` 可以帮助我们跳过某些字段。你可以使用 `-d` 来指定分隔符，使用 `-f` 来选择一个或多个字段进行打印。使用 `--complement -f` 可以指定不打印哪些字段。

```bash
$ cut -d "," -f 1 ../data/synsets.txt
1
2
3
4
...
```

- `sort` 用于对文本文件中的行进行排序。它提供了许多有用的标志来指定像大小写敏感、排序关键字位置（即每行中字段的顺序）等内容。你可以通过 `sort --help` 查看完整的标志列表。
- `uniq` 用于去除文本文件中**相邻**的重复行。使用 `-c` 标志时，会在每行前加上该行重复出现的次数。
- `wc` 用于计算文本文件中的字符数（`-c`）、单词数（`-w`）或行数（`-l`）。

#### 工具 1：`grep`

`grep` 的基本语法是：

```bash
$ grep 'regexp' filename
```

或者等效地（使用 UNIX 管道）：

```bash
$ cat filename | grep 'regexp'
```

输出只包含文件中匹配正则表达式的行。两个有用的 `grep` 选项：`grep -v` 会输出那些**不**匹配正则表达式的行，`grep -i` 会忽略大小写匹配。

#### 工具 2：`sed`

`sed` 是一种流编辑器。`sed` 的基本语法是：

```bash
$ sed 's/regexp/replacement/g' filename
```

对于输入的每一行，匹配正则表达式的部分（如果有的话）会被替换成 `replacement`。在一次操作中，`sed` 只能处理一行。你可以使用 `\(\)` 来引用匹配模式中的一部分。在上面的 `sed` 命令中，`\( \)` 捕获了用户 ID 部分，并且可以在替换中使用 `\1` 来引用。

例如，下面的命令就是我们用来清理 `worldcup.txt` 并生成 `worldcup-semiclean.txt` 的命令：

```bash
$ cat ../data/worldcup.txt \
  | sed \
    's/\[\[\([0-9]*\)[^]]*\]\]/\1/g;
    s/.*fb|\([A-Za-z]*\)}}/\1/g; 
    s/data-sort[^|]*//g;
    s/<sup><\/sup>//g;
    s/<br \/>//g;
    s/|style=[^|]*//g;
    s/|align=center[^|]*//g;
    s/|[ ]*[0-9] /|/g;
    s/.*div.*//g;
    s/|[a-z]*{{N\/a|}}/|0/g;
    s|[()]||g;
    s/ //g;
    /^$/d;' > ../data/worldcup-semiclean.txt
```

#### 工具 3：`awk`

最后，`awk` 是一种强大的脚本语言。`awk` 的基本语法是：

```bash
$ awk -F',' \
  'BEGIN{commands}
  /regexp1/ {command1}
  /regexp2/ {command2}
  END{commands}'
```

对于每一行，`awk` 会依次匹配正则表达式，如果匹配成功，则执行对应的命令（多个命令可以同时执行）。`BEGIN` 和 `END` 均为可选部分。`-F','` 表示将每行按逗号分隔成字段，这些字段可以在正则表达式和命令中通过 `$1`、`$2` 等访问。

#### 示例

以下是一些用来展示这些工具的示例，在后续问题中会用到一些类似的技巧。

1. **合并连续的行（即一个记录）**，用于 `labor.csv` 文件（这通常称为 *wrap*）。我们在 `combined` 中保持“正在进行中的记录”，并在每次遇到以 `Series Id:` 开头的行时打印并重新初始化。对于所有其他行，我们只需将它们（用逗号分隔）附加到 `combined` 后面。最后，我们确保在结束时打印最后的记录。

```bash
$ cat ../data/labor.csv \
  | awk \
    '/^Series Id:/ {print combined; combined = $0}
    !/^Series Id:/ {combined = combined", "$0;}
    END {print combined}'
```

1. 在 `crime-clean.txt` 中，以下命令会进行 *fill*（第一行输出为："Alabama, 2004, 4029.3"）。首先，使用 `grep` 排除那些只包含逗号的行。然后，使用 `awk` 提取每行的州名（第四个单词），或者提取包含数据的行，输出州名和数据。

```bash
$ cat ../data/crime-clean.txt \
   | grep -v '^,$' \
   | awk \
   '/^[A-Z]/ {state = $4} 
    !/^[A-Z]/ {print state, $0}'
```

1. 在 `crime-clean.txt` 中，以下脚本将数据转换为表格格式的 CSV，其中列为 `[State, 2004, 2005, 2006, 2007, 2008]`。请注意，假设数据是完美的（即没有缺失或额外的值，年份始终按相同顺序）。

```bash
$ cat ../data/crime-clean.txt \
   | grep -v '^,$' \
   |
```

## 第3部分：实验内容

### Part 1 Unit工具的使用

#### 问题1：

从 `synsets.txt` 开始，编写一个脚本，适当使用上述工具生成一个**词汇-语义**对的列表，并将其输出到 `submission` 目录中的 `q1.csv` 文件。输出应如下所示：

```
'hood,(slang) a neighborhood
1530s,the decade from 1530 to 1539
...
angstrom,a metric unit of length equal to one ten billionth of a meter (or 0.0001 micron)
angstrom, used to specify wavelengths of electromagnetic radiation
angstrom_unit,a metric unit of length equal to one ten billionth of a meter (or 0.0001 micron)
angstrom_unit, used to specify wavelengths of electromagnetic radiation
...
```

提示：考虑 `awk` 的 `split` 函数和 `for 循环` 结构。

在提交目录中将脚本保存为 `q1.sh`，并添加注释。

#### 问题2： 

在`q1.csv` 的基础上继续处理，并编写另一个脚本，确定此数据集中出现的唯一单词的数量（即 `q1.csv` 第一列中不同条目的数量），并将其输出到提交目录中的 `q2.csv` 。

将您的脚本保存为提交目录中的 `q2.sh`，并添加注释。

#### 问题3：

为处理 `worldcup-semiclean.txt` 编写一个脚本，适当使用上述工具生成的输出格式如下所示，并输出结果到提交目录中的`q3.csv`，即输出中的每一行包含一个国家、年份和该国当年的排名（如果进入前 4 名）：

```css
BRA,1958,1
BRA,1962,1
BRA,1970,1
BRA,1994,1
BRA,2002,1
BRA,1950,2
BRA,1998,2
...
```

在提交目录中将脚本保存为 `q3.sh`，并添加注释。

#### 问题4：

根据 `q3.csv`，每个国家赢得世界杯的次数是多少？编写一个脚本来计算此值，通过生成如下输出并将其输出到提交目录中的 `q4.csv`：

```css
BRA,5
GER,4
ITA,4
...
```

在提交目录中将脚本保存为 `q4.sh`，并添加注释。

### Part 2 不同格式数据处理

在这一部分，你将使用 Pandas 来查看不同格式的音乐数据（`lizzo_appearances.json`、`top2018.csv` 和 `wmbr.txt`），并回答有关数据的问题。你将需要使用我们之前介绍的 Unix 工具和 Pandas 来清理数据，并在清理后的数据上执行查询。你可能会发现，使用常见的格式（例如 CSV 或 JSON）来清理后的数据集将显著帮助你整合它们。像 `pandas.read_json()` 这样的函数也可能会派上用场。

#### 问题1：处理`wmbr.txt`

处理并封装（参见本实验的第 1 部分）wmbr.txt 中的数据，使其转化为包含相同信息，但易于查询的表示形式。请注意，每首歌曲的所有字段都存在，但其中一些字段可能为空。一些艺术家姓名包含拼写错误（例如“Billie Ellish”和“Billie Eilish”都出现），一些歌曲可能会重复，并且艺术家可能合作（例如“Dua Lipa”或“Cardi B”都有与其他人合作的歌曲）艺术家）。您的工作是确保您的数据清理脚本能够正确处理这些情况（提示：`uniq` Unix 工具可能会派上用场）。

将清理后的数据提交为 `q5.csv`。请注意，您选择的清理方法有一定的灵活性，但生成的文件的格式应一致。还要以 `q5.sh` 形式提交您的代码，包括对您应用的转换的注释。

#### 问题2：

哪些Artist曾在 WMBR 进行过现场(**live**)演奏或现场(**live**)录制？将您的答案输出到提交目录中的 `q6.csv`，每行一位艺术家，按艺术家姓名按字典顺序升序排序。如果您使用命令行工具，请以 `q6.sh` 形式提交您的代码；如果您使用 Pandas，请以 q6.py 形式提交您的代码。

#### 问题3：

对于 [Lizzo](https://en.wikipedia.org/wiki/Lizzo) 出现在脱口秀（talk show）过的年份（参见 `lizzo_appearances.json`；我们假设 talk show 可以通过标题中明确包含“show”这个词来识别），使用 Pandas 列出她担任主唱或合作者（例如，“featured（`feat.`）”也算在内）并在 WMBR 播放的所有歌曲，并统计这些歌曲在 `wmbr.txt` 播放的次数。请注意，`lizzo_appearances.json` 中的条目不以换行符分隔。

数据中可能包含特殊字符（例如注音符号、反斜杠等）。将你的答案输出到 `q7.csv` 文件中，存放在 `submission/` 目录下，每行包含一条记录。每条记录应有两列：歌曲标题和播放次数（见下方示例）。首先按播放次数降序排序，然后按歌曲名称升序排序。将你的代码提交为 `q7.py`（同样保存在 `submission/` 目录中）。以下是一个输出示例：

```css
song,num_plays
abc,7
def,7
aaa,6
...
```

#### 问题4：

对于那些其歌曲在 wmbr.txt 播放过，并且进入 2018 年 Spotify 排行榜前 100 的艺术家（top2018.csv），谁具有danceability评分最高的歌曲，以及那首歌是什么？
注意：在这里我们考虑合作歌曲，因此“Calvin Harris, Dua Lipa”意味着你在查找前 100 名艺术家时，也需要包括“Dua Lipa”。
将你的答案输出到 `q8.csv` 文件中，每行行数据应包含三列：artist_name、song_title和danceability。如果你使用了命令行工具，请将代码提交为 `q8.sh`；如果你使用了 Pandas，请将代码提交为 `q8.py`。