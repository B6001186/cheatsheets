# Git Cheatsheet

## การตั้งค่า (Configuration)
```bash
git config --global user.name "ชื่อของคุณ"
git config --global user.email "email@example.com"
git config --global core.editor "code --wait"
git config --list
```

## เริ่มต้นโปรเจกต์ (Getting Started)
```bash
git init                          # สร้าง repo ใหม่
git clone <url>                   # โคลน repo จาก remote
git clone <url> <ชื่อโฟลเดอร์>    # โคลนไปยังโฟลเดอร์ที่กำหนด
```

## การแก้ไขและบันทึก (Basic Snapshotting)
```bash
git status                        # ดูสถานะไฟล์
git add <ไฟล์>                     # เพิ่มไฟล์ไปยัง staging
git add .                         # เพิ่มทุกไฟล์ไปยัง staging
git commit -m "ข้อความ"            # commit พร้อมข้อความ
git commit -am "ข้อความ"           # add + commit (เฉพาะไฟล์ที่ถูก track แล้ว)
git diff                          # ดูการเปลี่ยนแปลงที่ยังไม่ staged
git diff --staged                 # ดูการเปลี่ยนแปลงที่ staged แล้ว
git rm <ไฟล์>                      # ลบไฟล์และ stage การลบ
git mv <เก่า> <ใหม่>               # เปลี่ยนชื่อไฟล์และ stage
```

## ประวัติและการดูการเปลี่ยนแปลง (History & Inspection)
```bash
git log                           # ดูประวัติ commit
git log --oneline                 # ดูประวัติแบบย่อ
git log --graph --oneline --all  # ดูประวัติแบบกราฟ
git log -p                        # ดูประวัติพร้อม diff
git log --author="ชื่อ"           # ดู commit ของผู้ใช้ที่กำหนด
git show <commit-hash>            # ดูรายละเอียด commit
git blame <ไฟล์>                   # ดูว่าใครแก้ไขบรรทัดไหนล่าสุด
```

## การยกเลิกการเปลี่ยนแปลง (Undoing Changes)
```bash
git checkout -- <ไฟล์>             # ย้อนกลับไฟล์ที่ยังไม่ staged
git restore <ไฟล์>                 # ย้อนกลับไฟล์ (ทางเลือกใหม่)
git restore --staged <ไฟล์>       # unstage ไฟล์
git reset HEAD <ไฟล์>              # unstage ไฟล์ (แบบเก่า)
git reset --soft HEAD~1           # ย้อน commit ล่าสุด แต่เก็บการเปลี่ยนแปลงไว้
git reset --hard HEAD~1           # ย้อน commit ล่าสุดและลบการเปลี่ยนแปลง
git revert <commit-hash>          # สร้าง commit ใหม่เพื่อยกเลิก commit ที่指定
```

## Branch และ Merge
```bash
git branch                        # ดู branch ทั้งหมด
git branch <ชื่อ branch>           # สร้าง branch ใหม่
git branch -d <ชื่อ branch>        # ลบ branch
git branch -m <ชื่อเก่า> <ชื่อใหม่> # เปลี่ยนชื่อ branch
git checkout <ชื่อ branch>         # สลับไปยัง branch
git switch <ชื่อ branch>           # สลับ branch (ทางเลือกใหม่)
git checkout -b <ชื่อ branch>      # สร้างและสลับไป branch ใหม่
git switch -c <ชื่อ branch>        # สร้างและสลับ branch (ทางเลือกใหม่)
git merge <ชื่อ branch>            # merge branch ปัจจุบันกับ branch ที่ระบุ
git merge --abort                 # ยกเลิก merge ที่มี conflict
```

## การทำงานกับ Remote
```bash
git remote -v                     # ดู remote URL
git remote add origin <url>       # เพิ่ม remote
git remote remove <ชื่อ>           # ลบ remote
git push origin <branch>          # push ไปยัง remote
git push -u origin <branch>       # push และ set upstream
git push --all                    # push ทุก branch
git push origin --delete <branch> # ลบ branch บน remote
git pull                          # pull + merge (fetch + merge)
git pull --rebase                 # pull + rebase
git fetch                         # ดึงข้อมูลจาก remote แต่ไม่ merge
git clone <url>                   # โคลน repo
```

## Stashing
```bash
git stash                         # เก็บการเปลี่ยนแปลงชั่วคราว
git stash list                    # ดูรายการ stash ทั้งหมด
git stash pop                     # นำ stash ล่าสุดกลับมาใช้
git stash apply                   # นำ stash กลับมาใช้ (ไม่ลบจาก list)
git stash drop                    # ลบ stash ล่าสุด
git stash clear                   # ลบ stash ทั้งหมด
```

## Rebasing
```bash
git rebase <branch>               # rebase branch ปัจจุบันบน branch ที่ระบุ
git rebase -i HEAD~3              # rebase แบบ interactive 3 commit ล่าสุด
git rebase --continue             # ต่อ rebase หลังจากแก้ conflict
git rebase --abort                # ยกเลิก rebase
```

## Tagging
```bash
git tag                           # ดู tag ทั้งหมด
git tag <ชื่อ tag>                  # สร้าง tag
git tag -a <ชื่อ tag> -m "ข้อความ" # สร้าง annotated tag
git push origin <ชื่อ tag>         # push tag ไปยัง remote
git push origin --tags            # push ทุก tag
git tag -d <ชื่อ tag>              # ลบ tag
```

## การเปรียบเทียบ (Comparing)
```bash
git diff <branch1>..<branch2>     # ดูความแตกต่างระหว่าง 2 branch
git diff --stat                   # ดูสรุปไฟล์ที่เปลี่ยนแปลง
git diff HEAD~1                   # ดู diff จาก commit ก่อนหน้า
```

## การตั้งค่า .gitignore
```
# ตัวอย่าง .gitignore
node_modules/
.env
*.log
dist/
.DS_Store
```

## คำสั่งที่มีประโยชน์อื่น ๆ
```bash
git clean -fd                     # ลบไฟล์ที่ไม่ได้ถูก track
git cherry-pick <commit-hash>     # นำ commit จาก branch อื่นมาใช้
git archive --format=zip HEAD     # สร้าง zip ของ repo
git shortlog -sn                  # ดูจำนวน commit ของแต่ละคน
git count-objects -v              # ดูขนาด repo
```

## Git Flow (รูปแบบการใช้งานทั่วไป)
```bash
# main - โค้ดพร้อมใช้งานจริง
# develop - โค้ดสำหรับพัฒนา
# feature/<ชื่อ> - สร้าง feature ใหม่
# hotfix/<ชื่อ> - แก้บั๊กเร่งด่วน
# release/<ชื่อ> - เตรียมปล่อยเวอร์ชัน
```
