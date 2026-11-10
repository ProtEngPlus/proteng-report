# Project overview — single source of truth

Every deliverable (proposal, slides, progress updates, final report) reads
these facts from here. Change a fact here first, then update the deliverables.
Thai notes are fine; Claude turns them into English when writing reports.

ค่าที่กรอกไว้ด้านล่างมาจากการตรวจโค้ดจริงของ repo ทั้งแปดตัวและเอกสารของทีมเมื่อ 2026-11-10
ข้อที่ยังไม่มีแหล่งยืนยันคงไว้เป็น `[TBD]` รายละเอียดที่ยาวกว่านี้อยู่ใน `notes/` ได้แก่
`system-facts.md` (ระบบคืออะไร ทำอะไรได้ โครงสร้างข้อมูล API), `ml-pipeline-facts.md`
(แต่ละขั้นตอนใช้วิธีไหน ใช้เวลาเท่าไร), `architecture-facts.md` (service, repo, การ deploy,
ข้อแลกเปลี่ยนเชิงออกแบบ), `work-progress.md` (ทำอะไรไปแล้ว เหลืออะไร ช่องโหว่ที่รู้อยู่)
และ `team-and-process.md` (การแบ่งงานและวิธีทำงาน)

## Identity
- Title (TH): [TBD]
- Title (EN): [TBD — ชื่อระบบคือ ProtEngPlus; ชื่อโครงงานของรุ่นก่อนคือ "software platform
  for protein engineering" ปีนี้ยังไม่ได้ตั้งชื่อทางการ]
- Academic year: [TBD — แม่แบบใน `midterm_proposal/docs/` พิมพ์ว่า 2568 แต่กำหนดส่งคือ
  พ.ศ. 2569 (ค.ศ. 2026) ต้องยืนยันว่าแม่แบบเป็นของปีก่อนหรือไม่]

## Team (3–5)
| Name (TH) | Student ID | Role |
|---|---|---|
| [TBD] | [TBD] | Infrastructure, DevOps, CI/CD, เอกสาร, auth และ production-readiness (git: BadLuckZ, 424 commit) |
| [TBD] | [TBD] | Evaluation plugin architecture (BLX1) และงาน conductor กับ frontend (git: art1722, 61 commit) |
| [TBD] | [TBD] | คุมช่วงตำแหน่งที่ mutate ได้ (BLF1) และงาน ML service (git: Napongthorn, 13 commit) |
| [TBD] | [TBD] | Wet-lab result loop (BLF7) และงาน frontend (git: poongeta, 10 commit) |

จำนวน commit นับจาก 2026-08-01 ถึง 2026-11-10 ไม่รวม merge commit การจับคู่ชื่อเล่นในแผนงาน
(Art, Gong, Poon) กับ git handle เป็นการอนุมาน [unverified] ดู `notes/team-and-process.md`

## Advisors
- Main advisor: [TBD — เป็นเจ้าของ GCE instance และ GCP project ที่ระบบรันอยู่ และเป็นผู้ให้
  requirement ของ feature ชุดปีนี้]
- Co-advisor (optional): [TBD]

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
ร่างข้อเสนอ ยังต้องยืนยันกับทีมและอาจารย์ก่อนใช้ เพราะวัตถุประสงค์ต้องคงที่ทุก deliverable
[TBD: ยืนยันหรือแก้ทั้งสามข้อ]

1. ขยาย pipeline ให้เพิ่มขั้นตอนประเมิน mutant ได้โดยไม่ต้องแก้แกนกลาง และพิสูจน์ด้วยการเพิ่ม
   plugin ประเมินอย่างน้อยสองตัว คือความเสถียรของการพับโปรตีน (ΔΔG) และ kcat
   โดยที่ plugin ตัวหนึ่งล้มเหลวต้องไม่ทำให้ job ทั้งก้อนล้มเหลว
2. ปิดวงการใช้งานระหว่างระบบกับห้องแล็บ คือผู้ใช้บันทึกผลที่วัดได้จริงกลับเข้าระบบได้
   และระบบแสดงค่าความสอดคล้องระหว่างค่าที่ทำนายกับค่าจริงเป็นตัวเลขได้
3. ทำให้ระบบพร้อมให้ผู้ใช้จริงใช้งาน โดยปิดช่องโหว่และจุดเปราะที่ระบุไว้ในรายการ BLB
   ให้ครบตามเกณฑ์ที่ตกลงกัน และมีกลไกที่ตรวจพบงานค้างหรือ consumer ที่หลุดได้เองภายในเวลาที่กำหนด

## Scope
- In scope: งานต่อยอดจากระบบที่มีอยู่ ได้แก่ สถาปัตยกรรมขั้นตอนประเมินและ plugin ประเมิน,
  การคัดและคุมขอบเขตของ mutant ที่เสนอ, การเพิ่มอัลกอริทึมให้ผู้ใช้เลือกในขั้น top model
  และขั้น mutation, การฝึกโมเดลทำนายต่อเมื่อมีผลแล็บชุดใหม่, การบันทึกผลแล็บจริงกลับเข้าระบบ,
  งาน production-readiness ตามรายการ BLB และการดูแลระบบที่รันอยู่จริงสอง environment
- Out of scope / limitations: ไม่ได้ออกแบบระบบใหม่จากศูนย์, ไม่ทำโหมดที่ใช้โครงสร้างสามมิติ
  ของโปรตีนในการประเมินความเสถียร, ไม่ได้ย้ายไปใช้ cluster หลาย node หรือบริการ Kubernetes
  ของ cloud, ไม่ควบคุมเวลาของ NCBI BLAST ซึ่งเป็นบริการภายนอก, ไม่มีการทดลองใน wet lab
  โดยทีมเอง และ ณ 2026-11-10 ยังไม่มีผู้ใช้จริงนอกทีม

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
- Tech stack: ฝั่ง backend เป็น Go 1.22 กับ Gin 1.9.1 (bff, conductor, user-mgmt) ใช้
  mongo-driver 1.12.1 และ amqp091-go 1.9.0 ฝั่งหน้าเว็บเป็น React 18.3.1 กับ TypeScript 5.5.3
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
| 2026-11-10 | ข้อเท็จจริงของระบบทั้งหมด | ว่างทั้งไฟล์ → กรอกจากโค้ดจริงและเอกสารของทีม | เริ่มเขียน proposal จึงต้องมีฐานข้อเท็จจริงที่ทุก deliverable อ้างได้ |
