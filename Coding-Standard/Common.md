# 常用寫法規則

## 原生類型

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

## `var` 使用時機
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

## `using` 的撰寫方式
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

## `??`運算子 使用時機

```ANSI
參數 ?? 預設值
```
判斷參數是否為空值，若非空值則回傳參數，當空值時則回傳預設值。
- 對象： 
    * Nullable `DateTime?`, `Int?`, `long?`
    * Generic `class`, `List`, `Dictionary`
- 避免： `string`, 因字串有可能空值(NULL)也有可能空字串，當需要兩者判別時，請使用 `string.IsNullOrEmpty` 判別。

> 字串避免使用 `??`，是因為開發人員容易錯判字串空值與空字串，且字串預設值為空字串，並不需要以此方法宣告預設值，故不推薦使用。

## `?.` 或 `?[]` 運算子 使用時機

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
