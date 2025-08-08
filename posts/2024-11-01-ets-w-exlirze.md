---
layout: post
title: ETS w Elixirze
date: 2024-11-01
category:
  ["elixir", "programowanie", "funkcjonalne", "funkcyjne", "fp", "erlang"]
---

![header](/img/exrt_015.jpeg)

- [Wstęp](#wstęp)
  - [Tworzenie tabeli](#tworzenie-tabeli)
  - [Definicja tabeli](#definicja-tabeli)
  - [Insert danych](#insert-danych)
  - [Odczyt danych](#odczyt-danych)
  - [Aktualizacja danych](#aktualizacja-danych)
  - [Usuwanie danych](#usuwanie-danych)
  - [Usuwanie tabel](#usuwanie-tabel)
- [Alternatywne podejście](#alternatywne-podejście)
- [Podsumowanie](#podsumowanie)
- [Referencje](#referencje)

## Wstęp

Ten artykulik będzie nieco krótszy niżeli pozostałe, a to z tego względu, że w teorii nad ETS'em w Elixirze nie ma nadmiernie co się rozpisywać.

W Elixirze, ETS (`:ets`) to biblioteka, której pełna nazwa brzmi `Erlang Term Storage` i służy ona jako pewna forma wbudowanego w język cache'u (naprawdę wydajnego cache'u).

W `:ets` można tworzyć tabele, które potrafią przechowywać spore ilości danych i umożliwiać ich procesowanie.

### Tworzenie tabeli

Tabele w ETS tworzymy z pomocą `:ets.new/2`, która wymaga nazwy (`:atom`) i listy opcji definiujących właściwości tabeli.

W Naszym przypadku dodatkowo użyjemy `:named_table`:

```elixir
table = :ets.new(:table, [:set, :protected, :named_table])
```

### Definicja tabeli

Możliwe parametry konfiguracji wyglądają tak:

- `:set` Domyślne ustawienie, gdzie każdy klucz jest unikalny.
- `:ordered_set` Jak powyższe, ale elementy są posortowane.
- `:bag` Zezwolenie na duplikaty kluczy, ale z innymi wartościami.
- `:duplicate_bag` Możliwość zduplikowanych kluczy i wartości.
- `:public` Każdy proces może wykonywać odczyt i zapis.
- `:protected` Każdy proces może czytać, tylko właściciel może zapisywać.
- `:private` Tylko proces, który jest włascicielem, może dokonywać odczyt/zapis.
- `:named_table` Dzięki tej opcji zyskujemy dostęp to tabeli po jej nazwie, zamiast referencji (zmiennej).

### Insert danych

Insert wykonujemy z pomocą funkcji `:ets.insert/2`, w której przekazujemy referencje do tabeli oraz `krotkę` (ang. `tuple`), która oczywiście reprezentuje **rekord**.

Insert powinien Nam zwrócić `true`:

```elixir
:ets.insert(:table, {:key1, "value1"})
```

### Odczyt danych

By odczytać danych używamy `:ets.lookup/2`, gdzie wskazujemy tabelę oraz klucz:

```elixir
:ets.lookup(:table, :key1)
```

Zwrotnie powinnyśmy otrzymać wynik: `[key1: "value1"]`.

### Aktualizacja danych

ETS nie posiada funkcji `update` per se. Tak więc by zaktualizować dane, musimy wykonać `insert` istniejącego już klucza:

```elixir
:ets.insert(:table, {:key1, "new_val"})
```

```elixir
:ets.lookup(:table, :key1)
```

Wartość oczywiście powinna teraz nosić nazwę `new_val`.

### Usuwanie danych

Dane usuwamy z pomocą `:ets.delete/2`, gdzie wskazujemy tożsamo jak w poprzednich przykładach tabelę oraz klucz:

```elixir
:ets.delete(:table, :key1)
```

```elixir
:ets.lookup(:table, :key1)
```

### Usuwanie tabel

Całe tabele usuwamy z pomocą także `delete`, ale z arnością `/1`:

```elixir
:ets.delete(:table)
```

## Alternatywne podejście

Jak już wspomniałem tabele możemy tworzyć w taki sposób, że odwołujemy się do nich `:atom`'em, ale możemy też to zrobić przy pomocy `zmiennej`:

```elixir
my_table = :ets.new(:table, [:set, :public])
```

```elixir
:ets.insert(my_table, {:foo, "bar"})
```

```elixir
:ets.lookup(my_table, :foo)
```

Tu wedle uznania, obydwie formy są poprawne (choć ja preferuję tabele imienne).

## Podsumowanie

ETS jest idealnym narzędziem dla scenariuszy, w których potrzebujemy procesować duże wolumeny danych w cache'u na potrzeby Naszej aplikacji lub wykonywać tak zwany `state management`.

Oczywiście należy pamiętać, że jest to procesowanie w pamięci i w razie potrzeby należy sięgnąć po `:dts`'a.

Należy także mieć na uwadze, że ETS ograniczony jest pamięcią samej maszyny wirtualnej `BEAM VM`, oraz, że tabele nie są synchronizowane między procesami, więc sami musimy zadbać o poprawną obsługę danych.

## Referencje

- [Elixir v17.2](https://hexdocs.pm/elixir/1.17.2/Kernel.html)
