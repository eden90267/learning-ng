# Chap 03. Hello Angular

此篇將討論：

- 基本術語
- 模組
- 元件
- 資料
- 事件連結
- 元件資料傳遞

## 啟動第一個 Angular 專案

```shell
$ ng new stock-market
```

`ng new` 的參數可設定選項：

- 是否使用 CSS、SCSS、或其他 CSS 框架 (ng new --style=scss)
- 是否產生導向模組 (ng new --routing)
- 是否需要行內樣式 / 模板
- 元件是否需要前綴 (ng new --prefix=acme，對所有元件前綴 acme)

還有其他參數。

## 認識 Angular CLI

Angular CLI 不只產生骨架。事實上，它可在開發過程中執行各種工作：

- 輔助啟動
- 服務應用程式
- 執行測試 (單元與完整)
- 建置與發佈
- 產生新元件、服務與導向等

每個工作有相對應的 CLI 命令。

### 執行應用程式

剛已經產生了應用程式，下一步是執行以從瀏覽器觀察。技術上有兩種執行方式：

- 以開發模式執行，由 Angular 的 CLI 編譯並更新 UI
- 以上線模式執行，經最佳化編譯成靜態檔案

現在以開發模式在專案根目錄執行：

```shell
$ ng serve
```

經過一陣子處理與編譯以供執行應用程式的檔案

- main.js
- polyfill.js
- runtime.js
- styles.js
- vendor.js

從瀏覽器打開 http://localhost:4200

可讓 `ng serve` 命令在終端機中持續執行並繼續修改。它會在儲存檔案自動更新瀏覽器開啟的應用程式。

## Angular 應用程式的基礎

Angular 為 SPA，因此載入是由一個對伺服器的請求觸發。從瀏覽器開啟一個 URL 時會發出第一個對伺服器的請求。第一個請求會回傳一個 HTML 網頁，然後它載入必要的 JavaScript 以載入 Angular 與我們的程式碼和模板。

Angular CLI 產生的結構：

![](https://imgur.com/JwDWxwb.png)

### index.html

要注意的是 `<app-root>` 元件，它是載入應用程式碼的標記。

載入核心 Angular 腳本與應用程式碼的部分呢？它是由 ng serve 命令在執行期動態的插入，結合所有廠商函式庫、應用程式碼、樣式表、放在個別檔案的行內模板，並於瀏覽器繪製該頁時插入到 index.html 中。

### main.ts

index.html 檔案決定載入什麼檔案。main.ts 識別應用程式啟動時要載入什麼 Angular 模組，它也可以改變應用程式層級組態 (譬如使用 enableProdMode() 關閉框架層級的斷言與檢驗)

```javascript
import { enableProdMode } from '@angular/core';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';
import { environment } from './environments/environment';

if (environment.production) {
  enableProdMode();
}

platformBrowserDynamic().bootstrapModule(AppModule) // 啟動 AppModule
  .catch(err => console.log(err));
```

main.ts 檔案部分大部分是通用的，通常無需修改此進入點檔案。它主要目的是對 Angular 框架指出應用程式的核心模組並從該點觸發其餘應用程式原始碼。

### app.module.ts

這是應用程式原始碼的啟動位置。應用程式模組檔案可視為應用程式的核心組態，載入相關與必要的相依檔案、宣告使用的元件、標記應用程式的主要進入點元件：

```javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({                 // NgModule 這個 TypeScript 的標記指出此類別定義為一個 Angular 模組
  declarations: [           // 宣告應用程式使用的元件 or 指示
    AppComponent
  ],
  imports: [                // 匯入其他功能模組
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent] // 啟動應用程式的進入點元件
})
export class AppModule { }
```

TypeScript 專屬的功能：注釋，它能讓我們以注釋與屬性以及元功能修飾類別

Angular 大量使用注釋，例如使用模組與組件的注釋。

其核心：

- declarations：定義此模組中可用於此 HTML 範圍內的所有元件。所有元件必須在使用前宣告
- imports：你不會建構應用程式的每一個功能，imports 陣列可匯入其他 Angular 應用程式與函式庫模組，並利用這些元件、服務、與其他已經寫在這些模組中的功能
- bootstrap：bootstrap 陣列定義應用程式的進入點元件。若未將主元件加入，則應用程式不會啟動，因為 Angular 不知道要從 index.html 中找什麼元素

加入

- 新元件
- 服務
- 函式庫
- 模組

通常需要修改這個檔案 (沒使用 CLI 的話)

### 根元件 - AppComponent

它是真正提供應用程式功能的 Angular 程式碼，是主要元件：

```javascript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',                // 此 DOM 選擇器會被轉譯成此元件的一個實例
  templateUrl: './app.component.html', // 此元件的 html 模板，此例中為指向它的 URL
  styleUrls: ['./app.component.scss']  // 元件專屬的樣式表，同樣指向另一個檔案
})
export class AppComponent {            // 元件類別與成員和樣式
  title = 'stock-market';
}
```