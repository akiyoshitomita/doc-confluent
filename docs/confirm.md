---
title: Confluent関連 確認方法
summary: 動作確認オペレーションについて記載します。 
authors:
  - A.Tomita 
date: 2019-06-04 
---

# 動作確認

## zookeeper

### サービス確認

    sudo systemctl status confluent-zookeeper

### 動作確認

Zookeeperの動作しているポートに対して、telnetを行い```stats```と入力する。
Zookeeperはサービス起動するもお互いに過半数のサーバが生存していないと利用可能になりません。
本コマンドで、利用可能かどうかを確認することができます。

    telnet 127.0.0.1 2181
    stats

出力例
```
Zookeeper version: 3.4.13-2d71af4dbe22557fda74f9a9b4309b15a7487f03, built on 06/29/2018 00:39 GMT
Clients:
 /10.42.151.90:47974[0](queued=0,recved=1,sent=0)
 /10.42.151.90:47964[1](queued=0,recved=440,sent=442)

Latency min/avg/max: 0/1/47
Received: 443
Sent: 444
Connections: 2
Outstanding: 0
Zxid: 0x100000022
Mode: follower
Node count: 30
Connection closed by foreign host.
```
