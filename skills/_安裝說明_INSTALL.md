# 📥 安裝說明
每個技能用「中文名-英文名」資料夾包著。裡面那層**英文資料夾**才是技能包(含 SKILL.md)。
## 一次全裝
```bash
for d in skills/*/*/; do cp -r "$d" ~/.claude/skills/; done
```
## 只裝一個
```bash
cp -r skills/檔案整理-file-organizer/file-organizer ~/.claude/skills/
```
裝完重開 Claude Code,用講的就會觸發。
