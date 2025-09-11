# A-1: Write a flutter code to display country list in dropdown on selection of country call state api and fill data in state list after selection state call city api and fill city list in driodown. 

## pubspec.yaml
```dart
dependencies:
  flutter:
    sdk: flutter
  http: ^1.2.0
```

## api
```link
https://countriesnow.space/api/v0.1/countries/states
```

## json format
``` json
{
    "error": false,
    "msg": "countries and states retrieved",
    "data": [
        {
            "name": "Afghanistan",
            "iso3": "AFG",
            "iso2": "AF",
            "states": [
                {
                    "name": "Badakhshan",
                    "state_code": "BDS"
                },
                {
                    "name": "Badghis",
                    "state_code": "BDG"
                }
            ]
        },
        {
            "name": "Aland Islands",
            "iso3": "ALA",
            "iso2": "AX",
            "states": []
        }
    ]
}
```

## CountryStateDropdown.dart
```dart
import 'dart:convert';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

class CountryStateDropdown extends StatefulWidget {
  const CountryStateDropdown({super.key});

  @override
  State<CountryStateDropdown> createState() => _CountryStateDropdownState();
}

class _CountryStateDropdownState extends State<CountryStateDropdown> {
  List countries = [];
  List states = [];

  String? selectedCountry;
  String? selectedState;

  @override
  void initState() {
    super.initState();
    fetchCountriesAndStates();
  }

  /// Fetch country + states from API
  Future<void> fetchCountriesAndStates() async {
    const url = "https://countriesnow.space/api/v0.1/countries/states";
    final response = await http.get(Uri.parse(url));

    if (response.statusCode == 200) {
      final parsed = jsonDecode(response.body);
      setState(() {
        countries = parsed["data"]; // countries array
      });
    } else {
      throw Exception("Failed to load data");
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Country → State Dropdown")),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          children: [
            // Country Dropdown
            DropdownButtonFormField<String>(
              value: selectedCountry,
              hint: const Text("Select Country"),
              items: countries.map<DropdownMenuItem<String>>((country) {
                return DropdownMenuItem(
                  value: country["name"],
                  child: Text(country["name"]),
                );
              }).toList(),
              onChanged: (value) {
                setState(() {
                  selectedCountry = value;
                  // get states for selected country
                  final country = countries.firstWhere(
                          (c) => c["name"] == value,
                      orElse: () => null);

                  states = country != null ? country["states"] : [];
                  selectedState = null;
                });
              },
            ),

            const SizedBox(height: 20),

            // State Dropdown
            DropdownButtonFormField<String>(
              value: selectedState,
              hint: const Text("Select State"),
              items: states.map<DropdownMenuItem<String>>((state) {
                return DropdownMenuItem(
                  value: state["name"],
                  child: Text(state["name"]),
                );
              }).toList(),
              onChanged: (value) {
                setState(() {
                  selectedState = value;
                });
              },
            ),
          ],
        ),
      ),
    );
  }
}

```
