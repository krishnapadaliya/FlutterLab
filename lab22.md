# A-1: Write a flutter code to call rest api (using Mockapi) with GET Method with httpclient library and display data in List widget using future builder.
## ApiListPage.dart
```dart
import 'dart:convert';
import 'dart:io';
import 'package:flutter/material.dart';

class ApiListPage extends StatelessWidget {
  const ApiListPage({super.key});

  Future<List<dynamic>> fetchData() async {
    final url = Uri.parse("https://6861123a8e7486408444d339.mockapi.io/Note");

    HttpClient httpClient = HttpClient();
    try {
      final request = await httpClient.getUrl(url);
      final response = await request.close();

      if (response.statusCode == 200) {
        final responseBody = await response.transform(utf8.decoder).join();
        return jsonDecode(responseBody);
      } else {
        throw Exception("Failed to load data. Status Code: ${response.statusCode}");
      }
    } finally {
      httpClient.close();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("MockAPI Jobs List"),
        centerTitle: true,
      ),
      body: FutureBuilder<List<dynamic>>(
        future: fetchData(),
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return const Center(child: CircularProgressIndicator());
          } else if (snapshot.hasError) {
            return Center(child: Text("Error: ${snapshot.error}"));
          } else if (!snapshot.hasData || snapshot.data!.isEmpty) {
            return const Center(child: Text("No data found"));
          } else {
            final data = snapshot.data!;
            return ListView.builder(
              itemCount: data.length,
              itemBuilder: (context, index) {
                final job = data[index];
                return ListTile(
                  leading: CircleAvatar(
                    child: Text(job['id']),
                  ),
                  title: Text(job['title']),
                );
              },
            );
          }
        },
      ),
    );
  }
}
```

# A-2: Write a flutter code pass Path Variable and Query Variable as parameter in GET Method and display single recod in full screen page. 

## 1. Call a REST API GET Method with:
###   - Path variable (example: /users/{id})
###     ex. final url = Uri.parse("https://6861123a8e7486408444d339.mockapi.io/Note/$id");
###   - Query variable (example: ?active=true)
###     ex. final url = Uri.parse("https://64f9f9c54099a25b41bda5c4.mockapi.io/users/$userId?active=$isActive");
## 2. Fetch a single record.

## DetailPage.dart
```dart
import 'dart:convert';
import 'dart:io';
import 'package:flutter/material.dart';

class DetailPage extends StatelessWidget {
  final String id;

  const DetailPage({
    super.key,
    required this.id,
  });

  Future<Map<String, dynamic>> fetchUser() async {
    final url = Uri.parse(
        "https://6861123a8e7486408444d339.mockapi.io/Note/$id");

    HttpClient httpClient = HttpClient();
    try {
      final request = await httpClient.getUrl(url);
      final response = await request.close();

      if (response.statusCode == 200) {
        final responseBody = await response.transform(utf8.decoder).join();
        return jsonDecode(responseBody);
      } else {
        throw Exception("Failed to load data. Status Code: ${response.statusCode}");
      }
    } finally {
      httpClient.close();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey[100],
      appBar: AppBar(
        title: const Text("Note Detail"),
        centerTitle: true,
        elevation: 2,
      ),
      body: FutureBuilder<Map<String, dynamic>>(
        future: fetchUser(),
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return const Center(child: CircularProgressIndicator());
          } else if (snapshot.hasError) {
            return Center(
              child: Text(
                "⚠️ Error: ${snapshot.error}",
                style: const TextStyle(color: Colors.red, fontSize: 16),
              ),
            );
          } else if (!snapshot.hasData) {
            return const Center(
              child: Text("No data found", style: TextStyle(fontSize: 18)),
            );
          } else {
            final user = snapshot.data!;
            return Padding(
              padding: const EdgeInsets.all(16.0),
              child: Card(
                elevation: 6,
                shadowColor: Colors.black54,
                shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(20),
                ),
                child: Container(
                  width: double.infinity,
                  padding: const EdgeInsets.all(20.0),
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      // Circle Icon
                      Center(
                        child: CircleAvatar(
                          radius: 40,
                          backgroundColor: Colors.blueAccent,
                          child: Text(
                            user['id'].toString(),
                            style: const TextStyle(
                                fontSize: 22,
                                fontWeight: FontWeight.bold,
                                color: Colors.white),
                          ),
                        ),
                      ),
                      const SizedBox(height: 20),

                      // Title
                      Text(
                        user['title'] ?? "No Title",
                        style: const TextStyle(
                            fontSize: 24, fontWeight: FontWeight.bold),
                      ),
                      const Divider(height: 30, thickness: 1),

                      // Details
                      Row(
                        children: [
                          const Icon(Icons.perm_identity,
                              color: Colors.blueAccent),
                          const SizedBox(width: 10),
                          Text("ID: ${user['id']}",
                              style: const TextStyle(fontSize: 18)),
                        ],
                      ),
                    ],
                  ),
                ),
              ),
            );
          }
        },
      ),
    );
  }
}
```
