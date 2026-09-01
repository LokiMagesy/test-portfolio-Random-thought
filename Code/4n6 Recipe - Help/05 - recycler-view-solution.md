# Recycler View

Le **Recycler View** premet d'afficher une liste d'éléments dans votre interface graphique.

## Mise en place

Plusieurs étapes et composantes sont nécessaires pour mettre en place un **Recycler View**

### Ajout de la dépendance maven

Le Recycler View n'est pas disponible par défaut dans la librairie d'Android : il faut l'ajouter comme dépendance. La dernière version est disponible sur le site de la [documentation d'Android](https://developer.android.com/jetpack/androidx/releases/recyclerview?hl=fr#declaring_dependencies).

```groovy
dependencies {
    ...
    implementation "androidx.recyclerview:recyclerview:1.2.1"
    // For control over item selection of both touch and mouse driven selection
    implementation "androidx.recyclerview:recyclerview-selection:1.1.0"
    ...
}
```

### Ajout dans l'activité

Comme pour les autres éléments graphiques (bouton, champs texte, etc), le **Recycler View** doit être ajouté dans le fichier XML qui définit l'interface de chaque activité.

`activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/monRecycleur"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#FF00DD" />
    

</LinearLayout>
```

> Note : À ce point, c'est normal que le **Recycler View** ne s'affiche pas lorsqu'on exécute l'application sur un téléphone puisqu'il n'y a pas de encore d'éléments qui ont été ajoutés dans la liste. 

### Interface d'un élément

Jusqu'à présent, l'emplacement où la liste va apparaitre dans l'interface a été définit, mais il faut aussi définir à quoi ressemblera chacun des éléments de la liste.

Le fichier XML ci-dessous permet de définir à quoi resemble un élément de la liste.

Il doit être ajouté au même niveau qu'une activité, soit dans `app/src/main/res/layout`.

`mes_secrets.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:layout_marginBottom="30dp">

    <TextView
        android:id="@+id/txtNom"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="25sp" />

    <TextView
        android:id="@+id/txtDate"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="25sp" />

    <TextView
        android:id="@+id/txtNbr"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="25sp" />

</LinearLayout>
```

### Classe d'un item

Afin de contenir les données d'un item de liste à afficher et de les communiquer entre les différentes composantes, une classe doit être définie.

`Secret.java`

```java
public class Secret {
    public String nom;
    public LocalDateTime date;
    public Long nbGrand;
}

```

### Classe de l'adapteur

L'adaptateur gère chacun des éléments visuels de la liste qui se retrouvent dans l'interface. Il permet donc de lier le fichier xml définit plus haut (`mes_secrets.xml`) pour chaque élément à afficher avec un **RecyclerView**.

`SecretAdapter.java`

```java
public class SecretAdapter extends RecyclerView.Adapter<SecretAdapter.MyViewHolder> {

    /**
     * Liste qui contient l'ensemble des éléments à afficher dans la liste
     */
    public List<Secret> localDataSet;

    /**
     * Chaque élément à afficher est un MyViewHolder
     */
    public static class MyViewHolder extends RecyclerView.ViewHolder {

        private final TextView txtNom;
        private final TextView txtDate;
        private final TextView txtNbr;
        
        public MyViewHolder(SecretItemBinding binding) {
            super(binding.getRoot());
            txtNom = binding.txtNom;
            txtDate = binding.txtDate;
            txtNbr = binding.txtNbr;
        }

        public TextView getTxtNom() {
            return txtNom;
        }

        public TextView getTxtDate() {
            return txtDate;
        }

        public TextView getTxtNbr() {
            return txtNbr;
        }
    }

    /**
     * Initialiser la liste de données de l'adapteur
     */
    public SecretAdapter() {
        localDataSet = new ArrayList<>();
    }

    /**
     * Créer un élément de liste.
     * Attention : Cette méthode n'est pas appellée pour chaque élément de la liste,
     * mais seulement ceux qui sont visible à l'écran.
     * Android est assez intelligent pour détecter si un élément ne s'affiche plus à l'écran et
     * pour le remplacer par un autre (recycler).
     *
     * @param viewGroup The ViewGroup into which the new View will be added after it is bound to
     *                  an adapter position.
     * @return L'élément de liste créé
     */
    @NonNull
    @Override
    public MyViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int viewType) {
        SecretItemBinding binding = SecretItemBinding.inflate(LayoutInflater.from(viewGroup.getContext()), viewGroup, false);
        return new MyViewHolder(binding);
    }

    /**
     * Mettre du contenu dans un item de la liste
     *
     * @param myViewHolder View Holder créé dans onCreateViewHolder sur lequel on veut mettre du contenu
     * @param position     Position de l'item à mettre qui se trouve dans notre jeu de données
     */
    @Override
    public void onBindViewHolder(MyViewHolder myViewHolder, final int position) {
        myViewHolder.getTxtNom().setText(localDataSet.get(position).nom);
        myViewHolder.getTxtDate().setText(localDataSet.get(position).date.toString());
        myViewHolder.getTxtNbGrand().setText(String.format(localDataSet.get(position).nbGrand.toString()));
    }

    /**
     * Renvoie la taille de la liste
     *
     * @return Taille de la liste
     */
    @Override
    public int getItemCount() {
        return localDataSet.size();
    }

}
```

### Lier l'adaptateur à l'activité

Afin d'être en mesure de remplir notre **Recycler View**, du code doit être ajouté dans la classe Java de l'activité.

`MainActivity.java`

```java
public class MainActivity extends AppCompatActivity {

    private ActivityMainBinding binding;
    private SecretAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());

        initializeRecycler();
        remplirRecycler();
        remplacer();
    }

    /**
        * Obtenir une référence au Recycler View dans le layout et le connecter au SecretAdapter.
        */
    private void initialiserRecycler() {
        RecyclerView recyclerView = binding.recyclerView;

        // IMPORTANT
        // On dit à Android que le nombre d'élément ne va pas changer.
        // Évite de toujours recréer les éléments.
        recyclerView.setHasFixedSize(true);

        LinearLayoutManager layoutManager = new LinearLayoutManager(this);
        recyclerView.setLayoutManager(layoutManager);

        adapter = new SecretAdapter();
        recyclerView.setAdapter(adapter);
    }

    /**
    * Remplir le Recycler View créé avec 1000 éléments 
    */
    private void remplirRecycler() {
        LocalDateTime d = LocalDateTime.of(2000, 1, 1, 0, 0);
        for (int i = 0; i < 1000; i++) {
            Secret s = new Secret();
            s.nom = "Objet #" + (i + 1);
            s.date = d;
            d = d.plusDays(1);
            s.nbGrand = (long) i;
            adapter.localDataSet.add(s);
        }
        // Fonctionne mais moins optimal
        // adapter.notifyDataSetChanged();
        // Plus optimal :
        adapter.notifyItemRangeInserted(0, adapter.localDataSet.size());
    }

    /**
     * Remplacer les 1000 éléments précédement créés par 1000 autres.
     */
    private void remplacer() {
        // ATTENTION : Ne jamais créer de nouvelle liste dans l'adapteur (Ex : adapter.localDataSet = new ArrayList<>()
        // On risque de perdre le lien entre la liste et l'adapteur. Plutôt vider la liste et la repeupler.
        LocalDateTime d = LocalDateTime.of(2000, 1, 1, 0, 0);
        for (int i = 0; i < 1000; i++) {
            Secret s = new Secret();
            s.nom = "Nouvel Objet #" + (i + 1);
            s.date = d;
            d = d.plusDays(1);
            s.nbGrand = (long) i;
            adapter.localDataSet.set(i, s);
        }
        // Fonctionne mais moins optimal
        // adapter.notifyDataSetChanged();
        // Plus optimal :
        adapter.notifyItemRangeChanged(0, adapter.localDataSet.size());
    }
}
```

## Problèmes fréquents et comment les déboguer

Le meilleur moyen de détecter rapidement un problème est de tester aussi souvent que possible l'application développée. Il peut être difficile de le faire avec un Recycler View puisque plusieurs étapes sont nécessaires avant d'avoir un résultat. 

### Les éléments de la liste ne s'affichent pas

Il peut être complexe de trouver la source du problème puisque plusieurs éléments peuvent causer le problème.

1. Essayer d'exécuter l'application en mode debug, avec des points de débug dans les différentes méthodes de l'adapeteur
2. Si les points de débug dans l'adaptateur **ne sont pas** atteints, il est probable que le problème provienne du code qui configure l'adaptateur dans l'activité.
3. Si les points de débug dans l'adaptateur **sont** atteints, il est probable que le problème provienne du code xml qui définit l'interface graphique. 

### Tout s'affiche, mais l'interface est lente pour gérer un grand nombre d'éléments

Lors de l'initialisation du Recycler View (voir la méthode `initialiserRecycler` dans [cette étape](#lier-linterface-à-lactivité)), il faut ajouter cette ligne : `recyclerView.setHasFixedSize(true);`. 

Cela indique à Android qu'il n'est pas nécessaire de recalculer chaque éléments graphique lorsqu'on se déplace dans la liste.

## Pour quoi ça s'appelle un **Recycler View**?

- Supposons que notre appareil a la capacité d'afficher 6 éléments à la fois, comme sur l'image ci-dessous.
- Rappelons nous aussi que chaque élément graphique de la liste s'appellent un **ViewHolder**.

À chaque fois qu'un **ViewHolder** doit être rendu à l'écran, la méthode `onCreateViewHolder` est appellée pour créer l'interface graphique du **ViewHolder**. Elle devrait donc être appelée 6 fois. Notez que la méthode `onCreateViewHolder` est assez coûteuse au niveau des performances.

La méthode `onBindViewHolder` est ensuite appelée pour mettre l'information voulue les **ViewHolder**. Elle devrait donc être appelée 6 fois.

Il n'y a toujours que 6 **ViewHolder** qui sont affichés à la fois dans l'interface. L'idée est donc de réutiliser (Recycler) les différents éléments en mettant à jour les informations à afficher pour éviter de toujours supprimer / recréer des **ViewHolder**.

À mesure qu'on fait défiler les éléments, la méthode `onBindViewHolder` va être appelée plusieurs fois pour remplacer le contenu des éléments à afficher. Cependant, la méthode `onCreateViewHolder` qui créée les ViewHolder ne va être appelée que 6 fois.

> Truc : pour mieux comprendre exécuter votre application contenant le Recycler View, et regardez dans le Layout Inspector d'Android Studio. Vous verrez comment se comportent les éléments de la liste lorsqu'on fait défiler les différents éléments  

![Recycler View](images/05%20-%20Recycler.png)

## Références

- [Vidéo du cours 1/2 - layout, adapteur et initialisation](https://www.youtube.com/watch?v=nkGseYC3QAw)
- [Vidéo du cours 2/2 - fonctionnement et débogage](https://www.youtube.com/watch?v=gtHix80YUx0)
- [Documentation Android](https://developer.android.com/jetpack/androidx/releases/recyclerview?hl=fr)
- [Guide Android](https://developer.android.com/develop/ui/views/layout/recyclerview#java)