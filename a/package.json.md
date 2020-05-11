[Simple SPA](https://github.com/scalagos0123/men-stack)
## package.json

A "dependencies" szekció, a függőségeket határozza meg, vagyis azt, hogy mely szoftver komponensek szükségesek az alkalmazás futtatásához.
A mongodb kliens, 3.1.13-at megelőző verziói sérülékenyek a "szolgáltatás megtagadás" típusú támadásokkal szemben (is).
A repóból letöltött fájlban egy korábbi verzió van beállítva a "mongodb" kulcshoz rendelve.
Ezért figyelmeztet az npm csomagkezelő a mongodb komponensek telepítése során.
Sajnos, ez a program, nem fut a 3.1.13-as verzióval! Ezért, csak a kód módosításával tudunk megszabadulni a szóban forgó figyelmeztetéstől.

```
{
  "name": "men-test",
  "version": "0.0.1",
  "dependencies": {
    "body-parser": "^1.18.2",
    "express": "^4.16.*",
    "mongodb": "^2.3.*"
  }
}
```
[Kliens kód](https://edutainer.github.io/github.io/a/client#) | [Szerver kód](https://edutainer.github.io/github.io/a/a#)
