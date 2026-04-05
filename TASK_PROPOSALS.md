# Codebase Task Proposals

以下是針對目前程式碼庫（NachOS MP4 區塊）整理出的 4 項可執行修正任務，每項對應你要求的類別。

## 1) 拼字錯誤修正任務
**任務名稱**：統一修正核心註解中的 `occured` 拼字錯誤為 `occurred`。

**發現依據**：
- `HW4/NachOS-4.0_MP4/code/machine/machine.cc` 註解出現 `occured`。
- `HW4/NachOS-4.0_MP4/code/threads/switch.h` 註解出現 `occured`。

**建議作法**：
- 直接修正上述兩處拼字。
- 順帶用 `rg -n "occured" HW4/NachOS-4.0_MP4/code` 再掃一次，確保沒有遺漏。

**完成標準**：
- 程式碼庫中不再出現 `occured`（至少在 `code/` 目錄下）。

---

## 2) 錯誤修正任務（功能邏輯）
**任務名稱**：修正 `OpenFile0` 開檔失敗時仍回傳看似有效 FD 的問題。

**發現依據**：
- `OpenFile0` 會把 `Open(name)` 的結果直接送進 `PutFileDescriptor`。
- `PutFileDescriptor` 沒有檢查 `OpenFile*` 是否為 `NULL`，可能把 `NULL` 寫入 descriptor table 並回傳非 0 的索引。

**風險**：
- 呼叫端誤以為開檔成功，後續 `ReadFile0/WriteFile0` 才失敗，造成錯誤定位困難。

**建議作法**：
- 在 `OpenFile0` 中先檢查 `openPtr == NULL`，直接回傳失敗碼（例如 `0` 或 `-1`，以既有介面慣例為主）。
- 或在 `PutFileDescriptor` 中拒絕 `NULL` 指標並回傳失敗碼。
- 補上介面註解，明確定義失敗回傳值。

**完成標準**：
- 嘗試開啟不存在檔案時，不會回傳可用 FD。

---

## 3) 註解/文件一致性修正任務
**任務名稱**：修復 `filesys.h` 中 MP4 相關註解亂碼（mojibake）並統一中英描述。

**發現依據**：
- `HW4/NachOS-4.0_MP4/code/filesys/filesys.h` 中多處註解為無法閱讀的亂碼（如 `MP4���o��`、`MP4�s�W`）。

**影響**：
- 維護者無法理解 API 設計意圖。
- 造成實作與文件理解落差。

**建議作法**：
- 將亂碼註解替換為可讀文字（建議英文為主，必要時附中文）。
- 在 `CreateFile0/OpenFile0/ReadFile0/WriteFile0/CloseFile0` 補齊「輸入、輸出、失敗語意」。

**完成標準**：
- `filesys.h` 的 MP4 擴充註解可讀且與實作語意一致。

---

## 4) 測試改進任務
**任務名稱**：新增「開檔失敗路徑」與「FD 邊界條件」測試案例。

**動機**：
- 目前測試主要偏整體流程，對錯誤路徑（不存在檔案、FD 無效值、FD table 滿載）覆蓋不足。

**建議測試內容**：
1. `OpenFile0("not_exist")` 應回傳失敗碼。
2. 對失敗碼做 `ReadFile0/WriteFile0/CloseFile0` 應維持一致錯誤行為。
3. 連續開檔直到 FD table 滿，下一次開檔應失敗且不得覆蓋既有 FD。
4. 關閉後重開，FD 可被安全重用。

**可落地方式**：
- 在 `HW4/NachOS-4.0_MP4/code/test/` 新增一個專門驗證 syscall wrapper 的測試程式（例如 `FS_fd_edge_test.c`），並加入 `Makefile` 的 `PROGRAMS`。
- 或擴充 `FS_test2.c` 增加錯誤路徑檢查。

**完成標準**：
- 測試可重現舊問題並驗證修正後行為。
- 測試結果可在失敗時提供清楚錯誤訊息。
