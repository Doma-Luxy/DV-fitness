# DV fitness

## About Flutter:
Flutter is Google's UI toolkit for building beautiful, natively compiled applications for mobile, web, and desktop from a single codebase. Flutter works with existing code, is used by developers and organizations around the world, and is free and open source.

## About this app:
This is an app written in Dart using Flutter. As it was written in Fulutter the app was tested in a browser, iOS device, and Android device. A demo of the apps functionality is showcased in the demo below.

https://user-images.githubusercontent.com/54951169/150196915-280a6cd6-081e-42ab-b5d9-2c9dc70d196d.MP4

## Topic covered in this app
* stateful and stateless widgets
* adding navidation
* retrieving data from a web service
* persisting data localy

## Brief code explanation

The code is organised into multiple funcional folders. Flutter creates all the folders needed. Amongst which the iOS and Android folder we use to build the project for the respective platform. We edit the code in the lib folder where we created the next subfolders:
* `data`
* `screens`
* `shared`

## data
In the `helper.dart` file we define the parameters to connect to an external api in this case [openweathermap.org](https://openweathermap.org/) and assignet a previously generated api key. Note that the retrieved data is in json format so we will have to convert it in order to get the data readable for the user.

```dart
class HttpHelper {
   final String authority = 'api.openweathermap.org';
  final String path = 'data/2.5/weather';
  final String apiKey = '70f4aaedc2d5281a2310091bd0e01203';

  Future<Weather> getWeather(String location) async {
    Map<String, dynamic> parameters = {'q': location, 'appId': apiKey};
    Uri uri = Uri.https(authority, path, parameters);
    http.Response result = await http.get(uri);
    Map<String, dynamic> data = json.decode(result.body);
    Weather weather = Weather.fromJson(data);

    return weather;
  }
  ```

In the `session.dart` file we defined the properties for creating and saving workout sessions.

```dart
class Session {
  int id = 0;
  String date = '';
  String description = '';
  int duration = 0;

  Session(this.id, this.date, this.description, this.duration);

  Session.fromJson(Map<String, dynamic> sessionMap) {
    id = sessionMap['id'] ?? 0;
    date = sessionMap['date'] ?? '';
    description = sessionMap['description'] ?? '';
    duration = sessionMap['duration'] ?? 0;
  }

  Map<String, dynamic> toJson() {
    return {
      'id': id,
      'date': date,
      'description': description,
      'duration': duration,
    };
  }
}
 ```
 In the `sp_helper.dart` file we import the *shared_preferences* from the `pubspec.yaml` to be able to share data between files
 
  In the `weather.dart` file we get the weather data retrieved from the api
  
  ```dart
class Weather {
  String name = '';
  String description = '';
  double temperature = 0;
  double perceived = 0;
  int pressure = 0;
  int humidity = 0;

  Weather(this.name, this.description, this.temperature, this.perceived, this.pressure,
      this.humidity);

  Weather.fromJson(Map<String, dynamic> weatherMap) {
    this.name = weatherMap['name'];
    this.temperature = (weatherMap['main']['temp'] - 273.15) ?? 0;
    this.perceived = (weatherMap['main']['feels_like'] - 273.15) ?? 0;
    this.pressure = weatherMap['main']['pressure'] ?? 0;
    this.humidity = weatherMap['main']['humidity'] ?? 0;
    this.description = weatherMap['weather'][0]['main'] ?? '';
  }
}
```
## screens

In the `bmi_screen.dart` we define the upper menu to let the user choose weather to calculate the BMI walue in "metric" or "imperial" units.
  
  ```dart
  
class _BmiScreenState extends State<BmiScreen> {
  final TextEditingController txtHeight = TextEditingController();
  final TextEditingController txtWeight = TextEditingController();
  final double fontSize = 18;
  String result = '';
  bool isMetric = true;
  bool isImperial = false;
  double? height;
  double? weight;
  late List<bool> isSelected;
  String heightMessage = '';
  String weightMessage = '';

  @override
  void initState() {
    isSelected = [isMetric, isImperial];
    super.initState();
  }
```
Furthermore we created a widget so the user can input the required infromation to perform the BMI calculation.

  ```dart
@override
  Widget build(BuildContext context) {
    heightMessage =
        'Please insert your height in ' + ((isMetric) ? 'cm' : 'inches');
    weightMessage =
        'Please insert your wight in ' + ((isMetric) ? 'kg' : 'lbs');
    return Scaffold(
      appBar: AppBar(title: Text('BMI Calculator')),
      bottomNavigationBar: MenuBottom(),
      drawer: MenuDrawer(),
      body: SingleChildScrollView(
        child: Column(children: [
          ToggleButtons(children: [
            Padding(
              padding: EdgeInsets.symmetric(horizontal: 16),
              child: Text('Metric', style: TextStyle(fontSize: fontSize)),
            ),
            Padding(
              padding: EdgeInsets.symmetric(horizontal: 16),
              child: Text('Imperial', style: TextStyle(fontSize: fontSize)),
            ),
          ], isSelected: isSelected, onPressed: toggleMeasure),
          Padding(
            padding: const EdgeInsets.all(24.0),
            child: TextField(
              controller: txtHeight,
              keyboardType: TextInputType.number,
              decoration: InputDecoration(hintText: heightMessage),
            ),
          ),
          Padding(
            padding: const EdgeInsets.all(24),
            child: TextField(
              controller: txtWeight,
              keyboardType: TextInputType.number,
              decoration: InputDecoration(hintText: weightMessage),
            ),
          ),
          ElevatedButton(
            child: Text('Calculate BMI', style: TextStyle(fontSize: fontSize)),
            onPressed: findBMI,
          ),
          Text(result, style: TextStyle(fontSize: fontSize)),
        ]),
      ),
    );
  }
```
Finally we define the formulas the app will use to calculate the users BMI
```dart
   void findBMI() {
    double bmi = 0;
    double height = double.tryParse(txtHeight.text) ?? 0;
    double weight = double.tryParse(txtWeight.text) ?? 0;

    if (isMetric) {
      bmi = (weight / height / height) * 10000;
    } else {
      bmi = weight * 703 / (height * height);
    }
    setState(() {
      result = 'Your BMI is ' + bmi.toStringAsFixed(2);
    });
  }
}
```
In the `intro_screen.dart` file we set up the homescreen of the app including the app bar, menu drawer, background image, and motivational text for the user

```dart
class IntroScreen extends StatelessWidget {
  const IntroScreen({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("DV Fitness")),
      drawer: MenuDrawer(),
      bottomNavigationBar: MenuBottom(),
      body: Container(
          decoration: BoxDecoration(
              image: DecorationImage(
                  image: AssetImage('assets/gym.jpg'),
                  fit: BoxFit.cover)),
          child: Center(
              child: Container(
                  padding: EdgeInsets.all(24),
                  decoration: BoxDecoration(
                    borderRadius: BorderRadius.all(Radius.circular(25)),
                    color: Colors.white70,
                  ),
                  child: Text("Per aspera ad astra",
                      textAlign: TextAlign.center,
                      style: TextStyle(fontSize: 22, shadows: [
                        Shadow(
                            offset: Offset(1.0, 1.0),
                            blurRadius: 5.0,
                            color: Colors.grey)
                      ]))))),
    );
  }
}
```
In the `sessions_screen.dart` file we created the layout of the sessions screen where the user can input, manage and delete training sessions.

In the `weather_screen.dart` we define the look of the weather screen. From the `weather.dart` file we retrieve the data of the place, visibility, temperature, feels as, pressure and humidity.

```dart
class WeatherScreen extends StatefulWidget {
  const WeatherScreen({Key? key}) : super(key: key);

  @override
  _WeatherScreenState createState() => _WeatherScreenState();
}

class _WeatherScreenState extends State<WeatherScreen> {
  final TextEditingController txtPlace = TextEditingController();
  Weather result = Weather('', '', 0, 0, 0, 0);
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(title: Text('Weather')),
        body: Padding(
          padding: EdgeInsets.all(16),
          child: ListView(children: [
            Padding(
                padding: EdgeInsets.all(16),
                child: TextField(
                    controller: txtPlace,
                    decoration: InputDecoration(
                        hintText: 'Enter a city',
                        suffixIcon: IconButton(
                            icon: Icon(Icons.search), onPressed: getData)))),
            weatherRow('Place: ', result.name),
            weatherRow('Description: ', result.description),
            weatherRow('Temperature: ', result.temperature.toStringAsFixed(2)),
            weatherRow('Perceived: ', result.perceived.toStringAsFixed(2)),
            weatherRow('Pressure: ', result.pressure.toString()),
            weatherRow('Humidity: ', result.humidity.toString()),

          ]),
        ));
  }

  Future getData() async {
    HttpHelper helper = HttpHelper();
    result = await helper.getWeather(txtPlace.text);
    setState(() {});
  }

  Widget weatherRow(String label, String value) {
    Widget row = Padding(
        padding: EdgeInsets.symmetric(vertical: 16),
        child: Row(children: [
          Expanded(
              flex: 3,
              child: Text(label,
                  style: TextStyle(
                    fontSize: 20,
                    color: Theme.of(context).hintColor,
                  ))),
          Expanded(
              flex: 4,
              child: Text(value,
                  style: TextStyle(
                    fontSize: 20,
                    color: Theme.of(context).primaryColor,
                  ))),
        ]));
    return row;
  }
}
```
## shared
In the `menu_bottom.dart` we created the bottom bar with the home, and BMI buttons do the user has quick access to these options.
```dart
import 'package:flutter/material.dart';

class MenuBottom extends StatelessWidget {
  const MenuBottom({
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return BottomNavigationBar(
      onTap: (int index) {
        switch (index) {
          case 0:
            Navigator.pushNamed(context, '/');
            break;
          case 1:
            Navigator.pushNamed(context, '/bmi');
            break;
        }
      },
      items: [
        BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Home'),
        BottomNavigationBarItem(
            icon: Icon(Icons.monitor_weight), label: 'BMI'),
      ],
    );
  }
}
```
In the `menu_drawer.dart` we create the drawer on the upper left of the app where the user can access the features like: "Home", "BMI Calculator", "Weather", "Training"
```dart
class MenuDrawer extends StatelessWidget {
  const MenuDrawer({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Drawer(
        child: ListView(
      children: buildMenuItems(context),
    ));
  }

  List<Widget> buildMenuItems(BuildContext context) {
    final List<String> menuTitles = [
      'Home',
      'BMI Calculator',
      'Weather',
      'Training',
    ];
    List<Widget> menuItems = [];
    menuItems.add(DrawerHeader(
        decoration: BoxDecoration(color: Colors.grey),
        child: Text('DV Fitness',
            style: TextStyle(color: Colors.black, fontSize: 28))));
    menuTitles.forEach((String element) {
      Widget screen = Container();
      menuItems.add(ListTile(
          title: Text(element, style: TextStyle(fontSize: 18)),
          onTap: () {
            switch (element) {
              case 'Home':
                screen = IntroScreen();
                break;
              case 'BMI Calculator':
                screen = BmiScreen();
                break;
              case 'Weather':
                screen = WeatherScreen();
                break;
              case 'Training':
                screen = SessionsScreen();
                break;
            }
            Navigator.of(context).pop();
            Navigator.of(context)
                .push(MaterialPageRoute(builder: (context) => screen));
          }));
    });
    return menuItems;
  }
}
```
