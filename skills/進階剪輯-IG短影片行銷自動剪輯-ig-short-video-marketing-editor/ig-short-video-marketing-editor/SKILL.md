---
name: ig-short-video-marketing-editor
description: >-
  IG 短影片行銷自動剪輯。當 Rick 說「剪短影片專用 / IG短影片行銷 / 自動剪輯 / Reels / Shorts /
  Podcast 訪談風格 / 純訪談黑底字幕 / 主持人受訪者分開 / 字幕不能漏 /
  直式拍螢幕預告 / 手機拍螢幕 / 課程預告 / 會員公告 / PalmierPro x Codex /
  要看到音軌字幕軌 / 打開 PalmierPro 專案給我看 / 不要CLI版本」時觸發。這是唯一的 9:16 短影音剪輯入口；
  內部分支為「Podcast 訪談風格」與「直式拍螢幕預告風」。必須輸出可檢查的 PalmierPro 專案，
  強制打開 PalmierPro，讓 Rick 看見字幕素材/字幕軌、音訊 waveform 與剪輯分段在軌道上，不能只跑 CLI/headless 成片。
  剪輯前必須完整抽字幕並理解全片；正式字幕一律白字黑描邊；完成後必須在 CLI/Claude/GPT 回覆建議生成提示詞。
  剪輯完成後必須全自動接 `rick-yt-publish` 產出上架包：CLI/Claude Code/Codex 有 skill 就直接接著用；Claude App/GPT App 不能呼叫本機 skill 時，也要在同一個最後回覆直接產出標題、簡介、章節、封面提示詞。
---

# IG 短影片行銷自動剪輯

這是 Rick 的 9:16 IG / Reels / Shorts 短影音剪輯總入口。

之後短影音只記這一個入口；不同短影音風格在本 skill 內部分支處理。

## 工具依賴與 preflight

這是硬規則，給 Codex CLI / Claude Code 執行前使用。任何短影音任務在讀素材、轉字幕、切 segment、建立專案或輸出影片前，先做 preflight；缺工具時停止並回報，不可改用純文字推測或假裝已完成。

必備本機工具：

- PalmierPro：用來建立並打開可檢查的 `.palmier` 專案。
- `ffmpeg`：用來轉檔、抽音訊、裁切測試片段、輸出 9:16 成片。
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

## 全域硬規則：字幕、理解、完成提示詞

這些規則套用到本 skill 的所有 9:16 分支。任何切片、剪節奏、刪口誤、重排、加 hook、加 CTA、建立 PalmierPro 專案或輸出成片前，都必須先完成：

1. 抽取或取得整支影片的完整字幕/逐字稿，不只抽片段。
2. 清理字幕：去除重複、修正明顯 Whisper 誤字、補標點、保留時間碼。
3. 讀完整份字幕，理解整部影片的主旨、段落、hook、賣點、CTA、可刪段落與不可刪段落。
4. 依完整理解再決定短影音切點、節奏、字幕斷句、畫面裁切、CTA 與輸出版本。

不可只看檔名、只看前 30 秒、只靠聲波或只靠畫面猜內容後直接剪。前 30 秒 smoke test 只用來驗證工具鏈，不可取代完整字幕理解。

字幕視覺統一規則：

- 所有正式字幕一律白色文字加黑色描邊/外框。
- 不使用金色、青綠色或其他彩色字幕文字；需要區分 speaker 時，用姓名標籤、位置、排版或時間軸分段處理。
- 黑色描邊不是黑底框；不要用大面積黑色底框遮住畫面。
- 字幕必須可讀、與聲音對齊，且位於 IG 安全區內。

完成後必須在 CLI、Claude App 或 GPT App 的最後回覆中寫：

- `已完成：剪輯完成。`
- 建議生成提示詞：依照這支成片內容，提供 3-5 條可直接複製的提示詞，用於封面、B-roll、社群文案、下一版修改或延伸短影音。不能只寫進檔案。

## 全自動接 rick-yt-publish

這是硬規則。剪輯、輸出、PalmierPro 驗收完成後，不要等 Rick 再說「做上架包」，必須自動接 `rick-yt-publish` 的上架包流程。

執行方式：

- CLI / Claude Code / Codex：若 `rick-yt-publish` skill 已安裝，立刻用它處理本次成片、完整字幕、清理字幕、章節/段落與剪輯摘要。
- CLI / Claude Code / Codex：若找不到 `rick-yt-publish` skill，不可停下；直接依同一規格內建產出上架包。
- Claude App / GPT App：即使不能呼叫本機 skill，也要在同一個最後回覆直接產出完整上架包，不要只給「請接著使用 rick-yt-publish」的提示。
- 不自動真的發布或上傳到 YouTube / IG / TikTok；只產出可複製的上架內容與封面提示詞，除非 Rick 明確要求發布。

上架包至少包含：

- `youtube_publish_package.md`：YouTube Shorts/Reels 標題 3-4 個、影片簡介、章節/段落、hashtag、置頂留言建議。
- `thumbnail_prompt_zh_en.md`：封面中文提示詞 + 英文提示詞，若有中文大字，提醒用本地工具或 Canva 疊字，避免生成模型中文字缺筆畫。
- 最後回覆中的「上架包」區塊：直接貼出可複製的標題、簡介、章節、hashtag、封面提示詞。

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
- Rick 與受訪者都遵守白色字幕加黑色描邊；可用姓名標籤、位置或排版區分 speaker，字幕斷句要像人看得懂。

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
- 字幕不要用大黑底框；預設白字加黑色描邊/外框。
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
├── transcript_full.txt
├── transcript.json
├── captions.srt 或 captions.ass
├── segments.csv
├── audio_check.txt
├── edit_prompt_suggestions.md
├── youtube_publish_package.md
├── thumbnail_prompt_zh_en.md
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
- `已完成：剪輯完成。`
- 建議生成提示詞：依成片內容提供 3-5 條可直接複製的提示詞，用於封面、B-roll、社群文案、下一版修改或延伸短影音。這段必須出現在 CLI、Claude App 或 GPT App 的最後回覆中，不能只寫進檔案。
- 自動接 `rick-yt-publish` 已完成：列出上架包檔案路徑，並在最後回覆直接貼出標題、簡介、章節/段落、hashtag、封面中英提示詞。
