# 第三章 docker-compose

## docker-composeとは？

複数のコンテナを使うDockerアプリケーションを、定義・実行するツール

- dockerコマンドを引数含めてyml化できる
- 複数のコンテナの依存関係、network、volumeの共有等を設定できる
- リファレンスは[ここ](https://docs.docker.com/compose/compose-file/)

## どんなときに使う？

- 本番環境のECSへのデプロイの定義

- 開発環境の構築

- オプションいっぱいのdockerコマンドをファイルとして残しておく

## [ハンズオン] Web + DB
下記のような2つのコンテナから成る構成を作成する

![10](https://user-images.githubusercontent.com/24800246/61018538-23c3b980-a3d2-11e9-868f-0591d91fead3.png)

## [ハンズオン] 準備

下記リポジトリをcloneする

```bash
git clone --depth=1 --branch=practice https://github.com/reireias/docker-hands-on.git
```

構成はこんな感じ

```bash
docker-hands-on
├── LICENSE
├── README.md
├── docker-compose.yml    # 今回修正するファイル
├── go-hello
│   ├── Dockerfile        # Dockerfileの演習と少し変わっている
│   └── main.go           # Dockerfileの演習とだいぶ変わっている
└── initdb.d              # DB起動時にsqlを実行する仕組みを利用するためのsql
    └── init.sql
```

**今回のハンズオンでは`docker-compose.yml`のみ変更する**

## [ハンズオン] 変更点の確認

`Dockerfile`

```dockerfile
RUN apk add git         # go getにgitコマンドが必要なので追加
RUN go get -d -v ./...  # mysql用のライブラリをコードに追加したので追加
```

`main.go`

- `/tasks`に対するハンドラーを追加
- DBに接続し、返ってきたデータをすべてレスポンスとして返す
- DBへの接続情報は環境変数から取得

## [ハンズオン] webサーバーコンテナの定義

`docker-compose.yml`を下記のように変更する

```yaml

services:              # 各コンテナの定義をservice以下に書く
  go-hello:            # コンテナの名前
    build: ./go-hello  # Dockerfileを使ってビルドする場合は、そのディレクトリのパス
    ports:             # docker run時の-pオプションで指定していた部分
      - 8888:8080
```

```bash
# イメージのビルド
docker-compose build
# 起動
docker-compose up -d
# curlで確認(/tasksの方はエラーになるはず)
curl http://localhost:8888
curl http://localhost:8888/tasks
# おまけ
docker-compose ps               # docker-compose.yml以下のコンテナを表示
docker-compose stop             # コンテナ停止
docker-compose start            # コンテナ開始
docker-compose exec go-hello sh # コマンド実行
docker-compose down             # コンテナ削除
```

## [ハンズオン] DBコンテナの定義

```yaml
# 省略
services:
  go-hello:
    # 省略
  db:
    image: mysql                     # レジストリ上のイメージを使う場合はimageで指定
    environment:                     # MySQLイメージは環境変数でいろいろ設定可能
      MYSQL_ROOT_PASSWORD: P@ssw0rd
      MYSQL_DATABASE: hello
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - db-data:/var/lib/mysql       # データ保存先をマウント
      - ./initdb.d:/docker-entrypoint-initdb.d # ローカルのディレクトリをマウント
volumes:
  db-data: # DBのデータ保存先用のvolumeを定義
    driver: local
```

```bash
# いったん前のコンテナを削除
docker-compose down
# 起動
docker-compose up -d
# 起動を確認
docker-compose ps
```

## [ハンズオン] go-helloコンテナにDB接続情報を設定

```yaml
  go-hello:
    build: ./go-hello
    environment:
      MYSQL_HOST: db            # 同じネットワーク上のコンテナ同士は名前でアクセス可能
      MYSQL_PORT: 3306          # MySQLのデフォルトポート
      MYSQL_DATABASE: hello
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    ports:
      - 8888:8080
    depends_on:                 # 依存なので起動順序を指定(なくてもエラーにはならない)
      - db
```

## [ハンズオン] アクセスしてみる

```bash
# いったん削除
docker-compose down
# 起動
docker-compose up -d
# curlでアクセス
curl http://localhost:8888
# /tasksにアクセス
curl http://localhost:8888/tasks
# [{1 hoge} {2 fuga} {3 foo} {4 bar}] が返ればOK
```

## [追加課題] さらにNginxも追加する

Nginxをwebサーバーの前段に追加してみる

![11](https://user-images.githubusercontent.com/24800246/61018542-26261380-a3d2-11e9-8141-f8e1ccf14e5a.png)

## まとめ

- OSS等を試すときにサクッとdockerで試せるようになった

- 任意のdockerイメージを作成できるできるようになった

- 複数のdockerコンテナからなる環境を構築できるようになった

## おまけ：Kubernetesの必要性

- コンテナ実行環境どう管理するの？

- コンテナ数（スケーリング）どう管理するの？

- 冗長化して数台あるコンテナの接続先はどう決める？

**コンテナオーケストレーションツールが必要！！**
