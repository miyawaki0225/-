# 社内システム用環境構築方法（VSCode利用）

[docker2ubuntu20.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04-ja)
[docker2ubuntu22.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)
### 流れ
windows版  

- wsl2　
- Ubuntu 22.04
- JDKをUbuntuにインストール
- VSCodeインストール
- dockerをインストールしてDB構築
- docekrの自動起動設定


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
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

ヒット:1 http://jp.archive.ubuntu.com/ubuntu focal InRelease
取得:2 https://download.docker.com/linux/ubuntu focal InRelease [57.7 kB]
ヒット:3 http://jp.archive.ubuntu.com/ubuntu focal-updates InRelease
```

#### dockerインストール ＞＞ ubuntu22.04
First, update your existing list of packages:
```console
sudo apt update
```
Next, install a few prerequisite packages which let apt use packages over HTTPS:

```console
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
Then add the GPG key for the official Docker repository to your system:

```console
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

Add the Docker repository to APT sources:
```console
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
Update your existing list of packages again for the addition to be recognized:

```console
sudo apt update
```

Make sure you are about to install from the Docker repo instead of the default Ubuntu repo:
```console
apt-cache policy docker-ce
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
```
  
```console
$ sudo apt-get install -y docker-ce docker-ce-cli containerd.io

パッケージリストを読み込んでいます... 完了
依存関係ツリーを作成しています
状態情報を読み取っています... 完了
以下の追加パッケージがインストールされます:
docker-ce-rootless-extras,docker-scan-plugin,git,git-man,liberror-perl,pigz,slirp4netns  
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

### wsl2でDocker自動起動設定
wsl2は、自動起動設定が出来ない。
本来は`service`コマンドでDocker起動が必要

#### ユーザでパスワード無しで起動できるようにsudoに設定
```console
$ sudo visudo
# docker deamon auto up
ユーザ ALL=(ALL:ALL) NOPASSWD: /usr/sbin/service docker start
```

```console
停止時
$ service docker status
 * Docker is not running
起動時
$ service docker status
 * Docker is running
```

#### .bashrcにdockerが起動していないときだけ、スタートさせるように追記
```console
$vim .bashrc
#追記
echo $(service docker status | awk '{print $4}') #起動状態を表示
if test $(service docker status | awk '{print $4}') = 'not'; then #停止状態
        sudo /usr/sbin/service docker start #起動
fi
```
この設定で、wsl2に入るときに、Dockerが停止していたら起動するようになりました。

#### ※発生するトラブル（wsl特有？）
dockerの自動起動に対するエラー
- 上記の自動起動メニューは実行するがdockerは起動しない
- 理由：systemctlのプロセスIDを1にしていない

```console
System has not been booted with systemd as init system (PID 1).
```

### genieのインストール（準備）
```console
#必要なパッケージのインストール１
sudo apt install daemonize dbus gawk libc6 libstdc++6 policykit-1 systemd systemd-container
#必要なパッケージのインストール２
wget https://packages.microsoft.com/config/debian/10/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
```

### パッケージの最新版を取得、更新
```console
sudo apt update
sudo apt upgrade
```

### dotnet-runtime-5.0をインストール
```console
sudo apt install dotnet-runtime-5.0
```

### genieのインストール
```console
sudo apt install apt-transport-https

wget -O /etc/apt/trusted.gpg.d/wsl-transdebian.gpg https://arkane-systems.github.io/wsl-transdebian/apt/wsl-transdebian.gpg

sudo chmod a+r /etc/apt/trusted.gpg.d/wsl-transdebian.gpg
```

### rootユーザーで実施。入力後exit
```console
cat << EOF > /etc/apt/sources.list.d/wsl-transdebian.list
deb https://arkane-systems.github.io/wsl-transdebian/apt/ $(lsb_release -cs) main
deb-src https://arkane-systems.github.io/wsl-transdebian/apt/ $(lsb_release -cs) main
EOF

sudo apt update
sudo apt install systemd-genie

#実行
genie -l
#もし「Waiting for systemd....!!!!!!!」で止まるようならCtrl+Cで停止した後で実行
genie -s
```
