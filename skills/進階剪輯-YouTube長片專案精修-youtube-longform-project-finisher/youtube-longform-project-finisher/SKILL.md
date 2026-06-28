---
name: youtube-longform-project-finisher
description: >-
  YouTube 長片專案精修版。當 Rick 說「YouTube長片 / U2長片 / 16:9長片 / 長片專案精修 /
  課程長片 / 訪談長片 / 幫我整理長片專案 / 長片字幕 / 長片音軌 / 長片剪輯軌道 /
  PalmierPro 長片 / 打開專案給我看」時觸發。用於 16:9 YouTube / 課程 / 訪談長片的專案精修、
  字幕音訊軌道整理、章節與包裝。不是 9:16 IG 短影音；短影音請用 `ig-short-video-marketing-editor`。
  必須輸出可檢查 PalmierPro 專案並打開 PalmierPro，讓 Rick 看見字幕素材/字幕軌、音訊 waveform 與剪輯分段在軌道上，不能只跑 CLI 成片。
  剪輯前必須完整抽字幕並理解全片；正式字幕一律白字黑描邊；完成後必須在 CLI/Claude/GPT 回覆建議生成提示詞。
  剪輯完成後必須全自動接 `rick-yt-publish` 產出 YouTube 上架包：CLI/Claude Code/Codex 有 skill 就直接接著用；Claude App/GPT App 不能呼叫本機 skill 時，也要在同一個最後回覆直接產出標題、簡介、章節、封面提示詞。
---

# YouTube 長片專案精修版

這是 Rick 的 16:9 YouTube / 課程 / 訪談長片專案精修入口。

它不是 IG 短影音，不負責 Podcast 訪談 Reels 或手機直拍預告。只要目標是 YouTube 長片、16:9、課程內容、長訪談、長教學，就用這個 skill。

## 適用情境

- 16:9 YouTube 長片。
- 課程錄影、講解影片、訪談長片。
- 已經有長片素材，要整理成可上架版本。
- 需要字幕軌、音軌、章節、封面節奏、片頭片尾、B-roll 或畫面包裝。
- 需要在 Palmier Pro 或本地預覽中看到剪輯過的軌道。

不要用在：

- 9:16 IG Reels / Shorts。
- Podcast 訪談黑底字幕短片。
- 手機直拍螢幕課程預告。

以上都改用 `ig-short-video-marketing-editor`。

## 工具依賴與 preflight

這是硬規則，給 Codex CLI / Claude Code 執行前使用。任何長片任務在讀素材、轉字幕、建立專案或輸出影片前，先做 preflight；缺工具時停止並回報，不可改用純文字推測或假裝已完成。

必備本機工具：

- PalmierPro：用來建立並打開可檢查的 `.palmier` 專案。
- `ffmpeg`：用來轉檔、抽音訊、裁切測試片段、輸出成片。
- `ffprobe`：用來檢查素材比例、長度、音訊 stream 與輸出檔有效性。
- Whisper / MLX Whisper：優先使用 `mlx_whisper` 搭配 `large-v3-turbo` 產生字幕；若環境只有其他 Whisper CLI，先回報並等 Rick 確認替代方案。

執行前必跑：

```bash
command -v ffmpeg
command -v ffprobe
command -v mlx_whisper
open -Ra "PalmierPro"
```

若 `mlx_whisper` 不是獨立指令，再試：

```bash
python3 -m mlx_whisper --help
```

preflight 規則：

- 任一必備工具缺失，就停止任務並列出缺哪個工具與建議安裝方向。
- 不可在沒有 `ffmpeg` / `ffprobe` 時處理素材或聲音檢查。
- 不可在沒有 Whisper / MLX Whisper 時自行腦補字幕；只能使用 Rick 已提供的 `.srt` / `.vtt` / `.ass`。
- 不可在 PalmierPro 無法開啟時宣稱已完成 PalmierPro 專案；只能先輸出中間檔並回報卡點。
- 正式處理前，先用素材前 30 秒做 smoke test：產生測試字幕、確認 audio stream、確認可建立或打開 PalmierPro 專案。

## 全片理解與字幕硬規則

這是硬規則。任何剪輯、切點、重排、章節、B-roll、字卡或成片輸出前，都必須先完成：

1. 抽取或取得整支影片的完整字幕/逐字稿，不只抽片段。
2. 清理字幕：去除重複、修正明顯 Whisper 誤字、補標點、保留時間碼。
3. 讀完整份字幕，整理整部影片的主旨、段落、重點、可刪段落與不可刪段落。
4. 依完整理解再決定剪輯節奏、章節、轉場、B-roll、片頭片尾與 CTA。

不可只看檔名、只看前 30 秒、只靠聲波或只靠畫面猜內容後直接剪。前 30 秒 smoke test 只用來驗證工具鏈，不可取代完整字幕理解。

字幕視覺統一規則：

- 所有正式字幕一律白色文字加黑色描邊/外框。
- 不使用彩色字幕文字；需要強調時用粗體、位置、節奏或獨立字卡處理。
- 黑色描邊不是黑底框；不要用大面積黑色底框遮住畫面。
- 字幕必須可讀、與聲音對齊，且不能壓到重要畫面。

## 全自動接 rick-yt-publish

這是硬規則。剪輯、輸出、PalmierPro 驗收完成後，不要等 Rick 再說「做上架包」，必須自動接 `rick-yt-publish` 的 YouTube 上架包流程。

執行方式：

- CLI / Claude Code / Codex：若 `rick-yt-publish` skill 已安裝，立刻用它處理本次成片、完整字幕、清理字幕、章節、段落與剪輯摘要。
- CLI / Claude Code / Codex：若找不到 `rick-yt-publish` skill，不可停下；直接依同一規格內建產出上架包。
- Claude App / GPT App：即使不能呼叫本機 skill，也要在同一個最後回覆直接產出完整上架包，不要只給「請接著使用 rick-yt-publish」的提示。
- 不自動真的發布或上傳到 YouTube；只產出可複製的上架內容與封面提示詞，除非 Rick 明確要求發布。

上架包至少包含：

- `youtube_publish_package.md`：YouTube 標題 3-4 個、影片簡介、章節、hashtag、置頂留言建議。
- `thumbnail_prompt_zh_en.md`：封面中文提示詞 + 英文提示詞，若有中文大字，提醒用本地工具或 Canva 疊字，避免生成模型中文字缺筆畫。
- 最後回覆中的「上架包」區塊：直接貼出可複製的標題、簡介、章節、hashtag、封面提示詞。

## 強制 PalmierPro 驗收規則

這是硬規則。不能只跑 CLI / headless / 自動渲染後丟一個 mp4 路徑。

每次執行都必須：

- 產生可檢查的 `.palmier` 專案。
- 打開 PalmierPro，讓 Rick 直接看到 timeline。
- V 軌、A 軌、字幕素材/字幕軌、章節標記、片段切點要能被檢查。
- A 軌必須看得到 waveform；字幕不能只燒進 mp4，必須有可辨識的字幕素材、字幕段或 overlay 檔在軌道上。
- 如果有重剪，V1 / A1 / 字幕或 overlay 分段要清楚，不能是一整條看不出剪輯。
- 本地 preview/review 頁只能當補充，不能取代 PalmierPro 驗收。若 PalmierPro 無法開啟，先回報原因並等待 Rick 確認。
- 不可只回報「已輸出」或「CLI 已完成」。

標準輸出資料夾至少包含：

```text
youtube_project_finish/
├── final_16x9.mp4
├── project.palmier
├── transcript_full.txt
├── transcript_cleaned.srt
├── chapters.txt
├── segments.csv
├── audio_check.txt
├── edit_prompt_suggestions.md
├── youtube_publish_package.md
├── thumbnail_prompt_zh_en.md
└── preview.html
```

## 長片規格

預設：

- 比例：16:9
- 解析度：1920x1080 或依素材升到 4K
- 字幕：繁體中文，必要時雙語；所有正式字幕一律白字黑描邊
- 字體：優先 `PingFang TC`
- 節奏：保留完整敘事，不像 Reels 那樣過度跳剪

## 標準流程

1. 先看素材資訊：長度、比例、音軌、字幕、是否已有剪輯專案。
2. 抽取或取得完整字幕/逐字稿，清理成 `transcript_full.txt` 與 `transcript_cleaned.srt`。
3. 讀完整字幕並寫出影片主旨、段落、重點、可刪段落與不可刪段落，再開始剪輯判斷。
4. 先修音訊：確認沒有靜音段、爆音、音量忽大忽小。
5. 建立章節：依主題切出 YouTube chapters。
6. 精修畫面：片頭、轉場、下三分之一、B-roll、重點字卡、片尾 CTA。
7. 套用字幕樣式：白色文字、黑色描邊/外框、繁體中文、必要時雙語。
8. 產生 PalmierPro 專案並打開給 Rick 看；字幕素材/字幕軌與音訊 waveform 必須在軌道上可見。
9. 輸出 final 16:9 成片。

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
- PalmierPro 專案路徑
- 已打開的 PalmierPro 專案名稱
- 字幕素材/字幕軌與 A 軌 waveform 在哪一條軌道
- 字幕、音訊、章節檢查結果
- 是否另外需要 YouTube 上架包
- `已完成：剪輯完成。`
- 建議生成提示詞：依照這支成片內容，提供 3-5 條可直接複製的提示詞，用於封面、B-roll、社群短影音、YouTube 標題/描述或下一版修改。這段必須出現在 CLI、Claude App 或 GPT App 的最後回覆中，不能只寫進檔案。
- 自動接 `rick-yt-publish` 已完成：列出上架包檔案路徑，並在最後回覆直接貼出標題、簡介、章節、hashtag、封面中英提示詞。
