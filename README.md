# docker for rails

# 特徴
- gemをコンテナ内に包含したコンテナ
- 最新のrailsで作りたい


# コンテナ作成

```
rm -rf .git
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

# rubyのバージョンを指定する

- コンテナ作成前にdockerhub.comにて、該当バージョンのalpine製のものを探す

https://hub.docker.com/_/ruby?tab=tags

- Dockerfileの先頭行を探したものに書き換える

# railsのバージョンを指定する

コンテナ作成前にGemfileのrails行を書き換える

```
gem "rails","6.0.2"
```