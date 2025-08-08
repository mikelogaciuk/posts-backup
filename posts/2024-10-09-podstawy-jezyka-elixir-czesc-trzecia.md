---
layout: post
title: Podstawy języka Elixir (część trzecia)
date: 2024-10-09
category:
  ["elixir", "programowanie", "funkcjonalne", "funkcyjne", "fp", "erlang"]
---

![header](/img/elixirc_013.jpeg)

## Spis treści

- [Spis treści](#spis-treści)
- [Procesy](#procesy)
  - [Tworzenie procesów](#tworzenie-procesów)
  - [Przekazywanie wiadomości](#przekazywanie-wiadomości)
  - [Inny przykład](#inny-przykład)
  - [Prosty producent/konsumer](#prosty-producentkonsumer)
  - [Procesy zlinkowane](#procesy-zlinkowane)
  - [Task](#task)
  - [Agent](#agent)
- [Podsumowanie](#podsumowanie)
- [Referencje](#referencje)

## Procesy

Cały kod `Elixira` działa wewnątrz procesów, które są od siebie odizolowane i działają współbieżnie tudzież równolegle, a komunikują się poprzez przekazywanie wiadomości.

Procesy te nie mogą być mylone z procesami w systemie operacyjnym.

Te są niesamowicie lekkie w rozumieniu zużycia procesora (ang. `CPU`) jak i pamięci (nawet w porównaniu do wątków w innych językach programowania).

Sprawdźmy więc jak wyglądają podstawowe konstrukty w tym obszarze (tworzenia procesów) oraz jak wygląda przekazywanie wiadomości między nimi.

### Tworzenie procesów

Wcześniej stworzyłem funkcję do generowania ID na potrzeby zamówień `Order.Helpers.generate_id/1`:

```elixir
Order.Helpers.generate_id
```

Aby uruchomić ją jako proces, wystarczy użyć polecenia `spawn`, które zwraca `PID` procesu:

```elixir
gen_id_pid = spawn(Order.Helpers, :generate_id, [])
```

Aby sprawdzić czy proces jeszcze żyje możemy sprawdzić z pomocą:

```elixir
Process.alive?(gen_id_pid)
```

Aby sprawdzić PID aktualnego procesu, używamy `self/0`:

```elixir
self()
```

Możemy też `zespawnować` proces do zwykłej anonimowej funkcji:

```elixir
fnxp = fn -> IO.inspect("I am the lucky phrase #{Order.Helpers.generate_id()}") end
fnxp_process = spawn(fnxp)
```

```elixir
Process.alive?(fnxp_process)
```

### Przekazywanie wiadomości

Wiadomości w przypadku procesów wysyłamy z pomocą `send/2`, a odbieramy przy pomocy `receive/1`:

```elixir
parent_pid = self()
```

```elixir
spawn(fn -> send(parent_pid, {:hi, self()}) end)
```

```elixir
receive do
  {:hi, pid} -> "Got a #{:hi} from #{inspect pid}"
end
```

### Inny przykład

### Prosty producent/konsumer

Innym ciekawym przykładem może być prosty `producent/konsumer` (ang. `pub/sub`), używający czystego Elixira:

```elixir
defmodule Producer do
  def start_link do
    spawn_link(fn -> loop() end)
  end
  defp loop do
    receive do
      {:produce, consumer_pid} ->
        produced_item = :rand.uniform(1000)
        send(consumer_pid, {:produced_item, produced_item})
        loop()
    end
  end
end

defmodule Consumer do
  def start_link do
    spawn_link(fn -> loop() end)
  end
  defp loop do
    receive do
      {:produced_item, item} ->
        IO.puts("Consumed item: #{item}")
        loop()
    end
  end
end
```

```elixir
defmodule ProducerConsumerExample do
  def run do
    consumer_pid = Consumer.start_link()
    producer_pid = Producer.start_link()

    send(producer_pid, {:produce, consumer_pid})
    send(producer_pid, {:produce, consumer_pid})
    :timer.sleep(1000)

    send(producer_pid, {:produce, consumer_pid})
    send(producer_pid, {:produce, consumer_pid})
    :timer.sleep(2000)

  end
end

ProducerConsumerExample.run()
```

### Procesy zlinkowane

Bardzo rzadko jednak, używamy procesów w takiej jak powyżej formie - no chyba, że chcemy aby coś się wykonało w tle i aby ew. błąd osiągnięty tam, nie miał jakiegokolwiek wpływu na to co robi Nasz główny kod.

Jeżeli chcemy aby błąd w jednym procesie miał wpływ na następny - powinniśmy je zlinkować z pomocą `spawn_link/1`.

### Task

Kolejnym ciekawym zagadnieniem jest istnienie `Task`'ów w Elixirze, które zamiast `spawn/1` oraz `spawn_link/1` udostępniają `Task.start/1` oraz `Task.start_link/1`, które w domyślne zamiast samego PID zwracają: `{:ok, pid}`.

To umożliwia używanie `task`'ów w tzw `supervision trees`, udostępniając dodatkowo programiście `Task.async/1` oraz `Task.await/1`.

Natomiast bardzo rzadko będziecie tak naprawdę używać funkcjonalności procesów i tasków bezpośrednio.

### Agent

Ku temu stworzono `Agents`, które jako abstrakcje w okół stanu wchodzą w skład mechanizmów `OTP`.

Dla przykładu:

```elixir
{:ok, pid} = Agent.start_link(fn -> %{} end) # {:ok, #PID<0.12.0>}
```

```elixir
Agent.update(pid, fn map -> Map.put(map, :hello, :world) end)
```

```elixir
Agent.get(pid, fn map -> Map.get(map, :hello) end)
```

Możemy też tego agenta oczywiście zatrzymać:

```elixir
Agent.stop(pid)
```

Dodatkowo możemy nadać mu nazwę z pomocą `:atom'u`:

```elixir
{:ok, store} = Agent.start_link(fn -> %{} end, name: :store) # {:ok, #PID<0.43.0>}
```

Natomiast... Nie jest to najlepsza praktyka, zwłaszcza w programach, które mogą mięć setki agentów. Po drugie atomy nie są odśmiecane, więc możemy dosyć sprawnie zapełnić pamięć mając np. miliony atomów tworzonych np. per proces.

W tym przypadku lepszym rozwiązaniem będzie użycie `GenServer` oraz `Supervisor`'a.

## Podsumowanie

To by było na tyle w tej części (krótszej niż pozostałe), kontynuacja oczywiście znajdzie się w kolejnym artykule.

Natomiast wiele ciekawych rzeczy nt. agent'ów, GenServer oraz Supervisor, znajdziecie także: [tutaj](https://hexdocs.pm/elixir/agents.html).

## Referencje

- [Elixir 1.18.0](https://hexdocs.pm/elixir/1.18.0/Kernel.html)
- [The Little Elixir & OTP Guidebook](https://www.manning.com/books/the-little-elixir-and-otp-guidebook)
- [From Ruby to Elixir](https://pragprog.com/titles/sbelixir/from-ruby-to-elixir/)
- [Learn Functional Programming with Elixir](https://www.oreilly.com/library/view/learn-functional-programming/9781680505757/)
- [Programming Elixir](https://pragprog.com/titles/elixir16/programming-elixir-1-6/)
