## java 零散知识点







& oxff 的作用

```bash
类型            存储要求               范围（包含）                默认值              包装类
int           4字节（32位）          -2^31~ 2^31-1                  0               Integer
short         2字节（16位）          -215~215-1                     0               Short
long          8字节（64位）          -2^63~2^63-1                   0               Long
byte          1字节（8位）           -2^7~2^7-1                     0               Byte
float         4字节（32位）          -3.4e+38 ~ 3.4e+38            0.0f             Float
double        8字节（64位）          -1.7e+308 ~ 1.7e+308           0               Double
char          2字节（16位）          u0000~uFFFF（‘’~‘？’）         ‘0’              Character 
boolean       1/8字节(1位）          true, false                  FALSE             Boolean
```



```bash
float类型： 内存中共占4个字节，32bit位，其中bit位从高到低，依次是1位符号位、8位指数位、23位尾数位；
double类型：内存中共占8字节，64bit位，其中bit位从高到低，依次是1位符号位、11位指数位、52位尾数位；
```



小端模式:

* 数据的低位保存在内存的低地址中 
* 数据的高位保存在内存的高地址中

大端模式:

* 数据的低位保存在内存的高地址中 
* 数据的高位保存在内存的低地址中



jvm用的是大端



**为什么要有大小端模式?**

因为计算机系统中。是以字节为单位的。一个字节8比特。但是对于位数大于8位的操作系统，比如 16位或者32位的处理器。因为寄存器宽度大于一个字节，那么就需要决定如何存储像 int(4字节) 这样的数据。





![大端、小端模式](/Users/chenqipeng/Downloads/大端、小端模式.png)



> 题目: java IP 地址转化为 32 位的整型数(int) 64位的(long)

以192.168.1.2为例，把它转为10进制数的公式是：

192 * 256^3 + 168 * 256^2 +1 * 256 + 2 * 1 = 3232235778

相当于将IP地址看成256进制的数。

由于乘法计算不如位运算快，因此对这个方法做如下改进：

a = 192 << 24
b = 168 << 16
c = 1 << 8
d = 2 << 0
最后计算 a | b | c | d的值，结果也是3232235778

java代码如下：

```java
/**
 * description:ip to decimal num
 * @author lijialin
 * @since 2017/08/28
 */
public class Main {

    public static long ipToLong(String strIp) {
        String[]ip = strIp.split("\\.");
        return (Long.parseLong(ip[0]) << 24) + (Long.parseLong(ip[1]) << 16) + (Long.parseLong(ip[2]) << 8) + Long.parseLong(ip[3]);
    }

}
```



> 题目: 在庞大的地址库中逐一比对 IP 地址所在的区间，是非常耗时的。假设我们有 12 万条这样的 IP 区间与归属地的对应关系，如何快速定位出一个 IP 地址的归属地呢？

​	当我们要查询某个 IP 归属地时，我们可以先通过二分查找，找到最后一个起始 IP 小于等于这个 IP 的 IP 区间，然后，检查这个 IP 是否在这个 IP 区间内，如果在，我们就取出对应的归属地显示；如果不在，就返回未查找到。

