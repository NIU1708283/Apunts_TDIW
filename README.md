# APUNTS TDIW – Tecnologies de Desenvolupament per a Internet i Web

> Guia i plantilla de resolució per a l'examen (Tema 1: HTML5, CSS, JavaScript/AJAX, PHP i MVC).
> Elaborada a partir de les sessions de problemes P1–P5 (repositori `MCarmen/tdiw-2627`, branques `p1`…`p5` i les solucions `p1-s`…`p5-s`) i dels problemes tipus examen dels cursos 22/23, 23/24 i 24/25.

---

## Índex

0. [Com és l'examen i com atacar-lo](#0-com-és-lexamen-i-com-atacar-lo)
1. [HTML5](#1-html5)
2. [CSS](#2-css)
3. [JavaScript i AJAX](#3-javascript-i-ajax)
4. [PHP](#4-php)
5. [Accés a base de dades PostgreSQL des de PHP](#5-accés-a-base-de-dades-postgresql-des-de-php)
6. [Arquitectura MVC](#6-arquitectura-mvc)
7. [Flux complet client ↔ servidor](#7-flux-complet-client--servidor)
8. [Plantilles de resolució per tipus d'exercici](#8-plantilles-de-resolució-per-tipus-dexercici)
9. [Resum dels exàmens anteriors](#9-resum-dels-exàmens-anteriors)
10. [Errors freqüents i trampes](#10-errors-freqüents-i-trampes)
11. [Xuleta final](#11-xuleta-final)

---

## 0. Com és l'examen i com atacar-lo

### 0.1 Estructura habitual del problema (6 punts)

Els tres problemes tipus examen segueixen **sempre el mateix patró**: es dóna una web (captures de pantalla + descripció de comportament), el fitxer principal (`enquesta.php` / `forum.php`) que **no es pot modificar**, i la sortida HTML que generen els controladors. Es demana:

| Exercici | Què es demana | Punts | Fitxers que es completen |
|---|---|---|---|
| 1 | Regles CSS (mínim nombre) + **layout** responsive (grid o flex) + a vegades animacions/sticky | 1 – 1,5 | `/css/xxx.css` |
| 2 | Completar la **MVC**: controlador(s) + model + vista perquè es generi la sortida HTML donada | 1,5 – 2 | `/controladors/c_*.php`, `/models/m_*.php`, `/vistes/v_*.php` |
| 3 | Funció **JavaScript asíncrona (AJAX amb fetch)** que crida un controlador i substitueix contingut del DOM | 1 – 2 | `/js/funcions.js` |
| 4 | Pàgina PHP que **rep el formulari** (`resposta.php`), inclòs el **nom del fitxer** (ve de l'atribut `action` del `<form>`) | 1 | `/resposta.php` |

### 0.2 Mètode de resolució (sempre el mateix)

1. **Llegeix el fitxer principal** i subratlla: `id`, `class`, atributs `style="grid-area:..."`, `onclick/onchange/onkeyup/onsubmit`, `action` del `form`, `name` dels inputs, rutes dels `include_once`.
2. **Llegeix la sortida HTML esperada** dels controladors: et diu exactament què ha d'imprimir la vista (quines etiquetes, quins camps de la BD van a cada lloc).
3. **Mira les captures**: decideix si el layout és `grid` (zones amb nom) o `flex` (fila/columna) i quina és la condició de la `@media`.
4. **CSS**: una regla per cada grup de propietats compartides (agrupa selectors amb comes per minimitzar regles).
5. **MVC**: el controlador sempre és "include model → connectar → consultar → include vista". El model sempre és "SQL → `pg_query` → `pg_fetch_all` → return". La vista sempre és "HTML + `foreach` + `echo`".
6. **JS**: `async function` → `fetch(url?param=valor)` → `await .text()` → `document.getElementById(...).innerHTML = ...`.
7. **resposta.php**: copia la capçalera de la pàgina principal, i mostra `$_POST['name_del_input']`.

---

## 1. HTML5

### 1.1 Esquelet mínim d'un document

```html
<!DOCTYPE html>                     <!-- declaració HTML5 -->
<html lang="ca">
<head>
    <meta charset="utf-8" />        <!-- codificació de caràcters (accents!) -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0" /> <!-- responsive -->
    <title>Títol de la pestanya</title>
    <link rel="stylesheet" type="text/css" href="css/estils.css" />   <!-- CSS extern -->
    <script src="https://code.jquery.com/jquery-4.0.0.js"></script>   <!-- llibreria externa (jQuery) -->
    <script src="js/funcions.js"></script>                            <!-- JS propi -->
</head>
<body>
    <!-- Comentari HTML -->
    ...
</body>
</html>
```

**Regles d'or**
- `<link>` del CSS va al `<head>`. L'ordre dels `<script>` importa: la llibreria (jQuery) abans del fitxer que la fa servir.
- Un `<script>` al `<head>` s'executa abans que existeixi el DOM → per accedir a elements cal fer-ho dins d'una funció cridada per un event, o dins `$(document).ready(...)`, o posar el script just abans de `</body>` (com a `p0`).
- Les rutes relatives es resolen respecte al **fitxer que el navegador ha carregat** (`index.php`), no respecte al fitxer PHP inclòs.

### 1.2 Etiquetes estructurals / semàntiques

| Etiqueta | Ús | Observacions |
|---|---|---|
| `<header>` | Capçalera (de la pàgina o d'una secció) | Pot aparèixer diverses vegades (una dins de cada `<section>`) |
| `<section>` | Bloc temàtic de contingut | S'usa com a "caixa" a què donar estil o `grid-area` |
| `<footer>` | Peu de pàgina | Copyright, imatge… |
| `<nav>`, `<article>`, `<aside>`, `<main>` | Altres semàntiques HTML5 | |
| `<div>` | Contenidor genèric sense semàntica | Usat com a **contenidor del layout** (`<div id="layout">`, `<div id="container">`) i com a **destí de l'AJAX** (`<div id="formDiv">`, `<div id="J1">`) |
| `<span>` | Contenidor genèric en línia | |
| `<hr />` | Línia horitzontal separadora | Element buit |
| `<br />` | Salt de línia | Element buit |

### 1.3 Text

| Etiqueta | Ús |
|---|---|
| `<h1>` … `<h6>` | Títols jeràrquics (`<h1 class="titolGran">`, `<h2 class="titolPetit">`, `<h3>`) |
| `<p>` | Paràgraf |
| `<em>` | Èmfasi (cursiva semàntica) — als exàmens se li dóna color via `.titolGran em` |
| `<strong>` | Importància (negreta semàntica) |
| `<a href="URL" target="_blank">` | Enllaç; `target="_blank"` obre en pestanya nova. Pot embolcallar una imatge: `<a href="..."><img .../></a>` |
| `&copy;` | Entitat HTML per ©. Altres: `&lt;` `&gt;` `&amp;` `&nbsp;` |

### 1.4 Imatges

```html
<img src="img/uab.png" width="200px" alt="Logo UAB" id="campus-e" />
```
- `src` (ruta relativa o absoluta), `width`/`height`, `alt` (text alternatiu), `id` per a animar-la amb CSS o seleccionar-la amb JS.
- Element buit (no té etiqueta de tancament; `<img></img>` apareix al repositori però no és correcte).

### 1.5 Llistes

```html
<ul>                                  <!-- llista no ordenada (bullets) -->
    <li><a href="...">Enginyeria Informàtica</a>
        <ul>                          <!-- llista niuada: va DINS del <li> pare -->
            <li>Menció Enginyeria del software</li>
            <li><a href="mencions/TI/index.html">Menció TI</a></li>
        </ul>
    </li>
    <li>Enginyeria Química</li>
</ul>
<ol>                                  <!-- llista ordenada (1, 2, 3…) -->
    <li>Primer</li>
</ol>
```

### 1.6 Taules

```html
<table border="1">
    <tr>                                        <!-- fila -->
        <th colspan="2">Escola d'Enginyeria</th> <!-- cel·la de capçalera que ocupa 2 columnes -->
        <th colspan="2">Ciències</th>
    </tr>
    <tr>
        <td>Enginyeria Informàtica</td>          <!-- cel·la de dades -->
        <td>300</td>
        <td>Matemàtiques</td>
        <td>200</td>
    </tr>
    <tr>
        <td>Eng. Sistemes de Telecomunicació</td>
        <td>150</td>
        <td rowspan="2">Física</td>              <!-- cel·la que ocupa 2 files -->
        <td rowspan="2">100</td>
    </tr>
    <tr>
        <td>Enginyeria Química</td>
        <td>300</td>                             <!-- aquesta fila només té 2 <td>: els altres 2 venen del rowspan -->
    </tr>
</table>
```
- `colspan="n"` → la cel·la s'estén n columnes. `rowspan="n"` → s'estén n files (les files següents tenen menys cel·les).
- Opcionals: `<thead>`, `<tbody>`, `<tfoot>`, `<caption>`.
- Vores: l'atribut `border="1"` és antic; millor CSS `table, th, td { border: 1px solid black; border-collapse: collapse; }`.

### 1.7 Formularis (molt important)

```html
<form method="post" action="registre.php" onsubmit="return confirmaRegistre();">
    <label for="nom">Nom complet:</label>
    <input type="text" name="nom" id="nom" size="15" maxlength="7" required /><br />

    <label for="clau">Password:</label>
    <input type="password" name="clau" id="clau" /><br />

    <label for="graus">Grau:</label>
    <select name="grau" id="graus" onchange="carregaMencions()">
        <option value="1">Enginyeria Informàtica</option>
        <option value="2" selected>Enginyeria Química</option>
    </select>

    <textarea id="inputResposta" name="resposta" rows="10" cols="200" required></textarea>

    <input type="text" id="inputNom" name="nom" readonly value="Cognoms, Nom" />
    <input type="button" value="3" onclick="puntua('J1', 3);" />
    <button type="button" onclick="ampliaMissatge(0);">Veure més</button>

    <input type="submit" id="inputEnviar" value="ENVIAR" disabled />
</form>
```

**`<form>`**
- `method="post"` → dades al cos de la petició; al servidor `$_POST`. `method="get"` → dades a la URL `?nom=valor&...`; al servidor `$_GET`. `$_REQUEST` funciona amb tots dos.
- `action="resposta.php"` → **fitxer del servidor que rep les dades** (és el nom del fitxer que et demanen a l'exercici 4). `action=""` → s'envia a la mateixa pàgina.
- `onsubmit="return funcio();"` → si la funció retorna `false` **no s'envia** el formulari (validació / tractament en client).

**`<input type="...">`** — element buit. L'atribut **`name` és la clau** amb la qual arriba el valor al servidor (`$_POST['nom']`); l'**`id`** és per a CSS/JS.

| `type` | Què és |
|---|---|
| `text` | Caixa de text d'una línia |
| `password` | Text ocult |
| `submit` | Botó que envia el formulari (`value` = text del botó) |
| `button` | Botó que NO envia; es combina amb `onclick` |
| `reset`, `checkbox`, `radio`, `number`, `email`, `date`, `hidden`, `file` | Altres tipus |

Atributs útils: `value` (valor inicial / text del botó), `size` (amplada visible), `maxlength` (màxim de caràcters), `readonly` (no editable però **s'envia**), `disabled` (desactivat i **no s'envia**), `required` (validació HTML5 obligatòria), `placeholder`.

**`<select name="..." id="..."><option value="...">Text</option></select>`** — desplegable. El servidor rep el **`value`** de l'`option` seleccionada (no el text). Els `<option>` sovint es generen amb PHP (foreach sobre resultats de la BD) i es **substitueixen via AJAX** (`select.innerHTML = options`).

**`<textarea rows="" cols="">`** — text multilínia; sí que té etiqueta de tancament.

**`<label for="id_input">`** — etiqueta associada a un camp (clicar-la enfoca el camp). També s'usa sense `for`.

**`<button type="button">`** — botó amb contingut HTML; `type="button"` evita que actuï com a submit dins d'un form.

### 1.8 Atributs globals i events

| Atribut | Ús |
|---|---|
| `id="..."` | Identificador **únic**. Selector CSS `#id`. JS: `document.getElementById("id")`. jQuery: `$("#id")` |
| `class="..."` | Classe (reutilitzable, pot haver-n'hi diverses separades per espai). Selector CSS `.classe` |
| `style="grid-area: titol"` | CSS en línia (als exàmens, defineixen la zona del grid de cada bloc) |
| `onclick="f();"` | En fer clic |
| `onchange="f();"` | En canviar el valor d'un `select`/`input` |
| `onkeyup="f();"` | En deixar anar una tecla (validar mentre s'escriu) |
| `onsubmit="return f();"` | En enviar un formulari |
| `onload`, `onmouseover`, `oninput`, `onblur`, `onfocus` | Altres events |

---

## 2. CSS

### 2.1 Com s'aplica

```html
<link rel="stylesheet" type="text/css" href="css/uab.css">   <!-- 1) fitxer extern (preferit) -->
<style> h1 { color: red; } </style>                          <!-- 2) bloc dins <head> -->
<h1 style="color: red;">...</h1>                             <!-- 3) en línia (atribut style) -->
```
Prioritat (cascada): en línia > `#id` > `.classe` / `[atribut]` > element. A igual especificitat, guanya l'última regla escrita.

### 2.2 Sintaxi

```css
selector1, selector2 {        /* agrupació: una sola regla per a diversos selectors */
    propietat: valor;         /* cada declaració acaba amb ; */
    /* comentari CSS */
}
```

### 2.3 Selectors (els que surten als exàmens)

| Selector | Significat | Exemple d'ús |
|---|---|---|
| `h1` | Tots els elements `h1` | `h1 { color: darkblue; }` |
| `h1, h3` | Agrupació (h1 **i** h3) | `header, footer { background-color: darkgray; }` |
| `header p` | **Descendent**: tot `p` dins d'un `header` (a qualsevol nivell) | `section.statistics header h3 { color: red; }` |
| `#layout > header` | **Fill directe** | `#container > header, #container > section { ... }` |
| `.fancy` | Qualsevol element amb `class="fancy"` | `.titolGran, .titolPetit { font-weight: bold; }` |
| `header.fancy` | Element `header` **amb** classe `fancy` | `h1.titolGran { font-size: 2.5em; }` |
| `.titolGran em` | `em` dins d'un element de classe `titolGran` | `.titolGran em, .titolPetit em { color: darkred; }` |
| `#jocs` | Element amb `id="jocs"` | `#layout { display: grid; }` |
| `input[readonly]` | `input` **que té** l'atribut `readonly` | `input[readonly] { background-color: gray; }` |
| `input[type="submit"]` | `input` amb atribut `type` igual a `submit` | `input[type="submit"] { opacity: 0.25; }` |
| `*` | Tots els elements | |
| `a:hover`, `li:first-child`, `tr:nth-child(even)` | Pseudo-classes | |
| `p::first-line` | Pseudo-elements | |

**Per a "el mínim nombre de regles"**: agrupa amb comes tot el que comparteixi propietats i, després, una regla separada per a cada propietat específica.

### 2.4 Propietats de text i tipografia

```css
color: darkblue;                         /* color del text (nom, #rrggbb, rgb(), rgba()) */
font-family: 'Courier New', Courier, monospace; /* llista de fonts alternatives */
font-size: 30px;   font-size: 2.5em;  font-size: 150%;  /* em = relatiu al pare */
font-style: italic;                      /* normal | italic | oblique */
font-weight: bold;                       /* normal | bold | 100..900 */
text-align: center;                      /* left | right | center | justify */
text-decoration: none;                   /* underline | line-through | none */
vertical-align: middle;
list-style-position: inside;             /* bullets alineats amb el text */
list-style-type: none;                   /* elimina bullets */
```

### 2.5 Model de caixa i fons

```css
background-color: lightgray;
border: 1px solid black;                 /* amplada estil color */
border-collapse: collapse;               /* taules: vores fusionades */
border-radius: 5px;
padding: 10px;                           /* interior: 1 valor | vertical horitzontal | dalt dreta baix esquerra */
padding: 0 0 0 10px;
margin: 2px;                             /* exterior */
margin-left: auto; margin-right: auto;   /* centrar horitzontalment un bloc (ex. una taula) */
width: 100px;  max-width: 40%;  min-width: 300px;  height: 50px;
gap: 5px;  column-gap: 5px;  row-gap: 5px;   /* separació entre cel·les de grid/flex */
opacity: 0.75;                           /* 0 transparent … 1 opac (75% = 0.75) */
cursor: pointer;
```

### 2.6 Posicionament i capes

```css
float: left;                /* l'element flota a l'esquerra i el text l'envolta */
position: sticky; top: 0px; /* es queda enganxat a dalt en fer scroll */
position: fixed | absolute | relative | static;
z-index: 1;                 /* ordre de capes: més gran = més al davant; -1 = al darrere */
display: none | block | inline | inline-block | flex | grid;
```
> Perquè una capçalera quedi **sticky al davant** dels missatges: `header { position: sticky; top: 0px; z-index: 1; }`.
> Perquè les imatges quedin **darrere del títol**: `img { z-index: -1; }`.

### 2.7 Layout amb Flexbox

```css
#layout {
    display: flex;
    flex-direction: row;          /* row (horitzontal, per defecte) | column (vertical) */
    justify-content: center;      /* eix principal: flex-start | flex-end | center | space-between | space-around */
    align-items: center;          /* eix secundari: stretch | flex-start | center */
    flex-wrap: wrap;              /* permet saltar de línia */
}
#layout > section { flex: 1; max-width: 40%; }
```
Idea: **fila a pantalla gran, columna a pantalla petita** (o a l'inrevés) → només cal canviar `flex-direction` dins la `@media`.

### 2.8 Layout amb Grid (zones amb nom)

```css
#layout {
    display: grid;
    grid-template-areas:
        'titol titol'             /* fila 1: la zona 'titol' ocupa les 2 columnes */
        'graus registre'          /* fila 2: dues zones, una per columna */
        'stats stats'
        'peu peu';
    column-gap: 5px;  row-gap: 5px;   /* o gap: 5px */
    grid-template-columns: 1fr 1fr;   /* opcional: amplades de columna */
    background-color: lightgray;  padding: 5px;
}
/* Cada bloc indica la seva zona: via CSS … */
#header { grid-area: titol; }
#register { grid-area: registre; }
/* … o directament a l'HTML: <header style="grid-area: titol"> (com als exàmens) */
```
Regles: cada fila de `grid-template-areas` és una cadena entre cometes amb el mateix nombre de noms; una zona que ocupa diverses cel·les es repeteix; `.` indica cel·la buida.

### 2.9 Media queries (responsive)

```css
/* Si la pantalla és IGUAL O MENOR a 1200px → disposició vertical */
@media (max-width: 1200px) {
    #jocs { flex-direction: column; }
}

/* Si és pantalla ("screen") i IGUAL O MAJOR a 1000px → disposició en fila */
@media screen and (min-width: 1000px) {
    #layout { flex-direction: row; }
    section { max-width: 40%; }
}

/* Grid: redefinir les zones en una sola columna */
@media (max-width: 600px) {
    #layout {
        grid-template-areas:
            'titol'
            'graus'
            'registre'
            'stats'
            'peu';
    }
    #layout > header, #layout > section, #layout > footer { text-align: left; }
}
```
Traducció de l'enunciat: "resolució **major** a X" com a cas per defecte i "igual o menor" → `@media (max-width: X)`. "Igual o major a X" → `@media (min-width: X)`. Si diu "dispositiu screen" → `@media screen and (...)`.

### 2.10 Animacions i transformacions

```css
@keyframes anim-campus-e {                      /* definició de l'animació */
    from { width: 0px; }                        /* o 0%  */
    to   { width: 100px; transform: rotate(360deg); }   /* o 100% */
}
#campus-e {
    animation-name: anim-campus-e;              /* nom del @keyframes */
    animation-duration: 5s;                     /* durada d'UNA iteració */
    animation-delay: 1s;                        /* retard inicial */
    animation-timing-function: linear;          /* linear | ease | ease-in | ease-out | ease-in-out */
    animation-iteration-count: 2;               /* nombre de voltes (infinite) */
    animation-direction: alternate;             /* opcional */
    animation-fill-mode: forwards;              /* opcional: es queda a l'estat final */
    /* forma curta: animation: anim-campus-e 5s linear 1s 2; */
}
/* "girar dues vegades 360º durant 1.5 s en total" → duration 0.75s + iteration-count 2 */
img { animation-name: logos; animation-duration: 0.75s; animation-iteration-count: 2; }
@keyframes logos { 100% { transform: rotate(360deg); } }
```
Transformacions: `transform: rotate(360deg) | scale(1.5) | translate(10px, 20px) | skew(...)`. Transicions: `transition: propietat durada;`.

### 2.11 Exemple complet (uab.css de P2–P4, resumit)

```css
h1 { color: darkblue; }
h1, h3 { font-style: italic; }
header p { font-family: 'Courier New', Courier, monospace; }
.fancy { font-family: 'Times New Roman', Times, serif; }
header.fancy { color: magenta; }
section.statistics header h3 { color: red; }
table, th, td { border: 1px solid black; border-collapse: collapse; }
p.important { font-weight: bold; font-size: 30px; background-color: aqua; }
img { float: left; padding: 0 0 0 10px; }
ul li { list-style-position: inside; }
#layout { display: grid; grid-template-areas: 'titol titol' 'graus registre' 'stats stats' 'peu peu'; gap: 5px; padding: 5px; background-color: lightgray; }
#layout > header, #layout > section, #layout > footer { background-color: white; padding: 5px; margin: 2px; text-align: center; }
#layout > footer { text-align: right; }
table { margin-left: auto; margin-right: auto; }
@media (max-width: 600px) { #layout { grid-template-areas: 'titol' 'graus' 'registre' 'stats' 'peu'; } }
```

---

## 3. JavaScript i AJAX

### 3.1 On s'escriu i com s'enllaça

```html
<script src="js/funcions.js"></script>        <!-- extern (al head o abans de </body>) -->
<script> function f(){ ... } </script>        <!-- en línia -->
<select onchange="carregaMencions()">         <!-- crida des d'un event HTML -->
```

### 3.2 Bàsics del llenguatge

```javascript
let x = 5;  const URL = "mencions.php";  var antic = true;   // declaracions (let/const preferits)
let s = "grau=" + degree;              // concatenació de cadenes
let t = `grau=${degree}`;              // template string
if (niu.value.length == 7) { ... } else { ... }
for (let i = 1; i <= 5; i++) { ... }
function nom(param1, param2) { return valor; }
const f = (a, b) => a + b;             // arrow function
console.log("missatge");               // consola del navegador (F12)
alert("missatge");                     // finestra emergent
```

### 3.3 Accés i manipulació del DOM

| Operació | Codi |
|---|---|
| Obtenir un element | `const el = document.getElementById("graus");` |
| Altres selectors | `document.querySelector("#id")`, `document.querySelectorAll("section")`, `getElementsByClassName`, `getElementsByTagName` |
| Llegir/escriure el **valor** d'un input/select/textarea | `el.value` / `el.value = "Cognoms, Nom";` |
| Llegir/escriure **contingut HTML** d'un bloc | `el.innerHTML = "<p class='important'>Fet!</p>";` |
| Només text | `el.textContent = "..."` |
| Activar/desactivar | `el.disabled = false;` |
| Classe / estil | `el.className = "x"; el.classList.add("x"); el.style.color = "red";` |
| Atributs | `el.getAttribute("src"); el.setAttribute("src", "a.png");` |
| Crear/afegir | `const p = document.createElement("p"); el.appendChild(p);` |
| Afegir un event | `el.addEventListener("click", () => { ... });` |

> **Patró clau d'examen**: la resposta AJAX **substitueix** tot el contingut del contenidor: `document.getElementById(id).innerHTML = respostaTxt;`. Per això el servidor només retorna el *fragment* HTML (les `<option>`, el text, el botó…), **no** una pàgina sencera.

### 3.4 Tractament del formulari al client (sense servidor)

```javascript
// <form onsubmit="return confirmaRegistre();">
function confirmaRegistre() {
    alert("registrant estudiant");
    document.getElementById("formDiv").innerHTML = "<p class='important'>T'has registrat amb èxit!</p>";
    console.log("registrant estudiant");
    return false;      // false → el formulari NO s'envia al servidor; true → s'envia
}
```

### 3.5 AJAX amb `fetch` + `async/await` (PLANTILLA PRINCIPAL)

```javascript
async function carregaMencions() {
    // Pas 0: llegir dades del DOM que cal enviar
    const degree = document.getElementById("graus").value;

    // Pas 1: petició asíncrona (GET): paràmetres a la URL amb ?clau=valor&clau2=valor2
    const resposta = await fetch("controladors/mencions.php?grau=" + degree);

    // Pas 2: esperar el cos de la resposta com a text (HTML/fragment)
    const options = await resposta.text();      // .json() si el servidor retorna JSON

    // Pas 3: actualitzar el DOM
    document.getElementById("mencions").innerHTML = options;
}
```

Variants vistes als exàmens:

```javascript
// Diversos paràmetres
async function puntua(jocId, punts) {
    var resposta = await fetch("./controladors/c_puntua.php?jocId=" + jocId + "&punts=" + punts);
    var respostaTxt = await resposta.text();
    document.getElementById(jocId).innerHTML = respostaTxt;   // l'id del div és el jocId
}

// Validació prèvia + condició sobre la resposta + activar botó
async function canviNiu() {
    var niu = document.getElementById("inputNiu");
    if (niu.value.length == 7) {
        var resposta = await fetch("./controladors/c_niuInfo.php?niu=" + niu.value);
        var respostaTxt = await resposta.text();
        if (respostaTxt.length != 0) {
            document.getElementById("inputNom").value = respostaTxt;
            document.getElementById("inputEnviar").disabled = false;
        }
    } else {
        document.getElementById("inputNom").value = "Cognoms, Nom";
    }
}

// Actualitzar una secció sencera (títol + imatge)
async function canviArticle() {
    var sel = document.getElementById("inputArticle");
    var resposta = await fetch("./controladors/c_articleInfo.php?articleCodi=" + sel.value);
    document.getElementById("articleInfo").innerHTML = await resposta.text();
}

// Paràmetre que arriba per l'onclick generat per PHP: onClick="ampliaMissatge(2);"
async function ampliaMissatge(IDmissatge) {
    var resposta = await fetch("./controladors/c_missatge.php?IDmissatge=" + IDmissatge);
    var respostaTxt = await resposta.text();
    document.getElementById(IDmissatge).innerHTML = respostaTxt;  // substitueix text truncat + botó
}
```

**Fetch amb POST** (per si cal):
```javascript
const resposta = await fetch("recurs.php", {
    method: "POST",
    headers: { "Content-Type": "application/x-www-form-urlencoded" },
    body: "grau=" + degree
});
```

**Versió amb promeses (`.then`)**, equivalent:
```javascript
fetch("mencions.php?grau=" + degree)
    .then(r => r.text())
    .then(options => { document.getElementById("mencions").innerHTML = options; });
```

**Versió clàssica `XMLHttpRequest`** (antiga, per si la demanen):
```javascript
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function () {
    if (xhr.readyState == 4 && xhr.status == 200)
        document.getElementById("mencions").innerHTML = xhr.responseText;
};
xhr.open("GET", "mencions.php?grau=" + degree, true);
xhr.send();
```

### 3.6 jQuery (P4)

```html
<script src="https://code.jquery.com/jquery-4.0.0.js"></script>   <!-- ABANS del js propi -->
```
```javascript
$(document).ready(function () {              // quan el DOM està carregat
    $("#graus").change(function () {         // event change sobre #graus (equival a onchange)
        const degree = $("#graus").val();    // .val() ≡ .value
        $.ajax({
            url: "mencions.php?grau=" + degree,
            // type: "GET", data: { grau: degree },  // alternativa per passar paràmetres
            success: function (options) {
                $("#mencions").html(options);  // .html() ≡ .innerHTML
            }
        });
    });
});
```

| Vanilla JS | jQuery |
|---|---|
| `document.getElementById("x")` | `$("#x")` |
| `.value` / `.value = v` | `.val()` / `.val(v)` |
| `.innerHTML = h` | `.html(h)` |
| `.textContent` | `.text()` |
| `el.addEventListener("change", f)` | `$("#x").change(f)` / `.on("change", f)` |
| `el.addEventListener("click", f)` | `$("#x").click(f)` |
| `el.disabled = false` | `$("#x").prop("disabled", false)` |
| `fetch(url).then(r=>r.text())` | `$.ajax({url, success})` / `$.get(url, f)` / `$("#x").load(url)` |
| `window.onload` | `$(document).ready(f)` o `$(function(){...})` |

---

## 4. PHP

### 4.1 Fonaments

```php
<?php
  // PHP s'executa AL SERVIDOR; el navegador només rep el HTML resultant.
  $nom = "Marta";                 // variables comencen per $
  $edat = 20;
  echo "Hola";                    // imprimir
  echo "Hola " . $nom . "!";      // concatenació amb punt
  echo "Hola $nom!";              // interpolació dins de cometes DOBLES (no dins de simples)
  echo "<option value='" . $row['id'] . "'>" . $row['nom'] . "</option>";
  echo "<option value=\"{$row['id']}\">{$row['nom']}</option>";   // claus per a arrays dins string
?>
<?= $variable ?>                  <!-- forma curta de <?php echo $variable; ?> -->
```

Fitxers `.php` poden barrejar HTML i PHP: tot el que és fora de `<?php ... ?>` s'envia tal qual.

### 4.2 Estructures de control

```php
if ($x > 100) { ... } elseif (...) { ... } else { ... }
for ($p = 1; $p <= 5; $p++) { ... }
while (...) { ... }
foreach ($rows as $row) { echo $row['nom']; }         // recórrer array
foreach ($mencions as $clau => $valor) { ... }         // amb clau

// Sintaxi alternativa (molt usada en plantilles HTML):
<?php foreach ($rows as $row) : ?>
    <option value="<?= $row['id'] ?>"><?= $row['nom'] ?></option>
<?php endforeach; ?>
<?php if ($cond) : ?> ... <?php else : ?> ... <?php endif; ?>
<?php for ($i = 0; $i < 5; $i++) : ?> ... <?php endfor; ?>

// Intercalar HTML dins d'un bucle amb claus:
<?php foreach ($jocs as $fila) { ?>
    <div><h2><?php echo $fila["nom"]; ?></h2></div>
<?php } ?>
```

### 4.3 Arrays

```php
$graus = [ ["id"=>1, "nom"=>"Eng. Informàtica"], ["id"=>2, "nom"=>"IA"] ];   // array d'arrays associatius
$mencions = [ 1 => [ ["id"=>1,"nom"=>"Eng Software"], ... ], 2 => [ ... ] ];  // clau numèrica explícita
$graus[0]['nom'];          // "Eng. Informàtica"
$mencions[$grau];          // sub-array per clau
count($rows);              // mida
print_r($rows); var_dump($rows);   // depuració
```
`pg_fetch_all()` retorna exactament aquesta estructura: **array de files, cada fila és un array associatiu amb els noms de columna com a claus**.

### 4.4 Funcions

```php
function consultaMencions($con, $grau) {
    $sql = "SELECT * FROM mencions WHERE grau=$grau";
    $result = pg_query($con, $sql);
    return pg_fetch_all($result);
}
$rows = consultaMencions($con, 2);
```

### 4.5 Rebre dades del client: `$_GET`, `$_POST`, `$_REQUEST`

| Variable superglobal | Conté |
|---|---|
| `$_GET['clau']` | Paràmetres de la URL `?clau=valor` (fetch AJAX, `form method="get"`) |
| `$_POST['clau']` | Camps d'un `form method="post"` (clau = atribut `name` de l'input) |
| `$_REQUEST['clau']` | Unió de GET + POST (+ COOKIE): funciona en tots dos casos |

```php
// Paràmetre opcional amb valor per defecte (3 formes equivalents):
$grau = 1;
if (isset($_REQUEST['grau'])) { $grau = $_REQUEST['grau']; }

$grau = isset($_GET['grau']) ? $_GET['grau'] : 1;

$grau = $_REQUEST['grau'] ?? 1;          // operador "null coalescing" (PHP 7+)
```
> Per què és necessari? Perquè el **mateix controlador** es crida 1) des de `index.php` amb `include_once` en carregar la pàgina (sense paràmetre) i 2) via AJAX amb `?grau=X`.

### 4.6 Incloure fitxers

```php
include_once __DIR__ . "/connectaBD.php";                 // mateix directori
include_once __DIR__ . "/../models/m_connectaBD.php";     // puja un nivell
include      __DIR__ . "/../vistes/v_jocs.php";
```
- `__DIR__` = directori absolut del fitxer **actual** → les rutes funcionen independentment de qui inclogui a qui.
- `include_once` / `require_once` eviten incloure dues vegades (imprescindible per a fitxers amb `function`, si no: "Cannot redeclare function").
- `include` (avís si falla) vs `require` (error fatal si falla). Per a vistes que es poden incloure diverses vegades, s'usa `include`.
- El fitxer inclòs **comparteix les variables** del fitxer que l'inclou (per això la vista pot usar `$rows`, `$jocs`, `$article` definits al controlador).

### 4.7 Funcions de cadena i data útils

```php
substr($text, 0, 100);         // primers 100 caràcters
strlen($text);                 // longitud
strtoupper($s); strtolower($s); trim($s); str_replace("a","b",$s); explode(",", $s); implode(",", $arr);
htmlspecialchars($s);          // escapar HTML (seguretat XSS)
date("d/m/y");                 // 24/10/26
date("H:i");                   // 17:20 (h:i → 12 h)
date("d/m/y") . " " . date("H:i");
```

### 4.8 Pàgina que rep el formulari (exercici 4 de l'examen)

El nom del fitxer = el `action` del `<form>`. Les claus = els `name` dels inputs. Normalment "les dades no s'introdueixen a cap BD, només es mostren".

```php
<!-- FITXER: /resposta.php  (perquè <form method="post" action="./resposta.php">) -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8"/>
    <title>Desenvolupament Web</title>
    <link rel="stylesheet" type="text/css" href="./css/forum.css">   <!-- mateix CSS perquè es vegi igual -->
</head>
<body>
    <header> ... (copiar la capçalera de la pàgina principal si a la figura hi surt) ... </header>
    <div id="layout">
        <section>
            <h2 class="titolPetit">
                <?php echo date("d/m/y") . " " . date("H:i"); ?> - <em><?php echo $_POST['nom']; ?></em> va escriure:
            </h2><hr/>
            <div class="missatge"><?php echo $_POST['resposta']; ?></div><hr/>
            <h2 class="titolPetit">GRÀCIES PER LA TEVA RESPOSTA!</h2>
        </section>
    </div>
</body>
</html>
```
Versió minimalista (23/24): `NIU: <?php echo $_POST['niu']; ?> ARTICLE: <?php echo $_POST['article']; ?>`.
Versió amb inputs de només lectura (P4/P5 `registre.php`): `<input type="text" readonly value="<?= $_REQUEST['nom'] ?>">`.

---

## 5. Accés a base de dades PostgreSQL des de PHP

### 5.1 Connexió (model `connectaBD.php`)

```php
<?php
function connectaBD() {
    $servidor = "deic-docencia.uab.cat";   // o "localhost"
    $port     = "5432";                    // port per defecte de PostgreSQL
    $DBnom    = "tdiw-problemes";
    $usuari   = "tdiw-problemes";
    $clau     = "passProblemes2627";

    $connexio = pg_connect("host=$servidor port=$port dbname=$DBnom user=$usuari password=$clau")
                or die("Error: (pg_connect) " . pg_last_error());
    return $connexio;
}
?>
```

### 5.2 Consulta (model `consultaXxx.php`) — PLANTILLA

```php
<?php
// Llista completa
function consultaGraus($con) {
    $sql = "SELECT * FROM graus";                       // o SELECT id, nom FROM graus
    $result = pg_query($con, $sql) or die("Error sql"); // executa; false si falla
    $rows = pg_fetch_all($result);                      // array de files associatives
    return $rows;
}

// Amb filtre (paràmetre)
function consultaMencions($con, $grau) {
    $sql = "SELECT * FROM mencions WHERE grau=" . $grau;   // o "... WHERE grau=$grau"
    $result = pg_query($con, $sql) or die("Error sql");
    return pg_fetch_all($result);
}

// Un sol registre → agafar la primera fila [0]
function consultaArticle($con, $codi) {
    $sql = "SELECT codi, nom, imatge FROM articles WHERE codi=" . $codi;
    $consulta = pg_query($con, $sql) or die("Error sql");
    $article = pg_fetch_all($consulta)[0];              // fila 0 → array associatiu
    return $article;                                    // $article['nom']
}

// Inserir i després consultar un valor calculat
function puntua($con, $jocId, $punts) {
    $sql1 = "INSERT INTO puntuacions (jocId, punts) VALUES(" . $jocId . "," . $punts . ")";
    pg_query($con, $sql1) or die("Error sql1");
    $sql2 = "SELECT mitja FROM mitjanes WHERE jocId=" . $jocId;
    $consulta2 = pg_query($con, $sql2) or die("Error sql2");
    $mitja = pg_fetch_all($consulta2);
    return $mitja[0]['mitja'];                          // ATENCIÓ al $ (a l'examen 22/23 hi falta)
}
?>
```

### 5.3 Funcions `pg_*` resum

| Funció | Què fa |
|---|---|
| `pg_connect("host=… port=… dbname=… user=… password=…")` | Obre connexió; retorna recurs o `false` |
| `pg_query($con, $sql)` | Executa SQL; retorna resultat o `false` |
| `pg_fetch_all($result)` | Totes les files → `[ ['col'=>val, …], … ]` |
| `pg_fetch_assoc($result)` | Següent fila com a array associatiu (bucle `while ($row = pg_fetch_assoc($r))`) |
| `pg_fetch_row($result)` | Següent fila com a array numèric |
| `pg_num_rows($result)` | Nombre de files |
| `pg_last_error($con)` | Últim missatge d'error |
| `pg_close($con)` | Tanca la connexió |
| `pg_query_params($con, "SELECT … WHERE id=$1", [$id])` | Consulta parametritzada (segura contra SQL injection) |
| `pg_escape_string($s)` | Escapar text per a SQL |

### 5.4 SQL bàsic que cal saber escriure

```sql
SELECT * FROM graus;
SELECT id, nom FROM mencions WHERE grau = 2;
SELECT IDmissatge, data, nom, text FROM missatges ORDER BY data DESC;
INSERT INTO puntuacions (jocId, punts) VALUES (1, 4);
UPDATE taula SET camp = valor WHERE id = 1;
DELETE FROM taula WHERE id = 1;
SELECT AVG(punts) AS mitja FROM puntuacions WHERE jocId = 1;
```

---

## 6. Arquitectura MVC

### 6.1 Estructura de directoris

```
/index.php  (o enquesta.php, forum.php)   ← pàgina principal: HTML + include_once dels controladors
/resposta.php (o registre.php)            ← pàgina que rep el formulari (action)
/css/xxx.css
/js/funcions.js                           ← AJAX: fetch("controladors/c_xxx.php?param=…")
/controladors/c_xxx.php                   ← CONTROLADOR: orquestra model + vista
/models/m_connectaBD.php                  ← MODEL: connexió
/models/m_xxx.php                         ← MODEL: funcions de consulta (SQL → arrays)
/vistes/v_xxx.php                         ← VISTA: HTML amb foreach/echo sobre les dades
```

### 6.2 Responsabilitats

| Capa | Fa | NO fa |
|---|---|---|
| **Model** | Connectar a la BD, executar SQL, retornar arrays | Imprimir HTML |
| **Vista** | Generar el fragment HTML a partir de variables (`$rows`, `$jocs`…) | Consultar la BD |
| **Controlador** | Llegir paràmetres (`$_GET`), incloure models, cridar funcions, passar resultats a la vista (`include`) | SQL ni HTML |

### 6.3 Plantilla de CONTROLADOR

```php
<?php
// FITXER: /controladors/c_mencions.php
// 1) Paràmetre d'entrada (amb valor per defecte si el controlador també s'inclou a la càrrega inicial)
$grau = $_REQUEST['grau'] ?? 1;          // o: $grau = 1; if (isset($_GET['grau'])) $grau = $_GET['grau'];

// 2) Incloure models (include_once perquè contenen funcions)
include_once __DIR__ . "/../models/m_connectaBD.php";
include_once __DIR__ . "/../models/m_mencions.php";

// 3) Connectar i consultar
$connexio = connectaBD();
$rows = consultaMencions($connexio, $grau);   // la vista espera la variable amb AQUEST nom

// 4) Incloure la vista (comparteix $rows)
include __DIR__ . "/../vistes/v_opcionsMencions.php";
?>
```

Variant on la vista defineix una funció (P5-s `opcionsGraus.php`):
```php
include_once __DIR__ . "/../vistes/opcionsGraus.php";   // defineix showDegrees($rows)
$con = connectaBD();
$rows = consultaGraus($con);
showDegrees($rows);
```

### 6.4 Plantilla de MODEL
Vegeu §5.2. Una funció per consulta; reben `$connexio` com a primer paràmetre; retornen l'array (o la fila `[0]`, o un escalar).

### 6.5 Plantilles de VISTA

```php
<!-- Opcions d'un <select> -->
<?php foreach ($rows as $row) : ?>
    <option value="<?= $row['id'] ?>"><?= $row['nom'] ?></option>
<?php endforeach; ?>

<!-- Bloc per element amb bucle intern i onclick amb paràmetres generats per PHP -->
<div id="jocs">
<?php foreach ($jocs as $fila) { ?>
    <div>
        <h2 class="titolPetit"><?php echo $fila["nom"]; ?></h2>
        <img src="<?php echo $fila['imatge']; ?>"/><br />
        <div id="<?php echo $fila['jocId']; ?>">
            PUNTUA:
            <?php for ($p = 1; $p <= 5; $p++) { ?>
                <input type="button" value="<?php echo $p; ?>"
                       onClick="puntua('<?php echo $fila["jocId"]; ?>', <?php echo $p; ?>);">
            <?php } ?>
        </div>
    </div>
<?php } ?>
</div>

<!-- Un sol registre -->
<h2 class="titolPetit"><em><?php echo $article['nom']; ?></em></h2>
<img src="<?php echo $article['imatge']; ?>" />

<!-- Text truncat + botó condicional -->
<?php foreach ($missatges as $fila) { ?>
<section>
    <h2 class="titolPetit"><?php echo $fila['data'] ?> - <em><?php echo $fila['nom'] ?></em> va escriure:</h2><hr/>
    <div id="<?php echo $fila['IDmissatge']; ?>">
        <?php echo substr($fila['text'], 0, 100);
              if (strlen($fila['text']) > 100) { ?>
            [...] <button type="button" onClick="ampliaMissatge(<?php echo $fila['IDmissatge']; ?>);">Veure més</button>
        <?php } ?>
    </div>
</section>
<?php } ?>

<!-- Només text (resposta AJAX mínima) -->
<?php echo $missatge["text"]; ?>

<!-- Fragment de resposta amb botó -->
PUNTUACIO MITJA: <input type="button" value="<?php echo $mitja; ?>">
```

### 6.6 Com es crida un controlador (dues vies)

```php
<!-- A) Càrrega inicial de la pàgina: inclusió al servidor (sense paràmetres → valor per defecte) -->
<select name="mencio" id="mencions">
    <?php include_once __DIR__ . "/controladors/mencions.php"; ?>
</select>
```
```javascript
// B) AJAX des del navegador (amb paràmetres a la URL)
const r = await fetch("controladors/mencions.php?grau=" + degree);
document.getElementById("mencions").innerHTML = await r.text();
```
El controlador és el **mateix fitxer**; només cal que gestioni el paràmetre opcional. Compte amb `include_once` a la pàgina principal: si el mateix controlador s'inclou dues vegades, la segona no s'executa (en P5 se'n fan servir dos de diferents: `graus.php` i `mencions.php`).

### 6.7 Evolució P3 → P4 → P5 (per entendre d'on surt tot)

| Sessió | Servidor | Client |
|---|---|---|
| P1 HTML | `index.html` estàtic | — |
| P2 CSS + JS bàsic | — | `uab.css`, `confirmaRegistre()` amb `onsubmit`, `innerHTML`, `return false` |
| P3 Layout + AJAX | `mencions.php` amb arrays PHP "hardcoded", `$_REQUEST['grau']`, `foreach` + `echo "<option…>"` | Grid layout, `@media`, `@keyframes`; `carregaMencions()` amb `fetch` |
| P4 BD + jQuery | `connectaBD.php` (`pg_connect`), `index.php` consulta `graus` i `mencions` amb `pg_query`/`pg_fetch_all`, `mencions.php` amb `isset`, `registre.php` mostra `$_REQUEST` | jQuery `$.ajax`, `$(document).ready`, `.change`, `.val`, `.html` |
| P5 MVC | `controladors/`, `models/`, `vistes/`; `??`; `index.php` només fa `include_once` dels controladors | `fetch("controladors/mencions.php?grau=…")` |

---

## 7. Flux complet client ↔ servidor

```
[1] Navegador demana index.php
        → Apache executa PHP: include_once controladors → connectaBD → pg_query → vistes generen <option>…
        → S'envia HTML complet + el navegador descarrega css/uab.css i js/funcions.js
[2] Usuari canvia el <select id="graus">  →  event onchange="carregaMencions()"
[3] JS: fetch("controladors/mencions.php?grau=2")        (petició HTTP GET asíncrona, la pàgina NO es recarrega)
[4] Servidor: $_GET['grau']=2 → model → SQL → vista → imprimeix només "<option …>…</option>"
[5] JS: await resposta.text() → document.getElementById("mencions").innerHTML = text
[6] Usuari prem submit → <form method="post" action="registre.php"> → el navegador carrega registre.php
        → PHP llegeix $_POST['nom'], $_POST['grau'], $_POST['mencio'] i genera la pàgina de resposta
```

---

## 8. Plantilles de resolució per tipus d'exercici

### 8.1 Exercici de CSS ("mínim nombre de regles" + layout)

Checklist:
1. Propietats **compartides** per diversos selectors → **una regla agrupada** (`header, footer { … }`, `.titolGran, .titolPetit { … }`).
2. Propietats **específiques** → regla pròpia (`footer { text-align: right; }`).
3. "Elements X **de la classe** Y" → `X.Y` (`h1.titolGran`). "La classe Y" → `.Y`. "Elements `em` **dins** de la classe Y" → `.Y em`.
4. "Elements input **amb l'atribut** readonly" → `input[readonly]`. "**de tipus** submit" → `input[type="submit"]`.
5. "El color de fons **de la pàgina**" → `body { background-color: … }`. "La font **de tota la pàgina**" → `body { font-family: … }`.
6. "Es quedi **enganxada** a dalt i **al davant**" → `position: sticky; top: 0px; z-index: 1;`.
7. "Al **darrere** del títol" → `z-index: -1`. "Opacitat del 75 %" → `opacity: 0.75`.
8. "Girin N vegades 360º durant T segons" → `@keyframes nom { 100% { transform: rotate(360deg); } }` + `animation-name: nom; animation-duration: T/N s; animation-iteration-count: N;`.
9. **Layout**: mira el fitxer principal. Si hi ha `style="grid-area:…"` → `display: grid` + `grid-template-areas` al contenidor. Si l'enunciat diu "amb flexbox" o només hi ha fila/columna → `display: flex; flex-direction: …`.
10. **Responsive**: tradueix la condició a `@media (max-width: Xpx)` / `@media screen and (min-width: Xpx)` i a dins redefineix **només** el que canvia (`flex-direction` o `grid-template-areas` i, si cal, `max-width`).

### 8.2 Exercici MVC (controlador + model + vista)

Checklist:
1. Identifica, a la sortida HTML donada, **quins camps** de la BD apareixen i on (→ columnes del `SELECT` i `$fila['camp']` a la vista).
2. Identifica si cal paràmetre (`WHERE … = $param`) i d'on ve (`$_GET["x"]` amb el nom usat a la URL del `fetch`).
3. Controlador: `include_once` connexió + model; `$connexio = connectaBD();` `$dades = consultaX($connexio[, $param]);` `include` vista.
4. Model: `function consultaX($connexio[, $param]) { $sql = …; $consulta = pg_query($connexio, $sql) or die("Error sql"); return pg_fetch_all($consulta); }` (afegeix `[0]` si és un sol registre).
5. Vista: HTML literal + `<?php foreach ($dades as $fila) { ?> … <?php echo $fila['camp']; ?> … <?php } ?>`. Reprodueix **exactament** la sortida (ids, classes, atributs `onClick` amb paràmetres).
6. Si hi ha condició (botó només si text > 100) → `if (strlen(...) > 100) { ?> … <?php }` i `substr($text, 0, 100)`.
7. Si el controlador es crida tant a la càrrega inicial com per AJAX → valor per defecte amb `isset`/`??`.

### 8.3 Exercici JavaScript / AJAX

Checklist:
1. Nom de la funció i paràmetres: els que apareixen a l'`onclick`/`onchange`/`onkeyup` del HTML.
2. `async function`.
3. Llegir del DOM el que cal (`.value` d'un input/select).
4. Validació prèvia si l'enunciat ho diu (`if (niu.value.length == 7)`; `else` → restaurar valor per defecte).
5. `var resposta = await fetch("./controladors/c_xxx.php?param=" + valor [+ "&param2=" + valor2]);`
6. `var txt = await resposta.text();`
7. Condició sobre la resposta si cal (`if (txt.length != 0)`).
8. Actualitzar DOM: `innerHTML` (blocs), `.value` (inputs), `.disabled = false` (botons).

### 8.4 Exercici "fitxer que rep el formulari"

Checklist:
1. Nom del fitxer = `action` del `<form>` (`resposta.php`, `registre.php`).
2. Variables = `$_POST['name']` (o `$_REQUEST`), un per cada `<input>`/`<select>`/`<textarea>` amb `name`.
3. Copia `<!DOCTYPE>`, `<head>` amb `<meta charset>`, `<title>` i el mateix `<link>` CSS (perquè la captura es vegi igual). Copia `header`/`layout` si surten a la figura.
4. Afegeix el text fix de la figura ("GRÀCIES PER LA TEVA RESPOSTA!").
5. Si cal data/hora actual → `date("d/m/y") . " " . date("H:i")`.

### 8.5 Exercici de HTML pur (P1)

Checklist: `<!DOCTYPE html>`, `<meta charset>`, `<title>`; estructura `header/section/footer`; enllaç-imatge `<a><img></a>`; llistes niuades (sub-`<ul>` dins del `<li>`); formulari amb `label`+`input`+`select`+`submit`; taula amb `colspan`/`rowspan`; `&copy;`.

---

## 9. Resum dels exàmens anteriors

### 9.1 Examen 22/23 — "Enquesta de Jocs Clàssics"
- **Web**: 3 jocs en fila (>1200 px) o columna (≤1200 px). Sota cada imatge "PUNTUA:" + 5 botons; en clicar → "PUNTUACIO MITJA:" + botó amb la mitjana.
- **CSS**: `header, footer {background-color: darkgray; border: 5px solid black; padding: 10px;}`, `footer {text-align: right;}`, `input {background-color: gray; color: white;}`, `h1.titolGran {font-size: 2.5em; font-style: italic;}`, `h2.titolPetit {font-size: 2em; font-weight: bold;}`, `#jocs {display: flex; flex-direction: row; justify-content: center;}`, `@media (max-width: 1200px) { #jocs { flex-direction: column; } }`.
- **MVC**: `c_jocs.php` (include models → `connectaBD()` → `consultaJocs()` → include `v_jocs.php`); `v_jocs.php` amb `foreach` + `for ($p=1;$p<=5;$p++)` generant `<input type="button" value="p" onClick="puntua('J1', p);">`.
- **JS**: `puntua(jocId, punts)` → `fetch("./controladors/c_puntua.php?jocId="+jocId+"&punts="+punts)` → `getElementById(jocId).innerHTML`.
- **Controlador 2**: `c_puntua.php` llegeix `$_GET["jocId"]`, `$_GET["punts"]`; model fa `INSERT` + `SELECT mitja`; vista imprimeix `PUNTUACIO MITJA: <input type="button" value="<?php echo $mitja; ?>">`.

### 9.2 Examen 23/24 — "Enquesta d'articles d'IEEE Spectrum"
- **Web**: grid `'titol titol' / 'formulari article'` (>1000 px) o una columna (≤1000 px). Input NIU amb `onkeyup="canviNiu();"`, select amb `onchange="canviArticle();"`, input `readonly` i botó `submit disabled`.
- **CSS**: `.titolGran, .titolPetit {color: black; font-weight: bold;}`, `.titolGran {font-size: 2.5em;}`, `.titolPetit {font-size: 1.5em;}`, `.titolGran em, .titolPetit em {color: darkred;}`, `input[readonly] {background-color: gray;}`, `input[type="submit"] {opacity: 0.25;}`, `#layout {display: grid; grid-template-areas: 'titol titol' 'formulari article';}`, `@media (max-width: 1000px) { #layout { grid-template-areas: 'titol' 'formulari' 'article'; } }`.
- **MVC**: `c_articlesLlistat.php` (llista d'`<option>`), `c_articleInfo.php` amb `$articleCodi = 0; if (isset($_GET["articleCodi"])) …` perquè es crida a l'inici **i** per AJAX; model amb `consultaArticles` i `consultaArticle` (`pg_fetch_all(...)[0]`); vistes `v_articlesLlistat.php` i `v_articleInfo.php`.
- **JS**: `canviNiu()` (validació de 7 dígits, fetch, `.value`, `.disabled = false`), `canviArticle()` (fetch + `innerHTML` de la `section#articleInfo`).
- **resposta.php**: `NIU: <?php echo $_POST['niu']; ?> ARTICLE: <?php echo $_POST['article']; ?>`.

### 9.3 Examen 24/25 — "Fòrum de Desenvolupament Web"
- **Web**: capçalera sticky amb 3 logos que giren 2 voltes en 1,5 s; llista de missatges truncats a 100 caràcters amb botó "Veure més"; formulari amb `textarea`. Flex column per defecte; flex row + `max-width: 40%` a pantalles grans.
- **CSS**: `body {background-color: white; font-family: 'courier new';}`, `header {text-align: center; background-color: darkgray; position: sticky; top: 0px; z-index: 1;}`, `section {text-align: left; background-color: lightgray;}`, `img {opacity: 0.75; z-index: -1; animation-name: logos; animation-duration: 0.75s; animation-iteration-count: 2;}`, `@keyframes logos {100% {transform: rotate(360deg);}}`, `#layout {display: flex; flex-direction: column;}`, `@media screen and (min-width: 1000px) { #layout {flex-direction: row;} section {max-width: 40%;} }` (l'enunciat deia 1400 px; la solució posa 1000 px — fes servir el valor de l'enunciat).
- **JS**: `ampliaMissatge(IDmissatge)` → `fetch("/controladors/c_missatge.php?IDmissatge=" + IDmissatge)` → `innerHTML` del `div` amb `id=IDmissatge`.
- **MVC**: `c_missatgesLlistat.php` + `v_missatgesLlistat.php` amb `substr`/`strlen` i botó condicional; `c_missatge.php` llegeix `$_GET["IDmissatge"]`, model `consultaMissatge` retorna la fila `[0]`, vista `<?php echo $missatge["text"]; ?>`.
- **resposta.php**: capçalera copiada + `date("d/m/y") . " " . date("h:i")` + `$_POST['nom']` + `$_POST['resposta']` + "GRÀCIES PER LA TEVA RESPOSTA!".

---

## 10. Errors freqüents i trampes

**HTML**
- Oblidar `name` als inputs → no arriben al servidor. Oblidar `id` → no es poden seleccionar amb JS/CSS.
- Posar la sub-llista fora del `<li>` pare. Oblidar `</li>`, `</tr>`.
- `disabled` no s'envia amb el formulari; `readonly` sí.
- `<button>` dins d'un `<form>` sense `type="button"` fa submit.
- `<script>` al `<head>` que accedeix al DOM directament (encara no existeix).

**CSS**
- `header.fancy` (header amb classe) ≠ `header .fancy` (descendent amb classe) — **l'espai canvia el significat**.
- Les files de `grid-template-areas` han de tenir el **mateix nombre de columnes** i anar entre cometes.
- A la `@media` només redefinir el que canvia; no repetir-ho tot.
- `opacity` va de 0 a 1 (75 % → 0.75). `z-index` només funciona amb elements posicionats (`sticky`, `relative`…) o fills de flex/grid.
- "Durant 1,5 s dues voltes": duration = 0.75 s × iteration-count 2 (no duration 1.5 s).
- `font-family` amb noms compostos entre cometes: `'Courier New'`.

**JavaScript**
- Oblidar `async` davant la funció o `await` davant `fetch` i `.text()`.
- `innerHTML` per a contenidors (div/section/select); `.value` per a inputs. No barrejar-los.
- Concatenar correctament la URL: `"?a=" + a + "&b=" + b` (el primer separador és `?`, els següents `&`).
- `onsubmit="return f();"` sense el `return` → el `false` no evita l'enviament.
- jQuery: carregar la llibreria **abans** del teu fitxer; codi dins `$(document).ready`.

**PHP**
- Oblidar el `$` a les variables (a la solució 22/23 hi ha `return(mitja[0]['mitja'])` → hauria de ser `$mitja[0]['mitja']`).
- A la solució 24/25 `consultaMissatge` conté línies sobreres/incorrectes (`$missatge = $missatges[$IDmissatge]` amb una variable inexistent, `$article = pg_fetch_all(...)[0]` i després `return($missatge)`); la versió correcta és: `$consulta = pg_query(...); $missatge = pg_fetch_all($consulta)[0]; return $missatge;`.
- Confondre el nom de la variable que espera la vista (`$rows` vs `$jocs` vs `$article`).
- Usar `$_POST` quan la crida AJAX amb `fetch(url?…)` és GET → cal `$_GET` o `$_REQUEST`.
- Rutes: usar sempre `__DIR__ . "/../models/..."` des de `controladors/`. Des de la pàgina principal: `__DIR__ . "/controladors/..."`.
- `include_once` d'un fitxer de vista que cal pintar dues vegades → només es pinta un cop; usa `include`.
- Cometes dins de cometes: `echo "<option value='".$row['id']."'>"` o `<?= $row['id'] ?>` dins d'HTML.
- Interpolació d'array dins string: `"WHERE grau=$grau"` funciona; `"$row['id']"` dóna error → usar `{$row['id']}` o concatenar.
- `pg_fetch_all` retorna `false` si no hi ha files → `foreach` sobre `false` dóna warning (comprova amb `if ($rows)` si cal).
- `substr($text, 0, 100)` i `strlen($text)`: ordre dels paràmetres (string, inici, longitud).

---

## 11. Xuleta final

```
HTML   <!DOCTYPE html> <html> <head> <meta charset="utf-8"/> <meta name="viewport"…> <title> <link rel="stylesheet" href> <script src>
       <body> <header> <section> <footer> <div id class style> <span> <h1..h6 class> <p> <em> <strong> <br/> <hr/> <a href target="_blank"> <img src width id/>
       <ul> <ol> <li> (niuades dins <li>)   <table border> <tr> <th colspan> <td rowspan>
       <form method="post" action="x.php" onsubmit="return f();"> <label for> <input type=text|password|submit|button name id value size maxlength readonly disabled required onclick onkeyup/>
       <select name id onchange><option value selected></select> <textarea name id rows cols required></textarea> <button type="button" onClick>   &copy;

CSS    sel, sel { } | pare fill | pare > fill | .classe | #id | elem.classe | elem[attr] | elem[type="submit"] | .classe em
       color font-family font-size(px/em) font-style font-weight text-align text-decoration list-style-position
       background-color border border-collapse padding margin margin:auto width max-width gap opacity float z-index position:sticky;top:0
       display:flex flex-direction:row|column justify-content align-items   display:grid grid-template-areas:'a a' 'b c' grid-area column-gap row-gap
       @media (max-width: Npx){…}  @media screen and (min-width: Npx){…}
       @keyframes nom { from/0% {…} to/100% { transform: rotate(360deg); } }  animation-name -duration -delay -timing-function -iteration-count

JS     document.getElementById("id").value | .innerHTML | .disabled = false | .length   console.log alert   return false
       async function f(p){ var r = await fetch("./controladors/c_x.php?a=" + p + "&b=" + q); var t = await r.text(); document.getElementById(id).innerHTML = t; }
       jQuery: $(document).ready(function(){ $("#id").change(function(){ $.ajax({ url: "...?a=" + $("#id").val(), success: function(d){ $("#dest").html(d); } }); }); });

PHP    <?php … ?>  <?= $v ?>  echo "a" . $b . "c"  "$v" "{$a['k']}"  $_GET $_POST $_REQUEST isset() ??  include_once __DIR__ . "/../models/x.php"  include vista
       function f($con, $p){ $sql = "SELECT … WHERE c=" . $p; $r = pg_query($con, $sql) or die("Error sql"); return pg_fetch_all($r); }   [0] per 1 fila
       pg_connect("host= port= dbname= user= password=") or die(pg_last_error())   pg_close
       foreach ($rows as $row) { ?> html <?php } ?>   / foreach(...): … endforeach;   for  if/else  substr($s,0,100) strlen($s) date("d/m/y") date("H:i")

MVC    index.php → include_once controladors/c_x.php → (include_once models) connectaBD() → consultaX($con,$p) → include vistes/v_x.php
       JS fetch("controladors/c_x.php?p=…") → mateix controlador → només fragment HTML → innerHTML
```
