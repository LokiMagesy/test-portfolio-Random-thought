# Intent

Un **intent** permet à une partie d'une application de signifier une "intention" à une autre.

Dans le cours d'application mobiles, nous utilisons les **intent** pour démarrer une nouvelle activité.

## Démarrer une activité

- Nous somme dans l'activité `MainActivity`
- À l'appuie d'un bouton on souhaite démarrer l'activité `AutreActivity`

`MainActivity.java`

```java
binding.monBouton.setOnClickListener(v -> {
    Intent autreIntent = new Intent(MainActivity.this, AutreActivity.class);
    startActivity(autreIntent);
});
```

## Démarrer une activité en lui passant de l'information

- Nous somme dans l'activité `MainActivity`
- Un champ texte permet d'entrer son animal préféré
- À l'appuie d'un bouton on souhaite démarrer l'activité `AutreActivity`
- Le nom de l'animal préféré qui provient de `MainActivity` doit apparaître sur une étiquette dans `AutreActivity`

`MainActivity.java`

```java
binding.monBouton.setOnClickListener(v -> {
    Intent intent = new Intent(this, AutreActivity.class);
    intent.putExtra("ANIMAL", binding.monChampTexte.getText().toString());
    startActivity(intent);
});
```

`AutreActivity`

```java
// TODO Ajouter le code dans le onCreate pour obtenir l'information qui provient de `MainActivity`
// TODO Ajouter le code pour afficher l'information récupérée dans une étiquette nommée "monAnimal"
if (intent.hasExtra("ANIMAL")) {
    String animal = intent.getStringExtra("ANIMAL");
    binding.monAnimal.setText(animal);
}
```

## Références

- [Vidéo du cours](https://www.youtube.com/watch?v=Zu2pd6vOxMM)
- [Geeks for geeks](https://www.geeksforgeeks.org/how-to-use-putextra-and-getextra-for-string-data-in-android/)
- [Documentation Android](https://developer.android.com/reference/android/content/Intent)
