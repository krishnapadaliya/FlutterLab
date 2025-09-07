# 📂 Flutter – Fetch JSON from Assets

This tutorial explains how to load a **JSON file from the assets folder**, parse it, and display data in Flutter widgets.

---

## 🔹 Step 1: Create JSON file in assets

Create a file named **`assets/data.json`**:

```json
[
  {
    "name": "Krishna",
    "age": 25,
    "city": "Ahmedabad"
  },
  {
    "name": "Rahul",
    "age": 28,
    "city": "Surat"
  },
  {
    "name": "Priya",
    "age": 22,
    "city": "Rajkot"
  }
]
```

## 🔹 Step 2: Add assets in pubspec.yaml

```yaml
flutter:
  assets:
    - assets/data.json
```

## 🔹Step 3: Flutter Code

```dart
import 'dart:convert';
import 'package:flutter/material.dart';
import 'package:flutter/services.dart' show rootBundle;

class JsonDemoPage extends StatefulWidget {
  const JsonDemoPage({super.key});

  @override
  State<JsonDemoPage> createState() => _JsonDemoPageState();
}

class _JsonDemoPageState extends State<JsonDemoPage> {
  List<dynamic> jsonData = [];

  // Load JSON from assets
  Future<void> loadJson() async {
    String data = await rootBundle.loadString('assets/data.json');
    setState(() {
      jsonData = json.decode(data);
    });
  }

  @override
  void initState() {
    super.initState();
    loadJson();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("JSON from Assets"),
        centerTitle: true,
      ),
      body: jsonData.isEmpty
          ? const Center(child: CircularProgressIndicator())
          : ListView.builder(
              itemCount: jsonData.length,
              itemBuilder: (context, index) {
                final person = jsonData[index];
                return Card(
                  margin: const EdgeInsets.all(8),
                  child: ListTile(
                    leading: CircleAvatar(
                      child: Text(person['name'][0]),
                    ),
                    title: Text(person['name']),
                    subtitle: Text("Age: ${person['age']} | City: ${person['city']}"),
                  ),
                );
              },
            ),
    );
  }
}
```

## 🔎 Understanding `rootBundle.loadString('assets/data.json')`

### Code:
```dart
String data = await rootBundle.loadString('assets/data.json');
```

### Explanation
#### rootBundle → Flutter ka built-in asset loader hota hai.
#### loadString() → ek function jo asset ko string me laata hai.

#### iska matlab hai:
#### 👉 "assets/data.json" file ko padh ke uska pura content ek String me daal do.
