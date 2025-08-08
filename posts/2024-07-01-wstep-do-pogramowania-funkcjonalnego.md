---
layout: post
title: Wstęp do programowania funkcjonalnego
date: 2024-07-01
category: ["programowanie", "funkcjonalne", "elixir"]
---

![header](/img/exr_cr_rust_17.jpeg)

## Spis treści

- [Spis treści](#spis-treści)
- [Czym jest programowanie funkcyjne?](#czym-jest-programowanie-funkcyjne)
- [Znaczenie niezmienności danych](#znaczenie-niezmienności-danych)
- [Czyste funkcje i efekty uboczne](#czyste-funkcje-i-efekty-uboczne)
- [Dopasowywanie wzorców](#dopasowywanie-wzorców)
- [Pętle, rekurencja](#pętle-rekurencja)
  - [Pętle w programowaniu funkcyjnym i funkcje wyższego rzędu](#pętle-w-programowaniu-funkcyjnym-i-funkcje-wyższego-rzędu)
  - [Rekurencja](#rekurencja)
  - [Zalety i wady rekurencji](#zalety-i-wady-rekurencji)
    - [Zalety](#zalety)
    - [Wady](#wady)
- [Funkcje wyższego rzędu](#funkcje-wyższego-rzędu)
  - [Kluczowe pojęcia funkcji wyższego rzędu](#kluczowe-pojęcia-funkcji-wyższego-rzędu)
- [Domknięcia i rozwijanie funkcji](#domknięcia-i-rozwijanie-funkcji)
  - [Domknięcia](#domknięcia)
  - [Rozwijanie funkcji](#rozwijanie-funkcji)
  - [Częściowa aplikacja](#częściowa-aplikacja)
- [Wartościowanie](#wartościowanie)
- [Podsumowanie](#podsumowanie)
- [Żródła](#żródła)

## Czym jest programowanie funkcyjne?

Programowanie funkcjonalne (lub funkcyjne), w skrócie FP, to paradygmat programowania, który koncentruje się na traktowaniu obliczeń jako ewaluacji funkcji matematycznych.

Wikipedia:

_Podstawą teoretyczną programowania funkcyjnego jest rachunek lambda (a dokładnie rachunek lambda z typami). Został on opracowany w latach 30. XX wieku przez Alonzo Churcha. Nazwa paradygmatu po raz pierwszy pojawiła się w pracy Johna Backusa: "Can Programming Be Liberated From the von Neumann Style? A Functional Style and Its Algebra of Programs" w roku 1977, dzięki której Backus dostał nagrodę Turinga, mimo że programowanie funkcyjne było znane już wcześniej._

Popularne języki programowania funkcjonalnego to `Haskell`, `Scala`, `Clojure`, `Erlang`, `Elixir` czy `F#`.

Dodatkowo funkcjonalność programowania funcjonalnego dostępna jest w takich choćby językach jak `Ruby`, `Python`, `Rust`, `JavaScript` itd.

W poniższym mini-artykule skupimy się na programowaniu funkcjonalnym w odniesieniu do języka **Elixir**, choć będziemy się także wspomagać, królem **Haskell'em**.

Oto kilka kluczowych zasad i cech programowania funkcyjnego:

1. **Czyste funkcje**: Funkcje w FP są czyste, co oznacza, że dla tych samych argumentów zawsze zwracają te same wyniki i nie mają efektów ubocznych. Dzięki temu kod jest bardziej przewidywalny i łatwiejszy do testowania.

2. **Niezmienność danych**: W FP dane są niezmienne, co oznacza, że po utworzeniu nie mogą być zmieniane. Zamiast tego tworzy się nowe wersje danych. To pomaga uniknąć problemów związanych z współbieżnością i stanem.

3. **Funkcje wyższego rzędu**: Funkcje mogą przyjmować inne funkcje jako argumenty lub zwracać je jako wyniki. To pozwala na tworzenie bardziej abstrakcyjnych i elastycznych rozwiązań.

4. **Rekurencja zamiast iteracji**: W FP często używa się rekurencji zamiast tradycyjnych pętli. Rekurencja jest naturalnym sposobem wyrażania powtarzających się obliczeń w FP.

5. **Przejrzystość referencyjna**: Programy funkcjonalne są przejrzyste referencyjnie, co oznacza, że wyrażenia mogą być zastępowane ich wartościami bez zmiany zachowania programu.

6. **Modułowość**: Kod w FP jest często bardziej modułowy, co ułatwia jego ponowne wykorzystanie i utrzymanie.

## Znaczenie niezmienności danych

Dane niemutowalne tudzież niezmienne (immutable) jest kluczową zasadą programowania funkcyjnego.

Poniżej kilka argumentów za tym by dane były niemutowalne:

1. **Niezawodność i przewidywalność**: Z racji, że dane są niezmienialne, prowadzi to do przewidywalnego i niezawodnego kodu. Nie ma sytuacji, w której podczas debugowania musimy śledzić stan obiektu. W tym wypadku też nie ma obiektu - jest wszakże funkcja.

2. **Współbieżność i równoległość**: Niezmienność przynosi niebywałe korzyści podczas pgoramowania współbieżnego i równoległego. Niezmienne struktury zapobiegają tzw. race conditions, umożliwiając bezpieczne udostępnianie danych między procesami.

3. **Efektywne wykorzystanie pamięci**: Z racji faktu, korzystania z techniki współdzielenia strukturalnego - nowe struktury danych współdzielą części starych danych - zamiast kopiować wszystko od nowa. To powoduje, że oparacje na danych są efektywniejsze pod względem zużycia pamięci.

4. **Paradygmat programowania funkcjonalnego**: Niezmienność jest zgodna z paradygmatem programowania funkcyjnego, który kładzie nacisk na użycie czystych funkcji i unikanie efektów ubocznych.

## Czyste funkcje i efekty uboczne

Czyste funkcje (pure functions) oraz efekty uboczne są kluczowymi elementami pomagającymi w pisaniu przewidywalnego i łatwego w utrzymaniu kodu.

**Czyste funkcje** to takie, które zawsze zwracają ten wynik dla tych samych argumentów i które nie mają efektów ubocznych.

**Efekty uboczne** to oczywiście wszelakie operacje mające wpływ na stan po za funkcją. Dla przykładu, język **Elixir** zachęca do używania czystych funkcji, zapewnia również mechanizmy do kontrolowanego zarządzania efektami ubocznymi. Często odbywa się to poprzez izolowanie efektów ubocznych od głównej logiki aplikacji:

```elixir
defmodule Logger do
  def log_message(message) do
    IO.puts("Log: #{message}")
  end
end

defmodule Math do
  def add(a, b) do
    result = a + b
    Logger.log_message("Adding #{a} and #{b} to get #{result}")
    result
  end
end

IO.inspect(Math.add(2, 3)) # Wynik: 5
# "Log: Adding 2 and 3 to get 5"
```

W tym przykładzie, funkcja `log_message/1` ma efekt uboczny (wydruk w konsoli). Funkcja `add/2` pozostaje czysta w swojej głównej logice, ale wywołuje funkcję `log_message/1`, aby obsłużyć efekt uboczny.

## Dopasowywanie wzorców

**Dopasowywanie wzorców** (ang. **pattern matching**) jest techniką w programowaniu funkcyjnym, która pozwala na sprawdzanie wartości względem określonego wzorca i, jeśli wartość pasuje do wzorca, wyodrębnianie z niej informacji. Jest to elastyczny i wyrazisty sposób podejmowania decyzji na podstawie struktury danych. Innymi słowy można przyrównać to do sprawdzania wartości prawej do lewej.

Jest to bardzo trafnie omówione w książce **Learn Functional Programming with Elixir** autorstwa **Ulisses Almeida'y**, bazując na przykładzie kodu:

```elixir
1 = 1 # Wynik: 1

2 = 1 # Wynik: ** (MatchError) no match of right hand side value: 1

1 = 2 # Wynik: ** (MatchError) no match of right hand side value: 2

x = 1 # Wynik: 1

1 = x # Wynik: 1
```

    1 = 1 matches, but 2 = 1 and 1 = 2 don’t because they’re different numbers.
    (...)

    You’re probably saying to yourself, "What a disappointment! It’s just a variable
    assignment!" Maybe you’re thinking it’s a joke. But it’s not. It’s pattern
    matching. Elixir is making both sides equivalent by binding the value 1 to the
    variable x. (...)

    The value is on the left side, the variable is on the right side, and it’s a valid
    Elixir expression. It’s fascinating, right? We said previously that x = 1, and
    now the value 1 is bound to the x variable. When we type 1 = x, Elixir tries to
    check if the value on the left side is equal to the right side. If the two sides
    are equal, then we have a valid expression.

Kluczowymi aspektami **pattern matching'u** są:

1. **Czytelność:** Kod z użyciem pattern matching jest bardziej zrozumiały. Można od razu zobaczyć, jak dane są strukturyzowane i przetwarzane.
2. **Redukcja złożoności**: Zmniejsza potrzebę stosowania złożonych, zagnieżdżonych instrukcji warunkowych. Zamiast serii bloków “if-else”, definiujesz wzorce i akcje, co prowadzi do bardziej zwięzłego i wyrazistego kodu.
3. **Bezpieczeństwo**: Pomaga wychwycić błędy i przypadki brzegowe na wczesnym etapie. Zapewnia, że rozważono wszystkie możliwe kształty danych i określono sposób obsługi każdego przypadku.
4. **Modularność**: Zachęca do modularnego podejścia do kodowania, gdzie można oddzielnie definiować wzorce i akcje. Promuje to ponowne użycie kodu i upraszcza testowanie oraz debugowanie.

Poniżej przykład pattern matching'u w **Haskellu**:

```haskell
-- Definicja typu danych
data Maybe a = Nothing | Just a

-- Funkcja z użyciem pattern matching
safeHead :: [a] -> Maybe a
safeHead [] = Nothing
safeHead (x:xs) = Just x

main :: IO ()
main = do
  print (safeHead [1, 2, 3])  -- Wynik: Just 1
  print (safeHead [])         -- Wynik: Nothing
```

W tym przykładzie, funkcja `safeHead` używa pattern matching do sprawdzenia, czy lista jest pusta. Jeśli jest pusta, zwraca `Nothing`. Jeśli lista zawiera elementy, zwraca pierwszy element opakowany w `Just`.

Inny przykład napisany w Elixirze:

```elixir
defmodule Example do
  def describe_list([]), do: "List is empty."
  def describe_list([head | tail]), do: "The list starts with #{head} and has #{length(tail)} remaining elements."
end

IO.puts Example.describe_list([])          # Wynik: List is empty.
IO.puts Example.describe_list([1, 2, 3])   # Wynik: The list starts with 1 and has remaining elements.
```

W Elixirze, pattern matching jest używany do dopasowywania struktury listy. Funkcja `describe_list` rozpoznaje, czy lista jest pusta, czy zawiera elementy, i odpowiednio zwraca opis.

## Pętle, rekurencja

W programowaniu funkcyjnym, pętle i rekurencja są kluczowymi technikami do wykonywania powtarzalnych operacji. Z tym, że w tzw. czystym programowaniu funkcjonalnym nie występują znane z obiektowego, pętle `for` czy `while`. Językami, w który one występują są `Scala`, `OCaml` czy `F#`.

### Pętle w programowaniu funkcyjnym i funkcje wyższego rzędu

W tradycyjnych językach programowania, takich jak **C** czy **Python**, pętle for i while są powszechnie używane do iteracji.
Jednak w czystym programowaniu funkcyjnym, stosuje się funkcje wyższego rzędu, takie jak `map`, `filter` i `reduce`:

```elixir
list = [1, 2, 3, 4, 5]
# Mapujemy element listy do funkcji używając funkcji wyższego rzędu 'map'
squared_list = Enum.map(list, fn x -> x * x end)
IO.inspect(squared_list)  # Wynik: [1, 4, 9, 16, 25]
```

### Rekurencja

**Rekurencja** (**ang. recursion**) to technika, w której funkcja wywołuje samą siebie, aby rozwiązać problem. Jest to podstawowy mechanizm iteracji w wielu językach funkcyjnych, takich jak **Haskell** czy **Elixir**.

Rekurencję można zobrazować na poniższym przykładzie:

```elixir
defmodule Example do
  def factorial(0), do: 1
  def factorial(n), do: n * factorial(n - 1)
end

IO.puts Example.factorial(5)  # Wynik: 120
```

### Zalety i wady rekurencji

#### Zalety

- **Czytelność**: Rekurencyjne rozwiązania są często bardziej zwięzłe i łatwiejsze do zrozumienia.
- **Naturalność**: Rekurencja jest naturalnym sposobem rozwiązywania problemów, które można podzielić na mniejsze podproblemy, jak np. przeszukiwanie drzew.

#### Wady

- **Wydajność**: Rekurencja może prowadzić do problemów z pamięcią, jeśli jest zbyt głęboka, ponieważ każde wywołanie rekurencyjne jest umieszczane na stosie wywołań.
- **Złożoność**: Może być trudniejsza do debugowania i zrozumienia dla osób nieprzyzwyczajonych do tego podejścia.

Podsumowując, tematykę iteracji w programowaniu funkcjonalnym:

- **Pętle**: W programowaniu funkcyjnym często zastępowane przez funkcje wyższego rzędu.
- **Rekurencja**: Podstawowy mechanizm iteracji, szczególnie przydatny w rozwiązywaniu problemów, które można podzielić na mniejsze podproblemy.

## Funkcje wyższego rzędu

Funkcje wyższego rzędu to potężna cecha wielu innych języków programowania funkcyjnego.

Te funkcje albo przyjmują inne funkcje jako argumenty, albo zwracają je jako wyniki. Ta zdolność pozwala na bardziej abstrakcyjny i elastyczny kod.

### Kluczowe pojęcia funkcji wyższego rzędu

1. **Funkcje jako argumenty**: Funkcje wyższego rzędu mogą przyjmować inne funkcje jako parametry. Pozwala to na tworzenie bardziej ogólnego i wielokrotnego użytku kodu. Przykład: `Enum.map/2` do zastosowania funkcji do każdego elementu w liście.

   ```elixir
   list = [1, 2, 3, 4]
   squared_list = Enum.map(list, fn x -> x * x end)
   IO.inspect(squared_list) # Wynik: [1, 4, 9, 16]
   ```

2. **Zwracanie funkcji przez funkcje**: Funkcje wyższego rzędu mogą również zwracać inne funkcje. Jest to przydatne do dynamicznego tworzenia funkcji na podstawie określonych warunków.

   Przykład:

   ```elixir
   defmodule Math do
    def adder(n) do
        fn x -> x + n end
    end
   end

   add_five = Math.adder(5)
   IO.inspect(add_five.(10)) # Wynik: 15
   ```

3. **Kompozycja funkcji**: Funkcje wyższego rzędu umożliwiają kompozycję funkcji, gdzie łączysz proste funkcje, aby zbudować bardziej złożone. Przykład: Użycie operatora pipe `(|>)` w języku Elixir do łączenia funkcji.

   ```elixir
   defmodule StringManipulation do
    def exclaim(string) do
        string
        |> String.trim()
        |> String.capitalize()
        |> Kernel.<>("!")
    end
   end

   IO.inspect(StringManipulation.exclaim(" hello world ")) # Wynik: "Hello world!"
   ```

Podsumowując, funkcje wyższego rzędu dają następujące korzyści:

- **Wielokrotne użycie**: Abstrahując wspólne wzorce do funkcji wyższego rzędu, możesz efektywniej ponownie używać kodu.
- **Modularność**: Funkcje wyższego rzędu promują modularność, ułatwiając rozbijanie złożonych problemów na prostsze, kompozycyjne funkcje.
- **Ekspresyjność**: Pozwalają na bardziej ekspresyjny i zwięzły kod, co ułatwia jego zrozumienie i utrzymanie.

## Domknięcia i rozwijanie funkcji

### Domknięcia

**Domknięcie** (ang. **Closure**) to funkcja, która przechwytuje powiązania wolnych zmiennych w swoim kontekście leksykalnym. Oznacza to, że funkcja zachowuje dostęp do tych zmiennych, nawet gdy jest wykonywana poza ich pierwotnym zakresem.

Wikipedia:

_Domknięcie – w metodach realizacji języków programowania jest to obiekt wiążący funkcję lub referencję do funkcji oraz środowisko mające wpływ na tę funkcję w momencie jej definiowania. Środowisko przechowuje wszystkie nielokalne obiekty wykorzystywane przez funkcję. Realizacja domknięcia jest zdeterminowana przez język, jak również przez kompilator. Domknięcia występują głównie w językach funkcyjnych, w których funkcje mogą zwracać inne funkcje (tzw. funkcje wyższego rzędu), wykorzystujące zmienne utworzone lokalnie. Aby funkcje tego typu były możliwe, muszą one być typem pierwszoklasowym._

Przykład domknięcia w Elixirze:

```elixir
defmodule Example do
  def make_multiplier(factor) do
    fn (number) -> number * factor end
  end
end

multiplier_by_3 = Example.make_multiplier(3)
IO.inspect(multiplier_by_3.(10)) # Wynik: 30
```

W tym przykładzie, `make_multiplier/1` zwraca funkcję, która mnoży swój argument przez podany czynnik w momencie wywołania `make_multiplier/1`. Zwrócona funkcja jest zamknięciem, ponieważ przechwytuje zmienną factor ze swojego zakresu leksykalnego.

### Rozwijanie funkcji

**Rozwijanie funkcji** (ang. **Currying**) to nic innego jak operacja polegająca na przeształceniu funkcji przyjmującej wiele argumentów w sekwencję funkcji, z których każda przyjmuje pojedyńczy argument.

Mimo, że **Elixir** nie obsługuje **currying'u** natywnie, można osiągnąć podobne zachowanie za pomocą domknięć i częściowej aplikacji:

```elixir
defmodule Example do
  def add(a) do
    fn (b) -> a + b end
  end
end

add_five = Example.add(5)
IO.inspect(add_five.(10)) # Wynik: 15
```

W tym przykładzie, `add/1` zwraca funkcję, która przyjmuje kolejny argument i dodaje go do `a`. Jest to forma 'u', gdzie funkcja `add/1` jest częściowo zastosowana z pierwszym argumentem, a zwrócona funkcja przyjmuje drugi argument.

Natywny przykład currying'u można przedstawić z pomocą języka **Haskell**, od którego nazwiska się tak naprawdę wziął ów **Currying** (**Haskell Curry**):

```haskell
-- Definicja funkcji, która dodaje dwie liczby
add :: Int -> Int -> Int
add x y = x + y

-- Użycie currying
addFive :: Int -> Int
addFive = add 5

main :: IO ()
main = do
  print (addFive 10)  -- Wynik: 15
```

W tym przykładzie, `add` jest funkcją, która przyjmuje dwa argumenty typu `Int` i zwraca ich sumę. Dzięki currying, możemy utworzyć nową funkcję `addFive`, która jest częściowo zastosowaną wersją funkcji `add` z pierwszym argumentem ustawionym na `5`. Następnie możemy użyć `addFive` do dodania `5` do dowolnej liczby.

### Częściowa aplikacja

**Częściowa aplikacja** (ang. **partial application**) jest ściśle związana z **_"kurryfikacją"_**. Polega na ustaleniu kilku argumentów funkcji i zwróceniu nowej funkcji, która przyjmuje pozostałe argumenty:

```elixir
defmodule Example do
  def add(a, b) do
    a + b
  end

  def partially_apply_add(a) do
    fn (b) -> add(a, b) end
  end
end

add_five = Example.partially_apply_add(5)
IO.inspect(add_five.(10)) # Wynik: 15
```

W tym przykładzie, `partially_apply_add/1` ustala pierwszy argument funkcji `add/2` i zwraca nową funkcję, która przyjmuje drugi argument.

Podsumowując:

- **Domknięcia**: Funkcje, które przechwytują i zachowują dostęp do zmiennych z ich definiującego zakresu.
- **Currying**: Przekształcanie funkcji z wieloma argumentami w sekwencję funkcji, z których każda przyjmuje pojedynczy argument.
- **Częściowa** aplikacja: Ustalanie niektórych argumentów funkcji i zwracanie nowej funkcji, która przyjmuje pozostałe argumenty.

## Wartościowanie

W programowaniu funkcjonalnym, wartościowanie odnosi się do sposobu, w jaki wyrażenia są oceniane i obliczane. Istnieją dwa główne podejścia do wartościowania:

- **Wartościowanie leniwe (ang. lazy evaluation)**: Wyrażenia są oceniane tylko wtedy, gdy ich wartości są potrzebne.
  Pozwala to na definiowanie nieskończonych struktur danych, takich jak listy, ponieważ elementy są obliczane na żądanie.
  Przykład: Haskell używa leniwego wartościowania domyślnie.
- **Wartościowanie natychmiastowe (ang. eager evaluation)**: Wyrażenia są oceniane natychmiast po ich zdefiniowaniu.
  Może prowadzić do większego zużycia pamięci, ponieważ wszystkie wartości są obliczane od razu.
  Przykład: Elixir i większość innych języków programowania używa natychmiastowego wartościowania.

  ```haskell
  -- Definicja nieskończonej listy liczb naturalnych
  naturals :: [Int]
  naturals = [0..]

  -- Pobranie pierwszych 10 elementów z nieskończonej listy
  main :: IO ()
  main = print (take 10 naturals)  -- Wynik: [0,1,2,3,4,5,6,7,8,9]
  ```

W powyższym przykładzie, lista `naturals` jest nieskończona, ale dzięki leniwemu wartościowaniu, Haskell oblicza tylko te elementy, które są potrzebne do wykonania funkcji `take 10`.

## Podsumowanie

Jak widać, nie taki diabeł straszny jak go malują.

O ile programowanie funkcjonalne na pierwszy moment może wydawać się z goła odmienne od programowania obiektowego ze względu na choćby niemutowalność czy świadomość, że nie mamy tu obiektów ze stanami jak w przypadku programowania obiektowego. Czy to, że po prostu wszystko jest funkcją. I fakt, że zamiast zwykłych pętli `for` i `while`, mamy funkcje wyższego rzędu lub rekurencję.

Zyskujemy jednak zatem na bardziej modularnym i wymiennym kodzie, który łatwiej debug'ować.

Plusem przemawiającym za językami funkcjonalnymi jest też bardziej zwięzła składnia...

![trollf](/img/trollllllf.jpeg)

... Chyba, że zapragniemy zwiedzić odmęty piekielnych czeluści Haskella. Gdzie z uśmiechem diabła czekać na Nas będą **functory** i **monady** oraz szalenie zawiłe mechanizmy dodawania bibliotek i innych aspektów przygotowywania produkcyjnego kodu napisanego w Haskell'u. Choć i tu **Cabal** przychodzi niejako z pomocą.

Niemniej jednak mam nadzieję, że na moim kolejnym etapie drogi przez świat programowania - Elixir mnie nie zawiedzie.
Oraz, że wraz z nauką tego języka jak i całego jego ekosystem'u, nie przyprawi mnie o depresję.

Gdy jednak już jej na serio zapragnę, po prostu otworzę książkę Haskell'em.

## Żródła

Lista źródeł:

- [Wikipedia](https://wikipedia.org)
- [Learn Haskell for Great Good](https://learnyouahaskell.github.io/)
- [From Ruby to Elixir](https://pragprog.com/titles/sbelixir/from-ruby-to-elixir/)
- [Learn Functional Programming with Elixir](https://www.oreilly.com/library/view/learn-functional-programming/9781680505757/)
- [Programming Elixir](https://pragprog.com/titles/elixir16/programming-elixir-1-6/)
