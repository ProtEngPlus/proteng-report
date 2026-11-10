# ProtEngPlus — ข้อเท็จจริงของระบบ

บันทึกนี้รวบรวมว่าระบบคืออะไร ทำอะไรได้ และข้างในประกอบด้วยอะไร เพื่อให้คนที่ไม่เคยเห็น repo
เขียนรายงานได้โดยไม่ต้องเปิดโค้ด ทุกข้อตรวจกับโค้ดบน branch `dev` เมื่อ 2026-11-10
ถ้าข้อใดยืนยันไม่ได้จะกำกับว่า [TBD] หรือ [unverified] ไว้ตรงจุดนั้น

แหล่งที่มาของข้อเท็จจริงในไฟล์นี้คือ repo ของระบบทั้งแปดตัวใน GitHub org ProtEngPlus
และเอกสาร `manual-guides-2023` ซึ่งเป็น docs hub ของทีม ส่วนรายละเอียดเชิงปฏิบัติการ เช่น IP,
NodePort, path บนเครื่อง และ runbook อยู่ใน repo private เท่านั้น จึงไม่ถูกคัดมาไว้ที่นี่

## 1. ระบบคืออะไร

ProtEngPlus เป็นเว็บแอปสำหรับงาน protein engineering ผู้ใช้ส่ง protein sequence เข้ามาหนึ่งเส้น
พร้อมผลการวัดจากห้องแล็บจำนวนไม่มาก ระบบจะค้นหาโปรตีนที่ใกล้เคียงกันก่อน แล้วใช้ machine learning
หลายขั้นตอนเพื่อเสนอ mutation ที่น่าจะทำให้โปรตีนทำงานได้ดีขึ้น เมื่อครบทุกขั้นตอนผู้ใช้ดาวน์โหลด
ผลลัพธ์ได้จากหน้าเว็บ

แนวคิดตั้งต้นมาจากงานวิจัย Low-N protein engineering ซึ่งอาศัยผลการทดลองจำนวนน้อยในการสร้างโมเดล
ทำนาย fitness ของโปรตีน จุดขายของระบบจึงอยู่ที่การทำงานได้ด้วยข้อมูลแล็บเพียงไม่กี่สิบตัวอย่าง
ไม่ใช่หลักพันหรือหลักหมื่นแบบงาน deep learning ทั่วไป

ระบบนี้ไม่ได้เริ่มใหม่ในปีนี้ รุ่นก่อน (ปีการศึกษา 2566) สร้างระบบและส่งเป็น capstone ไปแล้ว
โดยมีเอกสารอยู่ที่ `manual-guides-2023/resources-2023/` งานของรุ่นปัจจุบันคือรับระบบที่มีอยู่
มาทำให้พร้อมใช้งานจริงและเพิ่มความสามารถตามที่ stakeholder ขอ รายละเอียดอยู่ใน
[work-progress.md](./work-progress.md)

## 2. ผู้ใช้และผู้มีส่วนได้ส่วนเสีย

| กลุ่ม | ใช้ระบบทำอะไร | หลักฐาน |
| --- | --- | --- |
| นักวิจัยด้าน protein engineering | สร้าง job, อัปโหลดผลแล็บ, ดู mutant ที่ระบบเสนอพร้อม predicted assay score แล้วเลือกไปทดลองจริง | หน้าเว็บทั้ง 13 หน้าใน `protengplus-frontend/src/pages/` |
| ผู้ดูแลระบบ (admin) | ดูและจัดการบัญชีผู้ใช้ | route `/admins/*` และ `/auth/login/admin` ใน proteng-bff |
| อาจารย์ที่ปรึกษาและเจ้าของ cloud project | เป็นเจ้าของ VM และ GCP project ที่ระบบรันอยู่ และเป็นผู้ให้ requirement ของ feature ชุดใหม่ | `manual-guides-2023/reference/vms.md`, `resources-2026/plan/feature-details.md` |
| ห้องแล็บปลายทาง (BIOTEC) | เป็นผู้ใช้จริงที่ requirement ตั้งต้นของรุ่นก่อนอ้างถึง | proposal ของรุ่นก่อนใน `resources-2023/` |

ข้อจำกัดที่ควรบอกในรายงาน คือระบบยังไม่มีผู้ใช้จริงนอกทีม ณ 2026-11-10 การใช้งานทั้งหมดที่ผ่านมา
เป็นการทดสอบของทีมเอง

## 3. สิ่งที่ผู้ใช้ทำได้บนหน้าเว็บ

หน้าเว็บมี 13 หน้า แบ่งเป็นสามกลุ่ม กลุ่มบัญชีผู้ใช้ประกอบด้วย SignUp, SignIn, SentVerifyEmail,
SuccessVerifyEmail, ForgetPassword, SentResetPassword, ResetPassword, ChangePassword
และ AccountManagement กลุ่มงานหลักคือ CreateJob, Dashboard และ JobDetail และกลุ่มข้อมูลผลแล็บ
คือ AssayLibrary

ขั้นตอนการใช้งานหนึ่งรอบเป็นแบบนี้

1. ผู้ใช้สมัครบัญชีและยืนยันอีเมล จึงจะเข้าใช้งานได้
2. สร้าง job ผ่าน wizard โดยกรอก protein sequence ตั้งต้น (พิมพ์เอง วางเอง หรือดึงจาก UniProt
   ด้วย UniProt ID) แล้วเลือกเครื่องมือของแต่ละขั้นตอนพร้อมพารามิเตอร์
3. อัปโหลดผลแล็บเป็นคู่ของ sequence กับ assay score หรือเลือกจาก Assay Library ที่เคยบันทึกไว้
4. สั่งรัน job แล้วติดตามสถานะจาก Dashboard ซึ่งสรุปจำนวน job ตามสถานะและ job ล่าสุด
5. เมื่อ job เสร็จ เปิดหน้า JobDetail เพื่อดูผลของแต่ละขั้นตอน ได้แก่ homolog ที่ค้นเจอ,
   mutant ที่เสนอพร้อม predicted assay score, ผลของ evaluation plugin และดาวน์โหลดผลเป็นไฟล์
6. บันทึกผลที่วัดได้จริงจากแล็บกลับเข้าระบบในแท็บ wet-lab result แล้วดูกราฟเทียบค่าที่ทำนาย
   กับค่าจริง
7. สั่งรัน mutation รอบใหม่หรือสั่งรัน evaluation รอบใหม่จาก job เดิมได้ โดยไม่ต้องสร้าง job ใหม่

ระบบส่งอีเมลแจ้งสถานะให้เมื่อ job จบหรือล้มเหลว ถ้าผู้ใช้เปิดการแจ้งเตือนไว้ตอนสร้าง job

## 4. ขั้นตอนของ pipeline

งานหนึ่งงานเดินผ่าน stage ตามลำดับ โดย stage 0 ถึง 3 เป็นของเดิม และขั้นตอน evaluation
เป็นส่วนที่เพิ่มในปีนี้

| stage | ชื่อใน `run_time` | เครื่องมือที่เลือกได้ | หน้าที่ |
| --- | --- | --- | --- |
| 0 | `query` | `blast` หรือ `mmseqs2` | ค้นหาโปรตีนที่ใกล้เคียงกับ sequence ตั้งต้น เพื่อใช้เป็นข้อมูลฝึกโมเดล |
| 1 | `evotune` | Unirep หรือ ESM | ปรับโมเดลภาษาโปรตีนให้เข้ากับตระกูลโปรตีนนั้น แล้วได้ representation |
| 2 | `fittop` | RidgeCV | ฝึกโมเดลทำนาย assay score จากผลแล็บที่ผู้ใช้ให้มา บน representation ของ stage 1 |
| 3 | `mutation` | Mutation (directed evolution) | เสนอ mutant พร้อมค่า assay score ที่ทำนายได้ |
| ต่อท้าย | ไม่อยู่ใน `run_time` | plugin ที่ผู้ใช้เลือก | ประเมิน mutant ที่ได้เพิ่มเติม รันทีละ plugin ตามลำดับ |

รายละเอียดเชิงเทคนิคของแต่ละ stage รวมถึงอัลกอริทึมและเวลาที่ใช้ อยู่ใน
[ml-pipeline-facts.md](./ml-pipeline-facts.md)

สถานะของ job มีห้าค่า คือ Created, Pending, Ongoing, Completed และ Failed ส่วนขั้นตอน evaluation
มีสถานะของตัวเองสี่ค่า คือ PENDING, ONGOING, COMPLETED และ FAILED ตาม `models/evaluation.go`
ของ proteng-conductor

job มีสองโหมดการเดิน ถ้า `run_type` เป็น `auto` ระบบจะเดิน stage ถัดไปให้เอง ถ้าเป็นโหมดทีละขั้น
job จะหยุดรอให้ผู้ใช้สั่งเองระหว่าง stage ค่าเริ่มต้นที่หน้าเว็บส่งมาคือ `one-step`

## 5. โครงสร้างข้อมูล

ข้อมูลเก็บใน MongoDB แยกเป็น 12 collection ตามที่ปรากฏในโค้ดของ proteng-conductor
และ proteng-user-mgmt

| collection | ของ service | เก็บอะไร |
| --- | --- | --- |
| `jobs` | conductor | งานหนึ่งงาน พร้อม sequence ตั้งต้น, พารามิเตอร์ทุก stage, ผลแล็บที่แนบมา, สถานะ, เวลาเริ่มจบของแต่ละ stage และ error log |
| `mutations` | conductor | การสั่งรัน mutation หนึ่งรอบของ job หนึ่ง |
| `mutation_results` | conductor | mutant หนึ่งเส้นพร้อม predicted assay score และสถานะ bookmark |
| `experimental_results` | conductor | assay score ที่วัดได้จริงจากแล็บ ผูกกับ mutant หนึ่งเส้น |
| `query_results` | conductor | homolog ที่ค้นเจอใน stage 0 |
| `evaluation_runs` | conductor | การรัน evaluation หนึ่งรอบ เก็บรายการ plugin, ลำดับ plugin ที่กำลังรัน และ snapshot ของ mutant ที่จะประเมิน |
| `evaluation_results` | conductor | ผลของ plugin หนึ่งตัวต่อ mutant หนึ่งเส้น เก็บค่าเป็น map จึงรองรับ plugin ที่คืนค่าต่างรูปแบบกันได้ |
| `assay_types` | conductor | ชนิดของ assay ที่ผู้ใช้นิยามเอง |
| `configurations` | conductor | ชุดพารามิเตอร์ที่บันทึกไว้เพื่อสร้าง job ใหม่โดยอ้างอิง job เดิม |
| `users` | user-mgmt | บัญชีผู้ใช้ พร้อม token ยืนยันอีเมล |
| `admins` | user-mgmt | บัญชีผู้ดูแล |
| `refresh_tokens` | user-mgmt | refresh token ของ session ที่ยังใช้ได้ |

ไฟล์ขนาดใหญ่ที่แต่ละ stage สร้างขึ้น เช่น representation model และโมเดลทำนาย ไม่ได้เก็บใน MongoDB
แต่เก็บเป็น artifact ใน Google Cloud Storage โดย MongoDB เก็บเพียงชื่อ bucket และ path

## 6. API

proteng-bff เป็นประตูเดียวที่หน้าเว็บเรียก และมี route ทั้งหมด 63 เส้นใน `apis/router.go`
แบ่งเป็นสองกลุ่มตาม service ปลายทาง คือ `/proteng-user-mgmt/*` และ `/proteng-conductor/*`
Swagger ของ bff จึงเป็นเอกสาร API ของทั้งระบบ

กลุ่มงานของ route มีดังนี้ บัญชีผู้ใช้และการยืนยันตัวตน (register, login, refresh, logout,
forgot password, reset password, change password, send verification, verify email), การจัดการ
ผู้ใช้และผู้ดูแลสำหรับ admin, job (สร้าง แก้ ลบ สั่งรัน และ dashboard), configuration ที่บันทึกไว้,
mutation และผลของ mutation รวมถึงการ bookmark และ export, experimental result สำหรับผลแล็บจริง,
assay type กับ Assay Library, evaluation plugin กับ evaluation run, การดาวน์โหลด artifact
และการดึง sequence จาก UniProt ด้วย UniProt ID

ทุก route ต้องผ่าน `Authenticate()` และ `Authorize(...)` ยกเว้น route ที่ต้องใช้งานได้ก่อนล็อกอิน
คือ register, login, reset password และ verify email มีข้อยกเว้นหนึ่งจุดที่เป็นช่องโหว่ที่รู้อยู่
คือ `POST /proteng-user-mgmt/admins` ซึ่งยังเปิดอยู่ ดู [work-progress.md](./work-progress.md)
หัวข้อช่องโหว่ที่ยังไม่ได้ปิด

## 7. การยืนยันตัวตน

ผู้ใช้ล็อกอินแล้วได้ access token กับ refresh token ที่ถูกเก็บเป็น httpOnly cookie ไม่ใช่
เก็บใน sessionStorage อย่างที่เคยเป็น token เป็น JWT แบบ RS256 โดย proteng-user-mgmt
ถือ private key สำหรับเซ็น และ proteng-bff ถือ public key คู่กันสำหรับตรวจ เมื่อ access token
หมดอายุ หน้าเว็บจะขอ refresh เงียบ ๆ ให้เองเมื่อเจอ 401 แล้วบังคับออกจากระบบเฉพาะเมื่อ refresh
ไม่สำเร็จ การเปลี่ยนหรือ reset รหัสผ่านจะ revoke refresh token ทั้งหมดของบัญชีนั้น

กลไกนี้เพิ่งขึ้น branch `dev` เมื่อ 2026-11-03 และยังไม่ได้ปล่อยขึ้น production
