# 内容
CloudFormationとansibleを利用してアプリケーションサーバがスケールするシステムを作成する。
* [CloudFormation](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/Welcome.html)
* [ansible](https://docs.ansible.com/ansible/latest/index.html)

# 作業手順
1. ネットワーク & テンプレートサーバ作成(CloudFormation)
2. mongodb, growiをインストール(ansible)
3. ApplicationサーバのAMI作成(手作業)
4. ロードバランサー、オートスケーリングの設定(CloudFormation)

## 1. ネットワーク & テンプレートサーバ作成(CloudFormation)
CloudFormationを使用して以下のリソースを作成する
* VPC
* サブネット
* セキュリティグループ
* インターネットゲートウェイ
* ルートテーブル
* EC2インスタンス
  * Bastionサーバ
  * Databaseサーバ
  * Applicationサーバ

### 手順
[sample.yml](./cloudformation/template)を使用してCloudFormationでスタックを作成する。

## 2. mongodb, growiをインストール(Ansible)
ansibleを使用してmongodbとgrowiをインストールする。

### 手順
1. 秘密鍵をbastionサーバにアップロード

2. bastionサーバにSSHでログインする

3. 以下を実行してansibleをインストールする
    ```
    sudo yum -y install python-devel openssl-devel gcc git
    sudo easy_install pip
    sudo pip install ansible
    ```

4. ansibleのリソースを取得  
  以下のコマンドでAnsibleのリソースを取得する。  
  `git clone https://github.com/cupperservice/cloud-class-2020-2.git`

   ansibleのファイルは`cloud-class-2020-2/12_networking/auto_building/ansible/`の下に保管されている。  

5. 設定対象のサーバ(ApplicationサーバとDatabaseサーバ)のアドレスを設定

* ファイル: inventory/hosts.yml
* 修正内容  
  以下の[Change to your ...]を変更する。  

  ```
  all:
    children:
      dbservers:
        hosts:
          [Change to your database server's private address]
      appservers:
        hosts:
          [Change to your application server's private address]
  ```

6. Applicationサーバのパラメータを修正

* ファイル: group_vars/appservers/all.yml
* 修正内容  
  以下の[Change your ...]を変更する。

  ```
  nodejs:
    url: https://rpm.nodesource.com/setup_12.x 
    checksum: c68d96507d5b884470f5a6bc3e671d5003903c8a82d59b6e8ab74c19cf0519d4
    dest: /home/ec2-user/setup.sh
  yarn:
    url: https://dl.yarnpkg.com/rpm/yarn.repo
    checksum: f082589ae243fdaaaec735c7cb898624f57d9f93b77e1be259955b678fcde664
    dest: /etc/yum.repos.d/yarn.repo
  growi:
    url: https://github.com/weseek/growi.git
    installed_dir: /opt
    version: v4.0.9
    mongodb:  [Change to your database server's private address]:27017
  ```

7. playbookを実行  
  以下のコマンドでplaybookを実行する。  
  
    `ansible-playbook --private-key ~/hamajo.pem -i inventory/hosts.yml playbook.yml`

8. growiが動作完了したことを確認
ApplicationサーバにSSHでログインしてgrowiが正常に起動したことを確認する。

    a. bastionサーバからApplicationサーバにSSHでログインする  
    b. systemctlコマンドでgrowiが正常に起動しているかを確認する
    ```
    $ sudo systemctl status -l growi
    ```

    以下のメッセージが表示されればOK
    ```
    Nov 24 07:32:26 ip-10-0-30-114.ec2.internal npm[5482]: [2020-11-24T07:32:26.255Z]  INFO: growi:crowi/5782 on ip-10-0-30-114.ec2.internal: [production] Express server is listening on port 3000
    ```


## 3. ApplicationサーバのAMI作成(手作業)
ApplicationサーバのAMIを作成する。  
**次のステップで必要になるので、AMI IDを覚えておく**

## 4. ロードバランサー、オートスケーリングの設定(CloudFormation)
CloudFormationを使用して以下のリソースを作成する
* 起動テンプレート
* ターゲットグループ
* ロードバランサー
* Auto Scalingグループ

### 起動テンプレート
* Type: AWS::EC2::LaunchTemplate
* Properties
  * InstanceType
  * KeyName
  * ImageId
  * SecurityGroupIds
  * TagSpecifications
  * LaunchTemplateName

### ターゲットグループ
* Type: AWS::ElasticLoadBalancingV2::TargetGroup
* Properties
  * Name
  * TargetType
  * Port
  * Protocol
  * VpcId:
  * HealthCheckEnabled
  * HealthCheckIntervalSeconds
  * HealthCheckPath
  * HealthCheckProtocol
  * HealthCheckTimeoutSeconds
  * HealthyThresholdCount
  * UnhealthyThresholdCount
  * Matcher
    * HttpCode

### ロードバランサー
* Type: AWS::ElasticLoadBalancingV2::LoadBalancer
* Properties
  * Name
  * Type
  * Scheme
  * SecurityGroups
  * IpAddressType
  * Subnets
  * LoadBalancerAttributes

**LoadBalancerAttributesには以下を設定**

  ```
  - Key: access_logs.s3.enabled
    Value: false
  - Key: deletion_protection.enabled
    Value: false
  - Key: idle_timeout.timeout_seconds
    Value: 60
  ```

* Type: AWS::ElasticLoadBalancingV2::Listener
* Properties
  * LoadBalancerArn
  * Port
  * Protocol
  * DefaultActions

### Auto Scalingグループ
* Type: AWS::AutoScaling::AutoScalingGroup
* Properties
  * AutoScalingGroupName
  * Cooldown
  * HealthCheckGracePeriod
  * HealthCheckType
  * LaunchTemplate
  * MaxSize
  * MinSize
  * DesiredCapacity
  * VPCZoneIdentifier
  * TargetGroupARNs
