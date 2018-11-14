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

```typescript
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

```typescript
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

```typescript
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

- Angular 中的元件只是 TypeScript 類別，以一些屬性與元資料修飾。此類別封裝元件的所有資料與功能，而修飾子指定如何轉譯成 HTML
- 應用程式的選擇器是 Angular 找出 HTML 網頁中特定元件的 CSS 選擇器。雖然我們使用元素選擇器，但它可以是從 CSS 類別到屬性等任何 CSS 選擇器
- templateUrl 是繪製此元件的 HTML 路徑。也可用行內模板而非指定 templateUrl
- styleUrls 是對應模板的樣式表，它封裝此元件的所有樣式表，無須擔心一個元件的 CSS 類別會影響其他元件。與 templateUrl 不同，styleUrls 是個陣列

元件類別本身最終封裝元件的所有功能，可將此元件類別的責任視為兩個部分：

- 載入並保存繪製此元件的所有資料
- 處理元件中任何元素可能會發出的事件

類別中的資料會驅動元件的顯示。

```html
<h1>
  {{title}}
</h1>
```


- 連結元件類別中的一個欄位
- 雙大括弧語法指示 Angular 替換相對應類別變數

## 建構元件

我們建構一個顯示股票名稱、代號、價格、漲跌的股票小工具。

```shell
$ ng g c stock/stock-item
```

- Angular CLI 有個 generate 命令，可用於產生元件，也可產生其他 Angular 元素，例如介面、服務、模組等
- 此例告訴 Angular CLI 要在 stock 目錄下產生稱為 stock-item 的元件。若未指定 stock，他會在應用程式目錄下產生

此命令會產生新元件的相關檔案：

- stock-item.component.ts
- stock-item.component.html
- stock-item.component.css
- stock-item.component.spec.ts (元件單元測試的骨架)

此外，他更新 app module，使我們的 Angular 應用程式能識別新模組

下面是使用元件時建議的慣例：

- 檔名以建構項目的名稱開始
- 接著元素型別
- 最後是相關副檔名

看一下修改過的 app.module.ts：

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';
import { StockItemComponent } from './stock/stock-item/stock-item.component'; // 匯入新建構元件

@NgModule({
  declarations: [
    AppComponent,
    StockItemComponent // 新元件加入
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

必須確保新元件被匯入並加到 declarations 陣列中。

### 使用新元件

首先，看一下 stock-item.component.ts 檔案：

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-stock-item', // 注意前綴 app，這是 Angular CLI 依預設加入的，也可自行指定
  templateUrl: './stock-item.component.html',
  styleUrls: ['./stock-item.component.scss']
})
export class StockItemComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
```

接下來在主要應用程式元件中定義符合選擇器的元素 (app.component.html)：

```html
<div style="text-align:center">
  <h1>
    Welcome to {{ title }}!
  </h1>
  <app-stock-item></app-stock-item>
</div>
```

### 認識資料連結

如下改變元件程式碼 (stock-item.component.ts)：

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-stock-item',
  templateUrl: './stock-item.component.html',
  styleUrls: ['./stock-item.component.scss']
})
export class StockItemComponent implements OnInit { // 實作 OnInit，讓元件初始化取得掛鉤

  public name: string;
  public code: string;
  public price: number;
  public previousPrice: number;

  constructor() { }

  ngOnInit() {
    this.name = 'Test Stock Company';
    this.code = 'TSC';
    this.price = 85;
    this.previousPrice = 80;
  }

}
```

Angular 給我們元件的掛鉤以於元件初始化、繪製視圖、摧毀等事件時採取動作。我們會擴充原本空洞的元件

- OnInit：在 Angular 框架建構元件與資料欄位初始化後執行。通常建議在 OnInit 掛鉤中執行元件的初始化工作，使其容易進行測試而無需觸發初始化流程
- ngOnInit：OnInit 介面的函式實作
- 類別成員變數

接下來修改模板 (stock-item.component.html)：

```html
<div class="stock-container">
  <div class="name">
    <h3>{{name}}</h3> - <h4>{{code}}</h4>
  </div>
  <div class="price">
    $ {{price}}
  </div>
</div>
```

```scss
.stock-container {
  border: 1px solid black;
  border-radius: 5px;
  display: inline-block;
  padding: 10px;

  & > .name {
    & > h3, & > h4 {
      display: inline-block;
    }
  }
}
```

我們使用了稱為插值的雙大括弧記號法 (`{{ }}`)。這是 Angular 的單向資料連結。單向資料連結指以元件中的值更新 UI，然後在元件中的值有變化時持續更新 UI。若沒有單向資料連結，我們就必須寫程式取出元件中的值、找到正確 HTML 元素、更新其值、監聽程序追蹤元件值的變化，並有變化更新其值。有了資料連結就可以不用寫這些程式。

資料連結的運算式可以更複雜：

```html
<div class="stock-container">
  <div class="name">
    {{name + ' (' + code + ')'}}
  </div>
  <div class="price">
    $ {{price}}
  </div>
</div>
```

插值運算式 Angular 會將它當作一般 JavaScript 處理並回傳字串值給 UI。

## 認識屬性連結

Angular 不只提供連結字串，還有 DOM 元素屬性。它能讓我們修改繪製在瀏覽器中的 HTML 的內容和行為。

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-stock-item',
  templateUrl: './stock-item.component.html',
  styleUrls: ['./stock-item.component.scss']
})
export class StockItemComponent implements OnInit {

  public name: string;
  public code: string;
  public price: number;
  public previousPrice: number;
  public positiveChange: boolean;

  constructor() { }

  ngOnInit() {
    this.name = 'Test Stock Company';
    this.code = 'TSC';
    this.price = 85;
    this.previousPrice = 80;
    this.positiveChange = this.price >= this.previousPrice;
  }

}
```

```scss
.stock-container {
  border: 1px solid black;
  border-radius: 5px;
  display: inline-block;
  padding: 10px;
}

.positive {
  color: green;
}

.negative {
  color: red;
}
```

```html
<div class="stock-container">
  <div class="name">
    {{name + ' (' + code + ')'}}
  </div>
  <div class="price" [class]="positiveChange ? 'positive' : 'negative'">
    $ {{price}}
  </div>
</div>
```

這就是 Angular 的屬性連結語法，它連結運算式的值與方括號間的 DOM 屬性。`[]` 是可用於元素上任何屬性以單向連結元件到 UI 的通用語法。

此例中，Angular 把它當作一般 JavaScript 運算式計算並指派值給 div 元素的類別屬性。這裡是把 price 的類別直接取代成 positive。

若元件的 positiveChange 變數值改變，Angular 會自動的重新計算 HTML 中的運算式並更新。

Angular 的資料連結只操作 DOM 屬性而非 HTML 屬性。

> **HTML 屬性與 DOM 屬性的差別？**
>
> HTML 屬性通常用於初始化 DOM 元素，之後就對元素沒有作用。思考元件到 UI 的單向連結時要記住這件事！

### 認識事件連結

這裡討論如何處理使用者的互動與 Angular 的事件連結。

stock-item.component.ts：

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-stock-item',
  templateUrl: './stock-item.component.html',
  styleUrls: ['./stock-item.component.scss']
})
export class StockItemComponent implements OnInit {

  public name: string;
  public code: string;
  public price: number;
  public previousPrice: number;
  public positiveChange: boolean;
  public favorite: boolean;

  constructor() { }

  ngOnInit() {
    this.name = 'Test Stock Company';
    this.code = 'TSC';
    this.price = 85;
    this.previousPrice = 80;
    this.positiveChange = this.price >= this.previousPrice;
    this.favorite = false;
  }

  toggleFavorite() {
    console.log('We are toggling the favorite state for this stock');
    this.favorite = !this.favorite;
  }

}
```

接著更新 UI (stock-item.component.html)：

```html
<div class="stock-container">
  <div class="name">
    {{name + ' (' + code + ')'}}
  </div>
  <div class="price" [class]="positiveChange ? 'positive' : 'negative'">
    $ {{price}}
  </div>
  <button (click)="toggleFavorite()" [disabled]="favorite">Add to Favorite</button>
</div>
```

事件連結，括號記號法指事件，等號左邊的部分是要連結的事件，右邊的部分指 Angular 應該在事件發生時執行的模板陳述。

有的時候我們也會注意實際觸發事件。這種情況下，Angular 可讓你透過 $event 這個特殊變數存取底層 DOM 事件。你可以存取它或傳遞給函式：

```html
<div class="stock-container">
  <div class="name">
    {{name + ' (' + code + ')'}}
  </div>
  <div class="price" [class]="positiveChange ? 'positive' : 'negative'">
    $ {{price}}
  </div>
  <button (click)="toggleFavorite($event)" [disabled]="favorite">Add to Favorite</button>
</div>
```

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-stock-item',
  templateUrl: './stock-item.component.html',
  styleUrls: ['./stock-item.component.scss']
})
export class StockItemComponent implements OnInit {

  public name: string;
  public code: string;
  public price: number;
  public previousPrice: number;
  public positiveChange: boolean;
  public favorite: boolean;

  constructor() { }

  ngOnInit() {
    this.name = 'Test Stock Company';
    this.code = 'TSC';
    this.price = 85;
    this.previousPrice = 80;
    this.positiveChange = this.price >= this.previousPrice;
    this.favorite = false;
  }

  toggleFavorite(event) {
    console.log('We are toggling the favorite state for this stock', event);
    this.favorite = !this.favorite;
  }

}
```

以類似方式，我們可以與其他標準 DOM 事件掛鉤，例如 focus、blur、submit 等。

### 使用模型做出更清楚的程式碼

這裡使用 TypeScript 方式封裝確保元件不會在低階抽象與屬性上運作，定義一個具有股票定義的介面或類別，並一致的用於應用程式中：

```shell
$ ng g class model/stock
```

```typescript
export class Stock {
  favorite = false;

  constructor(public name: string,
              public code: string,
              public price: number,
              public previousPrice: number) {
  }

  isPositiveChange(): boolean {
    return this.price >= this.previousPrice;
  }
}
```

這裡使用了 TypeScript 的縮寫語法，以根據建構元參數 public 關鍵字自動產生相對應的屬性。四個屬性來自建構元而一個是自動初始化的。

以下來看如何在元件中使用：

```typescript
import {Component, OnInit} from '@angular/core';
import {Stock} from '../../model/stock';

@Component({
  selector: 'app-stock-item',
  templateUrl: './stock-item.component.html',
  styleUrls: ['./stock-item.component.scss']
})
export class StockItemComponent implements OnInit {

  public stock: Stock;

  constructor() {
  }

  ngOnInit() {
    this.stock = new Stock('Test Stock Company', 'TSC', 85, 80);
  }

  toggleFavorite(event) {
    console.log('We are toggling the favorite state for this stock', event);
    this.stock.favorite = !this.stock.favorite;
  }

}
```

這將大幅簡化元件程式碼，並將邏輯與底層功能封裝在 TypeScript 的型別中。

```html
<div class="stock-container">
  <div class="name">
    {{stock.name + ' (' + stock.code + ')'}}
  </div>
  <div class="price" [class]="stock.isPositiveChange() ? 'positive' : 'negative'">
    $ {{stock.price}}
  </div>
  <button (click)="toggleFavorite($event)" [disabled]="stock.favorite">Add to Favorite</button>
</div>
```

class 屬性連結現在是指向函式而非變數。這是可接受的，Angular 會對函式求值並使用回傳值判斷最終運算值。

## 總結

這裡學習到如何啟動 Angular 應用程式，以及認識 Angular 應用程式骨架各個部分的需求與使用。

這裡也使用插值、屬性連結做資料連結的運作，還有事件連結來處理使用者互動，最後將資訊封裝在 TypeScript 的類別中，以確保程式碼的清晰與模組化。
