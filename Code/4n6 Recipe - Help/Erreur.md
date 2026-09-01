# affichage 'ERROR' !
``` java
package org.deguet.erreurs;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.widget.Toast;
import com.google.android.material.dialog.MaterialAlertDialogBuilder;
import com.google.android.material.snackbar.Snackbar;

import org.deguet.erreurs.databinding.ActivityMainBinding;

// TODO snackbar with retry button
// TODO is action to retry is obvious > no need

// TODO ajouter un dialog pour

public class MainActivity extends AppCompatActivity {

    ActivityMainBinding binding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());

        // Messages d'erreur localisés sur un champ
        // validation d'un champ, explication d'un format etc.
        binding.envoyer1.setOnClickListener(view -> { showMessageSurChamp();});
        binding.envoyer2.setOnClickListener(view -> { showASnackBar();});

        // Messages d'erreur qui ne correspondent pas à un endroit particulier
        // erreur d'accès réseau ou serveur, pas les autorisations etc.
        binding.envoyer3.setOnClickListener(view -> { showADialog();});
        binding.fab.setOnClickListener(view -> { showASnack2();});

    }

    private void showASnack2() {
        Snackbar snacky = Snackbar.make(binding.coordinator,
                //<string name="no_network">Pas de réseau</string>
                R.string.no_network, Snackbar.LENGTH_LONG);
        //<string name="retry">Retry</string>
        snacky.setAction(R.string.retry, view1 -> {
            Toast.makeText(this, "Retrying", Toast.LENGTH_SHORT).show();
        });
        snacky.show();
    }

    private void showADialog() {
        MaterialAlertDialogBuilder builder = new MaterialAlertDialogBuilder(this);
        //<string name="no_network">Pas de réseau</string>
        builder.setTitle(R.string.no_network);
        //<string name="ok">Ok</string>
        builder.setPositiveButton(R.string.ok, (dialogInterface, i) -> {
            dialogInterface.dismiss();
        });
        builder.setNeutralButton(R.string.retry, (dialogInterface, i) -> {
            Toast.makeText(this, "Retrying", Toast.LENGTH_SHORT).show();
            dialogInterface.dismiss();
        });
        builder.show();
    }

    private void showASnackBar(){
        // On peut combiner le fait de marquer un message d'erreur avec le surlignage
        // des erreurs dans le formulaire

        //<string name="error_example">le message doit clairement indiquer comment résoudre la situation</string>
        binding.textInputLayout.setError(getString(R.string.error_example));

        // On peut mettre une action qui nous amène à l'erreur
        Snackbar snacky = Snackbar.make(binding.coordinator,
                //<string name="snack_message">Le snack bar mieux que Toast?</string>
                R.string.snack_message, Snackbar.LENGTH_LONG);
        //<string name="go_there">Voir</string>
        snacky.setAction(R.string.go_there, view1 -> { 
            binding.textInputLayout.requestFocus();
        });
        snacky.show();
    }

    private void showMessageSurChamp(){
        // si le message d'erreur sur un champ texte est en dehors de la zone
        // visible, il faut demander le focus pour obliger le scroll à y aller
        binding.password.setError(getString(R.string.error_password));
        //<string name="error_password">Erreur ou indice ancienne façon</string>
        binding.password.requestFocus();
        
    }
}

```

# 'ERROR' Retrofit !

``` java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // appeler un service de liste et afficher dans le log
        final Service service = RetrofitUtil.get();

        // appeler un service et mettre l'interface graphique
        final EditText et = findViewById(R.id.et);
        final Button btn = findViewById(R.id.btn);
        final TextView tv = findViewById(R.id.tv);
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                String nom = et.getText().toString();
                Repo repo = new Repo();
                repo.nom = nom;
                service.erreurOuPas(repo).enqueue(new Callback<String>() {
                    @Override
                    public void onResponse(Call<String> call, Response<String> response) {
                        if (response.isSuccessful()) {
                            tv.setText(response.body());
                        } else {
                            // ERROR ERROR ERROR
                            try {
                                String corpsErreur = response.errorBody().string();
                                Log.i("RETROFIT", "le code " + response.code());
                                Log.i("RETROFIT", "le message " + response.message());
                                Log.i("RETROFIT", "le corps " + corpsErreur);
                                if (corpsErreur.contains("TropCourt")) {
                                    // TODO remplacer par un objet graphique mieux qu'un toast
                                    Toast.makeText(MainActivity.this, "Ce message est trop court", Toast.LENGTH_SHORT).show();
                                }
                            } catch (IOException e) {
                                e.printStackTrace();
                            }
                        }
                    }

                    @Override
                    public void onFailure(Call<String> call, Throwable t) {
                        // TODO ici aussi il va falloir avoir un message d'erreur
                        int a = 0;
                    }
                });
            }
        });
    }
}
```
