---
title: CreateReactApp によるReactチュートリアルの写経
comments: false
date: 2016-08-22 00:00:00
category: React
tags:
- js
- React
- 写経
---
## 今回のゴール
Reactのチュートリアルを、CreateReactApp による開発環境で写経する。

## ソース

こちらの記事の内容を実際に書いたソースは [こちら](https://github.com/wusagi24/react-tutorial_by_create-react-app) になります。

基本的に、チュートリアルに登場するソースごとに、 commit を入れてあります。


## はじめに

[Reactのチュートリアル](https://facebook.github.io/react/docs/tutorial-ja-JP.html)を、最近公開された 「[Create React App](https://facebook.github.io/react/blog/2016/07/22/create-apps-with-no-configuration.html)」 というReactプロジェクトのスターターコマンドを利用した開発環境（+ ES2015の書き方）で写経しました。

この場合、写経というより書き換えなんでしょうか。

ということで、基本的に写経なので、 [チュートリアルのサイト](https://facebook.github.io/react/docs/tutorial-ja-JP.html) と [前項のコミットログ](https://github.com/wusagi24/react-tutorial_by_create-react-app/commits/master) を突き合わせて読めば比較的後からでも追いやすいかなと思うのですが、一部補足的にちょっと書き留めておきます。


## プロジェクトの作成

CreateReactApp インストールして、プロジェクトのひな形を作って、サンプル用のファイルを削除する。

### create-react-app コマンドのインストール

```bash
$ npm install -g create-react-app
```

### create-react-app コマンドでプロジェクトを作成

コミット 「[create project](https://github.com/wusagi24/react-tutorial_by_create-react-app/commit/f8564c426ddfbcef50b7ba46f8674b68c78515c0)」

```bash
$ create-react-app react_tutorial
$ cd react_tutorial
```

### サンプルのファイルを削除

コミット 「[remove default source file](https://github.com/wusagi24/react-tutorial_by_create-react-app/commit/7a0090ce505dad9e506d90e01af9a61d43b4a672)」

```bash
$ rm src/*
```


## index.html

コミット 「[modify index.html](https://github.com/wusagi24/react-tutorial_by_create-react-app/commit/282cc33a420186e0ae6e67d16257e0de9ec7f677)」

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8" />
    <title>React Tutorial by create-react-app</title>
</head>
<body>
    <div id="content"></div>
</body>
</html>
```

index.html を、チュートリアルで出てくるものを元にした上記内容に書き換え。

チュートリアルでは jsパッケージの取得を CDN から、依存関係の解消を html上のタグの記述順 で行っていますが、CreateReactAppでは [webpack](http://webpack.github.io/) が入っているので、今回はパッケージの取得を npm で、依存関係の解消を webpack で行うことにします。

というわけで、jsの読み込み部分はチュートリアル記載のものからばっさりカット！

あと、チュートリアルではjsをhtml内に記述していますが、さすがに面倒くさいので別ファイル ( `src/index.js` ) に切り出しています。

この環境では、index.html 内に scriptタグ を記述しなくても、ビルド時に自動的に `src/index.js` を読み込むタグが挿入されます。


## jsファイルについて

コミット 「[tutorial1.js](https://github.com/wusagi24/react-tutorial_by_create-react-app/commit/ccf8e3011d3799d6eeab06696b5de07e95f71f53)」

webpack による依存性解決と ES2015 の class 記法を使用するので、js ファイルをコンポーネントごとに切り分けて作成、import/export にて組み合わせています。

エントリーポイントとなる `src/index.js` で ReactDOM でのレンダリングを行い、そこでルートコンポーネント（今回は `CommentBox` ）を import しています。


## ライブラリの使用

コミット 「[add library 'remarkable'](https://github.com/wusagi24/react-tutorial_by_create-react-app/commit/124d56fff12dcc808c1021008bf7bdca5bb870ca)」

```bash
$ npm install -S remarkable
```

コミット 「[tutorial6.js](https://github.com/wusagi24/react-tutorial_by_create-react-app/commit/c0d0ac182a403f8a701798cb65530ec5116f8a76)」

npm でライブラリをダウンロードしてきて、使用する jsファイル 内で import で読み込んでいます。


## APIサーバ

ソースの README にも記載しましたが、[チュートリアルで用意されているAPIサーバプログラム](https://github.com/reactjs/react-tutorial)を、そのまま同一ドメインのAPIとして利用するのはこの環境だとちょっと面倒くさいので、別ドメインのAPIとして動かしてアクセスすることにしました。

```bash
$ git clone https://github.com/reactjs/react-tutorial.git react-tutorial-server
$ cd react-tutorial-server
$ npm install
$ PORT=3001 node server.js
```

コミット 「[modify comments api url](https://github.com/wusagi24/react-tutorial_by_create-react-app/commit/ef1010a0328299a36f548e13cd04bcbbce60897b)」

幸いこのチュートリアル用APIサーバプログラムは CORS 対応されているので、丸っとサンプルソース持ってきて、別ポートでサーバを起動。APIへのアクセスアドレスをそちらに向けてあげるだけで大丈夫です。


## アロー関数の利用

コミット 「[tutorial13.js](https://github.com/wusagi24/react-tutorial_by_create-react-app/commit/3b4c971cb9d55e034a664387d996f57dc33fee71)」

チュートリアルだと、 `$.ajax` 内のコールバック関数を `bind()` していますが、

```js
function (arg) {
    // code
}
```

でなく、

```js
(arg) => {
    // code
}
```

と、ES2015 のアロー関数を使うと、自動的に this がバインドされるのでそっちで書いてます。

あとついでに、コールバックの設定を Deferred の `done` `fail` で外に出す書き方に変えてます。


## class内メソッドの非同期呼び出し

コミット 「[tutorial16.js](https://github.com/wusagi24/react-tutorial_by_create-react-app/commit/087ac2a4ce58a2bfe991937f7c0f8bc106f401f8)」

class 記法を利用する際、onClickイベントなんかでクラス内メソッドを設定する場合、そのまま

```js
onChange={this.handleAuthorChange}
```

とかやると、対象メソッド内で js名物の **this** 迷子になるので、

```js
onChange={this.handleAuthorChange.bind(this)}
```

と、 this を `bind()` しています。


## おわりに

CreateReactApp は、ほんと素早く React 開発始められるし、開発・ビルドまわりで必要なものは最低限そろってるし、React 入門にはもってこいだと思います。


## 参考URL

* [チュートリアル - React](https://facebook.github.io/react/docs/tutorial-ja-JP.html)
* [Create React App - github](https://github.com/facebookincubator/create-react-app)
