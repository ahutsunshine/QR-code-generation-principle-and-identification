关键字：二维码 二维码生成 生成原理 识别 识别过程
# 二维码生成原理和识别的学习
##基本介绍
我们之所以对二维码进行扫描能读出那么多信息，就是因为这些信息被编入了二维码之中。制作二维码输入的信息可以分成三类，文本信息，比如名片信息；字符信息，比如网址、电话号码；还有图片信息，甚至还可以包括简短的视频。数据信息是怎么被编入的呢？信息输入后，首先要选择一种信息编码的码制。现在常见的二维码都是以QR码作为编码的码制。QR码是矩阵式二维码，它是在一个矩形空间内，通过黑、白像素在矩阵中的不同分布，来进行编码的。我们知道电脑使用二进制（0和1）数来贮存和处理数据，而在二维码中，用黑白矩形表示二进制数据我们肉眼能看到的黑色表示的是二进制“1”，白色表示二进制的“0”，黑白的排列组合确定了矩阵式二维条码的内容，以便于计算机对二维码符号进行编码和分析。
##基础知识
二维码一共有40个尺寸。官方叫版本Version。Version 1是21 x 21的矩阵，Version 2是 25 x 25的矩阵，Version 3是29的尺寸，每增加一个version，就会增加4的尺寸，公式是：**(V-1)*4 + 21**（V是版本号） 最高Version 40，(40-1)*4+21 = 177，所以最高是177 x 177 的正方形。

下面我们看看一个二维码的样例：
![](http://coolshell.cn//wp-content/uploads/2013/10/QR-Code-Overview.jpeg)

![](https://yqall02.baidupcs.com/file/e48dc0027ad5c15b2e28a8194682245e?bkt=p3-1400e48dc0027ad5c15b2e28a8194682245e987a1f2700000001423c&fid=3407542590-250528-246052364793632&time=1482070076&sign=FDTAXGERLBH-DCb740ccc5511e5e8fedcff06b081203-WZ5IWeg02RZIX4kLKptzZ5sKSpw%3D&to=yqhb&fm=Yan,B,T,t&sta_dx=82492&sta_cs=&sta_ft=png&sta_ct=0&sta_mt=0&fm2=Yangquan,B,T,t&newver=1&newfm=1&secfm=1&flow_ver=3&pkey=1400e48dc0027ad5c15b2e28a8194682245e987a1f2700000001423c&sl=76480590&expires=8h&rt=pr&r=184796598&mlogid=8185261892117311830&vuk=3407542590&vbdid=1988581034&fin=%E4%BA%8C%E7%BB%B4%E7%A0%81%E7%BB%93%E6%9E%84_cn.png&fn=%E4%BA%8C%E7%BB%B4%E7%A0%81%E7%BB%93%E6%9E%84_cn.png&slt=pm&uta=0&rtype=1&iv=0&isw=0&dp-logid=8185261892117311830&dp-callid=0.1.1&hps=1&csl=80&csign=u0NUuWdrlFBWrNRnMBKYOawXXB0%3D)
### 定位图案
* Position Detection Pattern是定位图案，用于标记二维码的矩形大小。这三个定位图案有白边叫Separators for Postion Detection Patterns。之所以三个而不是四个意思就是三个就可以标识一个矩形了。
* Timing Patterns也是用于定位的。原因是二维码有40种尺寸，尺寸过大了后需要有根标准线，不然扫描的时候可能会扫歪了。
* Alignment Patterns 只有Version 2以上（包括Version2）的二维码需要这个，同样是为了定位用的。

### 功能性数据
* Format Information 存在于所有的尺寸中，用于存放一些格式化数据的。
* Version Information 在 >= Version 7以上，需要预留两块3 x 6的区域存放一些版本信息。

### 数据码和纠错码
* 除了上述的那些地方，剩下的地方存放 **Data Code 数据码** 和 **Error Correction Code 纠错码**。


## 数据编码
*  **Numeric mode** 数字编码，从0到9。
*  **Alphanumeric mode**字符编码。包括 0-9，大写的A到Z（没有小写），以及符号$ % * + – . / : 包括空格。这些字符会映射成一个字符索引表。
*  **Byte mode**, 字节编码，可以是0-255的ISO-8859-1字符。
*  **Kanji mode** 这是日文编码，也是双字节编码
*  **Extended Channel Interpretation (ECI) mode** 主要用于特殊的字符集。并不是所有的扫描器都支持这种编码。
*  **Structured Append mode** 用于混合编码，也就是说，这个二维码中包含了多种编码格式。
*  **FNC1 mode** 这种编码方式主要是给一些特殊的工业或行业用的。比如GS1条形码之类的。

## 纠错码

上面我们说到了一些**纠错级别（Error Correction Code Level）**，二维码中有四种级别的纠错，这就是为什么二维码有残缺还能扫出来。

**错误修正容量**

L水平 | 7%的字码可被修正 
---- | ------------- 
M水平  | 15%的字码可被修正 
Q水平 | 25%的字码可被修正
H水平 | 30%的字码可被修正

那么，QR是怎么对数据码加上纠错码的？首先，我们需要对数据码进行分组，也就是分成不同的**块（Block）**，然后对各个Block进行纠错编码，对于如何分组，我们可以查看QR Code Spec的第33页到44页的Table-13到Table-22的定义表。注意最后两列：

*  **Number of Error Code Correction Blocks** ：需要分多少个块。
*  **Error Correction Code Per Blocks**：每一个块中的纠错码个数，所谓的纠错码的个数，也就是有多少个8bits的字节。

![](http://coolshell.cn//wp-content/uploads/2013/10/Error-Correction-Blocks.png)

举个例子：上述的Version 5 + Q纠错级：需要4个Blocks（2个Blocks为一组，共两组），头一组的两个Blocks中各15个bits数据 + 2个9bits的纠错码（注：表中的**codewords（码字）**就是一个8bits的byte，最后一例中的（c, k, r ）的公式为：c = k + 2 * r，因为脚注解释了：纠错码的容量小于纠错码的一半）

下图给一个5-Q的示例（因为二进制写起来会让表格太大，所以，我都用了十进制）
![](https://nj01ct01.baidupcs.com/file/be3a56c1727943ff41d7b8eb580d4587?bkt=p3-1400be3a56c1727943ff41d7b8eb580d458720808921000000010961&fid=3407542590-250528-549586314494535&time=1482067883&sign=FDTAXGERLBH-DCb740ccc5511e5e8fedcff06b081203-LpulKGrS%2BRUUzkxVqyO9qXMDf54%3D&to=njhb&fm=Yan,B,T,t&sta_dx=67937&sta_cs=&sta_ft=png&sta_ct=0&sta_mt=0&fm2=Yangquan,B,T,t&newver=1&newfm=1&secfm=1&flow_ver=3&pkey=1400be3a56c1727943ff41d7b8eb580d458720808921000000010961&sl=76480590&expires=8h&rt=pr&r=895474424&mlogid=8184673016971024945&vuk=3407542590&vbdid=1988581034&fin=code%E7%BC%96%E7%A0%81.png&fn=code%E7%BC%96%E7%A0%81.png&slt=pm&uta=0&rtype=1&iv=0&isw=0&dp-logid=8184673016971024945&dp-callid=0.1.1&hps=1&csl=80&csign=u0NUuWdrlFBWrNRnMBKYOawXXB0%3D)


注：二维码的纠错码主要是通过[Reed-Solomon error correction（里德-所罗门纠错算法](https://en.wikipedia.org/wiki/Reed%E2%80%93Solomon_error_correction)来实现的。这个算法如果想要深度学习的话，请查看相关文档进行学习。

## 最终编码
### 穿插放置
如果你以为我们可以开始画图，你就错了。到此还没玩完，它还要把数据码和纠错码的各个码字（codewords）交替放在一起。如何交替呢，规则如下：

对于数据码：把每个块的第一个codewords先拿出来按顺度排列好，然后再取第一块的第二个，如此类推。如：上述示例中的数据码字(Data Codewords)如下：
![](https://nj01ct01.baidupcs.com/file/6781cb8aaa91f42aadb0ebe8e748bc8a?bkt=p3-14006781cb8aaa91f42aadb0ebe8e748bc8a1f95069d000000006b34&fid=3407542590-250528-261339269042895&time=1482067964&sign=FDTAXGERLBH-DCb740ccc5511e5e8fedcff06b081203-gBaZzpewGIq0MzmTsGv%2BPJjuFAQ%3D&to=njhb&fm=Yan,B,T,t&sta_dx=27444&sta_cs=&sta_ft=png&sta_ct=0&sta_mt=0&fm2=Yangquan,B,T,t&newver=1&newfm=1&secfm=1&flow_ver=3&pkey=14006781cb8aaa91f42aadb0ebe8e748bc8a1f95069d000000006b34&sl=76480590&expires=8h&rt=pr&r=739332986&mlogid=8184694841370277370&vuk=3407542590&vbdid=1988581034&fin=data.png&fn=data.png&slt=pm&uta=0&rtype=1&iv=0&isw=0&dp-logid=8184694841370277370&dp-callid=0.1.1&hps=1&csl=80&csign=u0NUuWdrlFBWrNRnMBKYOawXXB0%3D)

我们先取第一列的：67， 246， 182， 70

然后再取第二列的：67， 246， 182， 70， 85，246，230 ，247

如此类推：67， 246， 182， 70， 85，246，230 ，247 …… …… ，38，6，50，17，7，236

对于纠错码，也是一样：
![](https://yqall02.baidupcs.com/file/ee1a337c8286e54173ce69c4a8ee74fb?bkt=p3-1400ee1a337c8286e54173ce69c4a8ee74fb871642e500000000754c&fid=3407542590-250528-1074428435650881&time=1482067958&sign=FDTAXGERLBH-DCb740ccc5511e5e8fedcff06b081203-WnMVIt%2FCFjdabnjC4wh1rUdwBMw%3D&to=yqhb&fm=Yan,B,T,t&sta_dx=30028&sta_cs=&sta_ft=png&sta_ct=0&sta_mt=0&fm2=Yangquan,B,T,t&newver=1&newfm=1&secfm=1&flow_ver=3&pkey=1400ee1a337c8286e54173ce69c4a8ee74fb871642e500000000754c&sl=76480590&expires=8h&rt=pr&r=221884997&mlogid=8184693264825895442&vuk=3407542590&vbdid=1988581034&fin=error_correction.png&fn=error_correction.png&slt=pm&uta=0&rtype=1&iv=0&isw=0&dp-logid=8184693264825895442&dp-callid=0.1.1&hps=1&csl=80&csign=u0NUuWdrlFBWrNRnMBKYOawXXB0%3D)

和数据码取的一样，得到：213，87，148，235，199，204，116，159，…… …… 39，133，141，236

然后，再把这两组放在一起（纠错码放在数据码之后）得到：

67, 246, 182, 70, 85, 246, 230, 247, 70, 66, 247, 118, 134, 7, 119, 86, 87, 118, 50, 194, 38, 134, 7, 6, 85, 242, 118, 151, 194, 7, 134, 50, 119, 38, 87, 16, 50, 86, 38, 236, 6, 22, 82, 17, 18, 198, 6, 236, 6, 199, 134, 17, 103, 146, 151, 236, 38, 6, 50, 17, 7, 236, 213, 87, 148, 235, 199, 204, 116, 159, 11, 96, 177, 5, 45, 60, 212, 173, 115, 202, 76, 24, 247, 182, 133, 147, 241, 124, 75, 59, 223, 157, 242, 33, 229, 200, 238, 106, 248, 134, 76, 40, 154, 27, 195, 255, 117, 129, 230, 172, 154, 209, 189, 82, 111, 17, 10, 2, 86, 163, 108, 131, 161, 163, 240, 32, 111, 120, 192, 178, 39, 133, 141, 236

这就是我们的数据区。

### Remainder Bits（剩余位）
最后再加上Reminder Bits，对于某些Version的QR，上面的还不够长度，还要加上Remainder Bits，比如：上述的5Q版的二维码，还要加上7个bits，Remainder Bits加零就好了。关于哪些Version需要多少个Remainder bit，可以参看QR Code Spec的第15页的Table-1的定义表。

## 画二维码图

### Position Detextion Pattern
首先，先把Position Detection图案画在三个角上。（无论Version如何，这个图案的尺寸就是这么大）

![](http://coolshell.cn//wp-content/uploads/2013/10/finder.png)

### Alignment Pattern
然后，再把Alignment图案画上（无论Version如何，这个图案的尺寸就是这么大）

![](http://coolshell.cn//wp-content/uploads/2013/10/alignment-pattern.png)

关于Alignment的位置，可以查看QR Code Spec的第81页的Table-E.1的定义表（下表是不完全表格）

![](http://coolshell.cn//wp-content/uploads/2013/10/Alignment-Position.png)

下图是根据上述表格中的Version8的一个例子（6，24，42）

![](http://coolshell.cn//wp-content/uploads/2013/10/alignment-example.png)

### Timing Pattern
接下来是Timing Pattern的线（这个不用多说了）

![](http://coolshell.cn//wp-content/uploads/2013/10/Timing-Pattern.png)

### Format Information
再接下来是Formation Information，下图中的蓝色部分。

![](http://coolshell.cn//wp-content/uploads/2013/10/Format-Information.png)

Format Information是一个15个bits的信息，每一个bit的位置如下图所示：（注意图中的Dark Module，那是永远出现的）

![](http://coolshell.cn//wp-content/uploads/2013/10/Format-Info-bits-postion.png)

这15个bit钟包括：

* 5个数据位：其中，2个bits用于表示使用什么样的**Error Correction Level**（纠错级别）， 3个bits表示使用什么样的**Mask**（掩码图案）
* 10个纠错位。主要通过BCH Code来计算

然后15个bits还要与101010000010010做XOR操作。这样就保证不会因为我们选用了00的纠错级别和000的Mask，从而造成全部为白色，这会增加我们的扫描器的图像识别的困难。

下面是一个示例：
![](http://coolshell.cn//wp-content/uploads/2013/10/Format-Information-Example.png)

关于Error Correction Level如下表所示：
![](http://coolshell.cn//wp-content/uploads/2013/10/Error-Correction-Indicator-Code.png)

### Version Information
再接下来是Version Information（版本7以后需要这个编码），下图中的蓝色部分。
![](http://coolshell.cn//wp-content/uploads/2013/10/Version-Information.png)

Version Information一共是18个bits，其中包括6个bits的版本号以及12个bits的纠错码，下面是一个示例：
![](http://coolshell.cn//wp-content/uploads/2013/10/Version-Information-Example.png)

而其填充位置如下：
![](http://coolshell.cn//wp-content/uploads/2013/10/Version-Information-Position.png)

### 数据和数据纠错码

然后是填接我们的最终编码，最终编码的填充方式如下：从左下角开始沿着红线填我们的各个bits，1是黑色，0是白色。如果遇到了上面的非数据区，则绕开或跳过。

![](http://coolshell.cn//wp-content/uploads/2013/10/Data-Placement.png)

### 掩码图案
这样下来，我们的图就填好了，但是，也许那些点并不均衡，如果出现大面积的空白或黑块，会告诉我们扫描识别的困难。所以，我们还要做Masking操作,QR有8个Mask你可以使用，如下所示：其中，各个mask的公式在各个图下面。所谓mask，说白了，就是和上面生成的图做XOR(异或)操作。Mask只会和数据区进行XOR，不会影响功能区。**（注：选择一个合适的Mask也是有算法的）**
![](http://coolshell.cn//wp-content/uploads/2013/10/masking-pattern.png)

其Mask的标识码如下所示：（其中的i,j分别对应于上图的x,y）
![](http://coolshell.cn//wp-content/uploads/2013/10/Mask-Pattern-Code.png)

下面是Mask后的一些样子，我们可以看到被某些Mask XOR了的数据变得比较零散了。
![](http://coolshell.cn//wp-content/uploads/2013/10/Masking-Examples.png)

Mask过后的二维码就成最终的图了。
