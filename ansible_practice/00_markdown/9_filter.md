# 9. filter
---

# 9-1. filterとは？

- 主に変数等にパイプで繋げて値を整形するもの。
- filterには多くの種類があるが今回取り扱うのはいかに示すfilterに限定する。


| パラメータ | 説明  |
| :----- | :----- |
| type_debug | 変数の型 ( 変数に設定した値の型 ) をチェックしたり、変数に設定された値を強制的に別の型に変換します。 |
| default | 変数が未定義のとき、変数に設定する既定値を設定します。 |
| regex_search | 正規表現で文字列を検索する際に使用します。 |
| regex_replace | 正規表現を使用して文字列のテキストを置換します。　|
| tearnary | true, false, nullに応じてそれぞれ別の値を返す。 　|

---

# 6-2. filterの説明

## ■type_debug

---
- type_debugを用いて変数の型をチェックします。
playbookは以下のようになる。
```yaml
 tasks:
   - name: set_fact
      set_fact:
        test_int: 123

    - name: check_type
      debug:
        var: test_int | type_debug 
```
このplaybookは変数に入っている値の型を確認する内容になっている。

## ■default

---
- default filterを用いて変数の既定値を定義します。
playbookは以下のようになる。
```yaml
tasks:

    - name: debug fruits
      debug:
        var: item.color | default("green")
      vars:
        fruits:
          - banana:
            color: "yellow"
            hardness: "soft"
          - melon:
            hardness: "middle"
      loop: "{{ fruits }}"
```

このplaybookはmelonの変数の中に、定義されていないcolorの既定値(green)が入る内容になっている。

## ■regex filter

---
- regex_serchを用いて文字列の検索をします。
playbookは以下のようになる。
```yaml
- name: regex_search
  hosts: localhost
  gather_facts: false
  vars:
    sample:
       test1
       test2
       test3
  tasks:
    - name: search
      debug:
        var: sample | regex_search('test1')
```
このplaybookは、sampleデータの中からtest1を検索する内容になっている。

---
- regex_replaceを用いて文字列の置換をします。
playbookは以下のようになる。
```yaml
---
- name: regex_replace
  hosts: localhost
  gather_facts: false
 
  tasks:
  - name: regex
    debug:
      msg:  "{{ 'foobar' | regex_replace('^f.*o(.*)$', '\\1') }}"
```
このplaybookは、foobarの文字列を正規表現を使い、後方参照でbarに置換している内容になっている。
<br>
\*後方参照:\\1のようにバックスラッシュ後ろの数字番目の()に囲まれている文字列(.\*)を参照すること。

## ■ternary

---
- true, false, nullに応じて返す値を変えるフィルター。
playbookは以下のようになる。
```yaml
---
---
- name: ternary_exam
  hosts: localhost
  gather_facts: false

  tasks:
    - name: set flag
      set_fact:
        flag: 'test'


    - name: debug flag
      debug:
        msg: "{{ flag | ternary('flag is true', 'flag is false', 'flag is null') }}"
```
このplaybookは、変数に値が入っている場合は'flag is true'、値が正しくない場合は'flag is false'、値が入っていない場合は'flag is null'を返すようになっている。

# 6-3. filterの実習(ハンズオン)

## 1. debug_typeフィルターを用いて変数の型をそれぞれ確認するplaybookを作成する。
<br>
以下のplaybookを作成する。

```yaml
---
- name: debug_filter
  hosts: localhost
  gather_facts: false

  tasks:
    - name: check_type
      debug:
        msg: Type of "{{ item | type_debug }}"
      loop: "{{ variables }}"
      vars:
        variables:
          - 1
          - 0.9
          - "abc"
          - [1, 2, 3]
          - key: value
```

以下は作成したplaybookを実行/出力例である。

<br>

```yaml
PLAY [debug_filter] ********************************************************************************************************************************************************

TASK [check_type] **********************************************************************************************************************************************************
ok: [localhost] => (item= 1) => {
    "msg": "Type of \"int\""
}
ok: [localhost] => (item= 0.9) => {
    "msg": "Type of \"float\""
}
ok: [localhost] => (item=abc) => {
    "msg": "Type of \"str\""
}
ok: [localhost] => (item=[ 1, 2, 3 ]) => {
    "msg": "Type of \"list\""
}
ok: [localhost] => (item={'key': 'value'}) => {
    "msg": "Type of \"dict\""
}

PLAY RECAP *****************************************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

## 2. 以下のパスに、パーミッション666と644のテキストファイルをdefaultフィルターの既定値を644として作成する。
<br>

- /home/ec2-user/yokogushi_contents_team/ansible_practice/09_filter/test1.txt"

- /home/ec2-user/yokogushi_contents_team/ansible_practice/09_filter/test2.txt"

---
以下が作成したplaybookである。

<br>

```yaml
---
- name: default_exam
  hosts: localhost
  gather_facts: false

  tasks:
    - name: default1
      file:
        dest: "{{ item.path }}"
        state: touch
        mode: "{{ item.mode | default(666) }}"
      loop:
      - path: "/home/ec2-user/yokogushi_contents_team/ansible_practice/09_filter/test1.txt"
        mode: "664"
      - path: "/home/ec2-user/yokogushi_contents_team/ansible_practice/09_filter/test2.txt"

```
以下は作成したplaybookを実行/出力例である。
```yaml
PLAY [default_exam] ********************************************************************************************************************************************************

TASK [default1] ************************************************************************************************************************************************************
changed: [localhost] => (item={'path': '/home/ec2-user/yokogushi_contents_team/ansible_practice/09_filter/test1.txt', 'mode': '664'})
changed: [localhost] => (item={'path': '/home/ec2-user/yokogushi_contents_team/ansible_practice/09_filter/test2.txt'})

PLAY RECAP *****************************************************************************************************************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
作成したファイルのパーミッションの確認結果は以下となる。
```yaml
-rw-rw-r-- 1 ec2-user ec2-user   0 Nov 15 13:41 test1.txt
-rw-rw-rw- 1 ec2-user ec2-user   0 Nov 15 13:41 test2.txt
```

## 3. regex_serch フィルターを使って、特定の値を検索し、該当の値があった場合は'OK'とメッセージを表示させる。
<br>

---
以下が作成したplaybookである。

```yaml
---
- name: regex_search
  hosts: localhost
  gather_facts: false
  vars:
    sample:
       test1
       test2
       test3
  tasks:
    - name: "search"
      debug:
        msg: "OK"
      loop:
        - "test2"
        - "exam1"
      when:
        - sample | regex_search(item)
```

以下は作成したplaybookを実行/出力例である。
```yaml
PLAY [regex_search] ********************************************************************************************************************************************************

TASK [search] **************************************************************************************************************************************************************
ok: [localhost] => (item=test2) => {
    "msg": "OK"
}
skipping: [localhost] => (item=exam1) 

PLAY RECAP *****************************************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```



