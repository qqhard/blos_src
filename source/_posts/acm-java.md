title: acm中的java
date: 2016-06-04 10:35:13
tags:
- acm
- java
categories:
- acm
---

c++中没有大数，所以java的BigInteger就显得十分有用，涉及到大数的题目，一般使用java，由于不长用，所以把java的输入输出总结一下。

<!-- more -->

# java输入
```
import java.util.*;
public class Main
{
    public static void main(String [] args){
        Scanner cin = new Scanner(System.in);//对于Scanner 类声明对象cin用来扫描控制台输入
        int a = cin.nextInt();
        int b = cin.nextInt();
        System.out.println(a+b);//输出a+b
        cin.close();
    }
}
```
读一个整数：  `int n = cin.nextInt();`
相当于 `scanf("%d", &n);  或 cin >> n;`
读一个字符串：`String s = cin.next();`
相当于 `scanf("%s", s);   或 cin >> s;`
读一个浮点数：`double t = cin.nextDouble();`
相当于`scanf("%lf", &t); 或 cin >> t;`
读一整行： `String s = cin.nextLine();`
相当于 `gets(s); 或 cin.getline(...);`
读一个大数： `BigInteger c = cin.nextBigInteger();`
不过需要注意的是它们没有像scanf一样有判断是否输入到结尾的功能。

# java-输入输出buffer
```
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.PrintWriter;
Scanner cin = new Scanner(new BufferedInputStream(System.in));
PrintWriter out = new PrintWriter(new BufferedOutputStream(System.out));
cin.close();
out.close();
```

# java大数类
```
import java.math.BigInteger;
public class Main { 
    public static void main(String[] args){
        int a=6,b=3;
        BigInteger x,y,z;
        x=BigInteger.valueOf(a); //转化赋值
        y=BigInteger.valueOf(b);
        System.out.println(x.add(y)); //加
        System.out.println(x.subtract(y)); //减
        System.out.println(x.multiply(y)); //乘
        System.out.println(x.divide(y)); //除
        System.out.println(x.mod(y)); //取余
    }
}
```

# 大数gcd
```
import java.math.BigInteger;
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner cin = new Scanner(System.in);
        int T=cin.nextInt();
        for(int Case=1;Case<=T;Case++){
            BigInteger a=cin.nextBigInteger(2);
            //读入2进制大数
            BigInteger b=cin.nextBigInteger(2);
            BigInteger c=a.gcd(b);
            System.out.println("Case #"+Case+": "+c.toString(2)); //大数二进制输出
        }
    }
}
```

# 字符串

```
import java.io.*;
import java.util.*;
public class Main
{
    public static void main(String[] args) 
    {
        Scanner cin = new Scanner (new BufferedInputStream(System.in));
        String st = "abcdefg";
        System.out.println(st.charAt(0)); // st.charAt(i)就相当于st[i].
        char [] ch;
        ch = st.toCharArray(); // 字符串转换为字符数组.
        for (int i = 0; i < ch.length; i++){
            ch[i] += 1;//字符数组可以像C++ 一样操作
        }
        System.out.println(ch); // 输入为“bcdefgh”.
        st = st.substring(1); // 则从第1位开始copy(开头为第0位).
        System.out.println(st);
        st=st.substring(2, 4); //从第2位copy到第4位（不包括第4位）
        System.out.println(st);//输出“de”
    }
}
```

# 进制转换
```
public class Main {
    public static void main(String[] args)
    {
        String string;
        int a=8;
        int x=2;
        string = Integer.toString(a, x); //把int型数据转换乘X进制数并转换成string型
        System.out.println(string);
        int b = Integer.parseInt(string, x);//把字符串当作X进制数转换成int型
        System.out.println(b);
    }
}
```

# java浮点型输出
```
public class Main {
    public static void main(String[] args){
        double d;
        d=9999.99999;
        System.out.format("%f",d); //9999.999990 没有回车
        System.out.format("%10.10f",d).println(); //9999.9999900000 输出回车
        System.out.format("%.4f",d).println(); //10000.0000 输出回车
        System.out.format("%3.4f",d).println(); //10000.0000 输出回车
        System.out.println(d); //输出当前变量 输出回车
        System.out.println(); // 输出回车
        System.out.printf("%f",d); //9999.999990 没有回车
        System.out.print(d); //9999.99999 没有回车
    }
}
```
当然，输出的时候也有对小数位数处理的方法：
0代表当前位向后都是0就输出0占位，#代表若当前位向后都是0就不输出这些位上的数字。
```
import java.text.DecimalFormat;
public class Main {
    public static void main(String[] args) {
        double num = 9.999;
        DecimalFormat p3 = new DecimalFormat("#.00#");
        DecimalFormat p4 = new DecimalFormat("#.000#");
        DecimalFormat p5 = new DecimalFormat("#.0000#");
        System.out.println(p3.format(num));//输出9.999
        System.out.println(p4.format(num));//输出9.999
        System.out.println(p5.format(num));//输出9.9990
    }
}
```

# 控制台输入输出重定向到文件
```
FileInputStream fis=new FileInputStream("b.in");  
System.setIn(fis);  
PrintStream ps=new PrintStream(new FileOutputStream("bb.out"));  
System.setOut(ps);  
```
