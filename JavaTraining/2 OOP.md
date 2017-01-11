OOP (Object-Oriented Programming)
=================================

Interface
---------

* 面向介面而非物件 (Interface vs. Concrete Class)
    * 可抽換性 (樂高積木)
        * List: ArrayList, LinkedList, Stack...
        * Set: HashSet, TreeSet, EnumSet, LinkedHashSet...
        * Storage: FloppyStorage, HardDiskStorage, CloudStorage...
    * 降低耦合度，提高可測試性
        * 搭配 Stub 或 Mock 元件
    * 契約式設計 (Design by Contract)
        * 確實規範類別應實作方法及參數
        * 外露方法夠用就好

Abstract class
--------------

* 抽象
    * 抽取共同流程 (ex: 填寫制式表格、約朋友出來、通報管理者)
    * 具體怎麼作則由子物件自行決定
       * 填寫制式表格: 用鉛筆填、用原子筆填、用電腦填...
       * 約朋友出來: 用電話約、用簡訊約、用即時訊息約...
       * 通報管理者: 電子郵件、簡訊、跳出桌面視窗...
* 可視為能夠實作共同流程的介面，減少重複程式碼

Extend / Implement class
------------

* 單一繼承
    * 所有父物件能做的，我都能作
    * 但是父物件做的方式，可能跟我的方式不同 (Override)
    * 除此之外，我還提供更多父物件沒有的功能 (Extend)
* 多重實作
    * 我能作... (契約式設計)
        * 我能打籃球、又能跑步、也能游泳，所以這三項比賽都可以找我
            * Class Me implements BaseballPlayer, Runner, Swimmer
        * 但是我不會畫漫畫，要畫漫畫的比賽不要找我
            * (Me instanceof ComicArtist) == false
        * 反之，實作「會畫漫畫的」就一定會畫漫畫 (畫得好不好再說)，
          管他是黑人、白人還是機器人 (多型)
            * ComicArtist someone;
              someone.drawOnePage();