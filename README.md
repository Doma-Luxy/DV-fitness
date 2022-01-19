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

The code is organised into funcional folders which are:
* `data`
* `screens`
* `shared`

## data
In the `helper.dart` file we define the parameters to connect to an external api in this case [openweathermap.org](https://openweathermap.org/) and assignet a previously generated api key. Note that the retrieved data is in json format so we will have to convert it in order to get the data readable for the user.

```dart
class HttpHelper {
  //http://api.openweathermap.org/data/2.5/weather?q=split&appid=70f4aaedc2d5281a2310091bd0e01203
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

In the ses
