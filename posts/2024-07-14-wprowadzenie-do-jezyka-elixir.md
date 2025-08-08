---
layout: post
title: Wprowadzenie do języka Elixir
date: 2024-08-01
category:
  ["elixir", "programowanie", "funkcjonalne", "funkcyjne", "fp", "erlang"]
---

![header](/img/elixir_006.jpeg)

## Spis treści

- [Spis treści](#spis-treści)
- [Wstęp](#wstęp)
  - [Czym jest Elixir?](#czym-jest-elixir)
  - [Zastosowanie języka Elixir](#zastosowanie-języka-elixir)
- [Programowanie funkcjonalne](#programowanie-funkcjonalne)
- [Instalacja](#instalacja)
- [Praca z kodem](#praca-z-kodem)
- [Biblioteki](#biblioteki)
- [Typy danych](#typy-danych)
- [Zmienne i dopasowywanie wzorców](#zmienne-i-dopasowywanie-wzorców)
  - [Porównywanie na listach](#porównywanie-na-listach)
  - [Destrukcja list (tak jakby)](#destrukcja-list-tak-jakby)
  - [Porównywanie krotek](#porównywanie-krotek)
  - [Porównywanie map](#porównywanie-map)
- [Składnia](#składnia)
  - [Przypisywanie zmiennych](#przypisywanie-zmiennych)
  - [Sprawdzanie typów](#sprawdzanie-typów)
  - [Funkcje anonimowe](#funkcje-anonimowe)
  - [Funkcje nazwane](#funkcje-nazwane)
  - [Arność funkcji](#arność-funkcji)
  - [Kolekcje](#kolekcje)
    - [Listy](#listy)
    - [Krotki](#krotki)
    - [Listy kluczy](#listy-kluczy)
  - [Mapy](#mapy)
  - [Przekazywanie](#przekazywanie)
  - [Struktury kontrolne](#struktury-kontrolne)
  - [Pętle czy rekurencja?](#pętle-czy-rekurencja)
  - [Wyrażenia listowe](#wyrażenia-listowe)
  - [Guardy (funkcje warunkowe)](#guardy-funkcje-warunkowe)
  - [Moduły](#moduły)
    - [Zagnieżdżone moduły](#zagnieżdżone-moduły)
    - [Atrybuty](#atrybuty)
    - [Importowanie, używanie i aliasy modułów](#importowanie-używanie-i-aliasy-modułów)
  - [Struktury](#struktury)
- [Pozostałe elementy składni](#pozostałe-elementy-składni)
- [Referencje](#referencje)

## Wstęp

### Czym jest Elixir?

Elixir to funkcyjny i współbieżny język programowania, który został stworzony w 2012 roku przez **José Valim'a** (twórca należał min. do zespołu rozwojowego `Rails`). Jego głównym celem było połączenie produktywności i elegancji języka Ruby z wydajnością i skalowalnością Erlang

Elixir jest językiem w korzystającym pełnymi garściami z ekosystemu języka `Erlang`. Elixir kompiluje się do kodu Erlanga i jest uruchamiany przez maszynę wirtualną `BEAM` (ang. `Bogdan's Erlang Abstract Machine`) nazywaną także jako Erlang VM.

Jest często on nazywany jako następca Ruby'ego, z którego czerpie wiele w swojej składni, jednak w przeciwieństwie do Ruby ten jest kompilowany, a i także także korzysta z tzw. `modelu aktora` (ang. `actor model`), który charakteryzuje go wysoką niezawodnością oraz wydajnością.

Dlatego też spisuje się idealnie wszędzie tam, gdzie wymagana jest wydajność oraz niezawodność. Z racji faktu, że wywodzi się z Erlanga to mamy do dyspozycji wszystko to co oferuje Nam Erlang z zakresu `OTP` (ang. `Online Telecom Protocol`) - o którym będę pisał w następnych artykułach.

### Zastosowanie języka Elixir

Elixir znajduje zastosowanie w wielu różnych dziedzinach, dzięki swojej wydajności i skalowalności:

1. Aplikacje Webowe: Framework Phoenix, zbudowany na Elixirze, jest często używany do tworzenia skalowalnych aplikacji webowych. Dzięki wsparciu dla WebSockets i LiveView, Phoenix umożliwia tworzenie interaktywnych aplikacji w czasie rzeczywistym.

2. Systemy Rozproszone: Elixir, działający na maszynie wirtualnej Erlanga, jest idealny do budowy systemów rozproszonych. Jego model współbieżności pozwala na łatwe zarządzanie wieloma procesami, co jest kluczowe w systemach wymagających wysokiej dostępności.

3. IoT (Internet of Things): Elixir jest używany w projektach IoT, gdzie wymagana jest obsługa wielu urządzeń jednocześnie. Jego zdolność do zarządzania wieloma równoczesnymi połączeniami sprawia, że jest idealnym wyborem do takich zastosowań.

4. Telekomunikacja: Dzięki swojej niezawodności i wydajności, Elixir jest używany w branży telekomunikacyjnej do budowy systemów, które muszą obsługiwać ogromne ilości danych i połączeń w czasie rzeczywistym.

5. Finanse: W sektorze finansowym, gdzie niezawodność i szybkość są kluczowe, Elixir jest używany do budowy systemów transakcyjnych i analitycznych.

6. Gry: Elixir znajduje również zastosowanie w branży gier, szczególnie w tworzeniu serwerów gier, które muszą obsługiwać dużą liczbę graczy jednocześnie.

Język Elixir jest używany przez takie firmy jak:

- **Discord**: Popularna platforma komunikacyjna używa Elixira do obsługi milionów równoczesnych połączeń.
- **Pinterest**: Wykorzystuje Elixira do zarządzania swoją infrastrukturą back-end'ową.
- **Bleacher Report**: Używa Elixira do obsługi swoich aplikacji mobilnych i webowych.

## Programowanie funkcjonalne

Na temat programowania funkcjonalnego na przykładzie Elixir'a, oraz po części Haskell'a możecie dowiedzieć się z tego [artykułu](https://mikelogaciuk.github.io/posts/wstep-do-pogramowania-funkcjonalnego/).

## Instalacja

Elixir wymaga Erlang'a, więc najpierw należy zainstalować go, a potem zgodną z Nim wersję Elixira.

O tym jak zainstalować Erlanga, dowiecie się [tutaj](https://mikelogaciuk.github.io/posts/przygotowanie-linuxa-do-pracy-sre/#erlang), a w przypadku Elixira [tu](https://mikelogaciuk.github.io/posts/przygotowanie-linuxa-do-pracy-sre/#elixir).

## Praca z kodem

W przypadku Elixira mamy dwie opcje:

- Interaktywny Shell Elixira (IEx)
- Klasyczna praca z kodem

W celu rozpoczęcia projektu, używamy w tym celu narzędzia `Mix`, które jest domyślnym narzędziem do zarządzania projektami.

```shell
$ mix help
mix                   # Runs the default task (current: "mix run")
mix app.config        # Configures all registered apps
mix app.start         # Starts all registered apps
mix app.tree          # Prints the application tree
mix archive           # Lists installed archives
mix archive.build     # Archives this project into a .ez file
mix archive.install   # Installs an archive locally
mix archive.uninstall # Uninstalls archives
mix clean             # Deletes generated application files
mix cmd               # Executes the given command
mix compile           # Compiles source files
mix deps              # Lists dependencies and their status
mix deps.clean        # Deletes the given dependencies' files
mix deps.compile      # Compiles dependencies
mix deps.get          # Gets all out of date dependencies
mix deps.tree         # Prints the dependency tree
mix deps.unlock       # Unlocks the given dependencies
mix deps.update       # Updates the given dependencies
mix do                # Executes the tasks separated by plus
mix escript           # Lists installed escripts
mix escript.build     # Builds an escript for the project
mix escript.install   # Installs an escript locally
mix escript.uninstall # Uninstalls escripts
mix eval              # Evaluates the given code
mix format            # Formats the given files/patterns
mix help              # Prints help information for tasks
mix hex               # Prints Hex help information
mix hex.audit         # Shows retired Hex deps for the current project
mix hex.build         # Builds a new package version locally
mix hex.config        # Reads, updates or deletes local Hex config
mix hex.docs          # Fetches or opens documentation of a package
mix hex.info          # Prints Hex information
mix hex.organization  # Manages Hex.pm organizations
mix hex.outdated      # Shows outdated Hex deps for the current project
mix hex.owner         # Manages Hex package ownership
mix hex.package       # Fetches or diffs packages
mix hex.publish       # Publishes a new package version
mix hex.registry      # Manages local Hex registries
mix hex.repo          # Manages Hex repositories
mix hex.retire        # Retires a package version
mix hex.search        # Searches for package names
mix hex.sponsor       # Show Hex packages accepting sponsorships
mix hex.user          # Manages your Hex user account
mix loadconfig        # Loads and persists the given configuration
mix local             # Lists tasks installed locally via archives
mix local.hex         # Installs Hex locally
mix local.phx         # Updates the Phoenix project generator locally
mix local.public_keys # Manages public keys
mix local.rebar       # Installs Rebar locally
mix new               # Creates a new Elixir project
mix phx.new           # Creates a new Phoenix v1.7.12 application
mix phx.new.ecto      # Creates a new Ecto project within an umbrella project
mix phx.new.web       # Creates a new Phoenix web project within an umbrella project
mix profile.cprof     # Profiles the given file or expression with cprof
mix profile.eprof     # Profiles the given file or expression with eprof
mix profile.fprof     # Profiles the given file or expression with fprof
mix profile.tprof     # Profiles the given file or expression with tprof
mix release           # Assembles a self-contained release
mix release.init      # Generates sample files for releases
mix run               # Runs the current application
mix test              # Runs a project's tests
mix test.coverage     # Build report from exported test coverage
mix xref              # Prints cross reference information
iex -S mix            # Starts IEx and runs the default task
```

Projekt tworzymy przy pomocy:

```shell
$ mix new foo

* creating README.md
* creating .formatter.exs
* creating .gitignore
* creating mix.exs
* creating lib
* creating lib/foo.ex
* creating test
* creating test/test_helper.exs
* creating test/foo_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd foo
    mix test

Run "mix help" for more commands.
```

Pełną, oryginalną dokumentację języka Elixir znajdziecie [tu](https://hexdocs.pm/elixir/1.17.2/Kernel.html).

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

- Liczby całkowite (ang. integer)
- Liczby zmiennoprzecinkowe (ang. float)
- Wartości logiczne (ang. boolean)
- Atomy (ang. atoms) - Innymi słowy stałe, który nazwa jest równocześnie ich wartością: `:config` etc
- Ciągi znaków (ang. string)
- Listy (ang. lists) - Dynamiczne, dowolnej długości z elementami dowolnego typu: `list = [1, 2, "3", "four"]`
- Krotki (ang. tuples) - Struktury o stałej długości, mogące przechowywać elementy dowolnego typu np. `tuple = {:ok, "hi", 89}`
- Mapy (ang. maps) - Znane w innych językach jako słowniki (para: klucz), które definiujemy z pomocą % np. `map = %{:name => "Alice", "town": "Gdańsk", 1 => "one"}`

## Zmienne i dopasowywanie wzorców

Można by pomyśleć: że poniższy zapis:

```elixir
1 = 1 # Wynik => 1
```

To nic innego jak typowe przypisanie... Nic bardziej mylnego, ponieważ wywołanie poniższego zwróci Nam to:

```elixir
1 = 2 # Wynik => ** (MatchError) no match of right hand side value: 2
```

U wielu może to wywołać zdziwienie, lecz biorąc pod uwagę fakt, że dane w Elixirze _są niemutowalne_ tj (niezmienne, z ang. immutable), powyższa sytuacja zaczyna nabierać sensu.

Powyższy przykład jest niczym innym jak **dopasowywaniem wzorców** (ang. **pattern matching**), który jest techniką w programowaniu funkcyjnym, która pozwala na sprawdzanie wartości względem określonego wzorca i, jeśli wartość pasuje do wzorca, wyodrębnianie z niej informacji. Jest to elastyczny i wyrazisty sposób podejmowania decyzji na podstawie struktury danych. Innymi słowy można przyrównać to do sprawdzania wartości prawej do lewej i vice versa.

Innym przykładami pattern-matching'u są choćby poniższe.

### Porównywanie na listach

```elixir
[1, 2, 3] = [1, 2, 3]  # Porównanie prawidłowe
[1, 2, 3] = [1, 2, 4]  # Błąd, otrzymamy: MatchError
```

### Destrukcja list (tak jakby)

```elixir
[head | tail] = [1, 2, 3, 4]  # Wynik => head = 1, tail = [2, 3, 4]
[_, second | rest] = [10, 20, 30, 40]  # Wynik => second = 20, rest = [30, 40]
```

### Porównywanie krotek

```elixir
{:ok, value} = {:ok, 42}  # Wynik => value = 42
{:ok, value} = {:error, "failed"}  # Wynik => Otrzymujemy MatchError
```

### Porównywanie map

```elixir
%{name: name, age: age} = %{name: "Alice", age: 30}  # Wynik => name = "Alice", age = 30
%{name: name} = %{age: 30}  # Wynik => Zwraca MatchError

# Nested maps
%{user: %{name: user_name}} = %{user: %{name: "Bob", age: 25}}  # Wynik => user_name = "Bob"
```

Więcej na temat `pattern-matching`'u napisałem w [tym](https://mikelogaciuk.github.io/posts/wstep-do-pogramowania-funkcjonalnego/#dopasowywanie-wzorc%C3%B3w) artykule.

## Składnia

Składnia języka Elixir jest inspirowana oczywiście językiem `Ruby`, a i także nie jest nazbyt skomplikowana.

Na rynek wydano kilka naprawdę świetnych książek opisujących ten język w sposób od nowicjusza do profesjonalisty. Z tego powodu nadmiernie też nie będę się nad tym rozpisywał, z racji także faktu, że język ma świetną dokumentację, która może w zupełności i za darmo zastąpić niejedną książkę.

### Przypisywanie zmiennych

Przypisywanie zmiennych i wzorcowanie zostało opisane we wcześniejszym rozdziale.

### Sprawdzanie typów

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

W przypadku funkcji `nazwanych`, `/1` lub `/2` mówią Nam o arności funkcji (ang. `arity`) - o tym za chwilę.

### Funkcje anonimowe

Funkcje anonimowe definiujemy tak:

```elixir
foo = fn arg -> arg * 2 end
IO.inspect foo.(2) # Wynik => 4
```

Oczywiście należy mieć na uwadze, że każda funkcja może też wywołać inną funkcję, a także fakt, że funkcje anonimowe (lambda) możemy definiować też tak:

```elixir
foo = &(& * 2)

IO.inspect foo.(2) # Wynik => 4
```

### Funkcje nazwane

Funkcje nazwane natomiast wyglądają tak:

```elixir
defmodule Doo do
  def foo(arg) do
    arg * 2
  end
end

IO.inspect Doo.foo(2) # Wynik => 4
```

### Arność funkcji

Termin **arność** funkcji (ang. **arity**) - referuje do liczby argumentów jakie dana funkcja przyjmuje.

Jest to bardzo ważny koncept w języku Elixir z uwagi na fakt, że ten identyfikuje funkcje nie tylko z jej nazwy, ale także właśnie arności.

Tym samym częstym w języku Elixir jest sytuacja posiadania wielu funkcji o tej samej nazwie. Jest to też ważny element języka w aspekcie rekurencji - będącego w tym przypadku ekwiwalentem dla klasycznych pętli, który w Elixirze nie ma.

### Kolekcje

#### Listy

```elixir
cities = [:Warsaw, :Gdansk, :Poznan]


cities |> List.first() # Wynik => :Warsaw
cities_new = cities ++ [:Cracow] # Wynik =>  [:Warsaw, :Gdansk, :Poznan, :Cracow]
cities_another = [ :Wroclaw | cities_new ] # Wynik => [:Wroclaw, :Warsaw, :Gdansk, :Poznan, :Cracow]
cities_another |> List.last() # Wynik => :Cracow

Enum.map(cities, fn city -> IO.puts "Hello from: " <> to_string(city) end)
# Wynik =>
# Hello from: Warsaw
# Hello from: Gdansk
# Hello from: Poznan
```

#### Krotki

```elixir
tupl = { :quack, :duck}

tupl |> elem(1) |> to_string() # Wynik => "duck"
```

#### Listy kluczy

To specjalny typ list, w którym każdy element jest krotką z dwoma elementami, którego klucz jest `:atomem`. Listy te są posortowane:

```elixir
# Define a keyword list
opts = [timeout: 3000, retries: 5]

# Add a new key-value pair
opts = Keyword.put(opts, :max_retries, 10)

IO.inspect opts # Wynik => [max_retries: 10, timeout: 3000, retries: 5]
```

### Mapy

Inaczej słowniki (klucz: wartość):

```elixir
map = %{a: 1, b: 2, c: 3}

result = map
         |> Enum.map(fn {key, value} -> {key, value * 2} end)
         |> Enum.into(%{})

IO.inspect(result)  # Wynik => %{a: 2, b: 4, c: 6}
```

### Przekazywanie

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

### Struktury kontrolne

**Struktury kontrolne** (bo tak wypadało by tłumacz z angielskiego **control structures**) to nic innego jak też inaczej nazywany **control flow** czyli kontrola przepływów.

Mimo faktu, że w Elixirze jest `if` oraz `unless`:

```elixir
something = true

if something do
  "This is true"
else
  "This is false"
end # Wynik => "This is true"
```

```elixir
something = false

unless something do
  "This is false"
else
  "This is true"
end
# Wynik => "This is false"
```

Z reguły, częściej stosowane są `case` oraz `cond`:

```elixir
x = 666

case x do
  0 -> "zero"
  1 -> "one"
  _ -> "other"
end # Wynik => "other"

cond do
  x < 0 -> "negative"
  x == 0 -> "zero"
  x > 0 -> "positive"
  x == 666 -> "hell"
end # Wynik => "hell"
```

### Pętle czy rekurencja?

W Elixirze nie uświadczycie znanych z języków obiektowych pętli `while` czy `until` z `Ruby`.

Z racji, że Elixir to język funkcjonalny, natywnym środkiem zastępującym klasyczne pętle, jest **rekurencja** (ang. **recursion**):

```elixir
defmodule Loop do
  def countdown(0), do: "Blastoff!"
  def countdown(n) do
    IO.puts(n)
    countdown(n - 1)
  end
end

Loop.countdown(3)
```

### Wyrażenia listowe

Elixir wspiera wyrażenia listowe, które umożliwiają tworzenie nowych list na podstawie istniejących kolekcji:

```elixir
for n <- 1..4, do: n * n  # Wynik => [1, 4, 9, 16]
```

### Guardy (funkcje warunkowe)

W Elixirze istnieje możliwość pisania funkcji warunkowych:

```elixir
defmodule GuardExample do
  def check_number(x) when x > 0, do: "Positive"
  def check_number(x) when x < 0, do: "Negative"
  def check_number(0), do: "Zero"
end

GuardExample.check_number(10)  # Wynik => "Positive"
GuardExample.check_number(-5)  # Wynik => "Negative"
GuardExample.check_number(0)  # Wynik => "Zero"
```

### Moduły

Moduły w Elixirze służą do grupowania kodu w logiczne jednostki, co ułatwia organizację i zarządzanie kodem. Moduły mogą zawierać funkcje, struktury, a także inne moduły. Dzięki modułom możemy tworzyć bardziej zorganizowane i czytelne aplikacje.

```elixir
defmodule Math do
  # Publiczna funkcja dodająca dwie liczby
  def add(a, b) do
    a + b
  end

  # Prywatna funkcja mnożąca dwie liczby
  defp multiply(a, b) do
    a * b
  end
end

IO.puts(Math.add(2, 3))  # Wynik => 5

# Próba wywołania funkcji prywatnej zakończy się błędem
# IO.puts(Math.multiply(2, 3))  # Błąd kompilacji
```

#### Zagnieżdżone moduły

Moduły mogą być zagnieżdżane, co pozwala na jeszcze lepszą organizację kodu. Zagnieżdżone moduły są definiowane wewnątrz innych modułów.

```elixir
defmodule Outer do
  defmodule Inner do
    def greet do
      "Hello from the inner module!"
    end
  end
end

IO.puts(Outer.Inner.greet())  # Wynik => "Hello from the inner module!"
```

#### Atrybuty

Moduły mogą zawierać atrybuty, które są używane do przechowywania metadanych. Najczęściej używanym atrybutem jest `@doc`, który służy do dokumentowania funkcji.

```elixir
defmodule Documented do
  @moduledoc """
  Moduł zawierający przykładowe funkcje z dokumentacją.
  """

  @doc """
  Dodaje dwie liczby.
  """
  def add(a, b) do
    a + b
  end
end
```

#### Importowanie, używanie i aliasy modułów

Elixir oferuje kilka sposobów na zarządzanie modułami w kodzie:

- **import**: Importuje funkcje z innego modułu, dzięki czemu można je wywoływać bez podawania pełnej nazwy modułu.
- **use**: Umożliwia włączenie funkcjonalności z innego modułu.
- **alias**: Tworzy alias dla modułu, co skraca jego nazwę.

```elixir
defmodule Example do
  import Math, only: [add: 2]

  use SomeLibrary

  alias Some.Long.Module.Name, as: ShortName

  def example_function do
    add(1, 2)
    ShortName.some_function()
  end
end
```

### Struktury

W Elixirze istnieją struktury, będące tak ustrukturyzowanymi mapami

```elixir
defmodule Engine do
  defstruct type: "", horsepower: 0
end

defmodule Car do
  defstruct make: "", model: "", year: 0, engine: %Engine{}
end

car = %Car{
  make: "Ford",
  model: "Mustang",
  year: 2021,
  engine: %Engine{type: "V8", horsepower: 450}
}
IO.inspect(car)  # Wynik => %Car{make: "Ford", model: "Mustang", year: 2021, engine: %Engine{type: "V8", horsepower: 450}}
```

## Pozostałe elementy składni

Składnię jezyka Elixir w bardziej rozwiniętej formie, możecie znaleźć pod [tym](https://mikelogaciuk.github.io/posts/podstawy-jezyka-elixir-czesc-pierwsza/) adresem.

## Referencje

- [Elixir v17.2](https://hexdocs.pm/elixir/1.17.2/Kernel.html)
- [From Ruby to Elixir](https://pragprog.com/titles/sbelixir/from-ruby-to-elixir/)
- [Learn Functional Programming with Elixir](https://www.oreilly.com/library/view/learn-functional-programming/9781680505757/)
- [Programming Elixir](https://pragprog.com/titles/elixir16/programming-elixir-1-6/)
- [The Little Elixir & OTP Guidebook](https://www.manning.com/books/the-little-elixir-and-otp-guidebook)
- [Designing Elixir Systems with OTP: Write Highly Scalable, Self-Healing Software with Layers](https://pragprog.com/titles/jgotp/designing-elixir-systems-with-otp/)
- [No Fluff Jobs Log](https://nofluffjobs.com/pl/log/praca-w-it/co-warto-wiedziec-o-jezyku-programowania-elixir/)
- [DevHints IO Cheat-sheet](https://devhints.io/elixir)
- [Learn Elixir in Y minutes](https://learnxinyminutes.com/docs/elixir/)
- [WhatsApp, Discord, and the Secret to Handling Millions of Concurrent Users](https://favtutor.com/articles/whatsapp-discord-and-the-secret-to-handling-millions-of-concurrent-users/)
- [How Discord handles push request burst of over a million per minute with Elixirs GenStage](https://discord.com/blog/how-discord-handles-push-request-bursts-of-over-a-million-per-minute-with-elixirs-genstage)
