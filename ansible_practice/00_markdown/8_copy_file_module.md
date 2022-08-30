---
marp: true
theme: apc_confidential
size: 16:9
---
<!--
class: title
-->

# 8. copy/file module

---

<!--
class: slide
paginate: true
-->

# 8-1. copy moduleとは？

- ファイル/ディレクトリをコピーする際に使用します

| パラメータ(一部抜粋) | 説明 |
| :-: | :- |
| src  | コピー元を指定する。 |
| dest | コピー先を指定する。<br>コピー先にファイルが無ければ新規作成する。 |
| mode | ファイルのパーミッションを指定する。 |
| content | コピー先ファイルに記述する内容を指定する。<br>変数の内容を書き込こむこともできる。 |

https://docs.ansible.com/ansible/2.9/modules/copy_module.html

---

# 8-2. copy moduleの説明

## ■src/dest/mode オプション
```yaml
tasks:
  - name: copy file and change mode
    copy:
      src: /home/ec2-user/yokogushi_contents_team/sample.txt
      dest: /home/ec2-user/yokogushi_contents_team/copy_sample.txt
      mode: 0644
```
sample.txtというファイルを、copy_sample.txtというファイルにコピーし、
権限を設定しています。
コピー先にファイルが無ければ新規作成されます。
ただし、同名のファイルが存在する場合は確認無しで上書きされます。

---

# 8-2. copy moduleの説明

## ■content オプション
```yaml
tasks:
  - name: write message
      copy:
        content: contentを使えば文字や変数の内容を書き込めます。例えば、このプレイの名前は{{ ansible_play_name }}です。
        dest: /home/ec2-user/yokogushi_contents_team/ansible_practice/yamamoto/sample.txt
        mode: 0644
```
sample.txtにcontentで指定した内容を上書き記載します。
上記を実行すると、sample.txtの中身は以下のようになります。

```
contentを使えば文字や変数の内容を書き込めます。例えば、このプレイの名前はcontent module descriptionです。
```

---

# 8-3. file moduleとは

- ファイル/ディレクトリ/シンボリックリンクを操作する際に使用します

| パラメータ(一部抜粋) | 説明 |
| :-: | :- |
| dest | 必須のパラメータ。操作するファイルを指定する。 |
| mode | ファイルのパーミッションを指定する。 |
| state | ・directory<br>pathにディレクトリを作成する。<br>・absent<br>操作対象を削除する。 |

https://docs.ansible.com/ansible/2.9/modules/file_module.html#file-module

---

# 8-4. file moduleの説明

## ■state オプション
```yaml
tasks:
  - name: delete file
    file:
      path: /home/ec2-user/yokogushi_contents_team/ansible_practice/yamamoto/sample.txt
      state: absent
```
pathで指定したファイルを削除します。

---

# 8-5. copy moduleの演習
```yaml
tasks:
  - name: register vyos config
    vyos_command:
      commands:
        - show configuration commands
    register: result_config
```
上記のように記述すると、hostsで指定したvyosのコンフィグを取得し、
変数result_configにコンフィグを書き込むことができます。

Q1.
このresult_configの中身を、
vyos_config.txtというファイルに権限0644で出力して下さい。

---

# 8-5. copy moduleの演習
```yaml
tasks:
  - name: register vyos config
    vyos_command:
      commands:
        - show configuration commands
    register: result_config

    - name: save configuration
      copy:
        content: "{{ result_config.stdout[0] }}"
        dest: /home/ec2-user/yokogushi_contents_team/ansible_practice/08_copy_file_modules/{{ inventory_hostname }}.log
        mode: 0644
```
A1.
上記のように記述すると、課題を解決することができます。
※stdout[0]をつけないと、改行されずに変数の中身がファイルに記述されます

---
# 8-6. file moduleの演習

Q2. 
/home/ec2-user/yokogushi_contents_team/ansible_practice配下に、
configureという名前のディレクトリを作成し、
Q1で取得したファイルを作成したディレクトリに移動してください。

また、/home/ec2-user/yokogushi_contents_team/ansible_practice/08_copy_file_modulesに残っている、
vyos01.log,vyos02.logを削除してください。

---
# 8-6. file moduleの演習

```yaml
tasks:
  - name: make directory
    file:
      path: /home/ec2-user/yokogushi_contents_team/configure
      state: directory
      mode: 0755
    register: directory_info

  - name: copy configure file
    copy:
      src: /home/ec2-user/yokogushi_contents_team/ansible_practice/08_copy_file_modules/{{ inventory_hostname }}.log
      dest: "{{ directory_info.path }}"
      mode: 0655

  - name: delete file
    file:
      path: /home/ec2-user/yokogushi_contents_team/ansible_practice/08_copy_file_modules/{{ inventory_hostname }}.log
      state: absent
```
A2.
上記のように記述すると、課題を解決することができます。

---

# 備考
registerの内容を変数に保存すると、
stdoutとstdout_linesの2つのデータ形式が保存されます。

上記の2つをdebug moduleでターミナルに表示させる場合と、
copy moduleのcontentでファイルに書き出した場合で、
出力が改行されるかをまとめました。

|  | stdout | stdout[0] | stdout_lines | stdout_lines[0] |
| - | :-: | :-: | :-: | :-: |
| ターミナル出力 | 改行されない | 改行されない | 改行される | 改行される |
| ファイル出力 | 改行されない | 改行される | 改行されない | 改行されない |

---

# 備考

"{{ result_configure.stdout }}"の場合

```
・コンソールの出力
TASK [debug vyos] *******************************************
ok: [vyos01] => {
    "msg": [
        "set high-availability vrrp group service_nw01 interface 'eth1'\nset 
        (以降、改行の無い出力)

・ファイルの内容
["set high-availability vrrp group service_nw01 interface 'eth1'\nset
(以降、改行の無い出力)
```

---

# 備考

"{{ result_configure.stdout[0] }}"の場合

```
・コンソールの出力
TASK [debug vyos] *******************************************
ok: [vyos01] => {
    "msg": [
        "set high-availability vrrp group service_nw01 interface 'eth1'\nset 
        (以降、改行の無い出力)

・ファイルの内容
set high-availability vrrp group service_nw01 interface 'eth1'
set high-availability vrrp group service_nw01 priority '150'
set high-availability vrrp group service_nw01 virtual-address '192.168.1.254/24'
set high-availability vrrp group service_nw01 vrid '10'
(以降、改行された出力)
```
---

# 備考

"{{ result_configure.stdout_lines }}"と
"{{ result_configure.stdout_lines[0] }}"の場合

```
・コンソールの出力
TASK [debug vyos] *******************************************
ok: [vyos01] => {
    "msg": [
        [
            "set high-availability vrrp group service_nw01 interface 'eth1'",
            "set high-availability vrrp group service_nw01 priority '150'",
            "set high-availability vrrp group service_nw01 virtual-address '192.168.1.254/24'",
            "set high-availability vrrp group service_nw01 vrid '10'",
            (以降、改行された出力)

・ファイルの内容
["set high-availability vrrp group service_nw01 interface 'eth1'\nset
(以降、改行の無い出力)
```
