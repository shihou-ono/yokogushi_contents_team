---
marp: true
theme: apc_confidential
size: 16:9
---
<!--
class: title
-->

# vyosを触ってみよう

---

<!--
class: slide
paginate: true
-->

# vyosとは
Wikipediaを貼る


---

<!--
class: slide
paginate: true
-->

# vyosにlogin
## 事前準備
```
cd yokogushi_contents_team/init_settings/
docker-compose up -d
ansible-playbook -i inventory.ini container_setting.yml
```

## vyos01にlogin
containerに直接入るのであれば
```
docker exec -it vyos01 su - vyos
```

sshするのであれば
```
ssh vyos@10.0.0.2
（初回login時は yes を選択）
pw: vyos
```

---

<!--
class: slide
paginate: true
-->

# vyosでshow command 取得
出力をページングしない（全行一括表示する）設定
```
vyos@vyos01:~$ set terminal length 0
```
interfaceの状態確認
```
vyos@vyos01:~$ show interfaces
Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down
Interface        IP Address                        S/L  Description
---------        ----------                        ---  -----------
eth0             10.0.0.2/24                       u/u
eth1             192.168.1.252/24                  u/u
                 192.168.1.254/24
eth2             192.168.2.252/24                  u/u
                 192.168.2.254/24
lo               127.0.0.1/8                       u/u
vyos@vyos01:~$
```

---

<!--
class: slide
paginate: true
-->

# vyosでshow command 取得
config取得（Tree形式）
```
vyos@vyos01:~$ show configuration
high-availability {
    vrrp {
        group service_nw01 {
            interface eth1
            priority 150
            virtual-address 192.168.1.254/24
            vrid 10
        }
<snip>
```
config取得（Set形式）
```
vyos@vyos01:~$ show configuration commands
set high-availability vrrp group service_nw01 interface 'eth1'
set high-availability vrrp group service_nw01 priority '150'
set high-availability vrrp group service_nw01 virtual-address '192.168.1.254/24'
set high-availability vrrp group service_nw01 vrid '10'
<snip>
```

---

<!--
class: slide
paginate: true
-->

# vyosで設定
configure modeに入る
```
vyos@vyos01:~$ configure
[edit]
vyos@vyos01#
```
working configをtreeで表示
(working configとは設定途中のconfigのこと。投入した設定がなければ、active configと同内容である。)
```
vyos@vyos01# show
 high-availability {
     vrrp {
         group service_nw01 {
             interface eth1
             priority 150
             virtual-address 192.168.1.254/24
             vrid 10
         }
<snip>
```
---

<!--
class: slide
paginate: true
-->

# vyosで設定

configのinterfaces配下の設定を確認
```
vyos@vyos01# show interfaces
 ethernet eth1 {
     address 192.168.1.252/24
     ipv6 {
         address {
             no-default-link-local
         }
     }
 }
<snip>
```
同じ"show interfaces"でも、operation modeではinterfaceの状態が出力され、
一方、configure modeではinterfaces配下のconfigが出力される。

---

<!--
class: slide
paginate: true
-->

# vyosで設定

active(=running)とworking(=candidate)のconfig比較にはcompareを使用する。
設定投入前には両者に差分がないことを確認
```
vyos@vyos01# compare
No changes between working and active configurations.
```
設定追加例: interfaceにdescriptionを付ける
```
vyos@vyos01# set interfaces ethernet eth1 description to_service_nw01
```
compareで比較
```
vyos@vyos01# compare
[edit interfaces ethernet eth1]
+description to_service_nw01
```
追加設定には+マークが付く。
無事投入されていること、想定外の設定がないことを確認できる。

---

<!--
class: slide
paginate: true
-->

# vyosで設定

showでも追加設定に+が付いてることを確認できる
```
vyos@vyos01# show interfaces ethernet eth1
 address 192.168.1.252/24
+description to_service_nw01
 ipv6 {
     address {
         no-default-link-local
     }
 }
```
working configをactive configに上書きする
```
vyos@vyos01# commit
```
runnningとworkingのconfigに差分がないことを確認
```
vyos@vyos01# compare
No changes between working and active configurations.
```

---

<!--
class: slide
paginate: true
-->

# vyosで設定

startup configに上書きする
```
vyos@vyos01# save
Saving configuration to '/config/config.boot'...
Done
```

exitでconfigure modeから抜ける
```
vyos@vyos01# exit
exit
vyos@vyos01:~$
```

active configに反映されていることを確認
```
vyos@vyos01:~$ show configuration commands
<snip>
set interfaces ethernet eth1 description 'to_service_nw01'
<snip>
```

---

<!--
class: slide
paginate: true
-->

# vyosで設定

設定削除例: 先ほど投入した設定を削除する
```
vyos@vyos01:~$ configure
[edit]
vyos@vyos01# delete interfaces ethernet eth1 description
```
compareする
```
vyos@vyos01# compare
[edit interfaces ethernet eth1]
-description to_service_nw01
```
削除設定には-マークが付く。無事投入されていること、想定外の設定がないことを確認。

commitしてsaveする
```
vyos@vyos01# commit

vyos@vyos01# save
```

---

<!--
class: slide
paginate: true
-->

# 演習

1. vyos02のinterfaceのdescription設定
    1. vyos02のeth1のinterfaceに"to_service_nw01"、eth2のinterfaceに"to_service_nw02"というdescriptionを設定し、active configに反映されたことを確認する。
    1. 設定した内容を削除する
1. vrrpのpriority値変更による経路変更
参考: 構成図
    1. show vrrpコマンドにてvyos01,02の事前状態を確認する
    1. vrrpのpriority値を変更して往復の通信がvyos02を通るように設定する
    1. show vrrpコマンドにてvyos01,02の事後状態を確認する
    1. vrrpのpriority値を元に戻して、vyos01を通るように設定し、状態確認をする。
    1. ここまでやって余裕がある人は、各hostにloginしてtracerouteにて経路が変化することも確認する。
    host01へのlogin方法は以下
    ```
    docker exec -it host01 /bin/bash
    ```
