---
name: ig-short-video-marketing-editor
description: >-
  IG 短影片行銷自動剪輯。當 Rick 說「剪短影片專用 / IG短影片行銷 / 自動剪輯 / Reels / Shorts /
  Podcast 訪談風格 / 純訪談黑底字幕 / 主持人受訪者分開 / 字幕不能漏 /
  直式拍螢幕預告 / 手機拍螢幕 / 課程預告 / 會員公告 / PalmierPro x Codex /
  要看到音軌字幕軌 / 打開 PalmierPro 專案給我看 / 不要CLI版本」時觸發。這是唯一的 9:16 短影音剪輯入口；
  內部分支為「Podcast 訪談風格」與「直式拍螢幕預告風」。必須輸出可檢查的 PalmierPro 專案，
  強制打開 PalmierPro，讓 Rick 看見字幕素材/字幕軌、音訊 waveform 與剪輯分段在軌道上，不能只跑 CLI/headless 成片。
---

# IG 短影片行銷自動剪輯

這是 Rick 的 9:16 IG / Reels / Shorts 短影音剪輯總入口。

之後短影音只記這一個入口；不同短影音風格在本 skill 內部分支處理。

## 兩個內部分支

### A. Podcast 訪談風格

用在純訪談、Podcast 感、沒有露臉、沒有主畫面，重點是主持人與受訪者的對話。

關鍵詞：

- Podcast 訪談風格
- 純訪談 Reels
- Podcast 訪談
- 黑底高級字幕
- 主持人受訪者分開
- Rick 跟受訪者不要混

核心規則：

- 先完整轉錄與讀字幕，再剪節奏。
- 必須分清楚 speaker。Rick 固定 `RICK BOWEN / HOST`，受訪者用真名，例如 `受訪者姓名 / GUEST`。
- 有聲音就要有字幕，不可自行省略。
- Rick 的話不能跑到受訪者名下，受訪者的話不能跑到 Rick 名下。
- 自動 speaker 不穩時，要做 review UI，不能只靠聲波硬猜。
- Review UI 要 autosave、自動播放下一段、保留 scroll position，可用快捷鍵 `1=Rick`、`2=受訪者`、`Space=播放`、`N=下一段`。
- 9:16 黑底高級訪談視覺，字體統一 `PingFang TC`。
- Rick 用金色，受訪者用青綠色，字幕斷句要像人看得懂。

### B. 直式拍螢幕預告風

用在手機直拍螢幕、手指指畫面、課程預告、會員公告、展示 Canva / PalmierPro / GitHub / Codex / Claude Code。

關鍵詞：

- 直式拍螢幕預告
- 手機拍螢幕
- 課程預告短片
- 會員公告
- 週末課
- 展示新 skill
- PalmierPro x Codex

核心規則：

- 保留手機直拍真實感，不要硬做成假廣告。
- 刪掉找鏡頭、口誤、重複、結尾雜句。
- 字幕用繁體中文，修正 Whisper 誤字。
- 字幕不要用大黑底框；預設白字加細陰影 / 描邊。
- 上方 POV / 課程標籤必須在 IG 安全區內。
- 片長通常 20-60 秒，重點是 hook、賣點、CTA。

## 強制 PalmierPro 驗收規則

這是硬規則。不能只跑 CLI / headless / 自動渲染後丟一個 mp4 路徑。

每次執行都必須做出可檢查的剪輯結果：

- 必須輸出 `.palmier` 專案，並打開 PalmierPro 給 Rick 看。
- 必須讓 V 軌、A 軌、字幕素材/字幕軌、overlay、分段素材能被檢查。
- A 軌必須看得到 waveform；字幕不能只燒進 mp4，必須有可辨識的字幕素材、字幕段或 overlay 檔在軌道上。
- V1 / A1 不能是一整條看不出剪輯；剪過的片段、字幕段、CTA、封面/標籤都要在 timeline 上可檢查。
- 本地 preview/review 頁只能當補充，不能取代 PalmierPro 驗收。若 PalmierPro 無法開啟，先回報原因並等待 Rick 確認，不可假裝已完成。
- 不可只回報「已輸出」或「CLI 已完成」。

標準輸出資料夾至少包含：

```text
project_or_preview/
├── final.mp4
├── project.palmier
├── transcript.json
├── captions.srt 或 captions.ass
├── segments.csv
├── audio_check.txt
└── preview.html
```

## IG 安全區

1080x1920 保守安全區：

```text
SAFE_LEFT = 92
SAFE_RIGHT = 988
SAFE_TOP = 170
SAFE_BOTTOM = 1640
```

規則：

- 上方標題 / POV / 人名不可貼頂，起點至少 `y >= 180`。
- 重要文字左右收在 `x=92..988`。
- 右側避免重要文字，留給 Reels 按鈕。
- 底部避免重要文字，留給帳號、文案、音訊資訊。
- 若 Rick 提供新的安全區圖，以圖為準。

## 音訊與字幕檢查

輸出前必查：

- `ffprobe` 確認有 audio stream。
- 抽查 0 秒、中段、尾段，字幕都有出現。
- 聽一小段確認聲音沒有中斷。
- Podcast 訪談風格要抽查 speaker 切換點。
- 直式拍螢幕預告風要抽查字幕沒有黑底、沒有蓋住手指或畫面重點。

## 交付格式

完成後回報：

- 使用分支：`Podcast 訪談風格` 或 `直式拍螢幕預告風`
- 成片路徑
- PalmierPro 專案路徑
- 已打開的 PalmierPro 專案名稱
- 字幕素材/字幕軌與 A 軌 waveform 在哪一條軌道
- 音訊、字幕、安全區檢查結果
