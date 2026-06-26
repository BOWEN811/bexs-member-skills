---
name: video-shorts
description: >-
  本地長片自動切短影音(Fomofly / Opus Clip 自架版,全程 $0、純本地)。當 Rick 說
  「切短影音 / 把這片切成 Shorts / 長片變短片 / 剪短片 / 切片 / 一鍵切片 / 做 Reels /
  做短影音 / 這支影片切重點 / 幫我切幾支 / clip 這支 / 把影片變短 / 自動剪片 / 抓重點剪 /
  這片有沒有可以發的片段 / local-clipper / 切 YouTube Shorts / 影片切成直式」等任何字眼,
  或丟出一個影片檔路徑並要求做成短影音時觸發。此 skill 用本地 mlx-whisper 轉錄 +
  本地 Qwen3-80B 挑完整重點 + ffmpeg 裁直式 9:16 + pycaps 動畫逐詞字幕,把長片自動切成
  一批可直接發 IG/TikTok/Shorts 的短影音。專長知識型乾貨(交易/財經教學)。NOT for：純訪談無露臉、
  黑底逐字稿、主持人/受訪者 speaker 分離的高級 Podcast 感 Reels；那種用 interview-reels-dialogue-editor。
---

# video-shorts — 本地長片自動切短影音

把一支長影片自動切成多支「完整重點」的直式短影音,適合發 IG Reels / TikTok / YouTube Shorts。
全程本地、零成本。實作在 `~/local-clipper/`。

如果 Rick 要的是純訪談、沒有露臉、用黑底逐字稿分主持人/受訪者的 Podcast 感短片，改用
`interview-reels-dialogue-editor`。本 skill 最多只負責先從長訪談中挑出片段，後續視覺與 speaker 驗證要交給訪談 skill。

## 中文 SKILL 題型關鍵詞

看到這些需求時，用這個 skill：

- **長片切短影音**：長影片變 Shorts、長片切 Reels、切 TikTok、切精華。
- **自動抓重點**：幫我抓這支影片重點、這片有沒有可以發的片段、幫我切幾支。
- **多支短片批量產出**：一次切 3 支、一次切 5 支、把一支長片做成一批短影音。
- **有主畫面的知識型影片**：交易教學、盤面講解、螢幕錄影、講師影片、財經乾貨。
- **先挑片段再精修**：先從長訪談裡找出值得剪的段落；後續若要黑底訪談字幕，轉 `interview-reels-dialogue-editor`。

不要用在：

- **純訪談黑底字幕片**：主持人/受訪者要分色、每句都要字幕、Podcast 感，改用 `interview-reels-dialogue-editor`。
- **已經剪好的 Palmier 專案要加 HUD/封面**：改用 `palmier-pro-shorts-finisher`。

## 能力
- **通讀全片 + 精剪**:本地 Qwen3-80B 先讀完整支逐字稿,去掉重複主題(同主題多次只取最好),
  每支片在主題範圍內**挑保留句、跳過口誤/重複/離題/卡頓**,用多段不連續區間串成緊湊短片。
- **開頭 HOOK**:每支第一句強制是鉤子(反差/問句/數字/結論前置)。
- **長度 ≤60 秒**(理想 35~55s)。
- **裁直式 9:16**:自動判斷 — 有人臉追臉裁、螢幕/圖表錄影「整畫面縮入+模糊背景」(fit)。
- **動畫字幕**:pycaps CSS 逐詞高亮;預設 shorts8 模板 = **單行、一次 ≤8 字**;
  斷句用 **jieba 中文斷詞 + 語音停頓**(不切爛詞如「正確」、斷在片語結尾);
  字幕用主轉錄(large-v3-turbo)精準逐字稿(不讓 pycaps 用 small 重轉產生錯字)+ 濾語助詞。
- **蓋原片字幕**:若原片本身有燒死字幕(常見於螢幕錄影教學),用 `--crop-bottom 0.09`
  裁掉底部那條蓋掉,避免雙字幕。run.sh 第 5 參數。
- **可選 BGM ducking**:講話時配樂自動壓低。

## 怎麼執行(Claude 收到觸發時照做)

1. **拿到影片路徑**。Rick 沒給就問「哪支影片?路徑給我」。

2. **一鍵跑**(run.sh 會自動起 80B server、自動判斷 fit/face、自動開資料夾):
   ```bash
   bash ~/local-clipper/run.sh "影片路徑.mp4"
   ```
   - 要指定支數:`bash ~/local-clipper/run.sh "影片.mp4" 8`
   - 原片有燒死字幕→裁底蓋掉:`bash ~/local-clipper/run.sh "影片.mp4" 5 fit "" 0.09`
   - 參數順序:`影片 [支數=5] [reframe=auto] [模板=shorts8] [crop-bottom=0]`
   - ⚠️ 跑前先抽一幀看原片有沒有自帶字幕(`ffmpeg -ss 60 -i 影片 -frames:v 1 /tmp/x.png` 再讀圖);
     有的話加 crop-bottom 蓋掉,避免雙字幕。

3. **回報挑出的重點**:跑完後把 console 印出的「選出 N 支」清單(標題+時長+理由)整理給 Rick 看,
   並說明成品在 `~/local-clipper/out/`。

4. **背景跑**:長片(40 分鐘)轉錄+切片約 5-8 分鐘,用背景執行 + 定時 tail log 監控,別卡住對話。

## 重要前提 / 踩雷(務必遵守)
- **記憶體**:80B 吃 ~45GB。跑前先 `ps aux | grep -iE "mflux|comfy|wan|ltx"` 確認**沒有其他重模型在跑**
  (Rick 規矩:一次只跑一個重模型)。有的話先問 Rick 要不要等,或改用輕量模型挑(品質較差)。
- **挑片一定用 80B**,別用 Qwen3-4B(實測 4B「重點完全切錯」被退貨)。
- **port**:80B 用 8091。8080 是圖像生成 server、別撞。
- **快速迭代**:同一支片只想換模板/模式,加 `--reuse-transcript` 跳過轉錄
  (直接呼叫 `~/local-clipper/clipper.py`,run.sh 預設不帶)。
- **桌面 TCC**:影片在 `~/Desktop` 通常讀得到;若報權限錯,請 Rick 把檔案搬到家目錄。

## 進階(直接呼叫 clipper.py 的參數)
`~/local-clipper/.venv/bin/python ~/local-clipper/clipper.py 影片 --n 5 --reframe fit
--template hype --clip-len 40 --reuse-transcript --bgm bgm/x.mp3
--llm-url http://localhost:8091/v1/chat/completions
--llm-model mlx-community/Qwen3-Next-80B-A3B-Instruct-4bit`

詳見 `~/local-clipper/README.md`。

## v1 待辦(Rick 問起才提)
- `--reframe smart`:接 smart-reframe(MediaPipe 主動講者追蹤+平滑運鏡)給談話頭用
- 切點多帶一句鋪陳、特效 zoom punch-in、Resolve 精修關
