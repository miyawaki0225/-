# 社内システム用環境構築方法（VSCode利用）

[docker2ubuntu20.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04-ja)
[docker2ubuntu22.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)
 

### 流れ

- wsl2　
- Ubuntu 22.04
- VSCodeインストール
  #### Local インストール
  
  #### WSL:インストール
  - Spring Boot Extension Pack
  - Lombok Annotations Support for VSCODE
- JDKをUbuntuにインストール
- docker desktop for windowsをインストールしてDB構築


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

## JDKをインストール
```console
Command 'javac' not found, but can be installed with:
sudo apt install openjdk-11-jdk-headless  # version 11.0.15+10-0ubuntu0.22.04.1, or
sudo apt install default-jdk              # version 2:1.11-72build2
sudo apt install ecj                      # version 3.16.0-1
sudo apt install openjdk-18-jdk-headless  # version 18~36ea-1
sudo apt install openjdk-8-jdk-headless   # version 8u312-b07-0ubuntu1
sudo apt install openjdk-17-jdk-headless  # version 17.0.3+7-0ubuntu0.22.04.1
```


トラブル：理解できていなかった点
1. 社内システムを仮想環境に構築するにあたりVSCODEやDockerをwindows側とwsl側のどちらに入れるか分からない
  1. Windows側：VSCode、Docker Desktop for windows、ブラウザの起動ページ
  2. wsl側：JDK
2. VSCodeを起動するときはDocker Desktopを起動させておかないとVSCodeのdockerが接続できない
