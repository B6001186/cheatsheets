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

### Cursor Methods
```javascript
// แปลงผลลัพธ์เป็น array
db.users.find().toArray()

// วนลูปผ่านแต่ละเอกสาร
db.users.find().forEach(doc => printjson(doc))

// แปลงค่าใน cursor
db.users.find().map(doc => doc.name)

// นับจำนวนเอกสารจาก cursor (ไม่ส่ง query ไปใหม่)
db.users.find({ age: { $gt: 20 } }).count()

// นับจำนวนเอกสาร (ส่ง hint เกี่ยวกับ index)
db.users.find({ age: { $gt: 20 } }).count({ hint: "age_1" })

// อ่าน cursor ทีละตัว
let cursor = db.users.find()
while (cursor.hasNext()) {
  printjson(cursor.next())
}

// ดึงเอกสารถัดไป
cursor.next()

// ตรวจสอบว่ามีเอกสถารถัดไป
cursor.hasNext()

// ทำลาย cursor (หยุดการดึงข้อมูล)
cursor.close()

// ตั้งค่า batch size (จำนวนเอกสารต่อรอบ)
db.users.find().batchSize(50)

// ตั้งเวลา timeout ของ cursor (ms)
db.users.find().noCursorTimeout()

// ระบุ index ให้ query ใช้
db.users.find({ name: "Somchai" }).hint({ name: 1 })

// อ่านแบบถอยหลังจากท้าย
db.users.find().sort({ $natural: -1 })

// รับประกันการอ่านจาก primary (กรณี replica set)
db.users.find().readPref("primary")
```

### Query Operators
```javascript
// เปรียบเทียบ
db.users.find({ age: { $eq: 25 } })    // เท่ากับ (equal)
db.users.find({ age: { $ne: 25 } })    // ไม่เท่ากับ (not equal)
db.users.find({ age: { $gt: 25 } })    // มากกว่า (greater than)
db.users.find({ age: { $gte: 25 } })   // มากกว่าหรือเท่ากับ
db.users.find({ age: { $lt: 25 } })    // น้อยกว่า (less than)
db.users.find({ age: { $lte: 25 } })   // น้อยกว่าหรือเท่ากับ

// Logical
db.users.find({ $and: [{ age: { $gt: 20 } }, { city: "Bangkok" }] })
db.users.find({ $or: [{ city: "Bangkok" }, { city: "Chiang Mai" }] })
db.users.find({ $nor: [{ age: 25 }, { city: "Bangkok" }] })
db.users.find({ $not: { age: { $gt: 25 } } })

// Array
db.users.find({ tags: { $in: ["mongodb", "database"] } })  // อยู่ใน array
db.users.find({ tags: { $nin: ["sql"] } })                  // ไม่อยู่ใน array
db.users.find({ tags: { $all: ["mongodb", "nosql"] } })     // ต้องมีทั้งหมด
db.users.find({ tags: { $size: 3 } })                       // array มีขนาดเท่ากับ
db.users.find({ scores: { $elemMatch: { $gte: 80, $lte: 90 } } }) // อย่างน้อย 1 ตัวตรงเงื่อนไข

// Element
db.users.find({ email: { $exists: true } })  // มี field นี้อยู่
db.users.find({ age: { $type: "int" } })     // ตรวจสอบ BSON type
db.users.find({ age: { $type: ["int", "double"] } })

// Pattern Matching (Regex)
db.users.find({ name: { $regex: /^Som/, $options: "i" } }) // case-insensitive
db.users.find({ name: /^som/i })                            // รูปแบบย่อ

// Evaluation
db.users.find({ $expr: { $gt: ["$age", "$maxAge"] } })          // เปรียบเทียบ field
db.users.find({ $expr: { $eq: ["$username", "$email"] } })      // ตรวจสอบค่าเท่ากัน
db.users.find({ $where: "this.age > 20" })                      // JavaScript expression
db.users.find({ $jsonSchema: {                                  // ตรวจสอบ schema
  required: ["name", "email"],
  properties: {
    name: { bsonType: "string" },
    age: { bsonType: "int", minimum: 0 }
  }
}})

// Geospatial
db.places.find({ location: { $near: { $geometry: { type: "Point", coordinates: [100.5, 13.7] }, $maxDistance: 1000 } } })
db.places.find({ location: { $geoWithin: { $centerSphere: [[100.5, 13.7], 0.01] } } })
db.places.find({ location: { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [[[100,13],[101,13],[101,14],[100,14],[100,13]]] } } } })

// Bitwise
db.users.find({ flags: { $bitsAllSet: 5 } })
db.users.find({ flags: { $bitsAnySet: 3 } })
db.users.find({ flags: { $bitsAllClear: 0 } })
db.users.find({ flags: { $bitsAnyClear: 2 } })

// Projection operators
db.users.find({}, { name: 1, scores: { $slice: 3 } })      // ดึงแค่ 3 ตัวแรกของ array
db.users.find({}, { name: 1, scores: { $slice: [1, 2] } }) // ข้าม 1 ตัว แล้วเอา 2 ตัว
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

// ค้นหาแล้วอัปเดต (ส่งคืนเอกสารก่อนหรือหลังอัปเดต)
db.users.findOneAndUpdate(
  { name: "Somchai" },
  { $set: { age: 28 } },
  { returnDocument: "after" } // "before" หรือ "after"
)

// ค้นหาแล้วลบ
db.users.findOneAndDelete({ name: "Somchai" })

// ค้นหาแล้วแทนที่
db.users.findOneAndReplace(
  { name: "Somchai" },
  { name: "Somchai", age: 30, city: "Phuket" }
)

// upsert — อัปเดตหรือสร้างใหม่ถ้าไม่พบ
db.users.updateOne(
  { name: "NewUser" },
  { $set: { age: 20, city: "Bangkok" } },
  { upsert: true }
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

// $mul - คูณค่า (ตัวเลข)
db.users.updateOne({ _id: 1 }, { $mul: { price: 1.1 } })

// $rename - เปลี่ยนชื่อ field
db.users.updateOne({ _id: 1 }, { $rename: { "oldName": "newName" } })

// $min - อัปเดตเฉพาะค่าที่น้อยกว่า
db.users.updateOne({ _id: 1 }, { $min: { age: 18 } })

// $max - อัปเดตเฉพาะค่าที่มากกว่า
db.users.updateOne({ _id: 1 }, { $max: { score: 100 } })

// $currentDate - ตั้งค่าเป็นวันที่ปัจจุบัน
db.users.updateOne({ _id: 1 }, { $currentDate: { lastModified: true } })

// $push - เพิ่มค่าใน array
db.users.updateOne({ _id: 1 }, { $push: { tags: "new-tag" } })

// $push พร้อม modifiers
db.users.updateOne({ _id: 1 }, {
  $push: {
    scores: {
      $each: [85, 90, 78],  // เพิ่มหลายค่า
      $sort: -1,             // เรียงจากมากไปน้อย
      $slice: 5              // เก็บแค่ 5 ค่า
    }
  }
})

// $pull - ลบค่าจาก array
db.users.updateOne({ _id: 1 }, { $pull: { tags: "old-tag" } })

// $pullAll - ลบหลายค่าจาก array
db.users.updateOne({ _id: 1 }, { $pullAll: { tags: ["tag1", "tag2"] } })

// $pop - ลบค่าแรก (-1) หรือค่าสุดท้าย (1) ของ array
db.users.updateOne({ _id: 1 }, { $pop: { tags: 1 } })   // ลบตัวสุดท้าย
db.users.updateOne({ _id: 1 }, { $pop: { tags: -1 } })  // ลบตัวแรก

// $addToSet - เพิ่มค่าใน array (ไม่ซ้ำ)
db.users.updateOne({ _id: 1 }, { $addToSet: { tags: "unique-tag" } })

// $each ร่วมกับ $addToSet
db.users.updateOne({ _id: 1 }, { $addToSet: { tags: { $each: ["a", "b", "c"] } } })
```

### Delete
```javascript
// ลบเอกสารเดียว
db.users.deleteOne({ name: "Somchai" })

// ลบหลายเอกสาร
db.users.deleteMany({ age: { $lt: 18 } })

// ลบทั้งหมด
db.users.deleteMany({})

// ค้นหาแล้วลบ (ส่งคืนเอกสารที่ถูกลบ)
db.users.findOneAndDelete({ status: "inactive" })
```

## Text Search (Full-Text Search)
```javascript
// สร้าง text index
db.articles.createIndex({ title: "text", content: "text" })

// ค้นหาข้อความ
db.articles.find({ $text: { $search: "mongodb database" } })

// ค้นหาแบบ phrase (ใส่เครื่องหมายคำพูด)
db.articles.find({ $text: { $search: "\"mongodb atlas\"" } })

// ไม่รวมคำที่ต้องการ
db.articles.find({ $text: { $search: "mongodb -sql" } })

// เรียงตาม relevance score
db.articles.find(
  { $text: { $search: "mongodb" } },
  { score: { $meta: "textScore" } }
).sort({ score: { $meta: "textScore" } })

// กำหนดภาษา
db.articles.createIndex({ content: "text" }, { default_language: "th" })

// ค้นหาแบบ $search พร้อม $caseSensitive และ $diacriticSensitive
db.articles.find({
  $text: { $search: "MongoDB", $caseSensitive: false, $diacriticSensitive: false }
})
```

## Aggregation Pipeline

### Pipeline Stages
```javascript
// โครงสร้าง pipeline
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$customerId", total: { $sum: "$amount" } } },
  { $sort: { total: -1 } },
  { $limit: 10 }
])

// $match — กรองเอกสาร (เหมือน find)
db.users.aggregate([{ $match: { age: { $gte: 18 } } }])

// $group — จัดกลุ่มและคำนวณ
db.users.aggregate([
  { $group: { _id: "$city", count: { $sum: 1 }, avgAge: { $avg: "$age" } } }
])

// $group — รวมทุกเอกสาร (null _id)
db.users.aggregate([
  { $group: { _id: null, totalAge: { $sum: "$age" }, avgAge: { $avg: "$age" } } }
])

// $sort — เรียงลำดับ
db.users.aggregate([{ $sort: { age: -1, name: 1 } }])

// $limit — จำกัดจำนวน
db.users.aggregate([{ $limit: 10 }])

// $skip — ข้ามเอกสาร
db.users.aggregate([{ $skip: 20 }])

// $project — เลือก/เปลี่ยนรูป field (1 = แสดง, 0 = ซ่อน)
db.users.aggregate([
  { $project: { name: 1, age: 1, ageGroup: { $cond: [{ $gte: ["$age", 18] }, "adult", "minor"] } } }
])

// $addFields / $set — เพิ่ม field ใหม่โดยไม่ลบ field อื่น
db.users.aggregate([
  { $addFields: { fullName: { $concat: ["$firstName", " ", "$lastName"] }, isAdult: { $gte: ["$age", 18] } } }
])

// $unset — ลบ field
db.users.aggregate([
  { $unset: ["temporaryField", "internalNote"] }
])

// $count — นับจำนวนเอกสารที่เหลือใน pipeline
db.users.aggregate([
  { $match: { age: { $gt: 20 } } },
  { $count: "totalCount" }
])

// $sample — สุ่มเอกสาร
db.users.aggregate([{ $sample: { size: 5 } }])

// $unwind — แยก array ออกเป็นหลายเอกสาร
db.users.aggregate([{ $unwind: "$tags" }])

// $unwind — รวมถึง array ว่าง
db.users.aggregate([{ $unwind: { path: "$tags", preserveNullAndEmptyArrays: true } }])

// $lookup — JOIN กับอีก collection
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

// $lookup — พร้อม pipeline (ยืดหยุ่นกว่า)
db.orders.aggregate([
  {
    $lookup: {
      from: "customers",
      let: { custId: "$customerId" },
      pipeline: [
        { $match: { $expr: { $eq: ["$_id", "$$custId"] } } },
        { $project: { name: 1, email: 1 } }
      ],
      as: "customer"
    }
  }
])

// $graphLookup — การค้นหาแบบกราฟ (recursive)
db.employees.aggregate([
  {
    $graphLookup: {
      from: "employees",
      startWith: "$_id",
      connectFromField: "reportsTo",
      connectToField: "_id",
      as: "subordinates",
      maxDepth: 5
    }
  }
])

// $bucket — จัดกลุ่มตามช่วง
db.users.aggregate([
  {
    $bucket: {
      groupBy: "$age",
      boundaries: [0, 18, 30, 50, 100],
      default: "other",
      output: { count: { $sum: 1 }, names: { $push: "$name" } }
    }
  }
])

// $bucketAuto — จัดกลุ่มอัตโนมัติ
db.users.aggregate([
  { $bucketAuto: { groupBy: "$age", buckets: 5, output: { count: { $sum: 1 }, avgAge: { $avg: "$age" } } } }
])

// $sortByCount — นับและเรียงตามจำนวน
db.users.aggregate([{ $sortByCount: "$city" }])

// $facet — หลาย pipeline พร้อมกันในชุดเดียว
db.articles.aggregate([
  {
    $facet: {
      byCity: [{ $group: { _id: "$city", count: { $sum: 1 } } }],
      byCategory: [{ $group: { _id: "$category", count: { $sum: 1 } } }],
      totalCount: [{ $count: "count" }]
    }
  }
])

// $replaceRoot — เปลี่ยน root เป็น sub-document
db.users.aggregate([
  { $replaceRoot: { newRoot: "$address" } }
])

// $replaceWith — เปลี่ยนเอกสารทั้งหมด ( shortcut )
db.users.aggregate([
  { $replaceWith: "$address" }
])

// $unionWith — รวมผลลัพธ์จากหลาย collections
db.users.aggregate([
  { $match: { city: "Bangkok" } },
  { $unionWith: { coll: "archive_users", pipeline: [{ $match: { city: "Bangkok" } }] } }
])

// $merge — เขียนผลลัพธ์ไปยัง collection (merge)
db.users.aggregate([
  { $match: { status: "active" } },
  { $merge: { into: "active_users", on: "_id", whenMatched: "merge", whenNotMatched: "insert" } }
])

// $out — เขียนผลลัพธ์ไปยัง collection ใหม่ (แทนที่ทั้ง collection)
db.users.aggregate([
  { $match: { status: "inactive" } },
  { $out: "inactive_users" }
])

// $setWindowFields — window functions (MongoDB 5.0+)
db.sales.aggregate([
  { $setWindowFields: {
    partitionBy: "$region",
    sortBy: { date: 1 },
    output: {
      runningTotal: { $sum: "$amount", window: { documents: ["unbounded", "current"] } },
      rank: { $rank: {} }
    }
  }}
])
```

### Aggregation Expressions

#### Array Expressions
```javascript
// $filter — กรอง array
db.users.aggregate([
  { $project: { highScores: { $filter: { input: "$scores", as: "s", cond: { $gte: ["$$s", 80] } } } } }
])

// $map — แปลงค่าใน array
db.users.aggregate([
  { $project: { upperNames: { $map: { input: "$tags", as: "t", in: { $toUpper: "$$t" } } } } }
])

// $reduce — ลด array ให้เหลือค่าเดียว
db.users.aggregate([
  { $project: { totalScores: { $reduce: { input: "$scores", initialValue: 0, in: { $add: ["$$value", "$$this"] } } } } }
])

// $slice — ตัด array
db.users.aggregate([{ $project: { top3: { $slice: ["$scores", 3] } } }])
db.users.aggregate([{ $project: { skip2: { $slice: ["$scores", 2, 3] } } }])

// $arrayElemAt — ดึงค่าตาม index
db.users.aggregate([{ $project: { first: { $arrayElemAt: ["$tags", 0] } } }])

// $first / $last — ค่าแรก/สุดท้ายใน array
db.users.aggregate([{ $project: { firstTag: { $first: "$tags" }, lastTag: { $last: "$tags" } } }])

// $size — ขนาดของ array
db.users.aggregate([{ $project: { tagCount: { $size: "$tags" } } }])

// $in — ตรวจสอบว่ามีค่าอยู่ใน array
db.users.aggregate([{ $project: { hasMongoDB: { $in: ["mongodb", "$tags"] } } }])

// $indexOfArray — หา index ของค่าใน array
db.users.aggregate([{ $project: { index: { $indexOfArray: ["$tags", "mongodb"] } } }])

// $concatArrays — รวม arrays
db.users.aggregate([
  { $project: { allTags: { $concatArrays: ["$tags", "$newTags"] } } }
])

// $setUnion / $setIntersection / $setDifference — การดำเนินการเซต
db.users.aggregate([
  { $project: {
    union: { $setUnion: ["$tags", "$newTags"] },
    common: { $setIntersection: ["$tags", "$newTags"] },
    diff: { $setDifference: ["$tags", "$newTags"] }
  } }
])
```

#### String Expressions
```javascript
// $concat — ต่อข้อความ
db.users.aggregate([
  { $project: { fullName: { $concat: ["$firstName", " ", "$lastName"] } } }
])

// $toUpper / $toLower — เปลี่ยนตัวพิมพ์
db.users.aggregate([
  { $project: { upper: { $toUpper: "$name" }, lower: { $toLower: "$email" } } }
])

// $substr / $substrCP — ตัดข้อความ
db.users.aggregate([
  { $project: { firstChar: { $substr: ["$name", 0, 1] } } }
])

// $strLenCP — ความยาวข้อความ
db.users.aggregate([{ $project: { len: { $strLenCP: "$name" } } }])

// $trim / $ltrim / $rtrim — ตัดช่องว่าง
db.users.aggregate([{ $project: { clean: { $trim: { input: "$name" } } } }])

// $split — แยกข้อความเป็น array
db.users.aggregate([{ $project: { words: { $split: ["$sentence", " "] } } }])

// $replaceOne / $replaceAll — แทนที่ข้อความ
db.users.aggregate([
  { $project: { cleanEmail: { $replaceAll: { input: "$email", find: "@old.com", replacement: "@new.com" } } } }
])

// $regexMatch — ตรวจสอบ regex
db.users.aggregate([
  { $match: { $expr: { $regexMatch: { input: "$name", regex: "^Som", options: "i" } } } }
])
```

#### Date Expressions
```javascript
// $dateToString — แปลงวันที่เป็นข้อความ
db.users.aggregate([
  { $project: { dateStr: { $dateToString: { format: "%Y-%m-%d", date: "$createdAt" } } } }
])

// รูปแบบวันที่: %Y=ปี, %m=เดือน, %d=วัน, %H=ชม., %M=นาที, %S=วินาที

// ดึงส่วนประกอบของวันที่
db.users.aggregate([
  { $project: {
    year: { $year: "$createdAt" },
    month: { $month: "$createdAt" },
    day: { $dayOfMonth: "$createdAt" },
    hour: { $hour: "$createdAt" },
    minute: { $minute: "$createdAt" },
    second: { $second: "$createdAt" },
    dayOfWeek: { $dayOfWeek: "$createdAt" },  // 1=อาทิตย์, 7=เสาร์
    week: { $week: "$createdAt" }
  } }
])

// $dateFromParts — สร้างวันที่จากส่วนประกอบ
db.users.aggregate([
  { $project: { date: { $dateFromParts: { year: 2024, month: 1, day: 15 } } } }
])

// $dateFromString — แปลงข้อความเป็นวันที่
db.users.aggregate([
  { $project: { date: { $dateFromString: { dateString: "$dateStr", format: "%Y-%m-%d" } } } }
])

// $dateDiff — ความแตกต่างระหว่างวันที่ (MongoDB 5.0+)
db.users.aggregate([
  { $project: { daysDiff: { $dateDiff: { startDate: "$createdAt", endDate: "$$NOW", unit: "day" } } } }
])

// $dateTrunc — ตัดวันที่ตาม unit (MongoDB 5.0+)
db.users.aggregate([
  { $project: { month: { $dateTrunc: { date: "$createdAt", unit: "month" } } } }
])
```

#### Conditional Expressions
```javascript
// $cond — ถ้า-แล้ว-อื่น (if-else)
db.users.aggregate([
  { $project: { status: { $cond: { if: { $gte: ["$age", 18] }, then: "adult", else: "minor" } } } }
])

// $cond — รูปแบบ shortened
db.users.aggregate([
  { $project: { status: { $cond: [{ $gte: ["$age", 18] }, "adult", "minor"] } } }
])

// $switch — หลายเงื่อนไข (switch-case)
db.users.aggregate([
  { $project: {
    ageGroup: {
      $switch: {
        branches: [
          { case: { $lt: ["$age", 13] }, then: "child" },
          { case: { $lt: ["$age", 20] }, then: "teen" },
          { case: { $lt: ["$age", 60] }, then: "adult" }
        ],
        default: "senior"
      }
    }
  } }
])

// $ifNull — ใช้ค่า default ถ้า null
db.users.aggregate([
  { $project: { displayName: { $ifNull: ["$nickname", "$name"] } } }
])

// $convert — แปลง type
db.users.aggregate([
  { $project: { ageAsInt: { $convert: { input: "$age", to: "int", onError: 0, onNull: null } } } }
])

// Shortcut conversions
db.users.aggregate([
  { $project: {
    ageInt: { $toInt: "$age" },
    nameStr: { $toString: "$name" },
    date: { $toDate: "$dateStr" },
    bool: { $toBool: "$active" }
  } }
])
```

#### Arithmetic Expressions
```javascript
// $add / $subtract / $multiply / $divide
db.orders.aggregate([
  { $project: {
    total: { $add: ["$price", "$tax"] },
    discounted: { $multiply: ["$price", { $subtract: [1, "$discountRate"] }] },
    avgPerItem: { $divide: ["$total", "$qty"] }
  } }
])

// $mod — หารเอาเศษ
db.users.aggregate([{ $project: { lastDigit: { $mod: ["$age", 10] } } }])

// $abs / $ceil / $floor / $round / $trunc
db.orders.aggregate([
  { $project: {
    abs: { $abs: "$difference" },
    ceil: { $ceil: "$price" },
    floor: { $floor: "$price" },
    rounded: { $round: ["$price", 2] }
  } }
])

// $pow / $sqrt / $log / $ln
db.orders.aggregate([
  { $project: { area: { $multiply: [{ $pow: ["$radius", 2] }, 3.14159] } } }
])
```

## Bulk Operations
```javascript
// bulkWrite — ดำเนินการหลายคำสั่งในครั้งเดียว (ประสิทธิภาพสูง)
db.users.bulkWrite([
  { insertOne: { document: { name: "NewUser", age: 25, city: "Bangkok" } } },
  { updateOne: { filter: { name: "Somchai" }, update: { $set: { age: 30 } } } },
  { deleteOne: { filter: { name: "OldUser" } } },
  { replaceOne: { filter: { name: "Somsak" }, replacement: { name: "Somsak", age: 35 } } }
])

// bulkWrite — พร้อม option (ordered = false อนุญาตให้ดำเนินการต่อแม้มี error)
db.users.bulkWrite(
  [
    { insertOne: { document: { _id: 1, name: "A" } } },
    { insertOne: { document: { _id: 1, name: "B" } } }  // duplicate key error
  ],
  { ordered: false }  // ถ้า true จะหยุดเมื่อเจอ error
)

// initializeOrderedBulkOp — สร้าง bulk operation (เวอร์ชันขับเคลื่อน)
let bulk = db.users.initializeOrderedBulkOp()
bulk.find({ status: "inactive" }).update({ $set: { status: "archived" } })
bulk.find({ age: { $lt: 18 } }).delete()
bulk.execute()

// initializeUnorderedBulkOp — ไม่เรียงลำดับ (เร็วแต่ไม่รับประกันลำดับ)
let bulk = db.users.initializeUnorderedBulkOp()
bulk.insert({ name: "User1", age: 20 })
bulk.insert({ name: "User2", age: 25 })
bulk.execute()
```

## Transactions
```javascript
// สร้าง session
let session = db.getMongo().startSession()
session.startTransaction()

try {
  let accounts = session.getDatabase("bank").accounts
  let logs = session.getDatabase("bank").logs

  accounts.updateOne({ _id: "A" }, { $inc: { balance: -100 } }, { session })
  accounts.updateOne({ _id: "B" }, { $inc: { balance: 100 } }, { session })
  logs.insertOne({ action: "transfer", from: "A", to: "B", amount: 100, time: new Date() }, { session })

  session.commitTransaction()
  print("Transaction committed successfully")
} catch (e) {
  session.abortTransaction()
  print("Transaction aborted: " + e)
} finally {
  session.endSession()
}

// Transaction พร้อม options
session.startTransaction({
  readConcern: { level: "snapshot" },        // ระดับการอ่าน
  writeConcern: { w: "majority" },           // ระดับการเขียน
  readPreference: "primary"                  // การอ่านจาก primary
})
```

## Schema Validation
```javascript
// สร้าง collection พร้อม validation
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email", "age"],
      properties: {
        name: {
          bsonType: "string",
          description: "ต้องเป็น string และไม่ว่าง"
        },
        email: {
          bsonType: "string",
          pattern: "^.+@.+$",
          description: "รูปแบบ email ไม่ถูกต้อง"
        },
        age: {
          bsonType: "int",
          minimum: 0,
          maximum: 150,
          description: "อายุต้องอยู่ระหว่าง 0-150"
        },
        phone: {
          bsonType: "string",
          pattern: "^[0-9]{10}$"
        },
        address: {
          bsonType: "object",
          properties: {
            city: { bsonType: "string" },
            zip: { bsonType: "string" }
          }
        },
        tags: {
          bsonType: "array",
          items: { bsonType: "string" },
          uniqueItems: true
        }
      }
    }
  },
  validationLevel: "strict",        // strict=ตรวจสอบทุก insert/update, moderate=ตรวจสอบเฉพาะ existing field
  validationAction: "error"          // error=ปฏิเสธ, warn=แค่เตือน
})

// เพิ่ม validation ให้ collection ที่มีอยู่
db.runCommand({
  collMod: "users",
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email"],
      properties: {
        name: { bsonType: "string" },
        email: { bsonType: "string" }
      }
    }
  },
  validationLevel: "moderate"
})
```

## Views
```javascript
// สร้าง view (อ่านอย่างเดียว เหมือน virtual collection)
db.createView("activeUsers", "users", [
  { $match: { status: "active" } },
  { $project: { name: 1, email: 1, age: 1 } }
])

// ดูข้อมูลจาก view
db.activeUsers.find()

// แสดงรายชื่อ views
db.getCollectionInfos({ type: "view" })

// ลบ view
db.activeUsers.drop()
```

## Change Streams
```javascript
// ฟังการเปลี่ยนแปลงใน collection (realtime)
let changeStream = db.users.watch()

// ฟัง event การเปลี่ยนแปลง
changeStream.on("change", change => {
  printjson(change)
  // { operationType: "insert", fullDocument: {...}, ns: { db: "test", coll: "users" }, ... }
})

// watch พร้อม pipeline กรอง
db.users.watch([
  { $match: { "fullDocument.age": { $gte: 18 } } }
])

// watch เฉพาะบาง operation type
db.users.watch([
  { $match: { operationType: { $in: ["insert", "update", "replace"] } } }
])

// watch ในระดับ database
db.watch()

// watch ในระดับ cluster (ต้องเปิด replica set)
// db.getMongo().watch()

// resume after (สำหรับ resume การฟังต่อจาก token เดิม)
let resumeToken = changeStream.resumeToken
db.users.watch([], { resumeAfter: resumeToken })
```

## Capped Collections
```javascript
// สร้าง capped collection (ขนาดจำกัด, เรียงตาม insertion order)
db.createCollection("logs", {
  capped: true,
  size: 100000,         // ขนาดสูงสุดเป็น bytes
  max: 1000             // จำนวนเอกสารสูงสุด (optional)
})

// ตรวจสอบว่าเป็น capped collection หรือไม่
db.logs.isCapped()

// แปลง collection ปกติเป็น capped
db.runCommand({ convertToCapped: "logs", size: 100000 })

// Tailable cursor (อ่านข้อมูลแบบ realtime จาก capped collection)
let cursor = db.logs.find().addOption(DBQuery.Option.tailable)
while (cursor.hasNext()) {
  printjson(cursor.next())
}
```

## Advanced Indexes
```javascript
// TTL Index — ลบเอกสารอัตโนมัติตามเวลา
db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })  // ลบหลังจาก 1 ชม.

// Partial Index — index เฉพาะเอกสารที่ตรงเงื่อนไข
db.users.createIndex(
  { age: 1 },
  { partialFilterExpression: { age: { $gte: 18 } } }
)

// Sparse Index — index เฉพาะเอกสารที่มี field นั้น
db.users.createIndex(
  { email: 1 },
  { sparse: true }
)

// Hashed Index — สำหรับ sharding (กระจายข้อมูล)
db.users.createIndex({ userId: "hashed" })

// Hidden Index — ซ่อน index (ทดสอบก่อนลบจริง)
db.users.createIndex({ name: 1 }, { hidden: true })

// Collation — กำหนดการเรียงลำดับตามภาษา
db.users.createIndex(
  { name: 1 },
  { collation: { locale: "th", strength: 1 } }  // locale "th" สำหรับภาษาไทย
)

// ค้นหาโดยใช้ collation
db.users.find({ name: "สมชาย" }).collation({ locale: "th", strength: 1 })

// Wildcard Index — index ทุก field ใน sub-document
db.users.createIndex({ "address.$**": 1 })

// Compound Index พร้อม collation
db.users.createIndex(
  { name: 1, age: -1 },
  { unique: true, collation: { locale: "en", strength: 2 } }
)

// สร้าง index แบบ background (ไม่บล็อกการทำงาน)
db.users.createIndex({ name: 1 }, { background: true })

// ตรวจสอบ query index usage
db.users.find({ name: "Somchai" }).explain("executionStats")

// ลบ indexes ทั้งหมด (ยกเว้น _id)
db.users.dropIndexes()
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

// ดูข้อมูล collection
db.users.stats()
db.users.dataSize()
db.users.totalSize()
db.users.storageSize()

// ตรวจสอบ index ขนาด
db.users.totalIndexSize()

// รับข้อมูล validation
db.users.getCollection().options()

// แสดง collection flags
db.users.getCollection().getStorageSize()

// คำสั่ง runCommand (เรียกใช้ command ใด ๆ)
db.runCommand({ ping: 1 })
db.runCommand({ buildInfo: 1 })
db.runCommand({ serverStatus: 1 })
db.runCommand({ dbStats: 1 })
db.runCommand({ collStats: "users" })

// ดูเวอร์ชัน MongoDB
db.version()

// ดู host ที่เชื่อมต่ออยู่
db.getMongo()

// ดู database name ปัจจุบัน
db.getName()

// ดู collections ทั้งหมด (รายละเอียด)
db.getCollectionNames()
db.getCollectionInfos()

// ดู database ขนาด
db.stats().dataSize
db.stats().storageSize

// รับ index suggestion สำหรับ query
db.users.find({ name: "Somchai" }).explain("queryPlanner")

// ตรวจสอบ shard status (ถ้าใช้ sharding)
// sh.status()

// รับรายการ databases
db.adminCommand({ listDatabases: 1 })

// รับ current operations แบบละเอียด
db.currentOp({ true: true })

// ตั้งค่าและรับ profiling level
db.getProfilingStatus()
db.setProfilingLevel(0)  // ปิด profiling
db.setProfilingLevel(2)  // เก็บทุก query

// ล้าง collection (ลบทั้งหมด แต่ retain indexes)
db.users.deleteMany({})

// clone collection
// db.runCommand({ cloneCollection: "source_db.users", from: "hostname:27017" })

// rename collection
db.users.renameCollection("users_archive")
db.users.renameCollection("users_archive", true)  // dropTarget ถ้ามีชื่อซ้ำ
```
