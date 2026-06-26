---
name: palmier-pro-shorts-finisher
description: Palmier Pro 短影音精修官。當 Rick 說「PalmierPro / Palmier Pro / 用 Palmier 剪 / 接續 Palmier 專案 / IG 短影音上方封面 / YouTube 封面放到短影音上面 / 下方字幕區太單調 / 加黑金紅 HUD / 加淺淺浮水印 / Rickbowen&Bexs / 片尾進場動畫 / 我沒看到 / 打開專案 / Palmier 沒刷新 / 另開副本」時觸發。用來接續既有 .palmier 專案，精修 9:16 交易短影音的封面、字幕區設計、品牌水印、片尾文字動畫，並避免覆蓋使用者或 Palmier 快取造成看不到更新。NOT for：純訪談無露臉、黑底逐字稿、主持人/受訪者 speaker 分離、Podcast 感 Reels；那種用 interview-reels-dialogue-editor。
---

# Palmier Pro 短影音精修官

這個 skill 負責把 Rick 已經剪到一半的 Palmier Pro 直式短影音，做成可以交付的「成片精修版」。

重點不是重新自動剪片，而是接續現有 `.palmier` 專案，把上方封面、盤面區、字幕區、品牌水印、片尾動畫和 Palmier 專案刷新問題處理到位。

如果素材是純訪談、沒有露臉、主畫面要靠黑底逐字稿與主持人/受訪者分色字幕成立，改用 `interview-reels-dialogue-editor`。不要把這兩種混在一起。

## 中文 SKILL 題型關鍵詞

看到這些需求時，用這個 skill：

- **Palmier 專案精修**：接續 Palmier、打開 Palmier、Palmier 沒刷新、另開副本。
- **既有短片變高級**：這支已經剪好了但畫面太普通、幫我包裝、精修成可發版本。
- **上方封面橫幅**：IG 短影音上面加 YouTube 封面感、上方縮圖區、封面放到短片上。
- **下方字幕區設計**：下方太空、加 HUD、黑金紅交易介面、字幕框、低調水印。
- **品牌水印 / 片尾動畫**：Rickbowen&Bexs、淺淺浮水印、片尾金句、片尾掃光。
- **Palmier 看不到更新**：我沒看到、怎麼沒改、沒有出現、是不是快取。

不要用在：

- **從長片自動切多支短影音**：改用 `video-shorts`。
- **純訪談黑底逐字稿 Reels**：改用 `interview-reels-dialogue-editor`。
- **YouTube 標題/簡介/封面提示詞**：改用 `rick-yt-publish`。

## 什麼時候用

- Rick 說要接續 Palmier Pro / Palmierpro 專案，不要從頭重剪。
- 上方需要 YouTube 封面感的橫幅，放在 IG / Reels / Shorts 直式影片上方。
- Rick 說「你根本沒有設計」、「下面太單調」、「下方介面有點空」。
- 需要在字幕區加黑金紅金融 HUD、交易介面感、淺淺浮水印。
- 需要把 `Rickbowen&Bexs` 這類品牌字樣做成低調水印。
- Palmier 已經打開但 Rick 說「我沒看到」、「沒有出現」、「你打開一下」。
- 需要在片尾兩句話加進場動畫，例如掃光、淡入、微縮放。

## Rick 滿意的成品標準

- 9:16 直式短影音，中間盤面清楚，不要被封面或字幕擋住重點。
- 上方封面要像 YouTube 縮圖，不是單純黑底白字。可以是黑金紅、壓力位、K 線、量能、紅色阻力帶、金色標牌這類強視覺。
- 不一定要寫 `POV`，除非 Rick 明確要。Rick 後來偏好「放開手設計」。
- 字幕一次一行，跟說話內容對上，不要自己改成不符合語音的意思。
- 下方字幕區不能只有黑底一行字。要有低調介面層次：玻璃感框、細金線、紅色市場光、淡網格、細小 K 線/音量/波形元素。
- 水印要淺，不搶字幕。`Rickbowen&Bexs` 要拼字完全正確；不要交給 AI 圖像模型亂生文字。
- 片尾金句要有進場動畫，但不要花俏到像模板感。淡入、輕微 scale、掃光線就夠。

## 必要輸入

- `.palmier` 專案路徑，例如：
  `/Users/huangweijie/Documents/Palmier Pro/Rick_clip01_xxx.palmier`
- 工作資料夾，例如：
  `/Users/huangweijie/Desktop/codex-test/edit_work/clip01_v3_palmier_visible`
- 如果有使用者喜歡的封面圖，先用那張，不要再硬生成。
- 若要加浮水印，確認精準字串，例如 `Rickbowen&Bexs`。

## Palmier 專案結構

Palmier 的 `.palmier` 是資料夾，常見內容：

- `project.json`: 時間線、tracks、clips、transform、opacity。
- `media.json`: 媒體庫 entries，圖檔/影片/音效路徑。
- `thumbnail.jpg`: 專案縮圖。

注意：

- `project.json` 不一定有專案名稱；Palmier UI 常用 `.palmier` 資料夾名稱當專案名。
- clip 引用媒體時常用 `mediaRef`，不是 `mediaId`。查不到時要全文搜尋。
- 媒體路徑通常在 `media.json` 的 `source.external.absolutePath`。

## 標準流程

1. 先保護現場

   - 先確認現在要改哪個 `.palmier`，不要碰 Rick 說不要碰的原專案。
   - 如果另一個 Palmier 視窗也在剪，先 `ditto` 成新副本再改。
   - 每次改 `project.json` / `media.json` 前都備份到工作資料夾。

2. 做封面橫幅

   - IG 9:16 影片上方橫幅建議做成 `1080x520`。
   - 如果 Rick 已經用 ChatGPT 網頁版生成滿意圖，優先裁切/縮放那張。
   - 封面要放在上方，常用 transform 方向：
     - `width: 1`
     - `height: 0.27`
     - `centerX: 0.5`
     - `centerY: 0.135`
   - 封面應該是完整設計圖，不要再疊一堆 Palmier 文字造成重複。

3. 做下方字幕 HUD

   - 建議資產尺寸 `1080x360`。
   - 中心保留暗色字幕安全區，邊緣做黑金紅 HUD、細線、微光、網格。
   - 不要讓亮線、K 線、紅光穿過字幕文字。
   - 常用 transform 方向：
     - `width: 1`
     - `height: 0.19`
     - `centerX: 0.5`
     - `centerY: 0.845`
   - opacity 視素材強度調整，通常 `0.55` 到 `0.75`。

4. 加淺水印

   - 精準文字用本地 PIL / canvas / SVG 合成，不要讓圖像模型直接生字。
   - 大字水印可放字幕框後方，alpha 約 5% 到 10%。
   - 小簽名可放右下，alpha 約 10% 到 18%。
   - Rick 要的是「淺淺的浮水印」，不是明顯 logo。

5. 片尾進場動畫

   - 片尾兩句金句可以分行、錯開進場。
   - 建議淡入 16 到 20 frames，scale 從 `0.94` 到 `1.0`。
   - 可加短掃光 PNG，duration 約 40 到 50 frames。
   - 不要用太多轉場，保持高級和乾淨。

6. 更新 Palmier JSON

   - 在 `media.json` 增加新的 image entry，使用新的 ID，避免 Palmier 快取舊素材。
   - 在 `project.json` 找到目標 clip，把 `mediaRef` 換成新的 media ID。
   - 不要只改檔案內容但沿用同一個 media ID；Palmier 可能不刷新。
   - 用 `jq empty` 驗證 JSON。

7. 驗證畫面

   - 至少渲染或截圖檢查：
     - 0 秒：封面是否出現。
     - 中段字幕：下方 HUD 是否出現、字幕是否清楚。
     - 片尾：兩句金句進場動畫是否有效。
   - 若有本地 renderer，記得用 `project.json` 的 `fps`，不要硬寫 30fps。

8. 打開給 Rick 看

   - 直接開專案：
     `open "/path/to/project.palmier"`
   - 拉 Palmier 到前景：
     `osascript -e 'tell application "PalmierPro" to activate'`
   - 如果 Rick 說看不到，通常不是沒寫入，而是 Palmier 還在記憶體用舊狀態。
   - 最穩方式：把整個 `.palmier` 複製成新資料夾名再 open，例如：
     `ditto old.palmier Rick_clip01_hud_watermark_v2.palmier`

## 常用命令

驗證 JSON：

```bash
jq empty "/path/to/project.palmier/project.json"
jq empty "/path/to/project.palmier/media.json"
```

找媒體引用：

```bash
rg -n "SUBTITLE|HUD|Rickbowen|mediaRef|BANNER" "/path/to/project.palmier/project.json" "/path/to/project.palmier/media.json"
```

直接開 Palmier：

```bash
open "/Users/huangweijie/Documents/Palmier Pro/Rick_clip01_hud_watermark_v2.palmier"
osascript -e 'tell application "PalmierPro" to activate'
```

Palmier 不刷新時另存新副本：

```bash
src="/Users/huangweijie/Documents/Palmier Pro/Rick_clip01_chatgpt_cover_final.palmier"
dst="/Users/huangweijie/Documents/Palmier Pro/Rick_clip01_hud_watermark_v2.palmier"
ditto "$src" "$dst"
open "$dst"
osascript -e 'tell application "PalmierPro" to activate'
```

## 圖像生成策略

- 如果 Rick 願意用 ChatGPT 網頁版生成封面，給他一版精準提示詞；拿到圖後再裁切進 Palmier。
- 如果 Codex 能用內建 image generation，就直接生成底圖，但最後要把檔案搬回工作資料夾，不能只留在 `~/.codex/generated_images/...`。
- 對文字要求高的圖，不要讓 AI 生成精準文字；改成無字底圖，再本地合成文字。
- 封面可以有大字，因為 Rick 會親自確認；水印和品牌字必須本地合成，避免拼錯。

## 封面提示詞模板

給 ChatGPT 網頁版時可用中文，因為要生成中文字。範例：

```text
請生成一張 YouTube 交易教學封面，尺寸 1792x1024 或 16:9 橫幅。
主題：開盤跳高，不要急著追多，先找壓力位，等到位置再出手。
風格：黑金紅高級金融感，像專業交易頻道縮圖，強烈但不雜亂。
畫面：右側是 K 線圖快速上漲，頂部有紅色壓力帶，底部有成交量柱；左側是大標題區，金色牌匾和紅色警示牌。
文字請精準寫：
開盤跳高
先別追
先找壓力位
等到位置再出手
要求：中文字正確、粗體、清楚、不要多餘英文、不要 logo、不要人物、不要浮水印。
```

## 下方 HUD 提示詞模板

底圖不要文字，文字後製：

```text
Create a premium cinematic trading HUD lower-third background for a vertical Instagram Reels trading education video.
Canvas: 1080 x 360.
Style: luxury finance, black/gold/red, subtle glassmorphism, thin gold accent lines, faint red market glow, low-contrast chart-grid texture, tiny abstract candlestick and volume motifs near the bottom edge.
Composition: keep the center area clean, dark, and readable for white Chinese subtitles. Add visual detail around the edges, not behind the text.
Constraints: no readable text, no logos, no people, no arrows, no watermark, no fake UI labels.
Avoid clutter, bright elements behind subtitles, purple/blue gradients, cartoon style, stock photo look.
```

## 踩過的雷

- Rick 說「設計」時，不是要普通文字排版；要真的像可發布的縮圖或商業短影音包裝。
- `mediaId` 查不到不代表沒掛上；Palmier clip 常用 `mediaRef`。
- 直接改同一個 PNG 檔，Palmier 可能快取不刷新。換新檔名、新 media ID 更穩。
- `open -a /Applications/PalmierPro.app project.palmier` 有時只會把 App 叫到前景。直接 `open project.palmier` 比較穩。
- Palmier 已開同名 project 時，可能不重新讀磁碟版。另存新 `.palmier` 資料夾名最穩。
- 下方 HUD 太亮會搶字幕；太工程感會變醜。中心暗、邊緣有設計感是關鍵。
- 浮水印不要大到像標題。Rick 要的是「淺淺的」。
- 生成圖的文字常出錯。封面文字若 AI 生成錯，讓 Rick 重生或改本地後製；品牌水印必須本地後製。

## 協作標準

- 先做一版可看見的版本，不要停在口頭建議。
- 每次 Rick 說「沒看到」先檢查 Palmier 是否開到正確專案和正確時間點，再判斷是否快取。
- 回覆時講清楚現在應該打開哪個 `.palmier`，避免 Rick 看舊專案。
- Rick 滿意後，把最終專案路徑、關鍵資產路徑、備份路徑整理給他。
- 若 Rick 說滿意並要求「寫成 skill」，立刻把這套流程固化，不要只做摘要。
