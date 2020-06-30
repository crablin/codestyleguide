# C# 程式碼標準 Coding Standards

當團隊規範出共同標準，一則編寫程式時不用費時思考及找尋命名，二則團隊知悉同伴們撰寫的想法，繼而進行Code Review，規範即可增加生產力，也可協助新進人員更快進入狀況，也幫助資深人員更瞭解自己的專案程式。 

此設計規範將以微軟原生的 Code-Style 為主。

## 命名 Naming

命名在撰寫程式時，是工程師們覺得最困難的部份，當定下規則後，可讓成員們在觀看程式時，更能瞭解參數來源、方法功能，進而快速的閱讀程式碼。

文字選擇：
- 簡單易懂，工程師常用的名稱
- 可讀性為主，而非簡潔性
- 不使用 C# 關鍵字

每個語言都有自己的格式標準，下列限定為 C# 程式的規範：

### 大小寫格式 Casing

1. `Pascal Case` : 使用於 Class, Method, Public Property, const

```C#
// Class
public class ProductManager
{
    // Public Property
    public string Name { get; set; }

    // Method
    public void DoWork() { ... }
}
```

> 微軟定義常數 `const` 也使用 `Pascal` 來命名，並非引用 `UPPER_CASE` 的方式命名。

2. `camelCase` : 使用於方法內的變數及方法參數

```C#
public void DoWork(string executorName)
{
    bool flag = false;
    int tryLoopCount = 3;
}
```

3. `_camelCase` : Class 內的私域變數

```C#
public class CheckoutManager
{
    private IProductDao _productDao;
    private ICampaignDao _campaignDao;

    private string _exceptionFormat = "ERROR:{0}, source by {1}, {2}";
}
```

為何使用底線做為私域變數？

因為當方法同時有同樣性質的參數傳入，需運算或寫回該欄位時，如以下狀況：

```C#
public class Person
{
    private string _displayName;

    public void SayHello(string displayName = null)
    {
        Console.WriteLine（$"Hello，{displayName ?? displayName}！"）;
    }
}
```

4. `Pascel_snake_case` : 單元測試時使用的函數命名

```C#
public class CheckoutManagerTests
{
    [SetUp]
    public void Checkout_Init()
    {...}

    public void Checkout_single_product() { ... }
    public void Checkout_multiple_product() { ... }
    public void Checkout_product_with_campaign_should_be_onSale() { ... }
    public void Checkout_product_without_campaign_() { ... }
}
```

5. `Hungarian Notation 匈牙利命名法` : 視窗程式物件、頁面程式物件

早期的 C# 程式、視窗程式以及其他程式會以匈牙利命名法來前置，如 `sDisplayName` 或是 `tbPerson`以前置字串來說明該參數的格式，C# 程式已無此命名習慣，目前僅舊有的視窗及頁面程式物件會以 `txtDisplayName` 及 `btnSubmit` 使用，請在該情境方可使用此命名方式。

6. 例外 `Boolean` 

布林值的變數或方法皆回傳 `ture`/`false`，通常會是交給 `if`/`else` 判斷，並選擇執行的流程，所以參數命名上，建議使用下列文字開頭：

- `is`~ ： isSuccess, isOpened, isClosed, isValid, isExisted, isCompleted
- `has`~ : hasValue, hasContent, hasText
- `can`~ : canOpen, canClose, canCheck
- `visible`~ : visibleSetting, visiblePage

7. 例外 `IPascalCase` Interface 前置名稱

建置 Interface 名稱時，都會使用 `I`~ 當作前置符號

8. 例外 `TPascalCase` Generic 前置名稱

使用泛型定義時，請使用都會使用 `T`~, `U`~, `V`~ 當作前置符號。

> 項目 *7* 與 *8*，兩個規則都類似匈牙利命名法。
主要是微軟在 C# 初始 Guildline 時，就長久引用了這個規則，所以在後續的程式開發就繼續延用該作法。


### 類型對照表

| 類型 | 規則 |
|------|------|
| Namspace | PascalCase |
| Class | PascalCase |
| Const variable | PascalCase |
| Private Field | _camelCase |
| Public Field | PascalCase |
| Protected Field | PascalCase |
| Internal Field | PascalCase |
| Property | PascalCase |
| Method | PascalCase |
| Interface | IPascalCase |
| Local variable | camelCase |
| Method Parameter | camelCase |
| Test Method | Pascel_snake_case |
| Page Element | Hungarian Notation |


### 類型命名 Class Naming

- 應以名詞或名詞片語命名
```C#
public class Employee { /* Value Object */}
public class InvoiceEntity { /* Business Logic Entity */ }
public class CheckoutProcess { /* Domain main Flow process */ }
```

### 方法命名 Method Naming

方法 `Method` 基於該物件 `Class` 的動作，所以在命名上應一致於下列的規範：

| 方法名稱 | 動作 | 對象 |目的或參數 | 說明 |
| --- | --- | --- | --- | --- |
| GetProduct | Get | Product | | 取得商品 |
| GetProductsByStatus | Get | Products | ByStatus | 依商品狀態取得商品清單 |
| GetProductsByName | Get | Products | ByName | 依商品名稱取得商品清單 |
| AddProduct | Add | Product | | 新增商品|
| ModifyProductForReport | Modify | Product | ForReport | 更新商品報表資訊 |
| RemoveProduct | Remove | Product | | 刪除商品 |
| DoWork | Do | Work | | 執行工作 |
| SendMessage | Send | Message | | 寄送訊息 |

1. 動作 : 限定動詞，動詞建使用程式常用的動詞為主
- Select, Create, Insert, Update, Delete (資料庫常用)
- Get, Add, Modify, Remove (替代資料庫取用、功能使用)
- Do, Run, Execute (工作執行)
- Get, Set, Filter, Convert, Vaild (參數轉換、設定、驗證)
- Receive, Send (非內部程式處理的接收及發送，例如: Queue or Mail)
- Get, Post, Put, Delete (有呼叫API相關功能使用)
- Valid, Has, Is(驗證及回傳布林值判斷)

2. 對象(選填) : 限定名詞，主要是動詞後的受詞或執行的對象
- Get`Product`: 取得商品，但是如果類別已強烈說明處理的對象，即可省略對象。
```C#
public class ProductAdapter
{
    // Good
    public Product Get() { ... }
    // Bad
    public Product GetProduct() { ... }
}
```
- 若回傳值為 `IEnumerable` 或 `List` 等，請使用複數名詞，如 `Product` 則使用 `Products`。若類別強烈說明處理對象，則可使用 `List` 做為對象名詞。
```C#
public class ProductAdapter
{
    // Good
    public Product GetProducts() { ... }
    // Better !
    public Product GetList() { ... }
}
```

3. 目的或參數 : 分辨函式為何執行，或多項條件、參數時以供辦別該函式運行的程式
- 目的：用 For, With, Without 做為介系詞
- 目的(事件)：用 After, Before 做為事件的介系詞
- 參數：用 By 做為參數的介系詞
```C#
public class ProductDto
{
    public Product GetById(int Id) { ... }
    public Product GetForReport(DateTime start, DateTime end) { ... }
}
```
介系詞後大多接
- 目的：~For`Report`, ~For`Search`, ~For`Exoprt`, ~For`Query`
- 目的(事件)：~After`ExportException`, ~Before`UpdatedStatus`
- 參數：~By`Id`, ~By`NameAndStatus`，也可以使用 ~By`Condition`

### 變數命名

- 除了 `ValueObject` 內的欄位值變數盡可能與該變數的物件為命名方式

```C#
HtmlHelper htmlHelper;
FtpTransfer ftpTransfer;
UIControl uiControl;
```

- 避免簡稱

```C#
// Good
UserGroup userGroup;
// Avoid
UserGroup usrGrp;
```

- 若變數更清楚來源及目的，可以加入前置名詞，並避免模擬兩可簡稱

```C#
// Good
Assignment employeeAssignment;
Assignment managerAssignment;
// Avoid
Assignment empAssignment; // employee or employer ?

// Good
IOrderRepository orderRepository;
// Acceptable
IOrderRepository orderRepo;
```

### 方法參數及變數

- 方法參數同變數命名，以變數的物件為命名方式
```C#
public class OrderDao
{
    public bool Save(OrderDto order, List<OrderItemDto> orderItems) {...}
}
```
- 方法內的私域變數，也單純可用來源及結果，可使用 `source` 及 `result` 當作變數命名。
```C#
public class OrderManager
{
    public CheckoutDto Checkout(UserDto user, List<OrderItemDto> orderItems)
    {

        // variable is order or source
        OrderDto source = GetOrderDto(user, orderItems);
        // variable is checkout or result
        CheckoutDto result = new CheckoutDto(source);

        result.Discount = GetDiscountByCampaign(source);
        result.Coupons = GetCoupons(user);
        
        return result;
    }
} 
```

## 常用寫法規則

### 原生類型

使用預定義的類型名稱，而不是系統類型名稱，例如`Int16`，`String`，`UInt64`等
```C#
// Good
string firstName;
int lastIndex;
bool isSaved;
 
// Avoid
String firstName;
Int32 lastIndex;
Boolean isSaved;
```

### `var` 使用時機
- 方法內的私域變數
- 當變數已確認 assign 的物件類型為何，一律可以使用 `var`。
```C#
// Good
var person = new Person(id);
var orders = _orderRepo.GetList(startTime, endTime);

// Bad
var name = 0; // string or int ?
var obj = new Person(id); // obj is person ?
```
- 變數的命名已可瞭解此變數是用在什麼功能或流程上
```C#
// Good
var isValidOrders = false;
var isValid = false;

// Avoid
var valid = false; // valid what ? better use isValid 
```
- 消除混亂，當有複雜類型時，讓程式碼可讀性提高
```C#
// Acceptable
MemoryStream stream = File.Create(path);
Dictionary<string, Product>() productMap = new Dictionary<string, Product>();

// Better
var stream = File.Create(path);
var productMap = new Dictionary<string, Product>();
```

### `using` 的撰寫方式
```C#
// Original
using(var connection = _connectionFactory.Get())
{
    using(var stream = connection.OpenStream())
    {
        // use the connection and the stream
    }
}

// Better, clear coding
using(var connection = _connectionFactory.Get())
using(var stream = connection.OpenStream())
{
    // use the connection and the stream
}
```

### `??`運算子 使用時機

```ANSI
參數 ?? 預設值
```
判斷參數是否為空值，若非空值則回傳參數，當空值時則回傳預設值。
- 對象： 
    * Nullable `DateTime?`, `Int?`, `long?`
    * Generic `class`, `List`, `Dictionary`
- 避免： `string`, 因字串有可能空值(NULL)也有可能空字串，當需要兩者判別時，請使用 `string.IsNullOrEmpty` 判別。

> 字串避免使用 `??`，是因為開發人員容易錯判字串空值與空字串，且字串預設值為空字串，並不需要以此方法宣告預設值，故不推薦使用。

### `?.` 或 `?[]` 運算子 使用時機

```ANSI
物件?.函式方法
```
判別物件是否為空值，當非空值則執行函式方法，當空值時則回傳空值(NULL)

```ANSI
物件?.[陣列索引鍵]
```
判別物件是否為空值，當非空值則可運作陣列功能，當空值時則回傳空值(NULL)

- 當物件有可能為空值時，但不希望增加太多判斷，導致程式閱讀困難，則可使用此運算子。
- 此運算值也可與 `??` 混合使用，可減少 `if`/`else` 的數量

```C#
var numbers = new [] { 1, 3, 5 };

if ((numbers?.Length ?? 0) < 2)
{
    // Do something.
}
```

> Null 條件成員存取運算子 `?.`, `?[]` 也被稱為 `Elvis` 運算子。

## 註解

註解對於所有成員互相瞭解對方的程式有很重大的意思，故撰寫**正確**及**適時**的註解是很重要的。

### Value Object(VO) 的註解

- `VO` 與資料表的欄位相關，若每個欄位皆有註解，可讓程式閱讀能更快速瞭解該資料的運作
- 欄位為列舉時，僅註解說明即可，若為字串則可說明項目

```C#
/// <summary> 訂單狀態 </summary>
public OrderStatus Status { get; set; }

/// <summary> 
/// 訂單狀態 
/// 0 : Unprocess,
/// 1 : Perpare,
/// 2 : Shipping,
/// 3 : Finish
/// </summary>
public string Status { get; set; }

// Located in OrderStatus.cs
public enum OrderStatus
{
    Unprocess,
    Perpare,
    Shipping,
    Finish
}
```

### Method 註解

- [必填] 說明註解使用方法：`<summary>...</summary>`
- [必填] 參數說明：`<param>...</param>`

```C#
/// <summary> 
/// 執行結帳，並儲存結帳結果
/// </summary>
/// <param name="user">購買者資訊</param>
/// <param name="orderItems">購買商品資訊</param>
public bool Checkout(UserDto user, List<OrderItemDto> orderItems)
{
    var checkout = PerpareCheckoutDto(user, orderItems);

    checkout.Discount = GetDiscount(checkout);
    checkout.Coupons = GetCoupons(checkout);

    var result = _checkoutAdatper.Save(checkout);

    return result;
}
```
- [建議] 若有商業邏輯或流程的，也可說明流程即結果
- [建議] 若回傳值無法從方法名稱確認回傳結果為何，即需要註解：`<returns>...</returns>`
- [建議] 如果該方法會拋出錯誤時，則可註解為何產生此錯誤：`<exception>...</exception>`

```C#
/// <summary> 
/// 執行結帳，並儲存結帳結果
/// 1. 取得結帳資訊
/// 2. 依結帳內容取得是否有折價金額
/// 3. 依結帳內容取得是否有可用的折價券
/// 4. 寫入資料庫並回傳是否成功儲存結帳資訊
/// </summary>
/// <param name="user">購買者資訊</param>
/// <param name="orderItems">購買商品資訊</param>
/// <returns>是否成功寫入資料庫</returns>
/// <exception cref="ArgumentNullException">
/// 當 <paramref name="user"/> 或 <paramref name="orderItems"/> 為空值
/// </exception>
public bool Checkout(UserDto user, List<OrderItemDto> orderItems)
{ ... }
```

### 工作清單 TODO, UNDONE, HACK

- TODO: 當有預計要執行的項目
- UNDONE: 尚未完成的項目，希望後續接手的人可以知道的說明
- HACK: 有資安或效能議題的說明

參考網址 - [MSDN 使用者清單](https://docs.microsoft.com/zh-tw/visualstudio/ide/using-the-task-list?view=vs-2019)

## 專案檔案

### Domain 資料夾結構
``` ANSI
Project
+-- Dal         # 資料存取
+-- Entities    # 依服務區分 - 工廠模式 / 執行工作模組
+-- Interfaces  # 對外使用的介面模組
+-- Services    # 依服務區分 - Process / Algorithm(Rule, Helper, Generator, Creator)

### 基礎程式

- 基底檔案的命名方式：
    * GOOD : ~`Base`.cs，命名規則同於微軟基底程式，如 `ControllerBase`
    * AVOID : `Base`~.cs

- 達到檔案閱讀方便，將繼承的基礎程式放至同目錄夾的 `_base` 之前
``` ANSI
.
+-- _base (NEW)
|   +-- CancelEntityBase.cs (MOVE)
|   +-- RegistEntityBase.cs (MOVE)
|   +-- TransactionEntityBase.cs (MOVE)
+-- BankTransactionEntity.cs
+-- RoCancelEntity.cs
+-- RoRegistEntity.cs
+-- RpCancelEntity.cs
+-- RpRegistEntity.cs

```
- `_`~ 開頭的資料夾，等同是該資料夾下的私域檔案，Namespace 與同於 `_base` 同階層的`cs`

### Interface 檔案分佈

- 專案對外的 `Interface` 皆放在根目錄的資料夾下

``` ANSI
Project
+-- Dal
+-- Interface
|   +-- ICancelProcess.cs
|   +-- IRegistProcess.cs
+-- Services
|   +-- CancelService.cs
|   +-- RegistProcess.cs
```

- 專案內部使用的 `Interface`，則將 Implements 的 class 放到該資料夾內
``` ANSI
Project
+-- Dal
|   +-- Implements
|   |   +-- AccountDal.cs
|   |   +-- PaymentDal.cs
|   +-- IAccountDal.cs
|   +-- IPaymentDal.cs
+-- Interface ...
+-- Services ...

```

### 分割型類別

當類別為 `partial` 為前置宣告時，檔案應放在同一個目錄下，檔案名稱應該可以瞭解類別的相依性
```C#
// Located in DbHelper.cs
public partial class DbHelper
{
    // Main method or Common methods
}

// Located in DbHelper.Account.cs
public partial class DbHelper
{
    // About Accout methods
}

// Located in DbHelper.Product.cs
public partial class DbHelper
{
    // About Product methods
}
```