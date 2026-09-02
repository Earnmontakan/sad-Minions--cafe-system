# แบบฟอร์ม AI Disclosure เพิ่มเติมสำหรับ Coding Sprint (งานกลุ่ม)
## สัปดาห์ที่ 5: Coding Sprint 1 — โครงสร้างโปรเจกต์และการเชื่อมต่อฐานข้อมูล

---

### 1. ข้อมูลทั่วไปของโปรเจกต์และสมาชิกกลุ่ม
* **รายวิชา:** 88734065 การวิเคราะห์และออกแบบระบบ (System Analysis and Design)
* **ชื่อโปรเจกต์:** ระบบจัดการร้านกาแฟ (Cafe Ordering & POS System)
* **Sprint:** Coding Sprint 1 (สัปดาห์ที่ 5)
* **GitHub Repository URL:** `https://github.com/Earnmontakan/sad-Minions--cafe-system.git`
* **สมาชิกกลุ่ม:**
 1. 67160002 นางสาวชาลิสา ศรีหงษ์
 2. 67160042 นางสาวมนทกานต์ ปาลี
 3. 67160178 นางสาวจตุพร ธรรมฤทธิ์
 4. 67160190 นางสาวบุษชยาภา คะสุดใจ
 5. 67160292 นางสาวชนิดาภา ชาภักดี


---

### 2. แผนภาพและเอกสารต้นทางของ Sprint (Source Design Artifacts)
* **Diagram ต้นทาง:** Use Case Diagram และ Use Case Description จากไฟล์ `wk04-user-stories.md`
* **Use Case ที่เลือก Implement ใน Sprint นี้:** Use Case **"รับออเดอร์และคำนวณราคา"**
* **Mapping สู่ API Endpoint:** `POST /api/orders`
* **User Stories และ Acceptance Criteria ที่เกี่ยวข้อง:**
  * **US-01 (Happy Path):** As a ลูกค้า, I want สั่งออเดอร์พร้อมเลือกวิธีชำระเงินและให้ระบบคำนวณยอดรวมให้อัตโนมัติ, So that มั่นใจว่ายอดเงินที่ต้องจ่ายถูกต้องโดยไม่ต้องคำนวณเอง
  * **US-02 (Payment Method Validation):** As a ลูกค้า, I want เห็นข้อความแจ้งเตือนทันทีเมื่อเลือกวิธีชำระเงินที่ไม่รองรับหรือไม่ได้เลือก, So that แก้ไขก่อนสั่งซื้อโดยไม่เสียเวลา
  * **US-03 (Items Data Validation):** As a ลูกค้า, I want ให้ระบบตรวจสอบความถูกต้องของรายการสินค้าทุกรายการก่อนบันทึกออเดอร์ (ต้องมีสินค้าอย่างน้อย 1 รายการ, ระบุชื่อครบ, ราคาและจำนวนต้องมากกว่า 0), So that มั่นใจว่าออเดอร์ที่บันทึกไม่มีข้อมูลผิดพลาดที่กระทบยอดเงินหรือการเตรียมสินค้า

---

### 3. ขอบเขตและเครื่องมือ AI ที่ใช้งาน (AI Tools & Scope of Usage)
* **เครื่องมือ AI ที่ใช้:** Antigravity / Claude 3.5 Sonnet / ChatGPT
* **วัตถุประสงค์การใช้งาน:**
  1. ช่วย Scaffold โครงสร้างโฟลเดอร์ตามแนวคิด MVC (`src/routes`, `src/controllers`, `src/models`, `src/config`, `public`)
  2. ช่วยสร้าง Boilerplate การเชื่อมต่อฐานข้อมูล MySQL ด้วย `mysql2/promise` ในรูปแบบ Connection Pool
  3. ช่วยจัดรูปแบบโครงสร้าง Routing และ Controller พื้นฐานของ Express.js
* **ตัวอย่าง Prompt ที่ใช้:**
  ```text
  "ช่วย scaffold โครงสร้างโปรเจกต์ Node.js/Express สำหรับระบบ POS ร้านกาแฟ (cafe-pos-system) 
   ตามสถาปัตยกรรม MVC โดยมี endpoint 'POST /api/orders' สำหรับ Use Case 'รับออเดอร์และคำนวณราคา' 
   ที่เชื่อมต่อ MySQL ผ่าน Connection Pool (mysql2/promise) และทำ Validation ข้อมูลตาม Acceptance Criteria 
   ของ US-01, US-02 และ US-03 ดังนี้:
   1. ตรวจสอบว่า items ต้องเป็น array และไม่ว่างเปล่า
   2. ตรวจสอบชื่อสินค้า (name) ต้องไม่เป็นค่าว่าง
   3. ตรวจสอบราคา (price) ต้องเป็นตัวเลขจำกัดและมากกว่า 0
   4. ตรวจสอบจำนวน (quantity) ต้องเป็นเลขจำนวนเต็มและมากกว่า 0
   5. ตรวจสอบ paymentMethod ต้องอยู่ใน whitelist ['cash', 'credit', 'qr']
   6. backend คำนวณ totalAmount เองและบันทึกลงตาราง orders แล้วตอบกลับ orderId พร้อม totalAmount"
  ```

---

### 4. การตรวจสอบและปรับปรุงแก้ไขโค้ดหลังได้รับจาก AI (Verification & Refinements)
กลุ่มได้ตรวจสอบโค้ดที่ AI สร้างขึ้นอย่างละเอียดและดำเนินการปรับปรุงแก้ไข ดังนี้:
1. **การปรับปรุง Type Checking:** AI เริ่มต้นใช้ `typeof item.price !== "number"` ซึ่งมีช่องโหว่ให้ค่า `NaN` หรือ `Infinity` ผ่านได้ จึงปรับแก้เป็น `!Number.isFinite(item.price) || item.price <= 0` และใช้ `!Number.isInteger(item.quantity) || item.quantity <= 0` เพื่อป้องกันค่าทศนิยมในจำนวนสินค้า
2. **การเพิ่ม Validation ป้องกันสินค้าชื่อซ้ำ:** เพิ่มการตรวจจับกรณีส่งสินค้าชื่อเดียวกันซ้ำหลาย Object ใน `items` เดียวกัน โดยใช้ `Set` และแปลงเป็นตัวพิมพ์เล็กพร้อมตัดช่องว่าง (`trim().toLowerCase()`) หากพบรายการซ้ำจะปฏิเสธด้วย `400 Bad Request`
3. **การจัดการ Security & Environment:** ตรวจสอบให้แน่ใจว่าไฟล์ `.env` มีการใส่ไว้ใน `.gitignore` และจัดทำไฟล์ `.env.example` เพื่อให้สมาชิกในทีม clone ไปตั้งค่าได้อย่างปลอดภัยโดยไม่มี Credential หลุดเข้า Git
4. **การจัดโครงสร้างแบบ Model-Controller (mc):** ปรับแยกส่วน Query SQL ออกจาก Controller ไปไว้ใน `src/models/orderModel.js` เพื่อให้สอดคล้องกับหลักการแยกความรับผิดชอบ (Separation of Concerns) ตามแบบจำลอง MVC

---

### 5. จุดต่างจาก Diagram เดิม (Discrepancies / Deviations from Original Design)
ให้บันทึกไว้ในแบบฟอร์มนี้เพื่อนำไปปรับปรุงในสัปดาห์ถัดไปโดยไม่ต้องย้อนแก้ไฟล์ `wk04-user-stories.md`:

| จุดที่แตกต่าง | สิ่งที่ออกแบบไว้ใน wk04 | สิ่งที่ Implement จริงใน Sprint 1 (wk05) | เหตุผลและความจำเป็นเชิงเทคนิค |
| :--- | :--- | :--- | :--- |
| **โครงสร้างตารางฐานข้อมูล** | บันทึกออเดอร์พร้อมรายละเอียดสินค้าแต่ละชิ้น | ตาราง `orders` บันทึกเพียง `total_amount` รวม ยังไม่มีตารางแยก `order_items` | เป็น Scaffold ขั้นต้นตามข้อกำหนด Sprint 1 เพื่อให้ endpoint ทำงานได้ภายในเวลาจำกัด และจะขยายเป็น ER Diagram เต็มรูปแบบในสัปดาห์ที่ 7 |
| **การตรวจสอบสินค้าชื่อซ้ำ** | ไม่ได้ระบุเป็น Exception Flow ชัดเจนใน wk04 | เพิ่มเงื่อนไขตรวจสอบไม่ให้ `name` ซ้ำกันใน `items` เดียวกัน | เพื่อป้องกันความสับสนในการแสดงผลใบเสร็จ และเตรียมความพร้อมสำหรับตาราง `order_items` ในอนาคต |
| **วิธีการชำระเงิน (Payment Method)** | ออกแบบให้รองรับการชำระเงินหลายรูปแบบรวมถึง Gateway | ตรวจสอบแบบ Whitelist (`cash`, `credit`, `qr`) และจำลอง (Mock) การบันทึก | ขอบเขตของระบบ POS ในระดับการศึกษามุ่งเน้นตรรกะการคำนวณและบันทึกข้อมูล ยังไม่ต้องเชื่อมต่อระบบธนาคาร/Gateway จริง |

---

### 6. ผลการทดสอบ API Endpoint (Test Results)

#### 6.1 ทดสอบกรณีสำเร็จ (Happy Path - 201 Created):
* **Request:**
  ```bash
  curl -X POST http://localhost:3000/api/orders \
    -H "Content-Type: application/json" \
    -d '{"paymentMethod":"cash","items":[{"name":"อเมริกาโน่","price":45,"quantity":2},{"name":"ครัวซองต์","price":35,"quantity":1}]}'
  ```
* **Response Status:** `201 Created`
* **Response Body:**
  ```json
  {
    "orderId": 1,
    "totalAmount": 125
  }
  ```

#### 6.2 ทดสอบกรณีข้อมูลไม่ถูกต้อง (Validation Error Cases - 400 Bad Request):
1. **กรณี items ว่างเปล่า:**
   * Response: `400 Bad Request` -> `{"error": "ต้องมีรายการสินค้าอย่างน้อย 1 รายการ"}`
2. **กรณี price <= 0 หรือไม่ใช่ตัวเลข:**
   * Response: `400 Bad Request` -> `{"error": "price ต้องมากกว่า 0"}`
3. **กรณี quantity ไม่ใช่จำนวนเต็มบวก:**
   * Response: `400 Bad Request` -> `{"error": "quantity ต้องมากกว่า 0"}`
4. **กรณี paymentMethod ไม่ถูกต้อง:**
   * Response: `400 Bad Request` -> `{"error": "paymentMethod ไม่ถูกต้องหรือไม่ได้ระบุ"}`
5. **กรณีส่งชื่อสินค้าซ้ำกันในรายการเดียว:**
   * Response: `400 Bad Request` -> `{"error": "พบรายการสินค้าชื่อซ้ำกันในออเดอร์ กรุณารวมรายการเป็นชื่อเดียวและปรับจำนวน (quantity)"}`

---

### 7. ปัญหาที่พบระหว่างการพัฒนาและแนวทางแก้ไข
1. **ปัญหา Connection Timeout / Exhaustion:**  
   * *สาเหตุ:* การเชื่อมต่อฐานข้อมูลโดยไม่จำกัดอาจทำให้ MySQL ทำงานหนักเมื่อมี Request เข้ามาถี่
   * *วิธีแก้ไข:* นำ `mysql2/promise` Connection Pool มาใช้งานพร้อมกำหนด `connectionLimit: 10` และ `waitForConnections: true`
2. **ปัญหา Environment Variables ไม่ถูกโหลด:**  
   * *สาเหตุ:* ลืมติดตั้งหรือเรียกใช้ `dotenv`  
   * *วิธีแก้ไข:* เพิ่มคำสั่ง `require("dotenv").config()` ในส่วนเริ่มต้นของโปรเจกต์

---

### 8. การรับรองความเข้าใจและการปฏิบัติตามนโยบาย (Team Declaration)
สมาชิกทุกคนในกลุ่มได้ร่วมกันศึกษา ทดสอบ และทำความเข้าใจการทำงานของซอร์สโค้ดทุกบรรทัด ทั้งในส่วนของ Express Server, Routing, Controller, Data Validation, และ Database Connection Pool โดยพร้อมที่จะตอบคำถามและอธิบายรายละเอียดการทำงานต่อผู้สอน และยืนยันว่าการใช้ AI ในครั้งนี้เป็นไปตามกรอบนโยบาย AI-USAGE-POLICY อย่างถูกต้องครบถ้วน
