# 社内システム用環境構築方法（VSCode利用）

[docker2ubuntu20.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04-ja)  
[docker2ubuntu22.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)
 
https://qiita.com/iridon0920/items/005a9224343413b74f78

### 流れ

- wsl2　
- MicrosoftStoreから >>> Ubuntu 22.04
- VSCodeインストール >> リポジトリを複製（クローン）
  #### Local インストール
  - remote wsl
  #### WSL:インストール
  - Spring Boot Extension Pack
  - Lombok Annotations Support for VSCODE
- JDKをUbuntuにインストール
- docker desktop for windowsをインストールしてDB構築  
- docker desktop のconfig > resources Enable in～をセットする  

※ハマったポイント
- VSCodeとUbuntuの連携が上手くいかなかった
> Ubuntuを管理者権限で実行してwslを2にアップデートしてた
> ローカル環境のwslは1のままなので、VSCodeを「*非*」管理者権限で実行していると Ubuntu wsl1版と連携して失敗してた。

オペレーティングシステム用にDockerをインストールして構成します。

Windows / macOS：
Docker Desktop for Windows/Macをインストールします。  
WindowsでWSL2を使用している場合は、WSL 2バックエンドが有効になっていることを確認します。Dockerタスクバー項目を右クリックして、[設定]を選択します。[WSL 2ベースのエンジンを使用する]をオンにし、 [リソース]>[WSL統合]でディストリビューションが有効になっていることを確認します。  
WSL 2バックエンドを使用しない場合は、Dockerタスクバー項目を右クリックし、[設定]を選択して、[リソース]>[ソースコードが保存されている場所とのファイル共有]を更新します。トラブルシューティングのヒントとコツを参照してください。  
https://docs.docker.com/desktop/windows/wsl/


VisualStudioCodeまたはVisualStudioCodeInsiderをインストールします。  
リモート開発拡張パックをインストールします。  
下記コマンドはvscodeからでOK
```console
cd infra（docker-composeがあるディレクトリに移動）
docker-compose up -d（コンテナを追加する）
bash start_db.sh（起動）
```

注意：社内システムに使われている、javaが古すぎてecripse2022より新しいとエラーが発生する。
対処方法：ecripseの起動設定をjava11に変更する
https://techhotoke.hatenablog.com/entry/2021/11/28/113957  
https://docs.docker.com/desktop/windows/wsl/　　
https://docs.microsoft.com/ja-jp/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package　　
https://blog.mori-soft.com/entry/2021/05/12/120821  

## JDKをインストール
※事前にパッケージリストのアップデート
```console
sudo apt-get update
apt purge needrestart
```

```console
Command 'javac' not found, but can be installed with:
sudo apt install openjdk-11-jdk-headless  # version 11.0.15+10-0ubuntu0.22.04.1, or
sudo apt install default-jdk              # version 2:1.11-72build2
sudo apt install ecj                      # version 3.16.0-1
sudo apt install openjdk-18-jdk-headless  # version 18~36ea-1
sudo apt install openjdk-8-jdk-headless   # version 8u312-b07-0ubuntu1
sudo apt install openjdk-17-jdk-headless  # version 17.0.3+7-0ubuntu0.22.04.1
```
環境変数を設定しておきます。.bachrc の最後に
※　上書き保存＋sourceコマンド実行しないと反映されない。
```console
vim .bashrc
```
```java
# i     編集モードに入り、書きたいコードを書く
# esc   編集モード解除
# :wq   保存して終了
```
> export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64/bin/java
```console
source .bashrc
```


トラブル：理解できていなかった点
1. 社内システムを仮想環境に構築するにあたりVSCODEやDockerをwindows側とwsl側のどちらに入れるか分からない
  1. Windows側：VSCode、Docker Desktop for windows、ブラウザの起動ページ
  2. wsl側：JDK、Extension Pack for Java,Spring Boot Extension Pack,Lombok Annotations Support for VS Code
2. VSCodeを起動するときはDocker Desktopを起動させておかないとVSCodeのdockerが接続できない

### Visual Studio Code で 「The Language Support for Java server crashed …」のエラーが発生した時の解決法
#### 原因
Visual Studio Code をインストールした環境に、過去のユーザー設定が残っていて、存在しない拡張機能のファイルを読み込もうとしていたことが原因でした。

なぜこんな環境になってしまったのかというと、アンインストールの仕方に問題があったからです。
私は以下の内容を実施しました。

Visual Studio Code + Java Extension Pack + Lombok Annotations Support for VS Code で開発環境を構築。
Visual Studio Code 本体をアンインストール。  
拡張機能のインストールフォルダを削除。  
（C:\Users\(ユーザー名)\.vscode\extensions のフォルダを削除する。）  
これだけでは、C:\Users\(ユーザー名)\AppData\Roaming\Code\User フォルダに保存されているユーザー設定(setting.json)は削除されていない為、削除した lombok.jar を参照している設定値が残ったままになってしまい、再インストールした Visual Studio Code がその設定で起動してエラーが発生していました。

#### まとめ
Language Support for Java(TM) by Red Hat の起動でエラーが発生した時は、ユーザー設定の java.jdt.ls.vmargs の値を確認する。
また、 Visual Studio Code を完全にアンインストールしたいときは、以下を実施する。  
  
Visual Studio Code 本体をアンインストール。  
拡張機能のインストールフォルダを削除。  
( C:\Users\(ユーザー名)\.vscode\extensions )  
ユーザー設定を削除。  
( C:\Users\(ユーザー名)\AppData\Roaming\Code\User )  
