これは特殊電子回路製のEZ-USB FX3のファームウェアの最新版です。

GPIFではなくSlaveFIFOを利用して毎秒300MB以上の速度でIn/Outできます。このファームウェアからのデータを受け取るFPGAは、特電FX3 IPコアを入れて使います。

このファームウェアを使うと、EZ-USB FX3を使って以下のことができます。

+ FPGAとの間の高速なデータ転送
+ USB-JTAG

Windowsのアプリケーションは[こちらのプロジェクト](https://github.com/tokuden/tkusbfx3_multi64)にあります。

このレポジトリはバイナリ配布用です。ソースコードをご希望の方は本README.mdを最後までお読みください。

# ビルド方法
CypressのEZ-USB FX3用のSDKをダウンロードして、EZ USB Suieを用いてビルドします。

+ (1) EZ USB Suiteを起動します。Elcipseが起動します。
+ (2) .gitがある一つ上のディレクトリをWorkspaceとして使用します。<br>![](https://github.com/tokuden/SlaveFifoNP1064/blob/master/firm1.png?raw=true)
+ (3) File->Import->General->Existing Projects into Workspaceでプロジェクトを読み込みます。<br> ![](https://github.com/tokuden/SlaveFifoNP1064/blob/master/firm2.png)
+ (4) root directoryは.gitのあるディレクトリを指定し、Optionsは両方ともOFFにします。<br>![](https://github.com/tokuden/SlaveFifoNP1064/blob/master/firm3.png)
+ (5) Finishを押します。
+ (6) プロジェクトが読み込まれるのでビルドします。<br>![](https://github.com/tokuden/SlaveFifoNP1064/blob/master/firm5.png)

プロジェクトのメインのプログラムは cyfxslfifosync.c で、機器の固有の情報を設定するのが cyfxslfifousbdscr.c です。

# EndPointの構成
+ 00 IN/OUT CONTROL
+ 01 OUT 汎用
+ 81 IN 汎用
+ 02 OUT 高速FPGA
+ 82 IN 高速FPGA

# USB IDの変更方法
cyfxslfifousbdscr.c を編集します。

修正箇所は下記の2箇所です。

```
const uint8_t CyFxUSB30DeviceDscr[] __attribute__ ((aligned (32))) =
{
    0x12,                           /* Descriptor size */
    CY_U3P_USB_DEVICE_DESCR,        /* Device descriptor type */
    0x00,0x03,                      /* USB 3.0 */
    0x00,                           /* Device class */
    0x00,                           /* Device sub-class */
    0x00,                           /* Device protocol */
    0x09,                           /* Maxpacket size for EP0 : 2^9 */
    0x29,0x21,                      /* Vendor ID */
    0x40,0x06,                      /* Product ID */
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

```
const uint8_t CyFxUSB20DeviceDscr[] __attribute__ ((aligned (32))) =
{
    0x12,                           /* Descriptor size */
    CY_U3P_USB_DEVICE_DESCR,        /* Device descriptor type */
    0x10,0x02,                      /* USB 2.10 */
    0x00,                           /* Device class */
    0x00,                           /* Device sub-class */
    0x00,                           /* Device protocol */
    0x40,                           /* Maxpacket size for EP0 : 64 bytes */
    0x29,0x21,                      /* Vendor ID */
    0x40,0x06,                      /* Product ID */
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

# シリアル番号の変更方法
cyfxslfifousbdscr.c を編集します。

下記の箇所です。

```
/* Standard manufacturer string descriptor */
const uint8_t CyFxUSBSerialNumberDscr[] __attribute__ ((aligned (32))) =
{
    0x10,                           /* Descriptor size */
    CY_U3P_USB_STRING_DESCR,        /* Device descriptor type */
    'A',0x00,
    '0',0x00,
    '0',0x00,
    '0',0x00,
    '4',0x00,
    '5',0x00,
    '6',0x00
};
```
この例ではシリアル番号はA000456と7文字ですが、Unicodeにするため(?)後ろに0x00を付けます。

ここで、最初の0x10というのはこのディスクリプタが16バイトであることを示しています。

文字が7ワード(14バイト)+最初の長さ(1バイト)+タイプ(1バイト)となります。

# ソースコードの入手方法
ソースコードは、[Artix-7ボード](http://www.tokudenkairo.co.jp/art7/)をお買い上げの方に提供しています。

# お問い合わせ先
特殊電子回路 内藤まで info@tokudenkairo.co.jp

# Copyright
(C)2013-2019 特殊電子回路㈱
