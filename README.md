これはEZ-USB FX3のファームウェアの最新版で、GPIFではなくSlaveFIFOを利用して毎秒300MB以上の速度でIn/Outできます。
特電FX3 IPコアと組み合わせて使います。

# ビルド方法
CypressのEZ-USB FX3用のSDKをダウンロードして、EZ USB Suieを用いてビルドします。

+ (1) EZ USB Suiteを起動します。Elcipseが起動します。
+ (2) .gitがある一つ上のディレクトリをWorkspaceとして使用します。<br>![image](http://git3.tokudenkairo.co.jp/nahitafu/SlaveFifoNP1064/raw/master/firm1.png)
+ (3) File->Import->General->Existing Projects into Workspaceでプロジェクトを読み込みます。<br> ![image](http://git3.tokudenkairo.co.jp/nahitafu/SlaveFifoNP1064/raw/master/firm2.png)
+ (4) root directoryは.gitのあるディレクトリを指定し、Optionsは両方ともOFFにします。<br>![image](http://git3.tokudenkairo.co.jp/nahitafu/SlaveFifoNP1064/raw/master/firm3.png)
+ (5) Finishを押します。
+ (6) プロジェクトが読み込まれるのでビルドします。<br>![image](http://git3.tokudenkairo.co.jp/nahitafu/SlaveFifoNP1064/raw/master/firm5.png)

プロジェクトのメインのプログラムは cyfxslfifosync.c で、機器の固有の情報を設定するのが cyfxslfifousbdscr.c です。

# EndPointの構成
+ 00 IN/OUT CONTROL
+ 01 OUT 汎用
+ 81 IN 汎用
+ 02 OUT 高速FPGA
+ 82 IN 高速FPGA

# USB IDの変更方法
cyfxslfifousbdscr.c を編集します。
下記の2箇所です。

`
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
`

`
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
`

# シリアル番号の変更方法
cyfxslfifousbdscr.c を編集します。
下記の箇所です。
`
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
`
この例ではシリアル番号はA000456と7文字ですが、Unicodeにするため(?)後ろに0x00を付けます。
ここで、最初の0x10というのはこのディスクリプタが16バイトであることを示しています。
文字が7ワード(14バイト)+最初の長さ(1バイト)+タイプ(1バイト)となります。

# Copyright
(C)2013-2019 特殊電子回路㈱