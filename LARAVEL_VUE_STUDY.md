https://github.com/rapide-act/laravel-vue-study

### phpのインストール

[配置場所と環境変数](https://weblabo.oscasierra.net/php-72-windows-install/)
[old version](https://windows.php.net/downloads/releases/archives/)
phpのパスが通らない時の注意点（.exeファイルが入っていないものをダウンロードしている）
- https://teratail.com/questions/63424

### Laravelのインストール
  - Laravelは、`Composer`というPHPのパッケージ管理システムを使用してインストールを行う
  - https://getcomposer.org/doc/00-intro.md#installation-windows
  - (phpのインストール場所を指定する)←自動で行われた

- `docker-compose up`を実行する前に`Docker Desktop`を起動しておく

### yarnのインストール
yarnを使うならnpm install -g yarnでグローバルインストールした方が良い  
- yarnコマンドが見つからない（インストールしているのに！？）
  - https://teratail.com/questions/296003

```console:npxを先頭につける
//frontendフォルダに入り、yarnファイルを実行するコマンド
cd frontend
npx yarn install

//サーバー実行
npx yarn serve
```

### Node.jsのインストール
  - https://nodejs.org/ja/

```console
npm install -g npm@8.18.0
npm install -g yarn
```

### vueのインストール
プロジェクトごとにvueを入れてる場合で確認
```console
npm list vue
```

```console
npm list -g vue
```

version確認
```console
php -v
/*
PHP 7.2.9 (cli) (built: Aug 15 2018 23:10:01) ( ZTS MSVC15 (Visual C++ 2017) x64 )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
*/

laravel -v
//Laravel Installer 4.0.0

node -v
//v16.17.0

npm -v
//8.18.0

npx yarn -v
//1.22.19

```

