# Code Conventions

## Code Conventions for the Java TM Programming Language

* [原始文件](http://www.oracle.com/technetwork/java/codeconv-138413.html)
* 一個 java 檔避免超過 2000 行
* 每一行一個敘述
* 一行維持 80 個字元以內，最多不超過 100 個字元
  * 在標點符號後換行
  * 運用字串串加切割字串
* 命名規範
  * 類別、介面開頭大寫駝峰 \(CamelCase\)
  * 變數開頭小寫駝峰 \(camelCase\)
  * 常數全部大寫 \(UPPER\_CASE\)
* 註解

  * TODO, FIXME, XXX
  * Annotation
    * @Override
    * @Deprecated
    * \[javax.annotation\] @Nullable, @Nonnull
  * Javadoc

    ```java
      /**
      * 類別說明。
      *
      * @author 作者
      * @since 自何版本增加
      */
      public class Test {
          /** 成員說明。 */
          private int age;

          /**
           * 方法說明。
           *
           * @param var1 變數一說明
           * @param var2 變數二說明
           * @return 回傳值說明
           */
          public int method1(String var1, int var2) {}
      }
    ```

## Static Syntax Analysis

### IntelliJ IDEA Inspections

此為 Java IDE IntelliJ IDEA 內建的檢查功能，可針對語法進行檢查，  
找出原始碼的壞味道 \(Code Bad Smell\)。

平常在撰寫原始碼時IDEA已在背景分析並指出問題，但也可以使用Analyze→Inspect Code來掃描進行檢查。

IDEA內建功能。

### FindBugs

為掃瞄 .class 檔找出壞味道的靜態語法分析軟體。  
此軟體偏重於指出不良的寫法 \(壞習慣、脆弱性、安全議題、效能議題\)，可以找出潛在的臭蟲。

[臭蟲描述](http://findbugs.sourceforge.net/bugDescriptions.html)

IDEA和eclipse皆有對應套件可用。

### Checkstyle

為掃瞄 .java 檔找出壞味道的靜態語法分析軟體。  
此軟體偏重於指出違反語法規範的寫法，例如變數的命名方式、檔案行數、修飾子用法等等。  
使開發人員可以確實遵守語法規範，提昇可讀性、降低錯誤發生率。

IDEA和eclipse皆有對應套件可用。

[可用掃瞄](http://checkstyle.sourceforge.net/availablechecks.html)

