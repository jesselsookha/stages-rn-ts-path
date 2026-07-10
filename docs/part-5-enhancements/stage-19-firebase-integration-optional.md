# Stage 19 — Firebase Integration (Optional)

## ❖ Introduction

Everything before this point has kept data either in memory, or on the
device itself, through AsyncStorage. This Stage looks briefly beyond that —
at a database that lives somewhere else entirely, reachable over the
internet. This is genuinely **not part of the official Manual**, and isn't
expected to be mastered here. Treat it as a window, not a syllabus item: a
look at what becomes possible once an app's data doesn't live on the phone
at all, with links pointing toward where the real depth lives, should you
want to explore further on your own.

## ✦ Databases: SQL vs NoSQL

- **SQL (Relational)** — data organised into tables, rows, and columns, tied
  together through defined relationships. Best suited to data with a strict,
  predictable structure — a banking system, for instance.
- **NoSQL (Non-Relational)** — data organised into collections and
  documents, closer in shape to JSON objects. No fixed schema is required
  up front, which suits apps whose data shape may evolve over time — chat
  apps, social feeds, and similar.

## ✧ Why Firebase?

**Firebase** is a cloud platform from Google, and its NoSQL database —
**Cloud Firestore** — integrates comfortably with React Native and Expo.

**Advantages:** real-time updates across devices, scales from a small
class project to a large production app, official SDKs for web, iOS, and
Android, built-in authentication, and a free tier suited to learning.

**Disadvantages:** tied specifically to Google's ecosystem, costs can grow
with heavy use, and NoSQL's flexibility comes at the cost of the more
powerful relational queries a SQL database offers natively.

## ❖ Collections and Documents

Firestore stores data as **collections** (comparable to a folder) holding
**documents** (comparable to a single JSON object inside that folder):

```json
{
  "name": "Alice",
  "age": 20,
  "course": "Mobile Development"
}
```

## ✱ Beyond One Table: A Simple One-to-Many Relationship

Every earlier example — students, users, scores — has stayed inside a
single collection. Real data is rarely that isolated. Here's the simplest
possible step beyond it: **one course, many students** — a course
document, and several student documents that each know which course they
belong to.

```typescript
import { getFirestore, collection, addDoc, query, where, getDocs } from 'firebase/firestore';
import { initializeApp } from 'firebase/app';

const firebaseConfig = {
  apiKey: 'YOUR_API_KEY',
  authDomain: 'YOUR_PROJECT.firebaseapp.com',
  projectId: 'YOUR_PROJECT_ID',
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
```

### The "One" Side: Adding a Course

```typescript
async function addCourse(title: string): Promise<string> {
  const docRef = await addDoc(collection(db, 'courses'), { title });
  return docRef.id; // Firestore generates this ID automatically
}
```

### The "Many" Side: Adding a Student, Linked to a Course

```typescript
async function addStudent(name: string, age: number, courseId: string) {
  await addDoc(collection(db, 'students'), {
    name,
    age,
    courseId, // the link back to a document in "courses"
  });
}
```

`courseId` here plays exactly the same role as the primary-key thinking
from Stage 18 — except now it's referencing a record in a *different*
collection, rather than identifying a record within its own.

### Querying the "Many" Side of the Relationship

```typescript
async function getStudentsForCourse(courseId: string) {
  const studentsQuery = query(
    collection(db, 'students'),
    where('courseId', '==', courseId)
  );
  const snapshot = await getDocs(studentsQuery);
  snapshot.forEach((doc) => {
    console.log(doc.id, '=>', doc.data());
  });
}
```

This is the one-to-many relationship in action: many `students` documents,
each pointing back to one `courses` document, retrieved by filtering on
that shared `courseId`.

!!! note "Conceptual Note"
    Firestore also supports **subcollections** — nesting `students`
    directly inside a specific course document
    (`courses/{courseId}/students/{studentId}`), rather than linking them
    through a shared field as shown here. Both are valid, commonly used
    patterns; the reference-field approach above was chosen for this
    introduction because it mirrors the foreign-key thinking from Stage 18
    most directly. Firestore's own documentation on [structuring
    data](https://firebase.google.com/docs/firestore/manage-data/structure-data)
    is worth reading if you'd like to compare both approaches properly.

## ❖ Real-Time Updates

```typescript
import { onSnapshot } from 'firebase/firestore';

function listenToStudents(courseId: string) {
  const studentsQuery = query(collection(db, 'students'), where('courseId', '==', courseId));

  const unsubscribe = onSnapshot(studentsQuery, (snapshot) => {
    snapshot.forEach((doc) => {
      console.log('Updated:', doc.data());
    });
  });

  // Call unsubscribe() later, e.g. when the screen closes
}
```

Unlike `getDocs`, which fetches once, `onSnapshot` keeps listening — any
change to a matching student, from any device, arrives here automatically.

## ✧ Application Storylines

- **Lecturer app** — courses and their enrolled students, updating live as
  a class roster changes.
- **Chat app** — a conversation document, with many message documents
  referencing it.
- **Todo app** — a project document, with many task documents belonging to
  it.

## ✦ Debugging Firebase Integration

!!! bug "Common Bug Spotlight"
    **Symptom:** `getStudentsForCourse` runs without error, but returns
    nothing — even though students were definitely added.

    **Likely cause:** the `courseId` passed to `addStudent` doesn't
    actually match the course's real document ID — often because the ID
    returned by `addCourse` was never captured and reused, and a typed-out
    or guessed ID was used instead.

    **Fix:** always capture and reuse the actual ID Firestore generates
    (`addCourse`'s return value above) — never assume or hardcode a
    document ID for a relationship like this one.

    Beyond this specific case: **authentication errors** usually mean the
    Firebase project isn't configured correctly; **permission denied**
    points to Firestore's security rules; **data not syncing live** usually
    means `onSnapshot` was needed but `getDocs` was used instead.

## Reflection

This Stage was always meant to be a door left ajar, not a room you're
expected to furnish. Firestore, real-time syncing, and even this small
one-to-many relationship are here so the shape of cloud-based, connected
data isn't a complete surprise the first time you meet it properly —
whether that's later in this course, in a following subject, or in a
project entirely your own.

[Stage 20](./stage-20-deployment-and-testing.md) returns to firmer,
required ground — preparing the XO Game for testing and deployment.

---

[^1]: Firebase — Official Documentation, "Cloud Firestore." Available at: https://firebase.google.com/docs/firestore (Accessed: 10 July 2026).
[^2]: Firebase — Official Documentation, "Structure Data." Available at: https://firebase.google.com/docs/firestore/manage-data/structure-data (Accessed: 10 July 2026).