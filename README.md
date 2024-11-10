# MongoDB

- [ ] Why mongodb?
- [ ] What is collection?
- [ ] creation of database, collections, documents
- [ ] use of operators in find() query
- [ ] What is document?
- [ ] Mongodb vs MySQL
- [ ] find - query & projection

## Why mongodb?

- MongoDB is a NoSQL database, which means it is a non-relational database.
- It is a document-oriented database, which means it stores data in JSON-like documents.
- It is a schema-less database, which means it does not require a predefined schema.
- It is a distributed database, which means it can be scaled horizontally by adding more servers.
- It is a high-performance database, which means it can handle large amounts of data and high traffic.

## What is collection?

- A collection in MongoDB is a group of documents that are stored together in the database.
- Each document in a collection is a JSON-like object that contains key-value pairs.
- Collections are similar to tables in a relational database, but they do not have a fixed schema.
- Collections can be created, updated, and deleted using MongoDB commands.

## creation of database, collections, documents

```javascript
// Create a new database
use zenclass;
```

```javascript
// Create a new collection
db.createCollection("users");
```

```javascript
// check the current database
db;
```

```javascript
// show all the databases
show dbs;
```

or

```javascript
// show all the databases
show databases;
```

```javascript
// drop a collection
db.users.drop();
```

```javascript
// drop a database
db.dropDatabase();
```

```javascript
// Insert a document into a collection
// insert a single document
db.users.insertOne({ name: "Clara John", age: 30 });

// insert multiple documents
db.users.insertMany([
  { name: "Alice Smith", age: 25 },
  { name: "Clara John", age: 35 },
]);

// delete a single document
db.users.deleteOne({ name: "Clara John" });

// this will delete the first document that matches the query\

// delete multiple documents
db.users.deleteMany({ name: "Clara John" });

// update a single document
db.users.updateOne({ name: "Clara John" }, { $set: { age: 40 } });

// update multiple documents
db.users.updateMany({ name: "Clara John" }, { $set: { age: 40 } });
```
```javascript
// Find all topics and tasks taught in the month of October

db.Topics.find({ date: { $gte: new Date("2020-10-01"), $lt: new Date("2020-11-01") } });
db.Tasks.find({ date: { $gte: new Date("2020-10-01"), $lt: new Date("2020-11-01") } });

// Find all company drives between 15 Oct 2020 and 31 Oct 2020

db.Company_Drives.find({ date: { $gte: new Date("2020-10-15"), $lte: new Date("2020-10-31") } });

// Find all company drives and students who appeared for placement

db.Company_Drives.aggregate([
    { $lookup: {
        from: "Users",
        localField: "students",
        foreignField: "_id",
        as: "students_info"
    }}
]);

// Find the number of problems solved by each user in Codekata

db.Codekata.aggregate([
    { $group: { _id: "$user_id", total_problems_solved: { $sum: "$problems_solved" } } }
]);

// Find all mentors with more than 15 mentees

db.Mentors.find({ "students_assigned.15": { $exists: true } });

// Find the number of users who were absent and did not submit tasks between 15 Oct 2020 and 31 Oct 2020

db.Attendance.aggregate([
    { $match: { date: { $gte: new Date("2020-10-15"), $lte: new Date("2020-10-31") }, status: "absent" }},
    { $lookup: {
        from: "Tasks",
        let: { userId: "$user_id" },
        pipeline: [
            { $match: { date: { $gte: new Date("2020-10-15"), $lte: new Date("2020-10-31") } }},
            { $match: { $expr: { $not: { $in: ["$$userId", "$submitted_by"] } } } }
        ],
        as: "unsubmitted_tasks"
    }},
    { $match: { unsubmitted_tasks: { $ne: [] } }},
    { $count: "absent_and_unsubmitted" }
]);
```

## Schema

```javascript

// Users Collection

db.users.insertMany([
    { _id: 1, name: "Alice", email: "alice@example.com", phone: "1234567890", mentor_id: 1 },
    { _id: 2, name: "Bob", email: "bob@example.com", phone: "1234567891", mentor_id: 2 },
    { _id: 3, name: "Charlie", email: "charlie@example.com", phone: "1234567892", mentor_id: 1 },
    { _id: 4, name: "David", email: "david@example.com", phone: "1234567893", mentor_id: 3 },
    { _id: 5, name: "Eve", email: "eve@example.com", phone: "1234567894", mentor_id: 2 },
    { _id: 6, name: "Frank", email: "frank@example.com", phone: "1234567895", mentor_id: 3 },
    { _id: 7, name: "Grace", email: "grace@example.com", phone: "1234567896", mentor_id: 4 },
    { _id: 8, name: "Hank", email: "hank@example.com", phone: "1234567897", mentor_id: 4 },
    { _id: 9, name: "Ivy", email: "ivy@example.com", phone: "1234567898", mentor_id: 2 },
    { _id: 10, name: "John", email: "john@example.com", phone: "1234567899", mentor_id: 1 }
]);

// Mentors Collection

db.Mentors.insertMany([
    { _id: 1, name: "Mentor A", students_assigned: [1, 3, 5, 7, 9, 11, 13, 15, 17, 19, 21, 23, 25, 27, 29, 31, 33] },
    { _id: 2, name: "Mentor B", students_assigned: [2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32] },
    { _id: 3, name: "Mentor C", students_assigned: [34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49] },
    { _id: 4, name: "Mentor D", students_assigned: [50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65] },
    { _id: 5, name: "Mentor E", students_assigned: [66, 67, 68, 69, 70, 71, 72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82] },
    { _id: 6, name: "Mentor F", students_assigned: [83, 84, 85, 86, 87, 88, 89, 90, 91, 92, 93, 94, 95, 96, 97, 98, 99, 100] }
]);
// Attendance Collection

db.Attendance.insertMany([
    { _id: 1, user_id: 1, date: new Date("2020-10-15"), status: "Present" },
    { _id: 2, user_id: 2, date: new Date("2020-10-15"), status: "Absent" },
    { _id: 3, user_id: 3, date: new Date("2020-10-16"), status: "Present" },
    { _id: 4, user_id: 4, date: new Date("2020-10-16"), status: "Absent" },
    { _id: 5, user_id: 5, date: new Date("2020-10-17"), status: "Present" },
    { _id: 6, user_id: 6, date: new Date("2020-10-18"), status: "Absent" },
    { _id: 7, user_id: 7, date: new Date("2020-10-19"), status: "Present" },
    { _id: 8, user_id: 8, date: new Date("2020-10-20"), status: "Absent" },
    { _id: 9, user_id: 9, date: new Date("2020-10-21"), status: "Present" },
    { _id: 10, user_id: 10, date: new Date("2020-10-22"), status: "Absent" }
]);


// Company_drives Collection

db.Company_Drives.insertMany([
    { _id: 1, company_name: "Google", date: new Date("2020-10-15") },
    { _id: 2, company_name: "Microsoft", date: new Date("2020-10-17") },
    { _id: 3, company_name: "Facebook", date: new Date("2020-10-20") },
    { _id: 4, company_name: "Amazon", date: new Date("2020-10-22") },
    { _id: 5, company_name: "Apple", date: new Date("2020-10-25") },
    { _id: 6, company_name: "Netflix", date: new Date("2020-10-28") },
    { _id: 7, company_name: "Tesla", date: new Date("2020-10-30") },
    { _id: 8, company_name: "IBM", date: new Date("2020-11-01") },
    { _id: 9, company_name: "Oracle", date: new Date("2020-11-03") },
    { _id: 10, company_name: "SAP", date: new Date("2020-11-05") }
]);

// Codekata Collection

db.codekata.insertMany([
    { _id: 1, user_id: 1, problems_solved: 50 },
    { _id: 2, user_id: 2, problems_solved: 30 },
    { _id: 3, user_id: 3, problems_solved: 75 },
    { _id: 4, user_id: 4, problems_solved: 20 },
    { _id: 5, user_id: 5, problems_solved: 90 },
    { _id: 6, user_id: 6, problems_solved: 45 },
    { _id: 7, user_id: 7, problems_solved: 60 },
    { _id: 8, user_id: 8, problems_solved: 35 },
    { _id: 9, user_id: 9, problems_solved: 40 },
    { _id: 10, user_id: 10, problems_solved: 25 }
]);

// Tasks Collection

db.tasks.insertMany([
    { _id: 1, topic_id: 1, user_id: 1, status: "Submitted", submission_date: new Date("2020-10-02") },
    { _id: 2, topic_id: 2, user_id: 2, status: "Not Submitted", submission_date: new Date("2020-10-04") },
    { _id: 3, topic_id: 3, user_id: 3, status: "Submitted", submission_date: new Date("2020-10-06") },
    { _id: 4, topic_id: 4, user_id: 4, status: "Not Submitted", submission_date: new Date("2020-10-08") },
    { _id: 5, topic_id: 5, user_id: 5, status: "Submitted", submission_date: new Date("2020-10-11") },
    { _id: 6, topic_id: 6, user_id: 6, status: "Not Submitted", submission_date: new Date("2020-10-13") },
    { _id: 7, topic_id: 7, user_id: 7, status: "Submitted", submission_date: new Date("2020-10-15") },
    { _id: 8, topic_id: 8, user_id: 8, status: "Not Submitted", submission_date: new Date("2020-10-17") },
    { _id: 9, topic_id: 9, user_id: 9, status: "Submitted", submission_date: new Date("2020-10-19") },
    { _id: 10, topic_id: 10, user_id: 10, status: "Not Submitted", submission_date: new Date("2020-10-21") }
]);

// Topics Collection

db.topics.insertMany([
    { _id: 1, name: "HTML Basics", date: new Date("2020-10-01") },
    { _id: 2, name: "CSS Basics", date: new Date("2020-10-03") },
    { _id: 3, name: "JavaScript Intro", date: new Date("2020-10-05") },
    { _id: 4, name: "Advanced JavaScript", date: new Date("2020-10-07") },
    { _id: 5, name: "React Basics", date: new Date("2020-10-10") },
    { _id: 6, name: "Node.js Intro", date: new Date("2020-10-12") },
    { _id: 7, name: "Database Basics", date: new Date("2020-10-14") },
    { _id: 8, name: "Express.js", date: new Date("2020-10-16") },
    { _id: 9, name: "API Development", date: new Date("2020-10-18") },
    { _id: 10, name: "Project Setup", date: new Date("2020-10-20") }
]);

