# 領域模型層

專案架構：

```ANSI
[微服務名稱].Domain
+-- Interfaces
+-- Services
+-- Factories
+-- Entities
+-- Dal
+-- Utility
+-- Configs
```

## 邏輯層分類

- **Service** : Domain 流程及邏輯運作
- **Factories** : 依指定參數取得特定的 Entity 物件
  - 僅依滿血模式或貧血模式產生 Entity，不應有邏輯、資料庫寫入、流程
  - 資料庫讀取或第三方資訊讀取，達到滿血模式
- **Entities** : 分為兩種 Entity 物件，分為下列兩種實體
  - 工廠實體(流程)：可能因為付款方式、銀行、縣市、廠商，而分成各種不同實體，可使用 DI 存取資料庫、產生檔案、第三方元件、發送郵件、寫資料至 RabbitMQ
  - 域實體(領域邏輯)：單純依函數執行處理資料，不可存取資料庫，僅依函式執行將實體內的資料新增或修改
    **Entities/Algorithm** : 對第三方物件的特殊邏輯處理
    **Entities/Rule** : 讀取資料後同時資料驗證後傳出
- **Dal** : 讀取資料庫端的邏輯，程式內組合 Repository 內取得及多表格寫入`TransactionScope`當流程大量寫入時發生錯誤，可全部 Rollback
- **Utility**：寄送郵件、檔案讀取寫入、傳送資料至 RabbitMQ
- **Configs**：將取得 Configuration 內的資料從字串改成 Property Get

## 領域服務流程 Services

流程檔名以 ~`Process`，命名規則為 `對象名詞+動詞`Process，例如： **AccountSync**Process、**ReceiptApply**Process

- DO
  - 可直接存取 ~`Dao`，存取資料庫
  - 可透過 ~`Factory` 產生工廠實體 ~`Entity`
  - 可直接建立域實體，並使用實體函式

- DO NOT
  - 不可直接建立工廠實體，需透過 Factory
  - 不可直接存取 Repository，需透過 Dao

> Service 內容的 Process 的 Interface 是對外的，所以一律放在 Interface 的資料夾下。

## 工廠模式 Factory-Entity

工廠模式產生的 Entity 為領域模型。
工廠檔名以 ~`Factory`，命名規則為 `對象名詞+動詞`Factory；也可使用`對象名詞`Factory，例如： **Account**Factory、**Receipt**Factory、**ReceiptApply**Factory

- DO
  - 可使用 ~`Dao` 讀取資料庫，準備領域模型需要的資料清單
  - 可切割資料清單，產生領域模型
  - 需要使用到相依注入需透過 `IServiceProvider` 產生模組
  - 工廠公用函式規則僅能以`Create`~ 開頭
  - 工廠公用函式回傳一律為領域模型(Aggregate Root or Entity)

- DO NOT
  - 撰寫邏輯及流程
  

> 工廠函式規則僅能以`Create`~ 開頭，名稱為`CreateEntity` 或 `Create`+**類型**+`Entity`，當回傳複數請使用 `CreateEntities` 或 `Create`+**類型**+`Entities`，例如： Create**Entity**、Create**Entities**、Create**ApplyEntities**

>當 Aggregate Root 根領域模型需要使用到相依注入的方式建構模組，所以需使用 `IServiceProvider` 透過 Autofac 產生模組。並在模組建立後，以充血的方式將資料設定至模組，並回傳給 Process 執行。

### 撰寫方式
```C#
// code on Factory
using (var scope = _serviceProvider.CreateScope())
{
    // 透過 Autofac 建置根領域模型
    var fetcEntity = scope.ServiceProvider.GetService<FETCReceiptApplyEntity>();
    // 充血模式
    fetcEntity.ServiceId = serviceId;
    fetcEntity.Identifier = source.Identitifier;
    fetcEntity.TransactionEntity = trans;
    fetcEntity.MailInfo.ServiceIdChiTrans = serviceName;        

    return fetcEntity;
}

// code on Entity(Aggregate Root)
public class FETCReceiptApplyEntity : ReceiptApplyEntityBase
{
    public FETCReceiptApplyEntity(IReceiptApplyDao receiptDao, IEpkAccDao epkAccDao, IAsyncSender asyncSender) 
        : base(receiptDao, epkAccDao, asyncSender)
    {
        
    }
}
```

- 因領域模型不是實作 Interface 的方式撰寫，所以需要在 `Startup.cs` 設定 `Autofac` 可以在建構式建置非 Interface 的物件

```C#
// Startup.cs

public IServiceProvider ConfigureServices(IServiceCollection services)
{
    //... 以上程式略
    var domains = Assembly.Load("[微服務名稱].Domain");

    builder.RegisterAssemblyTypes(domains).AsImplementedInterfaces();
    // AsSelf 這個函式即可在相依注入時建置非Inferface的領域模組
    builder.RegisterAssemblyTypes(domains).AsSelf();
}
```

### EntityBase

當領域模型中有可能會有因**地區**、**銀行**、**廠商**、**付款方式**，有同樣的處理流程，但有不一樣的處理邏輯，會使用一個底層領域模型去定義他有那些功能需實作或有被覆寫。

> 在工廠模式產出時，會以傳入的資料用switch case 去判斷應產出的領域模型是那一個。

目前專案內有用到範例如下：

- 開立發票
  - FETC
  - UParkingPay
- 縣市路邊停車
  - 台北市
  - 新北市
  - 高雄市

底層領域模型請使用 `abstract class`。

函式的宣告，請使用`virtual`實作基礎邏輯程式在底層，當繼承該底層的模型可以覆寫函式。例如：**開立發票**

當函式為流程內的一環，繼承的領域模型裡邏輯、流程都不同，則請使用`abstract`讓其模型必定實作該函式。例如：**縣市路邊停車**

## 領域模型 Entity

在DDD的架構中，又將領域模型分成： `根`領域模型、`子`領域模型
當根領域模型邏輯過於繁雜時，應將模型再切分為小型的領域模型，一方面易於模組資料測試，一方面也能更聚焦特定的邏輯。

開立發票模型範例
- 原模型流程：
  * 取字軌設定(GetTrackSetting)
  * 驗證字軌是否符合開立日期
  * 鎖定字軌
  * 檢查字軌數量與交易數量，不足時取得新字軌(GetMoreTrack)
  * 依字軌設定產生發票配號物件(GetTrack)
  * 從交易(Transaction)配號後產生發票內容(ReceiptDetail)
  * 儲存開立結果
  * 解鎖字軌
- 新模型流程：
  * 根領域：
    - 檢查字軌不足，取得新字軌
    - 開立發票流程
    - 儲存開立結果
  * 交易領域模型：
    - 交易數量
    - 開立發票
  * 字軌領域模型：
    - 字軌數量
    - 驗證字軌是否符合開立日期
    - 取得新發票號碼
    - 鎖定字軌
    - 解鎖字軌
  
因根領域模型方可存取資料庫及第三方資訊，所以流程內的存取資料邏輯皆在根領域模型，再將資訊交付給子領域模型去處理資料。

## 資料存取層 Dal

原本僅使用 `QueryableDal` 及 `WritableDal` 存取資料庫。

程式碼會依服務的需求而越變越大，所以改為共用及功能項的Dal。
以發票專案來說，開立發票、作廢發票為功能項，但裡面也會有讀取會員及付款方式的需求，所以調整的方式如下：
- EpkAccDao：共用功能，讀取 EpkAcc 相關的組件，EpkAccAccount, EpkAccAccountTx
- EpkMailDao：共用功能，讀取 EpkMail 相關組件，取得發送的電子郵件清單
- ReceiptApplyDao：開立功能，讀取發票相關資訊，最後寫入開立後的資料及設定值
- ReceiptQueryDao：查詢功能，讀取發票相關資訊，但與開立時條件不同

以上述調整，更能知悉領域模型用到的存取物件。

## Utility / Common

- 發送至第三方軟體功能，如：RabbitMQ Sender, Email Sender, SMS Sender
- 擴展功能，包含：LinqExtensions, StringExtensions, ObjectExtensions, EnumExtensions
- 檔案功能，包含：FileSystemHandler

## 設定值 Configs

將原本以字串方式讀取 AppConfig 的方式，改為物件的方式讀取。

``` C#
public class MailConfiguration : IMailConfiguration
{
    private IConfiguration _config;

    public MailConfiguration(IConfiguration config)
    {
        _config = config;
    }

    /// <summary>
    /// 寄送者(EPK 主郵件)
    /// </summary>
    public string MailFrom => _config["Mail:From"];

    /// <summary>
    /// 發票發送成功後的連絡人
    /// </summary>
    public string ReceiptAppliedKey => _config["Mail:ReceiptApplied"];

    /// <summary>
    /// 郵件 SMTP
    /// </summary>
    public string SmtpHost => _config["Mail:SmtpHost"];
}
```