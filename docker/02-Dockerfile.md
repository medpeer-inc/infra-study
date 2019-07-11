# 第二章 Dockerfile

## Dockerfileとは

Dockerイメージを構築するためのファイル

- リポジトリ上の既存のイメージをベースにできる
- 既存のイメージにパッケージを追加する
- 既存のrubyイメージに自分のソースコードを乗せるといった用途で使う

## Dockerfileの例

```docker
FROM ubuntu:18.04      # ベースとなるイメージを指定
COPY . /app            # ローカルのカレントディレクトリ以下をコンテナ内にコピー
RUN make /app          # makeコマンドによるアプリのビルド
CMD python /app/app.py # イメージをコンテナとして起動した際に実行するコマンドを指定
```

## [ハンズオン] golang製web server

最もシンプルな自分で実装したwebサーバーをコンテナで動かす例

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

## コードの確認

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

## Dockerfileの作成

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

## イメージのビルドと起動

```bash
# イメージをビルド
docker build -t go-hello .

# コンテナとして起動
docker run --rm -p 8888:8080 go-hello

# 別terminalからcurl
curl http://localhost:8888
```

### [追加課題] 環境変数を指定する
**問題：`HOGE=hoge`という環境変数をDockerfile中で指定せよ**

```bash
# 以下でコンテナとして実行して環境変数を確認でる
docker build -t go-hello .
docker run --rm go-hello env

```

### [追加課題] Multi Stage Build
**問題：Multi Stage Buildを使い、busyboxイメージベースにして最終的なイメージを小さくせよ**

- ヒント：`net`パッケージを利用すると、`go build`がDynamic Linkになってしまうので、`CGO_ENABLED=0 go build`とする

