# Django Girls Blogでユーザ登録機能を練習する

[Django Girls Japan 2019/06勉強会](https://djangogirls-org.connpass.com/event/131237/)用リポジトリ

Django Congressトーク「[Django Girls Blogのネクストステップ](https://gitpitch.com/ftnext/2019_slides/master?p=django_congress_2019_blog_next_step)」の一部を再構成してハンズオンを実施します

## ソースコード作成に使った環境

- macOS 10.14.4
- Python 3.7.3

## ソースコードについて

### masterブランチ

**ハンズオン開始時点** のソースコードです。  
[Django Girls Tutorial](https://tutorial.djangogirls.org/ja/)で作るブログアプリを簡単に用意しています。  
機能は以下の通りです：

- ブログ記事の一覧
- ブログ記事の作成
- ログイン・ログアウト

このソースコードを一緒に編集して、機能追加していきます。

## ハンズオンの準備

以下の手順は、勉強会の中で一緒に進める想定です。

前提：お手元のPCにPythonがインストールされた状態  
（はじめて勉強会に来られた方は、Pythonのインストールからサポートします）

1. このリポジトリの「Clone or download」からソースコードをZIPでダウンロードします。
2. ダウンロードしたZIPファイルを展開し、ホームディレクトリに置きます（ディレクトリの名前を`user_register`と付け直してください）
3. コマンドライン（Windowsの方はコマンドプロンプト、Macの方はターミナル）を起動します。以下のコマンドを上から順にコマンドラインで実行します（`#`で始まる行は説明ですので、コマンドラインで実行する必要はありません）

```shell
cd user_register

# 仮想環境の作成(macOSの場合) [※1]
python3 -m venv myvenv
# 仮想環境の有効化(macOSの場合) [※1]
source myvenv/bin/activate

# Djangoのインストール
python -m pip install --upgrade pip
pip install -r requirements.txt

# データベースのセットアップ
python manage.py migrate

# スーパーユーザの作成
python manage.py createsuperuser
# 続く入力内容は [※2] を参照

# サーバの起動
python manage.py runserver
# 続きはハンズオンで
```

[※1] Windowsの方はDjango Girls Tutorial [Djangoのインストール](https://tutorial.djangogirls.org/ja/django_installation/) を参考にしてください。  
また、Anacondaの方は[こちらのQiita記事](https://qiita.com/ftnext/items/082ec8fe96f26b181fc5)を参考にしてください。

[※2] スーパーユーザの情報を1行ずつ入力していきます。

```shell
Username (leave blank to use '<ユーザ名>'):  # 入力例: ftnext
Email address:  # 入力例: ftnext@gmail.com
Password:  # 文字が見えませんがパスワードを入力してください
Password (again):  # 同様のパスワードを入力してください
Superuser created successfully.
```

パスワードが短いなどの理由で、警告（Warning）が出ることがありますが、スーパーユーザは作成されています。
