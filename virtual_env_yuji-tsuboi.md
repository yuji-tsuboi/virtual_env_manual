# <font color="DeepSkyBlue">＜環境構築手順書＞</font>
## 全体の流れ (目次)  
### 仮想環境内で使用するLaravelアプリケーションに認証機能を実装する
1. Laravelの認証機能実装  

### 仮想環境を構築する
2. VirtualBoxのインストール    
3. Vagrantのインストール  
4. boxの取得  
5. 初期設定ファイルの編集  
6. プラグインのインストール  

### 構築した仮想環境内にPHP開発環境を整える
7. 関連パッケージをまとめて追加  
8. 仮想環境内PHPインストール  
9. 仮想環境内Composerインストール  

### 作成したLaravelアプリケーションを仮想環境内に導入、操作可能にする
10. Laravelアプリケーションのコピー  
11. 仮想環境内MySQLのインストール  
12. 仮想環境下MySQLパスワード設定  
13. 仮想環境内データベース作成  
14. 仮想環境内Nginxインストール  
15. nginx設定ファイル編集  
16. パーミッションの設定	
17. アプリケーション起動  

## 今回実装するソフトウェアのバージョン一覧
| Webサービス | バージョン   |
| ------- | ------- |
| PHP     | 7.3     |
| Nginx   | 1.19    |
| MySQL   | 5.7     |
| Laravel | 6.20    |
| OS      | CentOS7 |

## 実行手順
### 1. Laravelの認証機能実装
今回作成するアプリケーションでは、ユーザーがアカウント作成やログインができるように認証機能を実装します。  
以下の手順で実装して認証機能を導入しましょう。
1. 認証機能を以下のコマンドで導入します。  
```shell
$ composer require laravel/ui "^1.0" --dev
```  
```shell
$ php artisan ui vue --auth
```  

2. サーバーを起動したら、ブラウザで開きRegister画面で新規登録をしてください。  
その後にログイン画面でログインできるか確認してみましょう。  
```shell
$ php artisan serve
```  
[http://127.0.0.1:8000](http://127.0.0.1:8000/)  
問題なくできればOKです。

### 2. VirtualBoxのインストール
仮想環境構築のた為にまず、VirtualBox(ヴァーチャルボックス)のインストールをします。 

仮想環境とは、パソコンやサーバーなどの一つのハードウェアの中で、複数のOSが起動できる環境を構築したものを言います。  
仮想環境があれば、物理的なハードウェアを複数台数用意する必要がなくなり、それによってコスト削減などにつながります。

VirtualBoxとは、使用しているPC上に仮想的なPCを作成し、別のOSをインストール・実行できる仮想化ソフトです。このPCに既存で設備されているOS(MacだとMacOS)をホストOS、新たに仮想的に作成されたOSをゲストOSと言います。  
VirtualBoxはこのゲストOSを立ち上げる為に使用します。  

手順は以下の通りです。  
1. まずは、下記の公式サイトにアクセスして下さい。  
[VirtualBox公式サイト](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)  
今回はVagrntというVirtualboxなどの仮想化ソフトを、CUIで操作するソフトをインストールします。  
VirtualBoxのバージョンが最新だとVagarntに対応していない為、Virtual Boxはver6.0.14をインストールするようにしましょう。  

2. 最後に、下記のコマンドを実行してVirtualBoxのウィンドウが表示されればOKです。  
```shell
$ virtualbox
```  
こちらでVirtualBoxのインストールは完了となります。

### 3. Vagrantのインストール
Vagrant(ベイグラント)とは、Virtualboxなどの仮想化ソフトをCUIで操作する為のソフトです。  
つまりVirtualBoxのような仮想化ソフトが、インストールされていないと使用できません。  
手順は以下の通りです。  
1. 下記のコマンドをターミナルで実行してインストールして下さい。  
```shell
$ brew cask install vagrant
```  

2. インストールし終わったら次のコマンドで実際にインストールされているか、バージョンの確認をしましょう。  
```shell
$ vagrant -v
```  
バージョンが確認できていたらOKです。<br>  
今回Vagrantのインストールで使用したCaskとはHomebrewの拡張機能で、Vagrantの様なCUIのソフトをコマンド一発で管理できます。  
ただしこちらはMac用のパッケージマネージャーになります。  
こちらでVagrantのインストールは完了となります。

### 4. boxの取得
boxとは仮想マシンのテンプレートとなるものです。  
このテンプレートを使うことで、簡単に仮想マシンを作成できます。  
以下の手順で取得します。  
1. 次のコマンドで取得します。Vagrant boxのbox名は`centos/7`を指定してます。  
```shell
$ vagrant box add centos/7
```  

2. そうすると以下の様な表示になります。
```
`1) hyperv 
`2) libvirt  
`3) virtualbox  
`4) vmware_desktop  
`$ Enter your choice:
```
今回はvirtualboxをプロバイダーとして使用したいので3番を選択しましょう。  
```shell
$ Enter your choice: 3
```  
以下のような表示が出たらOKです。  
`box: Successfully added box 'centos/7' (v1905.1) for 'virtualbox'!`  
これでboxの取得は完了となります。

### 5. 初期設定ファイルの編集
まずは、Vagrantの作業用ディレクトリを作成して移動、そこに設定ファイルを作成します。  
手順は以下の通りです。  
1. 下記のコマンドでディレクトリを作成しましょう。  
```shell
$ mkdir vagrant_lesson
```

2. 作成したディレクトリに移動します。  
```shell
cd vagrant_lesson
```

3. 次に、移動したディレクトリの中で設定ファイルを以下のコマンドで作成します。  
```shell
$ vagrant init centos/7
```  
実行後、以下のように表示されていることを確認して下さい。
```
A `Vagrantfile` has been placed in this directory. You are now  
ready to `vagrant up` your first virtual environment! Please read  
the comments in the Vagrantfile as well as documentation on  
`vagrantup.com` for more information on using Vagrant.
```
確認ができれば問題ないです。  
4. Vagrantfileの編集を行なっていきます。  
エディタでVagrantfileを開き編集をしていきましょう。  
変更点は三箇所です。  
`# config.vm.network "forwarded_port", guest: 80, host: 8080`の部分をコメントインしましょう。  
`# config.vm.network "private_network", ip: "192.168.33.10"`の部分をコメントインした後にipアドレスを`"192.168.33.10"`から`"192.168.33.19"`に編集しましょう。  
`# config.vm.synced_folder "../data", "/vagrant_data"`の部分をコメントインして`config.vm.synced_folder "./", "/vagrant", type:"virtualbox"`に編集しましょう。  
これで設定ファイルの編集は完了となります。

### 6. プラグインのインストール
プラグインとは機能拡張用のソフトウェアです。  
色々種類はありますが、今回は`vagrant-vbguest`と言うプラグインをインストールしていきます。  
下記の手順でインストールして下さい。  
1. 次のコマンドでインストールしていきます。  
```shell
$ vagrant plugin install vagrant-vbguest
```

2. インストール完了されているか確認しましょう。  
```shell
$ vagrant plugin list
```  
確認ができていたらOKです。  
これでプラグインのインストールは完了です。

### 7. 関連パッケージをまとめて追加
仮想環境下でも依存関係性を意識したパッケージの取得を行います。  
以下が手順となっています。  
1. まずゲストOSを起動します。以下のコマンドを実行して下さい。  
```shell
$ vagrant up
```

2. ゲストOSを起動したらログインします。  
```shell
$ vagrant ssh
```  

3. 今回は`development tools`と言うグループパッケージを使ってパッケージを一括で取得します。以下のコマンドを実行しましょう。  
```shell
$ sudo yum -y groupinstall "development tools"
```  
`yum`と言うコマンドはLinuxで使用できるパッケージ管理ツールです。  
その前に記述がある`sudo`とはrootユーザーの権限を借りる様にできるコマンドです。  
パッケージのインストールは完了です。

### 8. 仮想環境内PHPインストール
仮想環境内でLaravelアプリケーションを動作させる為に、にPHPをインストールします。  
今回もyumとsudoを利用してインストールしていきます。  
以下の手順でインストールしましょう。  
1. まず下準備としてPHPをインストールする為、にEPELと言うリポジトリを下記のコマンドで有効にします。  
```shell
$ sudo yum -y install epel-release
```  
これで`Remi`がインストール可能になりました。

2. 次にPHPインストールの準備として、Remi(レミ)を下記のコマンドでインストールします。  
```shell
$ sudo wget rpms.famillecollet.com/enterprise/remi-release-7.rpm
```  
`wget`(ダブルゲット)とは、サーバーから指定したファイルを取ってくる事ができ、再帰的なダウンロードが可能なコマンドです。

3. その次にパッケージを更新します。  
```shell
$ sudo rpm -Uvh remi-release-7.rpm
```

4. PHPをと合わせて拡張モジュールを`Remi`を使ってインストールします。  
```shell
$ sudo yum -y install --enablerepo=remi-php72 php php-pdo php-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip
```

5. 最後にバージョンを確認し出来ていればOKです。  
```shell
$ php -v
```  
これでPHPのインストールは完了です。

### 9. 仮想環境内Composerインストール
Composerとは、Laravelが依存パッケージの管理に使用しているツールです。  
依存パッケージとは、インストールしてもまた別のパッケージがないと動作しないパッケージ(何か機能を持った部品、つまりはモジュールを集めた箱の様なもの)を言います。簡単にいうと、単体では動作しないパッケージをいいます。  
このパッケージの依存性を解決してくれるのがComposerです。  
Composerは、コマンド一つで依存関係にあるパッケージを一括で取得してくれます。
  
インストールの手順は以下の通りです。  
1. まずはインストーラーをダウンロードしていきます。  
```shell
$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
```

2. 次に、ダウンロードしたインストーラーを実行します。  
```shell
$ php composer-setup.php
```  

3. インストールしたインストーターを削除します。  
```shell
$ php -r "unlink('composer-setup.php');"
```

4. 次にCentOSのどこからでも使えるようにします。  
```shell
$ sudo mv composer.phar /usr/local/bin/composer
```

5. 最後に、バージョンを確認します。  
```shell
$ composer -v
```  
確認ができたら大丈夫です。  
これでComposerのインストールは完了です。

### 10. Laravelアプリケーションのコピー
仮想環境内に、作成したLaravelアプリケーションをコピーします。下記は手順となっています。  
1. まず、ゲストOSにいるならホストOSに下記のコマンドで移動します。  
```shell
$ exit
```

2. 次に、Vagrantの作業用ディレクトリに下記のコマンドで移動します。  
```shell
$ cd vagrant_lesson
```

3. 次の一文でLaravelアプリケーションをコピーします。  
```shell
$ cp -r laravel_lessonまでの絶対パス ./
```

4. Vagrantの作業用ファイルに`laravel_lesson`のディレクトリがコピーされた事を確認して下さい。  
確認ができたら完了となります。

### 11. 仮想環境内MySQLのインストール
LaravelアプリケーションでMySQLを使用する為に、インストールをしていきます。  
手順は以下のものです。  
1. 次のコマンドを実行して公式リポジトリ情報ファイルのインストールを行います。  
```shell
$ sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
```

2. 次に、インストールしたパッケージファイルを更新します。  
```shell
$ sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm
```  
rmpとは、RPM（Red Hat Package Manager）パッケージ”を扱うことができるパッケージ管理コマンドです。  

3. MySQLをインストールしていきます。  
```shell
$ sudo yum install -y mysql-community-server
```

4. バージョンをします。  
```shell
$ mysql --version
```  
バージョンが確認できたら完了となります。

### 12. 仮想環境下MySQLパスワード設定
rpmでMySQLをインストールした場合、パスワードの設定をする必要性が出てきます。  
以下の手順で変更していきます。  
1. まずは、簡易的なパスワードを作成できるようにMySQLの設定ファイルに編集を加えていきます。  
```shell
$ sudo vi /etc/my.cnf
```  
`vi`コマンドとはLinuxの標準のエディタを起動するコマンドです。  
作成されていないファイルをパスとして、設定すると自動で作成してくれます。<br>  
`vi`ではモードが2つあり、ノーマルモードとインサートモードがあります。  
切り替えは`escキー`とキーボードの`i`で行えます。  
ノーマルモードは編集は行えませんが、保存や削除ができます。  
対してインサートモードは編集処理が可能です。  

2. 起動したら、`i`でインサートモードに切り替えます。  
3. 次の文を確認して下さい。  
`socket=/var/lib/mysql/mysql.sock`  
この文の下に1行追加していきます。  
`validate-password=OFF`  

4. 編集したあとは`escキー`でノーマルモードに切り替えて、`:wq`で保存をかけて下さい。  
これで設定ファイルの内容の変更は完了です。  

5. その次に、サーバーの再起動を行います。  
```shell
$ sudo systemctl restart mysqld
```

6. 今現在のパスワードを確認します。  
```shell
$ sudo cat /var/log/mysqld.log ｜grep 'temporary password'
```  
出力された``:``後のものが現在のパスワードです。  

7. MySQL内での操作で、パスワードを変更していきます。  
```shell
$ mysql -u root -p
```  
```shell
$ Enter password:確認したパスワード
```  
```shell
mysql > set password = 変更したいパスワード
```  
これでパスワードの設定は以上になります。

### 13. 仮想環境内データベース作成
MySQLの諸々の設定が終わったので、データベースを作成していきます。以下の手順です。  
1. MySQL内で下記のコマンドを使用してデータベースの作成を行います。  
```shell
mysql > create database laravel_lesson
```

2. 最後にLaravelアプリケーションでのDBの接続情報の変更をしていきます。  
laravel_lessonディレクトリ下の`.env`ファイルを編集していきます。`DB=PASSWORD=`に設定したパスワードを入力します。  
```shell
DB_PASSWORD=入力したパスワード
```

3. 次のコマンドでマイグレーションファイルを実行します。  
```shell
$ php artisan migrate
```  
ここで出てきた`artisan`とは、Laravel専用のコマンドです。  
artisanコマンドを使うと、Laravelのコントローラーやモデルのひな形の作成、マイグレーションの実行などが行えます。  
これで仮想環境内でのデータベースの作成は完了です。

### 14. 仮想環境内Nginxインストール
WebサーバーであるNginx(エンジンエックス)をインストールします。  
手順は以下の通りです。  
1. まず、root権限で新たにファイル`/etc/yum.repos.d/nginx.repo`を追加します。  
```shell
$ sudo vi /etc/yum.repos.d/nginx.repo
```  
追加したら保存して下さい。  

2. ファイル追加後、次のように`yum install`コマンドでインストールできます。  
```shell
$ sudo yum install -y nginx
```

3. バージョンを確認します。  
```shell
$ nginx -v
```  
バージョンが確認できたら完了となります。

### 15. nginx設定ファイル編集
Laravelを動かす為の記述をNginxの設定ファイルに書いていきます。  
以下が手順となっています。  
1. まず、次のコマンドで設定ファイルを開きます。  
```shell
$ sudo vi /etc/nginx/conf.d/default.conf
```

2. 開いたら次の部分を編集していきます。  
`server_name  192.168.33.19;`に編集  
`root /vagrant/laravel_lesson/public;`を追加  
`index  index.html index.htm index.php;`を追加  
`root   /usr/share/nginx/html;`と`index  index.html index.htm;`をコメントアウト  
`location /`内に`try_files $uri $uri/ /index.php$is_args$args;`を追加  
`location ~ \.php$`と、その中のroot以外のコメントをコメントアウトする  
`location ~ \.php$`の中の`fastcgi_param`の部分を`SCRIPT_FILENAME  /$document_root/$fastcgi_script_name;`に編集  
これで編集は完了です。

### 16. パーミッションの設定
パーミッションとは操作権限のことです。  
今のままだと十分な操作権限がない為、Laravelのアプリケーションを操作する事ができません。  
以下の手順で操作権限を付与します。  
1. まず、Laravel_lessonに移動します。  
```shell
$ cd /vagrant/laravel_lesson
```

2. 次のコマンドで操作権限を付与できます。  
```shell
$ sudo chmod -R 777 storage
```

3.  次に、操作できる権限を持てるユーザーを変更します。  
```shell
$ sudo chown vagrant:vagrant /var
```  
これでパーミッションの設定は終了です。

### 17. アプリケーション起動
実装が全て完了したので実際にアプリケーションを起動します。以下が手順です。  
1. Nginxの起動をします。  
```shell
$ sudo systemctl start nginx
```

2. PHPのモジュールであるphp-fpmを起動します。  
```shell
$ sudo systemctl start php-fpm
```

3. 最後に、ブラウザにて確認します。以下のリンクで確認して下さい。  
[http://192.168.33.19](http://192.168.33.10/)  
ブラウザでアプリケーションの確認ができ、アカウント作成、ログインが出来れば今回の仮想環境構築は以上となります。  

##  参考サイト
### Laravelの認証機能実装
* [Qiita:Laravel6 ログイン機能を実装する](https://qiita.com/ucan-lab/items/bd0d6f6449602072cb87)

### VirtualBoxのインストール
* [情シスハック:VirtualBoxの導入からOSインストールまでのチュートリアル解説](http://success.tracpath.com/blog/2013/10/15/virtualbox%E3%81%AE%E5%B0%8E%E5%85%A5%E3%81%8B%E3%82%89os%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%81%BE%E3%81%A7%E3%81%AE%E3%83%81%E3%83%A5%E3%83%BC%E3%83%88%E3%83%AA%E3%82%A2/)
* [みんなの受験期:VirtualBoxとは？](http://jukenki.com/contents/other/virtualbox/about-virtualbox.html)
* [エンジニアの入り口:【初心者でもわかる】VirtualBoxインストール方法を詳しく！](https://eng-entrance.com/virtualbox-install)
* [キツネの惑星:【Linux環境構築】VagrantとVirtualBoxとは？使い方を初心者向けに解説！](https://kitsune.blog/linux-environment#Vagrant%E3%81%A8%E3%81%AF)
  
### Vagrantのインストール・boxの取得・初期設定ファイルの編集
* [Qiita:【備忘録】Vagrantを使用したCentOS7環境構築手順](https://qiita.com/Haruka-Ogawa/items/46f784e7f7cd44c6092d)

### プラグインのインストール
* [「分かりそう」で「分からない」でも「分かった」気になれるIT用語辞典](https://wa3.i-3-i.info/word11533.html)

### 関連パッケージをまとめて追加
* [HatenaBlog:VagrantでCentOSを使ってローカル開発環境を整える](http://nyameji.hatenablog.com/entry/2016/05/01/003139)

### 仮想環境内PHPインストール
* [たのしいWeb開発:VirtualBoxとVagrantでLAMP環境を構築する](https://dev83.com/virtualbox-vagrant-lamp/)

### 仮想環境内Composerインストール
* [Qiita:Composerをインストールするなら公式ドキュメントを見よう!](https://qiita.com/KeisukeKudo/items/df42bbf334cb9f6eedf0)
* [WEB ARCH LABO:Composer を CentOS にインストールする手順](https://weblabo.oscasierra.net/php-composer-centos-install/)

### Laravelアプリケーションのコピー
* [ミールの泥泉:VirtualboxとVagrantで作った仮想マシンを他のPCにコピーする](https://mimirswell.ggnet.co.jp/blog-20)

### 仮想環境内MySQLのインストール・仮想環境下MySQLパスワード設定
* [Qiita:CentOS 7 に MySQL Server 8 をインストールする](https://qiita.com/bezeklik/items/10e520e281bd32bde3e8)

### 仮想環境内Nginxインストール
* [WEB ARCH LABO:Nginx を CentOS 7 にインストールする手順](https://weblabo.oscasierra.net/nginx-centos7-install/)
* [WEB ARCH LABO:nginxをyumでインストールする手順 (CentOS/RedHat)](https://weblabo.oscasierra.net/install-nginx-1/)

### nginx設定ファイル編集
* [vdeep:CentOS7にnginxを導入＆初期設定まとめ](http://vdeep.net/centos7-nginx)

### パーミッションの設定
* [Qiita:【Laravel】プロジェクトを作るときやること Step by Step](https://qiita.com/nekyo/items/523d798652b26af9460d)

## 作成時の所感
この構築手順書は第三者の人が見てもできるだけ理解しやすいように作成しました。  
自分で理解できていない事は相手に説明できないと思うので、理解できるまでネットで検索を繰り返しました。  
手順書の作成によって現場での作業を、今までより実践的に学べました。しかし、今回の学習と実際現場での作業が全く同じとは思いません。  
ですから、実際に現場に出たとしてもギャップを感じないように、このような実戦に近い環境での作業を繰り返す必要性を感じました。
今後はただ学習するだけではなく、実際に「これは現場での作業なんだ」とイメージしながら学習しようと思います。