# 字幕校正對照表（可成長）

> 每次又抓到新錯字，往對應表加一行。這是「確定性替換」的彈藥庫，
> **非交易影片一律走這裡，不丟千問**（千問+BEXS交易術語表會亂套）。

---

## 表 A — 共用：簡→繁（用 opencc 自動處理，不用手列）

- 一律 `opencc` `s2tw`（**不是 s2twp**，s2twp 會連用詞一起改，風險高）。
- **全檔轉**（時間戳/數字是 ASCII 不受影響），不是只轉千問動過的句子。
- 冪等：原本就是繁體的轉了也不變。
- 原因：Whisper 有時某些段落自己就吐簡體，不一定經過千問。

```python
from opencc import OpenCC
cc = OpenCC('s2tw')
t = open(p, encoding='utf-8').read()
open(p, 'w', encoding='utf-8').write(cc.convert(t))
```

---

## 表 B — AI / 科技教學影片：工具名對照（Rick 口音 + Whisper 慣性錯）

| 錯 | 對 | 備註 |
|---|---|---|
| Cloud / cloud / 克勞德 | **Claude** | 全片最關鍵。但「雲端」中文＝真 cloud，不要動 |
| CloudAI | Claude AI | claude.ai |
| cloudcode / code,cloudcode | Claude Code | 終端機版 |
| CloudPro | Claude Pro | 600/月訂閱 |
| BEXS Code（進階到BEXS+Code跨行） | Claude Code | |
| OpenCloud / Opencloud | **OpenClaw** | 本地 Agent |
| GBT | GPT | |
| GPT-2Cloud | GPT、Claude | |
| CheckGPT | ChatGPT | |
| 謙問 / QWN | 千問 / Qwen | |
| 大圓模型 | 大語言模型 | LLM |
| 中端機 / 中段機 / 風端機 | 終端機 | terminal/CLI |
| NCP | MCP | |
| Course / 客時 | Cursor | |
| Olama | Ollama | 單 l 是錯字 |
| GPTGemini | GPT、Gemini | |
| M4的MakerMeaning | M4 的 Mac mini | |
| 預言5 / 預言5,FILG | Fable 5 | 被禁的那個模型 |
| MaxPrint | Max（方案） | Claude Max plan |
| 月費資料榜 | 月費吃到飽 | |
| scale / Scale | skill | 技能 |
| 新能包 | 技能包 | |
| 網頁炸藥 | 網頁摘要 | 30秒抓重點的 skill |
| AI位（磨掉AI位） | AI味 | 去 AI 腔 |
| mejanine | Midjourney | 出圖提示詞對象 |
| 媒體下來 | 媒體下載 | |
| 唱條數 | 長條圖 | 表格分析 |
| UIUXPro | UI UX Pro | skill ui-ux-pro-max |

### ⚠️ 不要改的（Rick 刻意口語暱稱）
- **羊駝** = Ollama 的暱稱（logo 是羊駝）→ 保留
- **龍蝦** = OpenClaw 的暱稱（lobster）→ 保留

### ⚠️ BEXS 在 AI 影片裡是「三義字」，務必用上下文唯一字串逐一判斷
1. `BEXS 學院` → 保留（真的學院名）
2. `BEXS 漢堡店` / 示範品牌 → 保留（他 demo 時自己取的牌名）
3. 描述「引擎、跑模型、旁邊出現羊駝」那段的 BEXS → **Ollama**（被亂套）
4. 描述「很有趣很簡單、進階到 XX Code、可自己做翻譯」的 BEXS → **Claude**（被亂套）

> 判斷靠語意，不能全域 replace。用「這BEXS它可以自己做」這種**整句唯一字串**逐條換。

---

## 表 C — 黃金 / 交易影片：諧音錯字（BEXS 系統術語）

> 交易影片可直接跑 `ai_polish.py`（千問 + BEXS 術語表 + 防幻覺鎖），
> 跑完再人工補下面這些字典/千問漏抓的。完整表在
> `~/Desktop/下載YOUtube…rick-subtitle-tool/字幕引擎（不用點）/ai_polish.py` 的 GLOSSARY。

| 錯 | 對 |
|---|---|
| 斐波那期 / 斐波納氣 / 飛波那氣 | 斐波那契 |
| SOB（觸發SOB） | SOP |
| FBG / FB+G 跨行 | FVG |
| 側方向 | 測方向（用止損測方向） |
| 玩路（減少你的玩路） | 彎路 |
| 絕竅 | 訣竅 |
| 頭骰子 | 丟骰子 |
| 職順（正確的職順） | 止損 |
| 縮小手術 | 縮小手數（口數） |
| 隻能 / 就是隻有 | 只能 / 就是只有 |
| 屍體/肢體 | 實體 |
| 銀線 | 影線 |
| 巨學/舉學 | 拒絕 |

---

## 加新字的規矩
抓到新錯字 → 判斷屬於 B（AI/科技）還是 C（交易）→ 加一行 → 下次自動受惠。
B 表也可考慮回頭加進 ai_polish.py 的 GUARD_TERMS / 另開非交易 glossary。
