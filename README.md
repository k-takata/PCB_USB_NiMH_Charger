# USB NiMH ゆっくり充電器 (USB NiMH Slow Charger) rev. 3

## 概要

[秋月電子](https://akizukidenshi.com/)の[いたわりNiCd充電器 キット](https://akizukidenshi.com/download/kairo/%E3%83%87%E3%83%BC%E3%82%BF/%E5%85%85%E9%9B%BB%E5%99%A8%E9%96%A2%E4%BF%82/H001%E3%81%84%E3%81%9F%E3%82%8F%E3%82%8ANiCd_.pdf)を元に、USBを電源とし、ニッケル水素電池を1本単位で単3、単4それぞれ2本まで充電できるようにした充電器です。

## 使用したソフトウェア

KiCad 7.0


## 回路図

[![schema](https://raw.githubusercontent.com/k-takata/PCB_USB_NiMH_Charger/batt-4/images/schema.png)](https://raw.githubusercontent.com/k-takata/PCB_USB_NiMH_Charger/batt-4/images/schema.pdf)

## 基板パターン図

![PCB pattern](https://raw.githubusercontent.com/k-takata/PCB_USB_NiMH_Charger/batt-4/images/pcb-pattern.png)

## 部品表

| Reference            |個数|値    | 説明 |
|----------------------|----|------|------|
|BT1, BT2              |   2|      |[単4電池ボックス](https://akizukidenshi.com/catalog/g/gP-02670/)|
|BT3, BT4              |   2|      |[単3電池ボックス](https://akizukidenshi.com/catalog/g/gP-00308/)|
|C1, C4, C5, C6, C7, C8|   6|0.1μF|積層セラミックコンデンサー|
|C2                    |   1|100μF|電解コンデンサー φ5[^1]|
|C3                    |   1| 10μF|[積層セラミックコンデンサー 10μF](https://akizukidenshi.com/catalog/g/gP-03095/)|
|D1                    |   1|[11EQS03L](https://akizukidenshi.com/catalog/g/gI-08997/)|ショットキーバリアダイオード|
|D2, D3, D4, D5        |   4|      |[低消費赤色LED](https://akizukidenshi.com/catalog/g/gI-16914/)|
|F1                    |   1|  1.1A|[ポリヒューズ](https://akizukidenshi.com/catalog/g/gP-00507/)|
|J1                    |   1|UJC-HP-3-SMT-TR|[USB Type-C コネクター 電源供給用](https://akizukidenshi.com/catalog/g/gC-16438/)|
|J2                    |   1|      |ピンヘッダー 1x2、強制充電用|
|Q1                    |   1|2SA1015||
|Q2, Q3, Q4, Q5        |   4|[2SB834](https://akizukidenshi.com/catalog/g/gI-08747/)|裏面に取り付け|
|R1, R2                |   2|5.1kΩ|緑茶赤金、小型|
|R3                    |   1| 10kΩ|茶黒橙金、半固定抵抗RV1と同じ値|
|R4, R5, R9, R13, R17  |   5| 10kΩ|茶黒橙金|
|R6, R10, R14, R18     |   4|  1MΩ|茶黒緑金|
|R7, R11               |   2|2.7kΩ|赤紫赤金、小型|
|R8, R12               |   2|  15Ω|茶緑黒金、1/2W 小型|
|R15, R19              |   2|1.6kΩ|茶青赤金、小型|
|R16, R20              |   2| 5.6Ω|緑青金金、1/2W 小型|
|RV1                   |   1| 10kΩ|抵抗R3と同じ値、[GF063P](https://akizukidenshi.com/catalog/g/gP-14905/)|
|U1                    |   1|[μPC339C](https://akizukidenshi.com/catalog/g/gI-12556/)|4回路入りコンパレーター、[LM339](https://akizukidenshi.com/catalog/g/gI-00436/)でも可|
|U2                    |   1|[S-812C33](https://akizukidenshi.com/catalog/g/gI-03289/)|低損失CMOS三端子レギュレーター 3.3V 50mA|

[^1]: このサイズならば、まっすぐ足が奥まで挿せる。

## いたわりNiCd充電器からの変更点

* 電源をACアダプターからUSB Type-Cに変更。
  - USB PD機器からも確実に電源を得られるようにするため、CC1, CC2には5.1kΩを接続。
  - 安全のため、ポリヒューズを使用。
* 充電を2本単位から1本単位に変更。
  - 基準電圧を5.0Vから3.3Vに変更。(U2をS-81350からS-812C33に変更。)
  - 温度補償用のダイオード1S2076A 2本をショットキーバリアダイオード1本に変更。  
    NiMH電池の充電終了時電圧には1本当たり約-3mV/Kの温度係数があります。PN接合には-2mV/Kの温度係数があることから、元の回路ではこれを3直列とし、2本分の-6mV/Kの補正を行っていました。ショットキーバリアダイオードの正確な温度係数は調べられませんでしたが、PN接合の温度係数より小さいよう(-1.0 ~ -1.5mV/K程度?)ですので、これをもって1本分の補正としています。
* 秋月電子の[B基板](https://akizukidenshi.com/catalog/c/cboard10/)のサイズに実装できるよう、充電本数は単3、単4それぞれ2本に変更。
* 強制充電用にJ2を用意。
  - 通常充電モード: 通常、J2は接続して使います。充電終止電圧の監視が行われます。
  - 強制充電モード: J2を開放すると、Vrefは3.3Vに設定され、充電終止電圧の監視が行われません。  
    新品のNiMH電池の内部抵抗は0.1Ω程度のようですが、寿命が近づいてくると徐々に内部抵抗が大きくなります。例えば内部抵抗が10Ωにもなると、70mAの充電電流を流すと端子電圧が0.7Vも上昇するため、正常に充電電圧の監視を行うことはできません。こうなってしまうと通常の充電器では充電することができません。そのような場合でも強制充電モードを使えば充電が可能です。ただし、充電電圧の監視がなくなってしまいますので、10～15時間程度で電池を外すようにしてください。
* 充電電流(I<sub>Chg</sub>)は以下のように設定。
  - 単4: 約70mA (= 1.05V / 15Ω)
  - 単3: 約190mA (= 1.05V / 5.6Ω)
* LEDを低消費版に変更し、LM339で直接駆動するように変更。
  - 2SC1815を削除。
  - プルアップ抵抗を削除。
  - R7, R11を、680Ωから2.7kΩに変更し、R15, R19を、680Ωから1.6kΩに変更。  
    2SB834のh<sub>FE</sub>を150程度と想定し、LEDの電流(I<sub>LED</sub>)が0.8mA程度となるように設定しています。
    - 単4の場合:  
      I<sub>B</sub> = I<sub>Chg</sub> / h<sub>FE</sub> = 70 / 150 = 0.47mA  
      R7, R11に流れる電流は I<sub>B</sub> + I<sub>LED</sub> = 0.47 + 0.8 = 1.27mA  
      抵抗値は (5.0V - 1.7V) / 1.27mA = 2.60kΩ
    - 単3の場合:  
      I<sub>B</sub> = I<sub>Chg</sub> / h<sub>FE</sub> = 190 / 150 = 1.27mA  
      R15, R19に流れる電流は I<sub>B</sub> + I<sub>LED</sub> = 1.27 + 0.8 = 2.07mA  
      抵抗値は (5.0V - 1.7V) / 1.27mA = 1.59kΩ
* 2SB948が入手できなかったので、簡単に入手できた2SB834に変更。
* RV1の回転方向を逆にし、右に回すほどVrefが高くなるように変更。


## 調整

J2を接続し、RV1を右に回しきって、700mAhの単4なら15時間程度、2000mAhの単3なら16時間程度充電します。その後電池を外して20分程度置いてから、再度電池を入れます。RV1を回して、LEDが点滅する位置に合わせて終了です。Vrefの電圧は1.40~1.45V程度になっているのが想定です。

## 完成品

準備中
<!--
[![完成品](https://raw.githubusercontent.com/k-takata/PCB_USB_NiMH_Charger/master/images/usb-slow-charger-thumb.jpg)](https://raw.githubusercontent.com/k-takata/PCB_USB_NiMH_Charger/master/images/usb-slow-charger.jpg)
-->

## License

CC0
