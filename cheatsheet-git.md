# Git Cheatsheet

## การตั้งค่า (Configuration)
```bash
git config --global user.name "ชื่อของคุณ"          # ตั้งชื่อผู้ใช้ (global = ทุก repo)
git config --global user.email "email@example.com" # ตั้ง email
git config --local user.name "ชื่อ"                 # ตั้งค่าเฉพาะ repo นี้
git config --system user.name "ชื่อ"                # ตั้งค่าเฉพาะเครื่อง (ทั้งระบบ)
git config --global core.editor "code --wait"       # ตั้ง editor สำหรับพิมพ์ commit message
git config --global core.autocrlf true             # จัดการ line ending (Windows)
git config --global core.autocrlf input            # จัดการ line ending (Mac/Linux)
git config --global init.defaultBranch main        # ตั้งชื่อ branch เริ่มต้นเป็น main
git config --global alias.co checkout              # สร้าง alias คำสั่งลัด (co = checkout)
git config --global alias.lg "log --oneline --graph" # สร้าง alias แบบหลายคำ
git config --global color.ui auto                  # เปิดสีใน terminal
git config --global user.signingkey <key>          # ตั้ง GPG key สำหรับ sign commit
git config --global commit.gpgsign true            # ให้ sign ทุก commit อัตโนมัติ
git config --global push.autoSetupRemote true      # push แล้ว set upstream อัตโนมัติ
git config --list                                  # ดูค่าที่ตั้งไว้ทั้งหมด
git config --list --show-origin                    # ดูค่าพร้อมที่มาของค่า
git config --get user.name                         # ดูค่าที่กำหนดแค่ข้อเดียว
git config --unset user.name                       # ลบค่านั้นออก
git config --global --edit                         # แก้ config ผ่าน editor
```

## เริ่มต้นโปรเจกต์ (Getting Started)
```bash
git init                          # สร้าง repo ใหม่ในโฟลเดอร์ปัจจุบัน
git init <ชื่อโฟลเดอร์>             # สร้าง repo ใหม่ในโฟลเดอร์ที่กำหนด
git init --bare <ชื่อ>             # สร้าง repo แบบ bare (ใช้เป็นเซิร์ฟเวอร์กลาง)
git clone <url>                   # โคลน repo จาก remote
git clone <url> <ชื่อโฟลเดอร์>     # โคลนไปยังโฟลเดอร์ที่กำหนด
git clone --branch <branch> <url> # โคลนเฉพาะ branch ที่กำหนด
git clone --depth 1 <url>         # โคลนแบบ shallow (เฉพาะ commit ล่าสุด)
git clone --recursive <url>       # โคลนพร้อม submodule ทั้งหมด
```

## การแก้ไขและบันทึก (Basic Snapshotting)
```bash
git status                        # ดูสถานะไฟล์
git status -s                     # ดูสถานะแบบย่อ (short)
git status -sb                    # ดูสถานะแบบย่อพร้อมชื่อ branch
git add <ไฟล์>                     # เพิ่มไฟล์ไปยัง staging
git add .                         # เพิ่มทุกไฟล์ในโฟลเดอร์ปัจจุบันไปยัง staging
git add -A                        # เพิ่มทุกไฟล์ (รวมไฟล์ที่ลบ) ไปยัง staging
git add -u                        # เพิ่มเฉพาะไฟล์ที่ถูก track แล้ว
git add -p <ไฟล์>                  # เลือก stage เป็นบางส่วน (hunk) ทีละส่วน
git add -i                        # โหมด interactive สำหรับ staging
git add -f <ไฟล์>                  # บังคับ add ไฟล์ที่อยู่ใน .gitignore
git commit -m "ข้อความ"            # commit พร้อมข้อความ
git commit -am "ข้อความ"           # add + commit (เฉพาะไฟล์ที่ถูก track แล้ว)
git commit -m "หัวข้อ" -m "รายละเอียด" # commit พร้อมข้อความหลายบรรทัด
git commit --amend                # แก้ commit ล่าสุด (ข้อความหรือเพิ่มไฟล์)
git commit --amend --no-edit      # แก้ commit ล่าสุดโดยไม่เปลี่ยนข้อความ
git commit --fixup <hash>         # สร้าง commit แก้ไขเพื่อใช้กับ autosquash
git diff                          # ดูการเปลี่ยนแปลงที่ยังไม่ staged
git diff --staged                 # ดูการเปลี่ยนแปลงที่ staged แล้ว (มี __cached)
git diff HEAD                     # ดูการเปลี่ยนแปลงทั้งหมดตั้งแต่ commit ล่าสุด
git diff <ไฟล์>                    # ดูการเปลี่ยนแปลงของไฟล์เฉพาะ
git rm <ไฟล์>                      # ลบไฟล์และ stage การลบ
git rm --cached <ไฟล์>             # หยุด track ไฟล์แต่เก็บไฟล์ไว้ในเครื่อง
git rm -r <โฟลเดอร์>               # ลบโฟลเดอร์และ stage การลบ
git mv <เก่า> <ใหม่>               # เปลี่ยนชื่อไฟล์และ stage
```

## ประวัติและการดูการเปลี่ยนแปลง (History & Inspection)
```bash
git log                           # ดูประวัติ commit
git log --oneline                 # ดูประวัติแบบย่อ (หนึ่งบรรทัดต่อ commit)
git log --graph --oneline --all   # ดูประวัติแบบกราฟทุก branch
git log -p                        # ดูประวัติพร้อม diff
git log --stat                    # ดูประวัติพร้อมสรุปไฟล์ที่เปลี่ยน
git log -n 5                      # ดู 5 commit ล่าสุด
git log --author="ชื่อ"            # ดู commit ของผู้ใช้ที่กำหนด
git log --since="2 weeks ago"     # ดู commit ตั้งแต่ช่วงเวลาที่กำหนด
git log --until="2024-01-01"      # ดู commit จนถึงวันที่กำหนด
git log --grep="คำค้น"             # ค้นหา commit จากข้อความ
git log --all --oneline           # ดู commit จากทุก branch
git log --oneline --all --graph   # ดูประวัติทุก branch แบบกราฟ
git log --follow -- <ไฟล์>         # ดูประวัติไฟล์รวมถึงการย้าย/เปลี่ยนชื่อ
git log -p -- <ไฟล์>               # ดูประวัติการแก้ไขของไฟล์เฉพาะ
git log -S "คำค้น"                 # ค้นหา commit ที่เพิ่ม/ลบข้อความนั้น (pickaxe)
git log --abbrev-commit           # ดู commit แบบ hash ย่อ
git log --pretty=format:"%h %an %s" # กำหนดรูปแบบการแสดงผลเอง
git show <commit-hash>            # ดูรายละเอียด commit (รวม diff)
git show HEAD                     # ดูรายละเอียด commit ล่าสุด
git show HEAD~1                   # ดู commit ก่อนหน้าหนึ่งตัว
git show <tag>                    # ดู commit ที่ tag ชี้ไป
git blame <ไฟล์>                   # ดูว่าใครแก้ไขบรรทัดไหนล่าสุด
git blame -L 10,20 <ไฟล์>          # ดู blame เฉพาะบรรทัดที่ 10-20
git grep "คำค้น"                   # ค้นหาคำในไฟล์ที่ถูก track
git grep -n "คำค้น"                # ค้นหาแบบแสดงเลขบรรทัด
```

## การยกเลิกการเปลี่ยนแปลง (Undoing Changes)
```bash
git checkout -- <ไฟล์>             # ย้อนกลับไฟล์ที่ยังไม่ staged
git restore <ไฟล์>                 # ย้อนกลับไฟล์ (ทางเลือกใหม่)
git restore --source=<hash> <ไฟล์> # ย้อนไฟล์กลับไปตาม commit ที่กำหนด
git restore --staged <ไฟล์>        # unstage ไฟล์ (เอาไฟล์ออกจาก staging)
git restore --worktree <ไฟล์>      # ย้อนเฉพาะไฟล์ใน working directory
git reset HEAD <ไฟล์>              # unstage ไฟล์ (แบบเก่า)
git reset --soft HEAD~1            # ย้อน commit ล่าสุด แต่เก็บการเปลี่ยนแปลงไว้ใน staging
git reset --mixed HEAD~1           # ย้อน commit ล่าสุด เก็บการเปลี่ยนแปลง (ค่าเริ่มต้น)
git reset --hard HEAD~1            # ย้อน commit ล่าสุดและลบการเปลี่ยนแปลงทั้งหมด
git reset HEAD~3                   # ย้อนกลับ 3 commit ล่าสุด
git reset --hard <commit-hash>     # ย้อนกลับไปยัง commit ที่กำหนด (ลบการเปลี่ยนทั้งหมด)
git revert <commit-hash>           # สร้าง commit ใหม่เพื่อยกเลิก commit ที่กำหนด
git revert --no-commit <hash>      # revert หลายๆ ตัวโดยไม่ commit ทันที
git clean -n                       # ดูรายการไฟล์ที่ไม่ได้ track (dry-run)
git clean -f                       # ลบไฟล์ที่ไม่ได้ถูก track
git clean -fd                      # ลบไฟล์และโฟลเดอร์ที่ไม่ได้ถูก track
git clean -fdx                     # ลบรวมถึงไฟล์ที่อยู่ใน .gitignore
git reflog                         # ดูประวัติการเคลื่อนไหวของ HEAD (กู้คืน commit ที่ถูกลบ)
git reflog --all                   # ดู reflog ของทุก branch
git reflog --oneline               # ดู reflog แบบย่อ
```

## Branch และ Merge
```bash
git branch                        # ดู branch ทั้งหมดในเครื่อง
git branch -a                     # ดู branch ทั้งหมดรวม remote
git branch -r                     # ดูเฉพาะ branch ฝั่ง remote
git branch -v                     # ดู branch พร้อม commit ล่าสุด
git branch --merged               # ดู branch ที่ merge เข้าไปแล้ว
git branch --no-merged            # ดู branch ที่ยังไม่ถูก merge
git branch <ชื่อ branch>           # สร้าง branch ใหม่
git branch -d <ชื่อ branch>        # ลบ branch (ถูกลบได้เฉพาะที่ merge แล้ว)
git branch -D <ชื่อ branch>        # บังคับลบ branch (แม้ยังไม่ merge)
git branch -m <ชื่อเก่า> <ชื่อใหม่> # เปลี่ยนชื่อ branch
git branch -m <ชื่อใหม่>           # เปลี่ยนชื่อ branch ปัจจุบัน
git branch --set-upstream-to=origin/<branch> # ตั้ง upstream ให้ branch
git branch --unset-upstream       # ยกเลิก upstream
git checkout <ชื่อ branch>         # สลับไปยัง branch
git checkout <commit-hash>        # สลับไปยัง commit (detached HEAD)
git checkout -                    # สลับกลับไป branch ก่อนหน้า
git switch <ชื่อ branch>           # สลับ branch (ทางเลือกใหม่)
git switch -c <ชื่อ branch>        # สร้างและสลับไป branch ใหม่ (ทางเลือกใหม่)
git switch -                      # สลับกลับไป branch ก่อนหน้า
git checkout -b <ชื่อ branch>      # สร้างและสลับไป branch ใหม่
git checkout -b <ใหม่> origin/<เก่า> # สร้าง branch จาก remote
git merge <ชื่อ branch>            # merge branch ที่ระบุเข้ามาใน branch ปัจจุบัน
git merge --no-ff <ชื่อ branch>    # merge โดยบังคับสร้าง merge commit
git merge --squash <ชื่อ branch>   # merge โดยรวม commit ทั้งหมดเป็น commit เดียว (ไม่ commit)
git merge --ff-only <ชื่อ branch>  # merge เฉพาะแบบ fast-forward เท่านั้น
git merge --abort                 # ยกเลิก merge ที่มี conflict
git merge --continue              # ต่อ merge หลังจากแก้ conflict แล้ว
git mergetool                     # เปิดเครื่องมือแก้ conflict
```

## การทำงานกับ Remote
```bash
git remote -v                     # ดู remote URL
git remote add origin <url>       # เพิ่ม remote
git remote show origin            # ดูรายละเอียด remote
git remote rename <เก่า> <ใหม่>    # เปลี่ยนชื่อ remote
git remote remove <ชื่อ>           # ลบ remote
git remote set-url origin <url>   # เปลี่ยน URL ของ remote
git remote prune origin           # ลบ remote branch ที่ถูกลบไปแล้วบนเซิร์ฟเวอร์
git push origin <branch>          # push ไปยัง remote
git push -u origin <branch>       # push และ set upstream
git push --all                    # push ทุก branch
git push origin --delete <branch> # ลบ branch บน remote
git push --tags                   # push ทุก tag ขึ้น remote
git push --force                  # บังคับ push (อันตราย - แทนที่ประวัติ remote)
git push --force-with-lease       # push แบบบังคับแต่ปลอดภัยกว่า (ถ้าไม่มีใคร push ใหม่)
git push --prune origin           # push และลบ branch ที่หายไปบน local
git pull                          # pull + merge (fetch + merge)
git pull --rebase                 # pull + rebase
git pull --ff-only                # pull แบบ fast-forward เท่านั้น
git pull origin <branch>          # pull จาก branch ที่กำหนด
git fetch                         # ดึงข้อมูลจาก remote แต่ไม่ merge
git fetch origin                  # fetch จาก remote origin
git fetch --all                   # fetch จากทุก remote
git fetch --prune                 # fetch และลบ reference ที่หายไป
git fetch origin <branch>         # fetch เฉพาะ branch
git clone <url>                   # โคลน repo
```

## Stashing
```bash
git stash                         # เก็บการเปลี่ยนแปลงชั่วคราว
git stash push -m "ข้อความ"        # stash พร้อมข้อความ
git stash -u                      # stash รวมไฟล์ที่ยังไม่ได้ track
git stash list                    # ดูรายการ stash ทั้งหมด
git stash show stash@{0}          # ดูการเปลี่ยนแปลงใน stash
git stash pop                     # นำ stash ล่าสุดกลับมาใช้ (และลบออกจาก list)
git stash apply                   # นำ stash กลับมาใช้ (ไม่ลบจาก list)
git stash apply stash@{2}         # นำ stash อันที่ 2 กลับมาใช้
git stash drop                    # ลบ stash ล่าสุด
git stash drop stash@{1}          # ลบ stash อันที่ระบุ
git stash clear                   # ลบ stash ทั้งหมด
git stash branch <ชื่อ branch>     # สร้าง branch ใหม่จาก stash
git stash pop stash@{0}           # นำ stash ล่าสุดกลับมาใช้พร้อมลบ
```

## Rebasing
```bash
git rebase <branch>               # rebase branch ปัจจุบันบน branch ที่ระบุ
git rebase -i HEAD~3              # rebase แบบ interactive 3 commit ล่าสุด
git rebase --continue             # ต่อ rebase หลังจากแก้ conflict
git rebase --skip                 # ข้าม commit ที่มีปัญหา
git rebase --abort                # ยกเลิก rebase
git rebase --onto <base> <เริ่ม> <สิ้นสุด> # rebase ช่วง commit ไปยัง base ใหม่
git rebase -i <base-commit>       # rebase interactive ตั้งแต่ commit ที่กำหนด
```

## Tagging
```bash
git tag                           # ดู tag ทั้งหมด
git tag -l "v1.*"                 # ดู tag ที่ตรงตามรูปแบบ
git tag <ชื่อ tag>                  # สร้าง lightweight tag
git tag -a <ชื่อ tag> -m "ข้อความ" # สร้าง annotated tag (มีข้อมูลผู้สร้าง)
git tag <ชื่อ tag> <commit-hash>   # สร้าง tag ที่ commit เฉพาะ
git push origin <ชื่อ tag>         # push tag ไปยัง remote
git push origin --tags            # push ทุก tag
git tag -d <ชื่อ tag>              # ลบ tag ในเครื่อง
git push origin --delete <tag>    # ลบ tag บน remote
git checkout <ชื่อ tag>            # สลับไปยัง tag (detached HEAD)
git show <ชื่อ tag>                # ดูข้อมูล tag
```

## การเปรียบเทียบ (Comparing)
```bash
git diff <branch1>..<branch2>     # ดูความแตกต่างระหว่าง 2 branch
git diff <branch1> <branch2>      # ดูความแตกต่างระหว่าง 2 branch (อีกแบบ)
git diff --stat                   # ดูสรุปไฟล์ที่เปลี่ยนแปลง
git diff --name-only              # ดูเฉพาะชื่อไฟล์ที่เปลี่ยนแปลง
git diff --name-status            # ดูชื่อไฟล์พร้อมสถานะ (M/A/D)
git diff HEAD~1                   # ดู diff จาก commit ก่อนหน้า
git diff HEAD~1..HEAD             # ดู diff ระหว่าง 2 commit
git diff --cached                 # ดู diff ของไฟล์ที่ staged (เหมือน --staged)
git diff --color-words            # ดู diff ระดับคำ (ไม่ใช่ระดับบรรทัด)
git diff <hash1> <hash2>          # เปรียบเทียบระหว่าง 2 commit
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

## คำสั่งที่มีประโยชน์อื่น ๆ (Advanced)
```bash
git cherry-pick <commit-hash>     # นำ commit จาก branch อื่นมาใช้
git cherry-pick -n <hash>         # นำ commit มาใช้แต่ยังไม่ commit
git cherry-pick -m 1 <hash>       # นำ merge commit มาใช้ (เลือก parent ที่ 1)
git archive --format=zip HEAD     # สร้าง zip ของ repo
git shortlog -sn                  # ดูจำนวน commit ของแต่ละคน
git shortlog -sne                 # ดูจำนวน commit พร้อม email
git count-objects -v              # ดูขนาด repo
git gc                            # เก็บกวาดและบีบอัดข้อมูล repo
git fsck                          # ตรวจสอบความถูกต้องของ database
git verify-pack <ไฟล์>             # ตรวจสอบแพ็คไฟล์ภายใน
git describe --tags               # หา tag ที่ใกล้กับ commit ปัจจุบันที่สุด
git describe --always             # แสดง hash ถ้าไม่มี tag
git bisect start                  # เริ่มการหา commit ที่ทำให้บั๊ก (binary search)
git bisect bad                    # บอกว่าจุดปัจจุบันมีบั๊ก
git bisect good <hash>            # บอกว่าจุดนี้ไม่มีบั๊ก
git bisect reset                  # จบการ bisect กลับสู่ปกติ
```

## Submodule (การฝัง repo ใน repo)
```bash
git submodule add <url>           # เพิ่ม submodule
git submodule init                # ตั้งค่า submodule
git submodule update              # ดาวน์โหลด/อัปเดต submodule
git submodule update --init --recursive # อัปเดต submodule ทั้งหมดแบบซ้อนกัน
git submodule status              # ดูสถานะของ submodule
git submodule foreach <คำสั่ง>     # รันคำสั่งในทุก submodule
git submodule deinit -f <ชื่อ>     # เอา submodule ออก
git submodule sync                # ซิงก์ URL ของ submodule
```

## Worktree (หลาย worktree จาก repo เดียว)
```bash
git worktree add <โฟลเดอร์>        # สร้าง worktree ใหม่
git worktree add <โฟลเดอร์> <branch> # สร้าง worktree พร้อม branch
git worktree list                 # ดู worktree ทั้งหมด
git worktree remove <โฟลเดอร์>     # ลบ worktree
git worktree prune                # ลบข้อมูล worktree ที่ถูกลบไปแล้ว
```

## Git Flow (รูปแบบการใช้งานทั่วไป)
```bash
# main - โค้ดพร้อมใช้งานจริง
# develop - โค้ดสำหรับพัฒนา
# feature/<ชื่อ> - สร้าง feature ใหม่
# hotfix/<ชื่อ> - แก้บั๊กเร่งด่วน
# release/<ชื่อ> - เตรียมปล่อยเวอร์ชัน
```

## ประเภทของ `git reset` (ผลต่างของ 3 โหมด)
```bash
git reset --soft <ref>   # ย้าย HEAD กลับ แต่เก็บการเปลี่ยนแปลงใน staging
git reset --mixed <ref>  # ย้าย HEAD กลับ เก็บการเปลี่ยนแปลงใน working dir (ค่าเริ่มต้น)
git reset --hard <ref>   # ย้าย HEAD กลับ และลบการเปลี่ยนแปลงทั้งหมด (อันตราย!)
```
