コンテナ技術について

# 目的
コンテナ技術の基礎を学習し、基本的な操作ができるようになること

# コンテナとは
See.  
https://www.redhat.com/ja/topics/containers/whats-a-linux-container

# Dockerを使ってみる
## URL
https://docs.docker.com/

## 事前準備
EC2インスタンスを1つ用意しておく

## install
EC2にsshでログイン
以下のコマンドを使用してDockerをインストールする

* コマンド
```
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user
```

### インストール確認
以下のコマンドを実行してDockerの情報が表示されれればOK

* コマンド
```
docker info
```

* 実行結果
```
$ docker info
Client:
 Debug Mode: false

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0

...

```

## コンテナの起動
以下のコマンドを実行してWebサーバを立ち上げる。

* コマンド
```
docker run -d --name websvr -p 8080:80 nginx
```

### 動作確認
WebブラウザからEC2の8080ポートにアクセスしてNginxの画面が表示されればOK

## コンテナに接続

* コマンド
```
docker exec -it websvr bash
```

## コンテナの停止／削除
* 停止コマンド
```
docker stop websvr
```

* 削除コマンド
```
docker rm websvr
```

-----

## docker-compose

### 参照
https://docs.docker.jp/compose/toc.html

### インストール
以下のコマンドでdocker-composeをインストールする

* コマンド
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### growiを起動する
1. プロジェクトを取得
```
git clone https://github.com/cupperservice/cloud-class-2020-2.git
```

2. ファイルを更新
* 場所  
`14_container/docker/web/files/proxy.conf`

* 内容  
以下の `Change your public ip` を変更する

```
upstream growi {
  server app:3000;
}

map $http_upgrade $connection_upgrade {
  default Upgrade;
  ''      close;
}

server {
  listen       0.0.0.0:80;
  server_name  Change your public ip;

  location / {
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://growi;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
    proxy_read_timeout 900s;
  }
}
```

3. コンテナを起動
以下のコマンドでコンテナを起動する

* 場所  
`14_container/docker`

* コマンド
```
docker-compose up -d
```
