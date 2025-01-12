---
layout: post
title:  "はじめてのJekyll"
date:   2025-01-11 13:54:07 +0900
categories: Jekyll GitHub
---

## GitHub Pages で Jekyllやってみた

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

## Jekyll関連の環境変数設定と bundlerのインストール

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

最後に bundler というRuby gemをインストールします。

```
$ gem install bundler
```

## GitHubのWebサイトからリポジトリを作成

参考URL: [サイト用にリポジトリを作成する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-a-repository-for-your-site)

GitHubの自分のアカウントでGitHub
Pages用の空のリポジトリをあらかじめ作成しておきます。

以下のようにGitHubホーム画面の右上にある + マークをクリックして New Repository
を選択します。

![GitHubで新しいリポジトリを作成]({{ site.baseurl }}/assets/images/Screenshot_2025-01-11_18-10-37b.png)

つぎの画面で好きなリポジトリ名を記入します。ここでは renshu
という名前にしました。公開設定にするため Public のラジオボタンを選択しておきます。

![renshuというリポジトリを作成]({{site.baseurl}}/assets/images/Screenshot_2025-01-11_18-44-32.png)

緑色の Create repository ボタンを押して新しいリポジトリを作成します。

以下の画面に切り替わります。

![]({{site.baseurl}}/assets/images/Screenshot_2025-01-11_19-24-11.png)

これで renshu というリポジトリが作成されました。


## ローカルにJekyllをインストールしてWebサイトを構築

(参考URL: [サイトを作成する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-your-site))

まずはターミナルでGit用のリポジトリを作成します。以下では
`/home/user名/Project/`
で作業しています。まずはこのProjectディレクトリに移ります。

```
$ cd ~/Project/
```

つぎにこのProjectディレクトリに renshu ディレクトリを作成して git
の初期化をやります。

```
$ git init renshu
Initialized empty Git repository in /home/user/Project/renshu/.git/
```

新規作成された renshu ディレクトリに移動します。

```
$ cd renshu
```

この時点でカレントディレクトリは以下のとおりです

```
$ pwd
/home/user/Project/renshu
```

GitHub PagesではPages用に公開するディレクトリを指定できます。renshu以下の
docs ディレクトリを公開ディレクトリとしてあとでGitHubの設定画面で設定します。

```
$ mkdir docs
$ cd docs/
```

この docs ディレクトリにおいて以下の Jekyll コマンドを実行します。

```
$ jekyll new --skip-bundle .
New jekyll site installed in /home/user/Project/renshu/docs.
Bundle install skipped.
```

`jekyll new --skip-bundle` の後ろに `.` (ドット)を入れるのを忘れないでください。

`/home/user/Project/renshu/docs` の中のファイル構成は以下のとおりです。

```
$ tree .
.
├── 404.html
├── Gemfile
├── _config.yml
├── _posts
│   └── 2025-01-11-welcome-to-jekyll.markdown
├── about.markdown
└── index.markdown

2 directories, 6 files
```

この中にある Gemfile を編集します。エディタで Gemfile を開いてください。
13〜14行目あたりに有用な指示が書いてあります。

```
# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
```
(訳) GitHub Pagesを使用する場合は、上記の「gem "jekyll"」を削除し、以下の行のコメントを外してください。アップグレードするには、bundle update github-pagesを実行します。

上の指示ではなくここではGitHubのマニュアルのほうに従って Gemfileの10行目の冒頭に # を加えて
```
# gem "jekyll", "~> 4.3.4"
```
として jekyllの新しすぎるgemをインストールしないようにします。

github-pages
という名前のRubyのGemを利用する必要があるのですが、これの最新バージョンを以下のWebサイトで確認します。

[https://pages.github.com/versions/](https://pages.github.com/versions/)

私が作業した時点では github-pages の最新バージョンは `232` でした。

この情報に基づいて、Gemfileの15行目あたりの # gem
"github-pages", group: :jekyll_plugins
と書いてあるところを修正します。まずはアンコメントします。すなわち冒頭の # を削除し、さらに以下のようにgithub-pagesのバージョン情報 "~> 232", を挿入します。

```
gem "github-pages", "~> 232", group: :jekyll_plugins
```

Gemfile を保存してエディタを閉じます。

編集後Gemfileは以下のようになっています。
```
$ cat Gemfile
source "https://rubygems.org"
# Hello! This is where you manage which Jekyll version is used to run.
(中略)
# Happy Jekylling!
# gem "jekyll", "~> 4.3.4"
# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.5"
# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
gem "github-pages", "~> 232", group: :jekyll_plugins
# If you have any plugins, put them here!
(後略)
```

このGemfileのあるディレクトリ docs から以下のようにして
Jekyllを以下のようにしてインストールします。

```
$ bundle install
Fetching gem metadata from https://rubygems.org/...........
Resolving dependencies...
Bundle complete! 7 Gemfile dependencies, 98 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
```

ここで bundle info jekyll を実行したらインストールされた
Jekyllのバージョンとインストール先のディレクトリの情報が得られました。

```
$ bundle info jekyll
  * jekyll (3.10.0)
	Summary: A simple, blog aware, static site generator.
	Homepage: https://github.com/jekyll/jekyll
	Path: /home/user/gems/gems/jekyll-3.10.0
(後略)
```

docsディレクトリから ls -a を実行すると、さきほどの bundle install によって、
.gitignore と Gemfile.lock というファイルが作成されていました。

この .gitignore ファイルをエディタで開いて、一番下に Gemfile.lock
を追加します。追加後は以下のようになります。

```
$ cat .gitignore
_site
.sass-cache
.jekyll-cache
.jekyll-metadata
vendor
Gemfile.lock
```

またここで作成しているWebサイトは https://futotak.github.io/renshu
というサブディレクトリで公開するつもりなので、サブディレクトリの設定を
_config.yml ファイルにて設定します。
_config.ymlファイルの baseurl で始まる行に
"/renshu"というサブディレクトリの情報を入れておきます。

```
baseurl: "/renshu" # the subpath of your site, e.g. /blog
```

そのほかのデータを適宜書き換えておきます。

私は _config_yml を以下のようにしました。

```
(前略)
title: futotak's renshu website
# email: your-email@example.com
description: >- # this means to ignore newlines until "baseurl:"
  futotak's renshu website
baseurl: "/renshu" # the subpath of your site, e.g. /blog
domain: futotak.github.io
url: https://futotak.github.io # the base hostname & protocol for your site, e.g. http://example.com
#twitter_username: jekyllrb
github_username:  futotak
(後略)
```

## サイトをローカルでビルドして確認

参考URL: [サイトをローカルでビルドする](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll#building-your-site-locally)

docsディレクトリから以下のコマンドを実行します。

```
$ bundle exec jekyll serve
Configuration file: /home/user/Project/renshu/docs/_config.yml
To use retry middleware with Faraday v2.0+, install `faraday-retry` gem
            Source: /home/user/Project/renshu/docs
       Destination: /home/user/Project/renshu/docs/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
                    done in 0.259 seconds.
 Auto-regeneration: enabled for '/home/user/Project/renshu/docs'
    Server address: http://127.0.0.1:4000/renshu/
  Server running... press ctrl-c to stop.
```

上記出力に Server address が書いてあります。
[http://127.0.0.1:4000/renshu/](http://127.0.0.1:4000/renshu/)
ここにWebブラウザでアクセスします。
ビルドにある程度時間がかかるようなので、エラーが出たらしばらく待ってから再度Webブラウザでアクセスします。

![]({{site.baseurl}}/assets/images/Screenshot_2025-01-11_23-14-45.png)

このようにしてWebサイトが作成されました。Welcome to
Jekyllの記事はJekyllがあらかじめ作成してくれているテスト記事です。また、さきほど作業した
_config_yml のデータが使われているようです。

## Git で addとcommitしてリモートにプッシュする

上記 renshu ウェブサイト用のローカルのディレクトリ構成は、/home/user/Project/ 以下に
renshu ディレクトリがあり、その中に docs
ディレクトリがあります。docsの中にJekyll
関連のファイル群があるのに対して、Gitが管理している"本拠地"のディレクトリは
renshu
ディレクトリのほうです。gitコマンドを実行するのは、このrenshuディレクトリから行います。

git add 及び git commit コマンドを実行しましょう。

```
$ git add .
$ git commit -m "Initial commit of renshu website"
[main (root-commit) 2bdd086] Initial commit of renshu website
 7 files changed, 171 insertions(+)
 create mode 100644 docs/.gitignore
 create mode 100644 docs/404.html
 create mode 100644 docs/Gemfile
 create mode 100644 docs/_config.yml
 create mode 100644 docs/_posts/2025-01-11-welcome-to-jekyll.markdown
 create mode 100644 docs/about.markdown
 create mode 100644 docs/index.markdown
```

つづいてGitのリモートとして
GitHubでさきほど作成したリポジトリを指定します。
ここでは SSH でGitHubにアクセスするタイプのリモートを登録します。

https://github.com/[ユーザー名]/renshu にアクセスすると、 Quick setup
のところで HTTPSとSSHのどちらかを選択するボタンがあるので、
SSHの方を押します。

![]({{site.baseurl}}/assets/images/Screenshot_2025-01-11_19-24-11.png)

下のほうに git remote add
のやり方が書いてあるので、それをローカルのrenshuディレクトリから実行します。

```
$ git remote add origin git@github.com:futotak/renshu.git
```

このあとリモートにプッシュします。

```
$ git push -u origin main
```
SSH鍵のパスフレーズが聞かれるので入力します。するとGitHubの renshu
リポジトリにローカルで作業していた内容がアップロードされます。

## GitHub Pagesを公開する設定

https://github.com/[ユーザー名]/renshu にアクセスすると docs
ディレクトリが新規に作成されているはずです。
まだWebサイトを公開する設定をしていないので、その設定作業をやります。

![]({{site.baseurl}}/assets/images/Screenshot_2025-01-12_15-06-16b.png)

上の画面で Settings タブをクリックします。つぎの画面で、左のメニュー欄から
Pages のところをクリックします。

![]({{site.baseurl}}/assets/images/Screenshot_2025-01-12_15-19-24b.png)

Pagesをクリックすると GitHub Pages の設定画面に移ります。

![]({{site.baseurl}}/assets/images/Screenshot_2025-01-12_15-28-29b.png)

真ん中あたりの `None` と表示されているドロップダウンメニューを押して、 main
ブランチのところをクリックしてください。

今クリックしたところのすぐ右の `/(root)`
のドロップダウンをクリックして、`/docs` を以下の画面のように選びます。

![]({{site.baseurl}}/assets/images/Screenshot_2025-01-12_15-35-05b.png)

右隣の`Save`ボタンを押します。

しばらく待つとウェブサイトがJekyllでビルトされてアクセスできるようになります。
"Your site is lite at https://futotak.github.io/renshu"
とウェブサイトがライブになったとメッセージが出ました。となりの `Visit
site`ボタンをクリックするとウェブサイトにアクセスできます。

![]({{site.baseurl}}/assets/images/Screenshot_2025-01-12_15-53-34b.png)

以下はGitHub Pagesのウェブサイトrenshu にアクセスしているところです。

![]({{site.baseurl}}/assets/images/Screenshot_2025-01-12_16-01-10.png)
