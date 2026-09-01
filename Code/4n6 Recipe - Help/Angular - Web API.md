# Copy&paste Code

Objectifs RESTful (Ce ne sont que des buzzwords)
+ Performance 📈
+ Évolutivité (Scalability) 📐
+ Simplicité 👶
+ Portabilité 📱📺
+ Fiabilité 🔩

Les six contraintes architecturales REST :
+ 👥 <span id="pink">`Architecture client-serveur`</span> : Séparation de l’interface utilisateur et du stockage de données
+ Lié à la scalability : L’application « client » ou « serveur » peut évoluer indépendamment de l’autre facilement. Aide aussi pour la portabilité.
+ 🧊 <span id="pink">`Sans état (Statelessness)`</span> : Peu ou pas d’information de session sur le serveur. (Tout sur le client autant que possible)
 Aide pour la performance du serveur
+ 💾 <span id="pink">`Cacheabilité`</span> : Mettre en cache certaines données. (Données récurrentes)
 Améliore la performance du client.
+ 📚 <span id="pink">`Système en couches`</span> : Des logiciels intermédiaires peuvent accompagner le client ou le serveur. (Couche pour la sécurité, couche pour load balancing, etc.)
 Encore lié à la performance / scalability
+ 💿 <span id="pink">`Code sur demande (optionnel)`</span> : On peut envoyer du Javascript pour « étendre les fonctionnalités du client situationnellement »
+ 💻📱 <span id="pink">`Interface uniforme`</span> : Peu importe l’application client, on envoie les mêmes requêtes au serveur et le serveur retourne les données dans le même format. Portabilité !

# CRUD
+ Opérations avec une base de données : Create, Retrieve, Update et Delete.
+ Avec REST, on rend ces opérations possibles via des requêtes HTTP faites par un client :
  + GET (retrieve) : Obtenir des données de la BD
  + POST (create) : Ajouter des données à la BD
  + PUT (update) : Modifier des données de la BD
  + DELETE (delete) : Supprimer des données de la BD

# Creation : Projet ASP net core Web API
Type (Project) : `ASP.NET CORE Web API`

[x] Place solution and project in the same directory

Version : `.Net 6.0`

WeatherForecast = DELETE

ShortCUT : write `prop` + (Double Tap) TAB

Resultat : `public int MyProperty { get; set; }` 

NuGet Packages: (Same Version)
+ Microsoft.EntityFrameworkCore.SqlServer 
+ Microsoft.EntityFrameworkCore.Tool
+ Microsoft.VisualStudio.Web.CodeGeneration.Deisgn

Auto-Generated
`API Controller with actions, using Entity Framework`

dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
dotnet ef database update

# Example
``` Typescript
const GetAnimal = "/GetAnimal";
const PostAnimal = "/PostAnimal";
const PutAnimal = "/PutAnimal";
const DeleteAnimal = "/DeleteAnimal";
const l = "/";
const Base_Url = "https://localhost:7053/api/Animals";

export class AppComponent {
  
  // Inputs
  id ?: number;
  type : string = "";
  name : string = "";

  animals : Animal[] = [];
  animal ?: Animal;

  constructor(public http : HttpClient) { }

  // Récupère tous les animaux dans la base de données
  async getAnimals() : Promise<void>{
	// A
    let x = await lastValueFrom(this.http.get<Animal[]>(Base_Url + GetAnimal));
    console.log(x);
    this.animals = x;
  }

  // Ajoute un animal dans la base de données
  async postAnimal() : Promise<void>{
    // N
    let newAnimal = new Animal(0, this.type, this.name)
    console.log(newAnimal);
    let x = await lastValueFrom(this.http.post<Animal>(Base_Url + PostAnimal, newAnimal));
    console.log(x);
  }

  // Récupère un animal en particulier dans la base de données
  async getAnimal() : Promise<void>{
    // I
    let x = await lastValueFrom(this.http.get<Animal>(Base_Url + GetAnimal + l + this.id));
    console.log(x);
    this.animal = x;
  }

  // Modifie (ou crée) un animal en particulier dans la base de données
  async putAnimal() : Promise<void>{
    // M
    if (this.id != null){
    let updateAnimal = new Animal(this.id, this.type, this.name)
    
    let x = await lastValueFrom(this.http.put<Animal>(Base_Url + PutAnimal + l + this.id, updateAnimal));
    console.log(x);
    }
  }

  // Supprime un animal en particulier dans la base de données
  async deleteAnimal() : Promise<void>{
    // A
    let x = await lastValueFrom(this.http.delete<Animal>(Base_Url + DeleteAnimal+ l + this.id));
    console.log(x);
  }

  // Sussy function
  async deleteAll() : Promise<void>{
    // L
    let x = await lastValueFrom(this.http.delete<Animal[]>(Base_Url + DeleteAnimal));
    console.log(x);
  }
}
```

# Program.cs
`builder.Services.AddScoped<GenericService<Animal>>();`
``` c#
builder.Services.AddCors(options =>
{
    options.AddPolicy("Allow all", policy =>
    {
        policy.AllowAnyOrigin();
        policy.AllowAnyMethod();
        policy.AllowAnyHeader();
    });
});

app.UseCors("Allow all");
```
# Service
``` c#
public class GenericService<T> where T : class
    {
        protected readonly Labo8_AnimalContext _context;

        public GenericService(Labo8_AnimalContext context)
        {
            _context = context;
        }

        public async Task<IEnumerable<T>> GetAll() {
            return await _context.Set<T>().ToListAsync();
        }

        public async Task<T?> Get(int id)
        {
            if (_context.Set<T>() == null)
            { return null; }
            var obj = await _context.Set<T>().FindAsync(id);
            if (obj == null)  { return null; }
            return obj;
        }

        public async Task<T?> Put(int id, T t)
        {
            _context.Entry(t).State = EntityState.Modified;
            try 
            {
                await _context.SaveChangesAsync();
            }
            catch (DbUpdateConcurrencyException)
            {
                if (await _context.Set<T>().FindAsync(id) == null)
                { return null; }
                else 
                { throw; }
            }
            return t;
        }

        public async Task<T?> Post(T t) {
            if (_context.Set<T>() == null) { return null; }
            _context.Set<T>().Add(t);
            await _context.SaveChangesAsync();
            return t;
        }

        public async Task Delete(int id)
        {
            if (_context.Set<T>() == null) { return; }
            var obj = await _context.Set<T>().FindAsync(id);
            if (obj == null) { return; }
            _context.Set<T>().Remove(obj);
            await _context.SaveChangesAsync();
        }
    }
```

# Controller + Service

`[Route("api/[controller]")]`

`[Route("api/[controller]/[action]")]`

``` c#
    [Route("api/[controller]/[action]")]
    [ApiController]
    public class AnimalsController : ControllerBase
    {

        private readonly Labo8_AnimalContext _context;
        private readonly GenericService<Animal> AnimalService;
        //public AnimalsController(Labo8_AnimalContext context)
        //{
        //    _context = context;
        //}
        public AnimalsController(GenericService<Animal> animalService, Labo8_AnimalContext context)
        {
            AnimalService = animalService;
            _context = context;
        }

        // GET: api/Animals
        [HttpGet]
        public async Task<ActionResult<IEnumerable<Animal>>> GetAnimal()
        {
            //if (_context.Animal == null)
            //{
            //    return NotFound();
            //}
            //  return await _context.Animal.ToListAsync();
            return Ok(await AnimalService.GetAll());
        }

        // GET: api/Animals/5
        [HttpGet("{id}")]
        public async Task<ActionResult<Animal>> GetAnimal(int id)
        {
            Animal? animal = await AnimalService.Get(id);
            return animal == null ? NotFound() : null;
        }

        // PUT: api/Animals/5
        // To protect from overposting attacks, see https://go.microsoft.com/fwlink/?linkid=2123754
        [HttpPut("{id}")]
        public async Task<IActionResult> PutAnimal(int id, Animal animal)
        {
            if (id != animal.Id)
            {
                return BadRequest();
            }

            Animal? UpdateAnimal = await AnimalService.Put(id, animal);

            if (UpdateAnimal == null) {
                return StatusCode(StatusCodes.Status500InternalServerError,
                    new { Message = "Error"
                    });
            }

            return NoContent();
        }

        // POST: api/Animals
        // To protect from overposting attacks, see https://go.microsoft.com/fwlink/?linkid=2123754
        [HttpPost]
        public async Task<ActionResult<Animal>> PostAnimal(Animal animal)
        {
            Animal? newAnimal = await AnimalService.Post(animal);
            if (newAnimal == null)
             {
               return Problem("Entity set 'Labo8_AnimalContext.Animal'  is null.");
             }

            return CreatedAtAction("GetAnimal", new { id = newAnimal.Id }, newAnimal);
        }

        // DELETE: api/Animals/5
        [HttpDelete("{id}")]
        public async Task<IActionResult> DeleteAnimal(int id)
        {
            await AnimalService.Delete(id);
            return NoContent();
        }

        private bool AnimalExists(int id)
        {
            //AnimalService.Equals(id);
            return (_context.Animal?.Any(e => e.Id == id)).GetValueOrDefault();
        }
    }
```

<style>
#pink {color:#FA4098;}
.pink {color:#FA4098;}
.orchid {color:#DA70D6;}
.violet {color:#EE82EE;}
</style>

### [Back To The Top](#Copy&paste Code)