``` dart
// Define abstract DataState<T>
abstract class DataState<T> {
  // Holds the data retrieved from the API
  final T? data;
  // Holds any error encountered during API call
  final DioError? error;
  // Constructor initializing data and error
  const DataState({this.data, this.error});  
}

// Define DataSuccess<T> representing a successful state
class DataSuccess<T> extends DataState<T> {
  // Constructor for DataSuccess class, initializes the 'data' property
  const DataSuccess(T data) : super(data: data);
}

// Define DataFailed<T> representing a failed state
class DataFailed<T> extends DataState<T> {
  // Constructor for DataFailed class, initializes the 'error' property
  const DataFailed(DioError error) : super(error: error);
}

// Define ApiService class for making API requests
class ApiService {
  final Dio _dio;

  ApiService(this._dio);  // Constructor initializing Dio instance

  // Perform GET request
  Future<DataState<T>> getRequest<T>(String endpoint, {Map<String, dynamic>? queryParameters}) async {
    try {
      final response = await _dio.get(endpoint, queryParameters: queryParameters);
      return DataSuccess<T>(response.data as T); // Return DataSuccess with retrieved data
    } on DioException catch (e) {
      return DataFailed<T>(e); // Return DataFailed with error if DioError occurs
    }
  }

  // Perform POST request
  Future<DataState<T>> postRequest<T>(String endpoint, dynamic data) async {
    try {
      final response = await _dio.post(endpoint, data: data);
      return DataSuccess<T>(response.data as T);  // Return DataSuccess with retrieved data
    } on DioException catch (e) {
      return DataFailed<T>(e);  // Return DataFailed with error if DioError occurs
    }
  }
}

// Define HikeService class for handling hike-related operations
class HikeService {
  final ApiService _apiService;

  HikeService(this._apiService);  // Constructor initializing ApiService instance

  // Fetch list of hikes
  Future<DataState<List<Hike>>> getHikeList() async {
    try {
      final state = await _apiService.getRequest<List<dynamic>>('Hike/GetAllValidated');
      if (state is DataSuccess) {
        final hikeList = (state.data! as List).map((elementJSON) => Hike.fromJson(elementJSON)).toList();
        return DataSuccess<List<Hike>>(hikeList);  // Return DataSuccess with list of hikes
      } else if (state is DataFailed) {
        final e = state.error!;
        // Handle DioError with more descriptive messages
        String errorMsg = 'Unknown error occurred';
        if (e.response != null) {
          if (e.response!.statusCode == 404) {
            errorMsg = 'Hike not found';
          } else if (e.response!.statusCode == 500) {
            errorMsg = 'Server error occurred';
          }
        }
        // Return DataFailed with descriptive error message
        return DataFailed<List<Hike>>(DioError(errorMsg, error: e.error));
      }
    } catch (error) {
      // Handle other errors as needed
      return DataFailed<List<Hike>>(DioError('An unexpected error occurred', error: error));
    }
    // Default return statement (should never reach)
    return DataFailed<List<Hike>>(DioError('An unexpected error occurred'));
  }
}

// Define Hike class for representing hike data
class Hike {
  // Hike properties and constructor

  factory Hike.fromJson(Map<String, dynamic> json) {
    // Create Hike object from JSON data
  }
}

```

``` dart
void handleResponse<T>(DataState<T> state) {
  if (state is DataSuccess<T>) {
    handleSuccess(state.data);
  } else if (state is DataFailed<T>) {
    handleError(state.error);
  } else {
    // Handle unexpected state
  }
}

void handleSuccess<T>(T? data) {
  if (data != null) {
    // Handle successful response
  } else {
    // Handle unexpected null data
  }
}

void handleError<T>(DioError error) {
  if (error.response != null) {
    final statusCode = error.response!.statusCode;
    // Handle different error status codes
    switch (statusCode) {
      case 404:
        // Handle 404 Not Found error
        break;
      case 500:
        // Handle 500 Internal Server Error
        break;
      default:
        // Handle other error status codes
        break;
    }
  } else {
    // Handle network-related errors
  }
}

```

```dart
// Define abstract DataState<T>
abstract class DataState<T> {
  final T? data;         // Holds the data retrieved from the API
  final DioError? error; // Holds any error encountered during API call

  // Constructor initializing data and error
  const DataState({this.data, this.error});
}

// Define DataSuccess<T> representing a successful state
class DataSuccess<T> extends DataState<T> {
  // Constructor for DataSuccess class, initializes the 'data' property
  const DataSuccess(T data) : super(data: data);
}

// Define DataFailed<T> representing a failed state
class DataFailed<T> extends DataState<T> {
  // Constructor for DataFailed class, initializes the 'error' property
  const DataFailed(DioError error) : super(error: error);
}

// Define ApiService class for making API requests
class ApiService {
  final Dio _dio;

  ApiService(this._dio);  // Constructor initializing Dio instance

  // Perform GET request
  Future<DataState<T>> getRequest<T>(String endpoint, {Map<String, dynamic>? queryParameters}) async {
    try {
      final response = await _dio.get(endpoint, queryParameters: queryParameters);
      return DataSuccess<T>(response.data); // Return DataSuccess with retrieved data
    } on DioException catch (e) {
      return DataFailed<T>(e); // Return DataFailed with error if DioError occurs
    }
  }

  // Perform POST request
  Future<DataState<T>> postRequest<T>(String endpoint, dynamic data) async {
    try {
      final response = await _dio.post(endpoint, data: data);
      return DataSuccess<T>(response.data);  // Return DataSuccess with retrieved data
    } on DioException catch (e) {
      return DataFailed<T>(e);  // Return DataFailed with error if DioError occurs
    }
  }
}

// Define HikeService class for handling hike-related operations
class HikeService {
  final ApiService _apiService;

  HikeService(this._apiService);  // Constructor initializing ApiService instance

  // Fetch list of hikes
  Future<DataState<List<Hike>>> getHikeList() async {
    try {
      final state = await _apiService.getRequest<List>('Hike/GetAllValidated');
      if (state is DataSuccess) {
        final hikeList = (state.data!).map((elementJSON) => Hike.fromJson(elementJSON)).toList();
        return DataSuccess<List<Hike>>(hikeList);  // Return DataSuccess with list of hikes
      } else if (state is DataFailed) {
        final e = state.error!;
        // Handle DioError with more descriptive messages
        String errorMsg = 'Unknown error occurred';
        if (e.response != null) {
          if (e.response!.statusCode == 404) {
            errorMsg = 'Hike not found';
          } else if (e.response!.statusCode == 500) {
            errorMsg = 'Server error occurred';
          }
        }
        // Return DataFailed with descriptive error message
        return DataFailed<List<Hike>>(DioError(errorMsg, error: e.error));
      }
    } catch (error) {
      // Handle other errors as needed
      return DataFailed<List<Hike>>(DioError('An unexpected error occurred', error: error));
    }
    // Default return statement (should never reach)
    return DataFailed<List<Hike>>(DioError('An unexpected error occurred'));
  }
}
```

# Main
```dart
import 'package:arsoude_mobile/pages/auth/login/login_page.dart';
import 'package:arsoude_mobile/pages/auth/register/register_page.dart';
import 'package:arsoude_mobile/pages/home/bottomNav.dart';
import 'package:arsoude_mobile/pages/profile.dart';
import 'package:arsoude_mobile/service/notificationService.dart';
import 'package:arsoude_mobile/utility/colors.dart';
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:flutter_localizations/flutter_localizations.dart';
import 'package:google_fonts/google_fonts.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await _initializeApp();
}

Future<void> _initializeApp() async {
  try {
    await NotificationService().initNotification();
  } catch (e) {
    print('Error initializing app: $e');
    // Handle initialization error gracefully
  }
  SystemChrome.setPreferredOrientations([DeviceOrientation.portraitUp])
      .then((_) {
    runApp(const MyApp());
  });
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Flutter Demo',
      scaffoldMessengerKey: SkeletonKey.scaffoldKey,
      localizationsDelegates: myMultilingual,
      supportedLocales: const [
        Locale('en'),
        Locale('fr'),
      ],
      theme: appTheme,
      navigatorKey: NavigationService.navigatorKey,
      home: const BottomNav(),
      routes: _routes(),
    );
  }

  Map<String, WidgetBuilder> _routes() {
    return {
      '/Home': (context) => const BottomNav(),
      '/RegisterPage': (context) => const RegisterPage(),
      '/LoginPage': (context) => const LoginPage(),
      '/ProfilePage': (context) => const ProfilePage(),
    };
  }
}

class SkeletonKey {
  static final GlobalKey<ScaffoldMessengerState> scaffoldKey =
      GlobalKey<ScaffoldMessengerState>();
}

class NavigationService {
  static final GlobalKey<NavigatorState> navigatorKey =
      GlobalKey<NavigatorState>();
}

final ThemeData appTheme = ThemeData.light().copyWith(
  textTheme: GoogleFonts.poppinsTextTheme(),
  appBarTheme: const AppBarTheme(
    color: DARKGREEN,
    foregroundColor: Colors.white,
  ),
  canvasColor: DARKGREEN,
  primaryColor: DARKGREEN,
  colorScheme: const ColorScheme.light(
    primary: DARKGREEN,
    secondary: DARKGREEN,
  ),
  hintColor: Colors.blueGrey,
  focusColor: DARKGREEN,
);

final Iterable<LocalizationsDelegate> myMultilingual = [
  Delegate(),
  GlobalMaterialLocalizations.delegate,
  GlobalWidgetsLocalizations.delegate,
  GlobalCupertinoLocalizations.delegate,
];
```

# profile Service
```dart
class ProfileService extends AuthService {
  final Dio _dio = DioService().getDioInstance();
  final Options _token = DioService().getToken();

  Future<EditDTO?> getProfile() async {
    try {
      final response = await _dio.get('User/GetProfile', options: _token);
      if (response.statusCode == 200) {
        return EditDTO.fromJson(response.data);
      } else {
        throw Exception('Failed to get profile');
      }
    } on DioExpection catch (e) {
      // Handle Dio errors
      throw Exception('Failed to get profile: ${e.message}');
    }
  }

  Future<void> sendNewProfile(EditDTO? dto) async {
    try {
      final response = await _dio.post(
        'User/EditUser',
        data: dto,
        options: _token,
      );
      // Log response if needed
      // log(response.toString());
    } on DioExpection catch (e) {
      // Handle Dio errors
      throw Exception('Failed to send new profile: ${e.message}');
    }
  }

  Future<void> resetPassword(PasswordDTO dto) async {
    try {
      final response = await _dio.post(
        'User/ChangePassword',
        data: dto,
        options: _token,
      );
      // Log response if needed
      // log(response.toString());
    } on DioExpection catch (e) {
      // Handle Dio errors
      throw Exception('Failed to reset password: ${e.message}');
    }
  }
}
```

Report: 
- Travail Équipe: Bien
- Aucune Problème en général

