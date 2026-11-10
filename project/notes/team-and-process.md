# ProtEngPlus — ทีมและวิธีทำงาน

บันทึกนี้รวบรวมหลักฐานเรื่องการแบ่งงานและกระบวนการทำงานของทีม สำหรับใช้เขียนส่วน teamwork
ของรายงาน ซึ่งต้องบอกบทบาท ความครอบคลุมของงาน และความสมดุลของภาระงาน ข้อมูลตรวจจาก git history
เมื่อ 2026-11-10

ข้อควรระวังคือ git บอกได้แค่ว่าใคร commit อะไร บอกไม่ได้ว่าใครออกแบบ ใครรีวิว ใครทดสอบ
หรือใครคุยกับ stakeholder ตัวเลขในหน้านี้จึงใช้เป็นหลักฐานประกอบ ไม่ใช่คำตอบสำเร็จของการแบ่งงาน

## 1. สมาชิกที่ปรากฏใน git ตั้งแต่ 2026-08-01

| ชื่อใน git | จำนวน commit (ไม่นับ merge) | repo ที่แตะ |
| --- | --- | --- |
| BadLuckZ / Thanagorn Chaiyut | 424 | ทุก repo |
| art1722 | 61 | conductor, frontend, kubeflow, bff |
| Napongthorn | 13 | kubeflow, conductor, frontend |
| poongeta | 10 | frontend, conductor, bff |
| Thanagorn Chaiyut | 2 | manual-guides-2023 |

`ProtengCI` ที่ปรากฏใน `devops-k8s` จำนวน 182 commit เป็นบัญชีของ CI ไม่ใช่คน commit
เหล่านั้นคือการอัปเดต image tag อัตโนมัติตอน deploy

## 2. จำนวน commit แยกตาม repo

| repo | BadLuckZ | art1722 | Napongthorn | poongeta |
| --- | --- | --- | --- | --- |
| protengplus-frontend | 77 | 24 | 2 | 5 |
| proteng-kubeflow | 65 | 7 | 8 | – |
| proteng-user-mgmt | 55 | – | – | – |
| devops-k8s | 53 | – | – | – |
| manual-guides-2023 | 46 | – | – | – |
| devops-infra | 42 | – | – | – |
| proteng-conductor | 36 | 26 | 3 | 3 |
| proteng-bff | 24 | 4 | 2 | – |

สิ่งที่ตารางนี้บอกคือภาระงานกระจุกตัวอยู่ที่คนเดียวค่อนข้างมาก และงาน infrastructure ทั้งหมด
คือ `devops-k8s`, `devops-infra` และเอกสาร มีผู้ดูแลคนเดียว ส่วนงาน feature กระจายอยู่ใน
conductor กับ frontend ซึ่งมีคนร่วมหลายคน ถ้ารายงานต้องอธิบายความสมดุลของภาระงาน ควรอธิบาย
ด้วยขอบเขตความรับผิดชอบ ไม่ใช่ด้วยจำนวน commit เพราะ commit หนึ่งก้อนมีขนาดงานไม่เท่ากัน

## 3. การแบ่งงานตาม feature ที่ปรากฏในแผน

ไฟล์ `resources-2026/plan/feature-details.md` ระบุชื่อผู้รับผิดชอบไว้ในวงเล็บของหัวข้อบางข้อ

| feature | ผู้รับผิดชอบที่ระบุไว้ |
| --- | --- |
| BLX1 Evaluation Plugin Architecture | Art |
| BLF1 คุมช่วงตำแหน่งที่ mutate ได้ | Gong |
| BLF7 Wet-lab Result Loop | Poon |

ชื่อเล่นเหล่านี้น่าจะตรงกับ git handle art1722, Napongthorn และ poongeta ตามลำดับ [unverified]
feature ข้ออื่นไม่ได้ระบุผู้รับผิดชอบไว้

## 4. กระบวนการทำงาน

กฎที่ทีมใช้ร่วมกันทุก repo เขียนไว้ใน `CONTRIBUTING.md` ของ `manual-guides-2023`
และสรุปอยู่ใน `CLAUDE.md` ของแต่ละ repo สาระสำคัญมีดังนี้

งานทุกชิ้นเริ่มจาก issue หรือ sub-issue ที่มีแผน commit ก่อน แล้วแตก branch จาก `dev`
โดยตั้งชื่อเป็นหมายเลข issue ตามด้วยคำอธิบายสั้น ๆ เช่น `150-evaluation-apis`
commit เขียนเป็น Conventional Commits ภาษาอังกฤษ หนึ่งเรื่องต่อหนึ่ง commit
PR เปิดเข้า `dev` และกรอกตามแม่แบบของ repo นั้นเป็นภาษาไทย ห้าม push เข้า `main` ของ repo
service โดยตรงเพราะจะ deploy ขึ้น production ทันที

ทุก repo ยกเว้น frontend มีคำสั่งชุดเดียวกัน คือ `make setup`, `make run`, `make check`
และ `make fmt` โดย `make check` ตรวจสิ่งเดียวกับที่ CI ตรวจ ส่วน frontend ใช้ npm scripts
ที่ทำหน้าที่เดียวกัน ทุก repo ติดตั้ง hook ในเครื่องที่ตรวจ format และ lint ตอน commit
และตรวจ build กับ test ตอน push แต่เพราะ hook ถูกข้ามได้ด้วย flag หรือตอน merge commit
CI จึงเป็นด่านสุดท้ายที่เชื่อถือได้

กฎเพิ่มเติมที่ทีมบังคับกับตัวเองคือฟังก์ชัน Go ใหม่ทุกตัวต้องมีไฟล์ test ในแพ็กเกจเดียวกัน
และอยู่ใน commit เดียวกัน ห้ามใส่ข้อมูล infrastructure หรือ credential ลง repo ที่เป็น public
และห้ามใช้ emoji ในทุกที่ รวมถึง commit message และ PR

## 5. เอกสารของทีม

เอกสารระดับระบบทั้งหมดอยู่ใน `manual-guides-2023` ซึ่งถูกเขียนใหม่ทั้งเล่มในเดือนกันยายน 2026
และจัดหมวดตามแนวคิด Diataxis คือแยกตามจุดประสงค์ของผู้อ่าน ได้แก่ tutorials สำหรับทำตามจนสำเร็จ
หนึ่งรอบ, how-to สำหรับทำงานหนึ่งอย่างให้เสร็จ, reference สำหรับเปิดหาข้อเท็จจริง,
explanation สำหรับทำความเข้าใจเหตุผล และ incidents สำหรับ postmortem

เอกสารชุดนี้มี CI ตรวจ markdownlint และตรวจว่าลิงก์ทุกเส้นยังชี้ไปที่ของจริง และมีกฎว่า
ต้องเขียนภาษาไทยเป็นประโยคเต็ม ต้องกำกับวันที่ตรวจล่าสุดของทุกหน้าที่เป็นขั้นตอนลงมือทำ
และต้องกำกับ `[unverified]` กับข้อที่ยืนยันไม่ได้

สิ่งที่ใช้อ้างได้ในรายงานคือทีมมีเอกสารส่งต่อที่ใช้งานได้จริง ไม่ใช่เอกสารที่เขียนเพื่อส่งงาน
โดยมีทั้ง runbook สำหรับอาการที่เคยเกิด, postmortem ของเหตุการณ์จริง และหน้าที่อธิบายเหตุผล
ของการตัดสินใจเชิงออกแบบแต่ละข้อ
