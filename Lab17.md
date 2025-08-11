# A-1: Create different tables for the to-do list app in SQLite and attach a .db file with the flutter project. 
## add dependencies in pubspec.yaml
```dart
dependencies:
  flutter:
    sdk: flutter
  path_provider: ^2.1.4
  sqflite: ^2.3.2
```

## myDatabase.dart
```dart
import 'package:sqflite/sqflite.dart';
import 'package:path/path.dart';

class MyDatabase {
  Future<Database> initDB() async {
    String path = await getDatabasesPath();
    return openDatabase(
      join(
        path,
        'todo_database.db',
      ),
      onCreate: (db, version) async {
        await db.execute(
          'CREATE TABLE todos(id INTEGER PRIMARY KEY AUTOINCREMENT, title TEXT, description TEXT)',
        );
      },
      version: 1,
    );
  }

  // static Future<List<Map<String, dynamic>>> getAllTodos() async {
  //   final db = await initDB();
  //   return db.query('todos');
  // }

  Future<List<Map<String, dynamic>>> getAllTodos() async {
    Database db = await initDB();
    List<Map<String, dynamic>> todos = await db.rawQuery('select * from todos');
    return todos;
  }

  Future<int> insertTodo({required String title, String? des}) async {
    Database db = await initDB();
    Map<String, dynamic> map = {};
    map['title'] = title;
    map['description'] = des;
    return db.insert('todos', map);
  }

Future<void> insertStaticTodos() async {
    final db = await initDB(); // already existing getter
    await db.insert(
      'todos', // table name
      {
        'title': 'Buy groceries again',
        'description': 'Milk, Bread, Eggs',
      },
    );
  }

  Future<int> updateTodo({required int id, String? title, String? des}) async {
    Database db = await initDB();
    Map<String, dynamic> map = {};
    map['title'] = title;
    map['description'] = des;
    return db.update('todos', map, where: 'id = ?', whereArgs: [id]);
  }

  Future<int> deleteTodo({required int id}) async {
    Database db = await initDB();
    return db.delete('todos', where: 'id = ?', whereArgs: [id]);
  }
}

```

## AddEditTodo.dart
```dart
import 'package:flutter/material.dart';
import 'myDatabase.dart';

class AddEditTodo extends StatefulWidget {
  final Map<String, dynamic>? todo;
  const AddEditTodo({this.todo, Key? key}) : super(key: key);

  @override
  State<AddEditTodo> createState() => _AddTodoScreenState();
}

class _AddTodoScreenState extends State<AddEditTodo> {
  final titleController = TextEditingController();
  final descController = TextEditingController();
  final MyDatabase db = MyDatabase();

  @override
  void initState() {
    super.initState();
    if (widget.todo != null) {
      titleController.text = widget.todo!["title"] ?? '';
      descController.text = widget.todo!["description"] ?? '';
    }
  }

  void _saveTodo() async {
    if (titleController.text.trim().isEmpty) return;

    if (widget.todo == null) {
      // Insert
      await db.insertTodo(
        title: titleController.text.trim(),
        des: descController.text.trim(),
      );
    } else {
      // Update
      await db.updateTodo(
        id: widget.todo!["id"],
        title: titleController.text.trim(),
        des: descController.text.trim(),
      );
    }

    Navigator.pop(context, true); // return true to refresh list
  }

  @override
  Widget build(BuildContext context) {
    final isEdit = widget.todo != null;

    return Scaffold(
      appBar: AppBar(
        title: Text(isEdit ? "Edit Todo" : "Add Todo"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(12.0),
        child: Column(
          children: [
            TextField(
              decoration: const InputDecoration(hintText: "Enter Todo Title"),
              controller: titleController,
            ),
            const SizedBox(height: 10),
            TextField(
              decoration: const InputDecoration(hintText: "Enter Todo Description"),
              controller: descController,
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: _saveTodo,
              child: Text(isEdit ? "Update" : "Add"),
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
import 'AddEditTodo.dart';
import 'myDatabase.dart';

class TodoListPage extends StatefulWidget {
  @override
  State<TodoListPage> createState() => _TodoListPageState();
}

class _TodoListPageState extends State<TodoListPage> {
  final MyDatabase db = MyDatabase();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Todo List'),
      ),
      body: FutureBuilder<List<Map<String, dynamic>>>(
        future: db.getAllTodos(),
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
              return Card(
                child: ListTile(
                  title: Text(todo['title'] ?? ''),
                  subtitle: Text(todo['description'] ?? ''),
                  trailing: Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      IconButton(
                        icon: const Icon(Icons.edit, color: Colors.blue),
                        onPressed: () {
                          Navigator.push(
                            context,
                            MaterialPageRoute(
                              builder: (context) => AddEditTodo(todo: todo),
                            ),
                          ).then((value) {
                            if (value == true) {
                              setState(() {});
                            }
                          });
                        },
                      ),
                      IconButton(
                        icon: const Icon(Icons.delete, color: Colors.red),
                        onPressed: () async {
                          await db.deleteTodo(id: todo['id']);
                          setState(() {});
                        },
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
            MaterialPageRoute(builder: (context) => const AddEditTodo()),
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
