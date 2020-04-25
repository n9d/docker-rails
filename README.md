# docker for rails

# 特徴
- gemをコンテナ内に包含したコンテナ
- 最新のrailsで作りたい

# バージョンを指定したいとき

## rubyのバージョンを指定する

- コンテナ作成前にdockerhub.comにて、該当バージョンのalpine製のものを探す

https://hub.docker.com/_/ruby?tab=tags

- Dockerfileの先頭行を探したものに書き換える

## railsのバージョンを指定する

コンテナ作成前にGemfileのrails行を書き換える

```
gem "rails","6.0.2"
```

## dbのバージョンを指定する
- コンテナ作成前にdockerhub.comにて、該当バージョンのalpine製のものを探す
- docker-composeのbuild行を探したものに書き換える


# sqlite3

## コンテナ作成

```
docker-compose build
docker-compose run -p 3000:3000 api sh
```
コンテナ内に入った後
```
rails new . -f -G
exit
```
ここで出来上がった Gemfile,Gemfile.lockでコンテナを作り直す
```
docker-compose build
```

## コンテナ起動

```
docker-compose up
```

- http://localhost:3000/ にてアクセスできる

# postgresql

## 前準備

- Dockerfileの `for sqlite3`をコメントアウト、`for postgresql` のapkをコメントインする

- docker-compose.yml.postgresql を docker-compose.ymlにする

```
cp docker-compose.yml.postgresql docker-compose.yml
```


## コンテナ作成

```
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

## コンテナ起動

```
docker-compose up
```

- http://localhost:3000/ にてアクセスできる


# mysql


## 前準備

- Dockerfileの `for sqlite3`をコメントアウト、`for mysql` のapkをコメントインする

- docker-compose.yml.mysql を docker-compose.ymlにする

```
cp docker-compose.yml.mysql docker-compose.yml
```


## コンテナ作成

```
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

## コンテナ起動

```
docker-compose up
```

- http://localhost:3000/ にてアクセスできる
