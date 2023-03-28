# USB NiMH ゆっくり充電器 (USB NiMH Slow Charger)

## 概要

[秋月電子](https://akizukidenshi.com/)の[いたわりNiCd充電器 キット](https://akizukidenshi.com/download/kairo/%E3%83%87%E3%83%BC%E3%82%BF/%E5%85%85%E9%9B%BB%E5%99%A8%E9%96%A2%E4%BF%82/H001%E3%81%84%E3%81%9F%E3%82%8F%E3%82%8ANiCd_.pdf)を元に、USBを電源とし、ニッケル水素電池を1本単位で2本まで充電できるようにした充電器です。

※単3と単4をそれぞれ2本ずつの計4本充電できるバージョンは[別ブランチ](https://github.com/k-takata/PCB_USB_NiMH_Charger/tree/batt-4)で準備しています。

## 使用したソフトウェア

KiCad 7.0


## 回路図

[![schema](https://raw.githubusercontent.com/k-takata/PCB_USB_NiMH_Charger/master/images/schema.png)](https://raw.githubusercontent.com/k-takata/PCB_USB_NiMH_Charger/master/images/schema.pdf)

## 基板パターン図

![PCB pattern](https://raw.githubusercontent.com/k-takata/PCB_USB_NiMH_Charger/master/images/pcb-pattern.png)

## 部品表

| Reference           |個数|値    | 説明 |
|---------------------|----|------|------|
|BT1, BT2             |   2|      |[単3電池ボックス](https://akizukidenshi.com/catalog/g/gP-00308/)|
|C1, C4, C5, C6       |   4|0.1μF|積層セラミックコンデンサー|
|C2                   |   1|220μF|電解コンデンサー φ6.3[^1] (100 ~ 470μF)|
|C3                   |   1| 10μF|電解コンデンサー|
|D1                   |   1|[11EQS04](https://akizukidenshi.com/catalog/g/gI-11363/)|ショットキーバリアダイオード|
|D2, D3               |   2|      |赤色LED|
|F1                   |   1|  1.1A|[ポリヒューズ](https://akizukidenshi.com/catalog/g/gP-00507/)|
|J1                   |   1|      |[USB Type-C コネクター](https://akizukidenshi.com/catalog/g/gK-13080/)、[同等品](https://akizukidenshi.com/catalog/g/gK-15426/)でも可|
|J2                   |   1|      |ピンヘッダー 1x2、強制充電用|
|J3, J4               |   2|      |ピンヘッダー 1x2、単3/単4切り替え用|
|Q1                   |   1|2SA1015||
|Q2, Q4               |   2|2SC1815||
|Q3, Q5               |   2|[2SB834](https://akizukidenshi.com/catalog/g/gI-08747/)||
|R1, R2               |   2|5.1kΩ|緑茶赤金、小型|
|R3                   |   1| 10kΩ|茶黒橙金、半固定抵抗RV1と同じ値|
|R4, R5, R9, R11, R15 |   5| 10kΩ|茶黒橙金|
|R6, R12              |   2|  15Ω|茶緑黒金、1/2W 小型|
|R7, R13              |   2|  10Ω|茶黒黒金、1/2W 小型|
|R8, R14              |   2|  1MΩ|茶黒緑金|
|R10, R16             |   2| 680Ω|青灰茶金|
|RV1                  |   1| 10kΩ|抵抗R3と同じ値、[GF063P](https://akizukidenshi.com/catalog/g/gP-14905/)|
|U1                   |   1|[LM393](https://akizukidenshi.com/catalog/g/gI-16987/)|2回路入りコンパレーター|
|U2                   |   1|[S-812C33](https://akizukidenshi.com/catalog/g/gI-03289/)|低損失CMOS三端子レギュレーター 3.3V 50mA|

[^1]: このサイズならば、まっすぐ足が奥まで挿せる。

## いたわりNiCd充電器からの変更点

* 電源をACアダプターからUSB Type-Cに変更。
  - USB PD機器からも確実に電源を得られるようにするため、CC1, CC2には5.1kΩを接続。
  - 安全のため、ポリヒューズを使用。(1.1Aを使用したが0.5A程度でも十分かもしれない。)
* 充電を2本単位から1本単位に変更。
  - 基準電圧を5.0Vから3.3Vに変更。(U2をS-81350からS-812C33に変更。)
  - 温度補償用のダイオード1S2076A 2本をショットキーバリアダイオード1本に変更。  
    NiMH電池の充電終了時電圧には1本当たり約-3mV/Kの温度係数があります。PN接合には-2mV/Kの温度係数があることから、元の回路ではこれを3直列とし、2本分の-6mV/Kの補正を行っていました。ショットキーバリアダイオードの正確な温度係数は調べられませんでしたが、PN接合の温度係数より小さいよう(-1.0 ~ -1.5mV/K程度?)ですので、これをもって1本分の補正としています。
* 秋月電子の[B基板](https://akizukidenshi.com/catalog/c/cboard10/)に実装できるよう、充電本数は2本に変更。
  - 充電回路は2セットになるため、コンパレーターを[LM339](https://akizukidenshi.com/catalog/g/gI-00436/)からLM393に変更。
* 充電電流を単3と単4用に切り替えられるようにJ3, J4を用意。
  - J3を開放すると電流設定抵抗はR6の15Ωとなり、BT1の充電電流は単4用の約70mA (= 1.05V / 15Ω)となります。(単4用変換アダプターなどを使用することを想定。)
  - J3を接続すると電流設定抵抗はR6とR7の合成で6Ωとなり、BT1の充電電流は単3用の約175mA (= 1.05V / 6Ω)となります。(2000mAhの場合は0.09C程度になるので充電時間は17時間程度が目安です。)
  - BT2については同様にJ4で設定できます。
* 強制充電用にJ2を用意。
  - 通常充電モード: 通常、J2は接続して使います。充電終止電圧の監視が行われます。
  - 強制充電モード: J2を開放すると、Vrefは3.3Vに設定され、充電終止電圧の監視が行われません。  
    新品のNiMH電池の内部抵抗は0.1Ω程度のようですが、寿命が近づいてくると徐々に内部抵抗が大きくなります。例えば内部抵抗が10Ωにもなると、70mAの充電電流を流すと端子電圧が0.7Vも上昇するため、正常に充電電圧の監視を行うことはできません。こうなってしまうと通常の充電器では充電することができません。そのような場合でも強制充電モードを使えば充電が可能です。ただし、充電電圧の監視がなくなってしまいますので、10～15時間程度で電池を外すようにしてください。
* 2SB948が入手できなかったので、簡単に入手できた2SB834に変更。
* RV1の回転方向を逆にし、右に回すほどVrefが高くなるように変更。


## 調整

J2を接続し、RV1を右に回しきって、700mAhの単4なら15時間程度、2000mAhの単3なら17時間程度充電します。この際、J3, J4は電池に合わせて設定しておいてください。電池を外して20分程度置いてから、再度電池を入れます。RV1を回して、LEDが点滅する位置に合わせて終了です。Vrefの電圧は1.40~1.45V程度になっているのが想定です。

## 完成品

[![完成品](https://raw.githubusercontent.com/k-takata/PCB_USB_NiMH_Charger/master/images/usb-slow-charger-thumb.jpg)](https://raw.githubusercontent.com/k-takata/PCB_USB_NiMH_Charger/master/images/usb-slow-charger.jpg)

## License

CC0
