# 命名 Naming

命名在撰寫程式時，是工程師們覺得最困難的部份，當定下規則後，可讓成員們在觀看程式時，更能瞭解參數來源、方法功能，進而快速的閱讀程式碼。

文字選擇：
- 簡單易懂，工程師常用的名稱
- 可讀性為主，而非簡潔性
- 不使用 C# 關鍵字

每個語言都有自己的格式標準，下列限定為 C# 程式的規範：

## 大小寫格式 Casing

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


## 類型對照表

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

## 方法命名 Method Naming

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

## 方法參數及變數

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