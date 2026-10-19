# XIVData Oxidizer(台服 fork)

Rust 工具，使用 [ironworks](https://github.com/ackwell/ironworks) 與
[EXDSchema](https://github.com/xivdev/EXDSchema) 把 FFXIV 遊戲資料匯出成 CSV 檔，
是 [SaintCoinach.Cmd](https://github.com/xivapi/SaintCoinach) `rawexd` 指令的精神後繼者。
Schema 以 git 子模組方式本機取得，不需等上游更新就能自行修改。

這份 fork 是艦隊台服 EXD dump（`exd-tc/<版本>/`）的產生工具，
本身**不是**任何插件的程式碼相依，而是離線資料產生管線。

## 台服 fork 的目的

原作者 mattantonelli 沒有台服支援，由社群開發者 CY（cycleapple）加上台服客戶端支援：
使用 `ChineseTraditionalTW`（語言代碼 8）匯出、輸出到 `tc` 目錄、搭配加了台服語言支援的
ironworks fork。我們自己在此基礎上修了兩個 EXDSchema 子模組的懸空/靜默截斷風險：

- **`EXDSchema` 改指向組織 fork，並把 gitlink 釘到正確版本**：原本記錄的 gitlink 不在
  `xivdev/EXDSchema` 任何存活分支的祖先鏈上（實質等同懸空），也不是台服需要的版本——
  跟 `latest` 走會靜默截斷 `Action` 等多張表。改釘 `ver/2025.07.30.0000.0000` 分支。
- **`.gitmodules` 的 `branch` 設定從 `latest` 改成同一個版本分支**：只釘對 gitlink 不夠，
  只要有人跑 `git submodule update --remote`（`extract.sh` 第一行就是）就會被拉回 `latest`，
  一樣靜默截斷同一批表；gitlink 與 branch 兩處都要對。

## 與上游的差異

台服支援本身是 CY 的 fork 既有的，我們只加了上述兩個子模組釘版修正。

## 使用方式

```
git clone --recurse-submodules <本 repo>
mkdir -p output/tc
cargo run -- "<台服遊戲安裝目錄>"
```

- 🔴 **要先建 `output/tc`**：程式不會自己建輸出目錄，沒建時每張表都寫檔失敗、被當成「略過」，
  最後印 `Completed: 0 sheets exported, … skipped`，結束碼仍是 0。
  `output/tc/.keep` 已納入版控（同 `de`/`en`/`fr`/`ja`），clone 下來就有這個目錄；
  但 dump 完若把整個 `output/tc` 搬走，目錄會跟著消失——搬檔案（`output/tc/*.csv`）不要搬目錄，
  跑完一律確認最後一行是 `0 skipped`。
- 只匯出頂層表；名稱含 `/` 的子目錄表（`quest/`、`custom/` 等）一律跳過。

## 誰在用它

不適用逐一列消費端——這是資料產生工具，輸出的 CSV（`exd-tc/<版本>/`）才是被整個艦隊
（loc 工具、sqpack 查表、各插件的台服資料校對）廣泛引用的東西。

---

原始碼：<https://github.com/mattantonelli/xiv-data-oxidizer>（台服支援來自：
<https://github.com/cycleapple/xiv-data-oxidizer>）

---

FINAL FANTASY is a registered trademark of Square Enix Holdings Co., Ltd.

FINAL FANTASY XIV © SQUARE ENIX CO., LTD.
