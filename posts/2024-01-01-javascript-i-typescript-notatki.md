---
layout: post
title: Javascript & Typescript - Notatki
date: 2024-01-01
category: ["javascript", "js", "typescript", "ts", "front-end"]
---

![pic](/img/fullstack_004.jpeg)

## Spis treści

- [Spis treści](#spis-treści)
- [Wstęp](#wstęp)
- [Zmienne](#zmienne)
- [Data Types](#data-types)
- [Funkcje](#funkcje)
- [Wyrażenia logiczne oraz pętle](#wyrażenia-logiczne-oraz-pętle)
- [Tablice](#tablice)
- [Obiekty](#obiekty)
- [Promise'y oraz Async/Await](#promisey-oraz-asyncawait)
- [Manipulacja DOM'em](#manipulacja-domem)
- [Obsługiwanie API](#obsługiwanie-api)
- [ES6](#es6)
- [Programowanie obiektowe (klasy)](#programowanie-obiektowe-klasy)
  - [Dziedziczenie](#dziedziczenie)
  - [Metody statyczne](#metody-statyczne)
  - [Getters, setters](#getters-setters)
  - [Prywatne atrybuty i metody](#prywatne-atrybuty-i-metody)
  - [Polimorfizm](#polimorfizm)
  - [Klasy abstrakcyjne (ES6)](#klasy-abstrakcyjne-es6)
- [TypeScript](#typescript)
  - [Instalacja TypeScript](#instalacja-typescript)
  - [Kompilacja pliku TypeScript](#kompilacja-pliku-typescript)
  - [Inicjalizacja projektu TypeScript](#inicjalizacja-projektu-typescript)
  - [Typy](#typy)
    - [Podstawowe typy](#podstawowe-typy)
    - [Enum](#enum)
    - [Any](#any)
    - [Void](#void)
  - [Funkcje w TS](#funkcje-w-ts)
    - [Typowanie parametrów i zwracanych wartości](#typowanie-parametrów-i-zwracanych-wartości)
    - [Parametry opcjonalne i domyślne](#parametry-opcjonalne-i-domyślne)
  - [Interfejsy w TS](#interfejsy-w-ts)
    - [Definiowanie interfejsów dla zamówienia](#definiowanie-interfejsów-dla-zamówienia)
  - [Klasy w TS](#klasy-w-ts)
    - [Definiowanie klasy dla zamówienia](#definiowanie-klasy-dla-zamówienia)
    - [Przykład użycia](#przykład-użycia)
    - [Dziedziczenie w Typescript](#dziedziczenie-w-typescript)
  - [Moduły](#moduły)
    - [Eksportowanie i importowanie modułów](#eksportowanie-i-importowanie-modułów)
  - [Typy zaawansowane](#typy-zaawansowane)
    - [Typy Union](#typy-union)
    - [Typy Intersection](#typy-intersection)
    - [Typy generyczne](#typy-generyczne)
  - [Narzędzia](#narzędzia)
    - [ts-node](#ts-node)
    - [Typowanie bibliotek JavaScript](#typowanie-bibliotek-javascript)
  - [Konfiguracja tsconfig.json](#konfiguracja-tsconfigjson)
    - [Przykładowa konfiguracja](#przykładowa-konfiguracja)

## Wstęp

Dawno zapomniane, odłożone do kąta notatki z JS'a & TS'a.

Może komuś się przydadzą, w przypadku obrania tej dosyć popularnej drogi w software developmencie.

## Zmienne

```js
let x = 10; // Zmienna w obrębie bloku
const y = 20; // Stała w obrębie bloku
var z = 30; // Zmienna w obrębie funkcji
```

## Data Types

```js
let str = "Hello"; // String
let num = 123; // Integer
let bool = true; // Boolean
let obj = { key: "value" }; // Obiekt
let arr = [1, 2, 3]; // Tablica
let und; // Undefined
let nul = null; // Null
```

## Funkcje

```js
// Deklarowanie funkcji
function add(a, b) {
  return a + b;
}

// Wyrażenie funkcyjne
const subtract = function (a, b) {
  return a - b;
};

// Arrow Function (lambda, anominowa - jak zwał tak zwał)
const multiply = (a, b) => a * b;

// IIFE, czyli ang. Immediately Invoked Function Expression
(function () {
  console.log("IIFE executed");
})();
```

## Wyrażenia logiczne oraz pętle

```js
// If-Else
if (x > 10) {
  console.log("x is greater than 10");
} else {
  console.log("x is 10 or less");
}

// Switch, odpowiednik Elixirowego case'a
switch (x) {
  case 10:
    console.log("x is 10");
    break;
  default:
    console.log("x is not 10");
}

// Pętla for
for (let i = 0; i < 5; i++) {
  console.log(i);
}

// Pętla While
let i = 0;
while (i < 5) {
  console.log(i);
  i++;
}

// Pętla Do-While
let j = 0;
do {
  console.log(j);
  j++;
} while (j < 5);
```

## Tablice

```js
let arr = [1, 2, 3, 4, 5];

// Methody tablic
arr.push(6); // Dodawanie na końcu
arr.pop(); // Usuwanie ostatniego elementu
arr.shift(); // Usuwanie pierwszego elementu
arr.unshift(0); // Dodawanie na początek tablicy
arr.forEach((item) => console.log(item)); // Iterowanie
let newArr = arr.map((item) => item * 2); // Transformowanie
let filteredArr = arr.filter((item) => item > 2); // Filtrowanie
let foundItem = arr.find((item) => item === 3); // Wyszukiwanie
let index = arr.indexOf(3); // Szukanie indeksu
let includes = arr.includes(3); // Sprawdzanie czy element istnieje w tablicy
let reducedValue = arr.reduce((acc, item) => acc + item, 0); // Reduce
```

## Obiekty

```js
let person = {
  name: "Alice",
  age: 18,
  greet: function () {
    console.log("Hello, " + this.name);
  },
};

// Dostęp do atrybutów
console.log(person.name);
console.log(person["age"]);

// Dodawanie właściwości
person.job = "Developer";

// Usuwanie
delete person.age;

// Metody obiektów
let keys = Object.keys(person); // Klucze
let values = Object.values(person); // Wartości
let entries = Object.entries(person); // Wpisy
```

## Promise'y oraz Async/Await

```js
// Promise'y
let promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve("Done!"), 1000);
});

promise.then((result) => console.log(result));

// Async/Await
async function asyncFunc() {
  let result = await promise;
  console.log(result);
}

asyncFunc();
```

## Manipulacja DOM'em

```js
// Wybieranie elementów
let element = document.getElementById("myId");
let elements = document.getElementsByClassName("myClass");
let queryElement = document.querySelector(".myClass");

// Modyfikowanie elementów
element.textContent = "New Text";
element.style.color = "red";
element.classList.add("newClass");

// Nasłuchiwanie event'óœ
element.addEventListener("click", function () {
  console.log("Element clicked!");
});
```

## Obsługiwanie API

```js
// Standard
fetch("https://api.example.com/data")
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.error("Error:", error));

// Async/Await & Fetch
async function fetchData() {
  try {
    let response = await fetch("https://api.example.com/data");
    let data = await response.json();
    console.log(data);
  } catch (error) {
    console.error("Error:", error);
  }
}

fetchData();
```

## ES6

```js
// Literały
let name = "John";
let greeting = `Hello, ${name}!`;

// Destrukturyzacja
let [a, b] = [1, 2];
let { name: personName, age: personAge } = person;

// Operator spread
let arr1 = [1, 2, 3];
let arr2 = [...arr1, 4, 5];
let obj1 = { a: 1, b: 2 };
let obj2 = { ...obj1, c: 3 };

// Operator variadic? Jakoś tak :)
function sum(...args) {
  return args.reduce((acc, val) => acc + val, 0);
}

// Domyślne atrybuty
function greet(name = "Guest") {
  console.log(`Hello, ${name}`);
}
```

## Programowanie obiektowe (klasy)

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    console.log(
      `Hello, my name is ${this.name} and I am ${this.age} years old.`
    );
  }
}

let john = new Person("John", 30);
john.greet();
```

### Dziedziczenie

```js
class Employee extends Person {
  constructor(name, age, jobTitle) {
    super(name, age); // Konstruktor 'rodzica'
    this.jobTitle = jobTitle;
  }

  work() {
    console.log(`${this.name} is working as a ${this.jobTitle}.`);
  }
}

let jane = new Employee("Jane", 25, "Software Engineer");
jane.greet(); // Hello, my name is Jane and I am 25 years old.
jane.work(); // Jane is working as a Software Engineer.
```

### Metody statyczne

```js
class MathUtil {
  static add(a, b) {
    return a + b;
  }
}

console.log(MathUtil.add(5, 3)); // 8
```

### Getters, setters

```js
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  get area() {
    return this.width + this.height;
  }

  set area(value) {
    this.width = value / 2;
    this.height = value / 2;
  }
}

let rect = new Rectangle(10, 20);
console.log(rect.area);
rect.area = 40;
console.log(rect.width);
console.log(rect.height);
```

### Prywatne atrybuty i metody

```js
class Counter {
  #count = 0; // Prywatny atrybut

  increment() {
    this.#count++;
    console.log(this.#count);
  }

  #privateMethod() {
    console.log("This is a private method");
  }
}

let counter = new Counter();
counter.increment();
counter.increment();
// counter.#privateMethod(); // Error: Private field '#privateMethod' must be declared in an enclosing class
```

### Polimorfizm

```js
class Animal {
  speak() {
    console.log("Animal speaks");
  }
}

class Dog extends Animal {
  speak() {
    console.log("Dog barks");
  }
}

class Cat extends Animal {
  speak() {
    console.log("Cat meows");
  }
}

let animals = [new Dog(), new Cat()];
animals.forEach((animal) => animal.speak());
// Dog barks
// Cat meows
```

### Klasy abstrakcyjne (ES6)

JS nie posiada klas abstrakcyjnych, ale można je symulować używając ES6:

```js
class AbstractAnimal {
  constructor() {
    if (new.target === AbstractAnimal) {
      throw new TypeError("Cannot construct AbstractAnimal instances directly");
    }
  }

  speak() {
    throw new Error("Method 'speak()' must be implemented.");
  }
}

class Bird extends AbstractAnimal {
  speak() {
    console.log("Bird chirps");
  }
}

let bird = new Bird();
bird.speak(); // Bird chirps

// let animal = new AbstractAnimal(); // Error: Cannot construct AbstractAnimal instances directly
```

## TypeScript

### Instalacja TypeScript

```bash
npm install -g typescript
```

### Kompilacja pliku TypeScript

```bash
tsc filename.ts
```

### Inicjalizacja projektu TypeScript

```bash
tsc --init
```

### Typy

#### Podstawowe typy

```typescript
let isDone: boolean = false;
let age: number = 30;
let name: string = "John";
let list: number[] = [1, 2, 3];
let tuple: [string, number] = ["hello", 10];
```

#### Enum

```typescript
enum Color {
  Red,
  Green,
  Blue,
}
let c: Color = Color.Green;
```

#### Any

```typescript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false;
```

#### Void

```typescript
function warnUser(): void {
  console.log("This is my warning message");
}
```

### Funkcje w TS

#### Typowanie parametrów i zwracanych wartości

```typescript
function add(x: number, y: number): number {
  return x + y;
}
```

#### Parametry opcjonalne i domyślne

```typescript
function buildName(firstName: string, lastName?: string): string {
  return firstName + " " + (lastName || "");
}
```

### Interfejsy w TS

#### Definiowanie interfejsów dla zamówienia

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
}

interface Customer {
  id: number;
  name: string;
  email: string;
}

interface OrderItem {
  product: Product;
  quantity: number;
}

interface Order {
  id: number;
  customer: Customer;
  items: OrderItem[];
  totalAmount: number;
  status: "pending" | "shipped" | "delivered" | "cancelled";
}
```

### Klasy w TS

#### Definiowanie klasy dla zamówienia

```typescript
class OrderClass implements Order {
  id: number;
  customer: Customer;
  items: OrderItem[];
  totalAmount: number;
  status: "pending" | "shipped" | "delivered" | "cancelled";

  constructor(id: number, customer: Customer, items: OrderItem[]) {
    this.id = id;
    this.customer = customer;
    this.items = items;
    this.totalAmount = this.calculateTotalAmount();
    this.status = "pending";
  }

  private calculateTotalAmount(): number {
    return this.items.reduce(
      (total, item) => total + item.product.price * item.quantity,
      0
    );
  }

  public addItem(product: Product, quantity: number): void {
    const existingItem = this.items.find(
      (item) => item.product.id === product.id
    );
    if (existingItem) {
      existingItem.quantity += quantity;
    } else {
      this.items.push({ product, quantity });
    }
    this.totalAmount = this.calculateTotalAmount();
  }

  public removeItem(productId: number): void {
    this.items = this.items.filter((item) => item.product.id !== productId);
    this.totalAmount = this.calculateTotalAmount();
  }

  public updateStatus(
    newStatus: "pending" | "shipped" | "delivered" | "cancelled"
  ): void {
    this.status = newStatus;
  }
}
```

#### Przykład użycia

```typescript
const customer: Customer = {
  id: 1,
  name: "John Doe",
  email: "john.doe@example.com",
};
const product1: Product = { id: 1, name: "Laptop", price: 1000 };
const product2: Product = { id: 2, name: "Mouse", price: 50 };

const order = new OrderClass(1, customer, [{ product: product1, quantity: 1 }]);
order.addItem(product2, 2);
order.updateStatus("shipped");

console.log(order);
```

#### Dziedziczenie w Typescript

```typescript
class Animal {
  private name: string;

  constructor(name: string) {
    this.name = name;
  }

  public move(distanceInMeters: number): void {
    console.log(`${this.name} moved ${distanceInMeters}m.`);
  }
}

class Dog extends Animal {
  bark() {
    console.log("Woof! Woof!");
  }
}

const dog = new Dog("Rex");
dog.bark();
dog.move(10);
```

### Moduły

#### Eksportowanie i importowanie modułów

```typescript
// In file person.ts
export interface Person {
  firstName: string;
  lastName: string;
}

// In file main.ts
import { Person } from "./person";

const user: Person = { firstName: "John", lastName: "Doe" };
```

### Typy zaawansowane

#### Typy Union

```typescript
let value: string | number;
value = "Hello";
value = 42;
```

#### Typy Intersection

```typescript
interface ErrorHandling {
  success: boolean;
  error?: { message: string };
}

interface ArtworksData {
  artworks: { title: string }[];
}

type ArtworksResponse = ArtworksData & ErrorHandling;
```

#### Typy generyczne

```typescript
function identity<T>(arg: T): T {
  return arg;
}

let output = identity<string>("myString");
```

### Narzędzia

#### ts-node

Uruchamianie plików TypeScript bezpośrednio w Node.js:

```bash
npm install -g ts-node
ts-node filename.ts
```

#### Typowanie bibliotek JavaScript

Instalacja typów dla bibliotek:

```bash
npm install --save-dev @types/express
```

### Konfiguracja tsconfig.json

#### Przykładowa konfiguracja

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```
