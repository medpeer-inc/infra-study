# 第一章 Docker

---

## 基礎知識

- カーネル

- ユーザー空間

- プロセス

---

### カーネル空間/ユーザー空間/プロセス
![1_kernel](https://user-images.githubusercontent.com/24800246/61018128-b2cfd200-a3d0-11e9-8414-1e8dac0579b8.png)

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

![2_container](https://user-images.githubusercontent.com/24800246/61018148-c3804800-a3d0-11e9-89e8-2e3f056e9684.png)

---

## ハイパーバイザ型との比較
- ゲストOSが無い分軽量
- ただし、Linuxカーネルが必要

<img width="431" alt="3_docker_vs_vm" src="https://user-images.githubusercontent.com/24800246/61018487-01ca3700-a3d2-11e9-88b6-a9bcb05f05de.png">

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

![4](https://user-images.githubusercontent.com/24800246/61018500-0bec3580-a3d2-11e9-9fd8-22a7139cd17f.png)

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

![5](https://user-images.githubusercontent.com/24800246/61018514-11498000-a3d2-11e9-8dae-aa104de83385.png)

---

## docker image

コンテナの状態

![6](https://user-images.githubusercontent.com/24800246/61018517-14447080-a3d2-11e9-90a6-b6ba681b4192.png)

---

## docker network

ネットワークの分離

![7_network](https://user-images.githubusercontent.com/24800246/61018525-17d7f780-a3d2-11e9-82bf-a68c1c57f9ce.png)

詳しくは[Docker Reference Architecture](https://success.docker.com/article/networking)参照

---

## docker network

ポートバインディング

![8](https://user-images.githubusercontent.com/24800246/61018532-1c041500-a3d2-11e9-9cdb-278267748fd3.png)

---

## docker volume

2パターンのvolumeの利用方法

![9](https://user-images.githubusercontent.com/24800246/61018534-21615f80-a3d2-11e9-8574-ec8aa80a3f64.png)

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
