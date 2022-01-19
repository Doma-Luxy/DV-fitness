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

In the `sesion.dart` file we defined the properties for creating and saving workout sessions.

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

