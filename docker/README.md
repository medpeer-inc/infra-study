# インフラ勉強会
## Docker

---

### ゴール：Dockerを使いこなせるようになる

- OSS等を試すときにサクッとdockerで試せる

- 任意のdockerイメージを作成できる

- 複数のdockerコンテナからなる環境を構築できる


---

- ## 第一章 Docker

- ## 第二章 Dockerfile

- ## 第三章 docker-compose

---

## 第一章 Docker

---

## 基礎知識

- カーネル

- ユーザー空間

- プロセス

---

### カーネル空間/ユーザー空間/プロセス
<img src="1_kernel.png" width="500px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

---

## Dockerとは隔離の技術

- 仮想化技術の一つ
  - 他にはハイパーバイザ型VMなど

- オーバーヘッドが小さい

  - 起動が速い

  - リソース消費が少ない

- Linuxカーネルが必要

---

- 隔離されたユーザー空間をコンテナと呼んで、あたかも一個の仮想マシンとしてみている

<img src="2_container.png" width="500px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

---

## ハイパーバイザ型との比較
- ゲストOSが無い分軽量
- ただし、Linuxカーネルが必要

<img src="3_docker_vs_vm.png" width="800px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

---

## ハイパーバイザ型との比較

- メリット
  - ゲストOSが無い分軽量
  - イメージの配布も軽量

- デメリット
  - Linuxカーネルが必要
  - WindowsやMacの環境は用意できない

---

## 実は、MacとWindowsではそんなに軽量ではない！
- **なぜならLinuxカーネルを搭載していないので**

<img src="4_mac_win_docker.png" width="800px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

---

Linuxで開発したいなぁ...

---

## Dockerの基本

最低限理解しておくDockerの概念

- image

- network

- volume

---

## docker image

レジストリとイメージとコンテナ

<img src="5_docker_image.png" width="800px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

---

## docker image

コンテナの状態

<img src="6_state.png" width="800px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

---

## docker network

ネットワークの分離

<img src="7_network.png" width="700px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

詳しくは[Docker Reference Architecture](https://success.docker.com/article/networking)参照

---

## docker network

ポートバインディング

<img src="8_port.png" width="700px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

---

## docker volume

2パターンのvolumeの利用方法

<img src="9_volume.png" width="700px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

---

## Dockerコマンド

- `docker pull`
- `docker run`
- `docker stop`
- `docker rm`
- `docker ps`
- `docker exec`
- `docker logs`
- `docker images`

---

ちなみに、補完をbashやzshに設定しとくと便利

[設定方法](https://docs.docker.com/compose/completion/)

---

## docker pull

リポジトリからイメージを取得する

```bash
Usage:	docker pull [OPTIONS] NAME[:TAG|@DIGEST]

# NAMEの指定方法
# 基本形 = ユーザー名/イメージ名
  minio/minio
# 下記と同じ(省略時はlatestとみなされる)
  minio/minio:latest
# 別タグを指定したい場合
  minio/minio:edge
# Docker Official Imageはユーザー名なし
  ruby
# 下記と同じ
  ruby:latest
```

---

## docker run

イメージからコンテナを起動する

イメージがローカルにない場合は取得もする


```none
Usage:	docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
Options:
  -d, --detach       Run container in background and print container ID
  -i, --interactive  Keep STDIN open even if not attached
      --name string  Assign a name to the container
      --rm           Automatically remove the container when it exits
  -t, --tty          Allocate a pseudo-TTY
  -v, --volume list  Bind mount a volume
```

---

## docker stop

起動中のコンテナを停止する

```none
Usage:	docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

## docker rm

停止中のコンテナを削除する

```none
Usage:	docker rm [OPTIONS] CONTAINER [CONTAINER...]
Options:
  -f, --force     Force the removal of a running container (uses SIGKILL)
```

---

## docker ps
コンテナ一覧を表示

```none
Usage:	docker ps [OPTIONS]
Options:
  -a, --all             Show all containers (default shows just running)
      --format string   Pretty-print containers using a Go template
  -l, --latest          Show the latest created container (includes all states)
```

## docker exec
コンテナ内でコマンドを実行

```none
Usage:	docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

---

## docker logs
コンテナのログを表示

**コンテナのログ = メインプロセスの標準出力**

```bash
Usage:	docker logs [OPTIONS] CONTAINER
Options:
  -f, --follow         Follow log output
```

---

## docker images
ローカルのイメージの一覧

## docker rmi
ローカルのイメージを削除

```bash
Usage:	docker rmi [OPTIONS] IMAGE [IMAGE...]
```

---

## その他便利コマンド
- `docker image prune`
  - 未使用のイメージの全削除

- `docker stats`
  - 各コンテナのCPU/メモリ/IOを表示

---

### [ハンズオン] 一通りのコマンド

```bash
# イメージの取得
docker pull nginx:alpine
# イメージ一覧に存在することの確認
docker images | grep nginx
# 現在のコンテナ一覧の確認(多すぎる場合は -f name=apline を指定)
docker ps
# イメージからコンテナを起動
docker run -d --name nginx-alpine -p 8888:80 nginx:alpine
# 現在のコンテナ一覧の確認(多すぎる場合は -f name=apline を指定)
docker ps
# curlでアクセスする(Welcome to nginx!が表示されればOK)
curl http://localhost:8888
# logsでアクセスログを確認する
docker logs nginx-alpine
```

---

### [ハンズオン] 一通りのコマンド 続き

```bash
# execでshを実行
docker exec -it nginx-alpine sh
# html書き換え(titleを適当に書き換える)
vi /usr/share/nginx/html/index.html
# Ctrl + D or exitでshを終了
# curlでアクセスする(書き換えたtitleが表示されればOK)
curl http://localhost:8888
# コンテナを停止する
docker stop nginx-alpine
# 一覧に表示されないことを確認
docker ps
# -aオプションをつけて停止中のコンテナも含めると表示されることを確認
docker ps -a
# 停止したコンテナを削除する
docker rm nginx-alpine
# イメージを削除する
docker rmi nginx:alpine
```

---

### [ハンズオン] runして対話型で利用

```bash
# ruby 2.6のirbを実行
docker run -it --rm ruby:2.6
# 2.7の機能であるメソッドオブジェクトの取得がエラーになることの確認
irb> 1.:to_s
# Ctrl + Dで終了(--rmオプションがあるのでコンテナは削除される)

# ruby 2.7のirbを実行
docker run -it --rm ruby:2.7-rc
irb> 1.:to_s
# => #<Method: Integer#to_s>
```

ローカルを汚さずに何かを確認したいときなどに便利

---

### [追加課題] run時のコマンド指定
**問題：`alpine` の `/etc/os-release` を表示させてみる**

- alpineは軽量Linuxディストリビューション
- DockerHubにOfficial Imageとしてある
- コンテナ起動時に実行されるコマンドを上書き指定できる
```none
Usage: docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

---

### [追加課題] volume
**問題：`-v`オプションを利用し、下記htmlファイルをコンテナ内にマウントし、curlでアクセスせよ**

- `hoge.html`の内容
```html
<!DOCTYPE html>
<title>hoge</title>
```
- コンテナ内のパスは`/usr/share/nginx/html/hoge.html`
- マウント時のホスト側は絶対パスで指定

---

### Tips: よくコンテナで実行するサービス

- Swagger
  - APIスキーマ定義/表示/Mock生成ツール
  - UIとかローカルで起動するとポートかぶるので
- jupyter
  - python製のデータ分析ツール
  - ローカルインストールは設定が面倒なので
- squid
  - proxyサーバー
  - 理由は同上
- mysql等のDB
  - 同じDBを複数バージョンローカルに用意するのは面倒なので

---

## 第二章 Dockerfile

---

## Dockerfileとは

Dockerイメージを構築するためのファイル

- リポジトリ上の既存のイメージをベースにできる
- 既存のイメージにパッケージを追加する
- 既存のrubyイメージに自分のソースコードを乗せる

---

## Dockerfileの例

```docker
FROM ubuntu:18.04      # ベースとなるイメージを指定
COPY . /app            # ローカルのカレントディレクトリ以下をコンテナ内にコピー
RUN make /app          # makeコマンドによるアプリのビルド
CMD python /app/app.py # イメージをコンテナとして起動した際に実行するコマンドを指定
```

---

### [ハンズオン] golang製web server

最もシンプルな自分で実装したwebサーバーを<br>コンテナで動かす例

---

### [ハンズオン] golang製web server

下記リポジトリをcloneする

```bash
git clone --depth=1 --branch=practice https://github.com/reireias/docker-golang-web.git
```

構成はこんな感じ

```bash
docker-golang-web
├── Dockerfile
├── README.md
└── main.go
```

**今回のハンズオンでは`Dockerfile`のみ変更する**

---

### [ハンズオン] golang製web server

コードの確認


golangでHello World!を返すweb serverのコード

```golang
package main

import (
	"fmt"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hello World!")
}

func main() {
	http.HandleFunc("/", handler)
	http.ListenAndServe(":8080", nil)
}
```

---

### [ハンズオン] golang製web server

下記内容で`Dockerfile`を作成

```dockerfile
FROM golang:alpine

WORKDIR /go/src/app
COPY . .
RUN go build -o hello main.go

CMD ["./hello"]
```

- ベースイメージには最新のgolangの軽量イメージであるalpineタグを指定

- `/go/src/app`以下に`main.go`を含むすべてをコピー

- `go build`でコードをビルドし、`hello`というバイナリを作成し、起動時に実行するコマンドに指定

---

### [ハンズオン] golang製web server
```bash
# イメージをビルド
docker build -t go-hello .

# コンテナとして起動
docker run --rm -p 8888:8080 go-hello

# 別terminalからcurl
curl http://localhost:8888
```

---

### [追加課題] 環境変数を指定する
**問題：`HOGE=hoge`という環境変数をDockerfile中で指定せよ**

```bash
# 以下でコンテナとして実行して環境変数を確認でる
docker build -t go-hello .
docker run --rm go-hello env

```

---

### [追加課題] Multi Stage Build
**問題：Multi Stage Buildを使い、busyboxイメージベースにして最終的なイメージを小さくせよ**

- ヒント：`net`パッケージを利用すると、`go build`がDynamic Linkになってしまうので、`CGO_ENABLED=0 go build`とする

---

## 第三章 docker-compose

---

## docker-composeとは？

複数のコンテナを使うDockerアプリケーションを、定義・実行するツール

- dockerコマンドを引数含めてyml化できる
- 複数のコンテナの依存関係、network、volumeの共有等を設定できる
- リファレンスは[ここ](https://docs.docker.com/compose/compose-file/)

---

## どんなときに使う？

- 本番環境のECSへのデプロイの定義

- 開発環境の構築

- オプションいっぱいのdockerコマンドをファイルとして残しておく

---

### [ハンズオン] Web + DB

<img src="10_compose.png" width="800px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

---

### [ハンズオン] 準備

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

---

### [ハンズオン] 変更点の確認

`Dockerfile`

```dockerfile
RUN apk add git         # go getにgitコマンドが必要なので追加
RUN go get -d -v ./...  # mysql用のライブラリをコードに追加したので追加
```

`main.go`

- `/tasks`に対するハンドラーを追加
- DBに接続し、返ってきたデータをすべてレスポンスとして返す
- DBへの接続情報は環境変数から取得

---

### [ハンズオン] webサーバーコンテナの定義

`docker-compose.yml`を下記のように変更する

```yaml
---
version: '3'

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

---

### [ハンズオン] DBコンテナの定義

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

---

### [ハンズオン] go-helloコンテナにDB接続情報を設定

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

---

### [ハンズオン] アクセスしてみる

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

---

### [追加課題] さらにNginxも追加する

<img src="11_nginx.png" width="800px" style="background-color: rgba(255, 255, 255, 0); border: none; box-shadow: none; margin: 20px;" />

---

## まとめ

- OSS等を試すときにサクッとdockerで試せるようになった

- 任意のdockerイメージを作成できるできるようになった

- 複数のdockerコンテナからなる環境を構築できるようになった

---

## おまけ：Kubernetesの必要性

- コンテナ実行環境どう管理するの？

- コンテナ数（スケーリング）どう管理するの？

- 冗長化して数台あるコンテナの接続先はどう決める？

**コンテナオーケストレーションツールが必要！！**
