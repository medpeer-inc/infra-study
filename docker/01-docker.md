# 第一章 Docker

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
