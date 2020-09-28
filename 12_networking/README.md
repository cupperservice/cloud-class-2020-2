# ネットワーキング
## ネットワークとは

### ネットワーク構成

![ネットワーク構成](./img/structure.png)

### [OSI参照モデル](https://ja.wikipedia.org/wiki/OSI参照モデル)

### [IPアドレス](https://ja.wikipedia.org/wiki/IPアドレス)

## AWSのサービス

### ネットワーク構成(AWS)
![ネットワーク構成AWS](./img/structure_aws.png)

### [VPC](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/what-is-amazon-vpc.html)
* VPCとは
* VPCの仕組み
* IPアドレス指定
* セキュリティ
  * セキュリティグループ
* VPCのネットワーキングコンポーネント
  * ルートテーブル
  * インターネットゲートウェイ
  * NAT

### Route 53

### API Gateway

### CloudFront

## 演習
### ネットワーク構築
* VPC
* インターネットゲートウェイ
* サブネット
  * Public(インターネットからアクセス可能)/Private(インターネットからアクセス不可)
* セキュリティグループ
* ルートテーブル

### サーバ構築
* bastion
* Webサーバ

### Nginxインストール
1. WebサーバにNginxをインストールする

    ```
    $ sudo amazon-linux-extras install nginx1
    ```

2. Nginxを起動

    ```
    $ sudo systemctl start nginx
    ```

3. Nginxの自動起動を有効にする

    ```
    $ sudo systemctl enable nginx
    ```

### PHPインストール
1. WebサーバにNode.jsをインストールする

    ```
    $ sudo amazon-linux-extras install php7.4
    ```

2. php-fpmを起動する

    ```
    $ sudo systemctl start php-fpm
    ```

3. php-fpmの自動起動を有効にする

    ```
    $ sudo systemctl enable php-fpm
    ```

### 動作確認
1. 動作確認要ファイル作成  
以下のファイルを作成する
* 場所  
/usr/share/nginx/html/phpinfo.php

* 内容
  ```
  <?php
  phpinfo();
  ?>
  ```

2. 動作確認  
Webブラウザから以下のアドレスにアクセスする

    ```
    http://WebサーバのPublic IPアドレス/phpinfo.php
    ```
