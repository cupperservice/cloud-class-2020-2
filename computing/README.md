# クラウドコンピューティングとは
クラウドコンピューティングとは、コンピュータの機能や処理能力、ソフトウェア、データなどをインターネットなどの通信ネットワークを通じてサービスとして呼び出して遠隔から利用すること。

[参照](http://e-words.jp/w/%E3%82%AF%E3%83%A9%E3%82%A6%E3%83%89%E3%82%B3%E3%83%B3%E3%83%94%E3%83%A5%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0.html)

この授業では利用者がアプリケーションを運用するためのリソースを提供するサービスをコンピューティングと定義する。

## AWSが提供するリソース
[参照](https://aws.amazon.com/jp/)

## [Amazon EC2](https://aws.amazon.com/jp/ec2/)
Amazon Elastic Compute Cloud (Amazon EC2) は、安全でサイズ変更可能なコンピューティング性能をクラウド内で提供するウェブサービス

### 操作方法
EC2を操作する方法は以下の通り
* [Management console](https://aws.amazon.com/jp/console/)
* [CLI(Command line interface)](https://docs.aws.amazon.com/cli/latest/reference/ec2/)
* [API(Application interface)](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/Welcome.html)

#### CLI
* example
  * インスタンス作成
  ```
  IMAGE_ID=ami-02354e95b39ca8dec
  KEY_NAME=cupper
  SG_ID=sg-2ef35709
  SUBNET_ID=subnet-2eab7871

  aws ec2 run-instances \
  --image-id ${IMAGE_ID} \
  --count 1 \
  --instance-type t2.micro \
  --key-name ${KEY_NAME} \
  --security-group-ids ${SG_ID} \
  --subnet-id ${SUBNET_ID}
  ```

  * インスタンス削除
  ```
  INSTANCE_ID=i-0bff71cb84f78aa96

  aws ec2 terminate-instances --instance-ids ${INSTANCE_ID}
  ```

## [演習1] EC2インスタンスでWebサーバを立ち上げる
### EC2インスタンス作成
以下の設定でEC2インスタンスを作成する
* AMI: Amazon Linux 2 AMI (HVM), SSD Volume Type - 64 ビット (x86)
* インスタンスタイプ: t2.micro
* インスタンスの詳細の設定: デフォルト
* ストレージの追加: デフォルト
* タグの追加: Nameタグを追加する
* セキュリティグループの設定: 新しいセキュリティグループを作成する  
  以下を指定する  
  * セキュリティグループ名
  * 説明

### EC2インスタンスの起動
* キーペアを作成してダウンロードする
  * ダウンロード先は自分のホームディレクトリの下  
  例: C:\Users\MyAccount\Documents

### EC2インスタンスに接続
SSHでEC2インスタンスに接続する
``` shell
ssh -i ダウンロードしたキーファイル ec2-user@EC2インスタンスのIPv4 パブリック IP
```

例
``` shell
> ssh -i C:\Users\MyAccount\Documents\mykey.pem ec2-user@100.26.246.124
```
