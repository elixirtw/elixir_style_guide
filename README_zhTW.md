# [The Elixir Style Guide][Elixir Style Guide]

## Table of Contents

* __[前言](#前言)__
* __[關於此指南](#關於此指南)__
  * [原始碼排板](#原始碼排版)
  * [語法](#語法)
  * [命名](#命名)
  * [註解](#註解)
    * [程式註釋](#程式註釋)
  * [模組](#模組)
  * [文件](#文件)
  * [型別](#型別)
  * [結構](#結構)
  * [例外](#例外)
  * _集合_
  * [字串](#字串)
  * _正規表示法_
  * [Metaprogramming](#metaprogramming)
  * [測試](#測試)
  * [Suggested Alternatives](#suggested-alternatives)
  * [條件式](#條件式)
  * [Style Guides](#style-guides)
  * [Tools](#tools)
* __[Getting Involved](#getting-involved)__
  * [Contributing](#contributing)
  * [Spread the Word](#spread-the-word)
* __[Copying](#copying)__
  * [License](#license)
  * [Attribution](#attribution)

## 前言

> Liquid architecture. It's like jazz — you improvise, you work together, you
> play off each other, you make something, they make something.
>
> —Frank Gehry

程式的語法風格很重要！
[Elixir] has plenty of style but like all languages it can be stifled.
Don't stifle the style.

## 關於此指南

這個社群的風格指南嘗試提供一個社群維護的
[Elixir 程式語言][Elixir] 的語法風格，
歡迎提出 Pull Request 來協助完善這份指南。

我們希望 Elixir 這個語言能夠像那些在它之前的語言一樣有個活躍的社群！

如果你想要找其他的 Project 來提出貢獻，請上
[Hex package manager site][Hex]。

### 原始碼編

<!-- TODO: Add crafty quote here -->

* <a name="spaces-indentation"></a>
  請用兩個**空格**來縮排，
  不要用 Hard Tab。
  <sup>[[link](#spaces-indentation)]</sup>

  ```elixir
  # 不好 - 四個空格
  def some_function do
      do_something
  end

  # 好
  def some_function do
    do_something
  end
  ```

* <a name="line-endings"></a>
  使用 Unix 風格的行編碼結尾 (預設包含 BSD/Solaris/Linux/OSX 的使用者，
  Windows 使用者要特別小心。)
  <sup>[[link](#line-endings)]</sup>

* <a name="autocrlf"></a>
  如果你使用 Git ，你也許會想加入下面這個配置設定，
  來保護你的專案被 Windows 的行編碼侵入:
  <sup>[[link](#autocrlf)]</sup>

  ```sh
  git config --global core.autocrlf true
  ```

* <a name="spaces"></a>
  使用空格來圍繞運算子，在逗點 `,` 、冒號 `:` 及分號 `;` 之後，圍繞 `{` ，
  和 `}` 之前。空格可能對（大部分）Elixir 直譯器來說是無關緊要的，
  但正確的使用是寫出可讀性高的程式碼的關鍵。
  <sup>[[link](#spaces)]</sup>

  ```elixir
  sum = 1 + 2
  {a, b} = {2, 3}
  [first | rest] = [1, 2, 3]
  Enum.map(["one", <<"two">>, "three"], fn num -> IO.puts num end)
  ```

* <a name="no-spaces"></a>
  在單元運算子之後，或是範圍運算子的前後不要加空白。
  <sup>[[link](#no-spaces)]</sup>

  ```elixir
  0 - 1 == -1
  ^pinned = some_func()
  5 in 1..10
  ```

* <a name="def-spacing"></a>
  在 `def` 之間使用空行，並且把方法分成合乎邏輯的段落。
  <sup>[[link](#def-spacing)]</sup>

  ```elixir
  def some_function(some_data) do
    altered_data = Module.function(data)
  end

  def some_function do
    result
  end

  def some_other_function do
    another_result
  end

  def a_longer_function do
    one
    two

    three
    four
  end
  ```

* <a name="single-line-defs"></a>
  ...但在單行 `def` 時不要空行，並把相同的函示集中在一起。
  <sup>[[link](#single-line-defs)]</sup>

  ```elixir
  def some_function(nil), do: {:err, "No Value"}
  def some_function([]), do: :ok
  def some_function([first | rest]) do
    some_function(rest)
  end
  ```

* <a name="long-dos"></a>
  當你使用 `do:` 的語法宣告函式時，如果函式主體太長，請考慮把 `do:` 放在新的一
  行，並縮排。
  <sup>[[link](#long-dos)]</sup>

  ```elixir
  def some_function(args),
    do: Enum.map(args, fn(arg) -> arg <> " is on a very long line!" end)
  ```

  如果你使用了以上的風格，並且你同時用多個 `do:` 函式，請把所有的 `do:` 函式
  主體都放在新的一行：

  ```elixir
  # 不好
  def some_function([]), do: :empty
  def some_function(_),
    do: :very_long_line_here

  # 好
  def some_function([]),
    do: :empty
  def some_function(_),
    do: :very_long_line_here
  ```

* <a name="multiple-function-defs"></a>
  如果你用了多行的 `def`，請不要再使用單行的 `def`。
  <sup>[[link](#multiple-function-defs)]</sup>

  ```elixir
  def some_function(nil) do
    {:err, "No Value"}
  end

  def some_function([]) do
    :ok
  end

  def some_function([first | rest]) do
    some_function(rest)
  end

  def some_function([first | rest], opts) do
    some_function(rest, opts)
  end
  ```

* <a name="pipe-operator"></a>
  請使用管線運算子(`|>`; pipe operator)鏈接多個函式。
  <sup>[[link](#pipe-operator)]</sup>

  ```elixir
  # 不好
  String.strip(String.downcase(some_string))

  # 好
  some_string |> String.downcase |> String.strip

  # 多行管線不用縮排
  some_string
  |> String.downcase
  |> String.strip

  # 如果多行管線用在模式比對(pattern match)的右側，請換行並縮排
  sanitized_string =
    some_string
    |> String.downcase
    |> String.strip
  ```

  雖然這是建議的寫法，務必記得在剪貼多行管線進 IEx 時，有可能會出錯。這是因為
  IEx 不知道會有之後的管線，並在會在接到第一行管線時馬上評估。

* <a name="avoid-single-pipelines"></a>
  減少只使用一次的管線運算子。
  <sup>[[link](#avoid-single-pipelines)]</sup>

  ```elixir
  # 不好
  some_string |> String.downcase

  # 好
  String.downcase(some_string)
  ```

* <a name="bare-variables"></a>
  使用_單純_的變數，在最起始的函式鍊。
  <sup>[[link](#bare-variables)]</sup>

  ```elixir
  # 非常不好！
  # 這會被編譯為 String.strip("nope" |> String.downcase)。
  String.strip "nope" |> String.downcase

  # 不好
  String.strip(some_string) |> String.downcase |> String.codepoints

  # 好
  some_string |> String.strip |> String.downcase |> String.codepoints
  ```

* <a name="trailing-whitespace"></a>
  避免尾隨的空白(trailing whitespace)。
  <sup>[[link](#trailing-whitespace)]</sup>

* <a name="newline-eof"></a>
  用新的一行來結尾每一個檔案。
  <sup>[[link](#newline-eof)]</sup>

### 語法

* <a name="parentheses"></a>
  Use parentheses when a `def` has arguments, and omit them when it doesn't.
  如果 `def` 有參數請用括號括起，如果沒有參數請不要使用括號。
  <sup>[[link](#parentheses)]</sup>

  ```elixir
  # 不好
  def some_function arg1, arg2 do
    # 省略
  end

  def some_function() do
    # 省略
  end

  # 好
  def some_function(arg1, arg2) do
    # 省略
  end

  def some_function do
    # 省略
  end
  ```

* <a name="do-with-multi-line-if-unless"></a>
  Never use `do:` for multi-line `if/unless`.
  多行 `if/unless` 時，不要使用 `do:`
  <sup>[[link](#do-with-multi-line-if-unless)]</sup>

  ```elixir
  # 不好
  if some_condition, do:
    # 一行程式碼
    # 又一行程式碼
    # note 這個程式主體沒有結束

  # 好
  if some_condition do
    # 幾
    # 行
    # 程式碼
  end
  ```

* <a name="do-with-single-line-if-unless"></a>
  Use `do:` for single line `if/unless` statements.
  單行 `if/unless` 請用 `do:`。
  <sup>[[link](#do-with-single-line-if-unless)]</sup>

  ```elixir
  # 好
  if some_condition, do: # some_stuff
  ```

* <a name="unless-with-else"></a>
  如果用 `unless` **絕對** 不要用 `else`，
  請將它們改寫成肯定條件。
  <sup>[[link](#unless-with-else)]</sup>

  ```elixir
  # 不好
  unless success? do
    IO.puts 'failure'
  else
    IO.puts 'success'
  end

  # 好
  if success? do
    IO.puts 'success'
  else
    IO.puts 'failure'
  end
  ```

* <a name="true-as-last-condition"></a>
  `cond` 的最後一個條件一定是 `true`。
  <sup>[[link](#true-as-last-condition)]</sup>

  ```elixir
  cond do
    1 + 2 == 5 ->
      "Nope"
    1 + 3 == 5 ->
      "Uh, uh"
    true ->
      "OK"
  end
  ```

* <a name="function-names-with-parentheses"></a>
  不要在函式名與左括號後之間使用空白。
  <sup>[[link](#function-names-with-parentheses)]</sup>

  ```elixir
  # 不好
  f (3 + 2) + 1

  # 好
  f(3 + 2) + 1
  ```

* <a name="function-calls-and-parentheses"></a>
  在使用函式時使用括號，特別是用在管線鍊時。
  <sup>[[link](#function-calls-and-parentheses)]</sup>

  ```elixir
  # 不好
  f 3

  # 好
  f(3)

  # 不好，此方法解讀為 rem(2, (3 |> g))，這應該不是你想要的。
  2 |> rem 3 |> g

  # 好
  2 |> rem(3) |> g
  ```

* <a name="macro-calls-and-parentheses"></a>
  在使用 `quote` 編輯巨集時，不要使用括號在 `do` 區塊之外。
  <sup>[[link](#macro-calls-and-parentheses)]</sup>

  ```elixir
  # 不好
  quote(do
    foo
  end)

  # 好
  quote do
    foo
  end
  ```

* <a name="parentheses-and-function-expressions"></a>
  如果函式在管線之外並且最後一個參數為函式時，可以選擇性的省略括號。
  <sup>[[link](#parentheses-and-function-expressions)]</sup>

  ```elixir
  # 好
  Enum.reduce(1..10, 0, fn x, acc ->
    x + acc
  end)

  # 也好
  Enum.reduce 1..10, 0, fn x, acc ->
    x + acc
  end
  ```

* <a name="parentheses-and-functions-with-zero-arity"></a>
  當呼叫無參數的函式時，加上括號以便與變數區分。
  <sup>[[link](#parentheses-and-functions-with-zero-arity)]</sup>

  ```elixir
  defp do_stuff, do: ...

  # 不好
  def my_func do
    do_stuff # 這是變數還是函式呼叫？
  end

  # 好
  def my_func do
    do_stuff() # 這很明確是一個函式
  end
  ```

* <a name="with-clauses"></a>
  利用縮排來排列每個 `with` 條件。
  把 `do:` 的參數放在新的一行，正常的縮排。
  <sup>[[link](#with-clauses)]</sup>

  ```elixir
  with {:ok, foo} <- fetch(opts, :foo),
       {:ok, bar} <- fetch(opts, :bar),
    do: {:ok, foo, bar}
  ```

* <a name="with-else"></a>
  如果 `with` 表達式使用了多行的 `do` 主體或是使用了 `else`，請使用多行語法。
  <sup>[[link](#with-else)]</sup>

  ```elixir
  with {:ok, foo} <- fetch(opts, :foo),
       {:ok, bar} <- fetch(opts, :bar) do
    {:ok, foo, bar}
  else
    :error ->
      {:error, :bad_arg}
  end
  ```

### 命名

* <a name="snake-case"></a>
  符號、方法與變數使用蛇底式小寫（snake_case）。
  <sup>[[link](#snake-case)]</sup>

  ```elixir
  # 不好
  :"some atom"
  :SomeAtom
  :someAtom

  someVar = 5

  def someFunction do
    ...
  end

  def SomeFunction do
    ...
  end

  # 好
  :some_atom

  some_var = 5

  def some_function do
    ...
  end
  ```

* <a name="camel-case"></a>
  模組使用駝峰式大小寫（CamelCase）。（保留像是 HTTP、RFC、XML 這種縮寫為大寫）
  <sup>[[link](#camel-case)]</sup>

  ```elixir
  # not preferred
  defmodule Somemodule do
    ...
  end

  defmodule Some_Module do
    ...
  end

  defmodule SomeXml do
    ...
  end

  # 好
  defmodule SomeModule do
    ...
  end

  defmodule SomeXML do
    ...
  end
  ```

* <a name="predicate-macro-names-with-guards"></a>
  _可以在 guard clause 使用的述語型巨集_（編譯產生的函示，回傳布林），請用 `is_`
  為開頭。
  允許的語法列表，請參考 [Guard][Guard Expressions] 文件。
  <sup>[[link](#predicate-macro-names-with-guards)]</sup>

  ```elixir
  defmacro is_cool(var) do
    quote do: unquote(var) == "cool"
  end
  ```

* <a name="predicate-macro-names-no-guards"></a>
  _無法在 guard clause 使用的巨集_請用問號(`?`)，不要使用 `is_` 開頭。
  <sup>[[link](#predicate-macro-names-no-guards)]</sup>

  ```elixir
  def cool?(var) do
    # Complex check if var is cool not possible in a pure function.
  end
  ```

* <a name="private-functions-with-same-name-as-public"></a>
  與公開函數同名的私有函數請使用 `do_` 開頭。
  <sup>[[link](#private-functions-with-same-name-as-public)]</sup>

  ```elixir
  def sum(list), do: do_sum(list, 0)

  # 私有函數
  defp do_sum([], total), do: total
  defp do_sum([head | tail], total), do: do_sum(tail, head + total)
  ```

### 註解

* <a name="expressive-code"></a>
  盡可能利用控制流、結構、和命名來表達你的程式的意圖。
  <sup>[[link](#expressive-code)]</sup>

* <a name="comment-leading-spaces"></a>
  在 `#` 後與註解間保留一空格。
  <sup>[[link](#comment-leading-spaces)]</sup>

* <a name="comment-spacing"></a>
  Comments longer than a word are capitalized and use punctuation.
  Use [one space][Sentence Spacing] after periods.
  一個字以上的註釋需要使用正確的英文大寫與標點符號規則，並在句點後
  加上一[空格][Sentence Spacing]。
  <sup>[[link](#comment-spacing)]</sup>

  ```elixir
  # 不好
  String.upcase(some_string) # Capitalize string.
  ```

#### 程式註釋

* <a name="annotations"></a>
  註釋請寫在對應程式碼的上一行。
  <sup>[[link](#annotations)]</sup>

* <a name="annotation-keyword"></a>
  註釋關鍵字後方伴隨著一個冒號及空白，接著一個描述問題的記錄。
  <sup>[[link](#annotation-keyword)]</sup>

* <a name="multiple-line-annotations"></a>
  如果需要用多行來描述問題，之後的行要放在 `#` 號後面並縮排兩個空白。
  <sup>[[link](#multiple-line-annotations)]</sup>

* <a name="exceptions-to-annotations"></a>
  在問題是顯而易見的情況下，任何的文件會是多餘的，註釋應該要留在可能有問題的那行。
  這個用法是例外而不是規則。
  <sup>[[link](#exceptions-to-annotations)]</sup>

* <a name="todo-notes"></a>
  使用 `TODO` 來標記之後應被加入的未實現功能或特色。
  <sup>[[link](#todo-notes)]</sup>

* <a name="fixme-notes"></a>
  使用 `FIXME` 來標記一個需要修復的程式碼。
  <sup>[[link](#fixme-notes)]</sup>

* <a name="optimize-notes"></a>
  使用 `OPTIMIZE` 來標記可能影響效能的緩慢或效率低落的程式碼。
  <sup>[[link](#optimize-notes)]</sup>

* <a name="hack-notes"></a>
  使用 `HACK` 來標記代碼異味，其中包含了可疑的編碼實踐以及應該需要重構。
  <sup>[[link](#hack-notes)]</sup>

* <a name="review-notes"></a>
  使用 `REVIEW` 來標記任何需要審視及確認正常動作的地方。
  舉例來說：`REVIEW: 我們確定用戶現在是這麼做的嗎？`
  <sup>[[link](#review-notes)]</sup>

* <a name="custom-keywords"></a>
  如果你覺得適當的話，使用其他你習慣的註釋關鍵字，但記得把它們記錄在專案的 `README` 或類似的地方。
  <sup>[[link](#custom-keywords)]</sup>

### 模組

* <a name="one-module-per-file"></a>
  Use one module per file unless the module is only used internally by another
  module (such as a test).
  每一個檔案內只有一個模組，除非另一個模組只有被並存的模組使用（如測試）。
  <sup>[[link](#one-module-per-file)]</sup>

* <a name="underscored-filenames"></a>
  使用小寫底線檔名（snake_case）配合駝峰式（CamelCase）模組名。
  <sup>[[link](#underscored-filenames)]</sup>

  ```elixir
  # 檔名： some_module.ex

  defmodule SomeModule do
  end
  ```

* <a name="module-name-nesting"></a>
  Represent each level of nesting within a module name as a directory.
  用模組名中的階層來表示檔案位置。
  <sup>[[link](#module-name-nesting)]</sup>

  ```elixir
  # 檔案名為 parser/core/xml_parser.ex

  defmodule Parser.Core.XMLParser do
  end
  ```

* <a name="defmodule-spacing"></a>
  不要在 `defmodule` 後空行。
  <sup>[[link](#defmodule-spacing)]</sup>

* <a name="module-block-spacing"></a>
  在模組區塊後空行。
  <sup>[[link](#module-block-spacing)]</sup>

* <a name="module-attribute-ordering"></a>
  用下列順序來整理模組屬性：
  <sup>[[link](#module-attribute-ordering)]</sup>

  1. `@moduledoc`
  1. `@behaviour`
  1. `use`
  1. `import`
  1. `alias`
  1. `require`
  1. `defstruct`
  1. `@type`
  1. `@module_attribute`

  在每個屬性後加入空行，並依照字母順序整理。
  以下為完整範例：

  ```elixir
  defmodule MyModule do
    @moduledoc """
    An example module
    """

    @behaviour MyBehaviour

    use GenServer

    import Something
    import SomethingElse

    alias My.Long.Module.Name
    alias My.Other.Module.Name

    require Integer

    defstruct name: nil, params: []

    @type params :: [{binary, binary}]

    @module_attribute :foo
    @other_attribute 100

    ...
  end
  ```

* <a name="module-pseudo-variable"></a>
  當在模組內參考自己，請使用 `__MODULE__` 虛擬變數。如模組名修改，將不用另外
  更新這些自參考。
  <sup>[[link](#module-pseudo-variable)]</sup>

  ```elixir
  defmodule SomeProject.SomeModule do
    defstruct [:name]

    def name(%__MODULE__{name: name}), do: name
  end
  ```

* <a name="alias-self-referencing-modules"></a>
  如果你想要一個比較好看的自參考，請使用 `alias`。
  <sup>[[link](#alias-self-referencing-modules)]</sup>

  ```elixir
  defmodule SomeProject.SomeModule do
    alias __MODULE__, as: SomeModule

    defstruct [:name]

    def name(%SomeModule{name: name}), do: name
  end
  ```

### 文件

Elixir 的文件（當在 `iex` 的 `h` 指令或是用 [ExDoc] 產生）是指 `@moduledoc`
和 `@doc` 的[模組變數](Module Attributes)。

* <a name="moduledocs"></a>
  務必包含一 `@moduledoc` 模組變數在 `defmodule` 模組內的下一行。
  <sup>[[link](#moduledocs)]</sup>

  ```elixir
  # 不好

  defmodule SomeModule do

    @moduledoc """
    關於模組
    """
    ...
  end

  defmodule AnotherModule do
    use SomeModule
    @moduledoc """
    關於模組
    """
    ...
  end

  # 好

  defmodule SomeModule do
    @moduledoc """
    關於模組
    """
    ...
  end
  ```

* <a name="moduledoc-false"></a>
  使用 `@moduledoc false` 如果你不想為這個模組增加文件。
  <sup>[[link](#moduledoc-false)]</sup>

  ```elixir
  defmodule SomeModule do
    @moduledoc false
    ...
  end
  ```

* <a name="moduledoc-spacing"></a>
  在 `@moduledoc` 後使用空行與程式碼分開。
  <sup>[[link](#moduledoc-spacing)]</sup>

  ```elixir
  # 不好

  defmodule SomeModule do
    @moduledoc """
    關於模組
    """
    use AnotherModule
  end

  # 好
  defmodule SomeModule do
    @moduledoc """
    關於模組
    """

    use AnotherModule
  end
  ```

* <a name="heredocs"></a>
  在文件內使用 heredocs 和 markdown。
  <sup>[[link](#heredocs)]</sup>

  ```elixir
  # 不好

  defmodule SomeModule do
    @moduledoc "About the module"
  end

  defmodule SomeModule do
    @moduledoc """
    關於模組

    Examples:
    iex> SomeModule.some_function
    :result
    """
  end

  # 好
  defmodule SomeModule do
    @moduledoc """
    關於模組

    ## Examples

        iex> SomeModule.some_function
        :result
    """
  end
  ```

### 型別

Typespecs 為宣告型別與規格，主要為文件或是靜態分析工具如 Dialyzer。

自定義型別應該與其他指令放在模組上方（請見 [模組](#模組)）。

* <a name="typedocs"></a>
  將 `@typedoc` 與 `@type` 宣告在一起，並每對之間用空行隔開。
  <sup>[[link](#typedocs)]</sup>

  ```elixir
  defmodule SomeModule do
    @moduledoc false

    @typedoc "The name"
    @type name :: atom

    @typedoc "The result"
    @type result :: {:ok, term} | {:error, term}

    ...
  end
  ```

* <a name="union-types"></a>
  如果一個聯合型別無法在單行內容那，請在新的一行繼續並且用空格平行縮排。
  <sup>[[link](#union-types)]</sup>

  ```elixir
  # 不好 - 沒有縮排
  @type long_union_type :: some_type | another_type | some_other_type
  | a_final_type

  # 好
  @type long_union_type :: some_type | another_type | some_other_type
                         | a_final_type

  # 也好 - 每個型別獨立一行
  @type long_union_type :: some_type
                         | another_type
                         | some_other_type
                         | a_final_type
  ```

* <a name="naming-main-types"></a>
  模組的主要型別命名為 `t`，範例：一個結構的型別。
  <sup>[[link](#naming-main-types)]</sup>

  ```elixir
  defstruct name: nil, params: []

  @type t :: %__MODULE__{
    name: String.t,
    params: Keyword.t
  }
  ```

* <a name="spec-spacing"></a>
  把函式型別宣告放在 `def` 的上一行，不用空行。
  <sup>[[link](#spec-spacing)]</sup>

  ```elixir
  @spec some_function(term) :: result
  def some_function(some_data) do
    {:ok, some_data}
  end
  ```

### 結構

* <a name="nil-struct-field-defaults"></a>
  如果結構的所有欄位皆為空，用一 atoms 的串列表示。
  <sup>[[link](#nil-struct-field-defaults)]</sup>

  ```elixir
  # 不好
  defstruct name: nil, params: nil

  # 好
  defstruct [:name, :params]
  ```

* <a name="additional-struct-def-lines"></a>
  如兩行以上的結構宣告，用空格縮排並對齊第一個 key。
  <sup>[[link](#additional-struct-def-lines)]</sup>

  ```elixir
  defstruct foo: "test", bar: true, baz: false,
            qux: false, quux: nil
  ```

### 例外

* <a name="exception-names"></a>
  Make exception names end with a trailing `Error`.
  自訂例外的結尾為 `Error`。
  <sup>[[link](#exception-names)]</sup>

  ```elixir
  # 不好
  defmodule BadHTTPCode do
    defexception [:message]
  end

  defmodule BadHTTPCodeException do
    defexception [:message]
  end

  # 好
  defmodule BadHTTPCodeError do
    defexception [:message]
  end
  ```

* <a name="lowercase-error-messages"></a>
  錯誤訊息使用小寫，並不要在最後加上標點符號。
  <sup>[[link](#lowercase-error-messages)]</sup>

  ```elixir
  # 不好
  raise ArgumentError, "This is not valid."

  # 好
  raise ArgumentError, "this is not valid"
  ```

### 集合

_No guidelines for collections have been added yet._

### 字串

* <a name="strings-matching-with-concatenator"></a>
  使用字串連接（string concatenatora）做模式比對，不要用二進制的模式。
  <sup>[[link](#strings-matching-with-concatenator)]</sup>

  ```elixir
  # 不好
  <<"my"::utf8, _rest>> = "my string"

  # 好
  "my" <> _rest = "my string"
  ```

### 正規表示法

_No guidelines for regular expressions have been added yet._

### Metaprogramming

* <a name="avoid-metaprogramming"></a>
  並免不必要的模板超程式（metaprogramming）。
  <sup>[[link](#avoid-metaprogramming)]</sup>

### 測試

* <a name="testing-assert-order"></a>
  當寫 [ExUnit] 斷言（assertions）時，保持期待值與實際值得一制性。
  盡量把期待值放在右邊，除非斷言為模式比對。
  <sup>[[link](#testing-assert-order)]</sup>

  ```elixir
  # 好 - 期待值在右邊
  assert actual_function(1) == true
  assert actual_function(2) == false

  # 不好 - 順序交叉
  assert actual_function(1) == true
  assert false == actual_function(2)

  # 必須 - 斷言為模式比對
  assert {:ok, expected} = actual_function(3)
  ```

### Suggested Alternatives

Suggested alternatives are styles that haven't been seen much in the community
yet but might provide some value.

#### 條件式

* <a name="atom-conditions"></a>
  Atom 為類真（truthy），可作為 `cond` 條件式內全捕捉（catch-all）條件。
  建議使用 `:else` 或是 `:otherwise`。
  <sup>[[link](#atom-conditions)]</sup>

  ```elixir
  cond do
    1 + 2 == 5 ->
      "Nope"
    1 + 3 == 5 ->
      "Uh, uh"
    :else ->
      "OK"
  end

  # 跟以下相同：

  cond do
    1 + 2 == 5 ->
      "Nope"
    1 + 3 == 5 ->
      "Uh, uh"
    true ->
      "OK"
  end
  ```

### Style Guides

Check [Awesome Elixir][Style Guides] for a list of alternative style guides.

### Tools

Refer to [Awesome Elixir][Code Analysis] for libraries and tools that can help
with code analysis and style linting.

## Getting Involved

### Contributing

It's our hope that this will become a central hub for community discussion on
best practices in Elixir.
Feel free to open tickets or send pull requests with improvements.
Thanks in advance for your help!

Check the [contributing guidelines](CONTRIBUTING.md)
and [code of conduct](CODE_OF_CONDUCT.md) for more information.

### Spread the Word

A community style guide is meaningless without the community's support. Please
tweet, [star][Stargazers], and let any Elixir programmer know
about [this guide][Elixir Style Guide] so they can contribute.

## Copying

### License

![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png)
This work is licensed under a
[Creative Commons Attribution 3.0 Unported License][License]

### Attribution

The structure of this guide, bits of example code, and many of the initial
points made in this document were borrowed from the [Ruby community style guide].
A lot of things were applicable to Elixir and allowed us to get _some_ document
out quicker to start the conversation.

Here's the [list of people who has kindly contributed][Contributors] to this
project.

<!-- Links -->
[Code Analysis]: https://github.com/h4cc/awesome-elixir#code-analysis
[Contributors]: https://github.com/christopheradams/elixir_style_guide/graphs/contributors
[Elixir Style Guide]: https://github.com/christopheradams/elixir_style_guide
[Elixir]: http://elixir-lang.org
[ExDoc]: https://github.com/elixir-lang/ex_doc
[ExUnit]: https://hexdocs.pm/ex_unit/ExUnit.html
[Guard Expressions]: http://elixir-lang.org/getting-started/case-cond-and-if.html#expressions-in-guard-clauses
[Hex]: https://hex.pm/packages
[License]: http://creativecommons.org/licenses/by/3.0/deed.en_US
[Module Attributes]: http://elixir-lang.org/getting-started/module-attributes.html#as-annotations
[Ruby community style guide]: https://github.com/bbatsov/ruby-style-guide
[Sentence Spacing]: http://en.wikipedia.org/wiki/Sentence_spacing
[Stargazers]: https://github.com/christopheradams/elixir_style_guide/stargazers
[Style Guides]: https://github.com/h4cc/awesome-elixir#styleguides
