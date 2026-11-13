# ย้ายไปทำงานบนเครื่องอื่น (Moving to another machine)

โค้ดทั้งหมดอยู่ใน GitHub แล้ว ดังนั้นการย้ายเครื่องไม่ต้อง copy โฟลเดอร์ไปทั้งอัน —
เครื่องใหม่แค่ **โคลน repo ลงมา** แล้วทำงานต่อได้เลย

Repo: `https://github.com/thammarat-ai/no-stop-web-dev`

---

## 1. เตรียมเครื่องใหม่ (ทำครั้งเดียว)

ติดตั้งเครื่องมือพื้นฐาน:

| เครื่องมือ | ใช้ทำอะไร | ลิงก์ |
|---|---|---|
| Git | ดึง/ส่งโค้ด | https://git-scm.com/downloads |
| GitHub CLI (`gh`) *(ไม่บังคับ)* | ล็อกอินและสร้าง PR ง่ายขึ้น | https://cli.github.com |
| Node.js LTS *(ถ้าเป็นโปรเจกต์เว็บ)* | รัน dev server | https://nodejs.org |
| Editor เช่น VS Code | เขียนโค้ด | https://code.visualstudio.com |

ตรวจว่าติดตั้งแล้ว:

```bash
git --version
gh --version     # ถ้าติดตั้ง
node -v          # ถ้าใช้ Node
```

---

## 2. ตั้งชื่อและอีเมลของ Git

ทำ **ก่อน** commit แรก เพื่อให้ประวัติ commit ถูกต้อง (ควรใช้ชื่อ/อีเมลเดียวกันทุกเครื่อง):

```bash
git config --global user.name "thammarat-ai"
git config --global user.email "thammarat.th@up.ac.th"
```

> ถ้าใช้หลายบัญชี GitHub ให้ตั้งเฉพาะ repo แทน `--global` ได้

---

## 3. โคลน repo ลงเครื่องใหม่

```bash
mkdir -p ~/projects && cd ~/projects
git clone https://github.com/thammarat-ai/no-stop-web-dev.git
cd no-stop-web-dev
```

บน Windows ใช้ Git Bash หรือ PowerShell ก็ได้ path จะเป็นเช่น `C:\Users\<ชื่อ>\projects`

---

## 4. ล็อกอิน GitHub บนเครื่องใหม่

เลือกวิธีใดวิธีหนึ่ง

### วิธี A — GitHub CLI (แนะนำ, ง่ายที่สุด)

```bash
gh auth login
# เลือก: GitHub.com → HTTPS → Login with a web browser
```

จะได้ทั้งสิทธิ์ของ `git push` และคำสั่ง `gh` ใช้งานได้ทันที

### วิธี B — HTTPS + Git Credential Manager

แค่ push ครั้งแรก Git จะเด้งหน้าเบราว์เซอร์ให้ล็อกอิน แล้วจำรหัสไว้ให้:

```bash
git push
```

### วิธี C — SSH key

```bash
ssh-keygen -t ed25519 -C "thammarat.th@up.ac.th"   # กด Enter รับค่า default
cat ~/.ssh/id_ed25519.pub                          # copy คีย์นี้
```

เอาไปวางที่ https://github.com/settings/keys → **New SSH key** แล้วเปลี่ยน remote:

```bash
git remote set-url origin git@github.com:thammarat-ai/no-stop-web-dev.git
ssh -T git@github.com    # ควรเห็นข้อความยินดีต้อนรับ
```

> ⚠️ **ห้าม copy โฟลเดอร์ `~/.ssh` ทั้งอันข้ามเครื่องแบบไม่คิด** ถ้าต้องการย้าย ให้สร้างคีย์ใหม่บนเครื่องใหม่ปลอดภัยกว่า และลบคีย์เก่าที่ไม่ได้ใช้แล้วออกจาก GitHub

---

## 5. ติดตั้ง dependencies (ถ้าเป็นโปรเจกต์ Node)

```bash
npm install     # หรือ bun install / pnpm install ตามที่โปรเจกต์ใช้
```

`node_modules/` ถูก ignore ไว้แล้ว จึงไม่ขึ้น GitHub — ต้องติดตั้งใหม่ทุกเครื่องเสมอ

---

## 6. งานประจำวัน (วงจรเดียวกับทุกเครื่อง)

```bash
git pull                 # 1. ดึงของล่าสุดก่อนเริ่มงานเสมอ
# ... แก้โค้ด ...
git status               # 2. ดูว่าเปลี่ยนอะไรบ้าง
git add -A               # 3. เลือกไฟล์ที่จะ commit
git commit -m "อธิบายสั้น ๆ ว่าแก้อะไร"
git push                 # 4. ส่งขึ้น GitHub
```

**กฎสำคัญที่สุด:** `git pull` ก่อนเริ่มงานทุกครั้ง เพื่อลด conflict เวลาสลับเครื่อง

---

## 7. สลับไปมาระหว่างสองเครื่อง

1. ก่อนปิดเครื่อง: `git push` ให้เรียบร้อย (อย่าลืม commit ที่ค้างอยู่)
2. เปิดเครื่องใหม่: `git pull` ทันที
3. ถ้าลืม push แล้วไปแก้บนอีกเครื่อง จะเกิด conflict — แก้ได้แต่เสียเวลา ดังนั้นจำง่าย ๆ ว่า **"จบงาน = push, เริ่มงาน = pull"**

ดูว่ามีอะไรค้างอยู่:

```bash
git status
git log --oneline -5
git stash list     # เผื่อมีงานที่ stash ไว้ (stash ไม่ขึ้น GitHub)
```

> หมายเหตุ: `git stash` เก็บไว้ในเครื่องเท่านั้น **ไม่ติดไปกับ repo** ถ้ามีของสำคัญใน stash ให้ commit เป็น branch ชั่วคราวแทน

---

## 8. ไฟล์ที่ "ไม่ควร" ย้ายผ่าน Git

`.gitignore` ของโปรเจกต์กันไฟล์เหล่านี้ออกอยู่แล้ว:

```
node_modules/   .freebuff/   .env   .env.*   *.log   dist/   .DS_Store
```

สิ่งที่ต้องเตรียมเองบนเครื่องใหม่:

| ไฟล์ | วิธีจัดการ |
|---|---|
| `.env` (รหัส/คีย์ API) | **อย่าใส่ใน Git** — ส่งผ่าน password manager หรือสร้างใหม่จาก `.env.example` |
| คีย์ SSH / token | สร้างใหม่บนเครื่องใหม่ (ดูข้อ 4) |
| ข้อมูลในฐานข้อมูลท้องถิ่น | export/import แยกเอง ไม่ได้อยู่ใน Git |

ถ้าโปรเจกต์ยังไม่มี `.env.example` ควรสร้างไว้ระบุชื่อตัวแปร (ไม่ใส่ค่า) เพื่อให้เครื่องใหม่ตั้งค่าได้ถูก

---

## 9. Checklist สั้น ๆ สำหรับเครื่องใหม่

- [ ] ติดตั้ง Git (+ Node ถ้าต้องใช้)
- [ ] `git config --global user.name` / `user.email`
- [ ] `gh auth login` หรือตั้ง SSH key
- [ ] `git clone https://github.com/thammarat-ai/no-stop-web-dev.git`
- [ ] ติดตั้ง dependencies (`npm install`)
- [ ] สร้าง `.env` ของตัวเองจาก `.env.example`
- [ ] รันโปรเจกต์ให้ขึ้น แล้วลอง `git pull` / commit เล็ก ๆ / `git push` เพื่อยืนยันสิทธิ์

---

## สรุป

ย้ายเครื่อง = **ติดตั้ง Git → โคลน repo → ล็อกอิน → ติดตั้ง dependencies → ตั้งค่า `.env`**
งานทั้งหมดที่ commit แล้วจะตามไปเอง เพราะอยู่ใน GitHub ไม่ใช่ในเครื่อง
