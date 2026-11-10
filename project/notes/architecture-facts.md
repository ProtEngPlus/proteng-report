# ProtEngPlus — ข้อเท็จจริงของสถาปัตยกรรมและการ deploy

บันทึกนี้อธิบายว่าระบบประกอบด้วย service อะไร แบ่ง repo อย่างไร รันอยู่ที่ไหน และขึ้นระบบจริง
ด้วยวิธีใด พร้อมเหตุผลของการตัดสินใจหลักที่ควรใช้ตอบคำถามเรื่อง design trade-off
ตรวจกับโค้ดและเอกสารของทีมเมื่อ 2026-11-10

รายละเอียดเชิงปฏิบัติการ เช่น IP, NodePort, port ของ tunnel, ชื่อเครื่อง และ path บนเครื่อง
อยู่ใน repo private `manual-guides-2023` และ `devops-infra` เท่านั้น จึงไม่ถูกคัดมาไว้ที่นี่
ถ้ารายงานต้องการตัวเลขเหล่านั้น ให้ยืนยันก่อนว่าเอกสารที่จะส่งเป็นเอกสารภายในหรือไม่

## 1. service และหน้าที่

ระบบเป็น microservice ห้ากลุ่ม

| ส่วน | ภาษาและเครื่องมือ | หน้าที่ |
| --- | --- | --- |
| frontend | React, TypeScript, Vite, Tailwind, MUI | หน้าเว็บทั้งหมด คุยกับ bff เท่านั้น |
| proteng-bff | Go, Gin | API gateway ตรวจ JWT และ role แล้ว forward ต่อ ไม่มีฐานข้อมูลและไม่ต่อ RabbitMQ เป็นที่อยู่ของ Swagger ของทั้งระบบ |
| proteng-user-mgmt | Go, Gin, MongoDB, RabbitMQ | บัญชีผู้ใช้ ยืนยันอีเมล ออก JWT จัดการรหัสผ่าน บัญชี admin และส่งอีเมลแจ้งสถานะงาน |
| proteng-conductor | Go, Gin, MongoDB, RabbitMQ | ขับ pipeline ทีละ stage เก็บสถานะของ job, mutation, query result และ evaluation |
| ML service | Python 3.10 | หนึ่ง service ต่อหนึ่งขั้นตอนของ pipeline ทุกตัวเป็น RabbitMQ consumer |

ส่วนประกอบพื้นฐานที่ใช้ร่วมกันคือ RabbitMQ เป็น message bus, MongoDB เป็นฐานข้อมูลหลัก
และ Google Cloud Storage เป็นที่เก็บ artifact ขนาดใหญ่

เส้นทางของ request หนึ่งเส้นเป็นแบบนี้ ผู้ใช้เปิดหน้าเว็บผ่าน HTTPS หน้าเว็บเรียก bff
bff ตรวจ JWT แล้ว forward ไปยัง user-mgmt หรือ conductor จากนั้น conductor คุยกับ ML service
ผ่าน RabbitMQ และ ML service อ่านเขียน artifact ใน GCS

## 2. repo ทั้งแปดตัว

ทุก repo อยู่ใน GitHub org ProtEngPlus และ clone ไว้ข้างกันในโฟลเดอร์เดียว

| repo | ภาษา | รันที่ไหน | สาธารณะหรือไม่ |
| --- | --- | --- | --- |
| proteng-bff | Go | Kubernetes | public |
| proteng-conductor | Go | Kubernetes | public |
| proteng-user-mgmt | Go | Kubernetes | public |
| protengplus-frontend | React + Vite | Kubernetes | public |
| proteng-kubeflow | Python | GPU VM | public |
| devops-k8s | Kustomize | ไม่ได้รัน | private |
| devops-infra | Ansible, Compose, bash | VM หลัก | private |
| manual-guides-2023 | Markdown | ไม่ได้รัน | private |

เพราะ repo ของ service เป็น public ข้อมูล infrastructure ทุกอย่างจึงถูกบังคับให้อยู่ใน repo
private สามตัวท้ายเท่านั้น และห้าม bake ค่า config ที่เป็นความลับเข้า image เพราะ image
บน Docker Hub ของทีมเป็น public ตาม free plan

## 3. สภาพแวดล้อมที่รัน

ระบบมีสาม environment คือ local, dev และ production

dev และ production รันอยู่บน MicroK8s cluster เดียวกันบน VM หลัก แยกกันด้วย namespace
แต่ละฝั่งมี image tag, config, secret และชื่อฐานข้อมูลของตัวเอง จึงแยกกันจริงในระดับ credential
และการ deploy แต่ยังใช้เครื่องเดียวกันและ container MongoDB ตัวเดียวกันร่วมกันอยู่

ผู้ใช้เข้าถึง production ที่ `protengplus.com` และ dev ที่ `dev.protengplus.com`
ส่วน Swagger ของฝั่ง dev อยู่ที่ `doc.protengplus.com` และ Argo CD อยู่ที่ `argocd.protengplus.com`
การรับ HTTPS และ map hostname ทำที่ container ระดับ host ไม่ได้ใช้ Kubernetes Ingress

ML consumer ของทั้งสอง environment ไม่ได้อยู่ใน cluster แต่รันบน GPU VM อีกเครื่องหนึ่ง
และต่อกลับมาที่ RabbitMQ ของ environment ของตัวเองผ่าน SSH tunnel

สเปกของเครื่องที่ควรรู้คือ VM หลักมี 2 vCPU, RAM 4 GB และ disk 100 GB ซึ่งเป็นเครื่องที่
อาจารย์จัดให้ตอนเริ่มโปรเจกต์ ส่วน GPU VM มีการ์ด RTX 5090 แต่เป็นเครื่องใน cloud project
ที่ทีมไม่ได้คุม

## 4. การ deploy

service ที่รันใน cluster ใช้ทางเดียวกันทั้งหมด คือ GitHub Actions ของ repo นั้น build image
แล้ว push ขึ้น Docker Hub โดยใช้ short SHA ของ commit เป็น tag จากนั้นเรียก workflow ของ
`devops-k8s` ให้เปลี่ยน tag ใน overlay แล้ว Argo CD จะ sync cluster ให้ตรงกับ git เอง
โดยเปิด selfHeal และ prune ไว้ การ deploy จึงไม่ต้องมีใครสั่ง apply ด้วยมือ และย้อนดูได้จาก
ประวัติของ `devops-k8s` ว่า image ไหนขึ้นเมื่อไร

กฎที่ตามมาคือการ push เข้า `dev` จะ deploy ขึ้น dev และการ push เข้า `main` จะ deploy ขึ้น
production ทันที แม้จะเป็นการแก้เอกสารเท่านั้น ส่วน `devops-k8s` และ `devops-infra`
มี branch เดียวและไม่มีขั้น staging การ merge จึงมีผลกับของจริงทันที

ML service บน GPU VM เป็นข้อยกเว้น เพราะเครื่องไม่มี sudo และไม่มี Docker ตั้งแต่ 2026-10-24
ฝั่ง dev มี cron ที่ดึงโค้ดจาก branch `dev` มาติดตั้งให้เองทุกห้านาที โดยรอให้ consumer
ว่างงานก่อนแตะ restart เฉพาะตัวที่ไฟล์เปลี่ยน และ roll back ให้เองถ้า consumer ไม่กลับมา
ส่วนฝั่ง production ยังต้องคัดลอกไฟล์ขึ้นเครื่องด้วยมือ

secret ใน cluster จัดการด้วย Sealed Secrets จึง commit ไฟล์ secret ที่เข้ารหัสแล้วลง git ได้
และถอดรหัสได้เฉพาะ controller ใน cluster นี้

## 5. การเฝ้าระวัง

VM หลักรัน health check ทุกห้านาที โดยตรวจจากฝั่ง broker ว่าทุก queue มี consumer ครบหรือไม่
มี job ค้างเกินเวลาที่กำหนดหรือไม่ container ยังทำงานอยู่หรือไม่ และ certificate กับ disk
ยังปกติหรือไม่ ผลส่งเข้า Discord ของทีมเฉพาะตอนที่สถานะเปลี่ยน พร้อม heartbeat วันละครั้ง
ถ้าวันไหนไม่มี heartbeat แปลว่าตัวตรวจเองมีปัญหา

เหตุผลที่ต้องตรวจจากฝั่ง broker ไม่ใช่ตรวจว่า process ยังอยู่ มาจากเหตุการณ์จริงเมื่อ 2026-09-29
ที่ consumer ของ production เลิกเชื่อมต่อ RabbitMQ ไปเงียบ ๆ ทั้งที่ process ยังอยู่
ทำให้ job ค้างและไม่มีใครรู้อยู่แปดวัน

GPU VM มี health check ของตัวเองที่ตรวจว่า consumer เข้าสู่ crash loop หรือไม่ และ GPU
ยังมองเห็นอยู่หรือไม่

## 6. การตัดสินใจเชิงออกแบบและข้อแลกเปลี่ยน

หัวข้อนี้มีประโยชน์กับส่วน design และ constraints ของรายงาน เพราะทุกข้อเป็นการประนีประนอม
กับข้อจำกัดจริง ไม่ใช่ทางเลือกที่ดีที่สุดในเชิงทฤษฎี

**ใช้ MicroK8s แบบ node เดียวบน VM เดียว** เพราะตอนเริ่มโปรเจกต์มีเครื่องให้ใช้เครื่องเดียว
ทีมเลือก Kubernetes เพราะระบบเป็น microservice หลายตัว ถ้าจะทำ production จริงจังควรใช้ cluster
หลาย node หรือบริการ Kubernetes ของ cloud

**dev และ production อยู่บน cluster เดียว แยกด้วย namespace** ข้อแลกเปลี่ยนคือทั้งสองฝั่ง
ใช้เครื่องและ container MongoDB ร่วมกัน ปัญหาระดับเครื่องจึงกระทบทั้งสองฝั่งพร้อมกัน

**ใช้ NodePort กับ reverse proxy ระดับ host แทน Ingress** เพราะตั้ง Ingress บน single-node
cluster แล้วมีปัญหา ข้อแลกเปลี่ยนคือ config ของ routing อยู่นอก Kubernetes และต้องแก้ให้ตรงกัน
ด้วยมือเมื่อ port เปลี่ยน

**ย้าย ML ไปรันบน GPU VM นอก cluster** เพราะบน VM หลักที่มี 2 vCPU และ disk เต็มบ่อย งาน ML
ช้ากว่ามาก ข้อแลกเปลี่ยนคือเครื่องนั้นไม่มี sudo ไม่มี Docker และไม่มี CI/CD มาตรฐาน
ทุกอย่างเป็น venv ที่ประกอบด้วยมือ และ Deployment ของ ML ใน cluster ถูกตรึงไว้ที่ศูนย์ replica
เผื่อต้องย้ายกลับ

**ใช้ SSH tunnel ระหว่างสองเครื่อง** เพราะ firewall ของ cloud project ฝั่ง GPU VM อนุญาตเฉพาะ
port ที่มีอยู่เดิมและทีมขอเปิดเพิ่มไม่ได้ ข้อแลกเปลี่ยนคือ tunnel หนึ่งเส้นเป็นจุดเสี่ยงจุดเดียว
ของ consumer ทุกตัวใน environment นั้น

**ML consumer ใช้ queue แบบ exclusive** ข้อดีคือรับประกันว่ามี consumer ตัวเดียวต่อ queue
จึงไม่มีสองเครื่องแย่งงานกัน ข้อเสียคือ queue หายไปพร้อม consumer และ message ที่ส่งมาระหว่างนั้น
ถูกทิ้งเงียบ ๆ ซึ่งเป็นสาเหตุโดยตรงของเหตุการณ์ 2026-09-29

**มีทั้ง blast และ mmseqs2 เป็นเครื่องมือ query** เพราะ BLAST ผ่าน NCBI ให้ผลตรงกับเว็บของ NCBI
แต่เวลาไม่คงที่ ส่วน MMseqs2 รันในเครื่องเราเองจึงเร็วและคาดเดาได้ ทีมจึงให้เลือกได้ทั้งสองแบบ

**ใช้ Sealed Secrets** แทนการเก็บ secret เป็น base64 ใน git ซึ่งเท่ากับไม่ได้เข้ารหัส
ข้อแลกเปลี่ยนคือถ้า key ของ controller หายโดยไม่มี backup ไฟล์ที่ seal ไว้ทั้งหมดจะใช้ไม่ได้
