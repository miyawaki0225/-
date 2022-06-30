# 社内システム用環境構築方法（VSCode利用）

### 流れ
windows版  

- wsl2　
- Ubuntu 22.04
- JDKをUbuntuにインストール
- VSCodeインストール
- dockerをインストールしてDB構築


※ハマったポイント
- VSCodeとUbuntuの連携が上手くいかなかった
> Ubuntuを管理者権限で実行してwslを2にアップデートしてた
> ローカル環境のwslは1のままなので、VSCodeを「*非*」管理者権限で実行していると Ubuntu wsl1版と連携して失敗してた。


### UbuntuにDockerをインストールする手順
#### 1. パッケージをアップデートする
```console
$ sudo apt-get update
[sudo] password for ユーザ名:
ヒット:1 http://jp.archive.ubuntu.com/ubuntu focal InRelease
```

#### 2. 必要 な パッケージ を インストール する
```console
$ sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
パッケージリストを読み込んでいます... 完了
依存関係ツリーを作成しています
状態情報を読み取っています... 完了
キーを登録
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
OK
```

#### 3. ダウンロードサイトをaptリポジトリに登録
```console
$ sudo add-apt-repository \
> "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
> $(lsb_release -cs) \
> stable"
ヒット:1 http://jp.archive.ubuntu.com/ubuntu focal InRelease
取得:2 https://download.docker.com/linux/ubuntu focal InRelease [57.7 kB]
ヒット:3 http://jp.archive.ubuntu.com/ubuntu focal-updates InRelease
```

#### 4. Docker Engine一式をインストール
```console
$ sudo apt-get update
ヒット:1 http://jp.archive.ubuntu.com/ubuntu focal InRelease
ヒット:2 http://jp.archive.ubuntu.com/ubuntu focal-updates InRelease
ヒット:3 http://jp.archive.ubuntu.com/ubuntu focal-backports InRelease
ヒット:4 https://download.docker.com/linux/ubuntu focal InRelease
ヒット:5 http://security.ubuntu.com/ubuntu focal-security InRelease
パッケージリストを読み込んでいます... 完了
$ sudo apt-get install -y docker-ce docker-ce-cli containerd.io
パッケージリストを読み込んでいます... 完了
依存関係ツリーを作成しています
状態情報を読み取っています... 完了
以下の追加パッケージがインストールされます:
  docker-ce-rootless-extras docker-scan-plugin git git-man liberror-perl pigz slirp4netns
```

#### 5. Dockerを利用できるようにする
```console
$ sudo gpasswd -a ユーザ名 docker
Adding user ユーザ名 to group docker
```

#### 6. ログオフする
```console
exit
```

#### 7. Dockerのバージョン確認
```console
$ docker -v
Docker version 20.10.12, build e91ed57
```
