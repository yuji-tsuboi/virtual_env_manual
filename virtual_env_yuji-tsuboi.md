# <font color="DeepSkyBlue">＜環境構築手順書＞</font>
## <font color="DeepSkyBlue">|</font>&nbsp;&nbsp;全体の流れ (目次)  
### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;仮想環境内で使用するLaravelアプリケーションに認証機能を実装する
1. Laravelの認証機能実装  

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;仮想環境を構築する
2. VirtualBoxのインストール    
3. Vagrantのインストール  
4. boxの取得  
5. 初期設定ファイルの編集  
6. プラグインのインストール  

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;構築した仮想環境内にPHP開発環境を整える
7. 関連パッケージをまとめて追加  
8. 仮想環境内PHPインストール  
9. 仮想環境内Composerインストール  

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;作成したLaravelアプリケーションを仮想環境内に導入、操作可能にする
10. Laravelアプリケーションのコピー  
11. 仮想環境内MySQLのインストール  
12. 仮想環境下MySQLパスワード設定  
13. 仮想環境内データベース作成  
14. 仮想環境内Nginxインストール  
15. nginx設定ファイル編集  
16. パーミッションの設定	
17. アプリケーション起動  

## <font color="DeepSkyBlue">|</font>&nbsp;&nbsp;今回実装するソフトウェアのバージョン一覧
| Webサービス | バージョン   |
| ------- | ------- |
| PHP     | 7.3     |
| Nginx   | 1.19    |
| MySQL   | 5.7     |
| Laravel | 6.20    |
| OS      | CentOS7 |

## <font color="DeepSkyBlue">|</font>&nbsp;&nbsp;実行手順
### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;1. Laravelの認証機能実装
今回作成するアプリケーションでは、ユーザーがアカウント作成やログインができるように認証機能を実装します。  
以下の手順で実装して認証機能を導入しましょう。
1. 認証機能を以下のコマンドで導入します。  
<font color="DeepSkyBlue">```$ composer require laravel/ui "^1.0" --dev```</font>  
<font color="DeepSkyBlue">```$ php artisan ui vue --auth```</font>  

2. サーバーを起動したら、ブラウザで開きRegister画面で新規登録をしてください。  
その後にログイン画面でログインできるか確認してみましょう。  
<font color="DeepSkyBlue">```$ php artisan serve```</font>  
[http://127.0.0.1:8000](http://127.0.0.1:8000/)  
問題なくできればOKです。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;2. VirtualBoxのインストール
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
<font color="DeepSkyBlue">```$ virtualbox```</font>  
こちらでVirtualBoxのインストールは完了となります。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;3. Vagrantのインストール
Vagrant(ベイグラント)とは、Virtualboxなどの仮想化ソフトをCUIで操作する為のソフトです。  
つまりVirtualBoxのような仮想化ソフトが、インストールされていないと使用できません。  
手順は以下の通りです。  
1. 下記のコマンドをターミナルで実行してインストールして下さい。  
<font color="DeepSkyBlue">```$ brew cask install vagrant```</font>  

2. インストールし終わったら次のコマンドで実際にインストールされているか、バージョンの確認をしましょう。  
<font color="DeepSkyBlue">```$ vagrant -v```</font>  
バージョンが確認できていたらOKです。<br>  
今回Vagrantのインストールで使用したCaskとはHomebrewの拡張機能で、Vagrantの様なCUIのソフトをコマンド一発で管理できます。  
ただしこちらはMac用のパッケージマネージャーになります。  
こちらでVagrantのインストールは完了となります。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;4. boxの取得
boxとは仮想マシンのテンプレートとなるものです。  
このテンプレートを使うことで、簡単に仮想マシンを作成できます。  
以下の手順で取得します。  
1. 次のコマンドで取得します。Vagrant boxのbox名は`centos/7`を指定してます。  
<font color="DeepSkyBlue">```$ vagrant box add centos/7```</font>  

2. そうすると以下の様な表示になります。
```  
`1) hyperv`  
`2) libvirt`  
`3) virtualbox`  
`4)vmware_desktop`  
`$ Enter your choice:`  
```
今回はvirtualboxをプロバイダーとして使用したいので3番を選択しましょう。  
<font color="DeepSkyBlue">```$ Enter your choice: 3```</font>  
以下のような表示が出たらOKです。  
`box: Successfully added box 'centos/7' (v1905.1) for 'virtualbox'!`  
これでboxの取得は完了となります。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;5. 初期設定ファイルの編集
まずは、Vagrantの作業用ディレクトリを作成して移動、そこに設定ファイルを作成します。  
手順は以下の通りです。  
1. 下記のコマンドでディレクトリを作成しましょう。  
<font color="DeepSkyBlue">```$ mkdir vagrant_lesson```</font>  

2. 作成したディレクトリに移動します。  
<font color="DeepSkyBlue">```cd vagrant_lesson```</font>  

3. 次に、移動したディレクトリの中で設定ファイルを以下のコマンドで作成します。  
<font color="DeepSkyBlue">```$ vagrant init centos/7```</font>  
実行後、以下のように表示されていることを確認して下さい。  
``A `Vagrantfile` has been placed in this directory. You are now``  
``ready to `vagrant up` your first virtual environment! Please read``  
`the comments in the Vagrantfile as well as documentation on`  
`` `vagrantup.com` for more information on using Vagrant.``
確認ができれば問題ないです。  
4. Vagrantfileの編集を行なっていきます。  
エディタでVagrantfileを開き編集をしていきましょう。  
変更点は三箇所です。  
* `# config.vm.network "forwarded_port", guest: 80, host: 8080`の部分をコメントインしましょう。  
* `# config.vm.network "private_network", ip: "192.168.33.10"`の部分をコメントインした後にipアドレスを`"192.168.33.10"`から`"192.168.33.19"`に編集しましょう。  
* `# config.vm.synced_folder "../data", "/vagrant_data"`の部分をコメントインして`config.vm.synced_folder "./", "/vagrant", type:"virtualbox"`に編集しましょう。  
これで設定ファイルの編集は完了となります。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;6. プラグインのインストール
プラグインとは機能拡張用のソフトウェアです。  
色々種類はありますが、今回は`vagrant-vbguest`と言うプラグインをインストールしていきます。  
下記の手順でインストールして下さい。  
1. 次のコマンドでインストールしていきます。  
<font color="DeepSkyBlue">```$ vagrant plugin install vagrant-vbguest```</font>  

2. インストール完了されているか確認しましょう。  
<font color="DeepSkyBlue">```$ vagrant plugin list```</font>  
確認ができていたらOKです。  
これでプラグインのインストールは完了です。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;7. 関連パッケージをまとめて追加
仮想環境下でも依存関係性を意識したパッケージの取得を行います。  
以下が手順となっています。  
1. まずゲストOSを起動します。以下のコマンドを実行して下さい。  
<font color="DeepSkyBlue">```$ vagrant up```</font>

2. ゲストOSを起動したらログインします。  
<font color="DeepSkyBlue">```$ vagrant ssh```</font>  

3. 今回は`development tools`と言うグループパッケージを使ってパッケージを一括で取得します。以下のコマンドを実行しましょう。  
<font color="DeepSkyBlue">```$ sudo yum -y groupinstall "development tools"```</font>  
`yum`と言うコマンドはLinuxで使用できるパッケージ管理ツールです。  
その前に記述がある`sudo`とはrootユーザーの権限を借りる様にできるコマンドです。  
パッケージのインストールは完了です。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;8. 仮想環境内PHPインストール
仮想環境内でLaravelアプリケーションを動作させる為に、にPHPをインストールします。  
今回もyumとsudoを利用してインストールしていきます。  
以下の手順でインストールしましょう。  
1. まず下準備としてPHPをインストールする為、にEPELと言うリポジトリを下記のコマンドで有効にします。  
<font color="DeepSkyBlue">```$ sudo yum -y install epel-release```</font>  
これで``Remi``がインストール可能になりました。  

2. 次にPHPインストールの準備として、Remi(レミ)を下記のコマンドでインストールします。  
<font color="DeepSkyBlue">```$ sudo wget rpms.famillecollet.com/enterprise/remi-release-7.rpm```</font>  
``wget``(ダブルゲット)とは、サーバーから指定したファイルを取ってくる事ができ、再帰的なダウンロードが可能なコマンドです。  

3. その次にパッケージを更新します。  
<font color="DeepSkyBlue">```$ sudo rpm -Uvh remi-release-7.rpm```</font>  

4. PHPをと合わせて拡張モジュールを`Remi`を使ってインストールします。  
<font color="DeepSkyBlue">```$ sudo yum -y install --enablerepo=remi-php72 php php-pdo php-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip```</font>  

5. 最後にバージョンを確認し出来ていればOKです。  
<font color="DeepSkyBlue">```$ php -v```</font>  
これでPHPのインストールは完了です。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;9. 仮想環境内Composerインストール
Composerとは、Laravelが依存パッケージの管理に使用しているツールです。  
依存パッケージとは、インストールしてもまた別のパッケージがないと動作しないパッケージ(何か機能を持った部品、つまりはモジュールを集めた箱の様なもの)を言います。簡単にいうと、単体では動作しないパッケージをいいます。  
このパッケージの依存性を解決してくれるのがComposerです。  
Composerは、コマンド一つで依存関係にあるパッケージを一括で取得してくれます。
  
インストールの手順は以下の通りです。  
1. まずはインストーラーをダウンロードしていきます。  
<font color="DeepSkyBlue">```$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"```</font>  

2. 次に、ダウンロードしたインストーラーを実行します。  
<font color="DeepSkyBlue">```$ php composer-setup.php```</font>  

3. インストールしたインストーターを削除します。  
<font color="DeepSkyBlue">```$ php -r "unlink('composer-setup.php');"```</font>  

4. 次にCentOSのどこからでも使えるようにします。  
<font color="DeepSkyBlue">```$ sudo mv composer.phar /usr/local/bin/composer```</font>  

5. 最後に、バージョンを確認します。  
<font color="DeepSkyBlue">```$ composer -v```</font>  
確認ができたら大丈夫です。  
これでComposerのインストールは完了です。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;10. Laravelアプリケーションのコピー
仮想環境内に、作成したLaravelアプリケーションをコピーします。下記は手順となっています。  
1. まず、ゲストOSにいるならホストOSに下記のコマンドで移動します。  
<font color="DeepSkyBlue">```$ exit```</font>  

2. 次に、Vagrantの作業用ディレクトリに下記のコマンドで移動します。  
<font color="DeepSkyBlue">```$ cd vagrant_lesson```</font>  

3. 次の一文でLaravelアプリケーションをコピーします。  
<font color="DeepSkyBlue">```$ cp -r laravel_lessonまでの絶対パス ./```</font>  

4. Vagrantの作業用ファイルに`laravel_lesson`のディレクトリがコピーされた事を確認して下さい。  
確認ができたら完了となります。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;11. 仮想環境内MySQLのインストール
LaravelアプリケーションでMySQLを使用する為に、インストールをしていきます。  
手順は以下のものです。  
1. 次のコマンドを実行して公式リポジトリ情報ファイルのインストールを行います。  
<font color="DeepSkyBlue">```$ sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm```</font>  

2. 次に、インストールしたパッケージファイルを更新します。  
<font color="DeepSkyBlue">```$ sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm```</font>  
rmpとは、RPM（Red Hat Package Manager）パッケージ”を扱うことができるパッケージ管理コマンドです。  

3. MySQLをインストールしていきます。  
<font color="DeepSkyBlue">```$ sudo yum install -y mysql-community-server```</font>  

4. バージョンをします。  
<font color="DeepSkyBlue">```$ mysql --version```</font>  
バージョンが確認できたら完了となります。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;12. 仮想環境下MySQLパスワード設定
rpmでMySQLをインストールした場合、パスワードの設定をする必要性が出てきます。  
以下の手順で変更していきます。  
1. まずは、簡易的なパスワードを作成できるようにMySQLの設定ファイルに編集を加えていきます。  
<font color="DeepSkyBlue">```$ sudo vi /etc/my.cnf```</font>  
`vi`コマンドとはLinuxの標準のエディタを起動するコマンドです。  
作成されていないファイルをパスとして、設定すると自動で作成してくれます。<br>  
`vi`ではモードが2つあり、ノーマルモードとインサートモードがあります。  
切り替えは`escキー`とキーボードの`i`で行えます。  
ノーマルモードは編集は行えませんが、保存や削除ができます。  
対してインサートモードは編集処理が可能です。  

2. 起動したら、<font color="DeepSkyBlue">`i`</font>でインサートモードに切り替えます。  
3. 次の文を確認して下さい。  
`socket=/var/lib/mysql/mysql.sock`  
この文の下に1行追加していきます。  
`validate-password=OFF`  

4. 編集したあとは<font color="DeepSkyBlue">`escキー`</font>でノーマルモードに切り替えて、<font color="DeepSkyBlue">`:wq`</font>で保存をかけて下さい。  
これで設定ファイルの内容の変更は完了です。  

5. その次に、サーバーの再起動を行います。  
<font color="DeepSkyBlue">```$ sudo systemctl restart mysqld```</font>  

6. 今現在のパスワードを確認します。  
<font color="DeepSkyBlue">```$ sudo cat /var/log/mysqld.log ｜grep 'temporary password'```</font>  
出力された``:``後のものが現在のパスワードです。  

7. MySQL内での操作で、パスワードを変更していきます。  
<font color="DeepSkyBlue">```$ mysql -u root -p```</font>  
<font color="DeepSkyBlue">```$ Enter password:確認したパスワード```</font>  
<font color="DeepSkyBlue">```mysql > set password = 変更したいパスワード```</font>  
これでパスワードの設定は以上になります。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;13. 仮想環境内データベース作成
MySQLの諸々の設定が終わったので、データベースを作成していきます。以下の手順です。  
1. MySQL内で下記のコマンドを使用してデータベースの作成を行います。  
<font color="DeepSkyBlue">```mysql > create database laravel_lesson```</font>  

2. 最後にLaravelアプリケーションでのDBの接続情報の変更をしていきます。  
laravel_lessonディレクトリ下の`.env`ファイルを編集していきます。`DB=PASSWORD=`に設定したパスワードを入力します。  
<font color="DeepSkyBlue">```DB_PASSWORD=入力したパスワード```</font>  

3. 次のコマンドでマイグレーションファイルを実行します。  
<font color="DeepSkyBlue">```$ php artisan migrate```</font>  
ここで出てきた`artisan`とは、Laravel専用のコマンドです。  
artisanコマンドを使うと、Laravelのコントローラーやモデルのひな形の作成、マイグレーションの実行などが行えます。  
これで仮想環境内でのデータベースの作成は完了です。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;14. 仮想環境内Nginxインストール
WebサーバーであるNginx(エンジンエックス)をインストールします。  
手順は以下の通りです。  
1. まず、root権限で新たにファイル`/etc/yum.repos.d/nginx.repo`を追加します。  
<font color="DeepSkyBlue">```$ sudo vi /etc/yum.repos.d/nginx.repo```</font>  
追加したら保存して下さい。  

2. ファイル追加後、次のように`yum install`コマンドでインストールできます。  
<font color="DeepSkyBlue">```$ sudo yum install -y nginx```</font>  

3. バージョンを確認します。  
<font color="DeepSkyBlue">```$ nginx -v```</font>  
バージョンが確認できたら完了となります。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;15. nginx設定ファイル編集
Laravelを動かす為の記述をNginxの設定ファイルに書いていきます。  
以下が手順となっています。  
1. まず、次のコマンドで設定ファイルを開きます。  
<font color="DeepSkyBlue">```$ sudo vi /etc/nginx/conf.d/default.conf```</font>  

2. 開いたら次の部分を編集していきます。  
* `server_name  192.168.33.19;`に編集  
* `root /vagrant/laravel_lesson/public;`を追加  
* `index  index.html index.htm index.php;`を追加  
* `root   /usr/share/nginx/html;`と`index  index.html index.htm;`をコメントアウト  
* `location /`内に`try_files $uri $uri/ /index.php$is_args$args;`を追加  
* `location ~ \.php$`と、その中のroot以外のコメントをコメントアウトする  
* `location ~ \.php$`の中の`fastcgi_param`の部分を`SCRIPT_FILENAME  /$document_root/$fastcgi_script_name;`に編集  
これで編集は完了です。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;16. パーミッションの設定
パーミッションとは操作権限のことです。  
今のままだと十分な操作権限がない為、Laravelのアプリケーションを操作する事ができません。  
以下の手順で操作権限を付与します。  
1. まず、Laravel_lessonに移動します。  
<font color="DeepSkyBlue">```$ cd /vagrant/laravel_lesson```</font>  

2. 次のコマンドで操作権限を付与できます。  
<font color="DeepSkyBlue">```$ sudo chmod -R 777 storage```</font>  

3.  次に、操作できる権限を持てるユーザーを変更します。  
<font color="DeepSkyBlue">```$ sudo chown vagrant:vagrant /var```</font>  
これでパーミッションの設定は終了です。

### <font color="DeepSkyBlue">●</font>&nbsp;&nbsp;17. アプリケーション起動
実装が全て完了したので実際にアプリケーションを起動します。以下が手順です。  
1. Nginxの起動をします。  
<font color="DeepSkyBlue">```$ sudo systemctl start nginx```</font>  

2. PHPのモジュールであるphp-fpmを起動します。  
<font color="DeepSkyBlue">```$ sudo systemctl start php-fpm```</font>

3. 最後に、ブラウザにて確認します。以下のリンクで確認して下さい。  
[http://192.168.33.19](http://192.168.33.10/)  
ブラウザでアプリケーションの確認ができ、アカウント作成、ログインが出来れば今回の仮想環境構築は以上となります。  

##  <font color="DeepSkyBlue">|</font>&nbsp;&nbsp;参考サイト
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

## <font color="DeepSkyBlue">|</font>&nbsp;&nbsp;作成時の所感
この構築手順書は第三者の人が見てもできるだけ理解しやすいように作成しました。  
自分で理解できていない事は相手に説明できないと思うので、理解できるまでネットで検索を繰り返しました。  
今回の学習で実際現場での作業を今までより実践的に学べました。ある一点だけを学習したとしても、おおまかな概要が分かっていないとイメージしづらいと思います。  
今回の学習が実際現場での作業が全く同じとは思いませんが、アプリケーションを作成するところから仮想環境を作成し、そこで実装を繰り返すと言うように少しイメージが出来るようになりました。  