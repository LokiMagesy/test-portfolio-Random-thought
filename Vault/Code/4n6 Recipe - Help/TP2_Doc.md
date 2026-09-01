
for artist : https://www.w3schools.com/howto/tryit.asp?filename=tryhow_css_image_overlay_title 
# 420-4W6 : TP2

## Super Musique Infinie
Date de remise : `Semaine 6`

Pondération : `20%`

## Consigne
Le projet sera fait de façon individuelle. Le projet sera fait avec le **Framework Angular**.

Si vous ne savez pas du tout par où commencer pour ce projet, rendez-vous à la dernière page de ce document. 

## Mise en contexte
Vous devez faire une gestion de contenu musical utilisant différents APIs.

L’application permettra de :
* Rechercher des artistes (et les conserver en favoris) à l’aide de l’API de Spotify.
* Rechercher les albums d’un artiste à l’aide de l’API de Spotify.
* Rechercher les chansons d’un album à l’aide de l’API de Spotify.
* Rechercher les concerts d’un artiste à l’aide de l’API BandsInTown ET afficher les emplacements des concerts à l’aide de l’API Google Maps.
* Rechercher une vidéo Youtube à partir d’une chanson pour l’écouter.

### Consignes générales
L’application devra être disponible en Anglais et en Français.

# Asset

## Image

## i18n 

## Page d’accueil (Liste d’artistes)
`ng generate component Artist`

À partir de la page d’accueil, l’utilisateur pourra ajouter des artistes à une liste d’artistes favoris. Cette liste sera persistante (sauvegardée) dans le localStorage du navigateur. 

Une requête à Spotify permettra de récupérer des informations sur des artistes. Vous devrez afficher le nom et une image de chaque artiste.

À partir de l’artiste nous pourrons aller voir les spectacles prévus de l’artiste ou encore aller voir les albums de l’artiste.

## Page des concerts
`ng generate component Concert`

Une page permettra d’afficher les concerts d’un artiste. Nous récupérerons les concerts à partir d’une requête à l’API de BandsInTown.

Le nom de l’artiste devra être fourni en paramètre dans le routage du composant.

Un carte Google Maps permettra de voir l’emplacement des concerts.

## Page des albums
`ng generate component Album`

Une page permettra d’afficher les albums d’un artiste. Nous récupérerons les albums à partir d’une requête à l’API de Spotify. Vous devrez afficher le nom et une image de chaque album.

Le nom de l’artiste (ou son ID) devra être fourni en paramètre dans le routage du composant.

## Page des chansons
`ng generate component Song`

Model : 
``` typescript
export class Album{
    constructor(public id : string, public name : string, public image : string, public songs: Song[] = [] ){}
}
```
JSON (Note) : 

Model : 
``` typescript
export class Artist{
    constructor(public id : number, public name : string, public imageUrl : string){}
}
```

JSON (Note) : 

Model : 
``` typescript
export class Concert {
    constructor (public latitude:number, public longitude:number, public city:string, public country:string, public date:string) {}
}
```

JSON (Note) : 

Model : 
``` typescript
export class Song{
    constructor(public id : string, public name : string, public imageUrl : string){}
}
```

JSON (Note) : 

Une page permettra d’afficher les chansons d’un album. Nous récupérerons les chansons à partir d’une requête à l’API de Spotify. Vous devrez seulement afficher le nom de chaque chanson.

Lors du clic sur une chanson, une requête à YouTube sera effectuée pour avoir accès à la chanson. Pour la requête, il est suggéré de placer le nom de la chanson et l’artiste dans la recherche.

Une fois la requête effectuée, il sera possible d’afficher une vidéo YouTube de la chanson. Pour ce faire, nous utiliserons le Id de la vidéo et un iFrame.

Au minimum, le id de l’album devra être fourni en paramètre dans le routage du composant.

## Material

## Pipe
Add Folder: `pipe`
``` typescript
@Pipe({
  name: 'trust'
})
export class TrustPipe implements PipeTransform {
  constructor(public sanitizer:DomSanitizer){}
  transform(value: any, args?: any): any {
    return this.sanitizer.bypassSecurityTrustResourceUrl(value);
  }
}
```

## Routing
add Folder: `routing`

``` typescript
const routes : Routes = [
      {path: "", redirectTo: "artist", pathMatch: "full"},
      {path: "artist", component:ArtistComponent},
      {path: "album", component:AlbumComponent},
      {path: "concert", component:ConcertComponent},
      {path: "song", component:SongComponent},
      {path: "album/:id", component: AlbumComponent},
      {path: ":name/song/:id", component: SongComponent},
      {path: "concert/:name", component: ConcertComponent},
];

@NgModule({
  imports: [
    CommonModule,
    RouterModule.forRoot(routes),
  ],
  declarations: [],
  exports: [RouterModule] // Important
})
export class RoutingModule { }

```
# Service
Folder Service
## Service Bandsintown
``` typescript
const Key: string = "2b32475766802ac01eefda45e9e42ea0"

@Injectable({
  providedIn: 'root'
})
export class BandsintownService {

constructor(public http : HttpClient) { }

  async searchConcert(artistName:string): Promise<Concert[]>{
      let x = await lastValueFrom(this.http.get<any>("https://rest.bandsintown.com/artists/"+ artistName+"/events?app_id="+ Key))
      console.log(x);
      let concerts:Concert[] = [];
      for(let i = 0; i < x.length; i++){
        concerts.push(new Concert(parseFloat(x[i].venue.latitude), parseFloat(x[i].venue.longitude), x[i].venue.city, x[i].venue.country, x[i].starts_at))
      }
      //console.log(concerts)
      return concerts;
  }
```
## Service Google
``` typescript
const googleApiKey = "AIzaSyDHeSOnUjzWWHaZZMo7nbEWSqGPJZmpUDA";
const googleApiKey_Cegep = "AIzaSyDEpKpSF6uc02C9YEUa5TN3Ro_4mcEXVr8";
const googleString = "https://www.googleapis.com/youtube/v3/search?type=video&part=id&maxResults=1&key=";

@Injectable({
  providedIn: 'root'
})
export class GoogleService {

  randomId :string = "";
  constructor(public http : HttpClient) { }

  async searchVideoId(searchText : string) : Promise<string> {
    //console.log(searchText)
    let i = await lastValueFrom(this.http.get<any>(googleString + googleApiKey_Cegep + "&q="+ searchText));
    console.log(i)
    let id = i.items[0].id.videoId;
    return id;
  }
}
```
## Service Spotify
``` typescript
const CLIENT_ID : string = "d10ea527868b4b16a8c8e2039c80fd38";
const CLIENT_SECRET : string = "64342dcff1bc43b282aebb3cc95d9ca7";

@Injectable({
  providedIn: 'root'
})
export class SpotifyService {

  spotifyToken : string | null = null;

  constructor(public http : HttpClient) { }

  async connect(): Promise<void> {
      let body = new HttpParams().set('grant_type', 'client_credentials');
      let httpOptions = {
        headers: new HttpHeaders({
        'Content-Type': 'application/x-www-form-urlencoded',
        'Authorization': 'Basic ' + btoa(CLIENT_ID + ':' + CLIENT_SECRET)
        })
      };
      let x = await lastValueFrom(this.http.post<any>('https://accounts.spotify.com/api/token', body.toString(), httpOptions));
      //console.log(x);
      this.spotifyToken = x.access_token;
  }
  
  getHttpOptions (): { headers:HttpHeaders} {
    return { headers: new HttpHeaders({
      'Content-Type':  'application/json',
      'Authorization': 'Bearer ' + this.spotifyToken
    })};
  }

  async searchArtist(artist : string): Promise<Artist> {
    let x = await lastValueFrom(this.http.get<any>('https://api.spotify.com/v1/search?type=artist&offset=0&limit=1&q=' + artist, this.getHttpOptions()));
    return new Artist(x.artists.items[0].id, x.artists.items[0].name, x.artists.items[0].images[0].url);
  }

 async searchAlbums(artist:string): Promise<Album[]>{
  let x =  await lastValueFrom(this.http.get<any>("https://api.spotify.com/v1/artists/"  + artist + "/albums?include_groups=album,single", this.getHttpOptions()));
  let albums = [];
  for(let i = 0; i < x.items.length; i++){
    albums.push(new Album(x.items[i].id, x.items[i].name, x.items[i].images[0].url, []))
  }
  return albums;
 }

 async searchSongs(albumId:string): Promise<Song[]>{

  let x =  await lastValueFrom(this.http.get<any>("https://api.spotify.com/v1/albums/" + albumId, this.getHttpOptions()));
  //console.log(x)
  let songs:Song[] = [];
  for(let i = 0; i < x.tracks.items.length; i++){
    songs.push(new Song(x.tracks.items[i].id, x.tracks.items[i].name, x.images[2].url))
  }
  return songs;
 }
}
```

## URLS et doc
[YouTube Search API](https://developers.google.com/youtube/v3/docs/search/list)

[Example d’utilisation](https://www.googleapis.com/youtube/v3/search?part=id&maxResults=1&key={API_KEY}&q={MA_RECHERCHE}) Vous devrez vous-même créer une clé d’API pour utiliser les API de Google

[BandsInTown](https://app.swaggerhub.com/apis/Bandsintown/PublicAPI/3.0.0#/artist%20events/artistEvents)

[Clé pour utiliser l’API de BandsInTown](https://rest.bandsintown.com/artists/MON_ARTISTE/events?app_id=API_KEY) 

Key: 2b32475766802ac01eefda45e9e42ea0 

[Spotify API](https://developer.spotify.com/documentation/web-api/reference/#/)

[Exemple d’utilisation de l’API](https://github.com/jmnadeau/4W6-exercices/tree/main/Exemples/Spotify)

## Par où commencer ?
La réalisation d’un projet Angular avec plusieurs composants, modèles et services peut être intimidant. Voici une séquence suggérée (loin d’être incontournable) pour avancer dans le projet :
1.	Créez tous les composants dont vous aurez besoin.
	* Le composant App servira seulement de squelette pour la page. (header, footer, ..) Donc comptez un composant de plus pour chacune des pages dont vous aurez besoin. 
	* Configurez le routage de votre application. Êtes-vous capable d’accéder à tous les composants que vous avez créés ?
2.	Préparez des services qui serviront à implémenter vos requêtes et stocker certaines données.
	* À combien d’API ferez-vous des requêtes ? Cela pourrait influencer le nombre de services pertinents à préparer.
3.	Préparez des modèles pour encapsuler les données.
	* Quel type de données aurez-vous à afficher dans vos pages Web ? Vous pourriez déjà préparer les classes de vos modèles en conséquence.
4.	Rendre vos composants fonctionnels, un à la fois.
	* Le gros du travail ! Un composant à la fois, préparer l’interface HTML puis rendre fonctionnels les formulaires et requêtes qui permettent à l’utilisateur d’interagir avec la page.
5.	Internationalisez.
	* Si tout fonctionne et que vous êtes satisfait de votre application, c’est l’heure de tout traduire !
  
Certains développeurs auraient préféré créer et compléter un composant à la fois plutôt que de tous les préparer dès le début. (Dans un projet gigantesque, on ne va tout de même pas préparer 50 composants d’un seul coup !)

De même, certains auraient peut-être inversé certaines des étapes citées. À vous de voir ! Bonne chance !

# Personel CSS

``` css
.background {
    height: auto;
    background: linear-gradient(141deg, Purple 0%, #DA70D6 51%, #2cb5e8 75%);
    border: 5px solid deepskyblue;
    box-shadow: 0px 5px 15px #008080;
    opacity: 0.9;
    padding-top: 45px;
    border-radius: 2em / 5em;
    margin-bottom: 15px;
}

.bottom {
    color: #FFFFF0;
    text-shadow: 2px 2px black;
    font-weight: bold;
    height: auto;
    background: linear-gradient(141deg, #8A2BE2 0%, #DDA0DD 51%, #008080 75%);
    border: 5px solid deepskyblue;
    box-shadow: 0px 5px 15px #008080;
    opacity: 0.8;
    padding-top: 30px;
    border-radius: 25px;
    padding-bottom: 5px;
}

// moving color
.backgroundIndex {
    height: auto;
    background: linear-gradient(270deg, Purple 0%, #DA70D6 51%, #2cb5e8 75%);
    border: 5px solid plum;
    box-shadow: 0px 5px 15px #008080;
    opacity: 0.9;
    padding-top: 45px;
    /* courbe étrange*/
    border-radius: 2em / 5em;
    /* animation */
    background-size: 400% 400%;
    animation: gradient 10s ease infinite;
    margin-bottom: 10px;
}

@keyframes gradient {
    0% {
        background-position: 0% 50%;
    }

    50% {
        background-position: 100% 50%;
    }

    100% {
        background-position: 0% 50%;
    }
	
}

// Glowing Character
.glow {
  font-size: 80px;
  transition: 1.5s;
  color: #fff;
  text-align: center;
  animation: glow 1s ease-in-out infinite alternate;
}
@keyframes glow {
  from {
    text-shadow: 0 0 10px #fff, 0 0 20px #fff, 0 0 30px #e60073, 0 0 40px #e60073, 0 0 50px #e60073, 0 0 60px #e60073, 0 0 70px #e60073;
  }

  to {
    text-shadow: 0 0 20px #fff, 0 0 30px #ff4da6, 0 0 40px #ff4da6, 0 0 50px #ff4da6, 0 0 60px #ff4da6, 0 0 70px #ff4da6, 0 0 80px #ff4da6;
  }
}

```