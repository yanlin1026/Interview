
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [1.基本数据类型](#1基本数据类型)
	- [自动类型转换](#自动类型转换)
- [2.面试题](#2面试题)
	- [1、](#1)
	- [2、char类型变量能不能储存一个中文的汉子，为什么？](#2char类型变量能不能储存一个中文的汉子为什么)
	- [3、Integer和int的区别](#3integer和int的区别)
	- [4、switch语句能否作用在byte上，能否作用在long上，能否作用在string上？](#4switch语句能否作用在byte上能否作用在long上能否作用在string上)

<!-- /TOC -->
# 1.基本数据类型
| 数据类型 | 内存 | 包装类 |范围|
| :------:| :------: | :------: | :------: |
| boolean | 8位 | Boolean |true/false
| byte | 8位 | Byte |[-128,127]
| short | 16位 | Short |[-32768,32767]
| char | 16位 | Charactor |[\u0000,\uffff]
| int | 32位 | Integer |[-2^31, 2^31-1]
| float | 32位 | Float |[-2^63, 2^63-1]
| long | 64位 | Long |
| double | 64位 | Double |
## 自动类型转换
```java
byte,short,char—> int —> long—> float —> double
```
数据类型转换必须满足如下规则：  
1. 不能对boolean类型进行类型转换。  
2. 不能把对象类型转换成不相关类的对象。  
3. 在把容量大的类型转换为容量小的类型时必须使用强制类型转换。  
4. 转换过程中可能导致溢出或损失精度，例如：
    ```java
    int i =128;
    byte b = (byte)i;
    ```
    因为 byte 类型是 8 位，最大值为127，所以当 int 强制转换为 byte 类型时，值 128 时候就会导致溢出。  

5. 浮点数到整数的转换是通过舍弃小数得到，而不是四舍五入，例如：
    ```java
    (int) 23.7 = 23;
    (int) -13.89f = -13;
    ```  
# 2.面试题
## 1、short
```java
short s1 = 1; s1 = s1 + 1; //1
short s1 = 1; s1 +=1;      //2
```
在s1+1运算时会自动提升表达式的类型为int，那么将int赋予给short类型的变量s1会出现类型转换错误。  
+=是java语言规定的运算符，java编译器会对它进行特殊处理，因此可以正确编译。
## 2、char类型变量能不能储存一个中文的汉子，为什么？
char类型变量是用来储存Unicode编码的字符的，unicode字符集包含了汉字，所以char类型当然可以存储汉字的，还有一种特殊情况就是某个生僻字没有包含在unicode编码字符集中，那么就char类型就不能存储该生僻字。
## 3、Integer和int的区别
int是java的8种内置的原始数据类型。Java为每个原始类型都提供了一个包装类，Integer就是int的封装类int变量的默认值为0，Integer变量的默认值为null，这一点说明Integer可以区分出未赋值和值为0的区别。
	- 类变量在[类加载准备](/Java基础\Java基本数据类型.md#准备)阶段被赋值为0，在
```java
public class test{
	static int i;//准备阶段赋值为0
	static int i1 = 123;//准备阶段赋值为0，在初始化阶段会被赋值为123,  
	final static i3 = 123;//final修饰的字段为ConstantValue，会在准备阶段直接赋值为123
	static Integer i2;
	int j;
	Integer j1;
	public static void main(String args[]){
		int k;
		Integer k1;
		System.out.println(i);// 0
		System.out.println(i2);// null
		System.out.println(new test().i);// 0
		System.out.println(new test().i2);// null
		System.out.println(new test().j);// 0
		System.out.println(new test().j1);// null
		System.out.println(j);//Compilation Failed
		System.out.println(j1);//Compilation Failed
		System.out.println(k);//Compilation Failed
		System.out.println(k1);//Compilation Failed
    }
}
```

## 4、switch语句能否作用在byte上，能否作用在long上，能否作用在string上？

byte的存储范围小于int，可以向int类型进行隐式转换，所以switch可以作用在byte上long的存储范围大于int，不能向int进行隐式转换，只能强制转换，所以switch不可以作用在long上String在1.7版本之前不可以，1.7版本之后switch就可以作用在String上了。
