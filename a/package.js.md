# package.js

A "dependencies" szekció, a függőségeket határozza meg, vagyis azt, hogy mely szoftver komponensek szükségesek az alkalmazás futtatásához.
A mongodb 3.1.13-at megelőző verziói sérülékenyek a "szolgáltatás megtagadás" típusú támadásokkal szemben (is).
A repóból letöltött fájlban egy korábbi verzió van beállítva a "mongodb" kulcshoz rendelve.
Ezért figyelmeztet az npm csomagkezelő a mongodb komponensek telepítése során.
Ezt a problémát, úgy kezelhetjük legegyszerűbben, ha az idézőjelek közül töröljük az ott megadott verziószámot, az alábbi módon.
így, az npm az aktuális verziót fogja telepíteni, amit már biztonságosnak tekint!

```
{
  "name": "men-test",
  "version": "0.0.1",
  "dependencies": {
    "body-parser": "^1.18.2",
    "express": "^4.16.*",
    "mongodb": ""
  }
}
```
