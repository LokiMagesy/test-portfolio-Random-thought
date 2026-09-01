# Introduction à Angular
Angular UI Libraries

Angular Material 
ngx-bootstrap
Clarity Angular 
https://akveo.github.io/nebular/docs/getting-started/what-is-nebular#what-is-nebular 
https://uibakery.io/ 

https://flowbite.com/docs/components/buttons/ 
https://markodenic.com/tools/  

https://www.quackit.com/html/templates/css_flexbox_templates.cfm 


### Angular vs les autes frameworks front end
#### Des avantages ✅
 Utilise TypeScript (JavaScript avec des outils supplémentaires)
 « Two-way Data Binding » : on peut changer le « Modèle 💾 » via l’interface graphique.
 Supporte complètement l’injection de dépendances.
 Constitué de plusieurs sous-librairies « built-in » qui permettent de faire des grandes applications complètes et structurées.
 Avec certains autres frameworks, il faut souvent se doter de librairies supplémentaires.
 Architecture MVC. Plus intuitif pour vous.
 #### Désavantages 😕
 Parfois moins performant. (Mais toujours mieux qu’un Framework Back End pour rendre l’affichage dynamique)
 Plus long à apprendre. (Car plus complet / sophistiqué)

### Exemple : 
`npm` (Node Package Manager) Gestionnaire de dépendances

tapez la commande (Terminal) `npm install -g @angular/cli` `npm uninstall bootstrap` `npm install` `ng new NomDeVotreProjet`  `ng serve -o` (Powershell)


``` javascript
// (:string) Type du paremètre => m
function mything(m:string): void { // Type retourné par Mything => void
  alert(m);
  console.log(m);
}
```

``` typescript
<p>{{PraiseTheSun}}</p>

export class Appcomponent {

    PraiseTheSun = "\[T]/"  // Propriété
    FallenSun:string; // Propriété

    Soul:Number= 69; //number=int

    Oath: boolean | null; // Also, Undefined
    Oath?: boolean; // ? or !

    console.log("Hello World!")
    // Constructeur
    constructor(public sun:string){ 
        this.Fallensun = "X(";
    }
    //Méthode
    kill():void{
      if(this.imposter){
        console.log(this.PraiseTheSun + "something")
      }else{
        console.log(this.FallenSun + "something")
      }
      this.Oath = True;
    }

}

class hollow{
    constructor(public thing:string, public bonfire:boolean){}
}

```
index.html (Selector) 
``` javascript
<body>
    <app-root></app-root>
</body>
```


``` typescript
<h3></h3>
<ul>
    <li *ngFor="let n of number">{{n}}</li>
    <span *ngIf="n.thing == true">{{n.Thing2}}</span>
</ul>
<div (click)="raiseValue()">+ 1</div>
(dbclick) (mouseover)
<form (ngSumbit)="AddNumber()">
    <input type="number" [(ngModel)]="Thing">
</from>

<div (click)="alert(thing)"></div>
export class Appcomponent {
    alert(n:number): void{
        alert(n);
    }
}

```

# Semaine: 2 
``` html
<div class="container boite">
  
  <div class="row">
    <div class="col-3 gauche">{{cheapWisdom}}</div>
    <div class="col-9 droite">{{n}}</div>
  </div>

  <div class="row mt-1">
    <div class="col-3 gauche">
      <p>Nom : {{child.name}}</p>
      <p>Âge : {{child.age}}</p>
      <p>Résultat de goToSchool() : {{child.goToSchool()}}</p>
    </div>
    <div class="col-9 droite">
      <ul>
        <li *ngFor="let h of hateList">{{h}}</li>
      </ul>
    </div>
  </div>

  <div class="row mt-1">
    <div class="col-3 gauche">
      <form (ngSubmit)="addNFT()">
        Description : <input class="form-control" type="text" name="desc" [(ngModel)]="desc"><br>
        Prix : <input class="form-control" type="number" name="price" [(ngModel)]="price"><br>
        <input class="btn btn-light" type="submit" value="Ajouter un NFT">
      </form>
      <button class="btn btn-light my-2" (click)="removeNFT()">Retirer le dernier NFT</button>
    </div>
    <div class="col-9 droite">
      <p *ngFor="let n of nfts">{{n.desc}} : {{n.price}} <span *ngIf="n.price > 100">(Arnaque)</span></p>
    </div>
  </div>

</div>

```
``` javascript
export class AppComponent {
  cheapWisdom = "Aide quelqu'un et il se souviendra de toi quand il aura besoin d'aide à nouveau.";
  n : number;
  hateList = ["Publicités Dodge Ram", "NFTs", "Enseigner à des étudiants en informatique"];
  child = new Child("Karl-Benjamin");
  nfts = [new NFT("Personnage d'anime banal", 150), new NFT("Bébé Girafe", 30), new NFT("Crotte de fromage", 15)];
  desc ?: string;
  price ?: number;

  constructor(){
    this.n = 5;
  }

  addNFT():void{
    if(this.desc != undefined && this.price != undefined){
      this.nfts.push(new NFT(this.desc, this.price));
    }
  }

  removeNFT():void{
    this.nfts.pop();
  }
}

class Child{
  age = 8;

  constructor(public name : string){}

  goToSchool() : string{
    return "Oups ! Il y a eu une éclosion à l'école. " + this.name + " doit revenir à la maison pour 2 semaines.";
  }
}

class NFT{
  constructor(public desc : string, public price : number){}
}

```

``` html
<div class="container-xxl">
  <header class="row align-items-center">
    <div class="col-1 mx-0 px-0"><img class="img-fluid" src="../assets/images/note.png" alt="Music note"></div>
    <div class="col"><h1>Requêtes musicales</h1></div>
  </header>
  <div class="row">
    <div class="col-3">
      <form (ngSubmit)="searchArtist()" *ngIf="!result">
        <input type="text" name="artistName" [(ngModel)]="artist" class="form-control" placeholder="Nom d'un artiste">
        <input type="submit" value="Rechercher l'artiste" class="btn btn-light">
      </form>
      <form (ngSubmit)="newSearch()" *ngIf="result">
        <input type="submit" value="Nouvelle recherche">
      </form>
    </div>
    <div class="col artists">
        <h2>Résultats</h2>
        <div *ngIf="result">
          <h3>Artistes similaires à {{artist}}</h3>
          <ul>
            <li *ngFor="let a of similarArtists">{{a}}</li>
          </ul>
        </div>
    </div>
  </div>
</div>
```

``` javascript
export class AppComponent {
  
  result = false;

  artist : string = "";
  similarArtists : string[] = [];

  constructor(public httpClient : HttpClient){}

  async searchArtist():Promise<void>{
    this.result = true;

    let x = await lastValueFrom(this.httpClient.get<any>("http://ws.audioscrobbler.com/2.0/?method=artist.getsimilar&artist=" + this.artist + "&api_key=e34ebf8561ba7c653a21d1d99a1a0070&format=json"));
    console.log(x);
    for(let i = 0; i < x.similarartists.artist.length; i++){
      this.similarArtists.push(x.similarartists.artist[i].name);
    }
  }

  newSearch():void{
    this.result = false;
  }
}
```

# Semaine: 3 Components & Routage

Commande à faire dans le Temrinal VS Code

## Service
### option 1 : Mode vire intrépide
Taper la commande 
```
ng generate service nom_service
```
Cela crée 2 nouveaux fichiers dans le dossier de l’application :
`data.service.spec.ts` & `data.service.ts` 

### option 2 : Utilisier l'extension Angular Files
Même les meilleurs ont besoin d’un coup de pouce 😏

Clic-droit sur le dossier où l’on souhaite créer un service

Choisir « Generate Service ».  Le résultat est le même : on a nos 2 nouveaux fichiers.

`<div class="{{maVariable != null ? 'col-12' : 'col-3'}}">Something</div>`

