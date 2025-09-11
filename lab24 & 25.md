# A-1: Write a flutter code to design entry screen with Name, DOB, City, Address, etc.. validate it & save data to webserver using POST method with formurlencoded. 

## PersonListScreen.dart
```dart
import 'dart:convert';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'package:insta/lab24/PersonAddScreen.dart';

class PersonListScreen extends StatefulWidget {
  const PersonListScreen({super.key});

  @override
  State<PersonListScreen> createState() => _PersonListScreenState();
}

class _PersonListScreenState extends State<PersonListScreen> {
  final String baseUrl = "https://6861123a8e7486408444d339.mockapi.io/person";
  List persons = [];
  bool isLoading = false;

  @override
  void initState() {
    super.initState();
    fetchPersons();
  }

  Future<void> fetchPersons() async {
    setState(() => isLoading = true);
    final response = await http.get(Uri.parse(baseUrl));
    if (response.statusCode == 200) {
      setState(() {
        persons = jsonDecode(response.body);
      });
    }
    setState(() => isLoading = false);
  }

  Future<void> deletePerson(String id) async {
    final response = await http.delete(Uri.parse("$baseUrl/$id"));
    if (response.statusCode == 200) {
      fetchPersons();
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(content: Text("Deleted Successfully ❌")),
      );
    }
  }

  void openForm({Map? person}) {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (context) => PersonAddScreen(
          person: person,
        ),
      ),
    ).then((_) => fetchPersons()); 
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Person List")),
      body: isLoading
          ? const Center(child: CircularProgressIndicator())
          : ListView.builder(
        itemCount: persons.length,
        itemBuilder: (context, index) {
          final p = persons[index];
          return Card(
            child: ListTile(
              title: Text(p["Name"]),
              subtitle: Text("${p["City"]} • ${p["Address"]}"),
              trailing: Row(
                mainAxisSize: MainAxisSize.min,
                children: [
                  IconButton(
                    icon: const Icon(Icons.edit, color: Colors.blue),
                    onPressed: () => openForm(person: p),
                  ),
                  IconButton(
                    icon: const Icon(Icons.delete, color: Colors.red),
                    onPressed: () => deletePerson(p["id"]),
                  ),
                ],
              ),
            ),
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => openForm(),
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

## PersonAddScreen.dart
```dart
import 'dart:convert';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

class PersonAddScreen extends StatefulWidget {
  final Map? person;
  const PersonAddScreen({super.key, this.person});

  @override
  State<PersonAddScreen> createState() => _PersonAddScreen();
}

class _PersonAddScreen extends State<PersonAddScreen> {
  final _formKey = GlobalKey<FormState>();

  final TextEditingController nameController = TextEditingController();
  final TextEditingController dobController = TextEditingController();
  final TextEditingController cityController = TextEditingController();
  final TextEditingController addressController = TextEditingController();

  bool isSaving = false;

  String baseUrl = "https://6861123a8e7486408444d339.mockapi.io/person";

  @override
  void initState() {
    super.initState();
    if (widget.person != null) {
      nameController.text = widget.person!["Name"];
      dobController.text = widget.person!["DOB"];
      cityController.text = widget.person!["City"];
      addressController.text = widget.person!["Address"];
    }
  }

  Future<void> savePerson() async {
    if (!_formKey.currentState!.validate()) return;

    setState(() => isSaving = true);

    final body = {
      "Name": nameController.text,
      "DOB": dobController.text,
      "City": cityController.text,
      "Address": addressController.text,
    };

    final url = widget.person == null
        ? Uri.parse(baseUrl) // POST
        : Uri.parse("$baseUrl/${widget.person!["id"]}"); // PUT

    final response = widget.person == null
        ? await http.post(url,
        headers: {"Content-Type": "application/x-www-form-urlencoded"},
        body: body)
        : await http.put(url,
        headers: {"Content-Type": "application/x-www-form-urlencoded"},
        body: body);

    setState(() => isSaving = false);

    if (response.statusCode == 201 || response.statusCode == 200) {
      Navigator.pop(context); // back to list
    } else {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text("Failed ❌: ${response.statusCode}")),
      );
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.person == null ? "Add Person" : "Edit Person"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              TextFormField(
                controller: nameController,
                decoration: const InputDecoration(labelText: "Name"),
                validator: (val) =>
                val == null || val.isEmpty ? "Enter Name" : null,
              ),
              TextFormField(
                controller: dobController,
                decoration: const InputDecoration(
                  labelText: "DOB",
                  suffixIcon: Icon(Icons.calendar_today),
                ),
                readOnly: true, // prevent keyboard
                onTap: () async {
                  DateTime? pickedDate = await showDatePicker(
                    context: context,
                    initialDate: DateTime.now(),
                    firstDate: DateTime(1900), // start year
                    lastDate: DateTime(2100),  // end year
                  );

                  if (pickedDate != null) {
                    String formattedDate =
                        "${pickedDate.year}-${pickedDate.month.toString().padLeft(2, '0')}-${pickedDate.day.toString().padLeft(2, '0')}";
                    dobController.text = formattedDate;
                  }
                },
                validator: (val) =>
                val == null || val.isEmpty ? "Select DOB" : null,
              ),

              TextFormField(
                controller: cityController,
                decoration: const InputDecoration(labelText: "City"),
                validator: (val) =>
                val == null || val.isEmpty ? "Enter City" : null,
              ),
              TextFormField(
                controller: addressController,
                decoration: const InputDecoration(labelText: "Address"),
                validator: (val) =>
                val == null || val.isEmpty ? "Enter Address" : null,
              ),
              const SizedBox(height: 20),
              ElevatedButton(
                onPressed: isSaving ? null : savePerson,
                child: isSaving
                    ? const CircularProgressIndicator(color: Colors.white)
                    : Text(widget.person == null ? "Save" : "Update"),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```
