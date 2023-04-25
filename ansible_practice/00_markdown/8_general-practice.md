
# day8 総合演習

- [構成図](https://docs.google.com/presentation/d/1Z5oyxRJH1G_lImkciK9mhdvWkzOOvG4Z/edit?usp=sharing&ouid=110508462132118985202&rtpof=true&sd=true)を見て、以下の問いに答えなさい

## 問題 

### 前提条件
- 使用インベントリファイル：「/home/ec2-user/yokogushi_contents_team/ansible_practice/08_general-practice」配下のinventory.ini
- playbook作成先ディレクトリ：「/home/ec2-user/yokogushi_contents_team/ansible_practice/08_general-practice」配下
- playbook名：自由
- 注意事項：
  - コンテナを停止するたびにhost01/host02の一部設定が消える(原因・解決方法調査中)よって、本実習時はコンテナを起動後、
    以下コマンドを実行してから行うこと。
```yaml
[ec2-user@ip-172-31-42-108 ansible_practice]$ docker exec -it host01 /bin/bash
[root@host01 /]# route add -host 192.168.2.2 gw 192.168.1.254 eth1
[root@host01 /]# exit
exit
[ec2-user@ip-172-31-42-108 ansible_practice]$ docker exec -it host02 /bin/bash
[root@host02 /]# route add -host 192.168.1.2 gw 192.168.2.254 eth1
[root@host02 /]# exit
exit
```


### 目的
- 現在host01→host02に通信をするとき、vyos01を経由する。playbookでこれをvyos02を経由することに変更したい。

<br>

1. 以下のplaybookを作成してください。
- 内容：
  - １. vyos01のvrrpのプライオリティ値を150→50に変更する。
  - ２. vyos01/vyos02の事前・事後で、「show vrrp」を実行する。
  - ３. 1-2で確認した事前・事後確認内容は実行結果に出力させる。
 
<br>

2. 以下のplaybookを作成してください。
- 内容：
  - １. 「/home/ec2-user/yokogushi_contents_team/ansible_practice/08_general-practice」配下に、
        「/before/vyos01」 「/before/vyos02」「/after/vyos01」「/after/vyos02」のディレクトリを作成する
  - ２. パーミッションは「0644」で作成する

3. 以下のplaybookを作成してください。
- 内容：
  - １. vyos01/vyos02の事前・事後でshowコマンドを実行する。(実行コマンドは何でも・何個でも可)
  - ２. 3-1で確認した事前・事後確認内容は実行結果に出力させる。
  - ３．3-1で確認した事前・事後確認内容の実行結果を、テキストファイルに出力させる。
  - ４. 3-3で出力させたテキストファイルは、2-1で作成したディレクトリにそれぞれ保存する。

<br>

4. 以下のplaybookを作成してください。
  - １. vyos01のvrrpのプライオリティ値を150→50に変更する。
  - ２. 4-1のタスクが成功/失敗したときのそれぞれのメッセージを出力する。

