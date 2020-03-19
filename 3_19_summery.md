# サーバーサイド勉強会 まとめ
### 3/18 (水) 19:00~22:00 @Kernel
## 1.　先週やったこと
## サーバの要求機能の確認と Google Cloud Platform(GCP) の準備

a.　Google Cloud sdk のインストール  
b.　Cloud SQL Database への接続  
c.　MySQL のインストール  

### a. Google Cloud sdk のインストール
(参考．[Macにgoogle-cloud-sdk（gcloudコマンド）をインストール - Qiita](https://qiita.com/aki-takano/items/963af2575b53091d0bf6))  

gcloudコマンドを使用するため，手元のMacBookにgoogle-cloud-sdkをインストールする．  

https://cloud.google.com/sdk/docs/quickstart-mac-os-x?hl=ja からダウンロードし，  
好きなディレクトリに展開して，インストールを行う．
```
$ ./google-cloud-sdk/install.sh
```

#### b. Cloud SQL Database への接続
(参考．[Cloud SQLのデータベースにローカルから接続する方法 - Qiita](https://qiita.com/ryu-yama/items/f635a7608469bf019de7))  

Cloud SQLにプロキシ経由で接続するため，プロキシをインストールする．

```
$ curl -o cloud_sql_proxy https://dl.google.com/cloudsql/cloud_sql_proxy.darwin.amd64

\\ 権限の付与
$ chmod +x cloud_sql_proxy
```

サービスアカウントの認証鍵ファイル(.json)を使ったプロキシサーバの起動方法．
```
$ ./cloud_sql_proxy -instances=Project_ID:region:Instance_Name=tcp:13306 -credential_file=Path_To_File_for_Authentification.json
```

#### c. MySQL のインストール
(参考．[[mac] Homebrewを使ってMySQL5.7をインストールして初期設定 - Qiita](https://qiita.com/knoth071211/items/241bd94af722593e685e))
(MySQL 8.0x はこちら：https://www.codingstock.jp/mac-mysql8/)

## 2.　今日の課題
## Go bookshelf アプリのチュートリアル
(参考．[Go Bookshelf アプリ  |  Google Cloud](https://cloud.google.com/go/getting-started/tutorial-app?hl=ja))

### 概要
* **サンプルアプリのクローンを作成する。またはサンプルアプリをダウンロードする。**
* **アプリをビルドし、ローカルマシンで実行する。**
  
* アプリを App Engine にデプロイする。
* サンプルコードの内容を一通り理解する。
* アプリが構造化データを格納する仕組みを理解する。
* アプリが Cloud Storage にバイナリデータを格納する仕組みを理解する。
* アプリがユーザーを認証する方法を理解する。
* Google Cloud Platform Console に表示されるイベントログをアプリが作成する仕組みを理解する。
* アプリが Cloud Pub/Sub を使用してバックグラウンド ワーカーにタスクを送信する仕組みを理解する。  

### アプリのローカル環境での実行
#### 1. GCP Console から GCP インスタンスを作成，設定(先週やった)．  
(参考．https://cloud.google.com/sql/docs/mysql/create-instance?hl=ja)

#### 2. Cloud SDK をダウンロード，インストール，初期化(先週やった)．  

#### 3. GCP サービスで認証するためのローカル認証情報を取得．  
Google のアカウントで認証設定して結局最後まで行ったけど，よく分かっていない．  
実はサービスアカウントでの認証鍵があればこの手順はいらないのではないか．(小池)  
参考．https://cloud.google.com/sql/docs/mysql/connect-admin-proxy?hl=ja
```
$ gcloud auth application-default login
```

#### 4. プロジェクト ID の設定  
```
$ gcloud config set project Project_ID
```

#### 5. Bookshelf サンプル リポジトリのクローン作成  
最新のチュートリアルが不完全なので，bookshelf ディレクトリを入れ直す．
```
// $GOPATH/src/github.com/ の部分は人による
$ cd $GOPATH/src/github.com/GoogleCloudPlatform/golang-samples/getting-started/

// bookshelf ディレクトリを削除
$ rm -rf $GOPATH/src/github.com/GoogleCloudPlatform/golang-samples/getting-started/bookshelf/

// 古いバージョンのものをクローンする．
$ git clone https://github.com/OnlyMyRailgun/bookshelf
```

#### 6. プロキシサーバの起動  
先週と同じ．サービスアカウントの認証鍵ファイル(.json)を使ってプロキシサーバを起動する．
```
$ ./cloud_sql_proxy -instances=Project_ID:region:Instance_Name=tcp:13306 -credential_file=Path_To_File_for_Authentification.json
```

#### 7. bookshelf アプリ内からの接続設定  
$GOPATH/src/github.com/GoogleCloudPlatform/golang-samples/getting-started/bookshelf/ にある以下のファイルを修正する．
- config.go
- app/app.yaml
- drone.yml

#### 8. ローカル環境でのアプリの起動  
```
$ cd app
$ go run app.go auth.go template.go
```
ブラウザに次のアドレスを入力するとアプリのUIが表示される．  
http://localhost:8080

