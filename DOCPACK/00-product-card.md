---
title: solo-intake — Product Card
doc_id: CARD-solo-intake
doc_type: product-card
verdict: invest
version: 0.1
date: 2026-10-11
status: ร่างเพื่อพิจารณา
owner: Product owner (Book Dev)
---

# solo-intake — Product Card

## 1. สรุปคุณค่าในหนึ่งประโยค

ลงทะเบียนแหล่งข้อมูล คิว intake พร้อม idempotency/lease และ event ของแต่ละ intake เพื่อให้ pipeline รับข้อมูลแบบตรวจสอบย้อนได้

## 2. ปัญหาและผู้ใช้เป้าหมาย

การรับข้อมูลเข้า data lake ต้องกันซ้ำและรู้สถานะ ผู้ใช้คือ pipeline/agent ของ Solo Empire

## 3. สิ่งที่ทำได้จริงตอนนี้

- REST `/v1/sources`, `/v1/intakes`, `/v1/intake-events`
- ตัวกรอง `status`, `domain`, `dataset`, `idempotency_key`, `lease_owner`
- ทุกการเขียนผ่านลำดับ precheck → เปลี่ยนข้อมูลในเครื่อง → execution บน empire-platform → commit → finish พร้อม row hash และ RLS แบบบังคับต่อ workspace (ตาม README)

## 4. หลักฐานสถานะ

| รายการ | ค่า |
|---|---|
| Stack | Python, FastAPI, PostgreSQL (Neon) |
| Commit | 2 ครั้ง (2026-10-10 ถึง 2026-10-11) |
| ซอร์สโค้ด | 15 ไฟล์ / 2208 บรรทัด |
| เทสต์ | 2 ไฟล์ (ไม่ได้รันในงานนี้) |
| CI | ไม่มี workflow |
| Deploy config | Railway (Serverless) + Neon; ทะเบียน `online-systems.yaml` ระบุสถานะ online (ไม่ได้ probe สดในงานนี้) |
| Live URL | ดูทะเบียน `online-systems.yaml` (ไม่ได้ probe สด) |

## 5. คุณค่าและจุดต่าง

- เป็นส่วนหนึ่งของสถาปัตยกรรม decentralize ที่ 1 domain = 1 cell จึงเปลี่ยนหรือขายแยกได้
- ใช้สิทธิ์/audit กลางร่วมกันโดยไม่ต้องเขียนใหม่ต่อ cell
- **[สมมติฐาน]** มูลค่าเชิงรายได้ยังไม่พิสูจน์ — ใช้เป็นระบบของเจ้าของก่อน

## 6. ช่องว่างสำคัญ

- พึ่ง empire-platform ตัวเดียว: ถ้า platform ล่ม cell เขียนไม่ได้ (fail closed)
- ผู้ผลิต/ผู้บริโภคจริง (data-lake, event-hub) ยังต้องยืนยันว่าเรียก cell นี้ **[ต้องยืนยัน]**
- เทสต์มี 2 ไฟล์ (contract)

## 7. คำตัดสิน

**`invest`** — เป็นแกนข้อมูลของ domain ที่ระบบ Solo Empire ใช้จริงและตาม cell contract เดียวกัน จะเปลี่ยนเป็น `showcase` หากเจ้าของยุบ domain นี้หรือย้ายไปรวมกับ cell อื่น

## 8. ขั้นต่อไป

1. ผูกกับ solo-data-lake/solo-event-hub ให้ใช้ cell นี้เป็น intake จริง
2. เพิ่มเทสต์ lease/idempotency เฉพาะ domain

## การอนุมัติ

| บทบาท | สถานะ | วันที่ | ลงนาม |
|---|---|---|---|
| Product owner (Book Dev) | รอพิจารณา | — | ________ |

## แหล่งข้อมูล

- `README.md`
- `cell.yaml`
- `tests/`
