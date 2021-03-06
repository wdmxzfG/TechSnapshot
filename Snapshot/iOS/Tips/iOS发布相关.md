## 一、   开发者账号申请

### 1.1个人账号

与公司账号唯一的区别就是个人账号不具备团队管理功能。

### 1.2 公司账号

#### 1.2.1 说明

售价：$99

用于APP上架AppStore。

任意用户可在app过审后，在AppStore上下载。

此证书在内部测试的时候有安装次数数量限制，限制为做多安装100台设备。

需要申请邓白氏码。

#### 1.2.1 所需材料

1. visa或者万事达国际信用卡。
2. 公司法人姓名公司法人的职称。
3. 公司的DUNS码（邓白氏编码）。
4. 公司的英文名。
5. 公司网址。
6. AppleID。

 

#### 1.2.2 邓白氏码相关

[邓氏编码](https://baike.baidu.com/item/%E9%82%93%E6%B0%8F%E7%BC%96%E7%A0%81)（D-U-N-S® Number，是Data Universal Numbering System的缩写）。它是一个独一无二的9位数字全球编码系统，相当于企业的身份识别码 （就像是个人的身份证），被广泛应用于企业识别、商业信息的组织及整理。可以帮助识别和迅速定位全球2.4亿家企业的信息。

需与邓白氏公司进行两次握手操作。

第一次，   需填写公司简单信息，（不需要营业执照）以及联系人信息。

第二次，   需填写公司具体信息，以下为例：

| 提供公司清晰的营业执照复印件或扫描件                         |
| ------------------------------------------------------------ |
| 公司中文名称 公司英文名称                                    |
| 中文办公地址 英文办公地址                                    |
| 邮编                                                         |
| 企业类型                                                     |
| 是否是独立企业                                               |
| 主营业务                                                     |
| 员工人数                                                     |
| 苹果联系人（务必填写申请公司的苹果项目联系人） 姓名 职务 电话（公司座机和联系人手机，所提供座机和办公地址保持一致） |

### 1.3 企业账号

售价：$299

用于公司内部测试使用。

可以在公司网站上进行下载。

证书没有设备安装数量限制。



## 二、 iOS APP签名原理

### 2.1 苹果想要解决的问题

在 iOS 出来之前，在主流操作系统(Mac/Windows/Linux)上开发和运行软件是不需要签名的，软件随便从哪里下载都能运行，导致平台对第三方软件难以控制，盗版流行。苹果希望解决这样的问题，在 iOS 平台对第三方 APP 有绝对的控制权，一定要保证每一个安装到 iOS 上的 APP 都是经过苹果官方允许的，怎样保证呢？就是通过签名机制。

### 2.2 一些加密算法

#### 2.2.1 RSA 非对称加密

签名 ó 数字签名，而数字签名是利用RSA实现的。

非对称加密，是利用公钥加密私钥解密，或者私钥加密公钥解密的一种加密算法。其原理为：

>
>
>1. 选两个质数 `p` 和 `q`，相乘得出一个大整数`n`，例如 p=61，q=53，n=pq=3233
>2. 选 1-n 间的随便一个质数 `e`，例如 e =      17
>3. 经过一系列数学公式，算出一个数字 `d`，满足：
>         a. 通过 `n` 和 `e` 这两个数据一组数据进行数学运算后，可以通过 n 和 d 去反解运算，反过来也可以。
>         b. 如果只知道 `n` 和 `e`，要推导出 `d`，需要知道 `p` 和 `q`，也就是要需要把      n 因数分解。
>
>#### 

#### 2.2.2 数字签名

数字签名的作用是我对某一份数据打个标记，表示我认可了这份数据（签了个名），然后我发送给其他人，其他人可以知道这份数据是经过我认证的，数据没有被篡改过。

数字签名原理：

>1. 首先用一种算法，算出原始数据的摘要。需满足 a.若原始数据有任何变化，计算出来的摘要值都会变化。 b.摘要要够短。这里最常用的算法是MD5。
>2. 生成一份非对称加密的公钥和私钥，私钥我自己拿着，公钥公布出去。
>3. 对一份数据，算出摘要后，用私钥加密这个摘要，得到一份加密后的数据，称为原始数据的签名。把它跟原始数据一起发送给用户。
>4. 用户收到数据和签名后，用公钥解密得到摘要。同时用户用同样的算法计算原始数据的摘要，对比这里计算出来的摘要和用公钥解密签名得到的摘要是否相等，若相等则表示这份数据中途没有被篡改过，因为如果篡改过，摘要会变化。

### 2.3 苹果的APP签名机制

#### 2.3.1 签名机制

以在开发阶段在个人iPhone上安装开发包为例：

1.本地mac生成公钥和私钥。

2.把公钥上传到苹果后台，苹果用他们的私钥对我们的公钥进行数字签名从而生成数字证书供我们下载。

3.在苹果开发者网站上申请AppId，并配置好设备ID列表和APP使用了那些操作系统权限，再加上第2步申请好的证书，提交给苹果后台进行数字签名，苹果后台签名完成之后，生成了一个**Provisioning Profile**供我们下载。

4.开发完成后，利用本地mac上的私钥对编译后的APP进行数字签名，同时将第3步得到的Provisioning Profile文件一起打包进APP当中，文件名为**embedded.mobileprovision**，把APP安装到手机上。

5.在安装APP的时候，iOS操作系统会通过内置公钥对embedded.mobileprovision进行验签（验签的意思见注解）。然后，继续用系统公钥对embedded.mobileprovision中的数字证书进行验签。经过这两步验签，并且没有问题后，可从数字证书当中拿到一个合法的没有被篡改过的mac公钥，最后用这个mac公钥去对APP二进制包进行验签，这个步骤完成后，就得到了一个合法的，没有被篡改的二进制包，iOS系统认可其合法性，便可在系统上正常运行。 

注解：**验签操作**是通过iOS操作系统的内置公钥，对数据（如embedded.mobileprovision，数字证书，APP二进制文件等）的摘要进行解密得到签名A，解密后利用同等的摘要算法对原数据进行摘要得到签名B，然后将签名A和签名B进行比对，观察二者是否一致，一致则认为原数据是经过官方授权的，是合法的，并且没有被篡改过的。

#### 2.3.2 一些概念

1. **证书**：内容是公钥或私钥，由其他机构对其签名组成的数据包。
2. **Entitlements**：包含了 App权限开关列表。
3. **CertificateSigningRequest**：本地公钥。
4. **p12**：本地私钥，可以导入到其他电脑。
5. **Provisioning Profile**：包含了 证书 / Entitlements 等数据，并由苹果后台私钥签名的数据包。

 

 