# [The Elixir Style Guide][Elixir Style Guide]

## Table of Contents

* __[前言](#前言)__
* __[關於此指南](#關於此指南)__
  * [原始碼排板](#原始碼排版)
  * [語法](#語法)
  * [Naming](#naming)
  * [Comments](#comments)
    * [Comment Annotations](#comment-annotations)
  * [Modules](#modules)
  * [Documentation](#documentation)
  * [Typespecs](#typespecs)
  * [Structs](#structs)
  * [Exceptions](#exceptions)
  * _Collections_
  * [Strings](#strings)
  * _Regular Expressions_
  * [Metaprogramming](#metaprogramming)
  * [Testing](#testing)
  * [Suggested Alternatives](#suggested-alternatives)
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

### 原始碼排版

<!-- TODO: Add crafty quote here -->

* <a name="spaces-indentation"></a>
  請用兩個**空格**來縮排，
  不要用 Hard Tab。
  <sup>[[link](#spaces-indentation)]</sup>

  ```elixir
  # 不建議 - 四個空格
  def some_function do
      do_something
  end

  # 建議
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
  使用空格來圍繞運算元，在逗點 `,` 、冒號 `:` 及分號 `;` 之後，圍繞 `{` ，
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
  不要加空白在單一參數的運算元之後，或是範圍運算元的前後。
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
  # 不建議
  def some_function([]), do: :empty
  def some_function(_),
    do: :very_long_line_here

  # 建議
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
  請使用管線運算元(`|>`; pipe operator)鏈接多個函式。
  <sup>[[link](#pipe-operator)]</sup>

  ```elixir
  # 不建議
  String.strip(String.downcase(some_string))

  # 建議
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
  減少只使用一次的管線運算元。
  <sup>[[link](#avoid-single-pipelines)]</sup>

  ```elixir
  # 不建議
  some_string |> String.downcase

  # 建議
  String.downcase(some_string)
  ```

* <a name="bare-variables"></a>
  使用_單純_的變數，在最起始的函式鍊。
  <sup>[[link](#bare-variables)]</sup>

  ```elixir
  # 非常不好！
  # 這會被編譯為 String.strip("nope" |> String.downcase)。
  String.strip "nope" |> String.downcase

  # 不建議
  String.strip(some_string) |> String.downcase |> String.codepoints

  # 建議
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
  使用 `def` 時，當有參數時使用括號，沒有參數時省略括號。
  <sup>[[link](#parentheses)]</sup>

  ```elixir
  # 不建議
  def some_function arg1, arg2 do
    # 省略主體
  end

  def some_function() do
    # 省略主體
  end

  # 建議
  def some_function(arg1, arg2) do
    # 省略主體
  end

  def some_function do
    # 省略主體
  end
  ```

* <a name="do-with-multi-line-if-unless"></a>
  永遠不要在多行的 `if/unless` 使用 `do:`
  <sup>[[link](#do-with-multi-line-if-unless)]</sup>

  ```elixir
  # 不建議
  if some_condition, do:
    # 一行程式碼
    # 再一行程式碼
    # 注意這個區塊沒有結束

  # 建議
  if some_condition do
    # 這是
    # 一些
    # 程式碼
  end
  ```

* <a name="do-with-single-line-if-unless"></a>
  在單行的 `if/unless` 敘述式使用 `do:`
  <sup>[[link](#do-with-single-line-if-unless)]</sup>

  ```elixir
  # 建議
  if some_condition, do: # some_stuff
  ```

* <a name="unless-with-else"></a>
  永遠不要使用 `unless` 搭配 `else`。
  將它們改寫成肯定條件。
  <sup>[[link](#unless-with-else)]</sup>

  ```elixir
  # 不建議
  unless success? do
    IO.puts 'failure'
  else
    IO.puts 'success'
  end

  # 建議
  if success? do
    IO.puts 'success'
  else
    IO.puts 'failure'
  end
  ```

* <a name="true-as-last-condition"></a>
  永遠使用 `true` 作為 `cond` 敘述式中最後的判斷條件。
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

* <a name="function-nameswith-parentheses"></a>
  永遠不要在函式名與左括號之間放一個空格。
  <sup>[[link](#function-names-with-parentheses)]</sup>

  ```elixir
  # 不建議
  f (3 + 2) + 1

  # 建議
  f(3 + 2) + 1
  ```

* <a name="function-calls-and-parentheses"></a>
  在呼叫函式時使用括號，特別是在管線中呼叫。
  <sup>[[link](#function-calls-and-parentheses)]</sup>

  ```elixir
  # 不建議
  f 3

  # 建議
  f(3)

  # 不建議。這會被解析成 rem(2, (3 |> g))，並不是你所預期的。
  2 |> rem 3 |> g

  # 建議
  2 |> rem(3) |> g
  ```

* <a name="macro-calls-and-parentheses"></a>
  呼叫巨集並傳入一個 do 區塊時，省略括號。
  <sup>[[link](#macro-calls-and-parentheses)]</sup>

  ```elixir
  # 不建議
  quote(do
    foo
  end)

  # 建議
  quote do
    foo
  end
  ```

* <a name="parentheses-and-function-expressions"></a>
  當呼叫函式(在管線外)，最後一個參數是函式表達式時，可以省略括號。
  <sup>[[link](#parentheses-and-function-expressions)]</sup>

  ```elixir
  # 建議
  Enum.reduce(1..10, 0, fn x, acc ->
    x + acc
  end)

  # 同樣建議
  Enum.reduce 1..10, 0, fn x, acc ->
    x + acc
  end
  ```

* <a name="parentheses-and-functions-with-zero-arity"></a>
  呼叫沒有參數的函式時使用括號，在閱讀時就不容易與變數混淆。
  <sup>[[link](#parentheses-and-functions-with-zero-arity)]</sup>

  ```elixir
  defp do_stuff, do: ...

  # 不建議
  def my_func do
    do_stuff # 這是變數還是在呼叫函式？
  end

  # 建議
  def my_func do
    do_stuff() # 這很清楚是在呼叫函式
  end
  ```

* <a name="with-clauses"></a>
  縮排對齊連續的 `with` 子句。
  將 `do:` 參數放在新的一行，並正常縮排。
  <sup>[[link](#with-clauses)]</sup>

  ```elixir
  with {:ok, foo} <- fetch(opts, :foo),
       {:ok, bar} <- fetch(opts, :bar),
    do: {:ok, foo, bar}
  ```

* <a name="with-else"></a>
  如果 `with` 表達式多行敘述中擁有一個 `do` 區塊，或是擁有 `else` 選項時，使用多行語法。
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

### Naming

* <a name="snake-case"></a>
  Use `snake_case` for atoms, functions and variables.
  <sup>[[link](#snake-case)]</sup>

  ```elixir
  # not preferred
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

  # preferred
  :some_atom

  some_var = 5

  def some_function do
    ...
  end
  ```

* <a name="camel-case"></a>
  Use `CamelCase` for modules (keep acronyms like HTTP, RFC, XML uppercase).
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

  # preferred
  defmodule SomeModule do
    ...
  end

  defmodule SomeXML do
    ...
  end
  ```

* <a name="predicate-macro-names-with-guards"></a>
  The names of predicate macros (compile-time generated functions that return a
  boolean value) _that can be used within guards_ should be prefixed with `is_`.
  For a list of allowed expressions, see the [Guard][Guard Expressions] docs.
  <sup>[[link](#predicate-macro-names-with-guards)]</sup>

  ```elixir
  defmacro is_cool(var) do
    quote do: unquote(var) == "cool"
  end
  ```

* <a name="predicate-macro-names-no-guards"></a>
  The names of predicate functions _that cannot be used within guards_ should
  have a trailing question mark (`?`) rather than the `is_` (or similar) prefix.
  <sup>[[link](#predicate-macro-names-no-guards)]</sup>

  ```elixir
  def cool?(var) do
    # Complex check if var is cool not possible in a pure function.
  end
  ```

* <a name="private-functions-with-same-name-as-public"></a>
  Private functions with the same name as public functions should start with
  `do_`.
  <sup>[[link](#private-functions-with-same-name-as-public)]</sup>

  ```elixir
  def sum(list), do: do_sum(list, 0)

  # private functions
  defp do_sum([], total), do: total
  defp do_sum([head | tail], total), do: do_sum(tail, head + total)
  ```

### Comments

* <a name="expressive-code"></a>
  Write expressive code and try to convey your program's intention through
  control-flow, structure and naming.
  <sup>[[link](#expressive-code)]</sup>

* <a name="comment-leading-spaces"></a>
  Use one space between the leading `#` character of the comment and the text of
  the comment.
  <sup>[[link](#comment-leading-spaces)]</sup>

* <a name="comment-spacing"></a>
  Comments longer than a word are capitalized and use punctuation.
  Use [one space][Sentence Spacing] after periods.
  <sup>[[link](#comment-spacing)]</sup>

  ```elixir
  # not preferred
  String.upcase(some_string) # Capitalize string.
  ```

#### Comment Annotations

* <a name="annotations"></a>
  Annotations should usually be written on the line immediately above the
  relevant code.
  <sup>[[link](#annotations)]</sup>

* <a name="annotation-keyword"></a>
  The annotation keyword is followed by a colon and a space, then a note
  describing the problem.
  <sup>[[link](#annotation-keyword)]</sup>

* <a name="multiple-line-annotations"></a>
  If multiple lines are required to describe the problem, subsequent lines
  should be indented two spaces after the `#`.
  <sup>[[link](#multiple-line-annotations)]</sup>

* <a name="exceptions-to-annotations"></a>
  In cases where the problem is so obvious that any documentation would be
  redundant, annotations may be left at the end of the offending line with no
  note.
  This usage should be the exception and not the rule.
  <sup>[[link](#exceptions-to-annotations)]</sup>

* <a name="todo-notes"></a>
  Use `TODO` to note missing features or functionality that should be added at a
  later date.
  <sup>[[link](#todo-notes)]</sup>

* <a name="fixme-notes"></a>
  Use `FIXME` to note broken code that needs to be fixed.
  <sup>[[link](#fixme-notes)]</sup>

* <a name="optimize-notes"></a>
  Use `OPTIMIZE` to note slow or inefficient code that may cause performance
  problems.
  <sup>[[link](#optimize-notes)]</sup>

* <a name="hack-notes"></a>
  Use `HACK` to note code smells where questionable coding practices were used
  and should be refactored away.
  <sup>[[link](#hack-notes)]</sup>

* <a name="review-notes"></a>
  Use `REVIEW` to note anything that should be looked at to confirm it is
  working as intended.
  For example: `REVIEW: Are we sure this is how the client does X currently?`
  <sup>[[link](#review-notes)]</sup>

* <a name="custom-keywords"></a>
  Use other custom annotation keywords if it feels appropriate, but be sure to
  document them in your project's `README` or similar.
  <sup>[[link](#custom-keywords)]</sup>

### Modules

* <a name="one-module-per-file"></a>
  Use one module per file unless the module is only used internally by another
  module (such as a test).
  <sup>[[link](#one-module-per-file)]</sup>

* <a name="underscored-filenames"></a>
  Use underscored file names for `CamelCase` module names.
  <sup>[[link](#underscored-filenames)]</sup>

  ```elixir
  # file is called some_module.ex

  defmodule SomeModule do
  end
  ```

* <a name="module-name-nesting"></a>
  Represent each level of nesting within a module name as a directory.
  <sup>[[link](#module-name-nesting)]</sup>

  ```elixir
  # file is called parser/core/xml_parser.ex

  defmodule Parser.Core.XMLParser do
  end
  ```

* <a name="defmodule-spacing"></a>
  Don't put a blank line after `defmodule`.
  <sup>[[link](#defmodule-spacing)]</sup>

* <a name="module-block-spacing"></a>
  Put a blank line after module-level code blocks.
  <sup>[[link](#module-block-spacing)]</sup>

* <a name="module-attribute-ordering"></a>
  List module attributes and directives in the following order:
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

  Add a blank line between each grouping, and sort the terms (like module names)
  alphabetically.
  Here's an overall example of how you should order things in your modules:

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
  Use the `__MODULE__` pseudo variable when a module refers to itself. This
  avoids having to update any self-references when the module name changes.
  <sup>[[link](#module-pseudo-variable)]</sup>

  ```elixir
  defmodule SomeProject.SomeModule do
    defstruct [:name]

    def name(%__MODULE__{name: name}), do: name
  end
  ```

* <a name="alias-self-referencing-modules"></a>
  If you want a prettier name for a module self-reference, set up an alias.
  <sup>[[link](#alias-self-referencing-modules)]</sup>

  ```elixir
  defmodule SomeProject.SomeModule do
    alias __MODULE__, as: SomeModule

    defstruct [:name]

    def name(%SomeModule{name: name}), do: name
  end
  ```

### Documentation

Documentation in Elixir (when read either in `iex` with `h` or generated with
[ExDoc]) uses the [Module Attributes] `@moduledoc` and `@doc`.

* <a name="moduledocs"></a>
  Always include a `@moduledoc` attribute in the line right after `defmodule` in
  your module.
  <sup>[[link](#moduledocs)]</sup>

  ```elixir
  # not preferred

  defmodule SomeModule do

    @moduledoc """
    About the module
    """
    ...
  end

  defmodule AnotherModule do
    use SomeModule
    @moduledoc """
    About the module
    """
    ...
  end

  # preferred

  defmodule SomeModule do
    @moduledoc """
    About the module
    """
    ...
  end
  ```

* <a name="moduledoc-false"></a>
  Use `@moduledoc false` if you do not intend on documenting the module.
  <sup>[[link](#moduledoc-false)]</sup>

  ```elixir
  defmodule SomeModule do
    @moduledoc false
    ...
  end
  ```

* <a name="moduledoc-spacing"></a>
  Separate code after the `@moduledoc` with a blank line.
  <sup>[[link](#moduledoc-spacing)]</sup>

  ```elixir
  # not preferred

  defmodule SomeModule do
    @moduledoc """
    About the module
    """
    use AnotherModule
  end

  # preferred
  defmodule SomeModule do
    @moduledoc """
    About the module
    """

    use AnotherModule
  end
  ```

* <a name="heredocs"></a>
  Use heredocs with markdown for documentation.
  <sup>[[link](#heredocs)]</sup>

  ```elixir
  # not preferred

  defmodule SomeModule do
    @moduledoc "About the module"
  end

  defmodule SomeModule do
    @moduledoc """
    About the module

    Examples:
    iex> SomeModule.some_function
    :result
    """
  end

  # preferred
  defmodule SomeModule do
    @moduledoc """
    About the module

    ## Examples

        iex> SomeModule.some_function
        :result
    """
  end
  ```

### Typespecs

Typespecs are notation for declaring types and specifications, for
documentation or for the static analysis tool Dialyzer.

Custom types should be defined at the top of the module with the other
directives (see [Modules](#modules)).

* <a name="typedocs"></a>
  Place `@typedoc` and `@type` definitions together, and separate each
  pair with a blank line.
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
  If a union type is too long to fit on a single line, add a newline
  and indent with spaces to keep the return types aligned.
  <sup>[[link](#union-types)]</sup>

  ```elixir
  # not preferred - no indentation
  @type long_union_type :: some_type | another_type | some_other_type
  | a_final_type

  # preferred
  @type long_union_type :: some_type | another_type | some_other_type
                         | a_final_type

  # also preferred - one return type per line
  @type long_union_type :: some_type
                         | another_type
                         | some_other_type
                         | a_final_type
  ```

* <a name="naming-main-types"></a>
  Name the main type for a module `t`, for example: the type specification for a
  struct.
  <sup>[[link](#naming-main-types)]</sup>

  ```elixir
  defstruct name: nil, params: []

  @type t :: %__MODULE__{
    name: String.t,
    params: Keyword.t
  }
  ```

* <a name="spec-spacing"></a>
  Place specifications right before the function definition,
  without separating them by a blank line.
  <sup>[[link](#spec-spacing)]</sup>

  ```elixir
  @spec some_function(term) :: result
  def some_function(some_data) do
    {:ok, some_data}
  end
  ```

### Structs

* <a name="nil-struct-field-defaults"></a>
  If all the struct's fields default to nil, supply them as a list of atoms.
  <sup>[[link](#nil-struct-field-defaults)]</sup>

  ```elixir
  # not preferred
  defstruct name: nil, params: nil

  # preferred
  defstruct [:name, :params]
  ```

* <a name="additional-struct-def-lines"></a>
  Indent additional lines of the struct definition, keeping the first keys
  aligned.
  <sup>[[link](#additional-struct-def-lines)]</sup>

  ```elixir
  defstruct foo: "test", bar: true, baz: false,
            qux: false, quux: nil
  ```

### Exceptions

* <a name="exception-names"></a>
  Make exception names end with a trailing `Error`.
  <sup>[[link](#exception-names)]</sup>

  ```elixir
  # not preferred
  defmodule BadHTTPCode do
    defexception [:message]
  end

  defmodule BadHTTPCodeException do
    defexception [:message]
  end

  # preferred
  defmodule BadHTTPCodeError do
    defexception [:message]
  end
  ```

* <a name="lowercase-error-messages"></a>
  Use lowercase error messages when raising exceptions, with no trailing
  punctuation.
  <sup>[[link](#lowercase-error-messages)]</sup>

  ```elixir
  # not preferred
  raise ArgumentError, "This is not valid."

  # preferred
  raise ArgumentError, "this is not valid"
  ```

### Collections

_No guidelines for collections have been added yet._

### Strings

* <a name="strings-matching-with-concatenator"></a>
  Match strings using the string concatenator rather than binary patterns:
  <sup>[[link](#strings-matching-with-concatenator)]</sup>

  ```elixir
  # not preferred
  <<"my"::utf8, _rest>> = "my string"

  # preferred
  "my" <> _rest = "my string"
  ```

### Regular Expressions

_No guidelines for regular expressions have been added yet._

### Metaprogramming

* <a name="avoid-metaprogramming"></a>
  Avoid needless metaprogramming.
  <sup>[[link](#avoid-metaprogramming)]</sup>

### Testing

* <a name="testing-assert-order"></a>
  When writing [ExUnit] assertions, be consistent with the order of the expected
  and actual values under testing.
  Prefer placing the expected result on the right, unless the assertion is a
  pattern match.
  <sup>[[link](#testing-assert-order)]</sup>

  ```elixir
  # preferred - expected result on the right
  assert actual_function(1) == true
  assert actual_function(2) == false

  # not preferred - inconsistent order
  assert actual_function(1) == true
  assert false == actual_function(2)

  # required - the assertion is a pattern match
  assert {:ok, expected} = actual_function(3)
  ```

### Suggested Alternatives

Suggested alternatives are styles that haven't been seen much in the community
yet but might provide some value.

#### Cond

* <a name="atom-conditions"></a>
  An atom can be used as a catch-all expression in a `cond` as it evaluates
  to a truthy value.
  Suggested atoms are `:else` or `:otherwise`
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

  # is the same as
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
