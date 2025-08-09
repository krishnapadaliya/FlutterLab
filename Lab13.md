# A-1: Write a flutter code scroll whole screen content using single child scrollview. 
## singleChildScroll.dart
```dart
import 'package:flutter/material.dart';

class singleChildScroll extends StatelessWidget {
  const singleChildScroll({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SingleChildScrollView(
        scrollDirection: Axis.horizontal,
        child: Row(
          children: [
            Container(
              height: 200,
              width: 200,
              color: Colors.red,
            ),
            Container(
              height: 200,
              width: 200,
              color: Colors.blue,
            ),
            Container(
              height: 200,
              width: 200,
              color: Colors.green,
            ),
            Container(
              height: 200,
              width: 200,
              color: Colors.orange,
            ),
            Container(
              height: 200,
              width: 200,
              color: Colors.yellow,
            )
          ],
        ),
      ),
    );
  }
}
```

# A-2: Write a flutter code to use listview/gridview inside single child scrollview. 
## gridView.dart
```dart
import 'package:flutter/material.dart';

class gridView extends StatelessWidget {
  const gridView({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        child: GridView.custom(
          gridDelegate:
              SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 5),
          childrenDelegate: SliverChildBuilderDelegate(
            (context, index) {
              return Container(child: Text("$index"),height: 200,decoration: BoxDecoration(border: Border.all()),);
            },
          ),
        ),
      ),
    );
  }
}

```

# A-3: Write a flutter code to get current date from system and format into different date formats. 
## Ex. dd/MM/yyyy, dd-MM-yyyy 
## dateFormate.dart
```dart
import 'package:flutter/material.dart';
import 'package:intl/intl.dart';

class dateFormate extends StatelessWidget {
  const dateFormate({super.key});

  @override
  Widget build(BuildContext context) {
    DateTime date = DateTime.now();
    //String formate1 = DateFormat('dd/MM/yyyy').format(date);

    return Scaffold(
      body: Column(
        children: [
          Text("$date", style: TextStyle(fontSize: 30)),
          // Text(formate1, style: TextStyle(fontSize: 30)),
          Text(DateFormat('dd-MM-yyyy').format(date), style: TextStyle(fontSize: 30)),
          Text(DateFormat('dd/MM/yy').format(date), style: TextStyle(fontSize: 30)),
          Text(DateFormat('MM/dd/yy').format(date), style: TextStyle(fontSize: 30)),
          Text(DateFormat('yyyy/dd/MM').format(date), style: TextStyle(fontSize: 30)),
          Text(DateFormat('dd MM yyyy').format(date), style: TextStyle(fontSize: 30)),
          Text(DateFormat('dd MMMM yy').format(date), style: TextStyle(fontSize: 30)),
        ],
      ),
    );
  }
}

```
