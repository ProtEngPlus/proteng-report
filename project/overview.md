# Project overview — single source of truth

Every deliverable (proposal, slides, progress updates, final report) reads
these facts from here. Change a fact here first, then update the deliverables.
Thai notes are fine; Claude turns them into English when writing reports.

ค่าที่กรอกไว้ด้านล่างมาจากการตรวจโค้ดจริงของ repo ทั้งแปดตัวและเอกสารของทีมเมื่อ 2026-11-15
ข้อที่ยังไม่มีแหล่งยืนยันคงไว้เป็น `[TBD]` รายละเอียดที่ยาวกว่านี้อยู่ใน `notes/` ได้แก่
`system-facts.md` (ระบบคืออะไร ทำอะไรได้ โครงสร้างข้อมูล API), `ml-pipeline-facts.md`
(แต่ละขั้นตอนใช้วิธีไหน ใช้เวลาเท่าไร), `architecture-facts.md` (service, repo, การ deploy,
ข้อแลกเปลี่ยนเชิงออกแบบ), `work-progress.md` (ทำอะไรไปแล้ว เหลืออะไร ช่องโหว่ที่รู้อยู่)
และ `team-and-process.md` (การแบ่งงานและวิธีทำงาน)

## Identity
- Title (TH): การปรับปรุงแพลตฟอร์มซอฟต์แวร์สำหรับวิศวกรรมโปรตีน
- Title (EN): Software Platform Improvement for Protein Engineering (ชื่อระบบคือ ProtEngPlus)
- Academic year: 2569

จำนวน commit นับจาก 2026-08-01 ถึง 2026-11-15 ทุก branch ไม่รวม merge commit การจับคู่ชื่อเล่น
ในแผนงาน (Art, Gong, Poon) กับ git handle ยืนยันกับทีมแล้ว ดู `notes/team-and-process.md`


## Problem
- Problem in one line: นักวิจัยที่ต้องการปรับปรุงโปรตีนด้วยการ mutate มีผลการทดลองในมือเพียง
  ไม่กี่สิบตัวอย่าง และการทดลองแต่ละชุดมีต้นทุนสูง จึงต้องการระบบที่เสนอ mutant ที่คุ้มค่า
  จะนำไปทดลองจริง พร้อมประเมินได้ว่า mutant นั้นดีจริงหรือไม่ก่อนลงมือ
- Who has this problem (target users / stakeholders): นักวิจัยด้าน protein engineering
  เป็นผู้ใช้หลัก โดยมีห้องแล็บปลายทางและอาจารย์ที่ปรึกษาเป็นผู้มีส่วนได้ส่วนเสีย
  ดู `notes/system-facts.md` หัวข้อ 2
- Why it matters (real-world motivation, with sources if any): protein engineering
  เป็นงานพื้นฐานของการพัฒนาเอนไซม์ แอนติบอดี และโปรตีนทางการแพทย์ แต่การทดลองใน wet lab
  ใช้เวลาและต้นทุนสูง การลดจำนวนรอบการทดลองจึงมีมูลค่าโดยตรง
  [CITATION NEEDED: งาน Low-N protein engineering และงาน Unirep ที่เป็นฐานของวิธีนี้]
- Why it is complex / non-trivial: ปัญหานี้ไม่ใช่การเขียนเว็บครอบโมเดลเดียว แต่ต้องประกอบ
  หลายอย่างให้ทำงานร่วมกัน คือ pipeline หลายขั้นตอนที่แต่ละขั้นใช้โมเดลและ dependency ต่างกัน,
  งานที่ใช้เวลาตั้งแต่ไม่กี่วินาทีถึงหลายสิบนาทีและบางขั้นพึ่งบริการภายนอกที่ควบคุมเวลาไม่ได้,
  การกระจายงานข้ามเครื่องสองเครื่องที่คนละ cloud project ผ่าน message queue,
  การกู้สถานะเมื่อขั้นตอนใดล้มเหลวกลางทาง และการทำให้ขั้นตอนประเมินเพิ่มเข้ามาได้โดยไม่ต้อง
  แก้แกนกลางของระบบ
- Sub-disciplines integrated (need ≥ 2): (1) distributed systems และ software architecture
  คือ microservice, message queue และ pipeline orchestration (2) machine learning
  คือ protein language model, regression และ Monte Carlo optimization (3) cloud infrastructure
  และ DevOps คือ Kubernetes, GitOps, CI/CD และ monitoring (4) web engineering และ security
  คือ SPA, API gateway, JWT และการจัดการ session (5) bioinformatics เป็น domain ของปัญหา

## Objectives (measurable)
ยืนยันแล้วเมื่อ 2026-11-16 ใช้ถ้อยคำชุดนี้ให้ตรงกันทุก deliverable ถ้าจะเปลี่ยนต้องแก้ที่นี่ก่อน
สามข้อนี้ครอบ backlog ที่เหลือทั้งหมด คือ BLX1 กับ BLF2 และ BLF3 อยู่ในข้อ 1, BLF4 กับ BLF5
และ BLF6 อยู่ในข้อ 2, รายการ BLB อยู่ในข้อ 3 ส่วน BLF1 กับ BLF7 ที่เสร็จแล้วนับเป็นผลงาน
ของภาคการศึกษาที่ 1 ไม่ใช่วัตถุประสงค์ และ BLE1 เป็นงานย่อยที่ไม่ตั้งเป็นข้อแยก

1. ทำให้ขั้นตอนประเมิน mutant ต่อเติมได้โดยไม่ต้องแก้แกนกลางของระบบ วัตถุประสงค์ข้อนี้
   ต่อยอดจากสถาปัตยกรรม evaluation plugin ที่วางเสร็จแล้วในภาคการศึกษาที่ 1 โดยพิสูจน์ว่า
   สถาปัตยกรรมนั้นต่อเติมได้จริงด้วยการเพิ่ม plugin ประเมินสองตัว ตัวแรกคือการประเมินความเสถียร
   ของการพับโปรตีนเทียบกับ wild-type ในโหมดที่ไม่ใช้โครงสร้างสามมิติ (BLF2) และตัวที่สองคือ
   การทำนาย kcat ด้วยโมเดล DLKcat ซึ่งต้องเพิ่มช่องให้ผู้ใช้กรอก substrate เป็น SMILES (BLF3)
   เกณฑ์วัดผลคือ plugin ทั้งสองตัวเพิ่มเข้าระบบได้โดยไม่แก้โค้ดส่วน orchestration ของ conductor
   และเมื่อ plugin ตัวใดตัวหนึ่งล้มเหลว ระบบยังต้องคืนผลของ plugin ที่เหลือและไม่ทำให้สถานะ
   ของ job ทั้งก้อนกลายเป็นล้มเหลว
2. ยกระดับคุณภาพและความคุ้มค่าของ mutant ที่ระบบเสนอ เนื่องจากผู้ใช้นำ mutant ไปทดลองจริงได้
   เพียงไม่กี่ตัวต่อรอบ ระบบจึงต้องเสนอชุดที่คุ้มค่าที่สุดและปรับตัวตามผลที่ได้กลับมา
   วัตถุประสงค์ข้อนี้ประกอบด้วยสามส่วน ส่วนแรกคือให้ขั้นตอน mutation คืน mutant มากกว่า
   เส้นสุดท้ายของแต่ละ trajectory แล้วคัดเฉพาะกลุ่มที่ดีที่สุด พร้อมคุมความหลากหลายไม่ให้ผลลัพธ์
   กระจุกอยู่ใน trajectory เดียว (BLF4) ส่วนที่สองคือเพิ่มทางเลือกอัลกอริทึมให้ผู้ใช้เลือกได้
   ต่อหนึ่ง job คือ Site-Saturation Mutagenesis ในขั้นตอน mutation และ Random Forest
   ในขั้นตอน top model (BLF5) และส่วนที่สามคือเมื่อผู้ใช้บันทึกผลแล็บชุดใหม่ ระบบต้องฝึกโมเดล
   ทำนายใหม่โดยใช้ representation ของ job เดิมซ้ำ จึงข้ามสองขั้นตอนแรกที่ใช้เวลานานที่สุดได้
   (BLF6) เกณฑ์วัดผลคือรอบการฝึกใหม่ต้องไม่เรียกขั้นตอนค้นหาโปรตีนใกล้เคียงและขั้นตอนปรับ
   โมเดลภาษาโปรตีนซ้ำ และระบบต้องรายงานค่าความสอดคล้องระหว่างค่าที่ทำนายกับผลแล็บจริง
   เป็นตัวเลขที่เทียบระหว่างรอบได้
3. ทำให้ระบบพร้อมรับผู้ใช้จริงทั้งในแง่ความปลอดภัยและความทนทาน วัตถุประสงค์ข้อนี้ครอบรายการ
   BLB ซึ่งเป็นช่องโหว่และจุดเปราะที่ทีมตรวจพบตอนรับช่วงต่อระบบ ไม่นับ bug ที่ทีมเจอเองระหว่าง
   พัฒนา feature ของปีนี้ เกณฑ์วัดผลแบ่งเป็นสองกลุ่ม กลุ่มแรกคือการปิดรายการ BLB ที่ยังค้างอยู่
   ให้ครบ ได้แก่ การนำการแก้ไขทั้งหมดบน `dev` ขึ้น production, การเปลี่ยน credential ที่เคยหลุด
   ไปอยู่ใน log, การเพิ่มการตรวจสิทธิ์เจ้าของทรัพยากรในเส้นทาง API ที่ยังขาดอยู่ และการลดอายุ
   access token บน production ให้เท่ากับ dev กลุ่มที่สองคือระบบต้องตรวจพบงานที่ค้างและ consumer
   ที่หลุดจาก broker ได้เองภายในห้านาทีแล้วแจ้งเตือนทีมโดยอัตโนมัติ ซึ่งต่อยอดจาก health check
   ที่ตรวจจำนวน consumer ของทุก queue จากฝั่ง broker ที่ทำไว้แล้ว

## Scope
- In scope: งานต่อยอดจากระบบที่มีอยู่ ได้แก่ สถาปัตยกรรมขั้นตอนประเมินและ plugin ประเมิน,
  การคัดและคุมขอบเขตของ mutant ที่เสนอ, การเพิ่มอัลกอริทึมให้ผู้ใช้เลือกในขั้น top model
  และขั้น mutation, การฝึกโมเดลทำนายต่อเมื่อมีผลแล็บชุดใหม่, การบันทึกผลแล็บจริงกลับเข้าระบบ,
  งาน production-readiness ตามรายการ BLB และการดูแลระบบที่รันอยู่จริงสอง environment
- Out of scope / limitations: ไม่ได้ออกแบบระบบใหม่จากศูนย์, ไม่ทำโหมดที่ใช้โครงสร้างสามมิติ
  ของโปรตีนในการประเมินความเสถียร, ไม่ได้ย้ายไปใช้ cluster หลาย node หรือบริการ Kubernetes
  ของ cloud, ไม่ควบคุมเวลาของ NCBI BLAST ซึ่งเป็นบริการภายนอก, ไม่มีการทดลองใน wet lab
  โดยทีมเอง และ ณ 2026-11-15 ยังไม่มีผู้ใช้จริงนอกทีม

## Solution idea
- Approach in 2–3 sentences: ระบบรับ protein sequence ตั้งต้นกับผลแล็บจำนวนน้อย แล้วเดินงาน
  ผ่าน pipeline สี่ขั้นคือค้นหาโปรตีนใกล้เคียง, ปรับโมเดลภาษาโปรตีนให้เข้ากับตระกูลนั้น,
  ฝึกโมเดลทำนาย assay score จากผลแล็บ และเสนอ mutant ด้วย directed evolution
  จากนั้นต่อด้วยขั้นตอนประเมินที่ผู้ใช้เลือก plugin ได้เอง ทุกขั้นเป็น service แยกกันที่รับงาน
  ผ่าน message queue โดยมีตัว orchestrator สั่งงานทีละขั้นและเก็บสถานะไว้ในฐานข้อมูล
- Main components (hardware / software / database / networking): frontend เป็น SPA,
  API gateway ที่ตรวจ JWT, service บัญชีผู้ใช้, service orchestrator, ML service หนึ่งตัว
  ต่อหนึ่งขั้นตอน, RabbitMQ เป็น message bus, MongoDB เป็นฐานข้อมูลหลัก, Google Cloud Storage
  เก็บ artifact ขนาดใหญ่, Kubernetes หนึ่ง cluster สำหรับ dev และ production
  และ GPU VM อีกเครื่องสำหรับงาน ML ที่ต่อกลับมาผ่าน SSH tunnel
- Tech stack: ฝั่ง backend เป็น Go 1.22 กับ Gin 1.9.1 (bff, conductor, user-mgmt) โดย conductor
  ใช้ mongo-driver 1.12.1 กับ amqp091-go 1.9.0 และ user-mgmt ใช้รุ่นใหม่กว่าคือ 1.13.1 กับ 1.10.0
  ฝั่งหน้าเว็บเป็น React 18.3.1 กับ TypeScript 5.5.3
  บน Vite 5.4.8 ใช้ Tailwind 3.4.14, MUI 6.1.4, axios 1.7.7 และ react-apexcharts 1.7.0
  ฝั่ง ML เป็น Python 3.10.12 ใช้ jax-unirep 2.2.0 กับ jax 0.6.0 สำหรับ Unirep,
  transformers 4.39.3 กับ torch 2 ขึ้นไปและ fair-esm 2.0.0 สำหรับ ESM,
  scikit-learn 1.2.2 สำหรับ RidgeCV, biopython 1.81 สำหรับเรียก NCBI BLAST,
  MMseqs2 กับฐาน Swiss-Prot, aio_pika 9.4.0 เป็น RabbitMQ client
  และ google-cloud-storage 2.12.0 สำหรับ artifact ส่วนงาน infrastructure ใช้ MicroK8s,
  Kustomize, Argo CD, Sealed Secrets, GitHub Actions, Docker Hub, nginx กับ Let's Encrypt
  และ Discord สำหรับแจ้งเตือน

## Change log (project-wide facts that changed, and why)
| Date | Fact | Old → New | Why |
|---|---|---|---|
| 2026-11-16 | ฐานข้อมูลเริ่มต้นของ BLAST | ค่าเริ่มต้นชั่วคราวบน `dev` → ใช้ `nr_cluster_seq` (ClusteredNR) เป็นค่าเริ่มต้นถาวร | ทีมตกลงแล้ว ไม่ต้องรอข้อสรุปอีก |
| 2026-11-16 | สถานะของวัตถุประสงค์ | ร่างที่รอยืนยัน → ยืนยันแล้ว ใช้อ้างอิงได้ทุก deliverable | ผ่านการยืนยันเมื่อ 2026-11-16 |
| 2026-11-16 | ขอบเขตงานหลังประชุม stakeholder 2026-10-09 | ไม่มีบันทึก จึงเติมไม่ได้ → ที่ประชุมไม่มีการเปลี่ยนขอบเขต ทำต่อตามแผนเดิม | ยืนยันกับผู้เข้าประชุมแล้ว |
| 2026-11-16 | วัตถุประสงค์ทั้งสามข้อ | ร่างเดิมที่ข้อ 2 ครอบงานที่เสร็จแล้ว → เขียนใหม่ให้ครอบ backlog ที่เหลือครบและทุกข้อมีเนื้องานของภาคการศึกษาที่ 2 | BLF1 กับ BLF7 เสร็จในภาคการศึกษาที่ 1 แล้ว ส่วน BLF4 ถึง BLF6 เดิมไม่มีวัตถุประสงค์ใดรองรับทั้งที่อยู่ใน scope |
| 2026-11-16 | ชื่อโครงงาน ปีการศึกษา และนิยามของรายการ BLB | ว่าง → ชื่อ TH/EN ตามหน้าปกข้อเสนอ, ปีการศึกษา 2569, BLB นับเฉพาะสิ่งที่ตรวจพบตอนรับช่วงต่อระบบ | ยืนยันกับทีมแล้ว |
| 2026-11-10 | ข้อเท็จจริงของระบบทั้งหมด | ว่างทั้งไฟล์ → กรอกจากโค้ดจริงและเอกสารของทีม | เริ่มเขียน proposal จึงต้องมีฐานข้อเท็จจริงที่ทุก deliverable อ้างได้ |
| 2026-11-15 | การจำกัดการเดารหัสผ่าน (BLB25) | ยังไม่ลงมือ → merge เข้า `dev` แล้ว ล็อกบัญชี 15 นาทีเมื่อกรอกผิดครบ 5 ครั้ง | งานเสร็จและมีผลกับหัวข้อ objective ข้อ 3 |
| 2026-11-15 | เพดานเวลาของการค้น BLAST | ไม่มีเพดาน ค้างได้ไม่จำกัด → 90 นาทีแล้วให้ job fail พร้อมสาเหตุ | เลิกใช้ `qblast` ของ Biopython มาเขียน client เองเพื่อคุมเวลา |
| 2026-11-15 | ฐานข้อมูลเริ่มต้นของ BLAST | `nr` → `nr_cluster_seq` (ClusteredNR) บน `dev` | ฐาน nr มักค้นไม่จบใน 90 นาที job จึงไปจบที่ fail |
| 2026-11-15 | จำนวน route ของ API และ collection | 63 route, 12 collection → 64 route, 13 collection | เพิ่ม route ตรวจลิงก์ reset และ collection `auth_attempts` |
| 2026-11-15 | อายุ access token ของ dev | 24 ชั่วโมง → 15 นาที | ลดผลกระทบเมื่อ token รั่ว production รอ release |
| 2026-11-15 | Deployment `ml-*-rest` | ยังรันอยู่โดยไม่มีใครเรียก → ศูนย์ replica ทั้งสอง environment | image ที่สะสมทำให้ disk ของ VM หลักเต็มจน MongoDB ล่ม 6.5 นาที |
