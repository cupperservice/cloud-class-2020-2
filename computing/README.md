# クラウドコンピューティングとは
クラウドコンピューティングとは、コンピュータの機能や処理能力、ソフトウェア、データなどをインターネットなどの通信ネットワークを通じてサービスとして呼び出して遠隔から利用すること。

[参照](http://e-words.jp/w/%E3%82%AF%E3%83%A9%E3%82%A6%E3%83%89%E3%82%B3%E3%83%B3%E3%83%94%E3%83%A5%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0.html)

この授業では利用者がアプリケーションを配備して運用するためのリソースをコンピューティングと定義する。

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
