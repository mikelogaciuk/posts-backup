---
layout: post
title: Instalowanie kerneli do VSCode i Jupytera
date: 2025-01-15
category:
  [
    "jupyter",
    "notebook",
    "kernel",
    "visual",
    "studio",
    "code",
    "ruby",
    "sql",
    "javascript",
    "typescript",
    "elixir",
  ]
---

![header](/img/exr_cr_rust_5.jpeg)

## Spis treści

- [Spis treści](#spis-treści)
- [Wstęp](#wstęp)
- [Jupyter](#jupyter)
- [DuckDB](#duckdb)
- [Ruby](#ruby)
- [Go](#go)
- [Javascript \& Typescript i Php](#javascript--typescript-i-php)
- [Elixir](#elixir)

## Wstęp

Jupyter Notebook w `Visual Studio Code` to bardzo fajna alternatywa dla jego webowej wersji.

Dla inżynierów danych czy `Python'istów` to wręcz podstawowe narzędzie do prototypowania kodu.

Nie każdy wie, że możemy doinstalować `Kernel'e` dla innych języków, takich jak:

- `Elixir`
- `Go`
- `Javascript`
- `Ruby`
- `Scala`
- `Typescript`

## Jupyter

Jupytera warto uruchamiać z wyodrębnionego środowiska.

W tym przypadku tworzymy mu osobny folder np.:

```shell
cd ~/Repos && mkdir -p jupyterbooks && cd jupyterbooks && virtualenv venv &&\
source venv/bin/activate && echo 'venv' > .gitignore &&\
pip freeze > requirements.txt
```

I instalujemy:

```shell
pip install notebook ipykernel
```

Następnie dodajemy sobie nowoutworzony folder do `Workspace'ów` w VSCode, restartujemy dla 'zdrowia' i po ponownych uruchomieniu, po wduszeniu `F1` lub `CTRL+SHIFT+P`, wybieramy: `Create: New Jupyter Notebook` a w nim po prawej stronie wybieramy zakładkę `Existing Jupyter Server`, wpisujemy `localhost` i voula, uprzednio uruchamiając w konsoli `jupyter server`.

Dlaczego?

Ponieważ dzięki temu izolujemy biblioteki od systemowego `Pythona`, ot co.

Teraz możemy zainstalować `Pandas'a`, `Polars'a` czy inne narzędzia do np. manipulacji danymi (choćby `Duckdb`) i pracować bez przeszkód:

```shell
pip install ipython-sql duckdb duckdb-engine pandas numpy polars
```

## DuckDB

By użyć `DuckDB` w notebook'u, musimy załadować plugin:

```jupyter
%load_ext sql
%sql duckdb:///:memory:
```

Następnie dla przykładu w komórce Pythonowej wywołujemy:

```python
import duckdb
import pandas as pd
# No need to import duckdb_engine
#  jupysql will auto-detect the driver needed based on the connection string!

# Import jupysql Jupyter extension to create SQL cells
%load_ext sql

%config SqlMagic.autopandas = True
%config SqlMagic.feedback = False
%config SqlMagic.displaycon = False

%sql duckdb:///:memory:
```

## Ruby

Instalujemy je tak:

```shell
gem install iruby
iruby register --force
```

## Go

Dla `Golanga` wykonujemy:

```shell
  mkdir -p "$(go env GOPATH)"/src/github.com/gopherdata
  cd "$(go env GOPATH)"/src/github.com/gopherdata
  git clone https://github.com/gopherdata/gophernotes
  cd gophernotes
  git checkout -f v0.7.5
  go install
  mkdir -p ~/.local/share/jupyter/kernels/gophernotes
  cp kernel/* ~/.local/share/jupyter/kernels/gophernotes
  cd ~/.local/share/jupyter/kernels/gophernotes
  chmod +w ./kernel.json # in case copied kernel.json has no write permission
  sed "s|gophernotes|$(go env GOPATH)/bin/gophernotes|" < kernel.json.in > kernel.json
```

## Javascript & Typescript i Php

O ile instrukcje instalacji znalazłem to nie udało mi się ich poprawnie zainstalować na Ubuntu 24.04.

Z tego też powodu nie dodaję instrukcji.

## Elixir

Dla Elixira, obecnie najlepszą alternatywą są `Livebook'i`, które możecie znaleźć: [tu](https://livebook.dev/).

Możecie też zainstalować go globalnie mają Elixira:

```shell
mix do local.rebar --force, local.hex --force
mix escript.install hex livebook
```

Uruchamianie:

```shell
livebook server
```
