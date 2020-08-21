# 註解

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

## Method 註解

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

## 工作清單 TODO, UNDONE, HACK

- TODO: 當有預計要執行的項目
- UNDONE: 尚未完成的項目，希望後續接手的人可以知道的說明
- HACK: 有資安或效能議題的說明

參考網址 - [MSDN 使用者清單](https://docs.microsoft.com/zh-tw/visualstudio/ide/using-the-task-list?view=vs-2019)