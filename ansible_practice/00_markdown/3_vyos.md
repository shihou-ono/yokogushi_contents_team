# vyosを触ってみよう
---

# vyosとは
- オープンソースで開発されているネットワークOS
- おもにソフトウェアルータとして運用
- 今回は、dockerコンテナでvyosを構築しています

参考：https://ja.wikipedia.org/wiki/VyOS

---

<!--
class: slide
paginate: true
-->

# vyos(host)にlogin
## 事前準備

vyosの初期構築として、以下を実施(初期構築時に実施済み)
```
cd yokogushi_contents_team/init_settings/
docker-compose up -d
ansible-playbook -i inventory.ini container_setting.yml
```

EC2インスタンスを起動すると、dockerコンテナ(vyos・host)は停止状態(stop)となっているため、
起動する(start)必要がある。  
トレーニング時や宿題時は以下のコマンドを実行して、vyosを起動する(ここでは、hostも起動します)

すべてのコンテナ(vyos01,vyos02,host01,host02)を起動する
```
docker start $(docker ps -aq)
```
1台のコンテナだけ起動する(例：vyos01のみ)
```
docker start vyos01
```
コンテナの起動状態を確認する  
出力されていない場合、起動していない
```
docker ps
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
参考：https://docs.google.com/presentation/d/1Z5oyxRJH1G_lImkciK9mhdvWkzOOvG4Z/edit#slide=id.p1

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
<skip>
```

config取得（Set形式）

```
vyos@vyos01:~$ show configuration commands
set high-availability vrrp group service_nw01 interface 'eth1'
set high-availability vrrp group service_nw01 priority '150'
set high-availability vrrp group service_nw01 virtual-address '192.168.1.254/24'
set high-availability vrrp group service_nw01 vrid '10'
<skip>
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
<skip>
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
<skip>
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
<skip>
set interfaces ethernet eth1 description 'to_service_nw01'
<skip>
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
    2. 1で設定した内容を削除する

2. vrrpのpriority値変更による経路変更
参考: 構成図
    1. show vrrpコマンドにてvyos01,02の事前状態を確認する
    2. vrrpのpriority値を変更して往復の通信がvyos02を通るように設定する
    3. show vrrpコマンドにてvyos01,02の事後状態を確認する
    4. vrrpのpriority値を元に戻して、vyos01を通るように設定し、状態確認をする。
    5. ここまでやって余裕がある人は、各hostにloginしてtracerouteにて経路が変化することも確認する。
    host01へのlogin方法は以下
    ```
    docker exec -it host01 /bin/bash
    ```

# 解答例

1. vyos02のinterfaceのdescription設定
    1. vyos02のeth1のinterfaceに"to_service_nw01"、eth2のinterfaceに"to_service_nw02"というdescriptionを設定し、active configに反映されたことを確認する。
    ```
    (venv) [ec2-user@ip-172-31-42-108 yokogushi_contents_team]$ docker exec -it vyos02 su - vyos
     vyos@vyos02:~$ show configuration commands 
     <skip>
     interfaces {
    ethernet eth1 {
        address 192.168.1.253/24
        description to_service_nw01
        ipv6 {
            address {
                no-default-link-local
            }
        }
    }
    ethernet eth2 {
        address 192.168.2.253/24
        description to_service_nw02
        ipv6 {
            address {
                no-default-link-local
            }
     <skip>
     vyos@vyos02:~$ 
     vyos@vyos02:~$ configure 
     [edit]
     vyos@vyos02# set interfaces ethernet eth1 description to_service_nw01
     [edit]
     vyos@vyos02# set interfaces ethernet eth2 description to_service_nw02
     [edit]
     vyos@vyos02# compare 
     [edit interfaces ethernet eth1]
     +description to_service_nw01
     [edit interfaces ethernet eth2]
     +description to_service_nw02
     [edit]
     vyos@vyos02# commit
     [edit]
     vyos@vyos02# save
     Saving configuration to '/config/config.boot'...
     Done
     [edit]
     vyos@vyos02# 
     [edit]
     vyos@vyos02# exit
     exit
     vyos@vyos02:~$ show configuration commands 
     <skip>
     set interfaces ethernet eth1 address '192.168.1.253/24'
     set interfaces ethernet eth1 description 'to_service_nw01'
     set interfaces ethernet eth1 ipv6 address no-default-link-local
     set interfaces ethernet eth2 address '192.168.2.253/24'
     set interfaces ethernet eth2 description 'to_service_nw02'
     set interfaces ethernet eth2 ipv6 address no-default-link-local
     <skip>
    ```
