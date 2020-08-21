# 專案檔案

## Domain 資料夾結構
``` ANSI
Project
+-- Dal         # 資料存取
+-- Entities    # 依服務區分 - 工廠模式 / 執行工作模組
+-- Interfaces  # 對外使用的介面模組
+-- Services    # 依服務區分 - Process / Algorithm(Rule, Helper, Generator, Creator)
```

## 基礎程式

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

## Interface 檔案分佈

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

## 分割型類別

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