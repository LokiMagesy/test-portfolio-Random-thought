# SQL
> Worksheet of something to pratice.
### Table of contents
1. [Introduction](#introduction)
2. [Some paragraph](#paragraph1)
    1. [Sub paragraph](#subparagraph1)
3. [Another paragraph](#paragraph2)
---
+ **DDL**: Data Definition Language
  + Définition de données (DDL)
+ **DML**: Data Manipulation Language
  + Manipulation de données (DML)
+ **DQL**: Data Query Language
+ **TCL**: Transaction Control Language (Sem 10+)
+ **DCL**: Data Control Language (Pas abordé)
# Définition de données
Après avoir conçu le <span id="pink">modèle logique</span>, (qui représente des tables de données), il est temps de créer concrètement la base de données et ses tables dans un <span id="pink">système de gestion de base de données </span>, (SGBD) comme Microsoft SQL Server.

Pour la définition de données (ainsi que toutes les futures étapes dans le cours), nous utiliserons majoritairement des <span id="pink">scripts SQL</span>, plutôt que d’utiliser une <span id="pink">interface utilisateur</span>. (<span id="pink">UI</span>)

* Créer une <span id="pink">`Base de Données`</span>
* Créer un <span id="pink">`Schéma`</span>
* Créer une <span id="pink">`Table`</span> (+ <span id="pink">clés</span>, + <span id="pink">contraintes</span>)
```sql
CREATE DATABASE nom;
DROP DATABASE nom;

CREATE DATABASE nom;
GO
USE nom;
GO
```
Le GO entre l’instruction CREATE DATABASE et USE DATABASE permet de s’assurer que la création de la base de donnée est terminée AVANT qu’on essaie de l’utiliser.

```sql
CREATE SCHEMA nom;
DROP SCHEMA nom;
SELECT * FROM database_name.sys.schemas;

```
<samp>File not found.<br>Press F1 to continue</samp>

The `<samp>` tag is used to define sample output from a computer program. The content inside is displayed in the browser's default monospace font.
<q>Build a future where people live in harmony with nature.</q>
We hope they succeed.</p>
<label for="file">Downloading progress:</label>
<progress id="file" value="32" max="100"> 32% </progress>

## Choix des types
### Chaînes de caractères
* <span id="pink">`char(N)`</span>: Chaîne de caractères de N caractères maximum. (1 à 8000) Taille fixe occupée en mémoire, peu importe la taille de la chaîne. À utiliser si toutes les chaînes ont une taille TRÈS similaire.
* <span id="pink">`varchar(N)`</span>: Chaîne de caractères de N caractères maximum. (1 à 8000) Taille variable occupée en mémoire lorsque la chaîne est plus petite que N. À utiliser si les tailles varient.
* <span id="pink">`nchar(N) / nvarchar(N)`</span>: Identique, sauf que les caractères Unicode sont utilisés. (Beaucoup plus d’alphabets et de symboles), mais 2 bytes de stockage par caractère au lieu de 1. BEAUCOUP PLUS UTILISÉ DE NOS JOURS.
* <span id="pink">`text / ntext`</span>: Jusqu’à 2 Go. Pour les très longues chaînes de caractères.
### Images
* <span id="pink">`varbinary(max)`</span>: Jusqu’à 2 Go. Nous l’utiliserons tard dans la session.
* <span id="pink">`image`</span>: Similaire à varbinary(max), mais sera retiré par Microsoft bientôt.
### Numérique
* <span id="pink">`bit`</span>: Vaut 0, 1 ou NULL. Parfait pour les booléens. On met 1 ou 0 au lieu de true ou false.
* <span id="pink">`tinyint`</span>: 0 à 255. 1 byte.
* <span id="pink">`smallint`</span>: 0 ± 32 767. 2 bytes.
* <span id="pink">`int`</span>: 0 ± 2 147 483 647. 4 bytes.
* <span id="pink">`bingint`</span>: 0 ± 9 223 372 036 854 775 807. 8 bytes.
* <span id="pink">`numeric(p,s) / decimal(p,s)`</span>: Les deux sont identiques à 90%. Nombres décimaux avec un maximum de p chiffres au total et un maximum de s chiffres après la virgule. Ex : avec numeric(5,2), je peux stocker 123.45, mais pas 1234.56 ou 12.345. 5 à 17 bytes.
* <span id="pink">`float(n)`</span>: n = 24 pour stocker sur 4 bytes ou n = 53 pour stocker sur 8 bytes. Moins précis que numeric(p,s).
* <span id="pink">`real`</span>: équivalent à float(24).
### Dates
* <span id="pink">`datetime`</span>: 1er janvier 1753 au 31 décembre 9999. Précision de 3.33 milli-s. 8 bytes.
  * `Format`: '<span id="pink">YYYY-MM-DD HH:MI:SS</span>' (ex : '<span id="pink">2020-12-20 17:59:59</span>')
* <span id="pink">`datetime2`</span>: 1er janvier 0001 au 31 décembre 9999. Précision de 100 nano-s. 8 bytes.
  * `Format`: '<span id="pink">YYYY-MM-DD HH:MI:SS.nnnnnnn</span>' (ex : '<span id="pink">2020-12-20 17:59:59.1234567</span>')
* <span id="pink">`date`</span>: Stocke seulement la date, sans le moment de la journée. Année 0001 à 9999. 3 bytes.
  * `Format`: '<span id="pink">YYYY-MM-DD</span>'
* <span id="pink">`time`</span>: Stocke seulement le moment de la journée. Précision de 100 nano-s. 5 bytes.
  * `Format`: '<span id="pink">HH:MI:SS</span>'

# Contraintes
Pour assurer l’intégrité de certaines données, SQL nous permet d’ajouter des contraintes parmi les suivantes :

| Contrainte    | Description  |
| ----------- | ----------- | 
| <span id="pink">NOT NULL</span> | La colonne ne peut pas être vide. (avoir des valeurs Null) |
| <span id="pink">UNIQUE</span> | Aucune donnée de la colonne ne peut se répéter. |  
| <span id="pink">CHECK</span> | S’assure que les valeurs respectent une condition. |  
| <span id="pink">DEFAULT</span> | Définit une valeur par défaut si la colonne n’est pas remplie. | 

| Contrainte    | Description  |
| ----------- | ----------- | 
|<span id="pink">PRIMARY KEY</span> |Combinaison de NOT NULL et UNIQUE. Définit une clé primaire.|
|<span id="pink">FOREIGN KEY</span> |La valeur doit être une référence vers une PRIMARY KEY qui existe.|
|<span id="pink">IDENITY(x,y)</span> |Auto-incrémentation des valeurs. x = première valeur, y = valeur d’incrémentation++. Pas besoin de fournir de valeur lors d’un INSERT.|

```sql
CREATE TABLE Database.Table(
    ItemId int IDENTITY(1,1),
    Nom varchar(15) NOT NULL,
    Something varchar(10) NOT NULL,
    Stats ,
    CONSTRAINT PK_Table_ItemID PRIMARY KEY (ItemID)
);
```
Convention pour les noms des contraintes.

**Type_Table_Colonne**

**Type** : PK (Primary Key), FK (Foreign Key), CK (Check), UC (Unique), DF (Default)

**Table** : Nom de la table

**Colonne** : Nom de la colonne concernée. Si c’est un groupe de colonnes, un autre nom cohérent peut être utilisé.

```sql

ALTER TABLE Database.Table ADD CONSTRAINT UC_Table_Something Unique (Something)
GO

//Pour ajouter une contrainte nommée de type DEFAULT.
ALTER TABLE Database.Table ADD CONSTRAINT DF_Table_Stats DEFAULT 0 FOR Stats

ALTER TABLE Database.Table ADD CONSTRAINT CK_Table_Stats CHECK (
    (Vitesse BETWEEN 0 AND 6) AND
    (Acceleration BETWEEN 0 AND 6)
)

// pour supprimer une contrainte
ALTER TABLE Database.Table DROP CONSTRAINT DF_TABLE_Stats
```
Cascade sur clés étrangères

Lorsqu’une clé primaire est supprimée, les clés étrangères qui lui faisaient référence peuvent provoquer des erreurs. (Car elles sont obligées de faire référence à une clé qui existe)

Dans une contrainte de clé étrangère, on peut spécifier la réaction à la modification / suppression de la clé primaire associée.

`CONSTRAIN Nom FORIGN KEY (Colonne) REFERENCES Schema.Table(Colonne) ON X Y`
* **X** peut être remplacé par **DELETE** ou **UPDATE**. (Pour quelle action on souhaite avoir une réaction ?)
* **Y** peut-être remplacé par **CASCADE**, **SET NULL**, **SET DEFAULT** ou **NO ACTION**.
    * **CASCADE** supprime / met à jour la rangée de données si sa clé étrangère est impactée.
    * **SET NULL** donne la valeur **NULL** à la clé étrangère impactée. (Assurez-vous que la FK ait le droit d’être NULL...)
    * **SET DEFAULT** donne la valeur par défaut prédéterminée pour la colonne qui contient la clé étrangère. (Assurez-vous que la colonne de la FK a une contrainte DEFAULT définie...)
    * **NO ACTION** ne spécifie aucune réaction.

`ON UPDATE CASCADE`

`ON DELETE CASCADE`

Cycles et / ou cascades multiples
* Parfois, malheureusement, certaines contraintes en cascade ne sont pas compatibles.
* Dans l’exemple ci-dessous, on a deux clés étrangères qui font référence à la même clé primaire. Impossible de spécifier ON CASCADE DELETE ou ON CASCADE UPDATE pour les deux clés étrangères. (Alors que techniquement, on en a besoin)
* Dans ce genre de situation, on laisse tout simplement tomber les cascades. (Tout en conservant les contraintes de clé étrangère) Nous pourrons implémenter ce comportement avec les déclencheurs. (Semaine 5+)
    * Pour l’instant, ce serait impossible de supprimer un utilisateur associé à un blocage.

```sql

```

# Exemple of Code

* Partie 1 : Créer la BD et ses schémas
    * USE MASTER permet de s’assurer que la BD est crée à partir de la racine du SGBD.
    * USE Nouvelle_BD permet de s’assurer que les schémas et tables seront créés dans la bonne base de données.
* Partie 2 : Créer les tables et les contraintes de clé primaire
    * Nous allons spécifier les autres contraintes d’intégrité plus tard ! Celles qui sont absolument prioritaires :
        * Clé primaire
        * NOT NULL (Nous sommes obligés de le spécifier avec le type)
        * IDENTITY (Nous sommes obligés de le spécifier avec le type)
* Partie 3 : Créer les contraintes de clé étrangère
* Partie 4 : Créer les autres contraintes (CHECK, UNIQUE et DEFAULT)

```sql
--Création de la BD  
USE master
GO
CREATE DATABASE Rencontre;
GO
USE Rencontre
GO
--Création des deux schémas
CREATE SCHEMA Utilisateurs;
GO
CREATE SCHEMA Messageries;
GO
--Création des tables + contraintes de clé primaire
CREATE TABLE Utilisateurs.Utilisateur(
	UtilisateurID int Identity (1,1),
	Pseudo varchar(25) null,
	Courriel varchar(30) not null,
	DateNaissance Date not null,
	Description varchar(255) null,
	Ville char(20) null,
	Pays char(20) null
	Constraint PK_Utilisateur_UtilisateurID Primary Key (UtilisateurID)
);
CREATE TABLE Messageries.Message(
	MessageID int Identity (1,1),
	Texte varchar(150) not null,
	DateEnvoi Datetime not null,
	EstLu bit not null,
	ConversationID int not null,
	Constraint PK_Message_MessageID Primary Key (MessageID)
);
CREATE TABLE Messageries.Conversation(
	ConversationID int Identity (1,1),
	NbMessages int not null,
	DateDebut Datetime not null,
	Constraint PK_Conversation_ConversationID Primary Key (ConversationID)
);
CREATE TABLE Messageries.UtilisateurConversation(
	ConversationID int null,
	UtilisateurID int null
);
GO
--Création des contraintes de clé étrangère
ALTER TABLE Messageries.UtilisateurConversation ADD CONSTRAINT FK_Utilisateur_UtilisateurID 
FOREIGN KEY (UtilisateurID) REFERENCES Utilisateurs.Utilisateur(UtilisateurID)
ON DELETE CASCADE 
GO
ALTER TABLE Messageries.UtilisateurConversation ADD CONSTRAINT FK_Conversation_ConversationID 
FOREIGN KEY (ConversationID) REFERENCES Messageries.Conversation(ConversationID) 
ON DELETE CASCADE 
GO
ALTER TABLE Messageries.Message ADD CONSTRAINT FK_Message_ConversationID
FOREIGN KEY (ConversationID) REFERENCES Messageries.Conversation(ConversationID)  
ON DELETE CASCADE 
GO
-- Création des autres contraintes
GO
ALTER TABLE Utilisateurs.Utilisateur ADD CONSTRAINT CK_Utilisateur_DateNaissance CHECK (DATEDIFF(year, DateNaissance, getDate()) > 18)
GO
ALTER TABLE	Utilisateurs.Utilisateur ADD CONSTRAINT UC_Utilisateur_Pseudo UNIQUE (Pseudo)
GO
ALTER TABLE	Utilisateurs.Utilisateur ADD CONSTRAINT UC_Utilisateur_Courriel CHECK (Courriel LIKE('_%@_%._%'))
GO
ALTER TABLE	Messageries.UtilisateurConversation ADD CONSTRAINT UC_UtilisateurConversation_UtilisateurID UNIQUE (UtilisateurID)
GO
ALTER TABLE	Messageries.UtilisateurConversation ADD CONSTRAINT UC_UtilisateurConversation_ConservationID UNIQUE (ConversationID)
GO
ALTER TABLE Messageries.Conversation ADD CONSTRAINT DF_Conversation_NbMessages DEFAULT 0 FOR NbMessages
GO
```

### [Back To The Top](#sql)


---

## Emphasis

Add emphasis with asterisks '*' and underscores '_'
Two before and after (no spaces) a section of texts makes it bold 


**Bold Text with asterisks**

__Bold Text with underscores__

*Italicized Text with asterisks*

_Italicized Text with underscores_

You can also put Bold and Italicized text inline by surrounding a group of words.

 Example: This text is **bold** and this text is *italicized* 


> **TODO**. Create a bold sentence, an italicized sentence, and a sentence with both bold and italicized text inline

## Horizontal Rule
A horizontal rule gives a visible line break.  You can create one by putting three or more hypens, asterisks, or underscores (-, *, _).

For what it's worth, I prefer dashes...

 ---

 ***

 ___


## Lists

Create unordered lists using '-', '*', '+, 
<!-- 
    Example with each 

    - item
    * item
    + item
    - sdfsd
-->

You can create sublists by indenting
<!-- 
    Example

    - item
    - subitem
-->

Create ordered lists using a number prefix

<!-- 
    Example

    1. item 1
    2. item 2
    3. item 3 
-->

---

## Images

Defining an image is similar to defining a link, except you prefix it with '!'

<!-- 
    Example

    ![James Quick](https://pbs.twimg.com/profile_images/887455546890211329/tAoS7KUm_400x400.jpg) 
-->

Just like links, you can define images by reference in the same format.

Define the reference

<!-- 
    Example

    [profile]: https://pbs.twimg.com/profile_images/887455546890211329/tAoS7KUm_400x400.jpg 
-->

Use the reference

<!-- 
    Example

    ![James Quick][profile] 
-->

> **TODO** Create a reference link to your profile picture and then reference it.

---

## Code

You can do inline code with `backticks` (``)

You can do blocks of code by surroung it with 3 backticks (``` ```)

<!-- 
    Example

    ``` 
    var num = 0;
    var num2 = 0;
```
-->

> **TODO** Display a block of code from your favorite language

The above does not give language specific highlighting.  You can specify the programming language immediately following the opening 3 backticks.  You Should see a difference in highliting!


<!-- 
    Example Javascript

    ```javascript
    var num = 0;
    var num2 = 0;
    ``` 
-->

<!--
    Example HTML

    ```html 
    <div>
        <p>This is an html example</p>
    </div>
    ```
-->


---

## Tables
Tables are useful for displaying rows and columns of data.  Column headers can be defined in between pipes (|).  Headers are separated from table content with a row of dashes (-) (still separated by pipes), and there must be at least 3 dashes between each header.  The row data follows beneath (still separated by pipes).

<!-- 
    Example

    | Header 1    | Header 2    | Header 3    |
    | ----------- | ----------- | ----------- |
    | Row 1 Col 1 | Row 1 Col 2 | Row 1 Col 3 | 
-->


The column definitions and row definitions do not have to have the exact same width sizes, but it would be much more readable.  Notice the output of the following two tables are the same, but the second (the raw markdown) is much more readable.

<!-- 
    Example

    | Header 1 | Header 2 |
    | ----| ---|
    |Loooooooooooooong item 1 | looooooooooong item 2 | 
-->


<!-- 
    Example

    | Header 1                | Header 2              |
    | ----------------------- | --------------------- |
    |Loooooooooooooong item 1 | looooooooooong item 2 | -->

> **TODO** Create a table with three columns and two rows

You can also align (Center, left, right) the text in a column by using colons (:) in the line breaks between headers and rows.  No colon means the default **left alignment**.  Colons on each side signifies **center alignment**.  And a trailing colon means **right alignment**.

> **TODO** Create a table with three columns, one aligned left, one aligned center, and one aligned right

<!-- 
    Example
    
    | Header | Header 1 | Header 2  |
    | ------ | :------: | --------: |
    | Aligned Left | Aligned Center | Aligned Right | 
-->

---

## Custom HTML

Since MarkDown gets automatically converted to HTML, you can add raw HTML directly to your MarkDown.


```html 
<p>Sample HTML Div</p>
```

Creates this 

<p>Sample HTML Div</p>


---

## Custom CSS

You can also add custom CSS to your MarkDown to add additional styling to your document. You can also include CSS by including a style tag.

```html
    <style>
        body {
            color:red;
        }
    </>
```

---

## Additional Resources
- [Markdown Cheat Sheet - Adam P on Github](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#images)
- [Daring Fireball Markdown Syntax](https://daringfireball.net/projects/markdown/syntax)
- [MarkDown in Visual Studio Code](https://code.visualstudio.com/docs/languages/markdown)

<style>
#pink {color:#FA4098;}
.pink {color:#FA4098;}
.orchid {color:#DA70D6;}
.violet {color:#EE82EE;}
</style>

### [Back To The Top](#sql)