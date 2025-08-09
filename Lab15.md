# A-1: Write a Flutter program to implement a basic onboarding flow using PageView.
## onboardingScreen.dart
```dart
import 'package:flutter/material.dart';
import 'package:insta/lab14/HomeScreen.dart';

class OnboardingScreen extends StatefulWidget {
  @override
  _OnboardingScreenState createState() => _OnboardingScreenState();
}

class _OnboardingScreenState extends State<OnboardingScreen> {
  PageController _controller = PageController();
  int _currentPage = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Expanded(
            child: PageView(
              controller: _controller,
              onPageChanged: (index) {
                setState(() {
                  _currentPage = index;
                });
              },
              children: [
                buildPage("Welcome", "This is the first page."),
                buildPage("Learn", "Learn how to use our app."),
                buildPage("Information", "Our company information!"),
                buildPage("Get Started", "Let’s begin your journey!"),
              ],
            ),
          ),
          Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: List.generate(4, (index) {
              return GestureDetector(
                onTap: () {
                  _controller.animateToPage(
                    index,
                    duration: Duration(milliseconds: 300),
                    curve: Curves.easeInOut,
                  );
                },
                child: Container(
                  margin: EdgeInsets.fromLTRB(6, 6, 6, 20),
                  width: _currentPage == index ? 12 : 8,
                  height: _currentPage == index ? 12 : 8,
                  decoration: BoxDecoration(
                    color: _currentPage == index ? Colors.blue : Colors.grey,
                    shape: BoxShape.circle,
                  ),
                ),
              );
            }),
          ),
          if (_currentPage == 3)
            Padding(
              padding: const EdgeInsets.only(bottom: 20),
              child: ElevatedButton(
                onPressed: () {
                  Navigator.of(context).push(
                    MaterialPageRoute(
                      builder: (context) {
                        return HomeScreen();
                      },
                    ),
                  );
                },
                child: Text("Get Started"),
              ),
            ),
        ],
      ),
    );
  }

  Widget buildPage(String title, String subtitle) {
    return Center(
      child: Padding(
        padding: const EdgeInsets.all(24.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Icon(Icons.mobile_friendly, size: 100, color: Colors.blue),
            SizedBox(height: 20),
            Text(title,
                style: TextStyle(fontSize: 26, fontWeight: FontWeight.bold)),
            SizedBox(height: 10),
            Text(subtitle,
                textAlign: TextAlign.center, style: TextStyle(fontSize: 16)),
          ],
        ),
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
# A-2: Write a Flutter program that displays a horizontal image gallery using PageView.
## Create Assets file and import in pubspec.yaml
```dart
  assets:
    - assets/images/
```

## imageGallery.dart
```dart
import 'package:flutter/material.dart';

class ImageGallery extends StatelessWidget {
  final List<String> imageUrls = [
    'images/animal1.jpg',
    'images/John-Wick.jpg',
    'images/Tripti1.jpg',
    'images/cap.jpg',
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Horizontal Image Gallery'),
        centerTitle: true,
      ),
      body: PageView.builder(
        scrollDirection: Axis.horizontal,
        itemCount: imageUrls.length,
        itemBuilder: (context, index) {
          return Center(
            child: Padding(
              padding: const EdgeInsets.all(16.0),
              child: Image.asset(
                'assets/${imageUrls[index]}',
                fit: BoxFit.cover,
                width: double.infinity,
              ),
            ),
          );
        },
      ),
    );
  }
}
```
