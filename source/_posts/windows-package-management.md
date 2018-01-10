---
title: Windwos の PackageManagement を使ってみる
comments: false
date: 2016-08-06 00:00:00
category: 開発環境
tags:
- Windows
- PackageManagement
- PowerShell
- 開発環境
---
## 今回のゴール

PowerShell の PackageManagement を使って、パッケージ管理できるようにする。


## はじめに

Windows 10 からは、*PowerShell* のモジュールとして **PackageManagement** という *パッケージ管理システム* がデフォルトで入っているらしい。

ちょうどクリーンインストールしたところだし、それを使って環境構築してみることにする。

ちなみに、win10 より前のバージョンでも、 `Windows Management Framework 5.0` とやらを入れれば利用できるようです。

（この記事内では、PowerShell のコマンドを記載するとき可読性のため大文字小文字書きわけてますが、PowerShell 自体は大文字小文字区別しないので、実際に打つ際は全部小文字でも問題ありません。）


## 手順

* PowerShellの設定

* PackageProviderの追加・確認

*  パッケージ（アプリケーション） の検索・追加・確認・削除

## PowerShellの設定

PowerShellのスクリプト実行に関するポリシーを変更する。

PowerShell を **管理者として実行する** で起動。

### 現在の設定を確認

```PowerShell
> Get-ExecutionPolicy
```

初期値は `Restricted` （全てのスクリプト実行禁止） になっている。

### 設定を変更

```PowerShell
> Set-ExecutionPolicy RemoteSigned
```

ポリシーを `RemoteSigned` （署名付きのスクリプトとローカルに保存されているスクリプトの実行を許可）へ変更する。

ポリシーの意味やその他の設定値に関しては、下記参考サイトなど詳しく書いてある。

参考: [WindowsでPowerShellスクリプトの実行セキュリティポリシーを変更する - @IT](http://www.atmarkit.co.jp/ait/articles/0805/16/news139.html)

## PackageProviderの追加・確認

PackageManagement では、パッケージの配布元を PackageProvider という形で管理して、そこ経由でパッケージを取得してくる。

で、この PackageProvider として、Windowsのパッケージ管理システムとして定番の **Chocolatey** が利用できる。

なので、とりあえず Chocolatey を PackageProvider として追加する。

### 利用できる（未登録の） PackageProvider 一覧

```PowerShell
> Find-PackageProvider
```

### PackageProvider 登録

```PowerShell
> Get-PackageProvider Chocolatey
```

###  登録済み PackageProvider 一覧

```PowerShell
> Get-PackageProvider
```


## パッケージ（アプリケーション） の検索・追加・確認・削除

なんとなく *nodist* で試してみる。

### パッケージの検索

```PowerShell
> Find-Package -name nodist
```

### パッケージのインストール

```PowerShell
> Install-Package -name nodist
```

アプリケーション付属のインストーラーを裏で実行しているだけっぽい？

### インストール済みパッケージ一覧

```PowerShell
> Get-Package
```

### パッケージのアンインストール

```PowerShell
> Uninstall-Package -name nodist
```

こちらもどうやらアプリケーション付属のアンインストーラーが起動しているだけっぽい？ので、アプリケーションごとに挙動が違ったりする。

ちなみにものによってこれだけではアプリケーション本体は消えないものがあるんで、そういったものはこれに加え通常のアンインストール手順で消してあげる必要がある。

## おわりに

これである程度のアプリケーションを Package Management で管理できるようになりました。

通常のGUIインストーラーからインストールするときに選べるインストールオプションが、この方法でインストールするとすべてデフォルト設定になるっぽくて、例えば *Git for Windows* とかは個人的にインストール時の設定をある程度変えたいので、そういうアプリケーションにはちょっと使いづらい感じ。逆にインストールオプションがあんまり無いようなor重要じゃないアプリケーションはこっちのほうが断然楽だと思います。

まだちょっと触った感じですが、便利は便利だと思うので、なんか「いい感じ」の付き合い方を見つけたいです（windowsのbashはまだしばらく微妙そうだし・・・）。


## 参考URL

* [Windows PowerShell でパッケージ管理(Package Management)してみる - はしくれエンジニアもどきのメモ](http://cartman0.hatenablog.com/entry/2016/03/20/233719)

* [Windows 10でPackageManagement (OneGet) + Chocolateyでアプリ管理 - rcmdnk's blog](http://rcmdnk.github.io/blog/2016/03/11/computer-windows-chocolatey-packagemanagement/)


## 余談

PowerShellデフォルトの、エラー出力が赤字黒背景なのがなんかもうとにかく苦手で、とりあえずそこの設定の変更だけ調べたのでついでにメモ。

下記ファイルが `.bashrc` 的な存在になるっぽいので作成。

```
%UserProfile%\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```

そこに下記の内容を記述。

```PowerShell
$host.privatedata.errorbackgroundcolor = "DarkMagenta"
$host.privatedata.errorforegroundcolor = "Yellow"
```

これで心の平穏は保たれた・・・

* [Windows PowerShell でエラーメッセージの色を変えるには](https://makandat.wordpress.com/2015/05/31/windows-powershell-%E3%81%A7%E3%82%A8%E3%83%A9%E3%83%BC%E3%83%A1%E3%83%83%E3%82%BB%E3%83%BC%E3%82%B8%E3%81%AE%E8%89%B2%E3%82%92%E5%A4%89%E3%81%88%E3%82%8B%E3%81%AB%E3%81%AF/)

* [Windows PowerShell のプロンプトに色を付ける](http://shirokichi2.blog.so-net.ne.jp/2014-03-15)
