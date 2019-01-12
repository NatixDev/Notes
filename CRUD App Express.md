#Initialisation

On oublie pas de faire un "npm init" pour créer notre fichier "package.json".
Ci-dessous, la procédure pour initialiser express, une page d'accueil avec res.render, la page se trouve dans un fichier "views" que l'on crée pour y stocker la page d'accueil et l'on retrouve celle-ci grâce au "app.set" qui sert à faire le lien entre les fichiers et l'application.

    var express = require("express");
    var app = express();
    
    app.set("view engine", "ejs");
    
    
    app.get("/", function(req, res){
        res.render("landing");
    });
    
    
    app.listen(process.env.PORT, process.env.IP, function(){
       console.log("YelpCamp Server has started !") 
    });

On oublie pas de permettre de pouvoir lancer l'application avec "app.listen".




#Deuxième page

On crée notre seconde page en y incluant sous forme d'objet le listing de notre contenu et on intègre cette variable via "{campgrounds: campgrounds}" 

    app.get("/campgrounds", function(req, res){
        var campgrounds = [
            {name: "Salmon Creek", image: "https://farm2.staticflickr.com/1424/1430198323_c26451b047.jpg"},
            {name: "Granite Hill", image: "https://farm9.staticflickr.com/8422/7842069486_c61e4c6025.jpg"},
            {name: "Mountain Goat's Rest", image: "https://farm3.staticflickr.com/2311/2123340163_af7cba3be7.jpg"}
        ];
        
        res.render("campgrounds", {campgrounds: campgrounds});
    });
    
Puis sur la page concernée :

    <% campgrounds.forEach(function(campground){ %>
    
        <div>
            <h4><%= campground.name %></h4>
            <img src="<%= campground.image %>">
        </div>
        
    <% }); %>
    
On y intègre le code suivant qui récupère les noms et images définies dans notre variable objet via le forEach. On oublie pas "<%= %>" dans la source ("src=''") afin de pouvoir avoir un rendu HTML et non pas un simple texte affichant le lien.




#Intégration header et footer (Layout).

Dans cette étape on intègre de manière dynamique le header et le footer nécessaire à la multitude de pages que nous pouvons avoir sans devoir répéter le même code.
Pour ce faire, on crée à l'intérieur du dossier "views" un dossier "partials" dans lequel on ajoute un fichier header.ejs et footer.ejs dans lesquels on inclut simplement du code HTML de base histoire d'avoir un squelette et que l'on divise dans ces deux fichiers.
Ensuite il ne reste plus qu'à spécifier cela sur nos pages via :

    En haut : <% include partials/header %>
    En bas : <% include partials/footer %>
    



#Intégration de la fonction POST



On installe tout d'abord body-parser (npm install body-parser --save).
Ensuite on l'intègre dans notre application avec le code ci-dessous : 

    Permet de l'inclure : var bodyParser = require("body-parser");
    
    Permet de l'exécuter pour l'utiliser : app.use(bodyParser.urlencoded({extended: true}));
    
body-parser permet de récupérer le contenu de nos "form" pour le manipuler dans l'application.


On utilise la même route "/campgrounds" pour la requête "POST", c'est une convention (REST).

    app.post("/campgrounds", function(req, res){
        
        // get data from form and add to campgrounds array
        // redirect back to campgrounds page
    });

On crée notre page pour récupérer les informations via notre formulaire (NOTE : On peut très bien créer un formulaire sur la page "/campgrounds" directement sans avoir besoin d'en créer une nouvelle) :
    
    // L'action renvoie à la page souhaitée, la méthode est POST car nous publions l'information récupérée, GET sert simplement à la récupérer pour la manipuler. Enfin on oublie pas "name='" afin de pouvoir cibler chaque information récupérée.
    <form action="/campgrounds" method="POST"> 
        <input type="text" name="name" placeholder="name">
        <input type="text" name="image" placeholder="image url"> 
        <button>Submit !</button>
    </form>
    
Puis on manipule cela dans notre application (app.js) :

    app.post("/campgrounds", function(req, res){
        // get data from form and add to campgrounds array
        
        var name = req.body.name; // req = la requête (req, res), name = (name="") définit dans le formulaire.
        var image = req.body.image;
        
        var newCampground = {name: name, image: image}
        campgrounds.push(newCampground);
        
        // redirect back to campgrounds page
        
        res.redirect("/campgrounds"); 
    });

On attribue une variable à chacune des informations récupérée (var name, var image),  
"res.redirect" par convention nous redirigera vers le lien du "app.get" et non pas du "app.post".
Enfin on définit un variable "newCampground" pour y intégrer les données au format de la variable "campground" précédente et les y ajouter via ".push()" :

    var campgrounds = [
        {name: "Salmon Creek", image: "https://farm2.staticflickr.com/1424/1430198323_c26451b047.jpg"},
        {name: "Granite Hill", image: "https://farm9.staticflickr.com/8422/7842069486_c61e4c6025.jpg"},
        {name: "Mountain Goat's Rest", image: "https://farm3.staticflickr.com/2311/2123340163_af7cba3be7.jpg"}
    ];

On oublie pas de sortir cette variable de l'ancienne fonction afin que celle-ci soit accessible par notre fonction "app.post".



#Ajouter Mongoose


On installe mongoose : 

    npm install mongoose --save


On l'ajoute à notre app.js :

    var express    = require("express"),
        app        = express(),
        bodyParser = require("body-parser"),
        mongoose   = require("mongoose")
        
(A noter que l'on peut disposer une virgule à la fin de la première variable et en écrire une nouvelle en-dessous sans devoir réecrire "var".)


Ensuite on ajoute ce code : 

    mongoose.connect("mongodb://localhost/yelp_camp");
    
Ce qui permet de se connecter à la base de données mongodb et créera la partie "yelp_camp" si celle-ci n'existe pas.


Pour installer la base de données sur Cloud 9, on se place dans notre workspace :


    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

Puis à la racide (~) en faisant "cd" :

    mkdir data
    echo "mongod --dbpath=data --nojournal" > mongod
    chmod a+x mongod

Pour lancer la base de données on ouvre sur une nouvelle fenêtre de terminal et on se place à la racine, ensuite on lance :

    ./mongod
    

Pour créer un schéma de base permettant d'ajouter correctement de nouvelles données à la base de données :

    var campgroundSchema = new mongoose.Schema({
        name: String,
        image: String
    });
    
Puis on valide ce schéma en l'incluant dans un modèle :

    var Campground = mongoose.model("Campground", campgroundSchema);


Enfin pour afficher les données directement sur l'application/site (dans ce cas les noms et images) on modifie notre ancien "/campgrounds" : 

    app.get("/campgrounds", function(req, res){
        
        // Récupère toute les données nécessaires à l'affichage sur l'application.
        
        Campground.find({}, function(err, allCampgrounds){
           if(err){
               console.log(err);
           } else {
                
                // Et enfin on envoie le rendu de ces données sur l'application, on oublie pas que "allCampgrounds" peut être n'importe quel nom, celui-ci étant défini après ".find()" afin de stocker les données de la base de données.
                
                res.render("campgrounds", {campgrounds:allCampgrounds});
           }
        });
    });


Il ne reste plus qu'à permettre d'envoyer des données dans la base de données, ce qui se fait avec :

    app.post("/campgrounds", function(req, res){
        // get data from form and add to campgrounds array
        var name = req.body.name;
        var image = req.body.image;
        var newCampground = {name: name, image: image};
        
        
    // La partie qui nous intéresse se trouve ci-dessous :
        
        
        Campground.create(newCampground, function(err, newlyCreated){
            if(err){
                console.log(err);
            } else {
                res.redirect("/campgrounds");
            }
        });
    });
    
On récupère les données du formulaire comme précédement dans la variable "newCampground" et on se sert de celle-ci directement dans la fonction ".create()".



#RESTFUL ROUTES 

name        url         verb    description
========================================================
INDEX       /dogs       GET     Display a list of all dog
NEW         /dogs/new   GET     Displays form to make a new dog
CREATE      /dogs       POST    Add new dog to DB
SHOW        /dogs/:id   GET     Shows info about one dog



On ajoute "/:id" afin de pouvoir afficher à l'avenir des détails provenant d'objets de la DB présent sur l'application :

    app.get("/campgrounds/:id", function(req, res) {
        res.send("This will be the show page one day !");
    });

Note : On oublie pas de placer cela après "/new", c'est important pour le bon fonctionnement.


Si l'on rajoute à notre schéma dans notre modèle une nouvelle valeur comme "description", que nous ne tenons pas aux anciennes données et que nous voulons les purger car elles n'ont pas de description, on utilise :

    db.campgrounds.drop()
    
    
On crée ensuite un fichier dans notre dossier "views" nommé "show.ejs" qui permettra d'accéder à l'information contenue dans les "/:id".

    touch views/show.ejs
    
On renomme également le fichier "campgrounds.ejs" en "index.ejs" afin de respecter la convention et on oublie pas de modifier dans le fichier app.js la ligne suivante : 

    res.render("index", {campgrounds:allCampgrounds});
    
On ajoute dans "index.ejs" à la suite de l'image et son nom :

<p>
    <a href="/campgrounds/<%= campground._id %>" class="btn btn-primary">More Info</a>
</p>

Note : "campground._id" va cibler l'ID de nos objets qui a été généré aléatoirement par mongodb sous le format "_id" d'où le "._id" un peu bizare
Note : Si l'on se pose la question de : "Comment trouve t-on directement l'id avant même d'en avoir fait la requête ?" C'est tout simplement car on a inclut l'ID dans la même boucle forEach que le nom et l'image, donc ils font partie du même objet.


On poursuit les modifications sur notre "/:id" :

    app.get("/campgrounds/:id", function(req, res) {
        
        //  Ici on utilise un nouvel outil : "findById" qui permet de cibler l'id dans la base de données.
        
        Campground.findById(req.params.id, function(err, foundCampground){
            if(err){
                console.log(err);
            } else {
            
                //render show template with that campground
                
                res.render("show", {campground: foundCampground});
            }
        });
    });


Enfin, on va aller ajouter dans "new.ejs" : 

<div class="form-group">
    <input class="form-control" type="text" name="description" placeholder="description">
</div>
    
Et dans app.js :

    var desc = req.body.description;
    var newCampground = {name: name, image: image, description: desc};


/////////////////////////////////////////////////////////////////////

                                V3 

/////////////////////////////////////////////////////////////////////


On exporte désormais une partie du code (le schéma) de "app.js" dans un fichier "campground.js" situé dans un dossier "models" :

    var mongoose = require("mongoose");
    
    // SCHEMA SETUP 
    
    var campgroundSchema = new mongoose.Schema({
        name: String,
        image: String,
        description: String
    });
    
    module.exports = mongoose.model("Campground", campgroundSchema);
    
On oublie pas de refaire un "require" de mongoose et de modifier notre "var Campground = mongoose.model..." par "module.exports = ..."

On retrouve notre "var Campground =" dans "app.js" mais cette fois-ci sous ce format :

    var Campground = require("./models/campgroud");

On fait appel à notre fichier de la même manière qu'on fait appel à Express, Mongoose etc...


# CREER UN ENVIRONNEMENT DE TEST 

Ici le programme est simple on crée une fonction qui supprime les données actuelles, en rajoute de nouvelles de test et prépare à l'intégration des commentaires :

    var data = [
            {
                name: "Cloud's Rest",
                image: "https://farm3.staticflickr.com/2311/2123340163_af7cba3be7.jpg",
                description: "Blah blah blah"
            },
            {
                name: "Desert Mesa",
                image: "https://farm2.staticflickr.com/1424/1430198323_c26451b047.jpg",
                description: "Blah blah blah"
            },
            {
                name: "Canyon Floor",
                image: "https://farm9.staticflickr.com/8457/7930235502_df747573ca.jpg",
                description: "Blah blah blah"
            }
        ];
    

    function seedDB(){
        // Remove all campgrounds
        Campground.remove({}, function(err){
            if(err){
                console.log(err);
            } else {
                console.log("removed campgrounds");
                //add a few campgrounds
                data.forEach(function(seed){
                    Campground.create(seed, function(err, campground){
                        if(err){
                            console.log(err);
                        } else {
                            console.log("added a campground");
                            //create a comment
                            Comment.create(
                                {
                                    text: "This place is great, but I wish there was internet",
                                    author: "Homer"
                                }, function(err, comment){
                                    if(err){
                                        console.log(err);
                                    } else {
                                        campground.comments.push(comment);
                                        campground.save();
                                        console.log("Created new comment");
                                    }
                                });
                        }
                    });
                });
            }
        });
    }
    
    
# MISE EN PLACE DES COMMENTAIRES TEST

On crée un fichier "comment.js" dans le dossier "models".

On y crée notre schéma :

    var commentSchema = mongoose.Schema({
       text: String,
       author: String
    });
    module.exports = mongoose.model("Comment", commentSchema);
    
Et on rajoute la partie "comments:" dans notre fichier "campground.js" :

    var campgroundSchema = new mongoose.Schema({
        name: String,
        image: String,
        description: String,
        comments: [
                {
                    type: mongoose.Schema.Types.ObjectId,
                    ref: "Comment"
                }
            ]
    });
    
Dans app.js on modifie :

    // SHOW - shows more info about one campground.
    app.get("/campgrounds/:id", function(req, res) {
        
        //La ligne modifiée
        Campground.findById(req.params.id).populate("comments").exec(function(err, foundCampground){

Afin d'y ajouter les commentaires.

Enfin on va modifier le fichier show.ejs afin d'y intégrer visuellement les commentaires :

    <% campground.comments.forEach(function(comment){ %>
        <p>
            <strong><%= comment.author %></strong> - <%= comment.text %>
        </p>
    <% }) %>


/////////////////////////////////////////////////////////////////////

                                V4

/////////////////////////////////////////////////////////////////////


#Commentaire New/Create (Nested routes)


INDEX   /campgrouds
NEW     /campgrouds/new
CREATE  /campgrounds
SHOW    /campgrouds/:id

NEW     /campgrouds/:id/comments/new    GET
CREATE  /campgrouds/:id/comments        POST


C'est le moment où l'on va créer la fonction d'ajout de commentaires, pour faire cela, on va créer dans "app.js" le code suivant :

    app.get("/campgrounds/:id/comments/new", function(req, res) {
        //find campground by id
        Campground.findById(req.params.id, function(err, campground){
            if(err){
                console.log(err);
            } else {
                 res.render("comments/new", {campground: campground});
            }
        });
    });
    
On constate que pour le lien "/comments/new" on a besoin d'un nouveau fichier "new.ejs" afin d'y ajouter le formulaire pour les commentaires...
Il existe déjà un fichier "new.ejs" pour ajouter de nouveaux "campground"...
Pour corriger cela, dans le dossier "views" on va créer 2 dossiers : 

    - campgrounds

On y transfère les fichiers "index.ejs", "new.ejs", "show.ejs" car ils concernent tous les campgrounds.

    - comments

On y crée notre nouveau fichier "new.ejs"

Et enfin on laisse "landing.ejs" là où il était, à la racine du dossier "views".


Une fois cela fait, on oublie pas de faire les modifications des liens vers les fichiers :

    res.render("campgrounds/show"...
    res.render("campgrounds/new"...
    res.render("campgrounds/index"...

Ainsi que dans les fichiers qu'on a deplacé (On y rajoute "../" pour que nos fichiers puissent retrouver le dossier "partials" situé un cran au-dessus) :

    <% include ../partials/header %>
    <% include ../partials/footer %>


Ensuite on crée notre formulaire, rien de spécial, très similaire au premier "new.ejs" destiné aux campgrounds, on y modifie seulement certaines choses :

<form action="/campgrounds/<%= campground._id %>/comments" method="POST">
    <div class="form-group">
        <input class="form-control" type="text" name="comment[text]" placeholder="text">
    </div>
    <div class="form-group">
        <input class="form-control" type="text" name="comment[author]" placeholder="author">
    </div>
    
Et on termine dans le fichier "app.js" en créant notre fonction "app.post" pour les commentaires afin de valider l'ajout du commentaire via le formulaire créé précédemment :

    app.post("/campgrounds/:id/comments", function(req, res){
       //lookup campground using ID
       Campground.findById(req.params.id, function(err, campground) {
           if(err){
               console.log(err);
               res.redirect("/campgrounds");
           } else {
              Comment.create(req.body.comment, function(err, comment){
                 if(err){
                     console.log(err);
                 } else {
                     campground.comments.push(comment);
                     campground.save();
                     res.redirect("/campgrounds/" + campground._id);
                 }
              });
           }
       });
    });
    

/////////////////////////////////////////////////////////////////////

                                V5

/////////////////////////////////////////////////////////////////////
    

#Styling Comments

On crée un dossier public ou l'on y incorpore un dossier "stylesheets" avec un fichier "main.css" à l'intérieur.

Pour faire le lien avec le fichier css et l'application, on utilise ceci :

    app.use(express.static(__dirname + "/public"));
    
"__dirname" fait simplement référence au dossier racine où se situe notre fichier "app.js", cela évite de devoir réecrire tout le répertoire et cela permet de sécuriser également.




/////////////////////////////////////////////////////////////////////

                                V6

/////////////////////////////////////////////////////////////////////


# Mise en place de passport et du User Model

On installe nos nouveaux packages afin d'implémenter la possibilité de Login :

    npm install passport passport-local passport-local-mongoose express-session --save

Et on ajoute dans "app.js" : 

    passport = require("passport"),
    LocalStrategy = require("passport-local"),
    User = require("./models/user"),
    
On crée donc un fichier "user.js" dans le dossier "models" dans lequel on code ceci :

    var mongoose = require("mongoose");
    var passportLocalMongoose = require("passport-local-mongoose");
    
    var UserSchema = new mongoose.Schema({
        username: String,
        password: String
    });
    
    UserSchema.plugin(passportLocalMongoose);
    
    module.exports = mongoose.model("User", UserSchema);
    

On met donc notre schéma en place, on intègre le système "passport" pour le Login et enfin "UserSchema.plugin(passportLocalMongoose);" s'occupe de faire le lien pour nous.


# Configuer passport

On met en place la configuration de Passport :

    // Permet la mise en place de sessions où on stocke des données que l'on définira visible ou non en fonction de l'accès.
        app.use(require("express-session")({
            secret: "Future Rich Self Made Man with a success application !",
            resave: false,
            saveUninitialized: false
        }));
        app.use(passport.initialize());
        app.use(passport.session());
    // Permet d'aller rechercher en local notre fichier "user.js" et son schéma afin de s'en servir dans notre méthode d'authentification
        passport.use(new LocalStrategy(User.authenticate()));
    // Permet d'encrypter et désencrypter les informations pour la base de données et la sécurite.
        passport.serializeUser(User.serializeUser());
        passport.deserializeUser(User.deserializeUser());

Les informations dans les commentaires (// Permet de...)

On crée nos "routes" pour l'authentification :
On commence par créer un fichier "register.ejs" dans le dossier "views" puis on code ceci :

    // show register form
    app.get("/register", function(req, res) {
        res.render("register");
    });

Ici on s'occupe simplement du rendu de notre fichier qu'on complète avec le formulaire suivant :

<form action="/register" method="POST">
    <input type="text" name="username" placeholder="username">
    <input type="password" name="password" placeholder="password">
    <button>Sign Up</button>
</form>

Enfin on s'occupe du code qui permet l'inscription du nouvel utilisateur dans la base de données :

    // handle sign up logic
    app.post("/register", function(req, res) {
        var newUser = new User({username: req.body.username});
        User.register(newUser, req.body.password, function(err, user){
           if(err){
               console.log(err);
               return res.render("register");
           } 
           passport.authenticate("local")(req, res, function(){
              res.redirect("/campgrounds"); 
           });
        });
    });
    
On note ici l'utilisation de User.register (à la place de .save par exemple, le .register étant une nouvelle option ajoutée par passport-local-mongoose).


# Login

* get
* fichier login
* 

On crée nos "routes" pour le Login :
On commence par créer un fichier "login.ejs" dans le dossier "views" et auquel on y ajoute notre formulaire :
Le formulaire est le même que pour le register, seule l'action change "/login"

Ci-dessous le .get pour afficher notre formulaire de connexion/login :

    // show login form
    app.get("/login", function(req, res) {
        res.render("login");
    });


Ci-dessous la vérification à l'aide d'un middleware des valeurs du formulaire pour permettre la connexion et la redirection ou non :


    // handling login logic 
    app.post("/login", passport.authenticate("local",
        {
            successRedirect: "/campgrounds",
            failureRedirect: "/login"
        }), function(req, res) {
    });
    

# Logout/Navbar

Pour se déconnecter, il suffit d'utiliser les lignes suivantes :


    app.get("/logout", function(req, res) {
        req.logout();
        res.redirect("/campgrounds");
    });

Ensuite on pense à rajouter le header et le footer aux fichiers "register.ejs" et "login.ejs" et on rajoute les bons liens à la barre de navigation :

/login
/register
/logout

Dans le fichier "header.ejs".


Enfin pour finir, on veut empêcher un utilisateur non connecté de publier un commentaire.
C'est très simple, on crée la fonction isLoggedIn :

    function isLoggedIn(req, res, next){
        if(req.isAuthenticated()){
            return next();
        }
        res.redirect("/login");
    }
    
Celle-ci vérifie si l'utilisateur est connecté ou non, si non, elle le renvoie au login
Mais pour ça, il faut se servir de notre fonction en tant que middleware, sur les lignes suivantes :

    app.get("/campgrounds/:id/comments/new", isLoggedIn, function(req, res) {
    app.post("/campgrounds/:id/comments", isLoggedIn, function(req, res){



# Afficher ou masquer Login/Register/Logout


Pour afficher ou masquer nos différents liens de redirection :

<% if(!currentUser){ %>
    <li><a href="/login">Login</a></li>
    <li><a href="/register">Sign Up</a></li>
<% } else { %>
    <li><a href="#">Signed In As <%= currentUser.username %></a></li>
    <li><a href="/logout">Logout</a></li>
<% } %>

On sert simplement de "if" et "else" sur notre barre de navigation dans le fichier "header.ejs"
Le "currentUser.username" permettant d'accéder au nom de l'utilisateur connecté.
On définit cette vérification comme ceci :

(Après le code de configuration de passport)

    app.use(function(req, res, next){
       res.locals.currentUser = req.user; 
       next();
    });

On définit cette fonction qui va permettre de faire une requête utilisateur sur toutes nos pages sans avoir besoin de faire ceci pour chaque route :

    res.render("campgrounds/index", {campgrounds:allCampgrounds, currentUser: req.user});
    
On avait ajouté cela : "", currentUser: req.user", mais une fois notre fonction définit cela n'est plus nécessaire.


/////////////////////////////////////////////////////////////////////

                                V7

/////////////////////////////////////////////////////////////////////


# REFACTOR / Optimisation du code

On crée un nouveau dossier "routes" dans lequels on crée les fichiers suivants :

campgrounds.js
index.js
comments.js

Ensuite on copie les codes correspondants de "app.js" dans ces nouveaux fichiers :

Le Auth et la page d'accueil (landing) dans le fichier index.js


Puis dans "app.js" on ajoute ces 3 lignes pour faire le lien avec nos nouveaux fichiers :

    var commentRoutes    = require("./routes/comments"),
        campgroundRoutes = require("./routes/campgrounds"),
        indexRoutes      = require("./routes/index");
        
        

Et on va modifier dans nos 3 nouveaux fichiers les parties suivantes :

    var express = require("express");
    var router = express.Router();

Ainsi avec "router", tous nos "app.get/post" vont être modifiés comme ceci :

    router.get
    router.post

Et enfin on exporte cela :

    module.exports = router;




On retourne dans "app.js" et on met en place l'utilisation des fichiers à la fin du fichier :

    app.use(indexRoutes);
    app.use(campgroundRoutes);
    app.use(commentRoutes);
    
Puis on copie depuis "index.js" notre fonction "isLoggedIn" qu'on colle dans "campgrounds.js" et "comments.js".

Enfin dans "comments.js", on ajoute le code suivant :

    var Campground = require("../models/campground");
    var Comment = require("../models/comment");
    
Afin que nos modèles liés à la base de données soient retrouvés.

Dans "index.js" : 

    var passport = require("passport");
    var User = require("../models/user");
    
Et dans "campgrouds.js" simplement : 

    var Campground = require("../models/campground");



On va maintenant simplifier nos liens en modifiant ceci dans "app.js" :

    app.use("/campgrounds", campgroundRoutes);
    app.use("/campgrounds/:id/comments", commentRoutes);

On a rajouté le chemin directement après notre app.use, de ce fait, on peut désormais supprimer les chemins dans "campgrounds.js" et "comments.js" :

Exemple 

    "/campgrounds/:id/comments/new" deviendra "/new"

Cependant pour le fichier "comments.js", il faut préciser cela :

    var router = express.Router({mergeParams: true});
    
Cela permettra de récupérer les paramètres propres à la partie "/:id" sans causer d'erreurs.



# Users + Comments 


On commence par modifier notre Schéma utilisateur : 

    var commentSchema = mongoose.Schema({
       text: String,
       author: {
          id: {
             type: mongoose.Schema.Types.ObjectId,
             ref: "User"
          },
          username: String
       }
    });

On supprime le contenu de la database provenant de "seeds.js" et on le désactive en le commentant dans "app.js" :

    //Seed the database
    // seedDB();
    
Puis on va dans le dossier "views/comments" dans le fichier "new.ejs" et on supprime :

<div class="form-group">
    <input class="form-control" type="text" name="comment[author]" placeholder="author">
</div>

Nous ne postons plus manuellement le nom de l'utilisateur, il sera récupéré automatiquement.

Puis on va ajouter dans le dossier "routes" dans le fichier "comments.js" :

    router.post("/", isLoggedIn, function(req, res){
       //lookup campground using ID
       Campground.findById(req.params.id, function(err, campground) {
           if(err){
               console.log(err);
               res.redirect("/campgrounds");
           } else {
              Comment.create(req.body.comment, function(err, comment){
                 if(err){
                     console.log(err);
                 } else {

Cette partie :                 
                 
                    //add username and id to comment
                    comment.author.id = req.user._id;
                    comment.author.username = req.user.username
                    // save comment
                    comment.save();

Et dans le fichier "show.ejs" on va modifier simplement cette ligne :

    <strong><%= comment.author.username %></strong>
    
On y ajoute .username en rapport à ceci :

    author: {
      id: {
         type: mongoose.Schema.Types.ObjectId,
         ref: "User"
      },
      
      // ICI
      username: String
      


# Users + Campgrounds

On va rajouter "isLoggedIn" cette fois-ci dans campground pour limiter l'usage aux utilisateurs connectés.

Puis on va modifier notre schéma utilisateur pour dans le fichier "models/campground.js" :

    var campgroundSchema = new mongoose.Schema({
        name: String,
        image: String,
        description: String,
        author: {
            id: {
                type: mongoose.Schema.Types.ObjectId,
                ref: "User"
            },
            username: String
        },
        comments: [
                {
                    type: mongoose.Schema.Types.ObjectId,
                    ref: "Comment"
                }
            ]
    });

Puis de retour dans le dossier "routes" et le fichier "campgrounds.js" on ajoute dans :

    router.post("/", isLoggedIn,function(req, res){
        // get data from form and add to campgrounds array
        var name = req.body.name;
        var image = req.body.image;
        var desc = req.body.description;
        
Ceci :

        var author = {
            id: req.user._id,
            username: req.user.username
        };
        var newCampground = {name: name, image: image, description: desc, author: author};

Et on termine dans notre fichier "show.ejs" en ajoutant le nom d'utilisateur :

<p>
    <em>Submitted By <%= campground.author.username %></em>
</p>



/////////////////////////////////////////////////////////////////////

                                V10

/////////////////////////////////////////////////////////////////////



# Editing Campgrounds

On effectue l'installation de "method-override" :

    npm install method-override --save
    
Et on l'ajoute à "app.js" : 
    
    methodOverride = require("method-override"),
    app.use(methodOverride("_method"));
    
Ensuite on ouvre notre fichier "routes/campgrounds.js" et on y glisse le code suivant :

    router.get("/:id/edit", function(req, res) {
        Campground.findById(req.params.id, function(err, foundCampground){
            if(err){
                res.redirect("/campgrounds");
            } else {
                res.render("campgrounds/edit", {campground: foundCampground});
            }
        });
    });

Et on crée un fichier "views/campgrounds/edit.ejs" que l'on complète avec le formulaire suivant :

<h1 style="text-align: center;">Edit <%= campground.name %></h1>
        
        <div style="width: 30%; margin: 25px auto;">
        
            <form action="/campgrounds/<%= campground._id %>?_method=PUT" method="POST">
                <div class="form-group">
                    <input class="form-control" type="text" name="name" placeholder="name">
                </div>
                <div class="form-group">
                    <input class="form-control" type="text" name="image" placeholder="image url">
                </div>
                <div class="form-group">
                    <input class="form-control" type="text" name="description" placeholder="description">
                </div>
                <div class="form-group">
                    <button class="btn btn-lg btn-primary btn-block">Submit !</button>
                </div>
            </form>
            
La ligne importante :

<form action="/campgrounds/<%= campground._id %>?_method=PUT" method="POST">

    ?_method=PUT : Permet l'envoi du formulaire avec la méthode POST tout en prenant en compte le PUT qui permet d'update.

Puis on modifie encore le formulaire ainsi avec les "value" qui sont les valeurs déjà éxistantes que l'on veut éditer :

    <input class="form-control" type="text" name="campground[name]"              value="<%= campground.name %>">
    <input class="form-control" type="text" name="campground[image]"             value="<%= campground.image %>">
    <input class="form-control" type="text" name="campground[description]"       value="<%= campground.description %>">

La raison pour laquelle le name est égal à "campground[]" est tout simplement pour intégrer les 3 noms à un objet qu'on récupérera comme ceci :

    req.params.campground 

Plutôt que de devoir écrire :

    req.body.name + req.body.image + ...

Et enfin on crée dans "campgrounds.js" notre Update route :

    router.put("/:id", function(req, res){
       // find and update the correct campground
       Campground.findByIdAndUpdate(req.params.id, req.body.campground, function(err, updatedCampground){
           if(err){
               res.redirect("/campgrounds");
           } else {
               // redirect somewhere (show page)
               res.redirect("/campgrounds/" + req.params.id);
           }
       });
    });
    
    
# Deleting Campgrounds


On ajoute au fichier "routes/campgrounds.js" : 

    // DESTROY CAMPGROUND ROUTE
    router.delete("/:id", function(req, res){
        Campground.findByIdAndRemove(req.params.id, function(err){
            if(err){
                res.redirect("/campgrounds");
            } else {
                res.redirect("/campgrounds");
            }
        })
    });

Et au fichier "show.ejs" : 

<form id="delete-form" action="/campgrounds/<%= campground._id %>?_method=DELETE" method="POST">
    <button class="btn btn-danger">Delete</button>
</form>

On fait l'usage d'un formulaire afin d'envoyer la requête de suppression : ?_method=DELETE


#Authorization 

Un gros ajout dans le fichier "routes/campgrounds.js" : 

    function checkCampgroundOwnership(req, res, next){
        // La fonction vérifie si l'utilisateur est connecté :
        
        if(req.isAuthenticated()){
            Campground.findById(req.params.id, function(err, foundCampground){
                if(err){
                    res.redirect("/campgrounds");
                } else {
                
                    // Compare si le post appartient à l'utilisateur connecté
                    
                    if(foundCampground.author.id.equals(req.user._id)) {
                        next();
                    } else {
                        res.redirect("back");
                    }
                }
            });
        } else {
            res.redirect("back");
        }
    }

Ici on a 3 points importants, notre fonction est un middleware qui check l'authentification : "req.isAuthenticated()"
Compare l'utilisateur et l'auteur : foundCampground.author.id.equals(req.user._id)
Et redirige en arrière si ce n'est pas correct : "back"


Enfin on ajoute le middleware à notre route "Edit" et on supprime le nécessaire pour avoir ce rendu :

    router.get("/:id/edit", checkCampgroundOwnership, function(req, res) {
            Campground.findById(req.params.id, function(err, foundCampground){
                res.render("campgrounds/edit", {campground: foundCampground});
            });
    });

On ajoute ensuite : checkCampgroundOwnership à nos ".put" et ".delete" également.

Et on va masquer nos boutons :

<% if(currentUser && campground.author.id.equals(currentUser._id)){ %>
    <a class="btn btn-warning" href="/campgrounds/<%= campground._id %>/edit">Edit</a>
    <form id="delete-form" action="/campgrounds/<%= campground._id %>?_method=DELETE" method="POST">
        <button class="btn btn-danger">Delete</button>
    </form>
<% } %>

Le "currentUser &&" permet d'éviter une errer dans le cas où aucun utilisateur n'est connecté, il n'éxécutera le code que dans le cas ou les 2 sont OK. Sans ça, on aura un message d'erreur nous indiquant qu'il ne trouve pas le code correspondant car il n'est pas declenché sans utilisateur connecté.



#Editing Comments

On commence par ajouter un bouton dans "show.ejs" qui permet d'éditer un commentaire :

<a  class="btn btn-xs btn-warning" 
    href="/campgrounds/<%= campground._id %>/comments/<%= comment._id %>/edit">
    Edit
</a>


Puis on crée un nouveau fichier "views/comments/edit.ejs" et on y copie le formulaire de "new.ejs" qui se situe dans le même dossier dans notre nouveau fichier avec les modifications suivantes :

<form action="/campgrounds/<%= campground_id %>/comments/<%= comment._id %>?_method=PUT" method="POST">
    <div class="form-group">
        <input class="form-control" type="text" name="comment[text]" value="<%= comment.text %>">
    </div>
    <div class="form-group">
        <button class="btn btn-lg btn-primary btn-block">Submit !</button>
    </div>
</form>

campground._id devient campground_id car on ne nécessite plus les même informations de Campground, on veut simplement récupérer l'id
Les autres modifications restent simples : 
?_method=PUT : Pour la modification.
value="" : Car on édite et donc on récupère les anciennes informations.


Ensuite on va ajouter notre Edit pour les commentaires dans le fichier "routes/comments.js" : 

    router.get("/:comment_id/edit", function(req, res){
        Comment.findById(req.params.comment_id, function(err, foundComment) {
            if(err){
                res.redirect("back");
            } else {
                res.render("comments/edit", {campground_id: req.params.id, comment: foundComment});
            }
        })
    });

Comment.findById va trouver l'ID du commentaire dans la DB et l'inscrire dans foundComment qui sera transmis à "comment" qui est le nom attribué dans le fichier "edit.ejs".

/!\ La raison pour laquelle req.params.id est égal à l'ID des campgrounds et req.params.comment_id aux commentaires est la suivante :

Dans notre route pour campground l'ID est défini ainsi : campgrounds/:id ce qui donne params.id
Pour les commentaires : comments/:comment_id 
Donc params.comment_id
Seuls les ":" disparaissent.

Et enfin on permet de modifier un commentaire en ajoutant notre route Update : 

    router.put("/:comment_id", function(req, res){
        Comment.findByIdAndUpdate(req.params.comment_id, req.body.comment, function(err, updatedComment){
            if(err){
                res.redirect("back");
            } else {
                res.redirect("/campgrounds/" + req.params.id);
            }
        });
    });




# Delete Comments


On ajoute la route DELETE :

// COMMENT DESTROY ROUTE

    router.delete("/:comment_id", function(req, res){
       //findByIdAndRemove
       Comment.findByIdAndRemove(req.params.comment_id, function(err){
           if(err){
               res.redirect("back");
           } else {
               res.redirect("/campgrounds/" + req.params.id);
           }
       });
    });
    
Et notre formulaire avec le bouton delete dans "show.ejs" : 

<form class="delete-form" action="/campgrounds/<%= campground._id %>/comments/<%= comment._id %>?_method=DELETE" method="POST">
    <input type="submit" class="btn btn-xs btn-danger" value="Delete">
</form>


#Authorization for Comments 

On crée la fonction(middleware) suivante dans le fichier "routes/comments.js" :

    function checkCommentOwnerShip(req, res, next){
        if(req.isAuthenticated()){
            Comment.findById(req.params.comment_id, function(err, foundComment){
                if(err){
                    res.redirect("/campgrounds");
                } else {
                    // does user own the comment ?
                    if(foundComment.author.id.equals(req.user._id)) {
                        next();
                    } else {
                        res.redirect("back");
                    }
                }
            });
        } else {
            res.redirect("back");
        }
    }
    
Et on l'ajoute comme middleware à nos : .get .put .delete

/!\ Uniquement le .get qui concerne l'édition, ceux de la création par exemple .post n'ont pas besoin de cette fonction, ils utilisent isLoggedIn et n'appartiennent pas à un utilisateur précis.



Et enfin on cache les boutons pour les utilisateurs qui ne possèdent pas les commentaires :

<% if(currentUser && comment.author.id.equals(currentUser._id)){ %>
    <a  class="btn btn-xs btn-warning" 
        href="/campgrounds/<%= campground._id %>/comments/<%= comment._id %>/edit">
        Edit
    </a>
    <form class="delete-form" action="/campgrounds/<%= campground._id %>/comments/<%= comment._id %>?_method=DELETE" method="POST">
        <input type="submit" class="btn btn-xs btn-danger" value="Delete">
    </form>
<% } %>


# Refactoring / Optimisation

On va créer un dossier "middleware" avec un fichier "index.js" en son sein, dans ce nouveau fichier on y ajoute nos fonctions :

    checkCampgroundOwnership
    checkCommentOwnerShip
    isLoggedIn

Et on les supprime bien évidemment de leur ancien fichier, ce qui donnera la chose suivante :


    var middlewareObj = {};
    
    middlewareObj.checkCampgroundOwnership = function(req, res, next){
        if(req.isAuthenticated()){
            Campground.findById(req.params.id, function(err, foundCampground){
                if(err){
                    res.redirect("/campgrounds");
                } else {
                    // does user own the campground ?
                    if(foundCampground.author.id.equals(req.user._id)) {
                        next();
                    } else {
                        res.redirect("back");
                    }
                }
            });
        } else {
            res.redirect("back");
        }
    }

    
    middlewareObj.checkCommentOwnerShip = function(req, res, next){
        if(req.isAuthenticated()){
            Comment.findById(req.params.comment_id, function(err, foundComment){
                if(err){
                    res.redirect("/campgrounds");
                } else {
                    // does user own the comment ?
                    if(foundComment.author.id.equals(req.user._id)) {
                        next();
                    } else {
                        res.redirect("back");
                    }
                }
            });
        } else {
            res.redirect("back");
        }
    }
    
    middlewareObj.isLoggedIn = function(req, res, next){
        if(req.isAuthenticated()){
            return next();
        }
        res.redirect("/login");
    }
    
    
    
    module.exports = middlewareObj;


Le plus important ici est de retenir qu'on crée un Objet nommé "middlewareObj" et on y stocke nos fonctions, puis on exporte tout simplement l'objet, qu'on ira "require" de la façon suivante dans les fichiers où l'on a supprimé nos fonctions pour les ajouter au nouveau fichier :

    var middleware = require("../middleware");
    
On ne précise pas ""../middleware/index.js" car "index.js" est une convention et un fichier portant se nom est trouvé automatiquement par express.


Enfin pour utiliser notre nouveau fichier de fonctions, on va ajouter à nos routes qui se servent de ces fonctions "middleware.*nom de la fonction", exemple :

    router.delete("/:comment_id", middleware.checkCommentOwnerShip, function(req, res){...
    router.get("/new", middleware.isLoggedIn, function(req, res) {


Et on oublie pas dans notre nouveau fichier (middleware/index.js) de faire un require de nos models :

    var Campground = require("../models/campground");
    var Comment = require("../models/comment");
    

/////////////////////////////////////////////////////////////////////

                                V11

/////////////////////////////////////////////////////////////////////


# Flash messages

On installe "connect-flash" :

    npm install --save connect-flash
    
Et on le met en place :    
    
    flash = require("connect-flash"),
    app.use(flash());
    
Puis on va se diriger vers notre dossier middleware avec notre "index.js", à la fonction isLoggedIn on va y ajouter la ligne suivante :

    middlewareObj.isLoggedIn = function(req, res, next){
        if(req.isAuthenticated()){
            return next();
        }
        
        //Celle-ci
        req.flash("error", "Please Login First !");
        
        res.redirect("/login");
    }

Son but est de s'exécuter/afficher un message uniquement lorsque la fonction se déclenche, puis on va modifier notre route Login dans "routes/index.js" :

    router.get("/login", function(req, res) {
        res.render("login", {message: req.flash("error")});
    });

On y ajoute : "{message: req.flash("error")}" pour qu'elle permette d'enregistrer le message d'erreur sous la valeur "message".
Valeur qu'on va utiliser ici dans "login.ejs" :

<h1><%= message %></h1>

Au final on déplace notre : <h1><%= message %></h1> dans le "header.ejs" afin qu'il soit accessible sur toutes les pages, on le place avec </nav>.

Puis pour qu'il n'y ai pas de bug, car "message" n'est pas accessible sur toutes les pages, on va supprimer dans "routes/index.js" la ligne suivante :

    {message: req.flash("error")}
    
Et dans "app.js", on ajoute :

    app.use(function(req, res, next){
       res.locals.currentUser = req.user;
       // CETTE LIGNE CI-DESSOUS
       
       res.locals.message = req.flash("error");
       next();
    });

Qui rend le tout accessible partout.


Ensuite pour le "logout" on va dans notre fichier "routes/index.js" et on ajoute :

    router.get("/logout", function(req, res) {
        req.logout();
        // CETTE LIGNE CI-DESSOUS
        
        req.flash("success", "Logged you out !");
        res.redirect("/campgrounds");
    });

Puis pour le style on modifie ceci dans notre header :

<div class="container">
    <div class="alert alert-danger" role="alert">
        <%= message %>
    </div>
</div>

Puis on retourne modifier et ajouter dans "app.js" ces lignes :

    res.locals.error = req.flash("error");
    res.locals.success = req.flash("success");

.error et .success sont simplement là pour provoquer le rendu dans "header.ejs" c'est pour cela que l'on va aller modifier une nouvelle fois notre header comme ceci :

<div class="alert alert-danger" role="alert">
    <%= error %>
</div>
<div class="alert alert-success" role="alert">
    <%= success %>
</div>


Enfin on termine en modifiant encore comme ceci :

<div class="container">
    <% if(error && error.length > 0){ %>
        <div class="alert alert-danger" role="alert">
            <%= error %>
        </div>
    <% } %>
    <% if(success && success.length > 0){ %>
        <div class="alert alert-success" role="alert">
            <%= success %>
        </div>
    <% } %>
</div>

Cela permet d'afficher uniquement le message d'erreur ou de succès si un message est contenu dans ".error" ou ".success", d'où le ".length" qui va servir à vérifier si la valeur est au-dessus de 0 ce qui signifierait qu'il y a un message et donc exécute le "if"


Note : On ne va pas s'amuser à ajouter partout dans notre code des req.flash("error", "message"); ou req.flash("success", "message"); 
Mais il faut savoir qu'on peut les ajouter comme ceci partout pour chaque intéraction d'un utilisateur.

Le point important ici est le code suivant dans "routes/index.js" : 

    router.post("/register", function(req, res) {
        var newUser = new User({username: req.body.username});
        User.register(newUser, req.body.password, function(err, user){
           if(err){
               req.flash("error", err.message);
               return res.redirect("/register");
           } 
           passport.authenticate("local")(req, res, function(){
              req.flash("success", "Welcome to YelpCamp " + user.username);
              res.redirect("/campgrounds"); 
           });
        });
    });
    
Ici on va ajouter dans le cas d'une erreur la requête flash "error" avec le message suivant sous ce format : err.message

On récupère simplement les informations contenues dans la fonction par (err, user){} affiliés eux-même à passport notre framework pour l'authentification

err.message va aller collecter les informations dans passport en cas d'erreur de différents types.



# GROSSE CORRECTION D'UN PROBLEME DE SECURITE

Dans "middleware/index.js" il va falloir faire une légère modification :

    middlewareObj.checkCampgroundOwnership = function(req, res, next){
        if(req.isAuthenticated()){
            Campground.findById(req.params.id, function(err, foundCampground){
                if(err || !foundCampground){

On a ajouté "|| !foundCampground", la raison à cela est d'éviter qu'un utilisateur qui rentre un mauvais lien/id passe l'erreur, si l'id n'est donc pas bon, il ne trouve pas le campground, donc ne passe pas l'erreur et la fonction d'erreur s'exécute normalement.

On fait de même ici ("routes/campgrounds.js") :

    router.get("/:id", function(req, res) {
        //find the campground with provided ID
        Campground.findById(req.params.id).populate("comments").exec(function(err, foundCampground){
            if(err || !foundCampground){
                req.flash("error", "Campground not found !");
                res.redirect("back");

Dans "middleware/index.js"

    middlewareObj.checkCommentOwnerShip = function(req, res, next){
        if(req.isAuthenticated()){
            Comment.findById(req.params.comment_id, function(err, foundComment){
                if(err || !foundComment){
                    req.flash("error", "Comment not found");
                    res.redirect("back");


Et enfin dans "routes/comments.js" :

    router.get("/:comment_id/edit", middleware.checkCommentOwnerShip, function(req, res){
        Campground.findById(req.params.id, function(err, foundCampground){
            if(err || !foundCampground){
                req.flash("error", "No campground found !");
                return res.redirect("back");
            }
            Comment.findById(req.params.comment_id, function(err, foundComment) {
                if(err){
                    res.redirect("back");
                } else {
                    res.render("comments/edit", {campground_id: req.params.id, comment: foundComment});
                }
            });
        });
    });

Ici on a modifié une grosse partie, on a ajouté un Campground.findById afin de vérifier avant que l'id du campground est correct, si ce n'est pas le cas on est redirigé avec l'erreur, sinon il poursuit le code qu'on a intégré à la suite.


Ces simples vérifications permettent d'éviter des erreurs qui font crash l'application.


/!\ La raison du crash : findById, cherche une valeur, si il ne trouve pas, il retourne la valeur "null", qui reste malgré tout une valeur, donc pas d'erreur, le code se poursuit avec la valeur "null" qui ne permet pas au reste du code d'avoir les informations nécessaires à son bon fonctionnement.


#Dynamic Prices 

Partie simple mais bonne révision :

Dans "models/campground.js" on rajoute "price" comme valeur :

    var campgroundSchema = new mongoose.Schema({
        name: String,
        price: String,

Dans "campgrounds/new.ejs" on ajoute un nouvel input :

<div class="form-group">
    <input class="form-control" type="number" name="price" placeholder="price" min="0.01" step="0.01">
</div>


min="0.01" step="0.01" : "min" donne la valeur minimum que l'on peut entrer, "step" permet de mettre un compteur qui permet de monter ou descendre de 0.01 à chaque fois pour ajuster son prix par exemple.


Ensuite on va faire pareil pour "edit.ejs" :

 <div class="form-group">
    <input class="form-control" type="number" name="campground[price]" value="<%= campground.price %>" min="0.01" step="0.01">
</div>

La valeur devient "campground.price"

Puis dans "show.ejs" on modifie simplement la valeur neutre mise par la valeur dans la DB : :

<h4 class="pull-right">$<%= campground.price %>/night</h4>

Et enfin dans "routes/campgrounds.js" on ajoute notre variable "price" et on fait le lien entre celle-ci et le "price" dans les fichiers ".ejs"/"frontend" :

    router.post("/", middleware.isLoggedIn,function(req, res){
        // get data from form and add to campgrounds array
        var name = req.body.name;
        var price = req.body.price
        var image = req.body.image;
        var desc = req.body.description;
        var author = {
            id: req.user._id,
            username: req.user.username
        };
        var newCampground = {name: name, price: price, image: image, description: desc, author: author};


#GIT


git init : Permet d'initialiser Git dans le dossier où l'on exécute cette ligne de code.

ls -a : Permet de montrer les fichiers cachés comme ".git" par exemple.

git status : Permet d'avoir des informations sur nos fichiers déjà sauvegardés ou non trackés par exemple.

git add + nom du fichier : Permet de l'ajouter à liste de fichier à sauvegarder avec "commit".

git commit : Permet de "sauvegarder" les fichiers dans une version, à chaque "commit" une nouvelle version, ce qui permet de suivre tous les changements dans le temps.

git commit -m "texte message" : Permet d'ajouter un message à notre "commit" pour détailler ce qui a été mis à jour afin de suivre correctement l'évolution de notre application dans le temps.

git add . : Permet d'ajouter tous les fichiers d'un coup pour le "commit".


git log : Permet d'accéder à tous nos anciens commit avec la date, le message du commit ainsi qu'un ID pour le commit.

Note : Cet ID est important pour la suite.

git checkout + ID : Lors d'un "git log", si l'on veut revenir à un commit spécifique, on copie l'ID du commit et on l'utilise dans cette commande.

Note : Cela nous fait quitter la branche "Master" et nous place dans une branche alternative "HEAD", exemple : Git/git_intro((5cdd932...).


        HEAD 
   O -> O -> O -> O -> O -> O
                            Master


git checkout master : Permet de retourner à la branche principale.



Pour revenir à un ancien "commit" et en faire à nouveau le principal (pour par exemple revenir à nos anciens fichiers avant qu'une grosse erreur ne soit apparue.) :

    git revert --no-commit 85299e445b17d6c49e22f291530b0309a356ffcd..HEAD
    
Ici on a récupéré l'ID du commit que l'on veut récupérer en tant que principal et on lui ajoute "..HEAD" à la fin.

Enfin il ne reste plus qu'à refaire un commit une fois la commande précédente exécutée afin de valider le tout :

    git commit -m "revert back to cats commit before dogs commit"
    

Si l'on a fait un "git revert" mais qu'on veut revenir en arrière :

    git revert --abort
    
    
    
    

# GIT ET GITHUB COURS AVANCE

// * Working Directory * //

- Area where all of our files and directories and changes are living all the time.

// * Staging Area * //

- Files and directories that we explicitly add to the staging area
L'endroit où l'on effectue nos "add" sans qu'ils soient déjà "commit" (zone neutre/tampon).

// * Git Repository * //

- Where all our snapshots/instantané are stored


// * Ignoring files * //


git add *.html : Cela ajoutera tous les fichiers portant l'extension ".html", cela est valable pour tous les types d'extension.

Note : "*" représente "ALL/tous les fichiers du type après '*'."


git add -A : Ajoutera tous les fichiers/dossiers d'un seul coup pour le "commit".


Si l'on a ajouté un fichier avec "git add" mais qu'on désire s'en séparer au final avant le commit pour qu'il ne soit pas "sauvegardé", on utilise :

    git reset HEAD + nom du fichier
    exemple : git reset HEAD test.txt
    

Pour ignorer des fichiers/dossiers afin qu'ils ne soient jamais ajoutés (git add) ou affichés (git status) et ne perturbent pas notre lisibilité, on crée un fichier :

    .gitignore
    exemple : touch .gitignore
    
Dans ce fichier, on ajoutera le nom des fichiers ou dossiers à ignorer :

    exemple : test.txt
    

(Petit parenthèse, si l'on se trompe sur le nom d'un fichier lors d'un "touch", on peut modifier celui-ci avec un "mv", exemple : 
    mv tes.txt test.txt
Le nouveau nom sera : test.txt)




// * GIT BRANCHES * //



- Listing all branches

git branch : Permet d'afficher nos branches

- Adding a branch

git checkout -b + nom de la branche : Permet de créer une nouvelle branche
exemple : git checkout -b test : Créera la branche "test".

- Changing branch

git checkout + nom de la branche : Permet d'utiliser la branche désirée.
exemple : git checkout master : Permet d'utiliser la branche "master".

- Merging a branch

Être dans une autre branche est semblable à être dans un autre dossier, si au début on a copié les fichiers du premier dans le deuxième dossier, les données seront similaires, 
en revanche, si l'on fait des modifications dans le deuxième dossier, celles-ci n'existeront pas dans le premier dossier.
L'inverse est valable également, un modification du premier, n'existera pas dans le deuxième.

L'intérêt de tout cela, est d'avoir notre dossier/branche principal "master" et d'effectuer nos tests, par exemple la création d'une nouvelle fonctionnalité, un formulaire, un design etc... dans un dossier/branche "test".
Lorsque nos tests ont été concluants et qu'il n'y a plus de bugs, on retourne dans notre dossier/branche "master" et on tape ceci :

    git merge test
    
Cela va fusionner notre dossier/branche "test" avec notre dossier/branche "master" afin d'avoir toutes les nouveautés faites dans "test" sur notre application par exemple, sans en avoir perturbé le fonctionnement dans le passé avec nos tests !



Exemple de branches et merging :

            /--- 0 --- 0 --- 0 ---- 0
           /                       /    
    0 --- 0 --- 0 --- 0 --- 0 --- 0


- Removing a branch

Pour supprimer une branche :

    git branch -d + nom de la branche
    exemple : git branch -d test : Supprime la branche "test"
    


Enfin, pour faire le lien avec Github, on va créer un "Repository" sur Github et récupérer l'adresse de celui-ci pour l'affilier à notre application ici avec le code suivant :

    git remote add origin https://github.com/NatixDev/intro-to-git.git

L'adresse est celle du repository, pour chacune de nos applications/sites/frameworks etc... on créera un repository différent qui aura donc une adresse différente :

    https://github.com/NatixDev/nom du repository

Et on termine en envoyant toutes les données sur GitHub avec cette commande :

    git push -u origin master
    


/////////////////////////////////////////////////////////////////////

                                V12 DEPLOYED

/////////////////////////////////////////////////////////////////////



# DEPLOIEMENT DE YELPCAMP VERS HEROKU !


On commence par faire un :

    git init

A la racine de notre fichier "app.js"


Puis on fait :

    git add .
    
Afin d'ajouter l'ensemble des fichiers pour le "commit".


Puis on fait :

    git commit -m "initial deploy commit"
    
Afin de deployer nos ajouts.


Puis on fait :

    heroku create
    
Afin de créer notre lien et l'affilier à nos fichiers.


Ensuite on va dans notre fichier "package.json" afin d'y ajouter la ligne suivante sous l'objet "script" :

    "start": "node app.js"
    
Qui permet de dire à Heroku comment lancer notre application.


Comme l'on a changé notre fichier "package.json", on refait :

    git add package.json
    git commit -m "add start script"


Et enfin on envoie le tout à Heroku :

    git push heroku master


On va créer un compte sur mLab pour y héberger la base de données et on y récupère le lien de celle-ci et on remplace l'ancienne en local dans "app.js" par ceci :

Juste avant on va dans Users et on crée un utilisateur pour la DB... 

    mongoose.connect("mongodb://Paul:Paul1995@ds211588.mlab.com:11588/natixcamp", {useMongoClient: true});


Afin de définir notre base de données de travail en local pour nos modifications on entre la ligne de code suivante dans la console :

    export DATABASEURL=mongodb://localhost/yelp_camp_v10

Et on modifie dans "app.js" cette ligne :

    mongoose.connect(process.env.DATABASEURL, {useMongoClient: true});

Qui permet de relier notre commande (DATABASEURL, qui peut être n'importe quel nom) à notre ligne de code dans "app.js".



Puis pour la base de données officielle, on va sur le site d'heroku sur notre application, dans settings et on crée une variable :

    KEY : DATABASEURL
    VALUE : L'adresse de la base de données sur mLab

OU on peut utiliser cette manière en ligne de commande :

    heroku config:set DATABASEURL=adresse de la base de données sur mLab











