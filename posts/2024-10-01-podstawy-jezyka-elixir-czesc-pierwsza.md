---
layout: post
title: Podstawy języka Elixir (część pierwsza)
date: 2024-10-01
category:
  ["elixir", "programowanie", "funkcjonalne", "funkcyjne", "fp", "erlang"]
---

![header](/img/elixir_019.jpeg)

## Spis treści

- [Spis treści](#spis-treści)
- [Małe przypomnienie](#małe-przypomnienie)
- [Biblioteki](#biblioteki)
- [Typy danych](#typy-danych)
- [Pattern matching](#pattern-matching)
  - [Przypisywanie wartości](#przypisywanie-wartości)
  - [Dopasowywanie list](#dopasowywanie-list)
  - [Dopasowywanie krotek](#dopasowywanie-krotek)
  - [Dopasowywanie map](#dopasowywanie-map)
  - [Dopasowywanie z użyciem pin operatora (^)](#dopasowywanie-z-użyciem-pin-operatora-)
  - [Dopasowywanie w funkcjach](#dopasowywanie-w-funkcjach)
- [Sprawdzanie typów danych](#sprawdzanie-typów-danych)
- [Listy](#listy)
- [Krotki](#krotki)
- [Listy kluczy](#listy-kluczy)
- [Mapy](#mapy)
- [Funkcje anonimowe](#funkcje-anonimowe)
- [Arność funkcji](#arność-funkcji)
- [Wyrażenia listowe](#wyrażenia-listowe)
- [Wyrażenia logiczne](#wyrażenia-logiczne)
  - [Case](#case)
  - [If/unless](#ifunless)
- [Moduły, funkcje imienne, warunkowe oraz rekurencja](#moduły-funkcje-imienne-warunkowe-oraz-rekurencja)
  - [Rekurencja](#rekurencja)
  - [Moduły](#moduły)
    - [Obliczanie silni](#obliczanie-silni)
    - [Sumowanie elementów listy](#sumowanie-elementów-listy)
    - [Ciąg Fibonacciego](#ciąg-fibonacciego)
    - [Odwracanie listy](#odwracanie-listy)
    - [Wartości domyślne w funkcjach](#wartości-domyślne-w-funkcjach)
    - [Atrybuty modułów](#atrybuty-modułów)
  - [Uwagi odnośnie atrybutów](#uwagi-odnośnie-atrybutów)
  - [Atrybuty zarezerwowane](#atrybuty-zarezerwowane)
- [Przekazywanie (pipe)](#przekazywanie-pipe)
- [Enumeratory i strumienie](#enumeratory-i-strumienie)
  - [Enumeratory](#enumeratory)
- [Druga część artykułu](#druga-część-artykułu)
- [Referencje](#referencje)

## Małe przypomnienie

Czym jest Elixir?

Elixir to funkcyjny i współbieżny język programowania, który został stworzony w 2012 roku przez **José Valim'a** (twórca należał min. do zespołu rozwojowego `Rails`). Jego głównym celem było połączenie produktywności i elegancji języka Ruby z wydajnością i skalowalnością Erlang

Elixir jest językiem w korzystającym pełnymi garściami z ekosystemu języka `Erlang`. Elixir kompiluje się do kodu Erlanga i jest uruchamiany przez maszynę wirtualną `BEAM` (ang. `Bogdan's Erlang Abstract Machine`) nazywaną także jako Erlang VM.

Jest często on nazywany jako następca Ruby'ego, z którego czerpie wiele w swojej składni, jednak w przeciwieństwie do Ruby ten jest kompilowany, a i także także korzysta z tzw. `modelu aktora` (ang. `actor model`), który charakteryzuje go wysoką niezawodnością oraz wydajnością.

Dlatego też spisuje się idealnie wszędzie tam, gdzie wymagana jest wydajność oraz niezawodność. Z racji faktu, że wywodzi się z Erlanga to mamy do dyspozycji wszystko to co oferuje Nam Erlang z zakresu `OTP` (ang. `Online Telecom Protocol`) - o którym będę pisał w następnych artykułach.

Większy wstęp, znajdziecie pod tym [adresem](https://mikelogaciuk.github.io/posts/wprowadzenie-do-jezyka-elixir/).

## Biblioteki

Bibliotek do użycia z Naszym kodem, możemy szukać w `Hex`'ie: [tutaj](https://hex.pm/).

Te następnie dodajemy do `mix.exs`:

```elixir
  defp deps do
    [
      {:plug, "~> 1.1.0"}
    ]
  end
```

Zależności pobieramy i kompilujemy z pomocą `mix deps.get && mix deps.compile`, a kod uruchamiamy z pomocą `mix run` lub interaktywnie z pomocą `iex -s mix`.

## Typy danych

W Elixirze mamy kilka typów danych (w tym miejscu nie będę tłumaczył każdego z nich gdyż uznaję, że zapewne już coś wiesz o programowaniu).

- `Liczby całkowite` (ang. **integer**)
- `Liczby zmiennoprzecinkow` (ang. **float**)
- `Wartości logiczne` (ang. **boolean**)
- `Atomy` (ang. **atoms**) - Innymi słowy stałe, który nazwa jest równocześnie ich wartością: `:config` etc
- `Ciągi znaków` (ang. **string**)
- `Charlisty` (ang. **charlist**) - Tablica znaków np. `~c'Foo'`
- `Listy` (ang. **lists**) - Dynamiczne, dowolnej długości z elementami dowolnego typu: `list = [1, 2, "3", "four"]`
- `Krotki` (ang. **tuples**) - Struktury o stałej długości, mogące przechowywać elementy dowolnego typu np. `tuple = {:ok, "hi", 89}`
- `Mapy` (ang. **maps**) - Znane w innych językach jako słowniki (para: klucz), które definiujemy z pomocą % np. `map = %{:name => "Alice", "town": "Gdańsk", 1 => "one"}`

## Pattern matching

`Pattern matching` w języku `Elixir` to potężna funkcjonalność, która pozwala na przypisywanie wartości do zmiennych oraz na sprawdzanie struktury danych.

Można by pomyśleć, że poniższy zapis:

```elixir
1 = 1 # Wynik => 1
```

To nic innego jak typowe przypisanie.. Jednakżę nic bardziej mylnego, ponieważ wywołanie poniższego zwróci Nam to:

```elixir
1 = 2 # Wynik => ** (MatchError) no match of right hand side value: 2
```

U wielu może to wywołać zdziwienie, lecz biorąc pod uwagę fakt, że dane w Elixirze są **_niemutowalne_** tj (ang. `immutable`), powyższa sytuacja zaczyna nabierać sensu.

Powyższy przykład jest niczym innym jak **dopasowywaniem wzorców** (ang. **pattern matching**), który jest techniką w programowaniu funkcyjnym, która pozwala na sprawdzanie wartości względem określonego wzorca i, jeśli wartość pasuje do wzorca, wyodrębnianie z niej informacji.

Jest to elastyczny i wyrazisty sposób podejmowania decyzji na podstawie struktury danych. Innymi słowy można przyrównać to do sprawdzania wartości prawej do lewej i vice versa.

Poniżej kilka dodatkowych przykładów:

### Przypisywanie wartości

```elixir
x = 1
# x teraz wynosi 1
```

### Dopasowywanie list

```elixir
[a, b, c] = [1, 2, 3]
# a = 1, b = 2, c = 3
```

### Dopasowywanie krotek

```elixir
{:ok, result} = {:ok, 42}
# result = 42
```

### Dopasowywanie map

```elixir
%{name: name, age: age} = %{name: "Alice", age: 30}
# name = "Alice", age = 30
```

### Dopasowywanie z użyciem pin operatora (^)

```elixir
x = 1
^x = 1
# Dopasowanie się powiedzie, ponieważ x wynosi 1
```

### Dopasowywanie w funkcjach

```elixir
defmodule Example do
  def greet(%{name: name}) do
    "Hello, " <> name
  end
end

Example.greet(%{name: "Alice"})
# "Hello, Alice"
```

Pattern matching w Elixirze jest używany w wielu miejscach, takich jak przypisywanie zmiennych, funkcje, case expressions, i wiele innych. Pozwala to na bardziej deklaratywne i czytelne pisanie kodu.

## Sprawdzanie typów danych

Typy sprawdzamy z pomocą funkcji:

```elixir
is_atom/1
is_bitstring/1
is_boolean/1
is_function/1
is_function/2
is_integer/1
is_float/1
is_binary/1
is_list/1
is_map/1
is_tuple/1
is_nil/1
is_number/1
is_pid/1
is_port/1
is_reference/1
```

W przypadku funkcji `nazwanych`, `/1` lub `/2` mówią Nam o arności funkcji.

## Listy

`Listy` (ang. `lists`) definiujemy przy pomocy `[]`, a te mogą zawierać elementy różnych typów:

```elixir
warehouse = ["XLM129", 56.123, 78.000, 2000, false, :open]
```

```elixir
Enum.at(warehouse, 4)
```

```elixir
Enum.at(warehouse, -1)
```

```elixir
List.first(warehouse)
```

```elixir
List.replace_at(warehouse, 0, "FLG23T")
```

```elixir
[:Warsaw | warehouse]
```

```elixir
warehouse ++ ["44-200", "own"]
```

```elixir
List.delete(warehouse, "own")
```

```elixir
hd(warehouse)
```

```elixir
tl(warehouse)
```

```elixir
[head | tail] = warehouse
```

```elixir
head
```

```elixir
tail
```

```elixir
cameras = [:fujifilm, :sony, :hasselbad, :canon, :olympus]
```

```elixir
[faz, daz, azz | rest] = cameras
```

```elixir
faz
```

```elixir
rest
```

## Krotki

`Krotki` (ang. `tuples`) zapisywane są w pamięci w postaci ciągłej.

Sprawia to, że dostęp do elementów po indeksie lub sprawdzanie ich rozmiaru jest operacją szybką.

```elixir
passion = {:ok, "photography", "devops", :coding}
```

```elixir
tuple_size(passion)
```

```elixir
elem(passion, 3)
```

```elixir
Tuple.append(passion, "foo")
```

```elixir
put_elem(passion, 1, :boo)
```

```elixir
elem(passion, 1)
```

## Listy kluczy

`Listy kluczy` (ang. `keywords lists`) w Elixirze to specjalny rodzaj listy, w której każdy element jest krotką (tuple) zawierającą dwa elementy: klucz i wartość. Klucze są zazwyczaj atomami.

Keyword lists są często używane do przekazywania opcji do funkcji.

```elixir
params = [environment: :prod, users: [:mlog00, :wszc02]]
bad_params = [environment: :staging, users: [:mlog00, :wszc02]]
```

```elixir
params[:users]
```

```elixir
params[:environment]
```

```elixir
defmodule FakeApp do
  def init(args) do
    cond do
      args[:environment] == :prod -> {:ok, "Running in production environment."}
      true -> {:err, "Not running in production environment"}
    end
  end
end

FakeApp.init params
```

```elixir
FakeApp.init environment: :dev
```

```elixir
list = [name: "Alice", age: 18]
```

```elixir
name = Keyword.get(list, :name)
```

```elixir
list = Keyword.put(list, :city, "New York")
```

```elixir
list = Keyword.delete(list, :age)
```

```elixir
has_name = Keyword.has_key?(list, :name)
```

## Mapy

`Mapy` (ang. `maps`) w Elixirze to struktury danych, które przechowują pary klucz-wartość.

Są bardziej elastyczne niż `keyword lists`, ponieważ klucze mogą być dowolnym typem danych, a nie tylko atomami.

Mapy tworzymy z pomocą keywordu: `%{}`:

```elixir
map = %{"name" => "Alice", :age => 18, 1 => "one"}
```

```elixir
name = map["name"]
```

```elixir
age = map[:age]
```

```elixir
Map.has_key?(map, 1)
```

```elixir
map = %{map | "name" => "Bob"}
```

## Funkcje anonimowe

`Funkcje anonimowe` w Elixirze, znane również jako `lambdy`, są funkcjami, które nie mają przypisanej nazwy.

Są one często używane do krótkotrwałych operacji, które nie wymagają pełnej definicji funkcji nazwanej.

Funkcje anonimowe są definiowane za pomocą słowa kluczowego `fn` i kończą się słowem `end`.

Istnieje również możliwość używania syntaxu `&1`, aczkolwiek nie jest on zbytnio lubiany i może znacząco utrudniać zrozumienie kodu w przypadku wielu argunemntów.

```elixir
tax_value = fn (product_value) -> product_value * 0.23 end
```

```elixir
console_tax_val = tax_value.(3999)
```

```elixir
adder = fn (a, b) -> a + b end
```

```elixir
res = adder.(500, 100)
```

## Arność funkcji

Termin **arność** funkcji (ang. **arity**) - referuje do liczby argumentów jakie dana funkcja przyjmuje.

Jest to bardzo ważny koncept w języku Elixir z uwagi na fakt, że ten identyfikuje funkcje nie tylko z jej nazwy, ale także właśnie arności.

Tym samym częstym w języku Elixir jest sytuacja posiadania wielu funkcji o tej samej nazwie.

Jest to też ważny element języka w aspekcie rekurencji - będącego w tym przypadku ekwiwalentem dla klasycznych pętli, który w Elixirze nie ma.

```elixir
defmodule Math.Arrity.Example do
  def add(a), do: a + a
  def add(a, b) do
    a + b
  end
end
```

```elixir
a = 2

# add/1
Math.Arrity.Example.add(a)
```

```elixir
# add/2
Math.Arrity.Example.add(a, a)
```

## Wyrażenia listowe

Język Elixir nie posiada klasycznych pętli `for`, natomiast posiada możliwość iterowania po enumeratorach, często je filtrując i mapując w inną listę.

```elixir
for n <- [3, 9, 40], do: n * (n-3)
```

Można także stosować zakresy (ang. `range`):

```elixir
for x <- 1..100, do: x * x
```

Lub filtrować dane:

```elixir
envs = [prod: :storify, prod: :prefect, staging: :walld,
  dev: :foox, uat: :d365, prod: :ally, staging: :pos]
```

```elixir
for {:staging, val} <- envs, do: val
```

## Wyrażenia logiczne

W języku Elixir mamy takie wyrażenia logiczne jak:

- `case`
- `cond`
- `if`
- `unless`

### Case

Wyrażenie `case` pozwala nam porównywać wartość z wieloma wzorcami, dopóki nie znajdziemy pasującego:

```elixir
case {333, 666, 999} do
  {1, 2, 3} -> "This will not work...."
  {a, 666, 999} -> "Will match and bind 'a' to 333"
  _ -> "Will match any value"
end
```

```elixir
#case_ex_val = params[:environment]
case_ex_val = :dev

case case_ex_val do
  case_ex_val when case_ex_val == :prod -> "Running on prod..."
  _ -> "Undefined environment..."
end
```

Inny przykład z użyciem modułu oraz funkcji prywatnej oraz publicznej:

```elixir
defmodule Example.Case.In.Func do
  defp convert_binary_to_string(binary) do
    binary
    |> :binary.bin_to_list()
    |> Enum.filter(&(&1 != 0))
    |> List.to_string()
  end

  def binary_to_string(value) do
    case value do
      value when is_binary(value) == true -> convert_binary_to_string(value)
      _ -> value
    end
  end
end

case_bin_example = <<83, 0, 48, 0, 48, 0, 49, 0>>

Example.Case.In.Func.binary_to_string(case_bin_example)
```

W tym przypadku funkcja konwertuje wszystko co ma typ `binarny` na `string`, resztę pozostawiając taką jaką jest.

Więcej o modułach i funkcjach, w następnym rozdziale.

### If/unless

W przypadku Elixira, `if/2` oraz `unless/2` to makra (o których później) i raczej nie muszę ich tłumaczyć, po za `unless`, które dla osób z poza świata `Ruby` jest odwrotnością `if`.

Przykłady:

```elixir
envs = [environment: :staging]

if envs[:environment] == :staging do
  "Running on staging"
end
```

```elixir
if envs[:environment] == :staging do
  "Running on staging as it should"
else
  raise "ErlangError"
end
```

```elixir
unless false do
  "Error"
end
```

Jeśli chodzi o `unless` to często jest nazywany jako: _Evil twin brother (if)._ Jest spora grupa programistów z poza świata `Ruby` skuszonych Elixirem, która niecierpi tego makra.

W przypadku gdybyście chceli użyć zagnieżdzonych `if` czy `unless` - lepszym wyborem zapewne będzie `cond`:

```elixir
temp = -21

cond do
  temp < -60 -> "Vodka is starting to froze..."
  temp == -33 -> "Could be better"
  temp < -20 -> "Not great not terrible"
  temp < -5 -> "It's warm..."
  temp == 0 -> "It's okay."
  temp in 5..20 -> "Global warming..."
  temp >= 21 -> "We are going to die!"
  true -> "Err"
end
```

## Moduły, funkcje imienne, warunkowe oraz rekurencja

### Rekurencja

`Rekurencja` (ang. `recursion`) to technika programowania, w której funkcja wywołuje samą siebie w celu rozwiązania problemu.

Jest to szczególnie przydatne w Elixirze, który nie posiada tradycyjnych pętli, takich jak `for` czy `while`.

### Moduły

W tym przypadku przydatne stają się `moduły` (ang. `modules`) oraz `funkcje nazwane` tudzież `funkcje imienne` (ang. `named functions`) oraz `funkcje warunkowe` (ang. `guard'y`).

W Elixirze moduły i funkcje nazwane są podstawowymi elementami strukturyzacji kodu.

Moduły pozwalają na grupowanie funkcji i definiowanie przestrzeni nazw, podczas gdy funkcje nazwane są funkcjami, które mają przypisane nazwy i mogą być wywoływane z różnych miejsc w kodzie.

Moduły denifiujemy z pomocą `defmodule`, a funkcje nazwane z pomocą `def`, natomiast `guard'y` występują w funkcjach po nazwie funkcji oraz atrybutach np. `when x = 0 do something`.

Funkcje imienne możemy definiować też *one-linerami*: `def func, do: IO.puts("Something")`.

#### Obliczanie silni

```elixir
defmodule Math do
  def factorial(0), do: 1
  def factorial(n) when n > 0 do
    n * factorial(n-1)
  end
end
```

```elixir
Math.factorial(5)
```

#### Sumowanie elementów listy

```elixir
defmodule Math.Calc do
  def sum([]), do: 0
  def sum([head | tail]) do
    head + sum(tail)
  end
end
```

```elixir
Math.Calc.sum([2, 4, 6, 8])
```

#### Ciąg Fibonacciego

```elixir
defmodule Math.Fib do
  def fibonacci(0), do: 0
  def fibonacci(1), do: 1
  def fibonacci(n) when n > 1 do
    fibonacci(n - 1) + fibonacci(n - 2)
  end
end


```

```elixir
Math.Fib.fibonacci(10)
```

#### Odwracanie listy

```elixir
defmodule BinaryTree do
  defstruct value: nil, left: nil, right: nil

  def search(nil, _value), do: false
  def search(%BinaryTree{value: value}, value), do: true
  def search(%BinaryTree{value: node_value, left: left, right: right}, value) do
    if value < node_value do
      search(left, value)
    else
      search(right, value)
    end
  end
end
```

```elixir
tree = %BinaryTree{
  value: 10,
  left: %BinaryTree{value: 5},
  right: %BinaryTree{value: 15}
}
```

```elixir
BinaryTree.search(tree, 15)
```

#### Wartości domyślne w funkcjach

Wartości domyślne w funkcjach dodajemy w dosyć prosty sposób: `x \\ "hello"`:

```elixir
defmodule SomePlaneModule do
  def do_something(arg \\ "hello") do
    IO.inspect arg
  end
end
```

#### Atrybuty modułów

Elixir posiada koncepcję atrybutów modułów (odziedziczone z `Erlanga`).

Takie atrybuty, podobnie jak w `Ruby` definiujemy z pomocą `@`:

```elixir
System.put_env("STORE_PWDX", "foo")
```

```elixir
defmodule SomePlaneModule.Attributes.Example do
  @password System.fetch_env!("STORE_PWDX")

  def connect do
    # :odbc.start()
    # conn_str = "Driver={Oracle ODBC Driver};DBQ=srv:1521/xe;UID=usr;PWD=#{env};"
    # conn = to_charlist(conn_str)
    # {:ok, ref} = :odbc.connect(conn, [])

    IO.puts "Boilerplate: Connecting to database with password: #{@password}"
  end
end

SomePlaneModule.Attributes.Example.connect()
```

### Uwagi odnośnie atrybutów

Generalnie atrybuty w Elixirze mają trzy zadania:

- Adnotacji dla modułów i funkcji.
- Jako tymczasowy storage na etapie kompilacji.
- Stałe typu `compite-time`.

### Atrybuty zarezerwowane

Dodatkowo Elixir wspiera kilka zarezerwowanych atrybutów:

- `@moduledoc` — dokumentacja dla aktualnego modułu
- `@doc` — dokumentacja dla funkcji w module (po atrybucie)
- `@spec` — typ dla funkcji w module (po atrybucie)
- `@behaviour` — używana przy OTP lub zdefiniowanym przez użytkownika 'zachowaniu' (ang. `behaviour`)

Poniżej przykład bezpośrednio z dokumentacji Elixira:

```elixir
defmodule SomePlaneModule.Attributes.Example.Math do
  @moduledoc """
  Provides math-related functions.

  ## Examples

      iex> SomePlaneModule.Attributes.Example.Math.sum(1, 2)
      3

  """

  @doc """
  Calculates the sum of two numbers.
  """
  def sum(a, b), do: a + b
end
```

## Przekazywanie (pipe)

Przekazywanie w języku Elixir jest ekwiwalentem zagnieżdżania funkcji znanych z innych języków tj np:

```python
string = "hello world"

uppercase_string = String.upcase(string)  # Wynik => "HELLO WORLD"
words_list = String.split(uppercase_string)  # Wynik => ["HELLO", "WORLD"]
reversed_words = Enum.map(words_list, &String.reverse/1)  # Wynik => ["OLLEH", "DLROW"]
final_string = Enum.join(reversed_words, " ")  # Wynik => "OLLEH DLROW"

IO.inspect(final_string)  # Wynik => "OLLEH DLROW"
```

W przypadku Elixira możemy to zrobić tak:

```elixir
string = "hello world"

final_string = string
               |> String.upcase()
               |> String.split()
               |> Enum.map(&String.reverse/1)
               |> Enum.join(" ")

IO.inspect(final_string)  # Wynik => "OLLEH DLROW"
```

Operator `|>` służy do przekazywania wyniku jednej funkcji do argumentu następnej funkcji.

## Enumeratory i strumienie

Większość operacji na danych, mimo, że język umożliwia Nam pisanie rekurencyjnego kodu - obsługujemy z pomocą modułów `Enum` oraz `Stream`.

### Enumeratory

Enumenatory (ang. `Enumerables`) udostępniają sporą ilość funkcji umożliwiających sortowanie, grupowanie, filtrowanie czy pobieranie elementów: list bądź map.

`Cheatsheet` szeroko opisujący Enum'y można znaleźć pod [tym](https://hexdocs.pm/elixir/enum-cheat.html) adresem.

Poniżej kilka przykładów:

```elixir
Enum.map([1, 2, 3], fn x -> x * 2 end)
```

```elixir
5..50_000 |> Enum.map(fn x -> x * 2 end) |> Enum.sum()
```

Alternatywą dla `Enum`'ów są `Stream`'y, które są `leniwe` (ang. `lazy`).

Przydają się w pracy z z dużymi, możliwie nieskończonymi kolekcjami danych:

```elixir
ex_stream = Stream.cycle([666..999])
```

```elixir
Enum.take(ex_stream, 5)
```

## Druga część artykułu

Dalsza część artykułu, znajduje się [tutaj](https://mikelogaciuk.github.io/posts/podstawy-jezyka-elixir-czesc-druga/).

## Referencje

- [Elixir v17.2](https://hexdocs.pm/elixir/1.17.2/Kernel.html)
- [From Ruby to Elixir](https://pragprog.com/titles/sbelixir/from-ruby-to-elixir/)
- [Learn Functional Programming with Elixir](https://www.oreilly.com/library/view/learn-functional-programming/9781680505757/)
- [Programming Elixir](https://pragprog.com/titles/elixir16/programming-elixir-1-6/)
