---
layout: post
title: Rails'y to nie wszystko
date: 2023-12-12
category:
  [
    "learning",
    "programming",
    "languages",
    "ruby",
    "sinatra",
    "sequel",
    "rails",
    "ror",
  ]
---

![Header](/img/rails_08.png)

- [Ruby](#ruby)
- [No dobra, a co z tymi Railsami?](#no-dobra-a-co-z-tymi-railsami)
- [To co po za Railsami?](#to-co-po-za-railsami)
- [Może maila?](#może-maila)
- [Potrzebujesz scheduler'a?](#potrzebujesz-schedulera)
- [Testy](#testy)
- [Maszyny wirtualne w formie infrastruktury jako kodu](#maszyny-wirtualne-w-formie-infrastruktury-jako-kodu)
- [CLI](#cli)
- [Podsumowanie](#podsumowanie)

## Ruby

**Ruby!** - język, który po dziś dzień ma tyle samo fanów - co wrogów.

Kochany za ekspresywność, z przypominającą język angielski składnią, bogatym ekosystemem bibliotek i framework'ów (gem'y), ale do niedawna dosyć mocno atakowany ze względu na jego wydajność i przypadłość w postaci Global Interpreter Lock'a (tak samo jak i Python zresztą).

Zapoczątkowany przez **Yukihiro 'Matza' Matsumoto** w drugiej połowie lat 90'tych język, przeszedł długą drogę.

Ostatnie wydania z serii **3.x** wprowadziły zmiany, które rzucają pozytywne światło na przyszłość tego języka. Począwszy od `fiber'ów`, które można nazwać samurajską wersją `gouritines` z **Go** po `ractory`.

> **Fiber'y** są podobne do wątków z tą różnicą, że nie są kontrolowane przez system operacyjny, a przez interpreter - dzięki czemu w teorii zyskujemy na wydajności ze względu na mniejsze przełączanie kontekstu. Minusem jest fakt, że musimy użyć schedulera... Natomiast **ractor'y**, tak jak fibery, są podobne do wątków - z tym, że ractory mają własny **GIL**.

Wraz z tymi zmianami doszedł jeszcze jeden element, od wersji `3.2`: **YJIT** tj **Yet Another Ruby JIT**, który znacząco zwiększył wydajność aplikacji pisanych w Ruby (Shopify przepisany z **C99** do **Rust'a** [_#1_](https://shopify.engineering/porting-yjit-ruby-compiler-to-rust) [_#2_](https://shopify.engineering/ruby-yjit-is-production-ready)).

W wersji 3.3.0 dostał jeszcze większego kopa w wydajności, która w moim przypadku zachęciła mnie do zrezygnowania z TruffleRuby w maszynie wirtualnej GraalVM (community) na rzecz właśnie YJIT w jednym moich produkcyjnych API.

## No dobra, a co z tymi Railsami?

**Ruby on Rails** to najbardziej znany framework napisany w języku Ruby używany do web development'u zarówno przez fullstack'ów jak i back-end'owców. Framework zapoczątkowany przez David'a Heinemeier'a Hansson'a, do dziś uznawany wzór w kwestii tworzenia framework'ów. Historię Railsów możecie poznać dzięki świetnemu [dokumentowi](https://youtu.be/HDKUEXBF3B4?si=QNht8Ki_TQV7m9ar) od **Honeypot**.

Wykorzystywany min. przez **Shopify**, **Airbnb**, **Dribble** czy **Twitch**'a a także **Github**'a i niezliczoną ilość MVP/PoC'ów, które tylko początkowo miały być pisane w Ruby...

![troll](/img/meme-faces-trol.png)

Niemniej jednak z Railsami jest jeden problem: przyćmiewa wszystko w ekosystemie Ruby. W tym sensie, że jeżeli jest jakaś wzmianka o nauce Ruby to za każdym razem pojawia się w niej słowo **Rails**.

Ba! Czasami pojawiają się artykuły, które sugerują, że Rails'y to jakiś tajemniczy superset Ruby (a nim oczywiście nie jest). No, ale takie artykuły z reguły są pisane przez bot'y z **Medium'a** w celu nabicia postów (sorry chłopaki).

## To co po za Railsami?

I tu zaczyna się jazda bez trzymanki, bo o ile uznamy, że obchodzi Nas tylko back-end to w sumie lista takich framework'ów i microframework'ów kończy się na 15 pozycjach (zależnie od faktu jak bardzo `batteries included` ma ów framework być).

Oczywiście do takich naj-najbardziej znanych zaliczyć możemy takie framework'i jak:

- Sinatra
- Padrino (Sinatra na sterydach)
- Hanami
- Cuba
- Roda
- czy Grape.

Z pomocą **ActiveRecord'u** lub **Sequel'a** oraz **bcrypt'u** jesteśmy w stanie naprawdę małym nakładem sił, napisać w pełni działający back-end.

W tym celu możemy użyć kodu Sinatry i go uruchomić:

```ruby
require 'sinatra'
require 'sequel'
require 'json'
require 'sqlite3'

begin
  LOCAL_DB = Sequel.connect('sqlite://db/database.db')
rescue Exception => e
  puts "Database not found"
ensure
  %x[mkdir -p db]
  LOCAL_DB = Sequel.connect('sqlite://db/database.db')
end

LOCAL_DB.create_table? :users do
  primary_key :id
  String :name
  String :email
  String :password
  String :salt
  String :token
  String :role
  DateTime :created_at
  DateTime :updated_at
end

get '/' do
  'Hello world!'
end

get '/users' do
  users = LOCAL_DB[:users]
  users.all.to_json
end

post '/users' do
  users = LOCAL_DB[:users]
  user = users.insert(:name => params[:name],
                      :email => params[:email],
                      :password => params[:password],
                      :salt => params[:salt],
                      :token => params[:token],
                      :role => params[:role],
                      :created_at => Time.now,
                      :updated_at => Time.now)
  user.to_json
end

get '/users/:id' do
  users = LOCAL_DB[:users]
  user = users.where(:id => params[:id])
  user.all.to_json
end

error 401 do
  status 401
  content_type :json
  { message: "You need to login in with a proper authentication key in order to use the API." }.to_json
end

error 403 do
  status 403
  content_type :json
  { message: "Access forbidden" }.to_json
end

error 404 do
  status 404
  content_type :json
  { message: "Endpoint not found" }.to_json
end
```

Nie, to nie żart. To API naprawdę będzie działać, mimo że wygląda jak pseudokod - to wcale nim nie jest. W połączeniu z szeroką gamą `gem'ów`, świat lekkiego developmentu staje przed Nami otworem.

## Może maila?

Potrzebujesz wysłać maila? Nic bardziej trudnego:

```ruby
title = "Some dummy title"
message = """
    <html>
    <head></head>
      <body>
        <p>
        Lorem ipsum....
        </p>
      </body>
    </html>
"""

Pony.mail(:to => 'john.doe@gmail.com',
          :from => 'author@domain.xd',
          :cc => 'cc@domain.xd',
          :bcc => 'bcc@domain.xd',
          :subject => title,
          :html_body => message,
          :body => raw,
          :via => :smtp,
          :via_options => {
            :address              => config[:mail_host],
            :port                 => config[:mail_port],
            :enable_starttls_auto => true,
            :user_name            => config[:mail_user],
            :password             => config[:mail_password],
            :authentication       => :login,
            :domain               => "domain.xd"
          })
```

## Potrzebujesz scheduler'a?

Użyj **whenever** albo **sidekiq'a**:

```ruby
every 3.hours do
  rake "database:indexes:rebuild"
end

every 1.day, at: ['3:00 am', '8:00 pm'] do
  command "echo $DOMAIN_PWD | kinit $DOMAIN_USR@COMPANY.LOCAL"
end
```

## Testy

Pisanie testów w Ruby może być równie przyjemne co kodowanie:

```ruby
RSpec.describe Receipt do
  it "sums the prices of receipt line items" do
    transaction = Receipt.new
    transaction.add_position(LineItem.new(:item => Item.new(
      :product => Product.new("FTX998213"),
      :price => Payment.new(99.99, :PLN)
    )))
    transaction.add_position(LineItem.new(:item => Item.new(
      :product => Product.new("XF92131P"),
      :price => Payment.new(666.00, :PLN),
      :quantity => 2
    )))
    expect(transaction.total).to eq(Money.new(1431.99, :PLN))
  end
end
```

## Maszyny wirtualne w formie infrastruktury jako kodu

W przypadku chęci uruchomienia lokalnej maszyny wirtualnej, np. z Ubuntu na potrzeby developerskie, nie musimy spędzać roku na setupie i przygotowaniu maszyn. Wystarczy **Vagrant** i kilka chwil nad kodem:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.define "ubuntu" do |machine|
    machine.vm.box = "bento/ubuntu-22.04"
    machine.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
    machine.vm.network "forwarded_port", guest: 443, host: 8443, host_ip: "127.0.0.1"

    machine.vm.synced_folder ".", "/vagrant", type: "nfs", nfs_version: 4, nfs_udp: false

    machine.vm.provider :libvirt do |libvirt|
      libvirt.driver = "kvm"
      libvirt.cpu_model = "EPYC-Rome"
      libvirt.cpus = 2
      libvirt.memory = 2048
      libvirt.storage_pool_name = "kvm"
      libvirt.storage :file, :size => '20G'
    end

    machine.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install apt-transport-https ca-certificates btop htop git unzip wget curl build-essential gcc -y
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
    export PATH="/home/linuxbrew/.linuxbrew/bin:${PATH}"
  SHELL
  end
end
```

```shell
vagrant up && vagrant ssh
```

## CLI

A co w przypadku gdy chcemy napisać CLI np. takie do wykonywania prostych zapytań na bazie SQL?

Możemy do tego użyć kombinacji w postaci d**ry/cli** oraz **sequel'a** i **table_print'u**:

```ruby
#!/usr/bin/env ruby
require 'bundler/setup'
require 'dry/cli'
require 'sequel'
require 'json'
require 'yaml'
require 'tiny_tds'

module Valctl
  module CLI
    module Commands
      extend Dry::CLI::Registry

      class Version < Dry::CLI::Command
        desc 'Print version'

        def call(*)
          puts '0.6.0'
        end
      end

      class Get < Dry::CLI::Command
        desc 'Query the database'

        argument :server, required: true, desc: 'The server to connect to'
        argument :database, required: true, desc: 'The database to connect to'
        argument :query, required: true, desc: 'The query to run'
        option :format, default: 'table', values: %w[json table], desc: 'The output format'

        def call(server:, database:, query:, format: 'table', **)
          db = Sequel.connect(
            adapter: 'tinytds',
            host: server,
            database:,
            user: '',
            password: ''
          )

          forbidden = %w[delete drop truncate update]

          if forbidden.any? { |word| query.downcase.include?(word) }
            (puts 'Error: Forbidden query')
          else
            (
              begin
                result = []

                db[query].all do |row|
                  row.each do |key, value|
                    row[key] = value.to_f if value.is_a?(BigDecimal)
                  end

                  result << row
                end

                db.disconnect

                case format
                when 'json'
                  puts JSON.pretty_generate(result)
                when 'table'
                  require 'table_print'
                  tp result
                end
              rescue StandardError => e
                puts "Error: #{e.message}"

                db.disconnect
              ensure
                db.disconnect
              end)

          end
        end
      end

      register 'version', Version, aliases: %w[v -v --version]
      register 'get', Get, aliases: %w[q -g --get]
    end
  end
end

Dry::CLI.new(Valctl::CLI::Commands).call
```

```shell
$ ./valctl get 'dwh.company.local' 'staging' 'SELECT TOP 5 store, sale_id, sale_date FROM dbo.Sales'

STORE  | SALE_DATE  | SALE_ID
-------|------------|-------------
91094  | 2013-10-14 | 709391160738
188343 | 2014-11-14 | 775295660268
188343 | 2014-11-14 | 775295991936
188343 | 2014-11-14 | 775296592335
188343 | 2014-11-14 | 775296601326
```

## Podsumowanie

Jak widać świat **Ruby**, ma więcej do zaproponowania niż tylko **Rails'y**, a to tylko delikatny zalążek jego ekosystemu.

Pomijając fakt, że składnia Ruby jest sama w sobie dość prosta, to wraz z jego możliwościami meta-programowania i pisania DSL'i, sprawia, że kodowanie w tym języku jest nadzwyczaj przyjemne i niebywale efektywne, zwłaszcza w obszarze automatyzacji czy back-end development'u.

Owszem, może nie napiszemy w Nim własnych kontenerów czy systemu operacyjnego, ale od czego mamy **C**, **C++** czy **Rust'a**.
