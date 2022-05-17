---
marp: true
theme: apc_confidential
size: 16:9
---
<!--
class: title
-->

# 5. 変数

---

<!--
class: slide
paginate: true
-->

# 5-1. 変数とは？

特定の文字列に対して様々な値を入れることができる箱のようなもの。
→このように変数に対して文字列や数字などの値を入れることを**変数定義**という。


<br>

数学でよく見る以下の式も変数の一種である。
```json
x = 2      <-「x」という変数は数字の「2」として定義されている

y = 3      <-「y」という変数は数字の「3」として定義されている

x + y = 5  <-「x=2」+「y=3」のため右辺は「5」となる
```

---

# 5-1. 変数とは？

ただし、プログラミング上での変数定義では必ずしも左辺と右辺がイコールになるわけではない。
以下に、プログラミングならではの変数定義例を記載する。

<br>


```python
$ python3

>>> x = 2       <-A. 「x」という変数は数字の「2」として定義されている
>>> x = 3 + x   <-B. 「x」という変数は新たに「3 + x」として定義される
>>> print(x)　　<-C. 　BではA時点で定義された「x=2」が右辺の「x」に代入されるため、Cの「x」の出力結果は「3 + 2 = 5」となる
5
>>>
```

---

# 5-2. 変数の説明

変数は任意の値を定義することが多いが、デフォルトで定義されているものも存在する。
playbookにおいて任意の値を定義する方法は以下の2つである。

- `vars`で定義
- `set_fact`で定義

<br>

## ■vars
varは「variable(変数)」の略で、`vars`とは変数を集めておく場所のようなもの。
次ページではAnsibleでよく使用する`vars`について解説する。


---

# 5-2. 変数の説明

以下にplaybook内のVarsセクションにてよく使用される`vars`について紹介する。下記のパラメータにより
定義された変数はTasksセクションで使用することができる。

<br>

| パラメータ | 説明 |
| :----- | :---------------------- | 
| `vars` | playbook内で変数定義する際に使用するパラメータ。 |
| `vars_files` | playbook外から変数定義されたyamlファイルを読み込む際に使用するパラメータ。複数ファイルを読み込むことも可能。 |
| `vars_prompt` | playbook実行時に定義した変数の値を対話的にユーザへ求めるようにするパラメータ。記録として残したくない情報(パスワードなど)などを使用したい時に使用することが多い。 |


---

# 5-2. 変数の説明

その他にもplaybook外でディレクトリを作成し、グループ/ホスト共通で使用する変数ファイルを作成することもできる。

<br>

| ディレクトリ名 | 説明 |
| :----- | :---------------------- | 
| `group_vars` | 対象のグループが共通で使用することのできる変数ファイルを格納するディレクトリ。 |
| `host_vars` | 対象のホストが共通で使用することのできる変数ファイルを格納するディレクトリ。 |

<br>

`group_vars`について横地さんが記事にされているため紹介する。ブログの内容は[こちら](https://tekunabe.hatenablog.jp/entry/2018/12/15/ansible_group_vars_dir)をクリック。



---

# 5-2. 変数の説明

## ■set_fact

Tasksセクションで変数定義をしたい時に使用するパラメータ。使用方法は先程紹介したVarsセクションで使用するパラメータの中の`vars`と似ている。`set_fact`の使用例を以下に記載。

```yaml
---
- name: variable_sample
  hosts: vyos01
  gather_facts: no
  
  tasks:
    - name: test
      set_fact:                   
        test: "Hello Ansible!"  # <-「set_fact:」配下で「<変数名>: <値>」で変数定義を行う  
```

---

# 5-2. 変数の説明

次にデフォルトで定義されているものとして、以下にAnsibleが用意している変数を紹介する。

<br>

| 変数の種類 | 説明 |
| :----- | :---------------------- | 
| マジック変数  | ユーザが直接設定することのできない変数。<br>Ansibleがシステム内の状態を反映してこの変数を常に上書きしている。 |
| ファクト変数  | 現在実行中のホストに関連する情報(inventory_hostname)を含む変数。<br>playbook内で`gather_facts: no`を指定した場合は使用不可。 |
| 接続変数 | ターゲットホストへのアクション実行方法を具体的に設定する時に使用。 |

<br>

変数についてのAnsibleの公式ドキュメントは[こちら](https://docs.ansible.com/ansible/2.9_ja/reference_appendices/special_variables.html)

---

# 5-2. 変数の説明

## ■マジック変数


以下にマジック変数の中でも特に使用頻度が高いものを紹介する。

<br>

| 変数名 | 説明 |
| :----- | :---------------------- | 
| `group_names` | 現在実行中のホストが所属するグループの一覧。 |
| `groups` | インベントリー内の全グループを含むディクショナリーマップ。 |
| `inventory_hostname` | 現在実行中のホストのイベントリー名。 |
| `ansible_play_name` | 現在実行されているplaybookの名前。 |
| `playbook_dir` | 現在実行されているplaybookのディレクトリパス。 |
| `inventory_dir` | インベントリファイルのディレクトリパス。 |

---

# 5-2. 変数の説明

## ■ファクトのためのマジック変数

ファクトとは、ホストに関するあらゆる情報を保存した変数のことを指す。
`hostvars`というマジック変数を使用すると、現在実行中のホストを含むターゲットホストのファクトにアクセスすることが可能になる。

<br>

| 変数名 | 説明 |
| :----- | :---------------------- | 
| `hostvars` | ターゲットホストのファクトを集約して格納した変数。 |

---

# 5-2. 変数の説明

## ■ファクト変数


以下にファクト変数の一部を紹介する。

<br>

| 変数名 | 説明 |
| :----- | :---------------------- | 
| `ansible_facts` | `gather_facts: no` の場合はターゲットホストのファクト情報が格納されない。`gather_facts: no` であっても、未定義ではなく、値は空になる。 |

---

# 5-2. 変数の説明

## ■接続変数


以下に接続変数の中でも特に使用頻度が高いものを紹介する。

<br>

| 変数名 | 説明 |
| :----- | :---------------------- | 
| `ansible_network_os` | ターゲットホストのプラットフォーム情報。 |
| `ansible_connection` | ターゲットホストへの接続方式。 |
| `ansible_user` | SSH接続するときのユーザ情報。 |
| `ansible_password` | SSH接続するときのパスワード情報。 |
| `become` | root権限昇格の有無。`false`を指定することで昇格する。 |

---

# 5-2. 変数の説明

## ■接続変数


接続変数は以下のインベントリファイルのように`vars`として設定することができる。
※接続変数でなくても変数定義は可能だが、インベントリファイル内では接続変数を記載することが多い。

```yaml
[vyos]
vyos01 ansible_host=10.0.0.2
vyos02 ansible_host=10.0.0.3

[vyos:vars]                       # <-vyosグループで指定されたホストへの接続で使用するvarsを定義
ansible_network_os=vyos
ansible_connection=network_cli
ansible_user=vyos
ansible_password=vyos               
```

---

# 5-3. 変数の実習

以下の目的を達成するためのplaybookをそれぞれ作成してみる。

## ■目的
1. Varsセクションにて`vars`を使用して変数定義を行い、変数の中身をdebugで出力する
2. `set_fact`を使用して変数定義を行い、変数の中身をdebugで出力する
3. マジック変数の中身をdebugで出力する
4. ファクト変数の中身の一部をdebugで出力する
<br>

---

# 5-3. 変数の実習

## 1. Varsセクションにて`vars`を使用して変数定義を行い、変数の中身をdebugで出力する

以下のplaybookを作成する。

```yaml
$ vi variable_sample1.yml
---
- name: variable_sample1
  hosts: localhost
  gather_facts: false
  
  vars:
    test: "Hello Ansible!"  # <-「test1」という変数に「Hello Ansible!」という文字列を定義

  tasks:
    - name: debug
      debug:
        var: test  # <-Varsセクションで定義した「test1」の変数の中身を出力
```

---

# 5-3. 変数の実習

以下は作成したplaybookを実行/出力例である。

<br>

```yaml
$ ansible-playbook -i inventory.ini variable_sample1.yml 

PLAY [variable_sample1] ****************************************************************************

TASK [debug] ***************************************************************************************
ok: [localhost] => {
    "test": "Hello Ansible!"  # <-想定通りの出力を確認
}

PLAY RECAP *****************************************************************************************
localhost : ok=1  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

# 5-3. 変数の実習

## 2. `set_fact`を使用して変数定義を行い、変数の中身をdebugで出力する

以下のplaybookを作成する。

```yaml
$ vi variable_sample2.yml
---
- name: variable_sample2
  hosts: localhost
  gather_facts: false
  
  tasks:
    - name: variable definition
      set_fact:                   
        test: "Hello Ansible!"  # <-「set_fact」を使用して「test2」という変数に「Hello Ansible!」という文字列を定義

    - name: debug
      debug:
        var: test  # <-「set_fact」で定義した「test2」の変数の中身を出力
```

---

# 5-3. 変数の実習

以下は作成したplaybookを実行/出力例である。

<br>

```yaml
$ ansible-playbook -i ../inventory.ini variable_sample2.yml 

PLAY [variable_sample2] ********************************************************************************************************

TASK [test] ********************************************************************************************
ok: [localhost]

TASK [debug] *******************************************************************************************
ok: [localhost] => {
    "test": "Hello Ansible!"  # <-想定通りの出力を確認
}

PLAY RECAP *********************************************************************************************
localhost : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

# 5-3. 変数の実習

## 3. マジック変数の中身をdebugで出力する

以下のplaybookを作成する。

```yaml
$ vi variable_sample3.yml
---
- name: variable_sample3
  hosts: localhost
  gather_facts: false
  
  tasks:
    - name: debug
      debug:
        var: ansible_play_name  # <-「ansible_play_name」はマジック変数のため、変数定義の必要なし
```

---

# 5-3. 変数の実習

以下は作成したplaybookを実行/出力例である。

<br>

```yaml
$ ansible-playbook -i ../inventory.ini variable_sample3.yml 

PLAY [variable_sample3] **************************************************************************************************

TASK [debug] *************************************************************************************************************
ok: [localhost] => {
    "ansible_play_name": "variable_sample3"  # <-「ansible_play_name」に格納されたplaybookの名前が出力されていることを確認
}

PLAY RECAP ***************************************************************************************************************
localhost : ok=1  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

# 5-3. 変数の実習

## 4. ファクト変数の中身の一部をdebugで出力する

以下のplaybookを作成する。

```yaml
---
- name: variable_sample4-1
  hosts: vyos01 
                            # <-「gather_facts: no」を省略した場合は「ansible_facts」にファクト情報が格納される。
  tasks:
    - name: debug
      debug:
        var: ansible_facts  # <-「ansible_facts」の中身を出力
```

---

# 5-3. 変数の実習

以下は作成したplaybookを実行/出力例である。

```yaml
PLAY [variable_sample4] ********************************************************************************************

TASK [Gathering Facts] *********************************************************************************************
[WARNING]: Ignoring timeout(10) for vyos_facts
[WARNING]: default value for `gather_subset` will be changed to `min` from `!config` v2.11 onwards
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but future installation of another Python interpreter could change this.
See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
ok: [vyos01]

TASK [debug] *******************************************************************************************************
ok: [vyos01] => {
    "ansible_facts": {       # <-「ansible_facts」の中身全てを出力すると量が多すぎる
        "discovered_interpreter_python": "/usr/bin/python",
        "net_api": "cliconf",
        "net_commits": [

---------------------------------(snip)---------------------------------

        "net_hostname": "vyos01",
        "net_neighbors": {},
        "net_python_version": "2.7.18",
        "net_serialnum": null,
        "net_system": "vyos",
        "net_version": "VyOS 1.4-rolling-202108071508",
        "network_resources": {}
    }
}

PLAY RECAP **********************************************************************************************************
vyos01 : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

# 5-3. 変数の実習

`ansible_facts`の中身全てを出力すると膨大な量があったため、今回は`ansible_facts`に格納されているディクショナリの中の`net_hostname`の値(value)を取り出してみる。以下はそのplaybookである。

```yaml
---
- name: variable_sample4-2
  hosts: vyos01 
                            # <-「gather_facts: no」を省略した場合は「ansible_facts」にファクト情報が格納される。
  tasks:
    - name: debug
      debug:
        var: ansible_facts.net_hostname  # <-「ansible_facts」の中身を出力
```

---

# 5-3. 変数の実習

以下は作成したplaybookを実行/出力例である。

<br>

```yaml
PLAY [variable_sample4] *****************************************************************************************

TASK [Gathering Facts] ******************************************************************************************
[WARNING]: Ignoring timeout(10) for vyos_facts
[WARNING]: default value for `gather_subset` will be changed to `min` from `!config` v2.11 onwards
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but future
installation of another Python interpreter could change this.
See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
ok: [vyos01]

TASK [debug] ****************************************************************************************************
ok: [vyos01] => {
    "ansible_facts.net_hostname": "vyos01"  # <-想定通りの出力になっていることを確認
}

PLAY RECAP ******************************************************************************************************
vyos01 : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0 
```

---

# 5-4.  演習

**Q1. 以下のplaybookを実行した場合に、`TASK [debug]`にて「"Hello":【 】」の【 】として表示される文字列を
　　1つ選択してください。**

```yaml
---
- name: variable_exercise1
  hosts: localhost
  gather_facts: false
  
  tasks:
    - set_fact:
        HelIo: "Hello Ansible!"

    - debug:
        var: Hello
```

1. “VARIABLE IS NOT DEFINED!”
1. "Hello"
1. "Hello Ansible!"
1. "error"

---

# 5-4.  演習

**Q2. 以下のplaybookを実行した場合に、`TASK [debug]`にて「"ansible_play_name":【 】」の【 】として
　　表示される文字列を1つ選択してください。**

```yaml
---
- name: variable_exercise2
  hosts: localhost
  gather_facts: false
  
  tasks:
    - set_fact:
        ansible_play_name: "Hello Ansible!"

    - debug:
        var: ansible_play_name
```

1. Hello Ansible!
1. "Hello Ansible!"
1. "error"
1. "variable_exercise2"

---

# 5-4.  演習

**Q3. debugモジュールの`var`パラメータを使用し、playbook実行時に`TASK [debug]`にて「vyos01」と
　　表示させるplaybookを作成してください。**


---

# 5-4.  演習

**A1. 以下のplaybookを実行した場合に、`TASK [debug]`にて「"Hello":【 】」の【 】として表示される文字列を
　　1つ選択してください**

```yaml
- name: variable_exercise1
  hosts: localhost
  gather_facts: false
  
  tasks:
    - set_fact:
        HelIo: "Hello Ansible!"

    - debug:
        var: Hello
```

1. **“VARIABLE IS NOT DEFINED!”**
1. ~~"Hello"~~
1. ~~"Hello Ansible!"~~
1. ~~"error"~~

---

# 5-4.  演習

実際の出力結果を以下に記載。

<br>

```yaml
TASK [set_fact] *******************************************************************************
ok: [localhost]

TASK [debug] **********************************************************************************
ok: [localhost] => {
    "Hello": "VARIABLE IS NOT DEFINED!"
}

PLAY RECAP ************************************************************************************
localhost : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

# 5-4.  演習

よく変数部分をみてみると`set_fact`で定義している変数名が「Hello」ではなく、「HelIo」になっていることが分かる。変数名が間違えていたため、「Hello」は定義されていません(VARIABLE IS NOT DEFINED!)と表示されている。

<br>

```yaml
- name: variable_exercise1
  hosts: localhost
  gather_facts: false
  
  tasks:
    - set_fact:
        HelIo: "Hello Ansible!"  # <-「Hello」ではなく、「HelIo」になっている。

    - debug:
        var: Hello
```

---

# 5-4.  演習

**A2. 以下のplaybookを実行した場合に、`TASK [debug]`にて「"ansible_play_name":【 】」の【 】として
　　表示される文字列を1つ選択してください。**

```yaml
---
- name: variable_exercise2
  hosts: localhost
  gather_facts: no
  
  tasks:
    - set_fact:
        ansible_play_name: "Hello Ansible!"

    - debug:
        var: ansible_play_name
```

1. ~~Hello Ansible!~~
1. ~~"Hello Ansible!"~~
1. ~~"error"~~
1. **"variable_exercise2"**

---

# 5-4.  演習

実際の出力結果を以下に記載。

<br>

```yaml
PLAY [variable_exercise2] ******************************************************************************

TASK [set_fact] ****************************************************************************************
ok: [localhost]

TASK [debug] *******************************************************************************************
ok: [localhost] => {
    "ansible_play_name": "variable_exercise2"
}

PLAY RECAP *********************************************************************************************
localhost : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0 
```

---

# 5-4.  演習

`set_fact`で定義した`ansible_play_name`という変数はマジック変数であり、現在実行されているplaybookの名前を変数の中に格納する。今回`set_fact`で「Hello Ansible!」という文字列を定義したが、それよりもマジック変数が優先されるため、出力結果は`variable_exercise2`となる。

<br>

```yaml
---
- name: variable_exercise2  # <-ここに記載されているplaybook名が「ansible_play_name」というマジック変数に格納される
  hosts: localhost
  gather_facts: false
  
  tasks:
    - set_fact:
        ansible_play_name: "Hello Ansible!"

    - debug:
        var: ansible_play_name
```

---

# 5-4.  演習

**A3. debugモジュールの`var`パラメータを使用し、playbook実行時に`TASK [debug]`にて「vyos01」と
　　表示させるplaybookを作成してください。**

以下に例として要件を満たすplaybookを記載。

```yaml
---
- name: variable_exercise3
  hosts: localhost
  gather_facts: false
  
  tasks:
    - set_fact:
        test_hostname: "vyos01"  # <-「set_fact」を使用して「test」という変数に「vyos01」という文字列を定義

    - debug:
        var: test_hostname  # <-「set_fact」で定義した「test」の変数の中身を出力
```

---

# 5-4.  演習

実際の出力結果を以下に記載。

<br>

```yaml
PLAY [variable_exercise3] ****************************************************************************************************

TASK [set_fact] ************************************************************************************
ok: [localhost]

TASK [debug] ***************************************************************************************
ok: [localhost] => {
    "test_hostname": "vyos01"
}

PLAY RECAP *****************************************************************************************
localhost : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0 
```

---

<!--
class: backcover
paginate: true
-->

[1]: https://blog-and-destroy.com/21376
[2]: https://blog-and-destroy.com/25199