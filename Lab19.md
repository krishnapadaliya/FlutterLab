# A-2: Write a flutter code to display a list with different colors with different priority. 
## main.dart
### resetDatabase() is used when you want to delete the old database and create a new one (for example, if you added a new column like priority and want the table to be created fresh).
```dart
void main() {
  WidgetsFlutterBinding.ensureInitialized();
  // Reset database (only for testing/development)
  //await resetDatabase();
  runApp(const MyApp());
}
```

## myDatabase.dart

### 1. Update your DB schema
#### Your onCreate currently creates a table with priority:
```dart
onCreate: (db, version) async {
  return await db.execute(
      'CREATE TABLE todos('
      'id INTEGER PRIMARY KEY AUTOINCREMENT, '
      'title TEXT, '
      'description TEXT, '
      'priority INTEGER DEFAULT 1)');
},
```

### 2. Keep your onUpgrade for existing DBs
#### This ensures old users with DB v1 get the new column added:
```dart
onUpgrade: (db, oldVersion, newVersion) async {
  if (oldVersion < 2) {
    await db.execute('ALTER TABLE todos ADD COLUMN priority INTEGER DEFAULT 1');
  }
},
```
### 3. Bump your version
```dart
version: 2,
```
#### if you previously ran the app with version: 2 but the DB schema didn’t match, bump again to 3 so that onUpgrade runs:
```dart
version: 3,
```

### 4. Clear old database (during dev)
#### Because SQLite keeps the DB file until you delete/uninstall the app, do one of these:
#### -> Uninstall the app from emulator/device, then rebuild.
#### -> OR, add a helper to delete DB during development:
```dart
Future<void> resetDatabase() async {
  String path = join(await getDatabasesPath(), 'todo_database.db');
  await deleteDatabase(path);
}
```

```dart
import 'package:sqflite/sqflite.dart';
import 'package:path/path.dart';

class MyDB {
  Future<Database> intialDatabase() async {
    String path = await getDatabasesPath();
    return openDatabase(
      join(
        path,
        'todo_database.db',
      ),
      version: 3,
      onCreate: (db, version) async {
        return await db.execute(
            'CREATE TABLE todos(id INTEGER PRIMARY KEY AUTOINCREMENT, title TEXT, description TEXT,priority INTEGER DEFAULT 1)');
      },
      onUpgrade: (db, oldVersion, newVersion) async {
        if (oldVersion < 3) {
          await db.execute(
              'ALTER TABLE todos ADD COLUMN priority INTEGER DEFAULT 1');
        }
      },
    );
  }

  Future<List<Map<String, dynamic>>> getData() async {
    Database db = await intialDatabase();
    List<Map<String, dynamic>> map = await db.rawQuery('select * from todos');
    return map;
  }

  Future<void> insertStaticData() async {
    Database db = await intialDatabase();
    await db.insert(
      'todos',
      {
        'title': 'jgsdhjgdhjgd',
        'description': 'kjhdfhkjfhih',
        'priority': 2,
      },
    );
  }

  Future<int> insertTodo(
      {required String title, String? des, required int prio}) async {
    Database db = await intialDatabase();
    Map<String, dynamic> map = {};
    map['title'] = title;
    map['description'] = des;
    map['priority'] = prio;
    return db.insert('todos', map);
  }

  // Future<void> resetDatabase() async {
  //   String path = join(await getDatabasesPath(), 'todo_database.db');
  //   await deleteDatabase(path);
  // }
}

```

## AddEditTodo.dart
```dart


import 'package:flutter/material.dart';
import 'package:insta/Extra/database.dart';
import '../lab17/myDatabase.dart';

class AddTodo extends StatefulWidget {
  const AddTodo({Key? key}) : super(key: key);

  @override
  State<AddTodo> createState() => _AddTodoState();
}

class _AddTodoState extends State<AddTodo> {
  final titleController = TextEditingController();
  final descController = TextEditingController();
  int? selectedPriority;
  final MyDB db = MyDB();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Add Todo"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(12.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            TextField(
              decoration: const InputDecoration(hintText: "Enter Todo Title"),
              controller: titleController,
            ),
            const SizedBox(height: 10),
            TextField(
              decoration:
              const InputDecoration(hintText: "Enter Todo Description"),
              controller: descController,
            ),
            const SizedBox(height: 20),
            DropdownButtonFormField(
              items: [
                DropdownMenuItem(value: 1, child: Text("High (1)")),
                DropdownMenuItem(value: 2, child: Text("Medium (2)")),
                DropdownMenuItem(value: 3, child: Text("Low (3)")),
              ],
              onChanged: (value) {
                setState(() {
                  selectedPriority = value;
                });
              },
              value: selectedPriority,
            ),
            const SizedBox(height: 20),
            Center(
              child: ElevatedButton(
                onPressed: () async {
                  await db.insertTodo(
                    title: titleController.text.trim(),
                    des: descController.text.trim(),
                    prio: selectedPriority!,
                  );
                  Navigator.pop(context, true);
                },
                child: const Text("Add"),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

## TodoListPage.dart
```dart

import 'package:flutter/material.dart';
import 'package:insta/Extra/database.dart';

import 'AddEdit.dart';

class ListPage extends StatefulWidget {
  const ListPage({super.key});

  @override
  State<ListPage> createState() => _ListPageState();
}

class _ListPageState extends State<ListPage> {
  MyDB database = MyDB();

  Color getPriorityColor(dynamic priority) {
    switch (priority) {
      case 1:
        return Colors.red.shade300; // High
      case 2:
        return Colors.orange.shade300; // Medium
      case 3:
        return Colors.green.shade300; // Low
      default:
        return Colors.grey.shade300;
    }
  }

  @override
  void initState() {
    super.initState();
    //database.insertStaticData();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("List Page"),
        backgroundColor: Colors.grey,
      ),
      body: FutureBuilder<List<Map<String, dynamic>>>(
        future: database.getData(),
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return const Center(child: CircularProgressIndicator());
          }
          if (!snapshot.hasData || snapshot.data!.isEmpty) {
            return const Center(child: Text('No todos found.'));
          }
          final todos = snapshot.data!;
          return ListView.builder(
            itemCount: todos.length,
            itemBuilder: (context, index) {
              final todo = todos[index];
              final priority = todo['priority'];
              final cardColor = getPriorityColor(priority);
              return Card(
                color: cardColor,
                child: ListTile(
                  title: Text(todo['title'] ?? ''),
                  subtitle: Text(todo['description'] ?? ''),
                  trailing: Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      IconButton(
                        icon: const Icon(Icons.edit, color: Colors.blue),
                        onPressed: () {},
                      ),
                      IconButton(
                        icon: const Icon(Icons.delete, color: Colors.red),
                        onPressed: () {},
                      ),
                    ],
                  ),
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: const Icon(Icons.add),
        onPressed: () {
          Navigator.push(
            context,
            MaterialPageRoute(builder: (context) => const AddTodo()),
          ).then((value) {
            if (value == true) {
              setState(() {});
            }
          });
        },
      ),
    );
  }
}
```
