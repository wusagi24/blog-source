---
title: Raspberry Pi をローカルサーバとして利用できるようにする
comments: false
date: 2018-12-14 17:20:52
category: 環境構築
tags: 
- RaspberryPi
- Server
- Linux
- Raspbian
---

（あくまで "ローカルネットワーク上" で運用する）サーバが必要になったので、使っていなかったラズパイを引っ張り出してきてサーバにしました。

その際の手順メモを残しておこうと思います。


## 0. 環境

### ラズパイ周り

* ハードウェア

  [Raspberry Pi 2 Model B](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/)

* microSD

  SDHC 32GB

* OS

  [Raspbian Stretch Lite](https://www.raspberrypi.org/downloads/raspbian/) (Version: November 2018)


### 作業環境

* OS

  Windows 10

* ソフトウェア
  - [SD Card Formatter](https://www.sdcard.org/downloads/formatter_4/)
  - [Etcher](https://www.balena.io/etcher/)


## 1. SD カードのフォーマット

前提: [利用できる SD カードについて（ラズパイ公式）](https://www.raspberrypi.org/documentation/installation/sd-cards.md)

まず SD カードをフォーマット。  
公式でも推奨されている [**SD Formatter**](https://www.sdcard.org/downloads/formatter_4/) を利用しました。

※ 今回は 32GB の SD カードだったので関係ないですが、64GB 以上の SD カードだとラズパイが非対応の exFAT フォーマットになってしまうので注意が必要なようです。  
[その場合の対応方法（ラズパイ公式）](https://www.raspberrypi.org/documentation/installation/sdxc_formatting.md)


## 2. SD カードに OS イメージを書き込む

今回はサーバとしての利用なので、OS はヘッドレスな [**Raspbian Stretch Lite**](https://www.raspberrypi.org/downloads/raspbian/) を使用。

基本的に[ラズパイ公式のここ](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)に手順が丁寧に書いてあるので、これに従って作業。

イメージの書き込みには、上記ページで推奨されている [**Etcher**](https://www.balena.io/etcher/) というソフトを利用しました。


## 3. 初回起動時から SSH 接続できるようにする

イメージを書き込んだ SD カードに改めて作業 PC からファイルアクセスして、ルートディレクトリに `ssh` というファイル名の空ファイルを作成。

（Raspbian 初回起動時にそのファイルの有無をフラグとして見ているだけのようなので、ファイル名だけは正確に、最もやり易い方法で空ファイルを作成すればOKだと思われます）


## 4. Raspberry Pi を電源と LAN に有線接続する

ラズパイに先ほど作成した SD カードを挿入。

さらに、ローカルネットワークに繋ぐための LAN ケーブルと、電源ケーブル（micro USB）を接続。  
電源が接続されたことでラズパイが自動起動します。

この後ラズパイに物理的に触ることはないので、良きところに設置しておきます。


## 5. Raspberry Pi の IP アドレスを確認する

（ラズパイと同じサブネットに所属している）作業 PC から、ラズパイに SSH 接続するために、ラズパイに割り振られた IP アドレスを確認。

### 以下、**管理者権限で実行した**コマンドプロンプトでの作業

#### I. ARP テーブルのクリア

```cmd
arp -d *
```

#### II. LAN 内で使われている IP アドレスを ARP テーブルに記録する

```cmd
for /l %i in (0,1,255) do ping -w 1 -n 1 192.168.1.%i && arp -a 192.168.1.%i
```
`xxx.xxx.xxx.%i` の `xxx` の部分は、所属しているネットワーク（サブネット）に合わせて書き換える。

#### III. ARP テーブルの確認

```cmd
arp -a
```

MAC アドレスが `B8:27`～ から始まるものがラズパイ


## 6. SSH クライアントから SSH 接続する

前項で調べた Raspberry Pi の IP アドレスに、SSH クライアントを用いて SSH 接続する。

Raspberry Pi のデフォルトでは、
* ユーザ: `pi`
* パスワード: `raspberry`


## 7. IP アドレスの固定

`/etc/dhcpcd.conf` で下記項目を環境に合わせて設定。
```
interface eth0
static ip_address=xxx.xxx.xxx.xxx
static routers=xxx.xxx.xxx.xxx
static domain_name_servers=xxx.xxx.xxx.xxx
```


## 8. raspi-config

https://www.raspberrypi.org/documentation/configuration/raspi-config.md

```sh
sudo raspi-config
```
で、設定画面が開く。

とりあえず以下の設定をしました。

* システム領域を SD カードの容量に合わせ拡張

  `Advanced Options` > `EXPAND FILESYSTEM` （再起動が必要）  
  初回起動時に自動的に実行されるらしいんですが、念のため手動で実行。

* 対応言語に日本語を追加

  `Localisation Options` > `CHANGE LOCALE` > `ja_JP.UTF-8` にチェックを入れて設定を進める  
  この手の設定画面あるあるですが、チェックボックスにチェックを入れるのは Enter キーではなく Space キー。

* タイムゾーンの変更

  `Localisation Options` > `CHANGE TIME ZONE` > `Asia` > `Tokyo` を選択


## 9. サーバとして最低限の初期設定もろもろ

ここからはラズパイどうこうというより、Linux サーバの話だと思うので端折り

* パッケージの最新化
* 作業用ユーザの作成、 sudo 権限付与、 SSH 接続用の公開鍵の設定など
* ssh 許可周りの設定（デフォルトユーザや root での接続の無効化など）

などなど


## 以上
