---
title: インストール(Ubuntu 18.04/apt)
summary: Ubuntuに対して、aptを利用したインストール手順を記載
authors:
  - A.Tomita
date: 2019-05-30
---

# 全サーバ共通設定

## openJDKのインストール

Ubuntuのレジストリから、JDKをインストール

    sudo apt install -y openjdk-11-jre-headless

## hostsファイルの編集

`/etc/hosts`ファイルに全サーバの名前解決ができるように行う
また、zookeeperに対しては、共通の名前を記載しておく

``` example
192.168.0.11   zoo1 zookeeper  # zookeeper 1
192.168.0.12   zoo2 zookeeper  # zookeeper 2 
192.168.0.13   zoo3 zookeeper  # zookeeper 3 
192.168.0.21   kafka1          # kafka broker 1
192.168.0.22   kafka2          # kafka broker 2 
192.168.0.23   kafka3          # kafka broker 3 
:
```
上記例では、`zookeeper`の名前で、`zoo1`,`zoo2`,`zoo3`のIPを解決できる

!!! note "補足"
    hostsファイルではなくDNSに登録してもよい

## APTソースリストへレジストリを追加 

Confluent公開鍵をインストール

    curl https://packages.confluent.io/deb/5.2/archive.key | sudo apt-key add -

ソースリストへ追加

    sudo add-apt-repository "deb [arch=amd64] https://packages.confluent.io/deb/5.2 stable main"

!!! Tip "メモ" 
    Confluentのドキュメントではwgetを利用していますが、本書ではcurlを利用しています。 

!!! note "備考"
    Ubuntuの場合、add-apt-repositoryコマンドがインストールされていない場合があります。
    その場合は `sudo apt install -y software-properties-common`コマンドでインストールを行ってください。

# 全ての機能をインストールする

APTからインストール

    sudo apt install confluent-platform-2.12

初期設定内容は、個別機能をインストールする場合を参照して下さい

!!! note "備考"
    パッケージ名の`Confluent-platform-2.12`のバージョン表記(`2.12`)は、ベースとなっているApache kafkaの
    バージョンを指しています。Confluentのバージョンとは異なるので注意が必要です。 

# 個別機能をインストールと設定

## zookeeperのインストールと設定

APTからkafka2.12のインストールします。

    sudo apt install confluent-kafka-2.12

!!! note "備考"
    zookeeperとbrokerは、同一パッケージにバンドルされいるため個別のインストールはできません。

`/etc/kafka/zookeeper.properties`ファイルに以下の設定を追加

```
tickTime=2000
initLimit=5
syncLimit=2
server.1=zoo1:2888:3888
server.2=zoo2:2888:3888
server.3=zoo3:2888:3888
autopurge.snapRetainCount=3
autopurge.purgeInterval=24
```

`server.*`のパラメーターは、以下の通り設定

    server.<マイID>=<ホスト名>:<leaderport>:<electionport>

* `マイID` : Zookeeper毎につけるユニークな番号 後述するファイルと一致させる必要があります。
* `ホスト名`: Zookeeperの固有のホスト名
* `leaderport`: Zookeeperのリーダへ接続するためのポート リーダでない場合もポートは開いています
* `electionport`: Zookeeperのリーダを決定するために利用するポート 常にすべてのZookeeperで開いています

マイIDを`/var/lib/zookeeper/myid`ファイルに登録します。登録する番号は、設定ファイルと同じ番号である必要があります。

    echo '1' > /var/lib/zookeeper/myid

!!! note "備考"
    全てのZookeeperに対して、設定は全て一致させる必要があります。


# Confluentレジストリについて

## 参考資料 Confluent5.2 パッケージ一覧

* confluent-cli
* confluent-common
* confluent-community-2.11
* confluent-community-2.12
* confluent-control-center
* confluent-control-center-fe
* confluent-hub-client
* confluent-kafka-2.11
* confluent-kafka-2.12
* confluent-kafka-connect-elasticsearch
* confluent-kafka-connect-hdfs
* confluent-kafka-connect-jdbc
* confluent-kafka-connect-jms
* confluent-kafka-connect-replicator
* confluent-kafka-connect-s3
* confluent-kafka-connect-storage-common
* confluent-kafka-mqtt
* confluent-kafka-rest
* confluent-ksql
* confluent-librdkafka-plugins
* confluent-libserdes++1
* confluent-libserdes-dev
* confluent-libserdes1
* confluent-platform-2.11
* confluent-platform-2.12
* confluent-rebalancer
* confluent-rest-utils
* confluent-schema-registry
* confluent-security
* confluent-support-metrics
* libavro-c-dev
* libavro-c1
* libavro-cpp-dev
* libavro-cpp1
* librdkafka++1
* librdkafka-dev
* librdkafka1
* librdkafka1-dbg
