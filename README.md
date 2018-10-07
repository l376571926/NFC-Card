# NFC-Card
android通过NFC读取公交卡的余额和交易记录

引用自：https://blog.csdn.net/menghnhhuan/article/details/16947361


通过NFC的读模式，读取公交卡的余额和交易记录。这是一个开源的工程，可以读取深圳通、羊城通、北京卡、八达通、武汉通等等。

读取分四个步骤：
1.select PSF (1PAY.SYS.DDF01)
选择支付系统文件，它的名字是1PAY.SYS.DDF01。
byte[] DFN_PSE = { (byte) '1', (byte) 'P',(byte) 'A', (byte) 'Y', (byte) '.', (byte) 'S', (byte) 'Y',(byte) 'S', (byte) '.', (byte) 'D', (byte) 'D', (byte) 'F',(byte) '0', (byte) '1', };
2.选择公交卡应用的名字或者ID
每个公交卡的名字都会不一样。
深圳通：
byte[] DFN_SRV = { (byte) 'P', (byte) 'A', (byte) 'Y',(byte) '.', (byte) 'S', (byte) 'Z', (byte) 'T' };
武汉通：
byte[] DFN_SRV = { (byte) 0x41, (byte) 0x50,(byte) 0x31, (byte) 0x2E, (byte) 0x57, (byte) 0x48, (byte) 0x43,(byte) 0x54, (byte) 0x43, };
羊城通：
byte[] DFN_SRV = { (byte) 'P', (byte) 'A', (byte) 'Y',(byte) '.', (byte) 'A', (byte) 'P', (byte) 'P', (byte) 'Y', };
长安通：
byte[] DFN_SRV = { (byte) 0xA0, (byte) 0x00,(byte) 0x00, (byte) 0x00, (byte) 0x03, (byte) 0x86, (byte) 0x98,(byte) 0x07, (byte) 0x01, };
北京市政交通卡ID：
byte[] DFI_EP = { (byte) 0x10, (byte) 0x01 };
其他公交卡：
3.读取余额
发送命令读取电子钱包的余额：
final byte[] cmd = { (byte) 0x80, // CLA Class(byte) 0x5C, // INS Instruction(byte) 0x00, // P1 Parameter 1(byte) 0x02, // P2 Parameter 2(byte) 0x04, // Le};
获取到的余额数据是byte[] data, 前4字节合并成int，再除以100（两个小数点），得到的结果就是余额。
4.读取交易记录
一次性读取命令，在不知道有多少条记录的时候，用这个命令：
byte[] cmd = { (byte) 0x00, // CLA Class(byte) 0xB2, // INS Instruction(byte) 0x01, // P1 Parameter 1(byte) 0xC5, // P2 Parameter 2(byte) 0x00, // Le};
返回所有的记录byte[] data,每23个字节代表一条记录
也可以一条一条的读取：
cmd = { (byte) 0x00, // CLA Class(byte) 0xB2, // INS Instruction(byte) index, // P1 Parameter 1(byte) 0xC4, // P2 Parameter 2(byte) 0x00, // Le};
一条记录是23个字节byte[] data，对其解码如下
data[0]-data[1]:index
data[2]-data[4]:over,金额溢出?
data[5]-data[8]:交易金额
data[9]:如果等于0x06或者0x09，表示刷卡；否则是充值
data[10]-data[15]:刷卡机或充值机编号
data[16]-data[22]:日期String.format("%02X%02X.%02X.%02X %02X:%02X:%02X",data[16], data[17], data[18], data[19], data[20], data[21], data[22]);
源码下载地址百度云盘下载地址：http://pan.baidu.com/s/1lBUmt

---------------------

本文来自 menghnhhuan 的CSDN 博客 ，全文地址请点击：https://blog.csdn.net/menghnhhuan/article/details/16947361?utm_source=copy 
