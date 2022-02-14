---
marp: true
theme: apc_confidential
size: 16:9
---
<!--
class: title
-->

# 4. debug module

---

<!--
class: slide
paginate: true
-->

# 4-1. debug moduleとは？

- playbookのデバッグをしたいときに使用する
- 変数の中身や式の結果を確認することができる

| パラメータ | 説明                                                                                                          |
| :-----: | :------------------------------------------------------------------------------------------------------------ |
| msg  | 記載した文字列を表示する。<br>"{{ 変数名 }}" とすると、変数の中身を表示可能。<br>省略するとデフォルトの"Hello world!"が出力される。 |
| var  | 変数の中身を表示する                                                                                          |

https://docs.ansible.com/ansible/2.9/modules/debug_module.html

---

# 4-2. debug moduleの説明

## ■msg オプション
以下に参考のplaybook例を記載。
  ```yaml
  vars:
    var1: This is Test Message

  tasks:
    - name: debug messages
      debug:
        msg: "みなさん {{ var1 }} ですよ。"
  ```
  上記のように記述すれば、以下のような実行例として出力ができる。
  ```yaml
TASK [debug messages] *****************************************************************************
ok: [localhost] => {
    "msg": "みなさん This is Test Message ですよ。"
}
  ```

---

# 4-2. debug moduleの説明

## ■var オプション
  ```yaml
  vars:
    var1: This is Test Message

  tasks:
    - name: debug messages
      debug:
        var: var1
  ```
  上記のように記述すればvar1の中身はみれますが、
  前ページのようにその前後に適当な文字列をつけることはできません。

```yaml
TASK [debug messages] ***************************************************************************
ok: [localhost] => {
    "var1": "This is Test Message"
}
```

---
  
# 4-2. debug moduleの説明

## ■デフォルトメッセージ
  ```yaml
  vars:
    var1: This is Test Message

  tasks:
    - name: debug messages
      debug:
  ```
  上記のように記述すれば、デフォルトのメッセージが表示されます。

  ```yaml
TASK [debug messages] ******************************************************************************
ok: [localhost] => {
   "msg": "Hello world!"
}
  ```

---

# 4-3. debugの実習

debugモジュールを用いて、以下の目的を実現するplaybookを作成します。

## ■目的
playbook実行時中に「Hello Ansible!」という文字を1行だけ表示させる。
<br>

```yaml
PLAY [debug] ***************************************************************************************

TASK [debug_msg] ***********************************************************************************
ok: [vyos01] => {
    "msg": "Hello Ansible!"  # <-「Hello Ansible!」という文字が出力されている
}

PLAY RECAP *****************************************************************************************
vyos01: ok=1 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

---

# 4-3. debugの実習

## 1. playbookを作成

  ```yaml
  $ vi debug.yml

  ---
  - name: debug
    hosts: vyos01
    gather_facts: false

    tasks:
      - name: debug_msg
        debug:
          msg: “Hello Ansible!”  # <- msgの内容は文字列なので「""」で囲う
  ```

---

# 4-3. debugの実習

## 2. インベントリファイルの内容を確認

  ```yaml
  $ vi inventory.ini

  [vyos]
  vyos01 ansible_host=10.0.0.2  # <- playbook内で指定したhostsがホスト変数として定義されていることを確認
  vyos02 ansible_host=10.0.0.3

  [vyos:vars]
  ansible_network_os=vyos
  ansible_connection=network_cli
  ansible_user=vyos
  ansible_password=vyos
  ```

---

# 4-3. debugの実習

## 3. playbookを実行

  ```yaml
  $ ansible-playbook -i inventory.ini debug.yml

  PLAY [debug] ****************************************************************

  TASK [debug_msg] ************************************************************
  ok: [vyos01] => {
      "msg": "Hello Ansible!"  # <- 目的通りの文字が出力されていることを確認
  }

  PLAY RECAP ******************************************************************
  vyos01: ok=1 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
  ```

---

# 4-4.  演習

**Q1. 以下のplaybookを実行した際に表示される`「"msg":【 】」`の【 】として表示される文字を1つ選択して
　　ください。**

  ```yaml
  ---
  - name: debug_exercise1
    hosts: vyos01
    gather_facts: false

    tasks:
     - name: debug_msg
       debug:
  ```

1. Hello World!
2. "Hello World!"
3. " "
4. "debug_msg"

---

# 4-4.  演習

**Q2. 「4-3. debugの実習」で使用したdebug.ymlの内容について、hosts指定をグループ名の「vyos」ではな
　　く、「vyos01」とした理由を1つ選択してください。**

  ```yaml
  ---
  - name: debug
    hosts: vyos01  # <- なぜ「vyos」ではなく「vyos01」？
    gather_facts: false

    tasks:
      - name: debug_msg
        debug:
          msg: “Hello Ansible!” 
  ```
1. msg内容を表示する際には、グループ名指定をすることはできないから
2. vyos01にmsg内容を表示するための設定を投入しているから
3. msgの内容がグループ名で定義された機器の数だけ表示されてしまうから
4. 特に意味はなく、グループ名でもホスト名でも表示は変わらない

---

# 4-4.  演習

**Q3. 次のplaybookを実行した場合に`「"msg":【 】」`の【 】として表示される文字を1つ選択してください。
　　また、以下2点の注意点に気をつけること。**
- playbookの中身は`cat`もしくは`vim`コマンドで確認する
- playbook実行する際にはインベントリファイルの階層に気をつける
<br>
  ```
  /home/ec2-user/yokogushi_contents_team/ansible_practice/04_debug/debug_exercise3.yml
  ```
1. “Hello APC!”
2. “Hello APC!,Hello APC!”
3. “Hello APC!”,“Hello APC!”
4. “VARIABLE IS NOT DEFINED!”

---

# 4-4.  演習

**Q4. playbookを実行した際に`「"msg": "APC"」`と表示させるplaybookを作成しなさい。**

---

<!--
class: backcover
paginate: true
-->

[1]: https://blog-and-destroy.com/21376
[2]: https://blog-and-destroy.com/25199