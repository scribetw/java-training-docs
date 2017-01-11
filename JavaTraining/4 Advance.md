# Advanced tricks

## Generic

### 泛型型別

在 Java 還沒導入泛型之前，Collection 類別的物件能夠存入任何型別的物件。

```java
List list = new ArrayList();
list.add("Test");
list.add(new Integer(5));
```

雖然很方便，但缺少型別的檢查，很容易得到非預期的型別物件，造成程式漏洞。

因此之後導入了泛型，能夠在編譯時期進行型別檢查，減少錯誤的發生。

```java
List<String> list = new ArrayList<String>();
list.add("Test");
list.add(new Integer(5)); // COMPILE ERROR
```

泛型型別的定義則可參考下面範例：

```java
public interface List<E> {
    void add(E element);
}
```

由於在建立物件時傳入 `String` 代表 `E`，因此 `add` 方法就只能接受型別為 `String` 的參數。

### 泛型方法

```java
public class Util {
    public static <K, V> boolean compare(Map.Entry<K, V> p1, Map.Entry<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
            p1.getValue().equals(p2.getValue());
    }
}
```

在回傳型態前加上鑽石 `<>` 並定義型別，  
上面的例子可以讓編譯器知道兩個參數物件的 Key 和 Value 型態必須一致。

## Type inference

有了泛型能夠告知編譯器型別後，可以讓編譯器在編譯期間進行靜態的型別檢查。  
下面的例子使用 `K`, `V` 兩個型別變數讓編譯器能夠自動推論 `get` 方法的參數型態，  
具有彈性之外也能兼顧型別的檢查。`K` 為 `String`，`V` 則為 `Integer`。

```java
public interface Map<K, V> {
    V get(K key);
}

Map<String, Integer> map = new HashMap<String, Integer>();
```

型別變數名稱有以下幾個常見的，但其實可以自由命名：

* `T` 類型
* `E` 元素
* `K` 鍵值
* `V` 儲存值

此外還能指定型別的上限與下限，上限規範型別必須為此型別或此型別的子類別，  
下限則規範類型必須是此型別或此型別的父類別。

`T` 型別上限為 `Number`，代表可以為 `Number` 或 `Number` 子類別 \(繼承 `Number`\)。  
例如 `Number`, `Byte`, `Double`, `Float`, `Integer`, `Long`, `Short`... 等都符合。

```java
public interface Example1<T extends Number> {
}
```

`T` 型別下限為 `Integer`，代表可以為 `Integer` 或 `Integer` 父類別。  
例如 `Integer`, `Number`, `Object` 都符合。

```java
public interface Example2<T super Integer> {
}
```

有一種特殊萬用字 `?`，可以代表所有型別，也代表不受限制。

```java
Class<?> c = Class.forName("...");
```

但 `?` 可以結合上限或下限使用：

```java
Class<? extends Annotation> a = Override.class;
```

要注意的是型別變數僅用來編譯時期判斷，在 Bytecode 中不存在 \(稱為 Type Erasure 型別擦除\)，因此以下操作是錯誤的。

```java
public class Test<T> {
    public void print() {
        System.out.println(T.class.getSimpleName()); // ERROR
    }
}

Test<Integer> t = new Test<Integer>();
t.print();
```

真的想得知，必須在呼叫時傳入物件：

```java
public class Test<T> {
    private Class<T> clazz;

    public Test(Class<T> clazz) {
        this.clazz = clazz;
    }

    public void print() {
        System.out.println(clazz.getSimpleName());
    }
}

Test<Integer> t = new Test<Integer>(Integer.class);
t.print();
```

## Type Erasure

型別推論雖然方便，但它只是編譯器的一種Sugar而已，不會記錄在Bytecode裡。  
因此，只有在編譯的時候才有用。上面 T 無法直接使用的例子就可以了解。

Type Erasure 就是說明這種現象，觀看下列兩組方法，你會以為他是多載 \(Overload\)，  
其實不是：

```java
void setObject(Class<? extends Labor> o);
void setObject(Class<? extends Employer> o);
```

事實上最後這兩個的參數在 Bytecode 裡都一視同仁為 Class o，因此根本是一模一樣的方法。因此在同一個類別內不允許同時存在這兩種方法。

## Annotation

`@Deprecated` 和 `@Override` 你已經知道了，這就是標註。

標註本身其實沒有什麼魔力，但搭配其他方式使用就能變成強悍的語法。  
例如 Spring 的 `@Autowired`、JPA 的 `@Entity`、JUnit 的 `@Test` 等，不是標註本身的功勞，  
而是這些函式庫有掃瞄包裹找出套用標註的地方，進行進階的操作。

我們可以自己定義自己的標註，語法和 interface 非常雷同，只多了一個 @。

```java
@Target({
    ElementType.TYPE,
    ElementType.FIELD,
    ElementType.METHOD
})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
}
```

然後就能夠自由的用在想要的地方。

```java
@MyAnnotation
public class Test {
    @MyAnnotation
    private int Num;

    @MyAnnotation
    public void echo() {
    }
}
```

### Meta-annotation

Annotation 也可以再用 Annotation 標註，有兩個很重要：

* Retention

  告訴 JVM Annotation 的保留範圍，有三種選項：

  1. `RetentionPolicy.SOURCE`

     只出現在原始碼裡，不會在編譯後的Bytecode裡。這種單純就是給人或IDE看的註解。

  2. `RetentionPolicy.CLASS`

     經過編譯後存在Bytecode裡，但只在編譯期間可用。執行期間無法透過反射取得。預設值。

  3. `RetentionPolicy.RUNTIME`

     經過編譯後存在Bytecode裡，且執行期間也可透過反射取得。

* Target

  指示 Annotation 所能使用的地方。預設是可以套用在所有地方，但如果有需要限制，  
  可以傳入陣列設定限制使用的地方。  
    1. `ElementType.TYPE` 類別 \(Class, Interface, Enum\)  
    2. `ElementType.FIELD` 成員  
    3. `ElementType.METHOD` 方法  
    4. `ElementType.PARAMETER` 參數  
    5. `ElementType.CONSTRUCTOR` 建構元  
    6. `ElementType.LOCAL_VARIABLE` 區域變數  
    7. `ElementType.ANNOTATION_TYPE` 標註  
    8. `ElementType.PACKAGE` 包裹

### 進階應用

執行期間可利用反射機制取得標註。

例如取得@MyAnnotation:

```java
Annotation anno = this.getClass().getAnnotation(MyAnnotation.class);
```

或可搭配掃瞄包裹函式庫，比方說 [annotation-detector](https://github.com/rmuller/infomas-asl)。

```java
// Scan all .class files on the class path
// Report all .class files, with org.junit.Test annotated methods
final MethodReporter reporter = new MethodReporter() {

    @SuppressWarnings("unchecked")
    @Override
    public Class<? extends Annotation>[] annotations() {
        return new Class[]{Test.class};
    }

    @Override
    public void reportMethodAnnotation(Class<? extends Annotation> annotation,
        String className, String methodName) {
        // do something
    }

};
final AnnotationDetector cf = new AnnotationDetector(reporter);
cf.detect();
```

## Enum

### 取代常數

可以用來取代 public static final int 常數。

```java
public class Util {
    public static final int MON = 1;
    public static final int TUE = 2;

    public static String getWeekday(int weekday) {
        // ...
    }
}

Util.getWeekday(MON);
```

看起來 public static final int 常數可以去除神奇數字，沒什麼不好的，不是嗎？  
但你沒辦法限制使用者的輸入，因為編譯器沒辦法檢查，所以以下非預期的情況仍舊會發生：

```java
Util.getWeekday(1); // 這還好
Util.getWeekday(-1); // Boooooom!!
```

使用列舉類別，限制使用者只能選其中一個，降低錯誤發生率：

```java
public class Util {
    public enum Weekday {
        MON, TUE, ...
    }

    public static String getWeekday(Weekday weekday) {
        // ...
    }
}

Util.getWeekday(Weekday.MON);
```

這下子使用者只能傳入列舉項目，因此 -1 的惡搞將難以發生。而且我們也不必關心 MON 到底代表什麼數字。

### 列舉順查

如果數字仍然是意義非凡的，那可以修改一下列舉：

```java
public enum Weekday {
    MON(1), TUE(2), ...;

    private int flag;
    private Weekday(int flag) {
        this.flag = flag;
    }
    public int getFlag() {
        return flag;
    }
}

Weekday.MON.getFlag(); // 1
```

### 列舉反查

很常發生一種情況，我只知道使用者輸入的數字，能不能對應回去列舉呢？  
很抱歉，這必須要自己寫方法。

第一種方法是迴圈比對 Enum 的 `values()` 各項目，直到找到對應的列舉物件，成本 O\(n\)。

```java
public static Weekday valueOf(int flag) {
    for (Weekday w : values()){
        if(w.getFlag() == flag){
            return w;
        }
    }
    return null;
}
```

但這種方法在項目一多時很沒效率，因此第二種方法是事前建表，事後查表，成本 O\(1\)。

```java
private static final Map<Integer, Weekday> mMap =
    new HashMap<Integer, Weekday>();

static {
    for (Weekday w : values()){
        mMap.put(w.getFlag(), w);
    }
}

public static Weekday valueOf(int flag) {
    return mMap.get(flag);
}
```

建表的時機據網路討論受到 ClassLoader 實作的影響，使用 `static {}` 區塊無法保證在存取前被呼叫。  
需要特別注意。\(Source: [stackoverflow](http://stackoverflow.com/a/1080912) \)

Java 7 後可正常運作 [Java Language Specification 8.9.2. Enum Body Declarations](http://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html#d5e12267)

## Reflection

Java 的反射機制一直以來都為人詬病，不但難以使用，效能也很差。  
因為動態呼叫繞過了許多 JVM 的最佳化與快取機制。\(使用反射呼叫與不透過反射速度大概差了幾十倍\)  
但是為了靈活性有時候可以忍受這種不便。Spring 即使用了反射來達成 `@Autowired` 便利性。

以下呼叫必須要以 `try {} catch {}` 包夾，因為反射過程可能會因為許多狀況拋出例外。

### 動態載入類別

```java
Class c = Class.forName("java.util.ArrayList");
```

### 動態載入方法

```java
Method m = c.getDeclaredMethod("add", Object.class);
m.invoke(c.newInstance(), "Hello World!");
```

### 呼叫 private 方法

在反射機制面前，任何的私有方法都是清楚可見的。在動態載入方法後只需要一行就能夠存取：

```java
m.setAccessible(true);
```

經過此行後反射便能夠穿過保護機制，就可以使用 invoke 呼叫。

### 動態載入標註

記得要被載入的標註 `@Retention` 要設為 RUNTIME 才能在反射時取得，不然會取得 null。

```java
Resource a = (Resource) this.getClass().getAnnotation(Resource.class);
String name = a.name(); // @Resource 的 name 屬性
System.out.println(name);
```

## Threading / Concurrent

### Thread

可以活用 CPU 多核心資源加快速度，但執行緒同步議題一直是很傷腦筋的事。

* Thread class
* Runnable interface
* Executors Thread Pool

Thread 類別可以直接繼承並複寫 run\(\) 方法。但由於單一繼承限制因此彈性較低。

```java
public class SomeThread extends Thread {
    @Override
    public void run() {
    }
}

Thread t = new SomeThread();
t.start();
```

Runnable 介面則只需實作 run\(\) 方法即可，之後則傳給 new Thread\(\) 作為參數以 start\(\) 執行。  
此方法較容易，彈性較高。

```java
public class SomeThread implements Runnable {
    @Override
    public void run() {
    }
}

Thread t = new Thread(new SomeThread());
t.start();
```

### Thread pool

將執行緒作管理，維護執行緒池，可有效提昇資源管理和執行效率。可利用 Executors 類別產生執行緒池。

```java
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(32);
scheduler.scheduleAtFixedRate(new SomeThread(), 0 , 5, TimeUnit.MINUTES);
scheduler.scheduleAtFixedRate(new SomeThread(), 0 , 30, TimeUnit.SECONDS);
```

範例會有兩個固定觸發排程，一個每5分鐘執行一次，  
另一個30秒執行一次，共用執行緒最大數量為32的執行緒池。

### 並行處理

* java.util.concurrent 包裹
* synchronize

多執行緒間的資源共享需要特別注意，因為有可能發生 A 更新了變數，但 B 當下讀取的時候還沒有更新。  
假如 B 把讀到的結果寫回去，就變成 A 的更新被覆蓋了。  
傳統上使用 Read/Write Lock, Mutex, Semaphore 等處理此類問題，  
如果處理不當很容易造成 busy waiting、deadlock、Starvation 等問題，影響系統穩定度。

java.util.concurrent 裡提供許多並行處理的類別：

#### Atomic 系列

java.util.concurrent.atomic 包裹。

AtomicInteger、AtomicLong 等物件，保證數值在讀取或寫入的時候是原子性的，即不可分割。  
這在多個執行緒共用同一變數的時候非常好用。使用也很簡便，以 AtomicInteger 為例：

* `get`取得數值
* `addAndGet`累加並取得新數值
* `getAndIncrement`/`getAndDecrement`等同於 `i++` 和 `i--`
* `incrementAndGet`/`decrementAndGet`等同於 `++i` 和 `--i`

但要注意為了維持原子性，效能會比沒有同步保護的變數還要差，  
所以非必要不要拿 Atomic 系列當一般型態的代替品。

#### Concurrent Collection 系列

HashMap 並非執行緒安全的物件。  
執行緒安全的是 Hashtable，但由於 Hashtable 是整個區段一把鎖，因此效能上會比較差。  
ConcurrentHashMap 利用分段鎖的方式使得其他執行緒在讀取時不一定會被鎖，  
可以提昇並行效能。同時在真正遇到讀寫同區塊的情況下，也可以保證同時間只有一個執行緒存取。

另一種機制: Collections.synchronizedMap\(map\) 就是為整個 Map 上一道同步鎖，  
因此情況與 Hashtable 類似。

