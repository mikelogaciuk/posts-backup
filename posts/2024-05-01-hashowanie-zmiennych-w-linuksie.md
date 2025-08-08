---
layout: post
title: Hashowanie zmiennych w Linuksie
date: 2024-05-01
category: ["linux", "passwords", "good", "practises", "unix"]
---

![logo](/img/rusty_passty.jpeg)

- [Wstęp](#wstęp)
- [Zależności](#zależności)
- [GPG](#gpg)
- [Pass](#pass)
- [Dodawanie kluczy](#dodawanie-kluczy)
- [Odczyt kluczy](#odczyt-kluczy)
- [Używanie kluczy w zmiennych](#używanie-kluczy-w-zmiennych)
- [Uwagi](#uwagi)

## Wstęp

Jeszcze do niedawna, jednym z sugerowanych rozwiązań na przechowywanie zmiennych w Linuxie w postaci np. kluczy do API - było używane czystego tekstu w `.zshrc`.

Dziś wiadomo, że nie jest to najlepszy pomysł.

W tej sytuacji na ratunek przychodzi Nam standardowy Unix'owy password manager o mało kreatywnej nazwie: `pass`.

## Zależności

Pakiet instalujemy przy pomocy:

```shell
sudo apt update && sudo apt install pass -y
```

## GPG

Na początek musimy wygenerować swój klucz GPG, którego użyjemy przy inicjalizacji `credential store'u` w **Pass'ie**:

```shell
gpg --full-generate-key
```

Po wybraniu stosownych opcji, prompt poprosi Nas o utworzenie hasła do nowo wygenerowanego klucza, następnie zgodnie z poniższym musimy odszukać key id.

Jeżeli klucz już posiadamy, to tylko listujemy klucze:

```shell
gpg --list-secret-keys --keyid-format=long
```

Potrzebny Nam id, powinien być podobny do tego:

```shell
66644X73F79...82ABD6DD8B
```

## Pass

Następnie inicjalizujemy `credentials store` dla Naszych kluczy:

```shell
pass init 66644X73F79...82ABD6DD8B
```

Powinniśmy otrzymać informację: `Password store initialized for 6644X73F79...82ABD6DD8B`.

## Dodawanie kluczy

Aby dodać klucz, używamy komendy:

```shell
pass insert App/Foo
```

Gdzie _App_ to Nasza grupa, a _Foo_ to nazwa aplikacji:

```shell
mkdir: created directory '/home/USER/.password-store/App'
Enter password for App/Foo:
Retype password for App/Foo:
```

Gdy wykonamy `cat'a` na nowo utworzonym pliku, dostaniemy zwrotnie zaszyfrowane hasło:

```shell
$ cat .password-store/App/Foo.gpg
J@a^%6+Y4Fq8d.,YwR0 2)f:sud <ZnXܼT e?	f̂g^WP!9Ҷ!:oǎR[pU
                                                        |m.Z	%
```

## Odczyt kluczy

W celu odczytu, wykonujemy proste:

```shell
$ pass show App/Foo
123
```

## Używanie kluczy w zmiennych

Jeżeli tego jeszcze nie zrobiliśmy, to zmieniamy uprawnienia do `.zshrc` lub `.bashrc`:

```shell
chmod 600 ~/.zshrc
```

A do pliku dodajemy przykładowo:

```shell
export Foo="$(pass show App/Foo)"
```

Od teraz po otworzeniu konsoli, prompt poprosi Nas o wpisanie klucza do `keystore'u`.

Nasz nowo zapisany klucz, możemy odczytać ze zmiennej:

```shell
echo $Foo
```

I użyć go do np. uruchomienia aplikacji, kontenera etc.

## Uwagi

Oczywiście są inne rozwiązania jak `Vault (od Hashicorp)` czy `Bitwarden`, lecz czy dla takich celów jest sens?

Biorąc pod uwagę fakt, że do root'a mamy inne hasło niżeli do Naszego usera, a dysk mamy zaszyfrowany kluczem, a zmienne w powyższy sposób.

To aby odczytać zawartość Naszych obecnych credentiali, potrzeba by było zalogować się do Naszej aktualnej sesji.

Trochę mało realne...
