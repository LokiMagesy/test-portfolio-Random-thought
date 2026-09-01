# Stockage Local

**Save** to Local Storage: 
`localStorage.setItem(key, value);`

Saving data of type String.
`PraiseTheSun : string = "\[T]/";`

Create: Function for saving the data in the local storage.
``` typescript
saveSun(){
  localStorage.setItem("Knight",this.PraiseTheSun);
  sessionStorage.setItem("Knight", this.PraiseTheSun);
  //Fonctionnment identique (localStorage = sessionStorage)

  //Sauvegarder une donnée d’un autre type que string
  ThirdPraiseTheSun = new Sun("something", 6)
  localStorage.setItem("Knight2",JSON.stringify(this.PraiseTheSun));
  // Toutefois, la donnée doit être transformée en string au préalable 
  // à l’aide de la méthode stringify() qui fait partie de la classe utilitaire JSON.
}
```

**Read** data from local storage:
`let Solaire  = localStorage.getItem(key);`

**(Delete)** Remove Data from Local Storage:
`localStorage.removeItem(key);`

**(Empty)** Remove All (Clear Local Storage):
`LocalStorage.clear();`

https://blog.jscrambler.com/working-with-angular-local-storage 

``` typescript
export class LocalService {

  constructor() { }

  public saveData(key: string, value: string) {
    localStorage.setItem(key, value);
  }

  public getData(key: string) {
    return localStorage.getItem(key)
  }
  public removeData(key: string) {
    localStorage.removeItem(key);
  }

  public clearData() {
    localStorage.clear();
  }
}
```

## Load
### String
`SecondPraiseTheSun:string|null = "\[T]/";`

Variable pour stocker la valeur récupérée. Elle doit pouvoir être null : Si on ne réussit pas à récupérer de valeur dans le stockage local, la valeur null sera rangée dans cette variable. 
### Other Type
`jsonData: String | null = null;`

`ThirdPraiseTheSun ?: Sun;`

Préparer une variable pour stocker la valeur originale et une variable de type string qui peut être null pour stocker la valeur récupérée avant de la transformer.
``` typescript
ngOnInit():void{
  this.SecondPraiseTheSun = localStorage.getItem("Knight");
}
ngOnInit():void{
  this.jsonData = localStorage.getItem("Knight2");
  if(this.jsonData != null){
    this.ThirdPraiseTheSun = JSON.parse(this.jsonData);
  }
}
```

https://blog.briebug.com/blog/managing-local-storage-in-angular 
### Local Storage Methods 
Local Storage is an Immutable API, meaning every interaction with the object does not mutate it. This is a good strategy and best practice when dealing with data in most situations. Luckily, the API only has four methods. It is very simple.
#### The Local Storage API has 4 methods:
1. setItem(key: string, data: string | JSON): void
   * Takes a key parameter and a value parameter. The key allows you to retrieve the value later using lookups. The value is stored as a JSON string. This method does not return anything.
2. getItem(key: string): string | JSON | null
   * Takes a key parameter for looking up data in storage. If this lookup fails it will return a null value.
3. removeItem(key: string): undefined
   * Takes a key parameter for looking up data in storage. This method always returns undefined.
4. clear(): undefined
   * Takes no parameters. Clears all data in local storage. This method always returns undefined.
### Using These Methods
A common way of setting data in local storage, getting it, and removing it:
``` typescript
setData(data) {
   const jsonData = JSON.stringify(data)
   localStorage.setItem('myData', jsonData)
}

getData() {
   return localStorage.getItem('myData')
}

removeData(key) {
   localStorage.removeItem(key)
}
```
----------------------------------------------------------------------------------------------
# i18n
Étape 1 : Installer deux nouvelles dépendances npm* 

`npm install @ngx-translate/core` & `npm install @ngx-translate/http-loader`

## app.module.ts
Étape 2 : Configuration dans app.module.ts

TranslateModule, qui permet de configurer la traduction.
TranslateHttpLoader, qui chargera des traductions depuis des fichiers que nous allons produire.

Import

`import { TranslateLoader, TranslateModule } from '@ngx-translate/core';`
`import { TranslateHttpLoader } from '@ngx-translate/http-loader';`

``` typescript
// Cette partie sert à configurer comment seront générées / chargées nos traductions. 
// Dans notre cas, ce sera à l’aide d’un TranslateHttpLoader. 
// On indique également que HttpClient est une dépendance associée.
  TranslateModule.forRoot({
      loader: {
        provide : TranslateLoader,
        useFactory: HttpLoaderFactory,
        deps: [HttpClient]
      }
  }),
export function HttpLoaderFactory(http:HttpClient){
  return new TranslateLoader(http);
}
// Nous avons besoin de créer  cette fonction exportable pour 
// instancier un TranslateHttpLoader qui a accès à la dépendance HttpClient. 
```

Étape 3 : Dans le composant qu’on souhaite rendre internationalisé, injecter le service TranslateService et définir la langue par défaut.

`import {TranslateService} from '@ngx-translate/core';`
``` typescript
export class FranglaisComponent implements OnInit {
  language : string = "fr";
  constructor(public translator: TranslateService){
    translator.setDefaultLang(this.language);
  }
}
```
Dans le constructeur : On a injecté TranslateService.

Dans le corps du constructeur, on a définit la langue par défaut au service. (Dans ce cas-ci, "fr")

La langue par défaut a été spécifiée dans une variable de classe. Ce n’est pas obligatoire, mais cette variable nous servira plus tard.

Dans le dossier assets, on doit avoir un sous-dossier « i18n » qui contient un fichier JSON par langue. Comme nous avions mis "fr" comme langue par défaut, obligatoirement, il nous faut au moins un fichier nommé « fr.json ».

``` json
{
  "composant1":{
    "title": "blabla fr",
    "text": "blabla fr"
  },
   "composant2":{
    "title": "blabla En",
    "text": "blabla En"
  }
}
```
`<Button (click)="changeLanguage('fr'))">Francais</Button>`

`<Button (click)="changeLanguage('en'))">English</Button>`

``` javascript
changeLanguage(lang:String):void{
  this.language = lang;
  this.translator.use(this.language);
}
```
Final
``` typescript
export class AppComponent{
  language : string = "fr";
  constructor(public translator: TranslateService){
    translator.setDefaultLang(this.language);
  }
  changeLanguage(lang:String):void{
  this.language = lang;
  this.translator.use(this.language);
}
}
```
# token
PowerPoint page 18


``` typescript
export class SpotifyComponent implements OnInit {
  spotifyToken ?: string;
  constructor(public http: HttpClient){}
}
```

``` typescript
async connect(): Promise<void> {
   let body = new HttpParams()
      .set('grant_type', 'client_credentials');
    let httpOptions = {
      headers: new HttpHeaders({
        'Content-Type':  'application/x-www-form-urlencoded',
        'Authorization': 'Basic ' + btoa(CLIENT_ID + ':' + CLIENT_SECRET)
      })
    };
    let x = await lastValueFrom(this.http.post<any>('https://accounts.spotify.com/api/token',      
    body.toString(), httpOptions));
    console.log(x);
    this.spotifyToken = x.access_token;
  }

```

## 4

```typescript
async searchArtist(artist : string): Promise<any> {
    const httpOptions = {
      headers: new HttpHeaders({
        'Content-Type':  'application/json',
        'Authorization': 'Bearer ' + this.spotifyToken
      })
    };
    let x = await lastValueFrom(this.http.get<any>('https://api.spotify.com/v1/search?type=artist&offset=0&limit=1&q=' + 
    artist, httpOptions));
    console.log(x);
    return new Artist(x.artists.items[0].id, x.artists.items[0].name, x.artists.items[0].images[0].url);
}
async getAlbums(artist : Artist): Promise<any> {
    
    const httpOptions = {
      headers: new HttpHeaders({
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + this.spotifyToken
      })
    };
    
    let x = await lastValueFrom(this.http.get<any>("https://api.spotify.com/v1/artists/" + artist.id + 
    "/albums?include_groups=album,single", httpOptions));
    console.log(x);
    
    let albums = [];
    for(let i = 0; i < x.items.length; i++){
      albums.push(new Album(x.items[i].id, x.items[i].name, x.items[i].images[0].url, []));
    }
    return albums;
 }
async getSongs(album: Album): Promise<Song[]> {
     const httpOptions = {
      headers: new HttpHeaders({
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + this.spotifyToken
      })
    };
    let x = await lastValueFrom(this.http.get<any>("https://api.spotify.com/v1/albums/" + album.id, httpOptions));
    console.log(x);
    
    let songs : Song[] = [];
    for(let i = 0; i < x.tracks.items.length; i++){
      songs.push(new Song (x.tracks.items[i].id, x.tracks.items[i].name));
    }
    return songs;
}
class Artist {
  constructor(public id:string, public name:string, public imageUrl:string){}
}
class Album {
  constructor(public id:string, public name:string, public image:string. public songs:Song[] = []){}
}
class Song {
  constructor(public id:string, public name:string){}
}
```
