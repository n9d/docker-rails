
# docker for rails

## 特徴
- gemをコンテナ内に包含したコンテナ
- 最新のrailsで作りたい

## バージョンを指定したいとき

### rubyのバージョンを指定する

- コンテナ作成前にdockerhub.comにて、該当バージョンのalpine製のものを探す

https://hub.docker.com/_/ruby?tab=tags

- Dockerfileの先頭行を探したものに書き換える

### railsのバージョンを指定する

コンテナ作成前にGemfileのrails行を書き換える

```
gem "rails","6.0.2"
```

### dbのバージョンを指定する
- コンテナ作成前にdockerhub.comにて、該当バージョンのalpine製のものを探す
- docker-composeのbuild行を探したものに書き換える


## sqlite3

### コンテナ作成

```
cp docker-compose-sqlite3.yml docker-compose.yml
docker-compose build
docker-compose run -p 3000:3000 api sh
```
コンテナ内に入った後
```
rails new . -fG
exit
```
ここで出来上がった Gemfile,Gemfile.lockでコンテナを作り直す
```
docker-compose build
```

### コンテナ起動

```
docker-compose up
```

- http://localhost:3000/ にてアクセスできる

## postgresql

### コンテナ作成

```
cp docker-compose-postgresql.yml docker-compose.yml
docker-compose build
docker-compose run -p 3000:3000 api sh
```
- コンテナ内に入った後

```
rails new . -fG -d postgresql
ruby -e 'fn="config/database.yml"; s=open(fn).read.gsub(/(^default: &default$)/){"#{$1}\n  host: <%= ENV.fetch(\"DB_HOST\") %>\n  port: <%= ENV.fetch(\"DB_PORT\") %>\n  username: <%= ENV.fetch(\"DB_USER\") %>\n  password: <%= ENV.fetch(\"DB_PASSWORD\") %>"}; open(fn,"w").write(s)'
```
- ここで出来上がった Gemfile,Gemfile.lockでコンテナを作り直す
```
docker-compose build
```

### コンテナ起動

```
docker-compose up
```

- http://localhost:3000/ にてアクセスできる


## mysql

### コンテナ作成

```
cp docker-compose-mysql.yml docker-compose.yml
docker-compose build
docker-compose run -p 3000:3000 api sh
```
- コンテナ内に入った後

```
rails new . -fG -d mysql
ruby -e 'fn="config/database.yml"; s=open(fn).read.gsub(/  username: root\n  password:\n  host: localhost\n/,"").gsub(/(^default: &default$)/){"#{$1}\n  host: <%= ENV.fetch(\"DB_HOST\") %>\n  port: <%= ENV.fetch(\"DB_PORT\") %>\n  username: <%= ENV.fetch(\"DB_USER\") %>\n  password: <%= ENV.fetch(\"DB_PASSWORD\") %>"}; open(fn,"w").write(s)'
```
- ここで出来上がった Gemfile,Gemfile.lockでコンテナを作り直す
```
docker-compose build
```

### コンテナ起動

```
docker-compose up
```

 - http://localhost:3000/ にてアクセスできる



## apiサーバ

- ほぼsqlite3と同じで `rails new` 時に `--api` をつけるだけ

```
rails new . -fG --api
```

### テスト

- Helloコントローラを作成

```
rails g controller Hello
ruby -e 'fn="app/controllers/hello_controller.rb";s=open(fn).read.gsub(/^end\Z/,"  def index; render json:{hello:\"world\"}; end;\nend"); open(fn,"w").write(s)'
ruby -e 'fn="config/routes.rb";s=open(fn).read.gsub(/^end\Z/,"  get \"hello\", to: \"hello#index\"\nend"); open(fn,"w").write(s)'
```

```
rails s -b 0.0.0.0
```

```
$ curl http://localhost:3000/hello
{"hello":"world"}
```

