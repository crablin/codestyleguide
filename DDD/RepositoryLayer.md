# 基礎架構層 (Repository)

- 專案內會使用的資料表(Table)都會有一個 Repository
- 當有複合表格時的以主表在該 Repository 額外增加接口(method)，提供 Domain 層接取
- 此架構專案僅存取資料庫，不做商業流程、邏輯的事情

專案架構：

``` ANSI
[微服務名稱].Repository
+-- Interfaces
|   +-- IEpkAccAccountRepository.cs
|   +-- IEpkAccAccountTxRepository.cs
+-- Repositories
    +-- _base
    |   +-- RepositoryBase.cs
    +-- EpkAccAccountRepository.cs
    +-- EpkAccAccountTxRepository.cs
```

## 資料存取底層 RepositoryBase.cs

- 簡易的CRUD，減少 Repository 的撰寫，不用在每個 `Repository` 重覆撰寫
- 實踐 `EntityFramework.Plus` 大量寫入及大量更新
- 當使用第三方元件發生錯誤時，寫入紀錄並回到原生方式寫入及更新

``` C#
// implement base
public class RepositoryBase<T> where T : class
{
    // CRUD implement
}

// interface of AccAccount with other query
public interface IEpkAccAccountTxRepository
{
    IEnumerable<AccAccountTx> Find(Expression<Func<T, bool>> expression);
    int UpdateStatus(IEnumerable<AccAccountTx> entity);
}

// implement Repository
public class EpkAccAccountTxRepository : RepositoryBase<AccAccountTx>, IEpkAccAccountTxRepository
{
    public IEnumerable<AccAccountTx> Find(Expression<Func<T, bool>> expression)
    {
      return Get(expression);
    }

    public int UpdateStatus(IEnumerable<AccAccountTx> entity)
    {
        return Modify(entity, options =>
        {
            options.ColumnInputExpression = a => new { a.DebtResult };
        });
    }
}
```
* 對於程式使用上，皆以 Interface 交付給 Domain Project 使用，其他未公開的接口並不會公開出去給 Domain 使用。
* _base 資料夾是方便將該 namesapce 下的檔案做一個簡易的分類，該namespace 還是同樣為 `Account.Repository.Repositories`

## 存取底層 RepositoryBase 與 ~Repository 函數命名對應

| ~Repository | RepositoryBase | 說明 | 延伸 |
| --- | --- | --- | --- |
| Find | Get | 依搜尋條件對資料庫查詢，回傳 IEnumerable 清單物件 | |
| FindOne | SingleOrDefault, FirstOrDefault | 依搜尋條件對資料庫查詢，但回傳單筆或第一筆 ||
| Create | Add | 新增資料或清單 ||
| Update | Modify | 更新資料或清單，或者依條件更新資料庫內容 | UpdateStatus, UpdateBySeq |
| Save | Merge | 新增或更新資料 | SaveBySeq, SaveForReport |
| Delete | Remove | 刪除資料或依搜尋條件 ||

## EntityFramework.Plus 應用

透過 `RepositoryBase` 的 `Action<BulkOperation<T>>` 參數，可以限制對資料庫的搜尋主鍵及更新條件，批次更新筆數的常用功能：

| Method | 功能說明 | 未設定(預設) |
| --- | --- | --- |
| ColumnPrimaryKeyExpression | 自定義搜尋條件 | 資料表的主鍵 | 
| ColumnInputExpression | 自定義更新欄位 | 全部欄位 |
| BatchSize | 批次更新大小 |  10,000 |


```C#
public int Update(List<EpkAccAccountTx> entity) 
{
  return Modify(entity, options =>
  {
      // 限定搜尋條件僅有 EodSeq
      options.ColumnPrimaryKeyExpression = a => a.EodSeq;
      // 僅更新 ReceiptSn 這個欄位
      options.ColumnInputExpression = a => new { a.ReceiptSn };
  });
}
```
