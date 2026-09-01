# Retrofit

Retrofit est une librairie Java qui permet d'effectuer des appels HTTP et d'en recevoir la réponse.

## Rappel HTTP

Le protocole [_HyperText Transfer Protocol_](https://fr.wikipedia.org/wiki/Hypertext_Transfer_Protocol) a été développé afin de permettre à un appareil client (ex: un ordinateur, un téléphone Android) de communiquer avec un serveur.

On peut envoyer et recevoir plusieurs types de contenu à l'aide du protocole, comme des fichiers HTML, CSS, Javascript, json, des images, etc.

HTTP en quelques points :

- On communique avec le service en spécifiant son **URL** ainsi que le **type d'action** à effectuer.
  - L'**URL** est composée de 3 principales parties :
    - Le **protocole** (ex: `https://`)
    - L'**adresse vers le serveur** (ex: `github.com`)
    - Le **chemin** sur le serveur pour accéder à la ressource voulue (ex: `/departement-info-cem/4N6-Mobile`)
  - Le **type d'action** correspond généralement à ce qu'on tente d'effectuer avec la ressource à laquelle on accès avec l'URL. Les actions les plus utilisées sont `GET` (obtenir), `POST` (envoyer), `PUT` (modifier), `DELETE` (supprimer)
  - Textuellement, on peu exprimer une partie d'une requête HTTP comme suis : `GET https://github.com/departement-info-cem/4N6-Mobile`
- À la réponse d'une requête, le serveur va accompagner la réponse d'un code qui résume comment la requête a été traitée. Voici quelques un des codes les plus fréquement utilisés :
  - `200` : La requête a bien été traitée
  - `201` : La requête a bien été traitée et un objet a été traité
  - `400` : La requête a été traité, mais les données fournies par le client sont problématiques
  - `404` : La ressource demandée n'a pas été trouvée sur le serveur
  - `500` : Une erreur interne innatendue est survenue

D'autres concepts comme les en-têtes, les cookies et les _body_ seront abordés dans d'autres documents.

## Mise en place

> Nous utilisons le View Binding dans cette recette. Vous pouvez vous référer au document 01 - View Binding pour l'ajouter au projet.

> Attention : le terme "méthode" est utilisé dans 2 contextes différents dans ce document. Il peut être question de méthodes java, qui servent à encapsuler un bout de code et à être appelé par d'autre code java. Il peut aussi être question de méthodes HTTP, qui définit le type d'action que l'appel HTTP exécute, soit habituellement GET, POST, PUT ou DELETE

### Dépendance

Nous devons ajouter la dépendance à Retrofit.

`build.gradle`

```groovy
dependencies {
    implementation group: 'com.squareup.retrofit2', name: 'retrofit', version: '2.9.0'
    implementation group: 'com.squareup.retrofit2', name: 'converter-gson', version: '2.9.0'
    implementation group: 'com.squareup.retrofit2', name: 'converter-scalars', version: '2.9.0'
    // Autre dépendances ...
}
```

### Permissions

Par défaut, une application Android ne peut pas communiquer avec l'internet. Il faut lui donner la permissions dans le manifeste de l'application.

`AndroidManifest.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- Autres configurations ... -->
</manifest>
```

### Obtenir de l'information d'un web service simple

> Par simple, on entend un type scalaire, soit un entier, une chaîne de caractères ou un nombre flottant.

#### `Service`

Chaque méthode définie dans l'interface représente un appel HTTP. Avec seulement l'interface définie, Retrofit va être en mesure de formater la requête HTTP, l'envoyer, obtenir la réponse à la requête et formater la réponse. Il y a beaucoup de magie 🪄. À l'appel de cette méthode, Retrofit s'occupera de générer la requête.

On peut décomposer le code d'une méthode comme suis:

- `@GET("")` : L'annotation indique à Retrofit que la méthode définit un appel HTTP. La chaîne de caractère en paramètre est le chemin sur le serveur vers la ressource à laquelle on tente d'accéder. Les bouts entre accolades sont dynamique. Ex : pour `exos/{bonjour}`, `{bonjour}` peut prendre n'importe quelle valeur, comme `exos/{bonjour}
- `Call<X>` : On s'attend à recevoir une réponse de type X du serveur
- `longDouble` : nom de la méthode, à la discrétion du développeur, ne change rien pour Retrofit
- `@Path("nombre")` : La partie entre accolades sera remplacée par le paramètre ayant le même nom. Par exemple, si on peut lire l'annotation comme suit : `@GET("exos/")`

`Service.java`

```java
public interface Service {
    @GET("exos/long/double/{nombre}")
    Call<Integer> longDouble(@Path("nombre") int nombre);
}
```

Si la méthode est appelée comme suit : `longDouble(23)`, la requête générée au format HTTP sera donc `"https://4n6.azurewebsites.net/exos/long/double/23"`. Remarquez que 23 a remplacé {nombre} dans la requête.

#### `RetrofitUtil`

Afin d'utiliser Retrofit à partir de n'importe où dans le code, nous devons définir une méthode statique qui y donne accès.

Deux des méthodes chainées sont particulièrement importantes ici :

- `addConverterFactory` : Permettre à Retrofit de convertir des types scalaires, soit tout les types primitifs en java. Ex : int, float, long, etc.
- `baseUrl` : Url de base du service avec lequel l'application tente de communiquer.

`RetrofitUtil.java`

```java
public class RetrofitUtil {
    public static Service get() {
        Retrofit retrofit = new Retrofit.Builder()
                .addConverterFactory(ScalarsConverterFactory.create())
                .baseUrl("https://4n6.azurewebsites.net/")
                .build();
        return retrofit.create(Service.class);
    }
}
```

> Bonus : Afin de permettre au développeur d'appeller des méthodes les unes à la suite des autres, Retrofit utilise ici le patron de conception "Désignation chaînée" ou "_Fluent interface_" en anglais.

#### Appeler le service dans les tests

Appeller le service dans les tests permet de constater rapidement si le service fonctionne correctement. Cela ne constitue probablement pas un test unitaire / de régression rigoureux de l'application tel que vu dans le cours de programmation 3.

`ExampleInstrumentedTest.java`

```java
@Test
public void monTest() throws IOException {
    Service service = RetrofitUtil.get();
    Call<Integer> call = service.longDouble(12);
    Response<Integer> response = call.execute();
    Integer nombre = response.body();
    Log.i("RETROFIT", nombre.toString());
}
```

#### Appeler le service dans l'activité

Maintenant que le service est configuré, et qu'il est accessible, il est prêt à être utilisé.

`MainActivity.java`

```java
@Override
protected void onCreate(Bundle savedInstanceState) {

  // Reste du code

  Service service = RetrofitUtil.get();

  binding.btnGo.setOnClickListener(view -> {
      int nombre = Integer.parseInt(binding.etValeur.getText().toString());

      service.longDouble(nombre).enqueue(new Callback<Integer>() {
          @Override
          public void onResponse(Call<Integer> call, Response<Integer> response) {
              binding.tvRes.setText(response.body().toString());
          }

          @Override
          public void onFailure(Call<Integer> call, Throwable t) {
              Toast.makeText(MainActivity.this, "Une erreur est survenue en communicant avec le serveur.", Toast.LENGTH_SHORT).show();
          }
      });
  });
}
```

### Obtenir de l'information d'un web service complexe (GitHub)

> Par complexe, on sous-entend que la réponse du serveur est un objet JSON plus complexe qu'un simple nombre.

La base pour communiquer avec un serveur HTTP a été implémentée dans l'[étape précédente](#obtenir-de-linformation-dun-web-service-simple). Nous allons ajouter le code pour être en mesure de communiquer avec le webservice de GitHub.

L'application devra communiquer avec l'adresse suivante : [https://api.github.com/users/departement-info-cem/repos](https://api.github.com/users/departement-info-cem/repos). Ce webservice donne accès aux repository publiques d'un utilisateur donnée, en l'occurence, l'utilisateur departement-info-cem.

#### Classe Repo

En naviguant vers le [lien donné](https://api.github.com/users/departement-info-cem/repos), on se rend compte de quelques points :

- Les crochets (`[]`) au début et à la fin de la page démontrent que le webservice nous fournit une liste d'objets.
- Beaucoup d'information est disponible. Il va falloir décider de quels champs doivent être récupérés plutôt que tous. 

Nous allons récupérer les champs suivants :

- `name` : Nom du repository
- `description` : Description du repository, s'il en existe une
- `html_url` : Lien vers la page web du répertoire

Afin d'être en mesure d'utiliser ces informations, une classe Java doit être définit. Les champs en Java doivent porter **exactement** le même nom que ceux fournis par le webservice.

`Repo.java`

```java
public class Repo {
    public String name;
    public String description;
    public String html_url;
}
```

#### Service

Une classe de service spécifique au service GitHub peut être créée, avec la méthode qui permet d'accéder au webservice qui nous intéresse.

`GitHubService.java`

```java
public interface Service {
    @GET("users/{user}/repos")
    Call<List<Repo>> getRepos(@Path("user") String user);
}
```

Remqarquez ici que le type de retour est maintenant une liste de Repo.

#### `RetrofitUtil`

Une méthode statique a précédemment été définie pour communiquer avec un autre serveur que celui spécifié. Une autre méthode similaire va être définie pour définir l'accès à `api.github.com`.

`RetrofitUtil.java`

```java
public class RetrofitUtil {
    // Autre méthdoe

    public static Service getGitHub() {
        Retrofit retrofit = new Retrofit.Builder()
                .addConverterFactory(GsonConverterFactory.create())
                .baseUrl("https://api.github.com/")
                .build();
        return retrofit.create(GitHubService.class);
    }
}
```

Quelques différences :

- `addConverterFactory` contient maintenant `GsonConverterFactory.create()`, qui permet à Retrofit de traiter des types complexes.
- `baseUrl` qui pointe maintenant vers le serveur du webservice de GitHub.

Le service est maintenant prêt à être utilisé dans un test ou dans une application Android.

#### Appeler le service GitHub dans les tests

`ExampleInstrumentedTest.java`

```java
@Test
public void monTest2() throws IOException {
    Service service = RetrofitUtil.getGitHub();
    Call<List<Repo>> call = service.getRepos("departement-info-cem");
    Response<List<Repo>> response = call.execute();
    Repo repo = response.body();
    Log.i("RETROFIT", repo.name.toString());
}
```

#### Appeler le service GitHub dans l'activité

`MainActivity.java`

```java
@Override
protected void onCreate(Bundle savedInstanceState) {

  // Reste du code

  Service service = RetrofitUtil.getGitHub();

  binding.btnGo.setOnClickListener(view -> {
      String utilisateur = binding.etValeur.getText().toString();
      service.getRepos(utilisateur).enqueue(new Callback<List<Repo>>() {
          @Override
          public void onResponse(Call<List<Repo>> call, Response<List<Repo>> response) {
              binding.tvRes.setText(response.body().name);
          }

          @Override
          public void onFailure(Call<List<Repo>> call, Throwable t) {
              Toast.makeText(MainActivity.this, "Une erreur est survenue en communicant avec le serveur.", Toast.LENGTH_SHORT).show();
          }
      });
  });
}
```

## Pourquoi `execute` dans les tests et `enqueue` dans une activité?

`execute` et `enqueue` permettent tout les 2 d'effectuer un appel HTTP définit dans un service. Quand devrait on utiliser l'un ou l'autre?

`execute` effectue l'appel de manière **synchrone**, ce qui signifie que l'exécution du programme sera bloquée tant qu'il n'aura pas obtenu de réponse du serveur avec lequel il tente de communiquer.

Par exemple, si on reprend l'exemple donné plus haut dans les tests :

```java
@Test
public void monTest() throws IOException {
    Service service = RetrofitUtil.get();         // 1
    Call<Integer> call = service.longDouble(12);  // 2
    Response<Integer> response = call.execute();  // 3
    Integer nombre = response.body();             // 4
    Log.i("RETROFIT", nombre.toString());         // 5
}
```

L'appel HTTP se fait à la ligne 3. Puisque la ressource à laquelle on tente d'accéder peut être sur un serveur lointant, et que la vitesse de réponse dépend aussi de la qualité de la connexion à l'internet de l'appareil qui exécute l'appel, le temps avant d'obtenir une réponse est indéterminé.

Si on suppose que l'appel HTTP de la ligne 3 prend 10 secondes avant de se compléter, 10 secondes seront nécessaires pour passer de la ligne 3 à la ligne 4.

La méthode `enqueue` est **asynchrone**, ce qui signifie que l'exécution du programme ne sera pas interrompue lors de l'exécution de l'appel HTTP.

Effet, dans l'exemple suivant, les lignes 1, 2, 3 et 6 seront exécutées de manière quasi instantané. Comment est-ce possible?

```java
Service service = RetrofitUtil.get();                                 // 1
int nombre = Integer.parseInt(binding.etValeur.getText().toString()); // 2
service.longDouble(nombre).enqueue(new Callback<Integer>() {          // 3
    @Override
    public void onResponse(@NonNull Call<Integer> call, @NonNull Response<Integer> response) {
        binding.tvRes.setText(response.body().toString());            // 4
    }
    @Override
    public void onFailure(@NonNull Call<Integer> call, @NonNull Throwable t) {
        Toast.makeText(MainActivity.this, "Une erreur est survenue en communicant avec le serveur.", Toast.LENGTH_SHORT).show();                               // 5
    }
});
Log.i("TESTY", "BONJOUR");                                           // 6
```

La ligne 3 déclenche la requête HTTP sur un nouveau **fil d'exécution**, ce qui fait en sorte que la requête HTTP et le reste du code vont s'exécuter en parralèle.

On ne peut pas utiliser `execute` sur une activité puisque l'entieretée de l'interface serait bloquée (impossible d'interragir avec) tant et aussi longtemps que la requête HTTP ne s'est pas terminée, ce qui peut prendre plusieurs secondes.

## Ajout d'un intercepteur (déboguer)

Un intercepteur permet de comprendre rapidement comment s'est déroulé un appel HTTP en voyant comment s'est déroulé l'envoie et la réception de l'appel. Il peut aussi être utile pour modifier une requête entrante ou sortante.

### Dépendance

L'intercepteur nécessite l'ajout d'une dépendance supplémentaire.

`build.gradle`

```groovy
dependencies {
    implementation group: 'com.squareup.okhttp3', name: 'logging-interceptor', version: '4.10.0'
    ...
}
```

### Mise en place

Dans la classe utilitaire, on ajoute une méthode qui définit l'intercepteur.

`RetrofitUtil.java`

```java
public class RetrofitUtil {

    // Reste du code

    private static OkHttpClient client() {
        HttpLoggingInterceptor interceptor = new HttpLoggingInterceptor();
        interceptor.setLevel(HttpLoggingInterceptor.Level.BODY);
        OkHttpClient client = new OkHttpClient.Builder().addInterceptor(interceptor).build();
        return client;
    }
}
```

> En changeant la constante fournie dans `setLevel`, on peut changer le niveau de granularité de ce qui est filtré par l'intercepteur.

On ajoute ensuite l'intercepteur à la méthode qui retourne le service.

`RetrofitUtil.java`

```java
public class RetrofitUtil {
    public static Service get(){
        Retrofit retrofit = new Retrofit.Builder()
                .addConverterFactory(ScalarsConverterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .client(client()) // Ligne ajoutée
                .baseUrl("https://api.github.com/")
                .build();

        Service service = retrofit.create(Service.class);
        return service;
    }

    // Reste du code
}
```

## Erreurs fréquentes

### android.os.NetworkOnMainThreadException

Vous tentez d'utiliser votre service avec `execute` plutôt qu'avec `enqueue` dans une activité.

Référez vous à l'[étape suivante](#pourquoi-execute-dans-les-tests-et-enqueue-dans-une-activité)

### Permission denied (missing INTERNET permission?)

Vous avez oublié de donner la permission à l'application de communiquer avec l'internet.

Référez vous à l'[étape suivante](#permissions).


## Références

- [HTTP sur Wikipédia](https://fr.wikipedia.org/wiki/Hypertext_Transfer_Protocol)
- [Documentation Retrofit](https://square.github.io/retrofit/)
- [Vidéo 1 sur Retrofit](https://www.youtube.com/watch?v=wsF3t33jN8Y)
- [Vidéo 2 sur Retrofit](https://www.youtube.com/watch?v=rs8aSam5FT8)
- [Vidéo 3 sur Retrofit](https://www.youtube.com/watch?v=CQo8CPBvlvw)
- [Vidéo 4 sur Retrofit](https://www.youtube.com/watch?v=DbB5Ja33jic)
