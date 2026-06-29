---
name: youtube-longform-project-finisher
description: >-
  YouTube 長片專案精修版。當 Rick 說「YouTube長片 / U2長片 / 16:9長片 / 長片專案精修 /
  課程長片 / 訪談長片 / 幫我整理長片專案 / 長片字幕 / 長片音軌 / 長片剪輯軌道 /
  PalmierPro 長片 / 打開專案給我看」時觸發。用於 16:9 YouTube / 課程 / 訪談長片的專案精修、
  字幕音訊軌道整理、章節與包裝。不是 9:16 IG 短影音；短影音請用 `ig-short-video-marketing-editor`。
  必須輸出可檢查 PalmierPro 專案並打開 PalmierPro，讓 Rick 看見字幕素材/字幕軌、音訊 waveform 與剪輯分段在軌道上，不能只跑 CLI 成片。
  剪輯前必須完整抽字幕並理解全片，字幕理解不得使用全量 PNG/OCR；PNG/截圖只做少量視覺抽查。正式字幕一律白字黑描邊，並用透明 PNG overlay 燒進最終 MP4；完成後必須在 CLI/Claude/GPT 回覆建議生成提示詞。
  當 Rick 說「不要重做 / 不要做另一版 / 直接改這版 / 修改第一版 / 暫停 / 等等 / 先不要動」時，必須優先遵守版本鎖與暫停規則：只改指定的既有專案或時間線，不得另開新版、不得覆蓋未知檔案、不得繼續輸出。
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

## 版本鎖與協作硬規則

這是本 skill 最優先的防呆規則。Rick 常常已經在 PalmierPro 裡手動修過一版；這時目標不是重新生成，而是接手「他現在畫面上的那一版」繼續修。

開始任何修改前，先判定任務屬於哪一種：

- `in_place_project_fix`：Rick 說「不要重做」「直接在這個上面改」「第一版」「現在這版」「我還沒刪完」「等一下」。只能改目前指定的 `.palmier` 專案或它的素材來源，不得另做一個新專案。
- `new_finish_project`：Rick 明確提供素材並要求「幫我剪」「做一版」「輸出一版」。可以建立新的完成資料夾與 PalmierPro 專案。
- `review_only`：Rick 只問問題、問設定、問怎麼操作或說「暫停」。只能說明或檢查，不得改檔或輸出。

版本鎖規則：

- 若 Rick 已指定「第一版」「現在這版」「不要做另一版」，必須先回覆確認「只改這個專案，不重做另一版」再動檔。
- 不得把後面不滿意的剪輯版覆蓋到前面指定版本。
- 不得在 Rick 還在手動刪片段時接管剪輯；Rick 說「等等」「先暫停」「我還沒刪完」就立刻停止所有寫入與輸出。
- 如果畫面上有多個 `final_16x9`、`source_rough_cut`、`subtitle_cut_*` 或多個專案資料夾，先用檔案路徑、修改時間、PalmierPro 畫面名稱確認要改哪一個。
- 若不能確定版本，停止並只問一句短問題：「要改哪個 `.palmier` 路徑？」不要猜。

檔案保護規則：

- 原始素材不覆蓋。
- Rick 指定的既有專案不先備份就不做破壞性改動。
- 對既有專案做較大修改時，輸出中間檔可加 `_work` 或 `_backup`，但最終必須回到 Rick 指定的專案或清楚說明哪個檔案是最終版。
- Rick 要「打開檔案位置」時，優先 `open -R <final_mp4>`，不要只回覆路徑。

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

PNG / 截圖省 token 規則：

- 字幕內容理解一律使用 `.srt` / `.vtt` / `.ass` / `.txt` / `transcript.json` / Whisper 逐字稿。
- 不得把整支影片切成大量 PNG 後靠 OCR 讀字幕，除非 Rick 明確要求，或字幕檔缺失且 Whisper/ASR 也失敗。
- 這裡限制的是「用畫面 PNG / OCR 讀字幕」；正式字幕輸出仍必須把清理後字幕文字渲染成透明 PNG subtitle overlay。
- 影片截圖 / keyframe PNG 只用於 PalmierPro 視覺抽查：畫面比例、字幕位置、白字黑描邊、是否擋臉或遮住重點、片頭片尾是否跑版。
- 預設只抽少量 keyframes：片頭 1 張、每個章節或大段落 1 張、字幕最密集處 2-3 張、片尾 1 張；長片若章節很多，優先抽問題段落，不做全片逐秒截圖。
- 不可用全量 PNG 抽查取代 `transcript_full.txt`、`transcript_cleaned.srt`、`chapters.txt`、`segments.csv` 與 PalmierPro timeline 檢查。
- 省 PNG 不等於跳過字幕樣式驗收；正式字幕仍必須白字黑描邊，優先檢查字幕樣式設定與 PalmierPro timeline，再用少量 keyframes 抽查實際畫面。

字幕視覺統一規則：

- 所有正式字幕一律白色文字加黑色描邊/外框。
- 不使用彩色字幕文字；需要強調時用粗體、位置、節奏或獨立字卡處理。
- 黑色描邊不是黑底框；不要用大面積黑色底框遮住畫面。
- 字幕必須可讀、與聲音對齊，且不能壓到重要畫面。
- 預設字幕只能一行，不要上下疊兩行；若文字太長，優先把句子切成兩個連續字幕事件，而不是同一張字幕圖換行。
- Rick 說「不要標點符號」時，字幕文字需移除逗號、句號、問號、驚嘆號、冒號、頓號、括號與多餘空白。
- 英文產品名保留原拼字，例如 Palmier Pro、Codex、Claude Code、GitHub、Skill；中英相接時可用空格提升可讀性。
- 字幕不要遮住講者臉、麥克風或螢幕重點；課程畫面預設放在下方安全區，必要時略上移。

字幕輸出硬規則：

- 正式字幕一律從清理後的 `.srt` / `.ass` / `transcript.json` 文字來源渲染成透明 PNG subtitle overlay。
- PNG 字幕圖層必須是白色文字 + 黑色描邊；不可只畫黑字、不可只有黑框、不可用大黑底框。
- PalmierPro 專案必須把 PNG 字幕圖層放在字幕 / overlay 軌，讓 Rick 看得到每段字幕素材。
- 最終 `final_16x9.mp4` 必須把同一批 PNG subtitle overlay 燒進影片，輸出硬字幕 MP4；不要只輸出軟字幕、不要只留 `.srt/.ass`、不要只用 PalmierPro 內建文字，除非 Rick 明確改規格。
- `.srt` / `.ass` 仍要保留，作為改字、校稿與重新生成 PNG 字幕圖層的來源。
- 輸出後至少抽查片頭、中段、片尾三個成片畫面，確認字幕真的呈現白字黑描邊。

PNG 字幕渲染防呆：

- PNG 必須是透明 RGBA，不得帶黑底或白底。
- 黑描邊用真正 stroke/outline 渲染，不要用大量重複陰影硬疊，避免 Rick 看到「奇怪毛邊」。
- 建議先畫黑色 stroke，再畫白色 fill；描邊厚度以 1080p 約 4-7px 為起點，依字體粗細調整。
- 不要同時疊多條相同字幕軌；若畫面字幕變粗、變黑、像一團墨，先檢查是否有重複 PNG overlay 或多層字幕軌。
- 字體優先使用清楚的繁中字體，例如 `PingFang TC Semibold`、`Noto Sans CJK TC Bold`、`Source Han Sans TC Bold`；不要用看起來廉價或過度圓胖的字體。
- 每次改字幕字體、顏色、描邊或換行規則時，先輸出 10-20 秒 proof clip 或抽 3 張 proof frames，Rick 確認後才跑全片。
- proof 必須包含：淺色背景一張、深色背景一張、最長字幕一張。三張都要白字黑描邊、無毛邊、單行可讀。

SRT / ASS 使用規則：

- `.srt` / `.ass` 是字幕文字來源與校稿來源，不是最終交付的唯一字幕形式。
- 若 PalmierPro 可匯入 SRT，可用來快速排軌或校對，但最終仍依 Rick 預設要求渲染透明 PNG overlay 並燒進 MP4。
- 不要用 PNG/OCR 反向抓字幕內容；要改字就改 `.srt` / `.ass` / transcript，再重新生成 PNG。
- 若 Rick 問「為什麼不能直接導入 SRT 改字幕」，回答重點：可以用 SRT 當文字來源，但為了固定白字黑描邊、字體、一行規則與最終 YouTube 硬字幕一致，正式輸出仍走 PNG overlay。

## 剪輯決策表

在真正剪長片前，必須先由完整字幕產生 `segments.csv` 或等價表格，避免邊剪邊猜。表格至少包含：

- `start`
- `end`
- `text_summary`
- `decision`：keep / trim / remove / review
- `reason`：停頓、重複、OKOK、讀取等待、操作錯誤、畫面重要、語意必要
- `join_to_next`：刪除後是否要與下一段貼齊

刪減規則：

- 明顯停頓、等待、重複操作、連續 OK / OKOK，預設標記為 `trim` 或 `remove`。
- 沒有說話但畫面正在展示關鍵操作、設定、輸出結果時，標記為 `keep` 或 `review`，不要只因無聲就刪。
- 切點要以語意順為準，不能只看音量波形。
- 刪除中間片段後，後方素材要自動往前貼齊，不能留下黑畫面、空白 gap 或長時間靜音。
- Rick 若指定「只要影片軌道」或「不要動字幕軌」，只處理指定軌道，不連動改其他軌道。

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
- A 軌必須看得到 waveform；不能只有最終 MP4，timeline 上也必須有可辨識的 PNG 字幕素材、字幕段或 overlay 檔。
- 字幕素材預設使用透明 PNG subtitle overlay，並在最終 MP4 中燒成硬字幕。
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
├── assets/subtitles/*.png
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

1. 先判定任務模式：原專案直接修改、新專案輸出、或只檢查不動檔。
2. 鎖定版本與路徑：確認要改的 `.palmier`、素材、final 檔與是否禁止重做。
3. 先看素材資訊：長度、比例、音軌、字幕、是否已有剪輯專案。
4. 抽取或取得完整字幕/逐字稿，清理成 `transcript_full.txt` 與 `transcript_cleaned.srt`。
5. 讀完整字幕並寫出影片主旨、段落、重點、可刪段落與不可刪段落。
6. 產生剪輯決策表 `segments.csv`，先決定 keep / trim / remove / review，再開始動軌道。
7. 先修音訊：確認沒有靜音段、爆音、音量忽大忽小。
8. 建立章節：依主題切出 YouTube chapters。
9. 精修畫面：片頭、轉場、下三分之一、B-roll、重點字卡、片尾 CTA。
10. 套用字幕樣式並渲染 10-20 秒 proof：白色文字、黑色描邊、單行、繁體中文、無標點符號若 Rick 指定。
11. proof 驗收通過後，才渲染全片 PNG overlay。
12. 產生或更新 PalmierPro 專案並打開給 Rick 看；PNG 字幕素材/字幕軌與音訊 waveform 必須在軌道上可見。
13. 輸出 final 16:9 成片，並把 PNG subtitle overlay 燒進 MP4。
14. 抽查 final：片頭、中段、字幕最長處、剪接 gap、高風險黑畫面、片尾。

## 檢查重點

- 有 audio stream。
- 字幕與聲音對齊。
- 字幕是白字黑描邊，不是黑字、不是只有白字、不是黑底框。
- 字幕只出現一行；沒有上下疊字，沒有同一字幕重複疊多層。
- 字幕描邊乾淨，沒有明顯毛邊或黑色糊成一團。
- 章節時間正確。
- 片頭片尾不壓到主內容。
- 16:9 畫面沒有被錯誤裁成 9:16。
- 刪除片段後沒有黑畫面、空白 gap、音訊斷裂或字幕殘留。
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
