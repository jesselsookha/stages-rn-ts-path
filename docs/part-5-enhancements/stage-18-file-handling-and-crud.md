# Stage 18 — File Handling & CRUD

## ❖ Introduction

This Stage is a natural next step from Stage 9's arrays — a 1D array, a
parallel array, a 2D array, and now: a structured collection of records,
each one built from a TypeScript interface rather than loose, separate
values. This is also where the shape of *file handling*, in the older,
traditional sense, and modern app data start to look like the same idea
wearing different clothes.

## ✦ From Arrays to Records: A Bit of History

Long before JSON, or even databases, structured data was stored in **flat
files** — plain files where each line represented one **record**, and each
record was made up of **fields**: name, course, ID, and so on. Two access
patterns mattered:

- **Sequential access** — reading a file from the very start, one record
  after another, until you find what you're looking for (or reach the
  end).
- **Random access** — jumping directly to a specific record's location in
  the file, without reading everything before it — usually made possible
  by each record having a fixed size, and a known **ID**, acting much like
  what we'd now call a **primary key**: a value guaranteed to uniquely
  identify one, and only one, record.

An array of objects, typed with a TypeScript interface, is the direct
descendant of that idea:

```typescript
interface Student {
  id: number;   // the "primary key" — uniquely identifies this record
  name: string; // a field
  course: string; // a field
}
```

Each `Student` is a record. `id` plays exactly the role a primary key
always has — a value the rest of the program can rely on to mean "this
specific one, and no other." `students.find(s => s.id === 3)` is random
access, conceptually identical to jumping straight to record 3 in an old
flat file, just expressed as an array method instead of a file offset.

!!! note "Conceptual Note"
    You won't be opening raw files byte-by-byte in this course — but
    recognising this lineage matters. Databases, which you'll meet properly
    in a later subject, are really a far more capable, far more reliable
    evolution of exactly this idea: records, fields, and a guaranteed-unique
    ID to find any one of them quickly.

## ✧ JSON as a Modern Flat File

**JSON** (JavaScript Object Notation) is a lightweight, human-readable
format for storing structured data — and functions today much like a flat
file once did, holding a collection of records:

```json
[
  { "id": 1, "name": "Alice", "course": "Mobile Development" },
  { "id": 2, "name": "Bob", "course": "React Native" }
]
```

```tsx
import students from './data/students.json';
console.log(students[0].name); // Alice
```

## ❖ CRUD: The Four Operations

**CRUD** names the four things any program does to structured data:

- **Create** — add a new record.
- **Read** — retrieve one or more records.
- **Update** — modify an existing record, found by its ID.
- **Delete** — remove a record, found by its ID.

Every one of these depends on the ID being reliable — `updateStudent` and
`deleteStudent` both need to find *exactly one* record and no other, which
is only possible because `id` is guaranteed unique.

## ✱ A Standalone Mini-App: User Management

The XO Game has no natural need for CRUD — a board game doesn't manage
growing collections of records. So, in the same spirit as Stage 15's task
list, here's a separate, self-contained app: a simple user management
screen, with full Create, Read, Update, and Delete.

### The `User` Interface

```typescript
type User = {
  id: number;
  name: string;
  email: string;
  phone: string;
  role: string;
};
```

### The Complete App

```tsx
import { useState } from 'react';
import {
  TextInput,
  TouchableOpacity,
  FlatList,
  Text,
  View,
  StyleSheet,
  ScrollView,
} from 'react-native';
import { Picker } from '@react-native-picker/picker';

type User = {
  id: number;
  name: string;
  email: string;
  phone: string;
  role: string;
};

export default function App() {
  const [name, setName] = useState<string>('');
  const [email, setEmail] = useState<string>('');
  const [phone, setPhone] = useState<string>('');
  const [role, setRole] = useState<string>('');

  const [searchTerm, setSearchTerm] = useState<string>('');
  const [editingUserId, setEditingUserId] = useState<number | null>(null);
  const [users, setUsers] = useState<User[]>([]);

  const [errors, setErrors] = useState({
    name: '',
    email: '',
    role: '',
    phone: '',
  });

  // Derived, not stored — filtered directly from `users` and `searchTerm`
  // on every render, the same reasoning used for `winner` back in Stage 9.
  const displayedUsers =
    searchTerm.trim() === ''
      ? users
      : users.filter(
          (user) =>
            user.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
            user.email.toLowerCase().includes(searchTerm.toLowerCase())
        );

  const validateInputs = (): boolean => {
    let valid = true;
    const newErrors = { name: '', email: '', phone: '', role: '' };

    if (!name.trim()) {
      newErrors.name = 'Name is required';
      valid = false;
    }
    if (!email.trim()) {
      newErrors.email = 'Email is required';
      valid = false;
    } else if (!/\S+@\S+\.\S+/.test(email)) {
      newErrors.email = 'Email format is invalid';
      valid = false;
    }
    if (!phone.trim()) {
      newErrors.phone = 'Phone is required';
      valid = false;
    } else if (phone.trim().length < 10) {
      newErrors.phone = 'Phone is too short';
      valid = false;
    }
    if (!role) {
      newErrors.role = 'Role is required';
      valid = false;
    }

    setErrors(newErrors);
    return valid;
  };

  // CREATE and UPDATE share one button — this decides which is happening
  const handleSaveUser = () => {
    if (!validateInputs()) return;

    if (editingUserId !== null) {
      // UPDATE — replace only the record whose id matches
      setUsers(
        users.map((user) =>
          user.id === editingUserId ? { id: editingUserId, name, email, phone, role } : user
        )
      );
    } else {
      // CREATE — append a new record with a freshly generated id
      const newUser: User = { id: Date.now(), name, email, phone, role };
      setUsers([...users, newUser]);
    }

    handleClearForm();
  };

  const handleClearForm = () => {
    setName('');
    setEmail('');
    setPhone('');
    setRole('');
    setErrors({ name: '', email: '', phone: '', role: '' });
    setEditingUserId(null);
  };

  // DELETE — removes the record currently loaded for editing
  const handleDeleteUser = () => {
    if (editingUserId === null) return;
    setUsers(users.filter((user) => user.id !== editingUserId));
    handleClearForm();
  };

  // READ (for one record) — loads a user's fields into the form for editing
  const handleEditUser = (user: User) => {
    setName(user.name);
    setEmail(user.email);
    setPhone(user.phone);
    setRole(user.role);
    setEditingUserId(user.id);
  };

  return (
    <View style={styles.safeArea}>
      <ScrollView contentContainerStyle={styles.scrollContainer} keyboardShouldPersistTaps="handled">
        <View style={styles.container}>
          <Text style={styles.heading}>User Management (CRUD)</Text>

          <View style={styles.searchContainer}>
            <TextInput
              style={styles.searchInput}
              value={searchTerm}
              onChangeText={setSearchTerm}
              placeholder="Search by name or email..."
              placeholderTextColor="#999"
            />
          </View>

          <FlatList
            data={displayedUsers}
            keyExtractor={(item) => item.id.toString()}
            ListEmptyComponent={() => (
              <Text style={styles.noResultsText}>No users found.</Text>
            )}
            renderItem={({ item }: { item: User }) => (
              <View style={styles.userItem}>
                <View style={styles.userInfo}>
                  <Text style={styles.userName}>{item.name}</Text>
                  <Text style={styles.userRole}>- {item.role.toUpperCase()}</Text>
                </View>
                <View style={styles.userActions}>
                  <TouchableOpacity
                    style={[styles.actionButton, styles.editButton]}
                    onPress={() => handleEditUser(item)}
                  >
                    <Text style={styles.actionButtonText}>VIEW/EDIT</Text>
                  </TouchableOpacity>
                </View>
              </View>
            )}
          />

          <Text style={styles.userCountText}>
            Total Registered Users: {users.length}
          </Text>

          <View style={styles.formContainer}>
            <Text style={styles.formHeading}>
              {editingUserId ? 'Edit / Delete User' : 'Register New User'}
            </Text>

            <Text style={styles.label}>Name</Text>
            <TextInput
              style={styles.input}
              value={name}
              onChangeText={setName}
              placeholder="Enter name"
              placeholderTextColor="#999"
            />
            {errors.name ? <Text style={styles.error}>{errors.name}</Text> : null}

            <Text style={styles.label}>Email</Text>
            <TextInput
              style={styles.input}
              value={email}
              onChangeText={setEmail}
              placeholder="Enter email"
              placeholderTextColor="#999"
              keyboardType="email-address"
            />
            {errors.email ? <Text style={styles.error}>{errors.email}</Text> : null}

            <Text style={styles.label}>Phone</Text>
            <TextInput
              style={styles.input}
              value={phone}
              onChangeText={setPhone}
              placeholder="Enter phone"
              placeholderTextColor="#999"
              keyboardType="phone-pad"
            />
            {errors.phone ? <Text style={styles.error}>{errors.phone}</Text> : null}

            <Text style={styles.label}>Role</Text>
            <View style={styles.pickerWrapper}>
              <Picker
                selectedValue={role}
                onValueChange={(itemValue) => setRole(itemValue)}
                style={styles.picker}
                dropdownIconColor="#1acef6"
              >
                <Picker.Item label="Select Role" value="" />
                <Picker.Item label="DEVELOPER" value="developer" />
                <Picker.Item label="UX DESIGNER" value="ux designer" />
                <Picker.Item label="MANAGER" value="manager" />
              </Picker>
            </View>
            {errors.role ? <Text style={styles.error}>{errors.role}</Text> : null}

            <View style={styles.actionButtonsContainer}>
              <TouchableOpacity
                style={[styles.button, editingUserId ? styles.updateButton : styles.saveButton]}
                onPress={handleSaveUser}
              >
                <Text style={styles.buttonText}>{editingUserId ? 'UPDATE' : 'SAVE'}</Text>
              </TouchableOpacity>

              <TouchableOpacity style={[styles.button, styles.clearButton]} onPress={handleClearForm}>
                <Text style={styles.buttonText}>CLEAR</Text>
              </TouchableOpacity>

              <TouchableOpacity
                style={[styles.button, styles.deleteButton, !editingUserId && styles.disabledButton]}
                onPress={handleDeleteUser}
                disabled={!editingUserId}
              >
                <Text style={styles.buttonText}>DELETE</Text>
              </TouchableOpacity>
            </View>
          </View>
        </View>
      </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  safeArea: { flex: 1, backgroundColor: '#063146' },
  scrollContainer: { flexGrow: 1 },
  container: { flex: 1, padding: 20, backgroundColor: '#063146' },
  heading: { fontSize: 28, color: '#1acef6', marginBottom: 10, fontWeight: 'bold', alignSelf: 'center' },
  formHeading: {
    fontSize: 22,
    color: '#1acef6',
    marginBottom: 10,
    fontWeight: 'bold',
    borderBottomWidth: 1,
    borderBottomColor: '#115b79',
    paddingBottom: 5,
    marginTop: 10,
  },
  searchContainer: {
    flexDirection: 'row',
    marginBottom: 15,
    borderWidth: 1,
    borderColor: '#1acef6',
    borderRadius: 8,
    overflow: 'hidden',
  },
  searchInput: { flex: 1, fontSize: 16, padding: 10, color: '#FFFFFF', backgroundColor: '#115b79' },
  formContainer: { paddingTop: 10, borderTopWidth: 1, borderTopColor: '#1acef6' },
  label: { fontSize: 16, color: '#FFFFFF', marginTop: 8, marginBottom: 4 },
  input: {
    backgroundColor: '#115b79',
    color: '#FFFFFF',
    borderRadius: 6,
    padding: 10,
    fontSize: 16,
  },
  pickerWrapper: { backgroundColor: '#115b79', borderRadius: 6, overflow: 'hidden' },
  picker: { color: '#FFFFFF' },
  error: { color: '#ff6b6b', fontSize: 13, marginTop: 2 },
  actionButtonsContainer: { flexDirection: 'row', justifyContent: 'space-between', marginTop: 16 },
  button: { flex: 1, padding: 12, borderRadius: 6, marginHorizontal: 4, alignItems: 'center' },
  saveButton: { backgroundColor: '#1acef6' },
  updateButton: { backgroundColor: '#f1ff58' },
  clearButton: { backgroundColor: '#115b79' },
  deleteButton: { backgroundColor: '#c0392b' },
  disabledButton: { opacity: 0.4 },
  buttonText: { color: '#063146', fontWeight: 'bold' },
  userCountText: { color: '#FFFFFF', marginVertical: 8, fontStyle: 'italic' },
  noResultsText: { color: '#999', textAlign: 'center', marginVertical: 12 },
  userItem: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    backgroundColor: '#115b79',
    padding: 10,
    borderRadius: 6,
    marginBottom: 6,
  },
  userInfo: { flexDirection: 'row', alignItems: 'center' },
  userName: { color: '#FFFFFF', fontWeight: 'bold', marginRight: 6 },
  userRole: { color: '#1acef6' },
  userActions: { flexDirection: 'row' },
  actionButton: { paddingVertical: 6, paddingHorizontal: 10, borderRadius: 4 },
  editButton: { backgroundColor: '#1acef6' },
  actionButtonText: { color: '#063146', fontWeight: 'bold', fontSize: 12 },
});
```

## ✧ Code Breakdown

| Operation | Function | How the record is found |
|---|---|---|
| Create | `handleSaveUser` (when `editingUserId` is `null`) | N/A — a new `id` is generated with `Date.now()` |
| Read | `displayedUsers`, rendered via `FlatList` | Filtered directly from `users`, no ID needed |
| Update | `handleSaveUser` (when `editingUserId` is set) | `user.id === editingUserId` |
| Delete | `handleDeleteUser` | `user.id === editingUserId` |

!!! note "Conceptual Note"
    `displayedUsers` is computed directly in the render body, not stored in
    its own `useState`, exactly the same reasoning as `winner` back in
    Stage 9 — it's always derivable from `users` and `searchTerm`, so
    storing it separately would only risk the two falling out of sync.

## ❖ Debugging CRUD Logic

!!! bug "Common Bug Spotlight"
    **Symptom:** pressing **Update** appears to work, but a *new* row
    appears in the list instead of the existing one changing.

    **Cause:** `handleSaveUser` unconditionally appended a new record,
    without checking `editingUserId` first — effectively always doing
    Create, never Update.

    **Fix:** as shown above, `handleSaveUser` branches on whether
    `editingUserId` is `null`. This is exactly the kind of bug that's easy
    to miss by testing Create in isolation, then Update in isolation, but
    catch immediately by testing the full Create → Edit → Update sequence
    together.

## Exercise

Get the User Management app running: register a few users, search for one,
edit it, and delete it. Commit this checkpoint once all four CRUD
operations work correctly in sequence. Then: `id: Date.now()` generates a
unique ID by using the current timestamp — can you think of a situation
where two records might, in theory, end up with the same `id` this way?

## Reflection

Everything in this Stage — the interface, the ID, the four operations — has
a direct ancestor in ideas that predate JSON, predate JavaScript, predate
React Native itself. CRUD isn't a React Native concept; it's a description
of what any program does with structured data, in any language, on any
platform. Recognising that is worth as much as the code itself.

[Stage 19](./stage-19-firebase-integration-optional.md) picks this exact
CRUD idea back up — this time, backed by a real cloud database instead of
in-memory state.

---

[^1]: TypeScript — Official Handbook, "Object Types." Available at: https://www.typescriptlang.org/docs/handbook/2/objects.html (Accessed: 10 July 2026).