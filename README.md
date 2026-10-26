# 📋 คู่มือระบบกระดานประกาศแต่ละแผนก (Bulletin Board)
> **สำหรับนักพัฒนาใหม่ (Junior Developer Guide & Cheat Sheet)**  
> โค้ดชุดนี้ถูกออกแบบให้เข้าใจง่าย ตรงไปตรงมา มีคอมเมนต์ภาษาไทยอธิบายทุกขั้นตอน และแยกสไตล์ CSS ออกจาก JSX เพื่อให้อ่านโค้ดได้สบายตาที่สุด

---

## 🗂️ แผนผังโฟลเดอร์และไฟล์ทั้งหมด (Project Structure)

```
C:\Work\bulletin-easy/
│
├── database/
│   └── setup.sql           # คำสั่ง SQL สร้างตาราง, Trigger และข้อมูลตัวอย่าง
│
├── backend/
│   ├── .env                # จุดตั้งค่ารหัสผ่านฐานข้อมูล และ Port ของ Backend
│   ├── package.json        # รายการไลบรารีของ Backend (express, pg, multer, cors)
│   ├── server.js           # รวม REST API ทั้งหมดไว้ในไฟล์เดียว อ่านง่าย ไม่ต้องกระโดดไปมา
│   └── uploads/            # โฟลเดอร์ที่ระบบจะเซฟไฟล์รูปภาพที่แนบมากับโพสต์
│
└── frontend/
    ├── package.json        # รายการไลบรารีของ Frontend (React + Vite)
    ├── vite.config.js      # ตั้งค่า Vite พอร์ต 5173
    └── src/
        ├── main.jsx        # จุดเริ่มต้นรัน React
        ├── App.jsx         # ตัวกำหนดเส้นทางหน้าเว็บ (Routing)
        ├── App.css         # คลาสสไตล์ทั้งหมด (แยกออกมาเพื่อไม่ให้ JSX รก)
        ├── api.js          # ฟังก์ชันเรียก API รวมไว้ที่เดียว
        ├── components/
        │   └── Navbar.jsx  # แถบเมนูด้านบน แสดงอีเมลผู้ใช้ และปุ่มออกจากระบบ
        └── pages/
            ├── Login.jsx       # หน้าล็อกอิน (มีปุ่ม Google SSO และ Dev Login)
            ├── Success.jsx     # หน้ายืนยันตัวตน + ปุ่ม "คลิกเพื่อเข้าสู่แอปพลิเคชัน" (ข้อ 6)
            ├── Home.jsx        # หน้าแรก: ปุ่ม + สร้าง Folder แผนก และการ์ดแผนก (ข้อ 1 & 2)
            ├── Department.jsx  # หน้าแผนก: ปุ่ม + สร้าง Folder Category (ข้อ 3)
            └── Category.jsx    # หน้าห้องประกาศ: สร้างโพสต์, แนบรูป, Edit/Delete (ข้อ 3b & 4)
```

---

## 🚀 วิธีเปิดใช้งานโปรเจกต์ (Step-by-Step)

### ขั้นตอนที่ 1: ติดตั้งฐานข้อมูล (PostgreSQL)
1. เปิดโปรแกรม **pgAdmin** หรือเปิด terminal ของ PostgreSQL
2. สร้างฐานข้อมูลชื่อ `bulletin_db`
3. เปิดไฟล์ [`database/setup.sql`](database/setup.sql) แล้วกด **Run / Execute** ทั้งหมด
   - ระบบจะสร้างตาราง `departments`, `categories`, `posts` และใส่ข้อมูลตัวอย่างให้พร้อมทดสอบทันที

### ขั้นตอนที่ 2: รัน Backend (พอร์ต 5000)
1. เปิด Terminal แล้วเข้าไปที่โฟลเดอร์ backend:
   ```bash
   cd C:\Work\bulletin-easy\backend
   ```
2. ติดตั้งไลบรารี:
   ```bash
   npm install
   ```
3. ตรวจสอบรหัสผ่าน Database ในไฟล์ `.env`:
   ```env
   PORT=5000
   PGHOST=localhost
   PGPORT=5432
   PGUSER=postgres
   PGPASSWORD=postgres
   PGDATABASE=bulletin_db
   ```
4. สั่งรันเซิร์ฟเวอร์:
   ```bash
   npm start
   # หรือรันแบบรีโหลดอัตโนมัติเมื่อแก้โค้ด:
   npm run dev
   ```

### ขั้นตอนที่ 3: รัน Frontend (พอร์ต 5173)
1. เปิดอีกหน้าต่าง Terminal แล้วเข้าไปที่โฟลเดอร์ frontend:
   ```bash
   cd C:\Work\bulletin-easy\frontend
   ```
2. สั่งรันหน้าเว็บ:
   ```bash
   npm run dev
   ```
3. เบราว์เซอร์จะเปิดไปที่ `http://localhost:5173` อัตโนมัติ

---

## 🧭 การทำงานของระบบตรงตามโจทย์ 7 ข้ออย่างไร?

| ข้อที่ | ความต้องการ | โค้ดที่รับผิดชอบ | จุดสังเกตในโค้ด |
| :---: | :--- | :--- | :--- |
| **1** | ปุ่ม + สำหรับสร้าง Folder แผนก | [`Home.jsx`](frontend/src/pages/Home.jsx) | ปุ่ม `<button onClick={() => setIsModalOpen(true)} className="btn btn-primary">+ สร้าง Folder แผนก</button>` |
| **2** | การ์ด Folder คลิกเข้าไปเป็นของแผนกนั้นๆ | [`Home.jsx`](frontend/src/pages/Home.jsx) | การ์ดมี `onClick={() => navigate('/department/' + dept.id)}` |
| **3** | ด้านในการ์ดแผนก สามารถสร้าง Folder แยก Category | [`Department.jsx`](frontend/src/pages/Department.jsx) | มีปุ่ม `+ สร้าง Folder หมวดหมู่` และแสดงการ์ด Category ย่อย |
| **3b**| ด้านใน Category สามารถสร้าง Post ประกาศ แนบรูป และข้อความได้ | [`Category.jsx`](frontend/src/pages/Category.jsx) | มีปุ่ม `+ สร้างโพสต์ประกาศใหม่` มีฟอร์มกรอกข้อความและอัปโหลดรูปภาพพร้อม Live Preview |
| **4** | Post สามารถ EDIT, DELETE ได้เฉพาะผู้สร้าง | [`Category.jsx`](frontend/src/pages/Category.jsx) และ [`server.js`](backend/server.js) | **Frontend:** ตรวจ `userEmail === post.created_by_email` ถ้าตรงถึงจะแสดงปุ่ม Edit/Delete<br>**Backend:** ใน `PUT /api/posts/:id` และ `DELETE /api/posts/:id` เช็คกับ Database ถ้าไม่ตรงจะตอบกลับ `403 Forbidden` |
| **5** | นำ Session จากการ Login มาใช้ยืนยันตัวตน | [`api.js`](frontend/src/api.js) และ ทุกหน้า | เก็บ `user_session` ไว้ใน `localStorage` และส่งอีเมลแนบไปกับทุกการสร้าง/แก้ไข/ลบ |
| **6** | กดปุ่ม 'คลิกเพื่อเข้าสู่แอปพลิเคชัน' ให้ตรงมาที่หน้า Home | [`Success.jsx`](frontend/src/pages/Success.jsx) | มีปุ่มเด่นชัด **"คลิกเพื่อเข้าสู่แอปพลิเคชัน"** นำทางตรงมาที่ `/home` |
| **7** | ข้อมูล Input บันทึกลง Database pgsql | [`database/setup.sql`](database/setup.sql) | ตารางและคำสั่ง SQL ครบถ้วนตามมาตรฐาน PostgreSQL |

---

## 🛠️ จุดแก้ไขยอดฮิตสำหรับเด็กใหม่ (Junior Dev Cheat Sheet)

### 1. อยากเปลี่ยนสีปุ่ม หรือขนาดตัวอักษร
- เปิดไฟล์ [`frontend/src/App.css`](frontend/src/App.css)
- คลาสถูกตั้งชื่อตรงไปตรงมา เช่น:
  - `.btn-primary` = ปุ่มสีน้ำเงินหลัก
  - `.btn-success` = ปุ่มสีเขียวสำหรับสร้างประกาศ
  - `.folder-card` = กล่องการ์ดโฟลเดอร์
  - `.navbar` = แถบเมนูด้านบน

### 2. อยากเพิ่มช่องกรอกข้อมูลในโพสต์ (เช่น เพิ่มเบอร์โทรติดต่อ หรือแท็ก)
1. **ใน Database ([`setup.sql`](database/setup.sql)):**
   ```sql
   ALTER TABLE posts ADD COLUMN phone_number VARCHAR(50);
   ```
2. **ใน Backend ([`server.js`](backend/server.js)):**
   - ใน `app.post('/api/posts')`: รับค่า `const { phone_number } = req.body;` และเพิ่มในคำสั่ง SQL `INSERT`
3. **ใน Frontend ([`Category.jsx`](frontend/src/pages/Category.jsx)):**
   - สร้าง state: `const [phone, setPhone] = useState('');`
   - ในฟังก์ชัน `handleCreatePost`: ใส่ `formData.append('phone_number', phone);`
   - เพิ่ม `<input>` ในฟอร์ม Modal เพื่อให้ผู้ใช้กรอก

### 3. ต้องการทดสอบระบบแบบไม่ต้องต่อ Google Apps Script
- ที่หน้า Login (`http://localhost:5173/login`) ให้คลิกข้อความด้านล่าง:
  `🛠️ โหมดทดสอบสำหรับนักพัฒนา (Dev Login)`
- พิมพ์อีเมลอะไรก็ได้ เช่น `john.doe@minebea.co.th` แล้วกดปุ่ม **"เข้าสู่ระบบ"**
- ระบบจะจำลองว่าคุณคือผู้ใช้อีเมลนั้นทันที ทำให้ทดสอบสิทธิ์การสร้างและแก้ไขโพสต์ได้สะดวกรวดเร็ว

