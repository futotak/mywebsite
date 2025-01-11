---
layout: post
title:  "はじめてのJekyll"
date:   2025-01-11 13:54:07 +0900
categories: Jekyll GitHub
---

# GitHub Pages で Jekyllやってみた

GitHub には静的Webサイトをホスティングする Pages
という機能があります。デフォルトでは Jekyll というRuby
で動く静的サイトを作成するジェネレーターがWebサイトを構築してくれます。
これを触ってみたので、以下に手順を書いていきます。

## 私の使用している環境

OSはFedora
40です。GitHubアカウント開設済みで、sshでローカルのGitからpushする仕組みも整備してあります。ホームディレクトリの .gitconfigで gitの defaultBranchは mainに設定してあります。

## Rubyと関連するパッケージのインストール

Fedoraでは以下のコマンドでJekyllを利用する前提となるRuby関連のパッケージがインストールされます。

```
$ sudo dnf install ruby ruby-devel openssl-devel redhat-rpm-config gcc-c++ @development-tools
```
(参考): [https://jekyllrb.com/docs/installation/other-linux/#fedora](https://jekyllrb.com/docs/installation/other-linux/#fedora)

## Jekyll関連の環境変数設定と Jekyllのインストール

上記の参考URLだと分かりにくいですが、今やったFedoraの`sudo dnf install ...`
のあとに [Ubuntu向けの記事](https://jekyllrb.com/docs/installation/ubuntu/)の続きに書いてあるコマンドを実行します。

```
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

上のコマンドは何やっているのかというと、ホームディレクトリの .bashrc
ファイルの末尾に以下のような3行を追加しています。

```
$ tail -3 ~/.bashrc
# Install Ruby Gems to ~/gems
export GEM_HOME="$HOME/gems"
export PATH="$HOME/gems/bin:$PATH"
```

私はRubyには詳しくないのですが、Ruby Gem
はRubyのアプリ同士でバージョンの競合などの不都合が起きないようにするための仕組みです。
上の.bashrc内にある環境変数のおかげで、Rubyで書かれた拡張パッケージみたいなものが、GEM_HOMEで指定されたディレクトリ ~/gems に保存されます。
また、Ruby Gem関連のコマンドのパスは `$HOME/gems/bin`
になってホームディレクトリに入っているものが使用されるようになります。
最後に `source ~/.bashrc`
を実行して現在のターミナルでもこの環境変数の設定が有効になります。

これで環境変数が設定できてJekyllはホームディレクトリの gems
ディレクトリに入ることになります。

最後に Jekyll 自身と bundler というRuby gemをインストールします。

```
$ gem install jekyll bundler
```



