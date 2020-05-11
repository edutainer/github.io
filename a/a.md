---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults
title: "Simple CRUD Page By Mongo & Express Server"
layout: page
---

## app.js 

#### Egyszerű [mintaalkalmazás](https://github.com/scalagos0123/men-stack) szerveroldali kódja 

Töltsük be az express- és egyéb a szerver kommunikációját kezelő programkönyvtárakat:

{% highlight javascript %}
// importing all libraries needed for this project
var express = require("express"), MongoClient = require('mongodb').MongoClient,
 assert = require('assert'), bodyParser = require("body-parser"), ObjectID = require('mongodb').ObjectID;
{% endhighlight %}

{% highlight javascript %}
// getting instance of express
const app = express();
{% endhighlight %}

A HTTP kérések törzsében érkező üzenetek feldolgozása, történjen meg:

{% highlight javascript %}
// used body-parser library to properly decode the response body of POST
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: true}));
{% endhighlight %}

Az assets mappában elhelyezett statikus (itt, css és js) fájlok felhasználása:

{% highlight javascript %}
// serving assets folder to use the css and js files
app.use(express.static(__dirname + "/assets"));`
{% endhighlight %}

A MongoDB adatbázis szerver mydb nevű adatbázisához - alapértelmezés szerint - az alábbi címmel csatlakozhatunk:

{% highlight javascript %}
// default MongoDB server link (you can change `mydb` to your liking)
let url = "mongodb://127.0.0.1:27017/mydb";`
{% endhighlight %}

Az express szerver a 3000-es porton várja a kéréseket:

{% highlight javascript %}
// server will listen to port 3000
app.listen(3000);`
{% endhighlight %}

Az aktuális utvonal http://localhost:3000. Ez most a mappaszerkezet aktuális gyökere. Az app.get függvény első paramétere ezt jelöli ki. A 2. paraméter pedig a válaszadás során végrehajtandó függvény.
__dirname a projekt gyökérkönyvtárára mutat. 
Tehát a http://localhost:3000 címre érkező kérésre a projekt gyökér mappájának app.html fájlja lesz a válasz:
{% highlight javascript %}
// default address (say http://localhost:3000) will all be routed to this block.
app.get("/", (req, res) => {
	 res.sendFile(__dirname + "/app.html"); // uses the HTML file as output
});
{% endhighlight %}

 A "http://localhost:3000/user/show/:username" alakú kérésre, az url változóban lévő útvonal segítségével kapcsolódik az adatbázishoz, lekérdezi az abban rögzített felhasználók közül, a username változóban megadott nevű adatait. Majd visszaküldi a kliensnek. Végül lezárja a kapcsolatot:

{% highlight javascript %}
// this will show the details of the user given a username
app.get("/user/show/:username", (req, res) => {
    MongoClient.connect(url, (err, db) => {
        let user = db.collection("users").find({username: req.params.username});
        user.toArray((err, result) => {
            res.send(result);
            db.close();
        });
    });
});
{% endhighlight %}

A "http://localhost:3000/user/register" alakú kérésre, az url változóban lévő útvonal segítségével kapcsolódik az adatbázishoz, beszúrja a kérés body részében szereplő username ill. password mezők értékének megfelelő felhasználói adatokat tartalmazó dokumentumot. Majd válaszol a kliensnek. Végül lezárja a kapcsolatot:

{% highlight javascript %}
// this will insert a user to the database
app.post("/user/register", (req, res) => {
	MongoClient.connect(url, (err, db) => {
        let user = {username: req.body.username, password: req.body.password};
        db.collection("users").insertOne(user, (err, result) => {
			res.json({status: 200});
			db.close();
        });
   	});
});
{% endhighlight %}

A "http://localhost:3000/user/update" alakú kérésre, az url változóban lévő útvonal segítségével kapcsolódik az adatbázishoz, módosítja a kérés body részében szereplő _id mező értékének megfelelő felhasználó adatait, a kérés body rész username ill. password mezőjének értékére. Majd válaszol a kliensnek. Végül lezárja a kapcsolatot:

{% highlight javascript %}
// this will update the user given its `_id` as its parameter
app.post("/user/update", (req, res) => {
    MongoClient.connect(url, (err, db) => {
        db.collection("users").updateOne(
            // converting string to readable ObjectID because when you insert a document to a collection without specifying the ID, it uses this.
            {_id: ObjectID(req.body._id)},
            {$set: {username: req.body.username, password: req.body.password}
        });
        res.send();
        db.close();
    });
});
{% endhighlight %}

A "http://localhost:3000/user/remove" alakú kérésre, az url változóban lévő útvonal segítségével kapcsolódik az adatbázishoz, törli a kérés body részében szereplő _id mező értékének megfelelő felhasználó adatait. Majd válaszol a kliensnek. Végül lezárja a kapcsolatot:

{% highlight javascript %}
// this will remove a specific user to the collection given its `_id`
app.post("/user/remove", (req, res) => {
    MongoClient.connect(url, (err, db) => {
        db.collection("users").remove({_id: ObjectID(req.body._id)});
        res.send({status: 200});
        db.close();
    });
});
{% endhighlight %}

A "http://localhost:3000/user/all" kérésre, az url változóban lévő útvonal segítségével kapcsolódik az adatbázishoz, lekérdezi az abban rögzített valamennyi felhasználó adatait. Majd visszaküldi a kliensnek. Végül lezárja a kapcsolatot.


{% highlight javascript %}
// returns all users on a collection as JSON
app.get("/user/all", (req, res) => {
    MongoClient.connect(url, (err, db) => {
        let users = db.collection("users").find();
        users.toArray(function(err, result) {
            res.send(result);
            db.close();
        });
    });
});
{% endhighlight %}
