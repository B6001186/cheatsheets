# MongoDB Cheatsheet

## การเข้าใช้งาน
```bash
# เข้าใช้งาน MongoDB Shell
mongosh

# ระบุ Database
use my_database
```

## Database Operations
```javascript
// แสดงรายชื่อ databases
show dbs

// แสดง database ปัจจุบัน
db

// สร้างหรือเปลี่ยน database
use my_database

// ลบ database
db.dropDatabase()
```

## Collection Operations
```javascript
// แสดง collections
show collections

// สร้าง collection
db.createCollection("users")

// ลบ collection
db.users.drop()
```

## CRUD Operations

### Create (Insert)
```javascript
// Insert เอกสารเดียว
db.users.insertOne({
  name: "Somchai",
  age: 25,
  email: "somchai@email.com"
})

// Insert หลายเอกสาร
db.users.insertMany([
  { name: "Somsak", age: 30, city: "Bangkok" },
  { name: "Somsri", age: 22, city: "Chiang Mai" }
])
```

### Read (Find)
```javascript
// ค้นหาทั้งหมด
db.users.find()

// ค้นหาทั้งหมด (เรียงสวยงาม)
db.users.find().pretty()

// ค้นหาแบบมีเงื่อนไข
db.users.find({ name: "Somchai" })

// ค้นหาด้วยเงื่อนไขหลายค่า
db.users.find({ age: { $gt: 20 }, city: "Bangkok" })

// ค้นหาเฉพาะบาง field
db.users.find({}, { name: 1, email: 1, _id: 0 })

// ค้นหาแค่เอกสารเดียว
db.users.findOne({ name: "Somchai" })

// จำกัดจำนวนผลลัพธ์
db.users.find().limit(5)

// ข้ามเอกสาร
db.users.find().skip(10)

// เรียงลำดับ (1 = ASC, -1 = DESC)
db.users.find().sort({ age: 1 })
```

### Query Operators
```javascript
// เปรียบเทียบ
db.users.find({ age: { $eq: 25 } })    // เท่ากับ
db.users.find({ age: { $ne: 25 } })    // ไม่เท่ากับ
db.users.find({ age: { $gt: 25 } })    // มากกว่า
db.users.find({ age: { $gte: 25 } })   // มากกว่าหรือเท่ากับ
db.users.find({ age: { $lt: 25 } })    // น้อยกว่า
db.users.find({ age: { $lte: 25 } })   // น้อยกว่าหรือเท่ากับ

// Logical
db.users.find({ $and: [{ age: { $gt: 20 } }, { city: "Bangkok" }] })
db.users.find({ $or: [{ city: "Bangkok" }, { city: "Chiang Mai" }] })
db.users.find({ $nor: [{ age: 25 }, { city: "Bangkok" }] })
db.users.find({ $not: { age: { $gt: 25 } } })

// Array
db.users.find({ tags: { $in: ["mongodb", "database"] } })
db.users.find({ tags: { $nin: ["sql"] } })
db.users.find({ tags: { $all: ["mongodb", "nosql"] } })
db.users.find({ tags: { $size: 3 } })

// Element
db.users.find({ email: { $exists: true } })
db.users.find({ age: { $type: "int" } })

// Pattern Matching (Regex)
db.users.find({ name: { $regex: /^Som/, $options: "i" } })
```

### Update
```javascript
// อัปเดตเอกสารเดียว
db.users.updateOne(
  { name: "Somchai" },
  { $set: { age: 26 } }
)

// อัปเดตหลายเอกสาร
db.users.updateMany(
  { city: "Bangkok" },
  { $set: { region: "Central" } }
)

// แทนที่เอกสาร
db.users.replaceOne(
  { name: "Somchai" },
  { name: "Somchai", age: 27, email: "somchai@new.com", city: "Phuket" }
)
```

### Update Operators
```javascript
// $set - กำหนดค่า
db.users.updateOne({ _id: 1 }, { $set: { age: 30 } })

// $unset - ลบ field
db.users.updateOne({ _id: 1 }, { $unset: { temporary: "" } })

// $inc - เพิ่ม/ลดค่า (ตัวเลข)
db.users.updateOne({ _id: 1 }, { $inc: { age: 1 } })

// $rename - เปลี่ยนชื่อ field
db.users.updateOne({ _id: 1 }, { $rename: { "oldName": "newName" } })

// $push - เพิ่มค่าใน array
db.users.updateOne({ _id: 1 }, { $push: { tags: "new-tag" } })

// $pull - ลบค่าจาก array
db.users.updateOne({ _id: 1 }, { $pull: { tags: "old-tag" } })

// $addToSet - เพิ่มค่าใน array (ไม่ซ้ำ)
db.users.updateOne({ _id: 1 }, { $addToSet: { tags: "unique-tag" } })
```

### Delete
```javascript
// ลบเอกสารเดียว
db.users.deleteOne({ name: "Somchai" })

// ลบหลายเอกสาร
db.users.deleteMany({ age: { $lt: 18 } })

// ลบทั้งหมด
db.users.deleteMany({})
```

## Aggregation Pipeline
```javascript
// โครงสร้าง pipeline
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$customerId", total: { $sum: "$amount" } } },
  { $sort: { total: -1 } },
  { $limit: 10 }
])

// ตัวอย่าง: กลุ่มตามเมืองและนับจำนวน
db.users.aggregate([
  { $group: { _id: "$city", count: { $sum: 1 } } }
])

// ตัวอย่าง: คำนวณค่าเฉลี่ย
db.users.aggregate([
  { $group: { _id: null, avgAge: { $avg: "$age" } } }
])

// $lookup (JOIN)
db.orders.aggregate([
  {
    $lookup: {
      from: "customers",
      localField: "customerId",
      foreignField: "_id",
      as: "customer"
    }
  }
])

// $project (เลือก/เปลี่ยนรูป field)
db.users.aggregate([
  { $project: { name: 1, age: 1, ageGroup: { $cond: [{ $gte: ["$age", 18] }, "adult", "minor"] } } }
])

// $unwind (แยก array)
db.users.aggregate([
  { $unwind: "$tags" }
])
```

## Indexing
```javascript
// สร้าง index
db.users.createIndex({ name: 1 })

// สร้าง compound index
db.users.createIndex({ name: 1, age: -1 })

// สร้าง unique index
db.users.createIndex({ email: 1 }, { unique: true })

// สร้าง text index (สำหรับ full-text search)
db.users.createIndex({ name: "text", description: "text" })

// แสดง indexes
db.users.getIndexes()

// ลบ index
db.users.dropIndex("name_1")

// อธิบาย query (ดูการใช้งาน index)
db.users.find({ name: "Somchai" }).explain("executionStats")
```

## การจัดการ Users และ Roles
```javascript
// สร้าง user
db.createUser({
  user: "myuser",
  pwd: "mypassword",
  roles: ["readWrite", { role: "read", db: "other_db" }]
})

// แสดง users
show users

// ลบ user
db.dropUser("myuser")

// อัปเดต user
db.updateUser("myuser", { roles: ["readWrite"] })
```

## Backup & Restore
```bash
# Backup database
mongodump --db my_database --out /backup/

# Restore database
mongorestore --db my_database /backup/my_database/

# Export collection to JSON
mongoexport --db my_database --collection users --out users.json

# Import JSON to collection
mongoimport --db my_database --collection users --file users.json
```

## การตรวจสอบประสิทธิภาพ
```javascript
// ดูสถานะ database
db.stats()

// ดูสถานะ collection
db.users.stats()

// ดูการทำงานที่กำลังรัน
db.currentOp()

// หยุดการทำงานที่ช้า
db.killOp(opid)

// ดู profile (query ที่ช้า)
db.setProfilingLevel(1, { slowms: 100 })
db.system.profile.find().sort({ ts: -1 }).limit(5)
```

## Useful Commands
```javascript
// นับจำนวนเอกสาร
db.users.countDocuments({ age: { $gt: 20 } })

// ค่าที่ไม่ซ้ำ
db.users.distinct("city")

// เอกสารสุ่ม
db.users.aggregate([{ $sample: { size: 3 } }])

// ประมาณจำนวนเอกสาร
db.users.estimatedDocumentCount()

// สร้าง ObjectId
ObjectId()

// ตรวจสอบ ObjectId
ObjectId.isValid("507f1f77bcf86cd799439011")
```
