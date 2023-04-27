
# day8 総合演習

- [構成図](https://docs.google.com/presentation/d/1Z5oyxRJH1G_lImkciK9mhdvWkzOOvG4Z/edit?usp=sharing&ouid=110508462132118985202&rtpof=true&sd=true)を見て、以下の問いに答えなさい

## 問題 

### 前提条件
- 使用インベントリファイル：「/home/ec2-user/yokogushi_contents_team/ansible_practice/08_general-practice」配下のinventory.ini
- playbook作成先ディレクトリ：「/home/ec2-user/yokogushi_contents_team/ansible_practice/08_general-practice」配下
- playbook名：自由
- 注意事項：
  - Dockerコンテナを停止(またはEC2インスタンスを停止)するたびに、一部設定が消える(原因・解決方法調査中)よって、本実習時はdockerコンテナを起動後、
    playbook (**container_setting.yml**) を実行すること。
```yaml
(venv) [ec2-user@ip-172-31-42-108 yokogushi_contents_team]$ ansible-playbook -i /home/ec2-user/yokogushi_contents_team/init_settings/inventory.ini /home/ec2-user/yokogushi_contents_team/init_settings/container_setting.yml
```

### 目的
- 現在host01→host02に通信をするとき、vyos01を経由する。playbookでこれをvyos02を経由することに変更したい。

<br>

1. 以下のplaybookを作成してください。
<br> ※playbookを実行する前後で、**traceroute**を実行して通信経路が変化していることを確認すること。
- 内容：
  - １. vyos01のvrrpのプライオリティ値を150→50に変更する。
  - ２. vyos01/vyos02の事前・事後で、「show vrrp」を実行する。
  - ３. 1-2で確認した事前・事後確認内容は実行結果に出力させる。
 
<br>

2-1. 以下のplaybookを作成してください。
- 内容：
  - １. localhostの「/home/ec2-user/yokogushi_contents_team/ansible_practice/08_general-practice」配下に、
        「/before/vyos01」 「/before/vyos02」「/after/vyos01」「/after/vyos02」のディレクトリを作成する(※loopディレクティブを使用すること)
  - ２. パーミッションは「0644」で作成する
  - ３. hosts: localhost で作成すること。

<br>

2-2. 以下のplaybookを作成してください。
- 内容：
  - １. vyos01/vyos02でshowコマンドを実行する。(実行コマンドは何でも・何個でも可)
  - ２. 2-2-1で確認した確認内容は実行結果に出力させる。
  - ３．2-2-1で確認した確認内容の実行結果を、2-1で作成したディレクトリにテキストファイルで出力させる。

<br>

2-3. 以下のplaybookを作成してください。
- 内容：
  - １. vyos01のvrrpのプライオリティ値を任意の値に変更する。その際、異常値が入力されてもplaybookがfailしないようにする。
  - ２. 2-3-1のタスクが成功したときに"succeeded"のメッセージを出力する。
  - ３. 2-3-1のタスクが失敗したときに"failed"のメッセージを出力する。
  - ４. 2-3-1のvrrp priority値が正常な場合と異常な場合に、2-3-2,2-3-3で想定通りのmessageが表示されることを確認する。なお、vrrp priorityの正常値は1-254の範囲。

<br>

### ヒント
- すべて講座で習った範囲で上記のplaybookを作成することができます
- 講座で習っていない内容をplaybookに組み込むことも可能です
- ブログもよいですが、最終的には同様の内容が書かれている公式のAnsibleドキュメントと照らし合わせながら調べることをお勧めします。

<br>

### 総合演習の解説日とそれまでについて
- 解説日：日程を改めてslackで調整させていただきます。
　　　　　<br>当日は発表(playbookの紹介)もしていただきたいと考えています
- 解説日まで：演習を進めていただければと思います。
  　　　　　　<br>日にちがいつもより空く予定なので、進捗確認を適宜行わせていただきますのでその際は解答お願いいたします。
