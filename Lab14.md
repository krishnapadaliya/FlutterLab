# A-1: Write a flutter code to open bottom sheet on button click.
## bottomSheet.dart
```dart
import 'package:flutter/material.dart';

class BottomSheetExample extends StatelessWidget {
  const BottomSheetExample({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: ElevatedButton(
          child: Text("Click"),
          onPressed: () {
            showModalBottomSheet(
              context: context,
              builder: (context) {
                return Container(
                  height: 100,
                  width: double.infinity,
                  child: Center(child: Text("Hello")),
                );
              },
            );
          },
        ),
      ),
    );
  }
}
```
# A-2: Write a flutter code to create and use navigation drawer.
## navigationDrawer.dart
```dart
import 'package:flutter/material.dart';

class NavigationDrawerExample extends StatelessWidget {
  const NavigationDrawerExample({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Drawer Example")),
      drawer: Drawer(
        child: ListView(
          padding: EdgeInsets.zero,
          children: [
            const DrawerHeader(
              decoration: BoxDecoration(color: Colors.blue),
              child: Text(
                'Welcome, Krishna!',
                style: TextStyle(color: Colors.white, fontSize: 24),
              ),
            ),
            ListTile(
              leading: Icon(Icons.home),
              title: Text('Home'),
              onTap: () => Navigator.pop(context),
            ),
            ListTile(
              leading: Icon(Icons.person),
              title: Text('Profile'),
              onTap: () => Navigator.pop(context),
            ),
            ListTile(
              leading: Icon(Icons.logout),
              title: Text('Logout'),
              onTap: () => Navigator.pop(context),
            ),
          ],
        ),
      ),
    );
  }
}

```
# A-3: Write a flutter code to use the Bottom NavigationBar & on click display different pages.
## bottomNavigationBar.dart
```dart
import 'package:flutter/material.dart';
import 'HomeScreen.dart';
import 'SearchScreen.dart';
import 'DownloadScreen.dart';

class BottomNavExample extends StatefulWidget {
  @override
  State<BottomNavExample> createState() => _BottomNavExampleState();
}

class _BottomNavExampleState extends State<BottomNavExample> {
  int selectedIndex = 0;

  final List<Widget> pages = [
    HomeScreen(),
    SearchScreen(),
    DownloadScreen(),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: pages[selectedIndex],
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: selectedIndex,
        selectedItemColor: Colors.red,
        items: const [
          BottomNavigationBarItem(icon: Icon(Icons.home_filled), label: 'Home'),
          BottomNavigationBarItem(icon: Icon(Icons.search), label: 'Search'),
          BottomNavigationBarItem(icon: Icon(Icons.download), label: 'Downloads'),
        ],
        onTap: (index) {
          setState(() {
            selectedIndex = index;
          });
        },
      ),
    );
  }
}
```
## HomeScreen.dart
```dart
import 'package:flutter/material.dart';
import 'SearchScreen.dart';

class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Center(
            child: Text(
              "Home Screen",
              style: TextStyle(fontSize: 50),
            ),
          ),
          ElevatedButton(
            onPressed: () {
              Navigator.of(context).push(
                MaterialPageRoute(
                  builder: (context) {
                    return SearchScreen();
                  },
                ),
              );
            },
            child: Text("Screen Page"),
          )
        ],
      ),
    );
  }
}

```
## SearchScreen.dart
```dart
import 'package:flutter/material.dart';

class SearchScreen extends StatelessWidget {
  const SearchScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text(
          "Search Screen",
          style: TextStyle(fontSize: 50),
        ),
      ),
    );
  }
}

```
## DownloadScreen.dart
```dart
import 'package:flutter/material.dart';

class DownloadScreen extends StatelessWidget {
  const DownloadScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text(
          "Download Screen",
          style: TextStyle(fontSize: 50),
        ),
      ),
    );
  }
}

```
