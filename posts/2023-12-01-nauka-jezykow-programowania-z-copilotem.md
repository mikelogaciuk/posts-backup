---
layout: post
title: Nauka języków programowania z Copilotem
date: 2023-07-07
category: ["nauka", "programowanie", "Copilot", "llm", "ai", "rust"]
---

![Header](/img/rusty1.jpeg)

- [Nauka języków programowania kiedyś i dziś](#nauka-języków-programowania-kiedyśi-dziś)
- [Czego potrzebujemy?](#czego-potrzebujemy)
- [Wstęp do nauki](#wstęp-do-nauki)
- [Zapytajmy o struktury danych w Ruście](#zapytajmy-o-struktury-danych-w-ruście)
- [Uczymy się sortować w Ruście](#uczymy-sięsortować-w-ruście)
- [Przykład z języka Ruby](#przykład-z-języka-ruby)
- [Przykład Crystal'a](#przykład-crystala)
  - [Kilka słów o Crystalu](#kilka-słów-o-crystalu)
  - [Nauka Crystala](#nauka-crystala)
  - [Brniemy dalej](#brniemy-dalej)
  - [Generujemy testy](#generujemy-testy)
- [Podsumowanie](#podsumowanie)

## Nauka języków programowania kiedyś i dziś

W przeszłości nauka programowania oraz poznawania nowych języków programowania wymagała trochę większego zaangażowania niż obecnie. Dawniej, uczono się bezpośrednio z dokumentacji danego języka, postów na grupach mail'owych oraz dyskusyjnych czy po prostu z książek.

Z czasem obszar ten ewoluował do niezliczonych blogów programistycznych, a jak grzyby po deszczu zaczęły pojawiać się interaktywne strony z kursami (darmowymi jak i płatnymi) czy setki godzin nagrać wideo np. na YouTube'ie.

Nauka pierwszego języka bywa bolesna, gdyż musimy zrozumieć np. czym jest stos, a czym sterta. Czym jest bit, a czym bajt. Czym są funkcje, obiekty, zmienne, klasy, struktury, tabele, tablice, słowniki, wskaźniki i inne aspekty programowania.

Obecnie w dobie błyskawicznie rozwijających się modeli LLM, jesteśmy w stanie za prawie darmo, uczyć się bezpośrednio w edytorze kodu. Cena Copilot’a w porównaniu do stosu książek czy kursów zwyczajnie niska.

> Oczywiście musimy mieć na uwadze, że i modele mogą się mylić. Trzeba do przykładów podchodzić z głową i rozwagą.
> Z reguły kod może nie uwzględniać pewnych aspektów security, ale za to jest mało prawdopodobnym, że będzie to brutalny spaghetti-code.

Kilka dni przed napisaniem tego artykułu, znalazłem zeszyt z czasów szkolnych ze swoimi notatkami. Przeglądając go, pomyślałem sobie jak wyglądać by mogła dziś moja nauka programowania, czy poznawania nowego języka mając możliwość korzystania z Copilot'a.

Oczywiście zakładając, że jednak przeczytaliśmy chociaż jakikolwiek wstęp do **Computer Science** oraz umiemy programować w jakimkolwiek innym języku np. **Pythonie** - sprawdźmy jak taka nauka może wyglądać na przykładzie **Rusta**, ale i **Ruby** oraz **Crystala**.

## Czego potrzebujemy?

By móc uczyć się w sposób zaproponowany w moim artykule, na pewno potrzebujemy **Rusta**, **Ruby** oraz **Crystala** , wraz z **Visual Studio Code** i pluginami: **Rust Analyzer**, **Ruby** od Shopify wraz LSP oraz plugin do **Crystala** z LSP (**Crystaline**) oraz **Github Copilot** i aktywną subskrypcję usługi Github Copilot.

Mając spełnione wymagania, w VSCode zauważymy nową ikonę przypominającą czat. I to w dużej mierze z tego miejsca, poza oczywiście kodem będzie korzystać najwięcej.

![Starting point](/img/rusty2.jpeg)

## Wstęp do nauki

Na początek w IDE tworzymy folder, po czym w konsoli wpisujemy:

```bash
cargo init --name rustypusty
```

Czy jakąkolwiek inną nazwę dla Naszego 'naukowego projektu'.

Po tym zabiegu Nasz folder powinien wyglądać tak:

```shell
.
├── Cargo.lock
├── Cargo.toml
├── src
│   └── main.rs
└── target

3 directories, 3 files
```

Z racji, że udajemy, że nie wiemy czym jest Rust - powinniśmy pierw zapytać Naszego **Pilota** czym jest w ogóle ten język Rust?

_Czym jest język Rust?_

> Rust to nowoczesny język programowania skoncentrowany na wydajności i bezpieczeństwie, szczególnie bezpiecznym równoległym programowaniu.
> Rust oferuje kontrolę na niskim poziomie nad systemem w sposób podobny do języka C++, ale z dodatkowymi narzędziami i ochroną przed błędami, które pomagają uniknąć powszechnych problemów związanych z pamięcią, takich jak wyścigi danych, wycieki pamięci i niezdefiniowane zachowanie.

_Czym jest Cargo w języku Rust?_

> Cargo to system zarządzania pakietami i narzędziem do budowania projektów w języku Rust.
> Umożliwia tworzenie nowych projektów, zarządzanie zależnościami, kompilację kodu, uruchamianie testów i wiele innych zadań związanych z rozwojem oprogramowania w Rust.
>
> Komenda `cargo init --name rustypusty` tworzy nowy projekt Rust o nazwie "rustypusty".
> Tworzy ona katalog o tej nazwie, jeśli jeszcze nie istnieje, a w nim pliki konfiguracyjne i źródłowe potrzebne do rozpoczęcia pracy nad projektem.

_Jak można zarządzać zależnościami w projekcie Rust przy użyciu Cargo?_

> Zarządzanie zależnościami w projekcie Rust za pomocą Cargo jest proste i odbywa się głównie za pomocą pliku `Cargo.toml`, który jest plikiem konfiguracyjnym dla twojego projektu.
>
> Aby dodać zależność, musisz dodać jej nazwę i wersję do sekcji `[dependencies]` w pliku `Cargo.toml`.
> Na przykład, aby dodać zależność od biblioteki `serde`, która jest używana do serializacji i deserializacji danych, możesz zrobić to w następujący sposób:

```toml
[dependencies]
serde = "1.0"
```

> Następnie, kiedy uruchomisz `cargo build` lub `cargo run`, Cargo automatycznie pobierze i skompiluje zależności, które są wymagane przez twój projekt.
>
> Możesz również zaktualizować zależności za pomocą `cargo update`, co spowoduje, że Cargo sprawdzi, czy istnieją nowsze wersje zależności, i jeśli tak, zaktualizuje plik `Cargo.lock`, który śledzi dokładne wersje zależności używane w twoim projekcie.

Jak widać model całkiem sprawnie i jasno odpowiada na Nasze pytania. Gdzie jak zresztą wiemy, ten nie przejmuje się tym, aby 'książka' była na tyle długa by spełnić wymogi wydawcy.
Tym samym odpowiedzi są zwięzłe, precyzyjne i nie odbiegają od omawianego zagadnienia.

## Zapytajmy o struktury danych w Ruście

Na początek zadajmy mu pytanie o typy i struktury danych:

_Jakie typy danych istnieją w języku Rust?_

> W języku Rust dostępne są różne typy danych. Oto kilka z nich:
>
> 1. **Liczby całkowite**: `i8`, `i16`, `i32`, `i64`, `i128`, `u8`, `u16`, `u32`, `u64`, `u128` - są to odpowiednio liczby całkowite ze znakiem i bez znaku o różnej wielkości.
>
> 2. **Liczby zmiennoprzecinkowe**: `f32`, `f64` - są to liczby zmiennoprzecinkowe o pojedynczej i podwójnej precyzji.
>
> 3. **Bool**: `bool` - typ logiczny, który może przyjmować wartości `true` lub `false`.
>
> 4. **Znaki**: `char` - typ znaku, który przechowuje pojedynczy znak Unicode.
>
> 5. **Łańcuchy znaków**: `String` i `&str` - są to typy używane do przechowywania łańcuchów znaków.
>
> 6. **Tablice**: `[T; N]` - tablica o stałej długości `N` i typie elementów `T`.
>
> 7. **Wektory**: `Vec<T>` - dynamiczna tablica o typie elementów `T`.
>
> 8. **Krotki**: `(T1, T2, ..., Tn)` - krotka o różnych typach elementów.
>
> 9. **Wskaźniki**: `Box<T>`, `&T`, `&mut T` - różne typy wskaźników, w tym wskaźniki do pudełka (Box), referencje i referencje mutowalne.
>
> 10. **Funkcje**: `fn(T1, T2, ..., Tn) -> T` - typ funkcji, która przyjmuje argumenty typów `T1` do `Tn` i zwraca wartość typu `T`.
>
> 11. **Option**: `Option<T>` - typ, który może przyjmować wartość `Some(T)` lub `None`.
>
> 12. **Result**: `Result<T, E>` - typ, który może przyjmować wartość `Ok(T)` lub `Err(E)`.
>
> 13. **Struktury i Enumy**: Użytkownik może zdefiniować własne typy danych za pomocą słów kluczowych `struct` i `enum`.

_Jakie struktury danych możemy znaleźć w języku Rust?_

> W języku Rust dostępne są różne struktury danych. Oto kilka z nich:
>
> 1. **Tuples (Krotki)**: Krotki to sekwencje wartości różnych typów. Na przykład, `(1, "a", 4.5)` jest krotką zawierającą wartość całkowitą, łańcuch znaków i liczbę zmiennoprzecinkową.
>
> 2. **Arrays (Tablice)**: Tablice to sekwencje wartości tego samego typu o stałej długości. Na przykład, `[1, 2, 3, 4, 5]` jest tablicą pięciu liczb całkowitych.
>
> 3. **Vectors (Wektory)**: Wektory są podobne do tablic, ale mogą zmieniać swoją wielkość. Są one jednym z najczęściej używanych typów sekwencyjnych w Rust.
>
> 4. **Structs (Struktury)**: Struktury są niemutowalnym zbiorem pól, które mogą zawierać różne typy danych. Są one podobne do klas w innych językach programowania, ale bez metod.
>
> 5. **HashMaps (Mapy hashujące)**: HashMapy są kolekcjami klucz-wartość, które przechowują dane w sposób, który umożliwia szybkie wyszukiwanie na podstawie klucza.
>
> 6. **Sets (Zbiory)**: Zbiory są kolekcjami unikalnych elementów. Rust oferuje dwa typy zbiorów: `HashSet` i `BTreeSet`.
>
> 7. **LinkedLists (Listy dwukierunkowe)**: Listy dwukierunkowe to sekwencje wartości, które są przechowywane w węzłach z linkami do następnego i poprzedniego węzła.
>
> 8. **Stacks and Queues (Stosy i kolejki)**: Stosy i kolejki to specjalne typy kolekcji, które oferują operacje dodawania i usuwania elementów w określonym porządku.

Jak widać bezproblemowo otrzymaliśmy odpowiedź opisującą typy i struktury danych.

## Uczymy się sortować w Ruście

Zakładając, że mamy nieposortowany **vector** i chcemy go posortować, zaczynamy od pisania własnej funkcji sortujacej, ale nie pamiętamy definicji **sortowania bąbelkowego**, tak więc pytamy Copilota i otrzymujemy:

> Sortowanie bąbelkowe to prosty algorytm sortujący, który wielokrotnie przechodzi przez listę, porównuje sąsiednie elementy i zamienia je, jeśli są w złej kolejności. Proces ten jest powtarzany, aż do momentu, gdy nie są już potrzebne żadne zamiany, co oznacza, że lista jest posortowana.
>
> Pseudokod dla sortowania bąbelkowego może wyglądać tak:

```rust
dla i od 0 do N-1
  dla j od 0 do N-i-1
    jeśli lista[j] > lista[j+1]
      zamień lista[j] i lista[j+1]
```

> Gdzie `N` to liczba elementów na liście. W każdej iteracji zewnętrznej pętli (i), największy element "bąbelkuje" do końca listy. > Wewnętrzna pętla (j) przechodzi przez listę od początku do końca, porównując i zamieniając sąsiednie elementy, jeśli są w złej kolejności.

Mając powyższą informację tworzymy **vector**:

```rust
let arr: Vec<i32> = vec![5555, 1, 23, 512, 4, 66, 89, 999];
```

Następnie potrzebujemy funkcji, która przyjmie nieposortowaną tablicę jako argument i zwróci Nam poprawny wynik, po chwili pisania otrzymujemy poniższe:

```rust
fn srt(arr: Vec<i32>) {
    for i in arr.len() - 1 {
        for j in 0..arr.len() - i - 1 {
            if arr[j] > arr[j + 1] {
                let temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}
```

Niby napisaliśmy, ale Rust podświetla cały kod na czerwono niczym pulpit kierowcy w popsutym aucie:

> rustc
> no method named `len` found for type `i32` in the current scope
> method not found in `i32`rustcClick for full compiler diagnostic

Zaznaczamy cały kod, klikamy prawym klawiszem na `Copilot -> Wyjaśnij to` i po lewej w oknie ukazuje Nam się odpowiedź:

> Wygląda na to, że próbujesz użyć metody `len` na typie `i32`, który jest typem liczby całkowitej w Rust. Metoda `len` jest dostępna dla typów kolekcji, takich jak wektory i ciągi znaków, ale nie dla typów liczbowych.
>
> Ponadto, próbujesz zmienić elementy wektora `arr` w miejscu, ale `arr` jest niemutowalny. Musisz zadeklarować `arr` jako mutowalny, używając słowa kluczowego `mut`.
>
> Oto poprawiona wersja twojego kodu:

```rust
fn srt(mut arr: Vec<i32>) {
    let len = arr.len();
    for i in 0..len {
        for j in 0..len - i - 1 {
            if arr[j] > arr[j + 1] {
                arr.swap(j, j + 1);
            }
        }
    }
}
```

> W tym kodzie, używamy metody `len` na wektorze `arr` do pobrania jego długości, a następnie używamy tej długości do iteracji przez elementy wektora. Używamy również metody `swap` do zamiany elementów wektora.

Fakt! O tym zapomnieliśmy, że Rust w domyśle wszystkie wartości ma niemutowalne, a gdy chcemy je zmienić to albo podczas deklaracji używamy keywordu **mut**.

Spróbujmy przypisać wynik posortowanego vectora do zmiennej:

```rust
fn main() {
    let arr: Vec<i32> = vec![5555, 1, 23, 512, 4, 66, 89, 999];
    let sorted_arr: Vec<i32> = srt(arr);

    println!("{:?}", sorted_arr);
}
```

No, ale nie działa, bo chyba zapomnieliśmy zwrócić tablicę? Woops, przecież aktualizujemy oryginalną tablicę, tym samym w teorii powinniśmy zrobić to tak:

```rust
    print!("Vector oryginalny: {:?}", arr);

    print!("Vector posortowany: {:?}", srt(arr));
```

Jednakże posortowany vector jest pusty, pytamy Copilota co zrobiliśmy źle:

> Problem polega na tym, że funkcja `srt` nie zwraca posortowanego wektora. W Rust, jeśli nie ma wyrażenia `return` ani średnika na końcu bloku funkcji, zwracany jest ostatni wyrażenie. W twoim przypadku, funkcja `srt` nie zwraca nic, ponieważ blok funkcji kończy się pętlą `for`, a nie wyrażeniem.
>
> Aby naprawić ten problem, musisz dodać `arr` na końcu funkcji `srt`, aby zwrócić posortowany wektor. Oto poprawiony kod:

```rust
fn main() {
    let arr: Vec<i32> = vec![5555, 1, 23, 512, 4, 66, 89, 999];

    println!("Vector oryginalny: {:?}", arr);

    println!("Vector posortowany: {:?}", srt(arr));
}

fn srt(mut arr: Vec<i32>) -> Vec<i32> {
    let len = arr.len();
    for i in 0..len {
        for j in 0..len - i - 1 {
            if arr[j] > arr[j + 1] {
                arr.swap(j, j + 1);
            }
        }
    }
    arr
}
```

> Zauważ, że dodałem również typ zwracany `-> Vec<i32>` do definicji funkcji `srt`, co jest dobrym zwyczajem w Rust, aby jasno określić, co funkcja zwraca.

No i pięknie, teraz otrzymaliśmy to co chcieliśmy:

```shell
Vector oryginalny: [5555, 1, 23, 512, 4, 66, 89, 999]
Vector posortowany: [1, 4, 23, 66, 89, 512, 999, 5555]
```

Owszem, przykład jest trywialny, ale wymyślenie scenariusza, który ukaże możliwości nauki jak i pracy z chatem, chwilę mi zajął. Wszak zajmuję się eliminowaniem i wykrywaniem błędów, a nie ich wymyślaniem.

W tym przypadku, Nasz sztuczny przyjaciel kodu poradził sobie świetnie. Poprawił kod, wspomniał o dobrych praktykach, wytłumaczył algorytm.

## Przykład z języka Ruby

Powiedzmy, że mamy proste API napisane w Sinatrze i chcemy, aby API na podstawie otrzymanego komunikatu, wysyłało Nam alert do innego zainteresowanych.

Jesteśmy leniwi, nie chce Nam się przeglądać RubyGems w poszukiwaniu biblioteki, więc zadajemy pytanie Copilotowi:

_Jakiej biblioteki Ruby byś użył w frameworku Sinatra aby wysłać przeparsowany alert w postaci maila do określonej listy zainteresowanych? Zależy mi tylko na bibliotece do wysyłania maili._

> Jeśli używasz Sinatry i szukasz lżejszej biblioteki do wysyłania e-maili, to polecam bibliotekę `mail`. Jest to bardzo popularna i wszechstronna biblioteka do obsługi e-maili w Ruby.
>
> Poniżej znajduje się przykładowy kod, który pokazuje, jak można użyć biblioteki `mail` do wysyłania e-maili.

```ruby
require 'mail'

options = {
  :address              => "smtp.gmail.com",
  :port                 => 587,
  :domain               => 'your.host.name',
  :user_name            => '<username>',
  :password             => '<password>',
  :authentication       => 'plain',
  :enable_starttls_auto => true
}

Mail.defaults do
  delivery_method :smtp, options
end

Mail.deliver do
  to 'mikel@test.lindsaar.net'
  from 'sender@test.lindsaar.net'
  subject 'Here is the image you wanted'
  body File.read('body.txt')
  add_file :filename => 'somefile.png', :content => File.read('/somefile.png')
end
```

> Pamiętaj, że musisz dostosować opcje SMTP do swojego dostawcy poczty e-mail. Powyższy przykład pokazuje konfigurację dla Gmaila.

Perfekcyjnie! Nie muszę przeszukiwać **gem'ów** ani tematów na StackOverflow, ani książek ani dokumentacji języka.

Wynik dostaję bezpośrednio w edytorze, używam kodu, który otrzymałem stosując odpowiednie zmiany osadzając dane wrażliwe w zmiennych i opracowuję parser.

## Przykład Crystal'a

### Kilka słów o Crystalu

Crystal to język programowania, który ma na celu połączenie wydajności języka C z wygodą składni Ruby.

Język ten jest statycznie typowany, co oznacza, że typy są sprawdzane w czasie kompilacji, a nie w czasie wykonania, co przyczynia się do zwiększenia wydajności. Fakt, że Crystal jest kompilowany do kodu maszynowego, czyni go znacznie szybszym od Ruby.

Język ten ma wiele cech, które są podobne do Ruby:

- Składnia: Crystal ma składnię bardzo podobną do Ruby i generalnie 90% czystego kodu Ruby będzie powinno działać w Crystalu.
- Obiektowość: Wszystko w Crystal jest obiektem, podobnie jak w Ruby.
- Wyjątki: Crystal obsługuje wyjątki i ma podobny mechanizm obsługi błędów jak Ruby.

Fani Crystala zwykli mówić:

> Piękny jak Ruby, szybki jak C.

### Nauka Crystala

Przypuśćmy, że nieco przespaliśmy lekcje informatyki, a i Nasze lenistwo uniemożliwia Nam sprawdzenie oficjalnej dokumentacji.

Widzimy w edytorze kodu, że linter podpowiada Nam nie tylko tworzenie klas, ale i struktur.

Zadajemy pytanie do Copilot'a celem sprawdzenia, czym się one różnią:

_Wytłumacz mi różnicę między 'class', a 'struct' w języku Crystal._

> W języku Crystal, `class` i `struct` są dwoma różnymi typami definiowania typów niestandardowych, ale mają kilka istotnych różnic.
>
> `class` w Crystal jest podobne do klasy w innych językach programowania. Jest to referencyjny typ danych, co oznacza, że kiedy przekazujesz obiekt klasy do funkcji lub metody, przekazujesz referencję do tego obiektu, a nie sam obiekt. To oznacza, że jeśli zmienisz obiekt w funkcji, zmiana ta będzie widoczna poza funkcją.

```crystal
class MyClass
  property :value

  def initialize(@value : Int32)
  end
end

def modify(obj : MyClass)
  obj.value = 42
end

obj = MyClass.new(0)
modify(obj)
puts obj.value # Wypisze "42", ponieważ obiekt został zmieniony w funkcji
```

> Z drugiej strony, `struct` w Crystal jest typem wartościowym. Kiedy przekazujesz obiekt `struct` do funkcji, przekazujesz kopię tego obiektu. To oznacza, że jeśli zmienisz obiekt w funkcji, zmiana ta nie będzie widoczna poza funkcją.

```crystal
struct MyStruct
  property :value

  def initialize(@value : Int32)
  end
end

def modify(obj : MyStruct)
  obj.value = 42
end

obj = MyStruct.new(0)
modify(obj)
puts obj.value # Wypisze "0", ponieważ obiekt nie został zmieniony w funkcji
```

> Inną różnicą jest to, że `struct` nie może być dziedziczony, podczas gdy `class` może.

No i pięknie! Kolejny raz dostajemy bardzo dobre wytłumaczenie do pytania zadanego w sprawie konkretnego języka.

Języka, który na tą chwilę ma może z trzy oficjalne książki ([jedną](https://helion.pl/ksiazki/crystal-programming-a-project-based-introduction-to-building-efficient-safe-and-readable-web-and-george-dietrich-guilherme-bernal,e_2t79.htm#format/e) kupiłem i polecam!).

### Brniemy dalej

No dobrze, ale może jednak nie przespaliśmy tak do końca informatyki i mamy wrażenie, że za faktem rozdzielenia możliwości tworzenia obiektów przy pomocy struktur i klas, stoi uzasadnienie w kwestii optymalizacji pamięci?

Zadajemy kolejne pytanie, dot. języka który mimo wersji 1.11, nadal jest bardzo młodym językiem:

_Czy w Crystalu, obiekty zdefiniowane z pomocą 'struct' trafiają na stos, a te z 'class' na stertę?_

> Tak, to jest jedna z kluczowych różnic między `struct` a `class` w Crystal.
>
> Obiekty `struct` są przechowywane na stosie, co oznacza, że są one zazwyczaj szybsze do tworzenia i niszczenia, ale mają ograniczoną wielkość. Ponieważ są przechowywane na stosie, obiekty `struct` są przekazywane przez wartość, co oznacza, że kiedy przekazujesz `struct` do funkcji, tworzona jest jego kopia.
>
> Z drugiej strony, obiekty `class` są przechowywane na stercie. Sterta ma znacznie większą pojemność niż stos, więc obiekty `class` mogą być znacznie większe. Jednak tworzenie i niszczenie obiektów na stercie jest zazwyczaj wolniejsze. Ponieważ obiekty `class` są przechowywane na stercie, są one przekazywane przez referencję, co oznacza, że kiedy przekazujesz `class` do funkcji, przekazujesz referencję do obiektu, a nie sam obiekt.

Jak widać nawet dla tak młodego języka, Copilot miał odpowiedź. Wyobraźmy sobie teraz ile informacji jest w stanie zwrócić w przypadku języków takich jak C, C++, Ada, Python, Java czy JavaScript.

Kolejny raz jestem pod wrażeniem, gdyż ani na chwilę nie opuściłem edytora kodu.

### Generujemy testy

Po utworzeniu prostego projektu, dodajemy kod:

```crystal
module Store
  VERSION = "0.1.0"

  class Store
    def initialize
      @store = Hash(String, String).new
    end

    def set(key, value)
      @store[key] = value
    end

    def get(key)
      @store[key]
    end

    def delete(key)
      @store.delete(key)
    end

    def clear
      @store.clear
    end
  end
end
```

Zaznaczamy kod i pod prawym klawiszem w sekcji `Copilot` zaznaczamy `Generuj testy`.

W przypadku testów, doszło jednak do błędnego wystawienia kodu i testy nie przechodzą:

```shell
$ crystal spec

FF..EEE

Failures:

  1) Store works
     Failure/Error: false.should eq(true)

       Expected: true
            got: false

     # spec/store_spec.cr:7

  2) Store works
     Failure/Error: false.should eq(true)

       Expected: true
            got: false

     # spec/store_spec.cr:15

  3) Store #get returns nil if the key does not exist

  4) Store #delete deletes the value for a given key

  5) Store #clear clears all key-value pairs in the store
```

I to jest pierwszy raz, kiedy Copilot poległ.

Tu akurat biorę poprawkę na fakt, że jest to młody język i Copilot może mylić składnię z **rspec'em** z Ruby.

## Podsumowanie

Jak widać, choćby na podstawie tej bądź co bądź - biednej demonstracji uczenia sie i zgłębiania wiedzy oparciu o pomoc LLM'ów.

Można zaobserwować jak wiele możliwości ona daje, gdyż nie tylko jest w stanie odpowiadać na Nasze pytania, dawać przykłady kodu czy generować testy, ale także refaktoryzować go i podpowiadać dobre praktyki.

Osobiście, gdybym dziś miał jakieś 15 lat i był na początku swojej informatycznej drogi, a miał dostęp do takich możliwości jakie dają LLM'y.
Zapewne uczyłbym się w ten sposób, wspomagając się książkami i zapewne dziś był bym znacznie bardziej doświadczony niż jestem obecnie.

W młodości, niestety nie miałem możliwości pójścia na studia informatyczne, a nie miałem nikogo pod ręką, kto mógł by mi pomóc w nauce i wytłumaczyć niektóre kwestie jak prostemu człowiekowi. Próg wejścia do świata IT wyższego rzędu (development, DevOps) wydawał mi się tak duży, że po prostu się nie odważyłem wtedy uczyć samemu.

Dopiero zrządzenie losu i rozpoczęcie pracy w IT, do którego trafiłem z tzw. niskiego szczebla tj zaczynając od Helpdesk'u. Dało mi na tyle sporego motywacyjnego kopniaka, że znalazłem w sobie na tyle samozaparcia, by jednak tą wiedzę zgłębić.

Dziś jestem tylko DevOps'em, oraz Data Engineer'em i Back-end Developerem, ale kto by pomyślał, że będę tu gdzie jestem dziś?

Szczerze, zazdroszczę dzisiejszej młodzieży, która już na etapie szkoły średniej ma takie możliwości. Mieć wirtualnego asystenta, który wytłumaczy praktycznie wszystko czego potrzebujesz. Zredaguje kod, podpowie, przyspieszy pracę czy przeszuka dokumentację.

Korzystajcie z tego jak tylko się da, nie marnujcie możliwości, które Wam dano. Możecie wszystko! Praca z kodem, chyba nigdy nie była tak przyjemna jak dziś.
