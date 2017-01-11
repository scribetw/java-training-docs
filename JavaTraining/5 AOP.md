# Aspect-Oriented Programming

## 基本概念

除錯記錄、資料庫交易機制......往往會有許多流程需要在一個方法中被呼叫多次，  
這使得方法被活生生的給「切」\(Cut\)成好幾段了，使得維護成本增加。

再者以除錯記錄為例，假設有需要修改，甚至是要全部拿掉，  
這時候能做的就只有一行一行修改或一行一行刪除，非常麻煩。

這時候提出了一種新的概念，將這些切的業務邏輯面\(Aspect\)集中起來，  
需要的時候就在指定的切點\(Pointcut\)縫合\(Weave\)起來。  
這樣既不會讓方法被切成好幾段，維護上也比較方便，重要的是可以靈活使用。

## AspectJ

AspectJ 利用了對 bytecode 的修改來達成縫合的動作，因此需要特別的工具來編譯。  
但好處是效率好。

Spring Framework 也有支援 AOP \(Spring AOP\)，  
預設是使用動態代理的方式來達成，不需要工具，但效率會比較差。  
但 Spring 也能結合 AspectJ。

有兩種方式可以定義 Aspect，一種是傳統的 \*.aj 檔案，  
一種是直接使用標註 @Aspect 在原始碼上，很顯然的後者易讀性較高，也較推薦。

```java
@Aspect
public class LoggingAspect {
    @Pointcut("execution(@com.example.aopdemo.annotation.Log * com.example.aopdemo.service..*())")
    public void computePointcut() {}

    @Before("computePointcut()")
    public void comLogBefore() {
        System.out.println("Before com. (AspectJ)");
    }

    @After("computePointcut()")
    public void comLogAfter() {
        System.out.println("After com. (AspectJ)");
    }
}
```

## @Pointcut

定義切入點，例如在指定方法被執行的時候。

範例是只要是com.example.aopdemo.service下的任意類別加上 @Log 的方法被執行的話。

## @Before, @After

引用切入點，在切入點之前或之後執行自訂的程式區段。

範例為在切入點前印出一段文字，然後執行完之後也會印一段文字。

以下是反組譯某個加上 @Log 的方法，可以看到 Aspect 確實被 Weave 到 bytecode 裡面了。  
因此方法的前後各會執行一段指定的內容。

```
// class version 50.0 (50)
// access flags 0x21
public class com/example/aopdemo/service/ComputerServiceImpl implements com/example/aopdemo/service/ComputerService  {
  // access flags 0x1
  public compute()V
  @Lcom/example/aopdemo/annotation/Log;()
    TRYCATCHBLOCK L0 L1 L2 java/lang/InterruptedException
    TRYCATCHBLOCK L3 L4 L4 java/lang/Throwable
   L3
    LINENUMBER 25 L3
    INVOKESTATIC com/example/aopdemo/aspect/LoggingAspect.aspectOf ()Lcom/example/aopdemo/aspect/LoggingAspect;
    INVOKEVIRTUAL com/example/aopdemo/aspect/LoggingAspect.comLogBefore ()V
   L0
    ALOAD 0
    GETFIELD com/example/aopdemo/service/ComputerServiceImpl.delaySec : I
    SIPUSH 1000
    IMUL
    I2L
    INVOKESTATIC java/lang/Thread.sleep (J)V
   L1
    LINENUMBER 28 L1
    GOTO L5
   L2
    LINENUMBER 26 L2
    ASTORE 1
   L6
    LINENUMBER 27 L6
    ALOAD 1
    INVOKEVIRTUAL java/lang/InterruptedException.printStackTrace ()V
   L5
    LINENUMBER 29 L5
    GOTO L7
   L7
    INVOKESTATIC com/example/aopdemo/aspect/LoggingAspect.aspectOf ()Lcom/example/aopdemo/aspect/LoggingAspect;
    INVOKEVIRTUAL com/example/aopdemo/aspect/LoggingAspect.comLogAfter ()V
    RETURN
    LOCALVARIABLE this Lcom/example/aopdemo/service/ComputerServiceImpl; L3 L4 0
    LOCALVARIABLE e Ljava/lang/InterruptedException; L6 L5 1
    MAXSTACK = 2
    MAXLOCALS = 3
}
```



