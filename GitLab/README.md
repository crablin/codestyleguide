# Git DevOps 規範

## 基礎分支

```
- sit     # 開發人員測試分支
- uat     # 對外廠商及內部人員測試分支
- master  # Prod，已上線服務的分支
```

僅有 sit  有設定自動建置及自動佈署，uat 及 master 僅自動建置並上傳至公用建置區

## **需求**或**重大錯誤**分支

當有新的需求開發時，請從 sit 或 uat 開出分支。
分支名稱請以 `featrue/` 開頭

當有重大的錯誤需修改Prod 線上的程式碼。
分支稱請以 `hotfix/` 開頭

`feature/` `hotfix/` 會在 git repository web 或 sourcetree 呈現為資料夾

## 分支名稱

除了主要的基礎分支外，命名規則為

| 分支 | 前置詞 | 說明 | 備註 |
| ---- | ---- | ---- | ---- |
| feature | topic-* , issue-* | 新需求開發 | 常用 |
| feature | refator-* | 重構調整 | 常用 |
| feature | bug-* | 錯誤修改 | 常用、非緊急 |
| hotfix | issue-* | 線上邏輯調整 | 非必要 |
| hotfix | refator-* | 線上重構調整 | 非必要 |
| hotfix | bug-* | 線上錯誤修改 | 緊急更新 |

分支名稱請使用 {prefix}-`Pascal Naming`，若名稱需要區分，請使用 `_` 切割名稱
ex. `issue-RetryFtpConnect` or `issue-Retry_FtpConnect`
ex.  `bugfix-UParkingUploadFile` or `hotfix-UParkingUploadFile`