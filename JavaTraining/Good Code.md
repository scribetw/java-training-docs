Good Code
=========

What is good code?
------------------

 * 可讀性高
     * 通常也較易維護，不會像坨屎
 * 易測試性高
     * 低耦合、高內聚
 * Bug 少

How to write good code?
-----------------------

 * 遵循語言規範
     * google-styleguide
	 * Java SDK source
	 * PSR-0, PSR-1 ...
 * 排版
     * 一行控制在 80 字以內，最多不要超過 100 字
	 * 可以在標點符號處換行
 * 註解
     * 說明方法用途及參數意義，這幾乎是茫茫程式海的一盞救命明燈，必寫
	     * JavaDoc, XMLDoc
	 * 言簡意賅，說明這樣寫的緣由
	     * ex: 為了迴避特定已知問題、規格等
	 * 活用 // TODO, FIXME
	     * 更動前跟同事、前輩討論可以快速找到
		 * 暫時不改，有空/有能力再改
 * 命名
     * 常數命名全大寫 `THIS_IS_AN_APPLE`
	 * 變數及方法命名要有意義
	     * 若非必要，不要用 i, j, temp 等命名
	 * 駝峰式命名法
	     * 遇到全大寫的簡寫字串仍然以駝峰處理，例如 Xml, Html, Dvd
 * 重視軟體工程
     * 自動化單元測試
	     * xUnit test suite
		 * 提高測試案例覆蓋率，每發現一個錯誤，就將其寫成測試案例
		 * Test-Driven Development (測試驅動開發 TDD) 先寫測試 (案例)，再實作
     * 活用設計模式、避免反面模式
     * SOLID
	     * Single responsibility principle
		   一個類別該只有單一功能
		 * Open/closed principle
		   軟體中的對象應該對於擴展是開放的 (自由繼承)，但是對於修改是封閉的 (程式碼審查)
		 * Liskov substitution principle
		   任何子物件必然能替代父物件
		 * Interface segregation principle
		   類別不應強迫依賴用不到的介面
		     * 以實作多個小介面取代實作一個大介面
			 * 單一介面功能越少越好
		 * Dependency inversion principle
		   依賴抽象而不是實作 (介面導向)
		     * 控制反轉
		     * 依賴注入 (by Constructor, Setter or field)
 * 名言
     * 越早修復 Bug，成本越低
	     * 開發過程就發現 vs 出貨到客戶端才發現，前者只要花你半天時間，後者公司可要賠錢的
     * "Premature optimization is the root of all evil (過早的最佳化是萬惡的根源)" -- Donald E. Knuth
         * 先求有再求好
	     * 循環進行重構
	 * DRY (Don't repeat yourself)
		 * 不要用剪貼簿作程式開發
		 * 抽出重複部份共用
	 * KISS (Keep it simple, stupid)
	     * 越簡單越好
	 