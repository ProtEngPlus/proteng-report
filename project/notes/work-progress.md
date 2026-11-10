# ProtEngPlus — ความคืบหน้าและงานที่เหลือ

บันทึกนี้ตอบว่าตอนนี้ทำอะไรไปแล้วและเหลืออะไร สถานะทั้งหมดเป็นของวันที่ 2026-11-10
ตรวจจาก git history และเอกสารของทีม ถ้าเวลาผ่านไปแล้วหลายสัปดาห์ ให้ตรวจซ้ำก่อนใช้อ้างในรายงาน

บันทึกรายสัปดาห์ที่ละเอียดกว่านี้อยู่ใน `manual-guides-2023/resources-2026/weekly-reports/`
โดยมีฉบับ 2026-08-20 ถึง 2026-09-11 ที่ทีมเขียนสด และฉบับ 2026-09-20 ถึง 2026-11-08
ที่เรียบเรียงย้อนหลังจาก git history

## 1. ขอบเขตงานของรุ่นปัจจุบัน

ระบบถูกสร้างโดยรุ่นก่อนและส่งเป็น capstone ไปแล้ว งานของรุ่นนี้แบ่งเป็นสามกอง

กองแรกคือการกู้และทำให้ระบบใช้งานได้จริง ตอนรับช่วงต่อ ระบบล่มเงียบมาแล้ว 13 เดือน
เพราะ container ระดับ host สามตัวหยุดทำงานตั้งแต่ VM reboot ครั้งหนึ่งในเดือนกรกฎาคม 2025
และไม่มีใครสังเกต TLS certificate ก็หมดอายุตามไปด้วย งานกองนี้คือกู้ระบบ แยก environment
dev กับ production ออกจากกันจริง ทำ GitOps ให้ทำงาน และต่อ CI/CD ให้ deploy อัตโนมัติตาม branch

กองที่สองคือ production-readiness คือปิดช่องโหว่และแก้จุดเปราะที่พบจากการอ่านโค้ดทั้งระบบ
รวมเป็นรายการบั๊ก 25 ข้อรหัส BLB1 ถึง BLB25 ครอบคลุม auth, flow สมัครสมาชิก, ความทนทาน
ของ conductor และการต่อฐานข้อมูล, ขั้นตอน BLAST, การไม่มี test baseline และ UI

กองที่สามคือ feature ใหม่ตามที่ stakeholder ขอ ซึ่งสรุปจากการประชุมเมื่อ 2026-09-04
ประกอบด้วย prerequisite หนึ่งข้อรหัส BLX1 คือสถาปัตยกรรม evaluation plugin, feature เจ็ดข้อ
รหัส BLF1 ถึง BLF7 และ improvement หนึ่งข้อรหัส BLE1 รายละเอียดทุกข้ออยู่ใน
`manual-guides-2023/resources-2026/plan/bug-details.md` และ `feature-details.md`

มีการประชุมกับ stakeholder อีกครั้งเมื่อ 2026-10-09 แต่ไม่มีบันทึกการประชุมอยู่ใน repo ใด
ผลของการประชุมนั้นต่อขอบเขตงานจึงยังเติมไม่ได้ [TBD]

## 2. งานที่เสร็จแล้ว

หัวข้อนี้นับเฉพาะงานที่ merge เข้า branch `dev` แล้ว

**การกู้ระบบและโครงสร้างพื้นฐาน** กู้ container ที่ตายและตั้ง restart policy, ต่ออายุและ
ทำให้ certificate ต่ออายุเองได้, แยก production ออกจาก dev, เปิดใช้ Argo CD จริงเป็นครั้งแรก
ในโปรเจกต์, ทำ CI/CD ให้ deploy อัตโนมัติตาม branch ครบทุก service, เปลี่ยนไปใช้ Sealed Secrets,
ย้าย ML ทั้งห้า service ขึ้น GPU VM, ทำ auto-restart และ watchdog ให้ consumer, สร้าง health check
ที่ตรวจจากฝั่ง broker พร้อมแจ้งเตือนเข้า Discord, ทำ auto-deploy ของ ML ฝั่ง dev,
และเพิ่ม CI ที่สแกน secret ทุกไฟล์

**บัญชีผู้ใช้และความปลอดภัยของ session** ทำให้อีเมลไม่แยกตัวพิมพ์ใหญ่เล็กพร้อม backfill
ข้อมูลเก่า, รวมการสมัครกับการส่งอีเมลยืนยันเป็นขั้นตอนเดียว, เพิ่ม password policy พร้อมคำแนะนำ
บนหน้าเว็บ, จำกัดการส่งอีเมลยืนยันซ้ำไว้ที่ 60 วินาที, เขียน email template ใหม่ทั้งชุด,
เพิ่ม route guard ให้หน้าที่ต้องมี token, ลบการเขียน SMTP ลง log, และย้าย session ไปเป็น
httpOnly cookie พร้อม refresh token และการ refresh เงียบเมื่อเจอ 401

**ความถูกต้องและความทนทานของ pipeline** ให้การต่อ MongoDB มี retry พร้อม backoff และทุก query
มี timeout, เพิ่ม graceful shutdown ให้ conductor, เปลี่ยนไปใช้ manual ack พร้อม publisher
confirm และ message แบบ persistent, ตรวจ message ที่มาผิดลำดับ stage แล้วให้ job fail
แทนการเงียบ, ทำให้ ML consumer ทุกตัวรองานที่กำลังรันให้จบก่อนปิดตัว, และวาง test baseline
พร้อมตั้ง CI ให้ build และ deploy ได้ต่อเมื่อ test ผ่าน

**ขั้นตอน query** ระบุตัวตนต่อ NCBI ด้วย email และ tool ตามกติกา, แก้ filter ของ homolog
ให้คิด query cover เทียบความยาวของ query และกรองด้วยความยาวจริงของ subject, ส่ง sequence
ของ hit แบบเต็ม, และ pin dependency ทั้งหมดตามที่รันอยู่บนเครื่องจริง

**feature ใหม่ที่เสร็จแล้ว** การคุมช่วงตำแหน่งที่ mutate ได้พร้อมจำนวนตำแหน่งต่ำสุดและสูงสุด
(BLF1), การบันทึกผลแล็บจริงของ mutant ที่ระบบเสนอแล้วเทียบกับค่าที่ทำนาย (BLF7),
สถาปัตยกรรม evaluation plugin ทั้งฝั่ง orchestration, API และ UI (BLX1), การใช้โมเดล ESM
จริงใน pipeline, assay type กับ Assay Library, และการจำกัดให้ใช้กรดอะมิโนมาตรฐาน 20 ตัว

## 3. งานที่กำลังทำหรือค้างอยู่

| งาน | สถานะ |
| --- | --- |
| กำหนดเวลาสูงสุดของการค้น BLAST ที่ NCBI และแก้ logger ที่ถูกทับ | อยู่บน branch ของตัวเอง ยังไม่ merge เข้า `dev` |
| release `dev` เข้า `main` เพื่อขึ้น production | ยังไม่ทำ ไม่มี release ตั้งแต่ 2026-10-13 |
| auto-deploy ของ ML ฝั่ง production | ยังไม่ทำ ต้อง release ขึ้น production ก่อนหนึ่งครั้ง |
| เปลี่ยน SMTP app password | ยังไม่ทำ ค่าเดิมเคยหลุดไปอยู่ใน log |
| rate limit ของ login, forgot password และ change password (BLB25) | ยังไม่ลงมือ แผนละเอียดเขียนไว้แล้ว |

ระยะห่างระหว่าง `dev` กับ `main` ณ วันที่เขียนคือ frontend 57 commit, kubeflow 50 commit,
conductor 47 commit, bff 20 commit และ user-mgmt 11 commit งานทั้งหมดในหัวข้อ 2
จึงยังไม่ถึงมือผู้ใช้จริง

## 4. feature ที่วางแผนไว้แต่ยังไม่ได้ทำ

ทุกข้อมีรายละเอียดเต็มใน `resources-2026/plan/feature-details.md` ซึ่งเขียนเป็นโครงเดียวกัน
คือ background, current, implementation, expected และ note

| รหัส | feature | สรุป |
| --- | --- | --- |
| BLF2 | Stability Service (ΔΔG) | plugin ประเมินว่า mutant ยังพับตัวได้และเสถียรแค่ไหนเทียบกับ wild-type โดยเริ่มจากโหมดที่ไม่ใช้โครงสร้างสามมิติ |
| BLF3 | DLKcat Prediction | plugin ทำนาย kcat ด้วยโมเดล DLKcat ต้องเพิ่ม input ให้ผู้ใช้กรอก substrate เป็น SMILES |
| BLF4 | Mutant Top X% Selection | ให้ mutation คืน mutant มากกว่าเส้นสุดท้ายของแต่ละ trajectory แล้วคัดเฉพาะกลุ่มที่ดีที่สุด พร้อมคุมความหลากหลายไม่ให้กระจุกใน trajectory เดียว |
| BLF5 | เพิ่มอัลกอริทึมให้เลือก | เพิ่ม Site-Saturation Mutagenesis ให้ขั้นตอน mutation และ Random Forest ให้ขั้นตอน top model |
| BLF6 | ฝึก top model ต่อเมื่อมีผลแล็บใหม่ | รวมผลแล็บเก่ากับใหม่แล้วฝึกใหม่ โดยใช้ representation ของ job เดิมซ้ำ จึงข้ามสองขั้นตอนแรกที่ใช้เวลานาน |
| BLE1 | legend และ tooltip ของกรดอะมิโน | แสดงชื่อเต็มของกรดอะมิโนเมื่อ hover ทั้งในหน้ากรอกและหน้าผลลัพธ์ |

BLF2, BLF3 และ BLF4 เคยถูกปิดกั้นด้วย BLX1 ซึ่งทำเสร็จแล้ว จึงเริ่มได้ทันที

## 5. ช่องโหว่และจุดอ่อนที่รู้อยู่แต่ยังไม่ได้แก้

รายการเต็มอยู่ใน `manual-guides-2023/explanation/known-issues.md` ซึ่งเป็นเอกสารที่ทีมดูแลอยู่
หัวข้อนี้คัดมาเฉพาะข้อที่มีผลกับเนื้อหาของรายงาน

**ด้านความปลอดภัย** route สร้างบัญชี admin ยังเปิดอยู่ ใครที่เข้าถึง API ได้จึงสร้างบัญชี admin
เองได้แล้วเรียก endpoint ที่ต้องเป็น admin ข้อนี้ต้องแก้ก่อนเปิดให้คนนอกใช้ และยังไม่อยู่ในแผน
ของรุ่นนี้ นอกจากนี้ยังมีช่องที่ผู้ใช้ที่ล็อกอินแล้วอาจสั่งให้เครื่องยิง request ไปที่ URL อื่นได้
ผ่านพารามิเตอร์ของ job ซึ่งปิดไปแล้วบน dev แต่ยังเปิดอยู่บน production และยังไม่มี rate limit
ของการเดารหัสผ่าน

**ด้าน pipeline** job หายเงียบได้เมื่อ consumer หลุด เพราะ queue เป็นแบบ exclusive
และ consumer ack message ก่อนงานเสร็จ ถ้า process ตายระหว่างรัน งานนั้นจะหายและ job ค้าง
นอกจากนี้ conductor ยังไม่เช็คว่า job ยังทำงานอยู่หรือไม่ตอนรับผล ผลที่มาถึงช้าจึงอาจพา job
ที่ถูกตั้งเป็น fail ไปแล้วเดินต่อได้ และยังไม่มีปุ่มให้ผู้ดูแลตั้ง job ที่ค้างเป็น fail
จากหน้าเว็บ ต้องแก้ที่ฐานข้อมูล

**ด้าน infrastructure** MongoDB มี container เดียวใช้ร่วมกันทั้งสอง environment และยังไม่มี
บันทึกเรื่อง backup, key ของ Sealed Secrets controller ยังไม่มีบันทึกว่ามี backup หรือไม่,
Argo CD Application ถูกสร้างตรงเข้า cluster และยังไม่อยู่ใน git, SSH tunnel เป็นจุดเสี่ยง
จุดเดียว, และมี key เก่าที่หลุดอยู่ในประวัติของ git ซึ่งต้องถือว่ารั่วไปแล้ว

**ด้านสภาพแวดล้อม** GPU VM ใช้ Python 3.10 ที่หมดระยะ support แล้ว และ jax ที่ติดตั้งเป็นรุ่น
CPU อย่างเดียว การย้ายไป Python รุ่นใหม่ต้องทำพร้อมกันทั้ง venv ทุกตัวบนเครื่องและไฟล์ pin ทุกไฟล์

## 6. เหตุการณ์จริงที่เคยเกิด

สองเหตุการณ์นี้ใช้อ้างในรายงานได้ เพราะมีบันทึกและมีงานแก้ที่ตามมาจริง

**ระบบล่มเงียบ 13 เดือน** container ระดับ host สามตัวคือฐานข้อมูล reverse proxy และตัวต่ออายุ
certificate หยุดทำงานตั้งแต่ VM reboot ครั้งหนึ่งในเดือนกรกฎาคม 2025 เพราะไม่มีการตั้ง
restart policy ไว้เลย อาการที่เห็นคือทุก domain ต่อไม่ติดและ service ที่ต้องใช้ฐานข้อมูล
เข้าสู่ crash loop งานแก้คือตั้ง restart policy ให้ครบและทำให้ certificate ต่ออายุเองได้

**ML consumer ของ production ขาดจาก broker** เมื่อ 2026-09-29 consumer ของขั้นตอน mutation
เลิกพยายามเชื่อมต่อใหม่ไปเงียบ ๆ ทั้งที่ process ยังอยู่ ทำให้ job ค้างเป็น Ongoing ตลอดไป
โดยไม่มี error มี test job ค้างเก้าตัว และตัวหนึ่งค้างมาตั้งแต่ 4 กันยายน เครื่องมือตรวจสุขภาพ
เดิมมองไม่เห็นอาการนี้เพราะดูแค่ว่า process ยังอยู่ postmortem อยู่ใน
`manual-guides-2023/incidents/2026-09-29-prod-ml-consumer-disconnect.md` งานแก้ที่ตามมาคือ
health check ที่ตรวจจำนวน consumer ของทุก queue จากฝั่ง broker

## 7. ตัวเลขที่วัดหรือยืนยันได้แล้ว

| สิ่งที่วัด | ค่า | ที่มา |
| --- | --- | --- |
| เวลาค้น BLAST ผ่าน NCBI | เร็วสุด 4.3 นาที, median 12.3, mean 19.4, ช้าสุด 40.3 จาก 15 รอบ | วัดเมื่อ 2026-08-31 ด้วย script ที่เรียก NCBI ตรง |
| เวลาของขั้นตอน mutation | ประมาณ 15 วินาทีที่ค่าเริ่มต้น 5 trajectory คูณ 25 iteration บน CPU | วัดบน production เมื่อ 2026-09-29 |
| เวลาของ mmseqs2 | ไม่กี่นาที | เอกสาร `reference/ml-services.md` |
| จำนวน route ของ API | 63 เส้น | `proteng-bff/apis/router.go` |
| จำนวน collection ใน MongoDB | 12 | โค้ดของ conductor และ user-mgmt |
| จำนวนไฟล์ test | conductor 26, user-mgmt 10, kubeflow 9, bff 5, frontend 0 | นับจาก branch `dev` เมื่อ 2026-11-10 |
| ขนาดฐาน Swiss-Prot ที่ใช้กับ mmseqs2 | ประมาณ 286 MB | `reference/ml-services.md` |

ตัวเลขที่ยังไม่มีและต้องวัดเองถ้ารายงานต้องการ ได้แก่เวลาของขั้นตอน evotune และ fittop,
เวลาของขั้นตอน evaluation, คุณภาพของการทำนายเมื่อเทียบ Unirep กับ ESM และจำนวนผู้ใช้จริง
