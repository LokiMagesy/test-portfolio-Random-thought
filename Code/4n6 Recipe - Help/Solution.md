# Lab 3
## app.component.html
``` html
<div id="container">
  <header>
    Traversée d'animaux
  </header>
  <hr>
  <nav>
    <!-- Le menu de navigation est meublé avec tous les [routerLink] nécessaires qui changent la route au besoin -->
    <a [routerLink]="['index']">Accueil</a>
    <a [routerLink]="['fish']">Poissons</a>
    <a [routerLink]="['bugs']">Bebites</a>
  </nav>
  <hr>
  <main>

    <!-- À CHANGER -->
    <!--<app-index></app-index>
    <hr>
    <app-fish></app-fish>
    <hr>
    <app-bugs></app-bugs>-->

    <!-- ↓↓↓ Pour seulement afficher le composant lié à la route actuelle, on doit mettre ↓↓↓ -->
    <router-outlet></router-outlet>

  </main>
  <hr>
  <footer>
    Tous droits réservés à Nintendo &copy;
  </footer>
</div>
```

## app.module.ts
``` javascript
@NgModule({
  declarations: [							
    AppComponent,
      FishComponent,
      BugsComponent,
      IndexComponent,
      SinglefishComponent,
      SinglebugComponent
   ],
  imports: [
    BrowserModule,
    HttpClientModule,
    RouterModule.forRoot([
      {path:"", redirectTo:"/index", pathMatch:"full"},
      {path:"index", component:IndexComponent},
      {path:"fish", component:FishComponent},
      {path:"bugs", component:BugsComponent},
      {path:"fish/:id", component:SinglefishComponent}
    ])
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## bug
``` html
<style>
    #bug-content{
    display:flex;
    margin:10px 0px;
    color:#3f8a69;
    font-family:helvetica;
    font-size:1.2em;
}

#bug-content>div:first-child{
    flex:1;
    padding:10px;
}

#bug-content>div:last-child{
    flex:2;
    display:flex;
    flex-wrap: wrap;
}

#bug-content>div:last-child>div{
    cursor:pointer;
    opacity:1;
}

#bug-content>div:last-child>div:hover{
    opacity:0.7;
}

.selected{
    background-image:linear-gradient(#99e7c4, #5ab78d);
}
</style>
<div id="bug-content">
  <div>
    <!-- Affichage de la bebite sélectionnée ici -->
    <app-singlebug [bug]="selectedBug"></app-singlebug>
  </div>
  <div>
    <div (click)="selectBug(bi)" *ngFor="let bi of bugIds" [class]="(selectedBug && selectedBug.id == bi) ? 'selected' : ''">
      <img [src]="'../../assets/images/bug' + bi + '.png'" [alt]="'Insecte #' + bi">
    </div>
  </div>
</div>
```
``` javascript
import { HttpClient } from '@angular/common/http';
import { Component, OnInit } from '@angular/core';
import { lastValueFrom } from 'rxjs';
import { Creature } from '../models/creature';
import { HttpService } from '../services/http.service';

@Component({
  selector: 'app-bugs',
  templateUrl: './bugs.component.html',
  styleUrls: ['./bugs.component.css']
})
export class BugsComponent implements OnInit {

  selectedBug ?: Creature;
  bugIds : number[] = [2,8,14,16,24,35,41,43,67,69];

  constructor(public httpService : HttpService) { }

  ngOnInit() {
  }

  async selectBug(id : number){
    this.selectedBug = await this.httpService.getBug(id.toString());
  }

}
```
## fish
``` html
<div id="fish-content">
  <div>
    <div *ngFor="let fi of dataService.fishIds" [routerLink]="['/fish', fi]"><!-- Cliquer sur ce div mène vers fish/:id -->
      <img [src]="'../../assets/images/fish' + fi + '.png'" [alt]="'Poisson #' + fi">
    </div>
  </div>
</div>
```
``` javascript
export class FishComponent implements OnInit {

  constructor(public dataService : DataService) { }

  ngOnInit() {
  }

}
```
## index
``` html
<div class="menu">
  <div [routerLink]="['/fish']"><!-- Ce div doit être cliquable et mener vers le composant Fish -->
    <div class="miniTitle">Poissons</div>
    <img *ngFor="let fi of dataService.fishIds" [src]="'../../assets/images/fish' + fi + '.png'" [alt]="'Fish #' + fi">
  </div>

  <div [routerLink]="['/bugs']"><!-- Ce div doit être cliquable et mener vers le composant Bugs -->
    <div class="miniTitle">Bebites</div>
    <img *ngFor="let bi of dataService.bugIds" [src]="'../../assets/images/bug' + bi + '.png'" [alt]="'Bug #' + bi">
  </div>
  
</div>
```
``` javascript
export class IndexComponent implements OnInit {

  constructor(public dataService : DataService) { }

  ngOnInit() {
  }

}
```
## models
``` javascript
export class Creature{
    constructor(
        public id : number, 
        public name : string, 
        public location : string, 
        public rarity : string, 
        public price : number,
        public icon : string){}
}
```
## services
### data.services.ts
``` javascript
export class DataService {

  bugIds : number[] = [2,8,14,16,24,35,41,43,67,69];
  fishIds : number[] = [1,2,40,49,50,51,55,56,60,67];

constructor() { }

}
```
### http.services.ts
``` javascript
export class HttpService {

  constructor(public httpClient : HttpClient) { }

  async getFish(fishId : string) : Promise<Creature>{

    let req = await lastValueFrom(this.httpClient.get<any>("http://acnhapi.com/v1/fish/" + fishId));
    console.log(req);
    
    return new Creature(req.id, req.name["name-USen"], req.availability.location, req.availability.rarity, req.price, req.icon_uri);

  }

  async getBug(bugId : string) : Promise<Creature>{
    let req = await lastValueFrom(this.httpClient.get<any>("http://acnhapi.com/v1/bugs/" + bugId));
    console.log(req);
    return new Creature(req.id, req.name["name-USen"], req.availability.location, req.availability.rarity, req.price, req.icon_uri);
  }

}
```
## singlebug
``` html
<div *ngIf="bug">
  <img [src]="bug.icon" [alt]="'Insecte #' + bug.id">
   <div>Bebite #{{bug.id}}</div>
   <div>Nom : {{bug.name}}</div>
   <div>Habitat : {{bug.location}}</div>
   <div>Rareté : {{bug.rarity}}</div>
   <div>Prix : {{bug.price}}</div>
</div>
<div *ngIf="!bug">
   <p>Sélectionnez une bebite...</p>
</div>

```
``` javascript
export class SinglebugComponent implements OnInit {

  @Input() bug ?: Creature;

  constructor() { }

  ngOnInit() {
  }

}
```
## singlefish
``` html
<style>
    
</style>
<div *ngIf="fish" class="card">
  <img [src]="fish.icon" [alt]="'Poisson #' + fish.id">
  <div>Poisson #{{fish.id}}</div>
  <div>Nom : {{fish.name}}</div>
  <div>Habitat : {{fish.location}}</div>
  <div>Rareté : {{fish.rarity}}</div>
  <div>Prix : {{fish.price}}</div>
  <button [routerLink]="['/fish']">Retour à la liste</button>
</div>
<div *ngIf="!fish">
  Chargement des données...
</div>
```
``` javascript
export class SinglefishComponent implements OnInit {

  fish : Creature | null = null;

  constructor(public route : ActivatedRoute, public httpService : HttpService) { }

  async ngOnInit() {

    let id = this.route.snapshot.paramMap.get("id");

    if(id != null && parseInt(id) > 0 && parseInt(id) < 81){
      this.fish = await this.httpService.getFish(id);
    }
  }
}
```

# Lab 3 PLus
### app.component.css
``` css
header{
    background-color:#170819;
    color:white;
    font-family:Calibri;
    font-weight:bolder;
    transition:0.5s;
}
header h1{
    transition:0.5s;
}
header:hover{
    color:#79E0FD;
}
header h1:hover{
    color:#F685D8;
}
.row{
    border:white solid 5px;
    border-radius:25px;
    padding:10px;
    margin:20px 0px 20px 0px;
}
.img-fluid{
    border-radius:25px;
}

form{
    background-color:rgba(255,255,255,0.2);
    padding:5px;
    border-radius:10px;
}

form input{
    padding:5px;
    margin:5px;
    width:95%;
}

.col-3{
    padding:0px;
}

.artists{
    background-color:rgba(255,255,255,0.2);
    padding:5px;
    border-radius:10px;
    margin-left:10px;
    color:white;
    font-family:Calibri;
    font-weight:bolder;
    text-align:center;
}

li{
  list-style: inside;
}
```
## app.component.html
``` html
<div class="container-xxl">
  <header class="row align-items-center">
    <div class="col-1 mx-0 px-0"><img class="img-fluid" src="../assets/images/note.png" alt="Music note"></div>
    <div class="col"><h1>Requêtes musicales</h1></div>
  </header>
  <div class="row">
    <div class="col-3">
      <form (ngSubmit)="searchArtist()" *ngIf="!result">
        <input type="text" name="artistName" [(ngModel)]="artistName" class="form-control" placeholder="Nom d'un artiste">
        <input type="submit" value="Rechercher l'artiste PLUS" class="btn btn-light">
      </form>
      <form (ngSubmit)="newSearch()" *ngIf="result">
        <input type="submit" value="Nouvelle recherche">
      </form>
    </div>
    <div class="col artists">
        <h2>Résultats</h2>
        <h3>Artistes similaires à {{artistName}}</h3>
        <ul>
          <li *ngFor="let matchedArtist of matchedArtists">{{matchedArtist.name}} {{matchedArtist.numberOfMatches}}</li>
        </ul>
    </div>
  </div>
</div>
```
## app.component.ts
``` javascript
import { HttpClient } from '@angular/common/http';
import { Component } from '@angular/core';
import { lastValueFrom } from 'rxjs';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  result = false;
  artistName : string = "";
  matchedArtists:Artist[] = [];

  constructor(public httpClient : HttpClient) {}

  async searchArtist():Promise<void> {
    this.result = true;

    let requestResult = await this.getSimilarArtistByName(this.artistName);
    console.log(requestResult);
    let similarArtists:any[] = requestResult.similarartists.artist;;

    for(let similarArtist of similarArtists) {
      let similarArtists2 = (await this.getSimilarArtistByName(similarArtist.name)).similarartists.artist;

      let matchedArtist:Artist = new Artist(similarArtist.name);

      for(let similarArtist2 of similarArtists2) {
        if(this.artistName == similarArtist2.name) {
          matchedArtist.numberOfMatches++;
          continue;
        }

        // En utilisant une boucle avec continue
        for(let artist of similarArtists) {
          if(artist.name == similarArtist2.name) {
            matchedArtist.numberOfMatches++;
            continue;
          }
        }
        // On pourrait également remplacer les lignes précédentes et utiliser Array.find
        /*if(similarArtists.find((a) => a.name == similarArtist2.name) != null)
          matchedArtist.numberOfMatches++;*/
      }

      this.matchedArtists.push(matchedArtist);
    }
  }

  newSearch():void{
    this.artistName = "";
    this.result = false;
    this.matchedArtists = [];
  }

  getSimilarArtistByName(artistName:string):Promise<any> {
    return lastValueFrom(this.httpClient.get<any>("http://ws.audioscrobbler.com/2.0/?method=artist.getSimilar&api_key=e34ebf8561ba7c653a21d1d99a1a0070&format=json&limit=8&artist=" + artistName));
  }
}

class Artist {
  numberOfMatches:number = 0;
  constructor(public name:string) {}
}

```
## app.module.ts

``` javascript
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpClientModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```