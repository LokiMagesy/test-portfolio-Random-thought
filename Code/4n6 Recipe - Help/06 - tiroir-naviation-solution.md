# Tiroir de Navigation (Navigation Drawer)

Un tiroir de navigation permet à l'utilisateur d'accéder rapidement à des fonctionnalités de l'application en encombrant minimalement l'interface visuelle.

## Mise en place

### Dépendances

Le tiroir de navigation n'est pas disponible par défaut dans Android, il faut ajouter les librairies qui y correspondent.

`build.gradle`

```groovy
dependencies {
    implementation 'androidx.legacy:legacy-support-v4:1.0.0'
    implementation 'com.google.android.material:material:1.8.0'
    ...
}
```

### Ajout du tiroir dans l'activitié

Rappel des différentes balises :

- `DrawerLayout` : Pour ajouter un tiroir, la balise à ajouter doit englober l'ensemble de ce qui se retrouvera dans l'activité. Notez que le `DrawerLayout` ne définit pas ce qui se retrouve dans le tiroir de navigation, il s'agit davantage d'un conteneur.
- `CoordinatorLayout` : Coordoner les comportements de différents éléments de niveau supérieur. Dans le contexte présent, c'est le conteneur de ce qui doit être affiché dans l'activité.
- `NavigationView` : Définit le contenu du tiroir de navigation. Le attributs suivants du `NavigationView` sont particulièrement importants :
  - `app:menu` : Définit le menu à afficher dans la partie principale du tiroir de navigation. La valeur associée devrait faire référence à un fichier XML situé dans `res/menu`, semblable à celui vu dans un ActionBar.
  - `app:headerLayout` : Définit l'apparence de l'en-tête du navigation layout. La valeur associée devrait faire référnece à un fichier XML situé dans `res/layout`. Si cet attribut est omis, il n'y aura simplement pas d'en-tête sur le tiroir de navigation.

`main_activity.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.drawerlayout.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:openDrawer="start">

    <androidx.coordinatorlayout.widget.CoordinatorLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello World!" />
    </androidx.coordinatorlayout.widget.CoordinatorLayout>

    <com.google.android.material.navigation.NavigationView
        android:id="@+id/nav_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:menu="@menu/navigation_drawer"
        app:headerLayout="@layout/header_layout"/>
</androidx.drawerlayout.widget.DrawerLayout>
```

> Info : À ce stade, si vous n'avez pas encore lié votre XML à votre code Java, rien ne devrait apparaitre.
> Truc : Donner des couleurs d'arrière-plan aux différentes composantes pour s'assurer que chaque élément apparait comme prévu.
> Attention : Votre application ne compilera pas si les attributs du `NavigationView`, `app:menu` et / ou `app:headerLayout` ont été définis mais que les fichier sur lesquels ils pointent sont innexistants. Vous trouverez comment définir ces fichiers plus bas dans ce document. En attendant, vous pouvez omettre ces options pour tester votre application.

### Définition du menu

Tel que mentionné à l'étape précédente le menu à afficher, qui est référé dans l'attribut `app:menu` de `NavigationView` doit être définit dans `res/menu`.

`navigation_drawer.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/nav_accueil"
        android:title="Accueil" />
    <item
        android:id="@+id/nav_profil"
        android:title="Profil" />
    <item
        android:id="@+id/nav_deconnexion"
        android:title="Déconnexion" />
</menu>
```

> Info : À ce stade, si vous n'avez pas encore lié votre XML à votre code Java, rien ne devrait apparaitre.

### Lier le XML du tiroir au code Java

Pour simplement afficher le tiroir de navigation, simplement faire comme avec n'importe quelle activité. Il ny a donc rien de spécifique au tiroir de navigation pour le moment.

`MainActivity.java`

```java
public class MainActivity extends AppCompatActivity {

    private ActivityMainBinding binding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());
    }
}
```

### Réagir aux boutons de menu du tiroir de navigation

Une référence au NavigationView est nécessaire pour réagir à l'appuis sur les différentes options de navigation.

Une référence au Drawer layout est aussi nécessaire pour demander au tiroir de navigation de se fermer.

`MainActivity.java`

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    binding = ActivityMainBinding.inflate(getLayoutInflater());
    setContentView(binding.getRoot());

    NavigationView navigationBinding = binding.navView;
    DrawerLayout drawerLayout = binding.drawerLayout;

    navigationBinding.setNavigationItemSelectedListener(item -> {
        switch (item.getItemId()) {
            case R.id.nav_accueil:
                this.finishAffinity();
                break;
            case R.id.nav_profil:
                Toast.makeText(this, "MON NOM", Toast.LENGTH_SHORT).show();
                break;
            case R.id.nav_deconnexion:
                Log.i("MON LOG", "DÉCO");
                break;
        }
        drawerLayout.closeDrawers();
        return false;
    });
}
```

Les fonctionnalités minimales afin de faire fonctionner le tiroir de navigation ont maintenant été implémentées. Pour afficher le tiroir de navigation, vous pouvez glisser votre doigt à partir d'un rebord à gauche de l'écran, vers le centre de l'écran.

Les prochaines étapes servent à le rendre plus polyvalent, plus robuste aux changements dans l'interface et à ajouter un menu hamburger.

> Vous remarquerez que l'interaction avec les éléments de menu sont semblable à celles qui ont été vues dans le ActionBar.

### Ajout d'un en-tête au tiroir de navigation

L'en-tête permet de donner un style personnalisé à notre application, et donne le contrôle sur l'apparence du tiroir de navigation.

Une fois le XML de l'en-tête définit, il faut simplement s'assurer que l'attribut `app:headerLayout` est définit sur le `NavigationView` dans `activity_main.xml`.

`header_layout.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="#AA65FF"
        android:text="Mon super en-tête"
        android:textSize="30sp" />
</LinearLayout>
```

### Menu hamburger

Dans l'état actuel de l'application, il est uniquement possible d'afficher le tiroir de navigation en balayant son doigt de gauche à droite.

Nous allons ajouter bouton à gauche de l'ActionBar afin d'afficher ou de cacher le tiroir de navigation.

`MainActivity.java`

```java
public class MainActivity extends AppCompatActivity {

    private ActivityMainBinding binding;
    
    // Mis dans un variable d'instance pour être réutilisé plus tard
    private ActionBarDrawerToggle actionBarToggle;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Affiche le bouton, mais aucun comportement n'est encore définit
        getSupportActionBar().setDisplayHomeAsUpEnabled(true);

        // Créer l'objet qui définit le comportement du bouton
        actionBarToggle = new ActionBarDrawerToggle(this, drawerLayout, R.string.dopen, R.string.dclose){

            // Optionnel : définit un comportement lorsque le tiroir est ouvert
            @Override
            public void onDrawerOpened(View drawerView) {
                super.onDrawerOpened(drawerView);
                getSupportActionBar().setTitle(R.string.dopen);
                Toast.makeText(MainActivity.this, "Ouvert!", Toast.LENGTH_SHORT).show();
            }

            // Optionnel : définit un comportement lorsque le tiroir est fermé
            @Override
            public void onDrawerClosed(View drawerView) {
                super.onDrawerClosed(drawerView);
                getSupportActionBar().setTitle(R.string.dclose);
                Toast.makeText(MainActivity.this, "Fermé!", Toast.LENGTH_SHORT).show();
            }
        };

        // Ajouter le comportement définit au bouton
        drawerLayout.addDrawerListener(actionBarToggle);
        
        // Synchronise avec l'état du tiroir : fonctionne même si le tiroir a été ouvert / fermé par le geste de gauche à droite
        actionBarToggle.syncState();
        
        // Reste du code définit précédement
    }

    // Ouvrir / fermer le menu au clic sur le bouton hamburger
    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        if (actionBarToggle.onOptionsItemSelected(item)){
            return true;
        }
        return super.onOptionsItemSelected(item);
    }

    // Assurer le fonctionnement lorsque l'appareil effectue une rotation
    @Override
    protected void onPostCreate(@Nullable Bundle savedInstanceState) {
        super.onPostCreate(savedInstanceState);
        actionBarToggle.syncState();
    }

    // Assurer le fonctionnement lorsque l'appareil effectue une rotation
    @Override
    public void onConfigurationChanged(@NonNull Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        actionBarToggle.onConfigurationChanged(newConfig);
    }
}
```

Deux string doivent être ajoutées dans les ressources (`res/values/strings.xml`). Ces chaînes de caractère servent à améliorer l'expérience des personnes mal voyantes qui utilisent votre application.

`strings.xml`

```xml
<resources>
    ...
    <string name="dopen">Drawer Open</string>
    <string name="dclose">Drawer Close</string>
</resources>
```

## Références

- [Vidéo du cours - Tiroir de navigation](https://www.youtube.com/watch?v=T2upKap9Jic)
- [Vidéo du cours - Menu hamburger](https://www.youtube.com/watch?v=W3EjsclJ6nQ)
