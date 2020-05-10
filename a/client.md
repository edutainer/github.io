```
<!DOCTYPE HTML>
<html>
    <head>
        <title>Sample SPA</title>
        <link rel="stylesheet" href="/css/bootstrap.min.css" type="text/css">
        <style>
            body {
                font-family: "Segoe UI";
            }
        </style>
    </head>
    <body>
```    
A tartalmat befoglaló elemek (nyitó tag-ek):
```
        <div class="container" style="margin-top: 40px">
            <div class="row">
```
Navigáció a sidebar-on:
```
                <div class="col-xs-2 sidebar">
                    <ul class="nav nav-pills nav-stacked">
                        <li class="active"><a href="#" data-action="all">Users</a></li>
                        <li><a href="#" data-action="create">Create a user</a></li>
                    </ul>
                </div>
```
A megjelenő adatokat tartalmazó elem (nyitó tag).
```
                <div class="col-xs-8 content" style="border-left: 1px lightgray dashed">
```
A tárolt felhasználói adatok táblázata:
```
                    <div class="all" style="display: block">
                        <table class="table table-striped table-bordered">
                            <thead>
                                <tr>
                                    <th>Username</th>
                                    <th>Password</th>
                                    <th>Actions</th>
                                </tr>
                            </thead>
                            <tbody></tbody>
                        </table>
                    </div>
```
A rögzítendő új felhasználói adatrekordok (Mongo terminológiával dokumentumok) adatait küldő form:
```
                    <div class="create" style="display: none;">
                        <form action="/user/create" method="POST">
                            <div class="row">
                                <div class="col-xs-4">
                                    <div class="form-group">
                                        <label for="">Username</label>
                                        <input type="text" class="form-control" name="username" required>
                                    </div>
                                </div>
                                <div class="col-xs-4">
                                    <div class="form-group">
                                        <label for="">Password</label>
                                        <input type="password" class="form-control" name="password" required>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="col-xs-4">
                                    <button type="submit" class="btn btn-primary">Submit</button>
                                </div>
                            </div>
                        </form>
                    </div>
```
A frissítendő felhasználói adatrekordok (Mongo terminológiával dokumentumok) adatait küldő form:
```
                    <div class="update" style="display:none;">
                        <form action="/user/update" method="post" id="update-form">
                            <input type="hidden" name="_id">
                            <div class="row">
                                <div class="col-xs-4">
                                    <div class="form-group">
                                        <label for="">Username</label>
                                        <input type="text" class="form-control" name="username" required>
                                    </div>
                                </div>
                                <div class="col-xs-4">
                                    <div class="form-group">
                                        <label for="">Password</label>
                                        <input type="password" class="form-control" name="password" required>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="col-xs-4">
                                    <button type="submit" class="btn btn-primary">Update</button>
                                </div>
                            </div>
                        </form>
                    </div>
```
A megjelenő adatokat tartalmazó elem, (záró tag):
```
                </div>
```
A tartalmat befoglaló elemek (záró tag-ek): 
```
            </div>
        </div>
```        
A szkriptek:
```
        <script src="/js/jquery.js"></script>
        <script src="/js/bootstrap.min.js"></script>
        <script>
```
_A **frissítő adatokat** küldő **form input mezőit** feltöltjük._

 - a **name='_id'** attribútumú **input** mezőt, a paraméterként **megadott html-elem** **"data-id"** attribútumával
 - a **name='username'** attribútumú **input** mezőt, ha - mint például a jelen esetben - a paraméterül **kapott "element"**, egy **táblázatcella elem**, akkor a vele egy táblázat sorban lévő **első cella** tartalmával.
 - a name='password' attribútumú input mezőt, ha - mint például a jelen esetben - a paraméterül **kapott "element"**, egy **táblázatcella elem**, akkor a vele egy táblázat sorban lévő **második cella** tartalmával.

{% highlight javascript %}

            function updateUser(element) {
                $(".update form input[name='_id']").val($(element).attr("data-id"));
                $(".update form input[name='username']").val($(element).parent().parent().children(":first-child").text());
                $(".update form input[name='password']").val($(element).parent().parent().children(":nth-child(2)").text());
                $(".content > *").hide();
                $(`.content > .update`).show();
            }

{% endhighlight %}

Az alábbi refreshUsers() függvény, get kéréssel lekéri az adatbázis táblában (Mongo terminológiával kollekció) szereplő adatokat, a szerver "/user/all" útvonaláról (route). A done függvény, egy tömb adatszerkezetben, a data paraméterben kapja meg a lekért adatokat. Minden tömbelem egy objektum, ami egy rekordnak/dokumentumnak felel meg.

A done függvény kiüríti a tábla törzsét (tbody), majd soronkat hozzáfűzve újratölti azt.

Minden sor utolsó cellájába egy Update és egy Delete nyomógomb ("a" html elem - más néven link - gombként formázva) kerül. A sor többi cellájába, a data tömb soron lévő elemének, mint objektumnak a mezői (username, password) kerülnek. Minden mező más-más cellába.

A nyomógombok kattintásakor az "updateUser(this)" ill. a "deleteUser(this)" függvényhívás történik meg. Vagyis, paraméterként magát a nyomógomb elemet adjuk át.

{% highlight javascript %}

            function refreshUsers() {
                $.get("/user/all")
                .done((data) => {
                    $(".all tbody *").remove();
                    for (let i = 0; i < data.length; i++) {
                        $(".all tbody").append(
                            `<tr>
                                <td>${data[i].username}</td>
                                <td>${data[i].password}</td>
                                <td><a href="#" data-action="update" onclick="updateUser(this)" data-id="${data[i]._id}">Update</a>
                                &nbsp; | &nbsp;
                                <a href="#" data-action="delete" onclick="deleteUser(this)" data-id="${data[i]._id}">Delete</a></td>
                            </tr>`
                        );
                    }
                });
            }

{% endhighlight %}

Az alábbi deleteUser(element) függvény, post kérést küldve a szerver "/user/remove" útvonalára (route), megadva a törlendő elem azonosítóját, arra utasítja az express szervert, hogy törölje az adott azonosítóval rendelkező rekordot/dokumentumot. Sikeres adatbázis művelet esetén, a done függvény frissiti a felhasználói adatokat a fenti refreshUsers() függvény hívásával.

{% highlight javascript %}

            function deleteUser(element) {
                $.post("/user/remove", {_id: $(element).attr("data-id")})
                .done((data) => {
                    if (data.status == 200) {
                        refreshUsers();
                    }
                });
            }

{% endhighlight %}

Az ".create" osztályhoz rendelt elemben található form elem ( az egyik fent látható form) submit eseménykezelőjének alábbi definíciója a következőket határozza meg.

A rögzítendő új felhasználói adatrekordok (Mongo terminológiával dokumentumok) adatait küldő form submit gombjának kattintásakor meghívódik egy névtelen függvény, mely post kérést küldve a szerver "/user/register" útvonalára (route), átadva a form input elemeiben szereplő adatokat, arra utasítja az express szervert, hogy szúrjon be egy adatrekordot/dokumentumot az adatbázis táblába/kollekcióba. Sikeres adatbázis művelet esetén, a done függvény frissiti a felhasználói adatokat a fenti refreshUsers() függvény hívásával. Aktiválja a sidebar users gombjának, - az alábbi snippetet követő code snippet-ben látható - click eseményét. Majd törli a username és password input mezőket.

{% highlight javascript %}

            $(".create form").submit(() => {
                $.post("/user/register", { username: $(".create form input[name='username']").val(), password: $(".create form input[name='password']").val()})
                .done((data) => {
                    if (data.status == 200) {
                        refreshUsers();
                        $(".sidebar li:first-child").trigger("click");
                        $("input[name='username'], input[name='password']").val("");
                    }
                });
                return false;
            });

{% endhighlight %}

Az ".sidebar" osztályhoz rendelt elemben található li elemek ( fentebb láthatóak ) click eseménykezelőinek alábbi definíciója a következőket határozza meg.

A sidebar valamelyik li elemének kattintásakor, a többit passzívvá teszi az active osztályból való kizárásával. Csak s kattintott elem lesz aktív. Az oldal tartalmának, a kattintott li elemben lévő "a" elem, "data-action" attribútumában szereplő értéknek megfelelő osztályú elemeit jeleníti csak meg, a többit elrejti.

{% highlight javascript %}
            
            $(".sidebar li").click(function() {
                $(".sidebar li").removeClass("active");
                $(this).addClass("active");

                let dataAction = $(this).children("a").attr("data-action");

                $(".content > *").hide();
                $(`.content > .${dataAction}`).show();
            });

{% endhighlight %}

Az "update-form" azonosítóhoz rendelt elem ( az egyik fent látható form) submit eseménykezelőjének alábbi definíciója a következőket határozza meg.

A frissítendő felhasználói adatrekordok (Mongo terminológiával dokumentumok) adatait küldő form submit gombjának kattintásakor meghívódik egy névtelen függvény, mely post kérést küldve a szerver "/user/update" útvonalára (route), átadva a form input elemeiben szereplő adatokat, arra utasítja az express szervert, hogy frissítsen egy adatrekordot/dokumentumot az adatbázis táblában/kollekcióban. Sikeres adatbázis művelet esetén, a done függvény frissiti a felhasználói adatokat a fenti refreshUsers() függvény hívásával. Majd aktiválja a sidebar users gombjának az előző code snippet-ben látható click eseményét.

{% highlight javascript %}

            $("#update-form").submit(() => {
                console.log("Updating...");
                $.post("/user/update", { 
                    _id: $("#update-form input[name='_id']").val(),
                    username: $("#update-form input[name='username']").val(),
                    password: $("#update-form input[name='password']").val()
                }).done((data) => {
                    refreshUsers();
                    $(".sidebar li:first-child").trigger("click");
                });
                return false;
            });
            refreshUsers();
            
{% endhighlight %}

```
        </script>
    </body>
</html>
```
