## ð¾  Javaå¸¸ç¨ç±»

### åé¨ç±»

> å¨ä¸ä¸ªç±»çåé¨åå®ä¹ä¸ä¸ªå®æ´çç±»

1. æååé¨ç±»
2. éæåé¨ç±»
3. å±é¨åé¨ç±»
4. å¿ååé¨ç±»

**ç¹ç¹**

- ç¼è¯ä¹åå¯ä»¥çæç¬ç«çå­èç æä»¶.class
- å¯ä»¥è®¿é®å¤é¨çç§ææååéï¼èä¸ç ´åå°è£
- å¯ä»¥ä¸ºå¤é¨ç±»æä¾å¿è¦çåè½ç»ä»¶

#### æååé¨ç±»

> å¨ç±»çåé¨å®ä¹ï¼ä¸æååéï¼å®ä¾æ¹æ³åçº§å«çç±»
>
> å¤é¨ç±»çä¸ä¸ªå®ä¾é¨åï¼åå»ºåé¨ç±»å¯¹è±¡æ¶ï¼å¿é¡»ä¾èµå¤é¨ç±»å¯¹è±¡

```java
Outer out = new Outer();
Inner in = out.new Inner();
```

åé¨ç±»ä¸å¤é¨ç±»çæåå±æ§éåçæ¶å ä¼åè®¿é®åé¨ç±»çåéï¼å¦ææ³è¦å¨åé¨ç±»ä¸­è®¿é®å¤é¨ç±»çåé¶éè¦å `å¤é¨ç±».this.åéå`

:warning: æååé¨ç±»ä¸è½å®ä¹éææå ä½æ¯å¯ä»¥å®ä¹éæå¸¸é (å äºfinal)

#### éæåé¨ç±»

ä¸ä¾èµå¤é¨å¯¹è±¡ï¼å¯ç´æ¥åå»ºæéè¿ç±»è®¿é®

```Java
public class åé¨ç±» {
        private  String name = "zhangsan";
        //éæåé¨ç±» åå¤é¨ç±»ç¸å
        static class  Inner{
            private String Address = "chengdu";
            private static int count = 1000;
            public void show(){
                //è®¿é®å¤é¨ç±»çå±æ§
                åé¨ç±» åé¨ç±» = new åé¨ç±»();
                System.out.println(åé¨ç±».name);
                System.out.println(Inner.count);
            }
        }
    public static void main(String[] args) {
        //åå»ºéæåé¨ç±»
        åé¨ç±».Inner inner = new åé¨ç±».Inner();
        inner.show();
    }
}
```

#### å±é¨åé¨ç±»

å®ä¹å¨å¤é¨ç±»çæ¹æ³ä¸­ ä½ç¨èå´ä»éäºå½åçæ¹æ³ä¸­ä½¿ç¨ï¼å¹¶ä¸ä¸è½å ä»»ä½çè®¿é®ä¿®é¥°ç¬¦ ç´æ¥å¨æ¹æ³ä¸­åclass ç±»å{   }ï¼

```Java
package Javaåºç¡.å¸¸ç¨ç±»;
/**
 * åè½æè¿°ï¼
 * @Author: ygq
 * @Date: 2021/8/30 16:13
 */
public class Outer {
    private String name = "è¡ç¬å°æ§";
    public void show(){
        String Address = "æé½";
        class Inner{
            private String pwd = "aaa";
            public void show2(){
                //è®¿é®å¤é¨ç±»çå±æ§
                System.out.println(Outer.this.name);
                //è®¿é®å±é¨åé 1.7ä¹ååéå¿é¡»è¦æ±æ¯å¸¸é 1.8èªå¨å final
                System.out.println(Address);
                //è®¿é®åé¨ç±»çå±æ§
                System.out.println(this.pwd);
            }
        }
        //åå»ºå±é¨ç±»å¯¹è±¡ å¹¶è°ç¨å¶æ¹æ³
        Inner inner = new Inner();
        inner.show2();
    }

    public static void main(String[] args) {
        Outer outer = new Outer();
        outer.show();
    }
}
```

#### å¿ååé¨ç±»

æ²¡æç±»åçå±é¨åé¨ç±»ï¼ä¸åç¹å¾ä¸å±é¨åé¨ç±»ç¸åï¼

å¿é¡»ç»§æ¿ç¶ç±»æå®ç°ä¸ä¸ªæ¥å£

å®ä¹ç±»ãå®ç°ç±»ãåå»ºå¯¹è±¡çè¯­æ³åå¹¶ãåªè½åå»ºä¸ä¸ªè¯¥ç±»çå¯¹è±¡

åå°ä»£ç é ä½æ¯å¯è¯»æ§è¾å·®

```java
// ä½¿ç¨å¿ååé¨ç±»ä¼åï¼ç¸å½äºåå»ºäºä¸ä¸ªå±é¨åé¨ç±»ï¼
Usb usb = new Usb(){ // Usbä¸ºä¸ä¸ªæ¥å£
  @Override
  public void service(){
    sout("è¿æ¥çµèæåï¼fanå¼å§å·¥ä½")
  }
};
usb.service();
```

### åè£ç±»

> åºæ¬æ°æ®ç±»åæå¯¹åºçå¼ç¨æ°æ®ç±»å 

```Java
public static void main(String[] args){
  // è£ç®±ï¼ åºæ¬ç±»å â å¼ç¨ç±»å
  // åºæ¬ç±»å
  int num1 = 18;
  // ä½¿ç¨Integerç±»åå»ºå¯¹è±¡
  Integer integer1 = new Integer(num1);
  Integer integer2 = Integer.valueOf(num1);
  
  // æç®±ï¼ å¼ç¨ç±»å â åºæ¬ç±»å
  Integer integer3 = new Integer(100);
  int num2 = integer3.intValue();
  
  // ä¸è¿°ä¸ºjdk1.5ä¹åæ¹æ³ï¼ä¹åæä¾äºèªå¨è£ç®±æç®±
  int age = 30;
  // èªå¨è£ç®±
  Integer integer4 = age;
  // èªå¨æç®±
  int age2 = integer4;
  
  // åºæ¬ç±»ååå­ç¬¦ä¸²ä¹é´è½¬æ¢
  // 1. åºæ¬ç±»åè½¬æå­ç¬¦ä¸²
  int n1 = 100;
  // 1.1 ä½¿ç¨+å·
  String s1 = n1 + "";
  // 1.2 ä½¿ç¨Integerä¸­çtoString()æ¹æ³
  String s2 = Integer.toString(n1);
  String s2 = Integer.toString(n1, x); // xä¸ºè¿å¶è¦æ±
  
  // 2. å­ç¬¦ä¸²è½¬æåºæ¬ç±»å
  String str = "150";
  // ä½¿ç¨Integer.parseXXX();
  int n2 = Integer.parseInt(str);
  
  // boolean å­ç¬¦ä¸²å½¢å¼è½¬æåºæ¬ç±»åï¼"true" ---> true éâtrue âââ> false
  String str2 = "true";
  boolean b1 = Boolean.parseBoolean(str2);
}
```

:taco: Javaé¢åå¯¹-128~127åå»ºäºæ´æ°çç¼å²æ±  å½ç´æ¥ä½¿ç¨Integerå£°æä¸ä¸ªå¯¹è±¡çæ¶å è¿ä¸¤ä¸ªå¯¹è±¡çå°åæ¯ä¸æ ·çInteger ii = 100è¿ç§æ¹å¼

```Java
public static void main(String[] args){
  // é¢è¯é¢
  Integer integer1 = new Integer(100);
  Integer integer2 = new Integer(100);
  sout(integer1 == integer2); // false
  
  Integer integer3 = new Integer(100);// èªå¨è£ç®±
  // ç¸å½äºè°ç¨ Integer.valueOf(100);
  Integer integer4 = new Integer(100);
  sout(integer3 == integer4); // true
  
  Integer integer5 = new Integer(200);// èªå¨è£ç®±
  Integer integer6 = new Integer(200);
  sout(integer5 == integer6); // false
  
  // å ä¸ºç¼å­åºæ°ç» [-128, 127] å¨è¿ä¹åå°åä¸æ ·
}
```

### Stringç±»

> Stringç±»ä¸å¯ä»¥è¢«ç»§æ¿ï¼åºå±æ¯ä¸ä¸ªä¸å¯åçæ°ç»

å­ç¬¦ä¸²æ¯å¸¸éï¼åå»ºä¹åä¸åæ¹åï¼å­å¨å¨å­ç¬¦ä¸²å¸¸éæ± ä¸­ï¼å¯ä»¥å±äº«

- `String s = "Hello";`äº§çä¸ä¸ªå¯¹è±¡ï¼å­ç¬¦ä¸²æ± ä¸­å­å¨
- `String s = new String("Hello");` äº§çä¸¤ä¸ªå¯¹è±¡ï¼**å ãæ± **åä¸ä¸ª

**euqalsæ¹æ³**

```Java
 public boolean equals(Object anObject) {
        if (this == anObject) { //é¦åæ¯è¾ä¸¤ä¸ªå¯¹è±¡çå°åæåç¸åçæ¶å ç´æ¥è¿å true
            //èªå·±åèªå·±æ¯è¾çæ¶åä¹è¿åtrue
            return true;
        }
        if (anObject instanceof String) { //åççä¼ è¿æ¥çå¯¹è±¡æ¯ä¸æ¯Stringç±»å
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }

```

**compareTo**

Stringç±»å®ç°äºComparebleï¼éé¢æä¸ä¸ªæ½è±¡æ¹æ³å«compareToï¼æä»¥Stringä¸­ä¸å®è¦å¯¹è¿ä¸ªæ¹æ³è¿è¡éå

```java
 public int compareTo(String anotherString) {
        int len1 = value.length;
        int len2 = anotherString.value.length;
        int lim = Math.min(len1, len2);
        char v1[] = value;
        char v2[] = anotherString.value;

        int k = 0;
        while (k < lim) {
            char c1 = v1[k];
            char c2 = v2[k];
            if (c1 != c2) {
                return c1 - c2;
            }
            k++;
        }
        return len1 - len2;
    }

```



```java
public class StringTest {
    public static void main(String[] args) {
        String str = "a"+"b"+"c";
        String str1 = "a"+"bc";
        String str2 = "ab"+"c";
        String str3 = "abc";
        String str4 = "abc"+"";
        //ä¸è¿°å ä¸ªå­ç¬¦ä¸²éè¿åç¼è¯ä¹åé½æ¯åä¸ä¸ªå­ç¬¦ä¸²"abc"
    }
}
```



<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210912203338351.png" alt="image-20210912203338351" style="zoom:50%;" />

å¸¸ç¨æ¹æ³

```java
String str = "abcdef";
System.out.println(str.isEmpty());//åºå±å°±æ¯å¤æ­è¿ä¸ªvalueæ°ç»çé¿åº¦æ¯å¦=0
System.out.println(str.charAt(2));
System.out.println(str.indexOf("abc"));
String str1 =   str.concat("yyy");
System.out.println(str1);


// 1. length(); è¿åå­ç¬¦ä¸²é¿åº¦
// 2. charAt(int index); è¿åæä¸ªä½ç½®çå­ç¬¦
// 3. contains(String str); å¤æ­æ¯å¦åå«æä¸ªå­ç¬¦ä¸²
String content = "javaæ¯æå¥½çè¯­è¨, java no1";
sout(content.length()); // 10
sout(content.charAt(content.length() - 1)); // è¨
sout(content.contains("java")); // true
// 4. toCharArray(); è¿åå­ç¬¦ä¸²å¯¹åºæ°ç» 
// 5. indexOf(); è¿åå­å­ç¬¦ä¸²é¦æ¬¡åºç°çä½ç½®
// 6. lastIndexOf(); è¿åå­ç¬¦ä¸²æåä¸æ¬¡åºç°çä½ç½®
sout(content.toCharArray());
sout(content.indexOf"java")); // 0
sout(content.indexOf("java", 4)); // ä»ç´¢å¼4å¼å§æ¾ è¿å12
sout(content.lastIndexOf("java")); // 12
// 7. trim(); //å»æå­ç¬¦ä¸²ååç©ºæ ¼
// 8. toUpperCase(); toLowerCase(); è½¬æ¢å¤§å°å
// 9. endWith(str); startWith(str);  å¤æ­æ¯å¦ä»¥str ç»å°¾ãå¼å¤´
String ct = " hello world ";
sout(ct.trim()); // "hello world"
sout(ct.toUpperCase()); // HELLO WORLD
sout(ct.toLowerCase()); // hello world
sout(ct.endWith("world")); // true
sout(ct.startWith("hello")) // true
// 10. replace(char old, char new); ç¨å¿çå­ç¬¦æå­ç¬¦ä¸²æ¿æ¢æ§çå­ç¬¦æå­ç¬¦ä¸²
// 11. split(); å¯¹å­ç¬¦ä¸²æå
// è¡¥åä¸¤ä¸ªequals/compareTo();æ¯è¾å¤§å°
String s1 = "hello";
String s2 = "HELLO";
sout(s1.equalsIgnoreCase(s2));// å¿½ç¥å¤§å°åæ¯è¾true

// compareTo(); ä¸¤å­ç¬¦ä¸åæ¶æ¯è¾å­ç¬¦å­å¸åºçasciiç 
// å­ç¬¦ç¸åæ¶æ¯è¾é¿åº¦ è¿åå·®å¼

```

### StringBuilderç±»

![image-20210912212515219](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210912212515219.png)



```java
public StringBuilder(String str) {
    super(str.length() + 16);
    append(str);
}
StringBuilder sb = new StringBuilder();//è¡¨é¢ä¸æ¯è°ç¨ç©ºæé å¨ å®éä¸æ¯å¯¹valueæ°ç»åå§å åå§åçé¿åº¦ä¸º16 ï¼åæ¶å¯ä»¥èªå·±æå®æ°ç»çé¿åº¦
```



### BigDecimalç±»

ä½äºmathåä¸­ï¼ä¸»è¦ç¨äºç²¾ç¡®è®¡ç®æµ®ç¹æ°

```Java
BigDecimal bigDecimal = new BigDecimal("1.0");
BigDecimal bigDecimal1 = new BigDecimal("0.9");
//åæ³
System.out.println(bigDecimal.subtract(bigDecimal1));
//å æ³
System.out.println(bigDecimal.add(bigDecimal1));
//ä¹æ³
System.out.println(bigDecimal.multiply(bigDecimal1));
```

### Calendarç±»

- Calendaræä¾äºè·åæè®¾ç½®åç§æ¥åå­æ®µçæ¹æ³
- æé æ¹æ³ `protected Calendar();` ç±äºæ¯protected æä»¥æ æ³ç´æ¥åå»º
- å¶ä»æ¹æ³

| æ¹æ³å                                                       | è¯´æ                                       |
| :----------------------------------------------------------- | :----------------------------------------- |
| static Calendar getInstance()                                | ä½¿ç¨é»è®¤æ¶åºååºåè·åæ¥å                 |
| void set(int year, int month, int date, int hourofday, int minute, int second) | è®¾ç½®æ¥åçå¹´ãæãæ¥ãæ¶ãåãç§           |
| int get(int field)                                           | è¿åç»å®æ¥åå­æ®µçå¼ãå­æ®µæ¯å¦å¹´ãæãæ¥   |
| void setTime(Date date)                                      | ç¨ç»å®çdateè®¾ç½®æ­¤æ¥åæ¶é´                 |
| Date getTime()                                               | è¿åä¸ä¸ªdateè¡¨ç¤ºæ­¤æ¥åçæ¶é´               |
| void add(int field, int amount)                              | æç§æ¥åçè§åï¼ç»æå®å­æ®µæ·»å æåå°æ¶é´é |
| long getTimeInMilles()                                       | æ¯«ç§ä¸ºåä½è¿åè¯¥æ¥åçæ¶é´å¼               |

```Java

public static void main(String[] args) throws ParseException {
    //calendaræ¯ä¸ä¸ªæ½è±¡ç±» ä¸å¯ä»¥ç´æ¥åé å¯¹è±¡
    Calendar calendar = Calendar.getInstance();

    System.out.println(calendar.get(1)); //2021
    System.out.println(calendar.get(Calendar.MONTH)); // 8 ä»0å¼å§
    System.out.println(calendar.get(Calendar.WEEK_OF_MONTH)); //3

    calendar.set(Calendar.YEAR,1998);
    System.out.println(calendar.get(1)); //1998

}

psvm(String[] args){
  // 1. åå»º Calendar å¯¹è±¡
  Calendar calendar = Calendar.getInstance();
  sout(calendar.getTime().toLocaleString());
  // 2. è·åæ¶é´ä¿¡æ¯
  // è·åå¹´
  int year = calendar.get(Calendar.YEAR);
  // è·åæ ä» 0 - 11
  int month = calendar.get(Calendar.MONTH);
  // æ¥
  int month = calendar.get(Calendar.DAY_OF_MONTH);
  // å°æ¶
  int hour = calendar.get(Calendar.HOUR_OF_DAY);
  // åé
  int minute = calendar.get(Calendar.MINUTE);
  // ç§
  int second = calendar.get(Calendar.SECOND);
  // 3. ä¿®æ¹æ¶é´
  Calendar calendar2 = Calendar.getInstance();
  calendar2.set(Calendar.DAY_OF_MONTH, x);
  // 4. addä¿®æ¹æ¶é´
  calendar2.add(Calendar.HOUR, x); // xä¸ºæ­£å°±å  è´å°±å
  // 5. è¡¥åæ¹æ³
  int max = calendar2.getActualMaximum(Calendar.DAY_OF_MONTH);// ææ°æå¤§å¤©æ°
  int min = calendar2.getActualMinimum(Calendar.DAY_OF_MONTH);
}
```

### SimpleDateFormatç±»

å°å­ç¬¦ä¸²ç±»åä¸Date()ç±»åç¸äºè½¬æ¢

- SimpleDateFormatæ¯ä¸ä¸ªä»¥ä¸è¯­è¨ç¯å¢æå³çæ¹å¼æ¥æ ¼å¼ååè§£ææ¥æçå·ä½ç±»
- è¿è¡æ ¼å¼åï¼æ¥æâææ¬ï¼ãè§£æï¼ææ¬âæ¥æï¼
- å¸¸ç¨çæ¶é´æ¨¡å¼å­æ¯

| å­æ¯ | æ¥æææ¶é´         | ç¤ºä¾ |
| :--- | :----------------- | :--- |
| y    | å¹´                 | 2019 |
| M    | å¹´ä¸­æä»½           | 08   |
| d    | æä¸­å¤©æ°           | 10   |
| H    | ä¸å¤©ä¸­å°æ¶ï¼0-23ï¼ | 22   |
| m    | åé               | 16   |
| s    | ç§                 | 59   |
| S    | æ¯«ç§               | 356  |

```Java
package Javaåºç¡.å¸¸ç¨ç±»;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
/**
 * åè½æè¿°ï¼
 * @Author: ygq
 * @Date: 2021/8/30 18:04
 */
public class test {
    public static void main(String[] args) throws ParseException {
        //èªå®ä¹å­ç¬¦ä¸²çæ¨¡å¼ çåç«¯çé¢ä¼ éè¿æ¥çå­ç¬¦ä¸²æ¯ä»ä¹æ ·ç
        DateFormat df = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
        //string->date
        Date date = df.parse("2019/12/12 12:12:12");
        System.out.println(date); //Thu Dec 12 12:12:12 CST 2019
        //date->string
        Date date1 = new Date();
        System.out.println(df.format(date1)); //2021/09/12 16:40:37
        
        
       
        
        Date date = new Date();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyyå¹´MMæddæ¥ HHæ¶mmåssç§");
        System.out.println(sdf.format(date));//2021å¹´08æ30æ¥ 18æ¶06å08ç§
//        è¿éè½¬æ¢çæ¶åéè¦åä¸é¢çè¾å¥æ ¼å¼ä¸è´ å¦åä¼æ¥é
        Date date1 = sdf.parse("2021å¹´01æ11æ¥ 12æ¶12å12ç§");
        System.out.println(date1); //Mon Jan 11 12:12:12 CST 2021

    }
}
```

### Mathç±»

> Java.Langåä¸é¢çç±» ç´æ¥è¿è¡ä½¿ç¨ ä¸ç¨å¯¼å

```Java
public static void main(String[] args) throws ParseException {
    System.out.println(Math.PI);
    System.out.println(Math.random());//[0,1)
    System.out.println(Math.abs(-80)); //ç»å¯¹å¼
    System.out.println(Math.ceil(9.1));//åä¸åå¼
    System.out.println(Math.floor(9.1));//åä¸åå¼
    System.out.println(Math.round(4.6));//åèäºå¥
}
```

### Randomç±»

```java
public class DateTest {
    public static void main(String[] args) throws ParseException {
        Random random =new Random(); //åºå±è¿æ¯è°ç¨æåæé 
        Random random1 = new Random(System.currentTimeMillis());//æåæé  æå®ç§å­
        System.out.println(random.nextInt(12)); //æå®çæçæ°å­çèå´
    }
}
```

