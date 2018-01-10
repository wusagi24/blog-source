---
title: Docker による Ruby on Rails の開発環境
date: 2017-11-08 01:30:55
category: 環境構築
tags:
- Rails
- Docker
- 開発環境
comments: false 
---
[ Github: [rails-dev-docker](https://github.com/wusagi24/rails-dev-docker) ]

初めて Ruby on Rails を触る機会があったので、Docker で開発実行環境を立ててみました。

基本的に自分で使うために、急ごしらえででっち上げたので汎用性はないです。

DockerHub の Rails Image には非推奨的なことが書いてあったので、Ruby の Image をベースに自前で Dockerfile 書いてます。

docker-compose を使ってるのは、MySQL や PostgreSQL を DB に利用する場合、別コンテナとして外出ししやすいようにです。（結局 SQLite3 使いましたが…
なにかと便利なので、最近何かしらの開発実行環境を Docker で立てるときには docker-compose 使うようにしてます。

Rails に関しては改めて何か書くかもしれませんが、想像以上に "魔法" が強くて、正直今はよく分かんねーでございます。
