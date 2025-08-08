---
layout: post
title: Notatki z Go
date: 2023-02-21
category: ["go", "golang", "notatki", "cheatsheet"]
---

![Gopher in the fire](/img/This_is_Fine_Gopher.png)

<!-- TOC -->

- [Wstęp](#wstęp)
- [Instalacja](#instalacja)
- [Inicjacja projektu](#inicjacja-projektu)
- [Biblioteka standardowa](#biblioteka-standardowa)
- [CLI](#cli)
- [Funkcja główna](#funkcja-główna)
  - [Importy](#importy)
- [Kompilacja](#kompilacja)
- [Podstawy](#podstawy)
  - [Deklaracja zmiennych](#deklaracja-zmiennych)
  - [Typy danych](#typy-danych)
  - [Konwersja typów](#konwersja-typów)
  - [Wartości zerowe](#wartości-zerowe)
  - [Printowanie wartości](#printowanie-wartości)
  - [Widzialność importów](#widzialność-importów)
- [Wskaźniki](#wskaźniki)
- [Deklarowanie typów](#deklarowanie-typów)
- [Bloki](#bloki)
  - [If/else](#ifelse)
  - [Switch](#switch)
  - [Pętle for](#pętle-for)
- [Funkcje](#funkcje)
  - [Podstawy](#podstawy-1)
  - [Rekurencja](#rekurencja)
  - [Funkcje jako zmienne](#funkcje-jako-zmienne)
  - [Domknięcia](#domknięcia)
  - [Zwracanie wielu wartości](#zwracanie-wielu-wartości)
  - [Defer](#defer)
  - [Funkcje wariadyczne](#funkcje-wariadyczne)
  - [Wskaźniki jako argumenty funkcji](#wskaźniki-jako-argumenty-funkcji)
- [Struktury](#struktury)
  - [Dostęp do wartości w obiektach](#dostęp-do-wartości-w-obiektach)
  - [Struktury i wskaźniki](#struktury-i-wskaźniki)
  - [Widoczność atrybutów](#widoczność-atrybutów)
  - [Kompozycja](#kompozycja)
  - [Tags](#tags)
- [Metody](#metody)
  - [Metody i odbiorcy wskaźników](#metody-i-odbiorcy-wskaźników)
- [Interfejsy](#interfejsy)
- [Obsługa błędów](#obsługa-błędów)

## Wstęp

`Go` lub często też zwany jako `Golang`, to jest programowania od Google rozwijany od 2007 roku.

Autorzy języka chcieli połączyć co najlepsze w C i Python'nie oraz wykorzystać zalety nowoczesnych wielowątkowych procesorów.

Go jest łatwym, kompilowanym i statycznie typowanym językiem, którego składania zawiera tylko 25 **keyword'ów**

Do wersji 1.20 mamy::

    break     default      func    interface  select
    case      defer        go      map        struct
    chan      else         goto    package    switch
    const     fallthrough  if      range      type
    continue  for          import  return     var

## Instalacja

By zainstalować Go na **Ubuntu** wystarczy, wykonać:

```shell
cd ~/Downloads
wget https://go.dev/dl/go1.20.linux-amd64.tar.gz
sudo tar -C /usr/lib/ -xzf go1.20.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc && source ~/.bashrc
```

Następnie możemy wywołać komendę `go`:

```shell
$ go

Go is a tool for managing Go source code.

Usage:

        go <command> [arguments]

The commands are:

        bug         start a bug report
       (...)
        vet         report likely mistakes in packages

Use "go help <command>" for more information about a command.

Additional help topics:

        buildconstraint build constraints
        buildmode       build modes
        c               calling between Go and C
        cache           build and test caching
        environment     environment variables
        filetype        file types
        go.mod          the go.mod file
        gopath          GOPATH environment variable
        gopath-get      legacy GOPATH go get
        goproxy         module proxy protocol
        importpath      import path syntax
        modules         modules, module versions, and more
        module-get      module-aware go get
        module-auth     module authentication using go.sum
        packages        package lists and patterns
        private         configuration for downloading non-public code
        testflag        testing flags
        testfunc        testing functions
        vcs             controlling version control with GOVCS

Use "go help <topic>" for more information about that topic.
```

## Inicjacja projektu

Projekt rozpoczynamy albo z poziomu **shell'a**:

```shell
cd repos && mkdir example && go mod init example && touch main.go
```

Albo z pomocą **IDE**, np. **Goland'a.**

## Biblioteka standardowa

Go posiada bogatą bibliotekę standardową, dzięki której w teorii bez korzystania z jakiegokolwiek framework'u - jesteśmy w stanie napisać każdą aplikację.

Dokumentacja **std** znajduje się pod tym [linkiem](https://pkg.go.dev/std).

## CLI

Jak już wspomniałem standardowe CLI dostępne jest via `go`:

```shell
go
```

The commands are:

    bug         start a bug report
    build       compile packages and dependencies
    clean       remove object files and cached files
    doc         show documentation for package or symbol
    env         print Go environment information
    fix         update packages to use new APIs
    fmt         gofmt (reformat) package sources
    generate    generate Go files by processing source
    get         add dependencies to current module and install them
    install     compile and install packages and dependencies
    list        list packages or modules
    mod         module maintenance
    work        workspace maintenance
    run         compile and run Go program
    test        test packages
    tool        run specified go tool
    version     print Go version
    vet         report likely mistakes in packages

## Funkcja główna

Główna funkcja jest deklarowana w sposób podobnych do innych języków jak **Kotlin**, **C**:

```go
package main

func main() {

}
```

### Importy

Importowanie zależności jest podobne do innych języków:

```go
// main.go
package main

import "store"

...your code
```

```go
// Package store
package store

... your code
```

## Kompilacja

Do tzw. kompilacji wraz z rozruchem możemy użyć:

```shell
go run main.go
```

Do zwyczajnej kompilacji, używamy:

```shell
go build main.go
```

W celu kompilacji do specyficznej architektury, możemy użyć:

```shell
GOOS=windows GOARCH=amd64 go build main.go -o bin/app.exe
GOOS=linux GOARCH=amd64 go build main.go -o bin/app
```

By wyświetlić wszystkie możliwe targety, należy użyć komendy:

```shell
go tool dist list
```

    aix/ppc64        freebsd/amd64   linux/mipsle   openbsd/386
    android/386      freebsd/arm     linux/ppc64    openbsd/amd64
    android/amd64    illumos/amd64   linux/ppc64le  openbsd/arm
    android/arm      js/wasm         linux/s390x    openbsd/arm64
    android/arm64    linux/386       nacl/386       plan9/386
    darwin/386       linux/amd64     nacl/amd64p32  plan9/amd64
    darwin/amd64     linux/arm       nacl/arm       plan9/arm
    darwin/arm       linux/arm64     netbsd/386     solaris/amd64
    darwin/arm64     linux/mips      netbsd/amd64   windows/386
    dragonfly/amd64  linux/mips64    netbsd/arm     windows/amd64
    freebsd/386      linux/mips64le  netbsd/arm64   windows/arm

Więcej możecie znaleźć [tu](https://opensource.com/article/22/4/go-build-options)
oraz [tu](https://www.digitalocean.com/community/tutorials/building-go-applications-for-different-operating-systems-and-architectures).

## Podstawy

### Deklaracja zmiennych

Do deklarowania zmiennych używamy `var`, natomiast dla stałych `consts`:

```go
var foo string
var bar string = "I am an initialized variable."
quaz := "I am short hard declared variable."
```

### Typy danych

W `Go` możemy odnaleźć między innymi takie typy danych:

```go
package main

//String
var thisIsTheString string = "I am a string"

// Bool
var isItTrue bool = true
var isItRobot bool = false

// Numeric types
var i int = 444                         // Platform dependent
var i8 int8 = 64                        // -128 to 127
var i16 int16 = 32767                   // -2^15 to 2^15 - 1
var i32 int32 = -2147483647             // -2^31 to 2^31 - 1
var i64 int64 = 9223372036854775807     // -2^63 to 2^63 - 1
var ui uint = 404                       // Platform dependent
var ui8 uint8 = 255                     // 0 to 255
var ui16 uint16 = 63535                 // 0 to 2^16
var ui32 uint32 = 2223483647            // 0 to 2^32
var ui64 uint64 = 9223372036854666666   // 0 to 2^64
var uiptr uintptr                       // Integer representation of a memory address

// Floats
var fl32 float32 = 1.1333
var fl64 float64 = 33.5555

// Complex
var compl128 complex128 = 20 + 2
var compl64 complex64 = 10 + 33

// Array -> [n]T
var arry [5]int // where 5 is a length
var arr [4]string = [4]string{"foo", "bar", "car"}
var arrx [5]int = [5]int{3, 6, 11, 9, 99}

// Slices -> []T
var arrx []string
var s []int = []int{0, 1, 2, 3, 4, 5}

// Maps -> map[K]V
var receipts map[string]int
invoices := make(map[string]int)
documents := map[string]float64{
	"ZS123405/500/2023/12": 5600.12,
	"INV00033/322/2022/01": 999312.87,
	"COR00231/001/2016/11": 123.93
}
```

W przypadku **integerów** zalecanym jest by używać `int`\*\* jeśli nie ma szczególnego powodu do używania konkretnej precyzji.

Typy `byte` (`uint8`) i `rune` (`int32`), są generalnie aliasami.

```go
type byte = uint8
type rune = int32
```

### Konwersja typów

Typy danych możemy konwertować w znanym z innych imperatywnych języków stylu:

```go
var inty int = 42
var f = float64(inty)
```

### Wartości zerowe

W przeciwieństwie do innych języków jak np. Python i Ruby, niezainicjowane zmienne otrzymują domyślną wartość `zerową`.

Dla liczb jest to`0`, a dla boolean'ów `false`, natomiast dla stringów `""`.

### Printowanie wartości

Do printowania używamy biblioteki `fmt`:

```go
var x string = "Hello <3"

func main () {
	fmt.Println("x")
}
```

Zmienne mozemy osadzać w tekście tak:

```go
var x string = "Mike"

func main () {
	fmt.Printf("Hello: %s", x)
}
```

Mały cheat-sheet dla mapowania:

    bool:                    %t
    int, int8 etc.:          %d
    uint, uint8 etc.:        %d, %#x if printed with %#v
    float32, complex64, etc: %g
    string:                  %s
    chan:                    %p
    pointer:                 %p

### Widzialność importów

W celu eksportu metod czy struktur albo funkcji, ta zdefiniowana musi być z pomocą dużej litery.

Wartości, metody i zmienne z tzw małej litery uznawane są za `nieeksportowalne`.

## Wskaźniki

Wskaźniki mają identyczne zastosowanie jak w C, tj jeżeli zadeklarujemy zmienną wskazującą wskaźnik innej wartości:

```go
var x int = 1
p := &x
*p = 2
fmt.Println(x)
fmt.Println(p)
fmt.Println(*p)
```

To `p` zwróci adres w pamięci dla `x`, `*p` wartość.

## Deklarowanie typów

W `Go`, możemy tworzyć właśne typy danych:

```go
// This is tempconversion package
package tempconversion

type Celsius float64
type Fahrenheit float64

const (
    AbsoluteZeroC Celsius = -273.15
    FreezingC Celsius = 0
    BoilingC Celsius = 100
)
func Fahrenheit(c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32) }
func FahrenheitToCelsius(f Fahrenheit) Celsius { return Celsius((f - 32) * 5 / 9) }
```

```go
package main

import "tempconversion"
import "fmt"

func main() {

}
```

![gonotes_header](/img/GOPHER_SHARE.png)

## Bloki

### If/else

Pętla `if/else` jest podobna jak w innych językach.

Prosty `if`:

```go
func main() {
	var foo string = "xd"

	if foo == "abc" {
		fmt.Println("Correct")
	}
}
```

Inny przykład:

```go
func main() {
	var numbs int = 666

	if numbs <= 333 {
		fmt.Println("Number is too low...")
    } else if numbs > 334 && <= 665 {
		fmt.Println("Number is kinda close...")
    } else if numbs >= 667 {
		fmt.Println("Seek lower...")
    } else {
		fmt.Println("Welcome to HELL.")
    }
}
```

Skrócony `if`:

```go
func main() {
	if x:= 555; x > 444 {
		fmt.Println("Variable 'x' is greater than 444...")
    }
}
```

W Go do wersji 1.20 nie było tzw. `ternary operator` (np. jak Pythonie: `min = a if a < b else b`).

### Switch

Przykładowy `switch/case` w `Go`:

```go
func main() {
    day := "wednesday"

    switch day {
    case "monday":
        fmt.Println("Back to Mordor...")
    case "friday":
        fmt.Println("It's weekend finally...")
    default:
        fmt.Println("I don't care.")
    }
}
```

Alternatywna wersja:

```go
func main() {
    day := "monday"

    switch day {
	case "monday":
		fmt.Println("Back to Mordor...")
		fallthrough
	case "friday":
		fmt.Println("It's weekend finally...")
	default:
		fmt.Println("I don't care.")
    }
}
```

_Uwaga; `fallthrough` używany jest do przetransferowania kontroli do następnego case'a nawet gdy obecny case spełnia warunek._

### Pętle for

Podstawowa pętla `for`:

```go
func main() {
	for i := 0; i < 10; i++ {
		fmt.Println(i)
	}
}
```

Inny przykład, `for/range`:

```go
var arr [4]string = [4]string{"a", "b", "c"}
var arrx [5]int = [5]int{3, 6, 8, 9, 99}

func iterate(fw [4]int, fwx [5]int) {
	// Values
	for _, v := range fw {
		fmt.Printf("%d\n", v)
	}

	// Keys and values
    for i, v := range fwx {
        fmt.Printf("%d %d\n", i, v)
	}
}
```

```go
var runes []rune

for _, r := range "Hi, Mike" {
    runes = append(runes, r)
}

fmt.Printf("%q\n", runes) // "['H' 'i' ',' ' ' ' M' 'i' 'k' 'e']"
```

```go
func equal(x, y map[string]int) bool {
    if len(x) != len(y) {
        return false
    }
    for k, xv := range x {
        if yv, ok := y[k]; !ok || yv != xv {
        return false
        }
    }
    return true
}
```

![Hugging](/img/Hugging_Gophers.png)

## Funkcje

### Podstawy

Funkcję w Go tworzymy jak poniżej:

```go
func name(param type) (result type) {
	function body
}
```

Bardziej realistyczny przykład:

```go
func receiptValue(sale float64, tax float32) float64 {

	fmt.Println(sale, tax)
	return sale + (sale * float64(tax))
}

func main() {
	fmt.Println(receiptValue(100.00, 0.19))
}
```

W przypadku gdy wszystkie parametry mają ten sam typ, używamy:

```go
func myFunc(z, x, c, v string) string {}
```

### Rekurencja

Przykład rekurencji w Go. Która w przypadku algorytmów, które operują na danych na danych może osiągać benefity z korzystania ze stosu np. FILO (First In Last Out).

```go
func recursiveFunction(number int) int {
	if number < 6 {
		return number
    }

	return number + recursiveFunction(number - 1)
}

func main() {
	calc := recursiveFunction(6)
	fmt.Printf("Recursive: %d", calc)
}
```

### Funkcje jako zmienne

Funkcję mogą być przypisane do zmiennych:

```go
func myFunction() {
	val := myInsideFunc() {
	    fmt.PrintLn("I am inside function.")
	}

	val()
}
```

### Domknięcia

Domknięcie, znane również jako leksykalne domknięcie lub funkcja blokująca, to funkcja, która ma dostęp do zmiennych z otaczającego ją kontekstu, w którym została zdefiniowana. Domknięcia "pamiętają" te zmienne, nawet po zakończeniu wykonania otaczającej funkcji.

```go
func adder() func(int) int {
    sum := 0
    return func(x int) int {
            sum += x
            return sum
    }
}
```

### Zwracanie wielu wartości

`Go` wspiera zwracanie wielu wartości:

```go
func myFunc(foo string) (string, float32) {
	return foo, 666.0
}

var text, nmb = myFunc('boo')
```

### Defer

`Defer` to keyword który umożliwia opóźnienie egzekucji funkcji dopóki dopóki otaczająca go funkcja nie zakończy swojego działania:

```go
func main() {
	defer fmt.Println("I am done!")
	fmt.Println("Deploying package...")
}
```

### Funkcje wariadyczne

Funkcje wariadyczne (variadic functions), to funkcje, które przyjmują zero lub wiele argumentów (`...`):

```go
func add(values ... int) int {
    sum := 0

	for _, v := range values {
		sum += v
    }

	return sum
}
```

### Wskaźniki jako argumenty funkcji

`Wskaźniki` mogą być używane jako argumenty funkcji:

```go
func myFunction(p *int) {}
```

![AzureBit](/img/Azure_Bit_Gopher.png)

## Struktury

Jeśli przychodzisz z języka obiektowego jak Ruby albo Python, to możesz pomyśleć o strukturach jak o lekkich klasach.

`Struktury` pozwalają na kompozycję, ale nie wspierają dziedziczenia (tam samo jak w C).

Struktury definiujemy jak poniżej:

```go
type Store struct {
Code      string
Name      string
Warehouse int
}
```

Tworzenie obiektu na podstawie struktury obiektu:

```go
func main() {
	var s1 = Store{
		Code: "X0001",
		Name: "Warsaw",
		Warehouse: 33,
	}

	var s2 = Store{"X0002", "Gdansk", 99}

	fmt.Println("Store #1:", s1)
	fmt.Println("Store #2:", s2)
}
```

_Note: Wszystkie pola muszą być wypełnione. Inaczej kod się nie skompiluje._

### Dostęp do wartości w obiektach

Zwracanie wartości z obiektów jest proste i intuicyjne:

```go
type Warehouse struct {
	Code int
	Town string
}
func main() {
	var wr1 = Warehouse{
		Code: 33,
		Town: "Berlin",
	}

	fmt.Println(wr1.Code)
	fmt.Println(wr1.Town)
}
```

### Struktury i wskaźniki

W przypadku struktur, też możemy używać wskaźników:

```go
type Warehouse struct {
	Code int
	Town string
}

func main() {
	var wr1 = Warehouse{
		Code: 33,
		Town: "Berlin",
	}

	wh := &wr1

	fmt.Println(wr1.Code)
	fmt.Println((*wh).Town)
}
```

### Widoczność atrybutów

Atrybuty zaczynające się tylko z dużej litery, są widoczne po za package'm:

```go
type Member struct {
    FirstName, LastName string
    Age                 int
    placeOfBirth        string
}
```

Structura `Member` zostanie wyeksportowana, ale bez atrybutu `placeOfBirth`.

### Kompozycja

Jak już wspomniałem `Go` nie wspiera `dziedziczenia`, ale za to wspiera `kompozycję`:

```go
type Engine struct {
    Fuel   string
    Power  float32
    Torque float32
}

type Locomotive struct {
    Engine Engine
    Axis   int
}
```

### Tags

W `Go` istnieje termin `struct tags`. Takie tagi używane są np przez liczne biblioteki `enkodujące` i `ORM'y`:

```go
type Animal struct {
	Name    string `json:"name"`
	Age     int    `json:"age"`
}
```

![BlueGopher](/img/BLUE_GOPHER.png)

## Metody

Do typów i struktur możemy definiować własne metody:

```go
func (variable Type) MethodName(params) (returnTypes) {}
```

```go
type Engine struct {
    Fuel   string
    Power  float32
    Torque float32
}

type Locomotive struct {
    Engine Engine
    Axis   int
}

type Train struct {
    Traction     Locomotive
    CoachesType  string
    CoachesCount int
    Destination  string
}

func (t Train) IsPassenger() bool {
    return t.CoachesType == "passenger"
}
```

Utworzenie obiektu oraz wywołanie metody:

```go
func main() {
	myEngine := Engine{"Diesel", 3400.00, 9000.00}
	myLocomotive := Locomotive{myEngine, 6}
	myTrain := Train{myLocomotive, "passenger", 16, "Warsaw"}

	fmt.Println("Is passenger: ", myTrain.IsPassenger())
```

### Metody i odbiorcy wskaźników

Weźmy np. taką metodę jak `CoachesType`:

```go
func (t Train) UpdateCoachesType(name string) {
	t.CoachesType = name
}
```

Spróbujmy zmienić wartość:

```go
func main() {
	myEngine := Engine{"Diesel", 3400.00, 9000.00}
	myLocomotive := Locomotive{myEngine, 6}
	myTrain := Train{myLocomotive, "passenger", 16, "Warsaw"}

	fmt.Println("Is passenger: ", myTrain.IsPassenger())

	myTrain.UpdateCoachesType("freight")
	fmt.Println(myTrain.CoachesType)
}
```

Jak widać wartość pozostała niezmieniona:

```shell
Is passenger:  true
passenger
```

Bez obaw, jest to oczekiwany wynik z racji, że metody bez wskaźników nie aktualizują wartości.

Jednakże jeżeli zrobimy to tak:

```go
func (t *Train) UpdateCoachesTypeProper(name string) {
	t.CoachesType = name
}

func main() {
	myEngine := Engine{"Diesel", 3400.00, 9000.00}
	myLocomotive := Locomotive{myEngine, 6}
	myTrain := Train{myLocomotive, "passenger", 16, "Warsaw"}

	fmt.Println("Is passenger: ", myTrain.IsPassenger())
	myTrain.UpdateCoachesType("freight")
	fmt.Println(myTrain.CoachesType)

	myTrain.UpdateCoachesTypeProper("freight")
	fmt.Println(myTrain.CoachesType)
}
```

Wartość ulega zmianie!

```shell
Is passenger:  true
passenger
freight
```

## Interfejsy

Interfejsy w Go to kluczowy element języka, który umożliwia tworzenie abstrakcyjnych typów danych. Interfejs definiuje zestaw metod, które muszą być zaimplementowane przez typ, aby spełniać ten interfejs.

Interfejsy są używane do definiowania funkcji, które mogą być używane przez różne typy. Są one szczególnie przydatne w sytuacjach, gdy chcesz, aby funkcja mogła obsługiwać różne typy danych.

W Go, typ spełnia interfejs, jeśli implementuje wszystkie metody wymienione w definicji interfejsu. Nie jest wymagane jawnie stwierdzenie, że dany typ spełnia interfejs - to jest ustalane dynamicznie w czasie wykonania.

Przykładem może być interfejs `io.Reader` z biblioteki standardowej Go, który definiuje metodę `Read`. Każdy typ, który implementuje metodę `Read` z odpowiednim sygnaturą, spełnia interfejs `io.Reader`. To pozwala na użycie różnych typów, takich jak pliki, sieci, buforów itp., w sposób generyczny, gdzie jest wymagany `io.Reader`.

Przykład interfejsu **Vehicle** oraz typów **Car**, **Lorry** oraz **Tractor**:

```go
package main

import "fmt"

type Vehicle interface {
    StartEngine() string
}

type Car struct {
    Brand string
}

type Lorry struct {
    Brand string
}

type Tractor struct {
    Brand string
}


func (c Car) StartEngine() string {
    return fmt.Sprintf("%s's car engine started", c.Brand)
}
func (l Lorry) StartEngine() string {
    return fmt.Sprintf("%s's lorry engine started", l.Brand)
}

func (t Tractor) StartEngine() string {
    return fmt.Sprintf("%s's tractor engine started", t.Brand)
}

func main() {
    car := Car{"Toyota"}
    lorry := Lorry{"Volvo"}
    tractor := Tractor{"John Deere"}

    vehicles := []Vehicle{car, lorry, tractor}

    for _, vehicle := range vehicles {
        fmt.Println(vehicle.StartEngine())
    }
}
```

Oto przykład interfejsu, struktury i metody w Go, które używają Car, Lorry i Tractor:

```go
package main

import "fmt"

// Vehicle jest interfejsem, który definiuje metody, które muszą być zaimplementowane przez każdy typ pojazdu
type Vehicle interface {
    StartEngine() string
}

// Car jest strukturą reprezentującą samochód
type Car struct {
    Brand string
}

// Lorry jest strukturą reprezentującą ciężarówkę
type Lorry struct {
    Brand string
}

// Tractor jest strukturą reprezentującą traktor
type Tractor struct {
    Brand string
}

// StartEngine jest metodą struktury Car, która implementuje interfejs Vehicle
func (c Car) StartEngine() string {
    return fmt.Sprintf("%s's car engine started", c.Brand)
}

// StartEngine jest metodą struktury Lorry, która implementuje interfejs Vehicle
func (l Lorry) StartEngine() string {
    return fmt.Sprintf("%s's lorry engine started", l.Brand)
}

// StartEngine jest metodą struktury Tractor, która implementuje interfejs Vehicle
func (t Tractor) StartEngine() string {
    return fmt.Sprintf("%s's tractor engine started", t.Brand)
}

func main() {
    car := Car{"Toyota"}
    lorry := Lorry{"Volvo"}
    tractor := Tractor{"John Deere"}

    vehicles := []Vehicle{car, lorry, tractor}

    for _, vehicle := range vehicles {
        fmt.Println(vehicle.StartEngine())
    }
}
```

W tym kodzie mamy interfejs `Vehicle`, który definiuje metodę `StartEngine()`. Mamy też trzy struktury: `Car`, `Lorry` i `Tractor`, które wszystkie implementują ten interfejs. W funkcji `main` tworzymy instancje tych struktur i używamy ich do wywołania metody `StartEngine()`.

## Obsługa błędów

Język Go nie został wyposażony w mechanizm wyjątków błędów w stylu `try .. catch` lub `begin .. escape .. ensure`.

Go obsługuje błędy jędynie poprzez zwracanie wartości typu `error`, zawsze jako ostatniej spośród wszystkich wartości zwracanych przez funkcję.

W każdym przypadku gdy funkcja coś zwraca - możesz przy pomocy funkcji sprawdzić, czy zmienna odpowiadająca za błędy ma wartość nil:

```go
func calcRemainderAndMod(num, den int) (int, int, error) {
    if den == 0 {
        return 0, 0, errors.New("Denominator is 0")
    }
        return num / den, num % den, nil
}

func main() {
    num := 20
    den:= 3

    remainder, mod, err := calcRemainderAndMod(num, den)
    if err != nil {
        fmt.Println(err)
        os.Exit(1)
    }
    fmt.Println(remainder, mod)
}
```
