---
name: youtube-longform-project-finisher
description: >-
  YouTube 長片專案精修版。當 Rick 說「YouTube長片 / U2長片 / 16:9長片 / 長片專案精修 /
  課程長片 / 訪談長片 / 幫我整理長片專案 / 長片字幕 / 長片音軌 / 長片剪輯軌道 /
  PalmierPro 長片 / 打開專案給我看」時觸發。用於 16:9 YouTube / 課程 / 訪談長片的專案精修、
  字幕音訊軌道整理、章節與包裝。不是 9:16 IG 短影音；短影音請用 `ig-short-video-marketing-editor`。
  必須輸出可檢查 project/timeline 並打開 GUI 或預覽頁，不能只跑 CI 成片。
---

# YouTube 長片專案精修版

這是 Rick 的 16:9 YouTube / 課程 / 訪談長片專案精修入口。

它不是 IG 短影音，不負責粥粥 Reels 或手機直拍預告。只要目標是 YouTube 長片、16:9、課程內容、長訪談、長教學，就用這個 skill。

## 適用情境

- 16:9 YouTube 長片。
- 課程錄影、講解影片、訪談長片。
- 已經有長片素材，要整理成可上架版本。
- 需要字幕軌、音軌、章節、封面節奏、片頭片尾、B-roll 或畫面包裝。
- 需要在 Palmier Pro 或本地預覽中看到剪輯過的軌道。

不要用在：

- 9:16 IG Reels / Shorts。
- 粥粥訪談黑底字幕短片。
- 手機直拍螢幕課程預告。

以上都改用 `ig-short-video-marketing-editor`。

## 強制可視化規則

這是硬規則。不能只跑 CI / headless / 自動渲染後丟一個 mp4 路徑。

每次執行都必須：

- 產生可檢查 project 或 timeline。
- 打開 Palmier Pro、剪輯專案、或本地 preview/review 頁。
- 音軌、字幕軌、章節與片段切點要能被檢查。
- 如果有重剪，V1 / A1 / 字幕或 overlay 分段要清楚。
- 不可只回報「已輸出」或「CI pass」。

標準輸出資料夾至少包含：

```text
youtube_project_finish/
├── final_16x9.mp4
├── transcript_cleaned.srt
├── chapters.txt
├── segments.csv
├── audio_check.txt
└── preview.html 或 .palmier 專案
```

## 長片規格

預設：

- 比例：16:9
- 解析度：1920x1080 或依素材升到 4K
- 字幕：繁體中文，必要時雙語
- 字體：優先 `PingFang TC`
- 節奏：保留完整敘事，不像 Reels 那樣過度跳剪

## 標準流程

1. 先看素材資訊：長度、比例、音軌、字幕、是否已有剪輯專案。
2. 先修音訊：確認沒有靜音段、爆音、音量忽大忽小。
3. 再處理字幕：轉繁體、修錯字、斷句、匯出 SRT/ASS。
4. 建立章節：依主題切出 YouTube chapters。
5. 精修畫面：片頭、轉場、下三分之一、B-roll、重點字卡、片尾 CTA。
6. 產生可視化專案或 preview，打開給 Rick 看。
7. 輸出 final 16:9 成片。

## 檢查重點

- 有 audio stream。
- 字幕與聲音對齊。
- 章節時間正確。
- 片頭片尾不壓到主內容。
- 16:9 畫面沒有被錯誤裁成 9:16。
- 可視化專案已打開，不是只輸出檔案。

## 交付格式

完成後回報：

- 成片路徑
- project / preview 路徑
- 已打開哪個程式或預覽頁
- 字幕、音訊、章節檢查結果
- 是否另外需要 YouTube 上架包
