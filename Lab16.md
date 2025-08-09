# A-2: Write a flutter code to do navigation between two different pages using a name route.
## main.dart
```dart

void main() {
  WidgetsFlutterBinding.ensureInitialized(); // Add this line
  runApp(const MyApp());
}


class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'hello',
      theme: ThemeData(
        // textTheme: TextTheme(
        //   // bodyLarge: TextStyle(fontFamily: 'FontMain',color: Colors.amber),
        //   titleMedium:   TextStyle(fontFamily: 'FontMain'),
        // ),
        //colorScheme: ColorScheme.fromSeed(seedColor: Colors.green),
        useMaterial3: true,
      ),
      // home:  HomeScreen(),
      initialRoute: '/',
      routes: {
        '/': (context) => PageRouting(),
        '/search': (context) => SearchScreen(),
      },
    );
  }
}
```
## PageRouting.dart
```dart
import 'package:flutter/material.dart';

class PageRouting extends StatelessWidget {
  const PageRouting({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Center(
              child: Text(
                "Home Screen home",
                style: TextStyle(fontSize: 50),
              )),
          ElevatedButton(
            onPressed: () {
              Navigator.pushNamed(context, '/search');
            },
            child: Text("Screen Page"),
          )
        ],
      ),
    );
  }
}
```
