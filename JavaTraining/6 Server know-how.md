# Server Know-how

## NIO Client/Server Framework

java.nio 包裹的封裝。  
著名的有 [Apache MINA](http://mina.apache.org/) 和 [Netty](http://netty.io)，  
這兩套框架其實都是出自同一位韓國人作者 \(Trustin Lee\) 之手。

### Session

連線，例如 192.168.1.1:1234 連過來的。可對 Session 進行讀寫動作。

### Filter

過濾器，可針對連線進行操作，例如針對封包進行加/解碼、寫入除錯訊息、記錄流量、阻擋黑名單連線等。

### Handler

一種特殊的 Filter，專門用來處理解碼後的邏輯行為，並產生結果封包進行編碼傳送。

### Decoder

將二進位封包轉成 POJO 物件。對人來說將一堆 0101 轉成物件，是解碼的動作。

### Encoder

反過來，將 POJO 物件轉成二進位封包以作發送用途。

## Spring Framework

一套輕量級 Java Bean 框架。利用 XML 或標註動態建立 Beans 並透過 `@Autowired` 標註實現依賴注入。

```java
public interface AService {}

@Service
public class AServiceImpl implements AService {}

@Component
public class MyBusinessLogic {
    @Autowired
    private AService aService;

    public void myMethod() {
        // I can use aService here.
    }
}
```

## Java Persistence API \(JPA\)

永續層的規範 API 介面 \(JSR-220 / JSR-317\)，業界常用的實作函式庫是 Hibernate。  
應該說是先有 Hibernate 之後，才有 JPA 標準制定，然後 Hibernate 也實現它 \(hibernate-jpa\)。

因為 Hibernate 完全支援 JPA，而且 JPA 也可以抽換實作\(就像 JDBC 那樣\)，  
因此較建議學習 JPA 的規範。

### Entity

首先是定義物件的哪些屬性要和哪張表的哪個欄位對應。這裡大量使用標註來進行對應。

```java
@Entity
public class Author {
    @Id
    private Integer id;
    private String firstName;
    private String lastName;

    @ManyToMany
    private List<Book> books;
}
```

上例對應 Author 表的 id, firstName, lastName 三個欄位。id為主鍵。

| id | firstName | lastName |
| --- | --- | --- |
| 1 | JH | Liu |

### JPQL

使用 Java Persistence Query Language \(JPQL\) 可以不必依賴底層的 Native SQL  
寫出僅適用於某資料庫平台的語法，而可以通用於各資料庫而不需修改。

基本上語法與 Native SQL 十分類似，與 Hibernate 的 HQL 也幾乎一模一樣。  
唯一的差異在於 `SELECT u FROM User u` 的 `SELECT`，JPQL 不可省略但 HQL 可以。

### Criteria

使用物件串出查詢。好處是可以在編譯期間就發現錯誤，缺點則是往往得寫的很長。

初期不支援修改、刪除。JPA 後續版本才有支援。

```java
public List<Computer> getVisibleComputers() {
    return sessionFactory.getCurrentSession()
        .createCriteria(Computer.class)
        .add(Restrictions.eq("visible", 1))
        .list();
}
```

## Quartz Task Schuduler

工作排程器。  
字串，以下為簡單說明。  
類似 \*nix 的 crontab，可以設定週期格式並執行指定工作。

### cron

可使用方便工具 [Cron Maker](http://www.cronmaker.com/) 協助產生與分析 Cron 字串，以下為簡單說明。

```
0 0 12 * * ? *
秒 分 時 日 月 週 年
=> 每天 12:00:00 執行
```

詳細可參見 Quartz [CronTrigger](http://www.quartz-scheduler.org/docs/tutorials/crontrigger.html) 的說明頁面。

