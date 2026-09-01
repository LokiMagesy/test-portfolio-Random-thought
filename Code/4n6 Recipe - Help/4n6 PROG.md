https://www.geeksforgeeks.org/how-to-post-data-to-api-using-retrofit-in-android/ 

`Private ActivityMainBinding binding;` & `SetContentView(binding.getRoot());`

#### android manifests : 
* `<uses-permission android:name="android.permission.INTERNET"/>` (Outside of Application)
* `<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>` (Outside of Application)
* `android:usesClearTextTraffic="true"` (Inside of Application)

#### (dependencies) Retrofit :
+ `implementation group: 'com.squareup.retrofit2', name: 'retrofit', version: '2.5.0'`
+ `implementation group: 'com.squareup.retrofit2', name: 'converter-gson', version: '2.5.0'`
+ `implementation group: 'com.squareup.retrofit2', name: 'converter-scalars', version: '2.5.0'`

#### (dependencies) Logging:
`implementation group: 'com.squareup.okhttp3', name: 'logging-interceptor', version: '3.12.0'`

----------------------------------------------------------------------------------------------
# Exemple / Exercice & Video

### Retrofit
``` java
public class RetrofitUtil {

    private static String github = "https://api.github.com/";
    private static String local = "http://10.0.2.2:8080/";
    private static String exercice = "https://4n6.azurewebsites.net/";

    public static Service getSpringBoot(){
        Retrofit retrofit = new Retrofit.Builder()
                .addConverterFactory(ScalarsConverterFactory.create()) // String, Intger, Float, Long
                .addConverterFactory(GsonConverterFactory.create()) // CLasses
                .client(client()) // <= Video #4
                .baseUrl(github)
                .build();

        Service service = retrofit.create(Service.class);
        return service;
    }

    // logging (Video #4)
    private static OkHttpClient client() {
        HttpLoggingInterceptor interceptor = new HttpLoggingInterceptor();
        interceptor.setLevel(HttpLoggingInterceptor.Level.BODY);
        OkHttpClient client = new OkHttpClient.Builder().addInterceptor(interceptor).build();
        return client;
    }
}
```
### Service
``` java
public interface Service {

    //region Retrofit #1 video
    @GET("users/{utilisateur}/repos")
    Call<String> listReposString(@Path("utilisateur") String utilisateur);

    @GET("users/{utilisateur}")
    Call<String> utilisateurString(@Path("utilisateur") String utilisateur);
    //endregion

    //region EXERCICE #1
    //Retrofit - Exercice simple
    @GET("exos/long/double/{num}")
    Call<String> num(@Path("num") String num);
    //Retrofit - Exercice Objet complexe
    @GET("exos/truc/{complexe}")
    Call<String> complexe(@Path("complexe") String complexe);
    //endregion

    //region Retrofit #2 video
    @GET("users/{utilisateur}")
    Call<Utilisateur> utilisateur(@Path("utilisateur") String utilisateur);

    @GET("users/{utilisateur}/repos")
    Call<List<Repo>> listRepos(@Path("utilisateur") String utilisateur);
    //endregion

    //[] debut_array
    //{} debut_object

    //region EXERCICE #2
    @GET("exos/long/{num}")
    Call<List<Long>> numList(@Path("num") String num);

    @GET("exos/truc/{complexe}")
    Call<List<ComplexeList>> complexeList(@Path("complexe") String complexe);
    //endregion
}
```

### classe modele (Exercice & Video)
``` java
// Video
public class Utilisateur {
    public String login;
    public Long id;
    public Long followers;
}
// Exercice
public class Repo {
    String name;
    Long id;
}
// Exercice
public class ComplexeList {
    Long a;
    String b;
    List<Long> c;
}
```

## Test
``` java
public class ExampleInstrumentedTest {

    //region First Video
    @Test
    public void testSimple() throws IOException {
        // Context of the app under test.
        Service service = RetrofitUtil.get();
        Call<String> call = service.listReposString("hoiluncheng");
        Response<String> response = call.execute();
        String resultat = response.body();
        Log.i("Retrofit", resultat);
    }

    @Test
    public void testSimpleUtilisateur() throws IOException {
        // Context of the app under test.
        Service service = RetrofitUtil.get();
        Call<String> call = service.utilisateurString("hoiluncheng");
        Response<String> response = call.execute();
        String resultat = response.body();
        Log.i("Retrofit", resultat);
    }
    //endregion

    //region Exercice
    @Test
    public void testSimpleExercice() throws IOException {
        // Context of the app under test.
        Service service = RetrofitExercice.get();
        Call<String> call = service.num("4");
        Response<String> response = call.execute();
        String resultat = response.body();
        Log.i("Retrofit", resultat);
    }
    @Test
    public void testComplexeExercice() throws IOException {
        // Context of the app under test.
        Service service = RetrofitExercice.get();
        Call<String> call = service.complexe("complexe?name=whippsie");
        Response<String> response = call.execute();
        String resultat = response.body();
        Log.i("Retrofit", resultat);
    }
    //endregion

    //region Second Video
    @Test
    public void testSimpleUtilisateurStructure() throws IOException {
        // Context of the app under test.
        Service service = RetrofitUtil.get();
        Call<Utilisateur> call = service.utilisateur("hoiluncheng");
        Response<Utilisateur> response = call.execute();
        Utilisateur resultat = response.body();
        Log.i("Retrofit", resultat.toString());
    }

    @Test
    public void testStructure() throws IOException {
        // Context of the app under test.
        Service service = RetrofitUtil.get();
        Call<List<Repo>> call = service.listRepos("hoiluncheng");
        Response<List<Repo>> response = call.execute();
        List<Repo> resultat = response.body();
        Log.i("Retrofit", resultat.toString());
    }
    //endregion

    //region Exercice 2
    @Test
    public void testSimpleListExercice() throws IOException {
        // Context of the app under test.
        Service service = RetrofitExercice.get();
        Call<List<Long>> call = service.numList("list");
        Response<List<Long>> response = call.execute();
        List<Long> resultat = response.body();
        Log.i("Retrofit", resultat.toString());
    }
    @Test
    public void testComplexeListExercice() throws IOException {
        // Context of the app under test.
        Service service = RetrofitExercice.get();
        Call<List<ComplexeList>> call = service.complexeList("list");
        Response<List<ComplexeList>> response = call.execute();
        List<ComplexeList> resultat = response.body();
        Log.i("Retrofit", resultat.toString());
    }
    //endregion
}
```

## MainActivity (Exercice & Video #4)
Github
Finding the Id.
``` java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // appeler un serice de list et afficher dans le log
        final Service service = RetrofitUtil.get();
        service.listRepos("hoiluncheng").enqueue(new Callback<List<Repo>>() {
            @Override
            public void onResponse(Call<List<Repo>> call, Response<List<Repo>> response) {
                if(response.isSuccessful()){
                    // http 200 htpp tout s'est bien passé
                    List<Repo> resultat =  response.body();
                    Log.i("RETROFIT", resultat.size() + "");
                }else{
                    // cas d'erreur http 400 404
                    Log.i("RETROFIT", response.code()+"");
                }
            }
            @Override
            public void onFailure(Call<List<Repo>> call, Throwable t) {
                // erreur accès réseau ou alors GSON
                Log.i("RETROFIT", t.getMessage());
            }
        });

        // appeler un service et mettre l'interface graphique
        final EditText et = findViewById(R.id.et);
        final Button btn = findViewById(R.id.btn);
        final TextView tv = findViewById(R.id.tv);
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String nom = et.getText().toString();
                service.utilisateur(nom).enqueue(new Callback<Utilisateur>() {
                    @Override
                    public void onResponse(Call<Utilisateur> call, Response<Utilisateur> response) {
                     if (response.isSuccessful()){
                         tv.setText(response.body().id+"");
                     }else{
                         Log.i("RETROFIT", response.code()+"");
                     }
                    }
                    @Override
                    public void onFailure(Call<Utilisateur> call, Throwable t) {
                        Log.i("RETROFIT", t.getMessage());
                    }
                });
            }
        });
    }
}
```
# PROF

#### Android Studio
``` java
public interface Service {
    @GET("test")
    Call<String> test();

    @GET("/api/home")
    Call<List<HomeItemResponse>> home();
}
```

``` java
public class MainActivity extends AppCompatActivity {

    private ActivityMainBinding binding;
    private Service service;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        binding = ActivityMainBinding.inflate(getLayoutInflater());
        service = RetrofitUtil.get();

        setContentView(binding.getRoot());

//        getTest();
        getHome();
    }

    private void getHome() {
        service.home().enqueue(new Callback<List<HomeItemResponse>>() {
            @Override
            public void onResponse(Call<List<HomeItemResponse>> call, Response<List<HomeItemResponse>> response) {
                List<HomeItemResponse> list = response.body();
                binding.tvTest.setText(list.toString());
            }

            @Override
            public void onFailure(Call<List<HomeItemResponse>> call, Throwable t) {
                Toast.makeText(MainActivity.this, "Une erreur est survenue en communicant avec le serveur.", Toast.LENGTH_SHORT).show();
            }
        });
    }

    private void getTest() {
        service.test().enqueue(new Callback<String>() {
            @Override
            public void onResponse(Call<String> call, Response<String> response) {
                String res = response.body();
                binding.tvTest.setText(res);
            }

            @Override
            public void onFailure(Call<String> call, Throwable t) {
                Toast.makeText(MainActivity.this, "Une erreur est survenue en communicant avec le serveur.", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```
-------------------------------------------------------------------------------------------------------------

- [ ] Mercury
- [x] Venus
- [x] Earth (Orbit/Moon)
- [x] Mars
- [ ] Jupiter
- [ ] Saturn
- [ ] Uranus
- [ ] Neptune
- [ ] Comet Haley

## KickMyB-Server-main [main] (IntelliJ)

`KickMyB-Server-main` (Download) It need to be open in **IntelliJ** and not in **Android Studio** Because the server was make with the libary of Java, `Springboot`.

`ServerApplication` (click droit/right) `RUN`. In the console, It will show the port: **8080**. (Double-Check)

In Android Sutdio (Retrofit) : `.baseUrl("http://10.0.2.2:8080/")` 

Two useful Classes : `ControllerAccount` & `ControllerTask` (Use the *POST* and *GET*)

Two Classes for debugging: `ServiceAccountImpl` & `ServiceTaskImpl` (Break Point)

In the Controller, it use the *POST*(Mapping) and *GET*(Mapping). The fragment URL need to be the same from server Springboot and (android studio) *Service* Class. **Exemple**: `@GetMapping("/test")` = `@Get("test")`

``` java
@GetMapping("/test")
    public @ResponseBody String test() { return "SALUT";}

@GetMapping("/index")
	public @ResponseBody String htmlIndex() { return serviceTask.index(); }
```
It's how they're capable of making connection between the two.

### Try Url in a Browesr
`LocalLost:8080` + "/api/id/signin" or  "/api/add"
``` Java
// TODO quand on s'inscrit ou qu'on se connecte, on est pas encore dans le système
    .antMatchers("/api/id/**").permitAll() // Tout le monde va pouvoir voie le contenu de ces URL
// TODO tous les autres appels à API requierent un utilisateur authentifié
    .antMatchers("/api/**").authenticated() //  Il faut être authenitifés 
```
Normal: There was an unexpected error (type=Not Found, status=404).
--------------------------------------------------------------------------------------------------------------
#### ACCOUNT 
``` java 
//POST
@PostMapping("/api/id/signin")
    public @ResponseBody SigninResponse signin(@RequestBody  SigninRequest s) throws BadCredentialsException {}
//POST
@PostMapping("/api/id/signup")
    public @ResponseBody SigninResponse signup(@RequestBody SignupRequest s) {}
//POST
@PostMapping("/api/id/signout")
    public @ResponseBody String signout() throws BadCredentialsException {}
//GET
```

#### TASK
``` java 
//POST
@PostMapping("/api/add")
    public @ResponseBody String addOne(@RequestBody AddTaskRequest request) {}
//GET
@GetMapping("/api/progress/{taskID}/{value}")
	public @ResponseBody String updateProgress(
			@PathVariable long taskID,
			@PathVariable int value)  {}
//GET
@GetMapping("/api/home")
	public @ResponseBody List<HomeItemResponse> home() {}
//GET
@GetMapping("/api/detail/{id}")
    public @ResponseBody TaskDetailResponse detail(@PathVariable long id) {}
```

``` java
@Entity
public class MProgressEvent {
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Id     public Long id;
    public int deltaPercentage;
    public int resultPercentage;
    public boolean completed;
    public Date timestamp;
}
@Entity
public class MTask {

    @Id     @GeneratedValue(strategy = GenerationType.AUTO)
    public Long id;
    public Date creationDate;
    public Date deadline;

    @Convert(converter = AttributeEncryptor.class)  // TODO exemple stupide, servirait plutôt pour NAS ou numero carte crédit
    public String name;

    @OneToMany(fetch=FetchType.EAGER)
    public List<MProgressEvent> events = new ArrayList<>();

    @OneToOne
    public MPhoto photo;

}
```
``` java
// ??????????????????????
@Repository
public interface MProgressEventRepository extends PagingAndSortingRepository<MProgressEvent, Long> { }
// ??????????????????????
@Repository
public interface MTaskRepository extends PagingAndSortingRepository<MTask, Long> { }
```

# Local Server
`setting.gradle` :  
dependencyResolutionManagement { ...

    maven { url 'https://jitpack.io'}
}

## dependencies
Url (Exemple): departement-info-cem/KickMyB-Library

`implementation 'com.github.User:Repo:Tag'` 

- User: departement-info-cem
- Repo: KickMyB-Library
- Tag: 31d81e984312861329d96292055371b6be46755c

`implementation 'com.github.departement-info-cem:KickMyB-Library:31d81e984312861329d96292055371b6be46755c'`