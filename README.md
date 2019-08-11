# AWSCloudFormationSampleRDSAurora

AWS CloudFormationのテンプレート。RDS Auroraを作成する参考テンプレート。

「Template」内にSampleのテンプレート(CreateSampleRDSAurora.yaml/json)を用意しました。 ご参考になれば幸いです。

## CreateSampleRDSAuroraの構成イメージ

![CreateSampleRDSAuroraの構成イメージ](https://github.com/tanukinokegawa/AWSCloudFormationSampleRDSAurora/blob/master/img/20190810_AWS_CloudFormation_CreateRDSAurora_01.PNG)

## 事前準備

- RDS Auroraを配置するための、VPC/サブネットを用意する。
    - （※サブネットは違うAZで2個以上作成する。今回はap-northeast-1cとap-northeast-1dに各1つずつ作成する。）
    - （※サブネットはパブリックサブネットにする。）
- 自分の接続元を決めておく。インターネット接続するのであれば、自分の利用しているGlobal IPを確認する。自分の利用しているGlobal IPを確認するには[こちら](https://www.cman.jp/network/support/go_access.cgi)。後でVPN接続するなら、Private IPを確認する。
（※RDS Auroraに接続する際のインバウンドとして登録されます）
- アラート送付先のSNSトピックを用意する。

## 実行

CloudFormationにてスタックを作成し、テンプレート(CreateSampleRDSAurora.yaml/json)を設定する。

![CloudFormationのテンプレート選択](https://github.com/tanukinokegawa/AWSCloudFormationSampleRDSAurora/blob/master/img/20190810_AWS_CloudFormation_CreateRDSAurora_02.PNG)

※もちろん、S3にテンプレートを置いて実行でもOKです！

「次へ」を押下。以下を設定して、作成！

- MyLocation: 自分の接続元のCIDRを記す。
- SnsTopic: Amazon SNSのトピックの名前を記す。
- VPC: RDS Auroraを配置先のVPC IDを記す。
- SubnetIdC: ap-northeast-1cのサブネット IDを記す。
- SubnetIdD: ap-northeast-1dのサブネット IDを記す。

## テスト

RDSはPaaSなので、MySQLでの接続テストになる。
MyLocationにMySQLクライアントをインストールしておく。

    mysql -h クラスタのホスト名 -u 『RDSAuroraUser』で指定した値 -p 『RDSAuroraDBname』で指定した値
    Enter password: 『RDSAuroraPassword』で指定した値
    
    mysql> SHOW variables like "chara%";
    +--------------------------+--------------------------------------------------+
    | Variable_name            | Value                                            |
    +--------------------------+--------------------------------------------------+
    | character_set_client     | utf8                                             |
    | character_set_connection | utf8                                             |
    | character_set_database   | utf8                                             |
    | character_set_filesystem | utf8                                             |
    | character_set_results    | utf8                                             |
    | character_set_server     | utf8                                             |
    | character_set_system     | utf8                                             |
    | character_sets_dir       | /rdsdbbin/oscar-5.6.10a.200340.0/share/charsets/ |
    +--------------------------+--------------------------------------------------+


## 注意

* この構成は東京リージョンで作成することを想定しています。そのため、サブネットのAvailabilityZoneをap-northeast-1に直書きされていたりします。他のリージョンで使用したい場合にはご修正後利用してください。
* 今回はAurora MySQLを作成しています。
* 今回はインターネット経由で接続するようにしているので、パブリックサブネットを作ったり、PubliclyAccessibleをtrueにしていますが、一般的な構成ならばプライベートサブネットにして、PubliclyAccessibleはfalseにするものと思われます。
* 日本語の文字化けを避けるために、characterはutf8にしておいた方が良いかと存じます。詳しくは参考URLをご参照ください。
* 今回はRDS Auroraのインスタンスは1つのみになっています。これではAurora DB クラスターの耐障害性が機能していない状態です。耐障害性を持たせたい場合は、せめて2インスタンス（プライマリとレプリカ）をご用意ください。
* 本稿はサンプルとしてご参考になればと記したものです。ご自分の環境にそぐわない場合もございますことご承知おきください。


## 参考URL

* [AWS リソースおよびプロパティタイプのリファレンス](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
* [MySQLの日本語文字化け回避！文字コードを確認＆変更する方法](https://proengineer.internous.co.jp/content/columnfeature/6653)
* [[AWS] RDSのパラメータグループ仕様変更がクリティカルな件](https://noname.work/?p=1231)
* [AWS RDS デフォルトDBパラメータグループ運用止めませんか？](http://htnosm.hatenablog.com/entry/2015/08/02/210000)
* [Aurora DB クラスターのバックアップと復元の概要](https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/AuroraUserGuide/Aurora.Managing.Backups.html)
