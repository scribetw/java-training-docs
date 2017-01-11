# TDD \(Test-Driven Development\)

傳統的開發思維都是瀑布式 \(即需求分析→設計→實作→測試→維護\)，實作完成才進行測試。  
但近年來敏捷開發扭轉了這種流程，以因應快速變更的需求與降低變更的成本。  
其中測試驅動開發變成一種顯學，完全扭轉先開發後測試的步驟，而是在了解需求時，  
先設計對應的測試案例，再根據測試案例實作出能通過測試的程式碼。

這樣實作過程的目標就很明確：實作能通過全部測試的程式碼即可。

## JUnit

建立自動化單元測試的測試框架 xUnit 家族，而且主流 IDE 皆有內建支援。  
原理很簡單，每次產生一個物件，並呼叫待測方法，以斷言比較實際回傳值和預期回傳值，  
如果相同表示通過測試。可根據需求修改斷言條件。 \(例如 `assertTrue`, `assertNull` 等\)

* `@BeforeClass`

  整個測試案例開始前的類別初始化，只做一次

* `@Before`

  單一測試案例開始前的初始化，以確保前一次的測試結果不會影響到下一個

* `@Test`

  測試案例本體

* `@After`

  單一測試案例結束的清理動作

* `@AfterClass`

  整個測試案例結束的清理動作

```java
    import org.junit.Assert;
    import org.junit.Test;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonPrimitive;
    public class ContentParserTest {
        @Test
        public void testGetContentAsByteArray() throws Exception {
            JsonObject content = new JsonObject();
            content.add("iTest", new JsonPrimitive(-1));
            content.add("sTest", new JsonPrimitive("TEST"));
            ContentParser contentParser = new ContentParser(content);
            byte[] expect = new byte[] {-1, -1, -1, -1, 4, 0, 0, 0, 0x54, 0x45, 0x53, 0x54};
            byte[] actual = contentParser.getContentAsByteArray();
            Assert.assertArrayEquals(expect, actual);
        }
    }
```

測試單一方法因為不可能傳入所有的可能值進行測試，因此至少要傳入一般值、邊界值以進行測試。  
以int來說的話是 `0`, `1`, `-1`, `Integer.MAX_VALUE`, `Integer.MIN_VALUE`。  
如果日後程式發生 Bug，引發 Bug 的值在修正程式後也需要作成新的測試案例以避免未來再次發生。  
因此通過更完整的測試案例代表著程式碼的更強健性。  
好的測試案例應該是隨著開發而越來越完整，並且能夠一直使用的。

## Mocking Library

有的時候待測的方法因為與某些函式庫耦合 \(例如 JDBC、網路通訊、檔案系統\)，  
使得測試一定要有對應的環境，這提高了測試的難度。  
與實際環境在一起的測試叫做整合測試，在單元測試中不應該出現。  
單元測試應該只是測試程式邏輯是否正確，不包含外部的實際操作。

因此需要 Mocking Library 的協助。\(資料庫有另一種方案是透過 DBUnit 作整合測試，這裡就不解釋\)

Mocking Library 的功用就是模擬並隔絕這些外部操作，使得我們的程式不需要依賴真實的物件。  
這需要搭配良好的解耦合設計和面向介面設計，否則會發現很難把模擬物件代進去。  
這時應該考慮重新設計類別，以提高可測試性。  
\(所有的類別都是可以測試的，若發現測試很難寫，那一定是類別設計不良\)

在此推薦兩種 Mocking Library，一個是 EasyMock，一個是 JMockit。

* [EasyMock](http://easymock.org/)
* [JMockit](https://code.google.com/p/jmockit/)

### EasyMock

如其名，簡單易用，但功能上比較有限制。EastMock遵循 Record, Replay, Verify 三步驟。  
即是先記錄可能被呼叫的方法和回傳值、進行重播、跑完之後驗證呼叫方式是否符合預期。

首先先建立模擬物件。

```java
IDatabase db = createMock(IDatabase.class);
```

記錄被呼叫的方法和回傳值。重播。

```java
expect(db.fetch("1")).andReturn("Hello world.");
expect(db.fetch("2")).andReturn("Easymock is easy.");
replay(db);
```

實際方法執行，檢查是否正確。

```java
myClass.setDatabase(db);
myClass.doSomething();
verify(db);
```

### JMockit

很強大的 mock 函式庫，利用 Java Instrumentation API \(-javaagent\) 動態插入 bytecode，  
使得幾乎任何東西都能 mock，這包含介面、物件、靜態方法、甚至是 Java API！

JMockit同樣也支援 Record, Replay, Verify 三步驟，但不必特別呼叫replay和verify方法。

建立模擬物件。

```java
@Mocked
IDatabase db;
```

記錄被呼叫的方法和回傳值。

```java
new Expectations() {
    {
        db.fetch("1"); result = "Hello world.";
        db.fetch("2"); result = "JMockit is easy too.";
    }
}
```

實際方法執行，檢查是否正確。

```java
myClass.setDatabase(db);
myClass.doSomething();
```

## Code Coverage

程式碼覆蓋率 \(測試覆蓋率\) 代表單元測試的品質。覆蓋率越高表示越多地方有被測到。  
經過測試通過的程式碼才是可靠的，也代表一種保證。

一般來說安裝完程式碼覆蓋率套件後，執行測試。就可以得到結果，  
有執行的程式碼為綠色，沒執行過的程式碼為紅色。

* JaCoCo
* JMockit Coverage
* IDEA Code Coverage \(Community available since 14\)



