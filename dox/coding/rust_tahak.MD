# Rust tahák

> [!NOTE]  
> Toto zdaleka není veškerý výčet funkcí a vlastností daného programovacího jazyka.<br>Jedná se pouze o&nbsp;mé zápisky vzniklé během řešení [AoC](https://github.com/RDMCz/AdventOfCode) a/nebo při (samo)studiu.<br><br>Psáno při používání jazyka Rust ve verzi 1.90.0.

* Základy
  * [Deklarace a výpis proměnných](#deklarace-a-výpis-proměnných)
  * [Tuple a pole](#tuple-a-pole)
  * [Podmínky a cykly](#podmínky-a-cykly)
  * [Funkce](#funkce)
* [Práce s pamětí a reference](#práce-s-pamětí-a-reference)
  * [Lifetime: „životnost“](#lifetime-životnost)
* [Struktury](#struktury)
* [Enum a `match`](#enum-a-match)
  * [`Option<T>`: `Some(T)` a `None`](#optiont-somet-a-none)
  * [`if let` a `let else`](#if-let-a-let-else)
  * [`Result<T, E>`: `Ok(T)` a `Err(E)`; `panic!`](#resultt-e-okt-a-erre-panic)
    * [`unwrap`, `expect` a operátor `?`](#unwrap-expect-a-operátor-)
* Kolekce
  * [Vector](#vector)
  * [Hash map](#hash-map)
  * [String](#string)
* [Slices](#slices)
  * [String slices](#string-slices)
* [Traity a `dyn`](#traity-a-dyn)
* [Smart pointer `Box<T>`](#smart-pointer-boxt)
* [Anonymní funkce / uzávěry](#anonymní-funkce--uzávěry)
* [Iterátory](#iterátory)
* [Příloha](#příloha)
  * [Datové typy a převody mezi nimi](#datové-typy-a-převody-mezi-nimi)
  * [Kolekce/řetězce/slices a jejich metody](#kolekceřetězceslices-a-jejich-metody)
  * [Iterátory a jejich metody](#iterátory-a-jejich-metody)
* [Použité zdroje](#použité-zdroje)

## Základy

### Deklarace a výpis proměnných

```rust
let num1 = 5;       // Immutable („neměnné“) číslo, implicitní datový typ `i32`
let mut num2 = 5;   // Mutable číslo, opět `i32`
let num3: u32 = 7;  // Explicitně specifikovaný typ `u32`

//num1 += 1;        // Nelze, `num1` je immutable
num2 += 1;

println!("{num1} < {num2} < {num3}");        // → 5 < 6 < 7
println!("{} < {} < {}", num1, num2, num3);  // → 5 < 6 < 7
                                             // `println!` končí vykřičníkem, protože se jedná o makro

const SECONDS_IN_HOUR: u32 = 3_600;  // U konstant nutno specifikovat datový typ
                                     // U čísel lze používat podtržítka pro zpřehlednění

// Znovu-deklarovat proměnnou s již existujícícm jménem není problém
let numx = 8;
let numx = 9;
let numx = "x";
```

### Tuple a pole

```rust
let my_tup: (char, bool, f64) = ('Ř', true, 0.5);
// Přístup k hodnotám pomocí tečkové notace
println!("{}", my_tup.1);   // → true

let my_arr: [i32; 5] = [1, 2, 3, 4, 5];
// Přístup k hodnotám pomocí hranatých závorek
println!("{}", my_arr[1]);  // → 2

// V těcto dvou případech by datové typy mohly být vynechány a byly by určeny implicitně.
// (`i32` je implicitní pro celá čísla, `f64` je implicitní pro čísla s plovoucí řádovou čárkou)
```

Tuple `()`, která nemá žádnou hodnotu, se nazývá _unit_ a reprezentuje prázdnou hodnotu. Nevrací-li výraz nějakou explicitní hodnotu, pak implicitně vrací unit.

### Podmínky a cykly

```rust
// Klasické podmínky
if num1 == 7 {
    // ...
} else if num1 == 6 {
    // ...
} else {
    // ...
}

// Přiřazení ifu je podobné ternárnímu operátoru
let num4 = if my_tup.1 { 8 } else { 9 };  // → 8

// `loop` značí cyklus analogický k `while(true)`, ten lze také přiřadit s tím, že `break` vrací hodnotu
let mut cnt = 0;
let num5 = loop {
    cnt += 1;
    if cnt == 4 {
        break cnt * 2 + 1  // → 9
    }
};

// Vyskočit z vnějšího cyklu lze pomocí labelů; název labelu začíná jednoduchou uvozovkou
'label: loop {
    loop {
        break 'label;
        // (krom `break` je k dispozici i klíčové slovo `continue`)
    }
}

// K dispozici je i klasický `while`
while cnt > 0 {
    cnt -= 1;
}

// Nakonec je tu `for` ve stylu foreach
for element in my_arr {
    print!("{element} ");  // → 1 2 3 4 5
}

// `for` může iterovat přes rozsah
for n in 1..11 {
    print!("{n} ");  // → 1 2 3 4 5 6 7 8 9 10
}

for n in (1..11).rev() {
    print!("{n} ");  // → 10 9 8 7 6 5 4 3 2 1
}

for n in (1..11).step_by(2) {
    print!("{n} "); // → 1 3 5 7 9
}
```

### Funkce

U funkcí je nutné specifikovat datové typy vstupních parametrů a případný datový typ návratové hodnoty. Vrátit hodnotu lze klíčovým slovem `return`. Pokud ale chceme vrátit hodnotu na konci funkce (což je častý případ), je zvykem vynechat klíčové slovo `return` a středník, tedy psát pouze samotný výraz.

```rust
fn double_w_return(i: i32) -> i32 {
    return 2 * i;  // Funguje, ale není standardní
}

fn double_wo_semicolon(i: i32) -> i32 {
    2 * i  // Preferovaný přístup
}

fn double_or_triple(i: i32, do_triple: bool) -> i32 {
    if do_triple {
        return 3 * i;  // Zde je nutné použít `return`
    }

    2 * i  // Zde preferujeme absenci klíčového slova `return`
}

fn main() {
    println!("{}", double_w_return(12));          // → 24
    println!("{}", double_wo_semicolon(12));      // → 24

    println!("{}", double_or_triple(12, false));  // → 24
    println!("{}", double_or_triple(12, true));   // → 36
}
```

## Práce s pamětí a reference

Jazyk Rust nemá garbage collector a nevyužívá ani explicitní (de)alokaci paměti programátorem. Namísto toho přináší pravidla, která musí kód v jazyce Rust splňovat. Bere-li náš kód tato pravidla v&nbsp;potaz, pak je paměť spravována „automaticky“. Pokud jde náš kód nějakým způsobem proti těmto pravidlům, tak se nezkompiluje.

Pravidla pracují s koncepty vlastnictví, přesunutí, referencí a výpůjček. Znějí takto:

* Každá hodnota má právě jednoho vlastníka
* Jakmile skončí rozsah platnosti vlastníka, hodnota je zahozena
* Každá hodnota může mít maximálně jednu mutable referenci
* Pokud pro hodnotu existuje mutable reference, nemohou pro ni existovat žádné jiné (immutable) reference

Tato pravidla řešíme ve chvíli, kdy pracujeme s „neprimitivními datovými typy“, které jsou uloženy na haldě; např. kolekce `String`.

_Každá hodnota má právě jednoho vlastníka_:

```rust
// Primitivní datový typ
let i1 = 5;
let i2 = i1;
println!("{}", i2);  // → 5
println!("{}", i1);  // → 5

// String
let s1 = String::from("abc");
let s2 = s1;
println!("{}", s2);    // → abc
//println!("{}", s1);  // Nelze, hodnota byla z `s1` tzv. přesunuta do `s2`
                       // `s2` je nový vlastník a `s1` byl zneplatněn, nemůžeme jej nijak použít

// Přesunutí (move) si můžeme představit jako shallow-copy následované zahozením zdroje
// Pro deep copy (bez zahození zdroje) lze použít funkci clone
let s3 = s2.clone();
println!("{s2}{s3}");  // → abcabc
```

Pravidlo jednoho vlastníka tedy v praxi neznamená, že nemůžeme nikde v kódu napsat řádek ve stylu `let s2 = s1;`, ale že proměnná `s1` bude po přesunutí zneplatněna. Pokud už nikde po tomto řádku `s1` nezmíníme, tak nejdeme proti žádnému pravidlu a kód bude fungovat.

_Jakmile skončí rozsah platnosti vlastníka, hodnota je zahozena_:

```rust
{
    let mut s4 = String::from("abc");
    s4.push_str("def");  // → abcdef
}
//s4.push_str("ghi");  // Nelze, `s4` zde již neexistuje a řetězec byl zahozen
//println!("{}", s4);  // (Také nelze)

// Zahození hodnoty po ukončení rozsahu platnosti platí obecně i pro primitivní datové typy;
// zde pouze zdůrazňujeme, že toto platí i ve světě vlastníků.
```

Přesunutí probíhá i při předávání vstupních parametrů funkci.

```rust
fn main() {
    let sf = String::from("Hey!");
    println!("{sf}");    // → Hey!
    print_string(sf);
    //println!("{sf}");  // Nelze, hodnota byla přesunuta do vstupního parametru a zde už neplatí
}

fn print_string(some_string: String) {
    println!("{}", some_string);  // → Hey!
    // Hodnota byla přesunuta sem do `some_string` a na konci tohoto bloku bude také zahozena
}
```

Pokud bychom chtěli vrátit vlastnictví do hlavní funkce, mohla by funkce `print_string` po vypsání řetězec zase vrátit; ve funkci `main` bychom pak volali `sf = print_string(sf);`. To je ale nepraktické, a proto přicházejí reference.

```rust
fn main() {
    let sf = String::from("Hey!");
    println!("{sf}");  // → Hey!
    print_string_ref(&sf);
    println!("{sf}");  // → Hey!
}

fn print_string_ref(some_string: &String) {
    println!("{}", some_string);  // → Hey!
    // Reference není vlastník, její zahození ničemu nevadí
}
```

Napsáním `&` před datový typ myslíme referenci na tento typ. Napsáním `&` před název proměnné referencujeme tuto proměnnou, tomu se říká výpůjčka (borrow). Znak `*` pak případně slouží pro dereferenci.

Klasické reference jsou immutable, tedy neměnné. Cheme-li vypůjčenou proměnnou jakkoliv upravit, musíme použít mutable referenci.

```rust
fn main() {
    let mut sf = String::from("Hey");
    println!("{sf}");  // → Hey
    shoutify(&mut sf);
    println!("{sf}");  // → Hey!!!
}

fn shoutify(some_string: &mut String) {
    some_string.push_str("!!!");
}
```

_Každá hodnota může mít maximálně jednu mutable referenci_:

```rust
let mut sf = String::from("Hey");
let sfmr1 = &mut sf;
let sfmr2 = &mut sf;
println!("{}", sfmr2);    // → Hey
//println!("{}", sfmr1);  // Nelze; podobně jako s vlastnictvím, první „mutable výpůjčka“ byla zneplatněna
```

_Pokud pro hodnotu existuje mutable reference, nemohou pro ni existovat žádné jiné (immutable) reference_:

```rust
let mut sf = String::from("Hey");
let sfr1 = &sf;
let sfr2 = &sf;
println!("{sfr1} {sfr2} People!");    // → Hey Hey People!
let sfmr = &mut sf;
//println!("{sfr1} {sfr2} People!");  // → Nelze

// ⇒ Jakmile dojde k mutable výpůjčce, všechny předchozí výpůjčky jsou zneplatněny.
// ⇒ Jakmile dojde k libovolné výpůjčce, předchozí mutable výpůjčka je zneplatněna.
```

### Lifetime: „životnost“

```rust
let xr;
let x = 5;
{
    xr = &x;
}
print!("{xr}");  // → 5

// ↑ Tento kód je v pořádku; prohozením dvou konkrétních řádků nastává problém:

let xr;
{
    let x = 5;     // Zde je vytvořeno `x`
    xr = &x;       // Zde probíhá výpůjčka
}                  // Zde je zahozeno `x`
//print!("{xr}");  // Nelze pracovat s výpůjčenou hodnotou, která byla zahozena
```

Reference `xr` žije déle než hodnota `x`, na kterou ukazuje. Při odkomentování řádku s `print!` se kód nezkompiluje, o to se postará Rust „borrow checker“. Ten ale není vševědoucí.

```rust
/// Ze dvou vstupních referencí vrátí tu, která ukazuje na delší String
fn return_the_longer_one(s1: &String, s2: &String) -> &String {
    if s1.len() > s2.len() { s1 } else { s2 }
}

// (Poznámka: jako typ vstupního parametru obvykle používáme `&str` namísto `&String`, o tom ale až později)
// (Poznámka: tři lomítka `///` slouží pro psaní dokumentace „ructdoc“)
```

Funkce `return_the_longer_one` se nezkompiluje. Borrow checker předem nepozná, zdali funkce vrátí `s1` nebo `s2`. Po zavolání `sr = return_the_longer_one(...)` tedy nemá jak zjistit, zdali reference `sr` nebude žít déle než hodnota, na kterou ukazuje. V tomto případě musíme borrow checkeru pomoci s využitím anotací životnosti. Referenci na libovolný datový typ `T` (kterou zapisujeme `&T`) anotujeme takto: `&'jmeno_anotace T`. Tím jsme k danému členu přidali anotaci s názvem `'jmeno_anotace`. Anotace životnosti musí začínat jednoduchou uvozovkou `'`. Pokud je chceme využít v signatuře funkce, musíme je vypsat do špičatých závorek za její název.

```rust
fn return_the_longer_one<'a>(s1: &'a String, s2: &'a String) -> &'a String {
    if s1.len() > s2.len() { s1 } else { s2 }
}
```

Tímto jsme borrow checkeru řekli, že výstupní reference bude validní tak dlouho, dokud budou validní oba vstupní parametry. Jinými slovy životnost výstupu bude rovna té kratší ze životností dvou vstupních parametrů. Touto anotací nijak neměníme životnost žádné ze zúčastněných hodnot, pouze pomáháme borrow checkeru – v podstatě mu popisujeme význam této funkce. Ten pak nedovolí kompilaci, pokud by náš kód, který tuto funkci využívá, nějakým způsobem porušoval daná pravidla.

```rust
let result;
let ms1 = String::from("123");
let ms2 = String::from("12345");
{
    result = return_the_longer_one(&ms1, &ms2);
}
println!("Delší řetězec: {result}");  // → Delší řetězec: 12345

// ↑ Tento kód je v pořádku
// Prohozením dvou konkrétních řádků nastává problém, na který borrow checker díky přidaným anotacím životnosti přijde:

let result;
let ms1 = String::from("123");
{
    let ms2 = String::from("12345");             // Zde je vytvořeno `ms2`
    result = return_the_longer_one(&ms1, &ms2);  // Zde probíhá výpůjčka
}                                                // Zde je zahozeno `ms2`
//println!("Delší řetězec: {result}");           // Nelze, výsledkem by mohla být zahozená `ms2`
```

V existujícím kódu někdy můžeme narazit na `'_`. Tento zápis lze použít, pokud chceme zdůraznit, že daný člen má nějakou životnost, i když si ji kompilátor dokáže odvodit. V tomto případě tedy nepomáháme borrow checkeru, ale jinému programátorovi, který používá náš kód. Anotace životnosti lze totiž kromě k parametrům funkcí přidávat i k položkám struktury, kde může zápis `'_` [objasnit význam kódu](https://publish.reddit.com/embed?url=https://www.reddit.com/r/rust/comments/w06q7e/comment/igds6t1/).

## Struktury

Struktura je programátorem definovaný datový typ, který zapouzdřuje více pojmenovaných souvisejících hodnot.

```rust
struct NPC {
    name: String,
    location: String,
    hitpoints: u32,
}

// ...

let npc1 = NPC {
    name: String::from("Easy Pete"),
    location: String::from("Goodsprings"),
    hitpoints: 60,
};

// Přístup k hodnotám pomocí tečkové notace
println!("{}", npc1.name);  // → Easy Pete
//npc1.hitpoints += 5;      // Momentálně nelze, můžeme ale strukturu nastavit jako mutable: `let mut npc1 = NPC {...}`

let npc2 = NPC {
    name: String::from("Trudy"),
    hitpoints: npc1.hitpoints + 35,  // → 95
    ..npc1  // Zbylé položky se přesunou z npc1 (v tomto případě pouze `location`)
            // stejného bychom docílili pomocí `location: npc1.location`
};

// Stále můžeme vypsat `name` z obou struktur
println!("{}", npc2.name);  // → Trudy
println!("{}", npc1.name);  // → Easy Pete

// U `location` nastává problém, je to totiž přesunutý řetězec
println!("{}", npc2.location);    // → Goodsprings
//println!("{}", npc1.location);  // Nelze

// Hodnota `npc1.location` se přesunula stejně, jako kdybychom ji někam přiřadili pomocí `=`
```

Speciální typ struktury je struktura bez pojmenovaných položek, kterou lze chápat jako pojmenovaný tuple.

```rust
struct RGB(u8, u8, u8);
// ...
let green = RGB(0, 255, 0);  // Vytvoření
let RGB(r, g, b) = green;    // „Rozbalení“ hodnot
println!("{} {} {} == {} {} {}", r, g, b, green.0, green.1, green.2);  // → 0 255 0 == 0 255 0
```

Pojmenovat lze i unit (tuple bez hodnot, `()`), tím nám vzniká _unit-like struct_: `struct UnitLikeStruct;`.

### Metody struktur

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn rectangle_area(rect: &Rectangle) -> u32 {
    rect.width * rect.height
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

// ...

let rect1 = Rectangle {
    width: 70,
    height: 30,
};
println!("rect1 is {rect1:?}");  // → rect1 is Rectangle { width: 70, height: 30 }
                                 // Pro výpis s každou položkou na vlastním řádku lze použít `rect1:#?`

println!("{}, {}", rectangle_area(&rect1), rect1.area())  // → 2100, 2100
```

Přidáním `#[derive(Debug)]` nad definici struktury bude struktura implementovat metodu _Debug_ vhodnou pro ladění, kterou pak můžeme volat přidáním `:?` (případně `:#?`) za název instance struktury. Řádky tvaru `#[...]` se nazývají [atributy](https://doc.rust-lang.org/rust-by-example/attribute.html).

Pomocí `impl Rectangle` vytvoříme blok, ve kterém všechny definice přísluší struktuře `Rectangle`. Díky tomu můžeme pro strukturu snadněji definovat metody: parametr `&self` je ve skutečnosti zkrácený zápis pro `self: &Self`, a `Self` je alias pro strukturu daného `impl` bloku. Pokud bychom chtěli definovat metodu, která mění hodnoty struktury, tak jako první parametr použijeme `&mut self`. Pokud jako první parametr nezvolíme ani jednu variantu `Self`, dostáváme funkci, která není spjatá s konkrétní instancí dané struktury, ale s typem struktury obecně. Takovou metodu pak voláme pomocí tzv. čtyřtečky `::`.

```rust
impl Rectangle {
    fn square(size: u32) -> Self {
        // Vracíme `Self`, tedy Rectangle, protože metoda je definována v bloku `impl Rectangle`
        Self {
            width: size,
            height: size,
        }
    }
}
// ...
let square = Rectangle::square(66);
println!("{square:?}");  // → Rectangle { width: 66, height: 66 }
```

## Enum a `match`

```rust
enum Location {
    // Enum varianta bez přidružených dat
    NotSpecified,
    // Enum varianta s přidruženými daty (String)
    Address(String),
    // Enum varianta s přidruženými daty (struct)
    Coordinates { latitude: f64, longitude: f64 },
}

// Stejně jako pro struktury lze pro enumy definovat metody
impl Location {
    // Definujeme metodu, která vypíše Location a příslušná přidružená data podle její varianty
    // `&self` je zkrácený zápis pro `self: &Self`, kde `Self` je alias pro Location (díky použití `impl`)
    fn print(&self) {
        // `match` je podobný konstrukci `switch` z některých jazyků
        match self {
            // Je daný `self: &Location` typu `Location::NotSpecified`?
            Self::NotSpecified => {
                println!("Not specified");
            }
            // Přidružená data (v tomto případě jeden řetězec) si vyzvedneme např. do proměnné `text`
            Self::Address(text) => {
                println!("Address: {text}");
            }
            // Vyzvednutí přidružených dat ze struktury
            Self::Coordinates {
                latitude: lat,
                longitude: lon,
            } => {
                println!("Coordinates: {lat}, {lon}");
            }
        }
    }
}

// ...

let liberec = Location::Coordinates {
    latitude: 50.7702648,
    longitude: 15.0583947,
};
let atlantis = Location::NotSpecified;

liberec.print();   // → Coordinates: 50.7702648, 15.0583947
atlantis.print();  // → Not specified
```

Konstrukce `match` musí pokrýt všechny hodnoty, kterých může vstup nabývat. S tím nám může pomoci _catch-all pattern_.

```rust
let number = rand::thread_rng().gen_range(1..=6);  // Náhodné číslo od jedné do šesti (včetně)

match number {
    6 => println!("The number is SIX"),
    other => println!("The number is {other}"),  // Vyzvednutí nepokrytých hodnot, např. do proměnné `other`
}

match number {
    6 => println!("The number is SIX"),
    _ => println!("The number is NOT SIX"),  // Hodnota nepokryté hodnoty nás nezajímá, podtržítkem ji zahodíme
}

match number {
    6 => println!("The number is SIX"),
    _ => (),  // Nedělej nic, pokud číslo není šestka (respektive vrať „unit“, tedy prázdný tuple)
}

// Makro `unreachable!` nám umožňuje kompilátoru označit nedosažitelný kód
match number {
    1..=5 => println!("The number is NOT SIX"),
    6 => println!("The number is SIX"),
    _ => unreachable!(),  // Zakomentováním tohoto řádku by se kód nezkompiloval; nebyly by pokryty všechny hodnoty.
                          // My ale víme, že jiný případ nemůže nastat.
}
```

### `Option<T>`: `Some(T)` a `None`

`Option<T>` je enum definovaný standardní knihovnou a reprezentuje hodnotu, která může být nějakým `T`, nebo také ničím, přičemž tyto dva stavy se značí `Some(T)` a `None`. Tento mechanismus nahrazuje hodnotu `null` z některých programovacích jazyků. Prefix `Option::` je implicitní, v kódu tedy není třeba psát `Option::None`, ale stačí pouhé `None`.

```rust
//let o_num0 = None;              // Nelze, kompilátor implicitně nepozná T
let o_num0: Option<i32> = None;   // Ok

let n_num1 = 5;                   // Implicitně i32
let o_num1 = Some(5);             // Implicitně Option<i32>
//let result1 = n_num1 + o_num1;  // Nelze sečíst i32 a Option<i32>

// Pro sečtení n_num1 + o_num1 můžeme využít naší znalosti `match`
let result1 = n_num1
    + match o_num1 {
        None => 0,
        Some(val) => val,
    };
println!("{result1}");  // → 10

// To je trochu krkolomné, `Option<T>` naštěstí nabízí spoustu funkcí, jako je např. `unwrap_or`,
// která vrací `Some(T)`, pokud existuje; jinak vrací výchozí hodnotu, kterou dostane jako parametr.
let result2 = n_num1 + o_num1.unwrap_or(0);
println!("{result2}");  // → 10
```

### `if let` a `let else`

Konstrukce `if let` umožňuje zkrátit zápis pro `match`, který plně reaguje pouze na jeden možný případ a zbylé hodnoty ignoruje.

```rust
let number = rand::thread_rng().gen_range(1..=6);
match number {
    6 => println!("The number is SIX"),
    _ => ()  // Nedělej nic
}
// Lze zapsat zkráceně jako:
if let 6 = rand::thread_rng().gen_range(1..=6) {
    println!("The number is SIX");
}

// V konstrukci `if let` je možné využít „vyzvednutí přidružených dat“
// Předpokládejme `fn computation_that_may_fail() -> Option<u8> {...}`
let result = computation_that_may_fail();
match result {
    Some(val) => println!("Result is {val}."),
    _ => (),
}
// Lze zapsat zkráceně jako:
if let Some(val) = computation_that_may_fail() {
    println!("Result is {val}.");
}

// Vyzvednutí přidružených dat ze struktury a využití větve `else`
// Předpokládejme `fn get_location() -> Location {...}`
if let Location::Coordinates {
    latitude: lat,
    longitude: lon,
} = get_location()
{
    println!("Coordinates: {lat}, {lon}");
} else {
    println!("Location does not have specified coordinates.")
}

// Stejně jako samotný `if` je možné kontrukci `if let` přiřadit do proměnné
let result = if let Some(val) = computation_that_may_fail() { val } else { 0 };  // (Zde je větev `else` povinná)

// V konstrukci lze využít i tuplů
if let (true, Some(val)) = (func_that_returns_bool(), computation_that_may_fail()) {...}

// Vyzvednutá přidružená data lze použít už v navazující podmínce
if let Some(val) = computation_that_may_fail() && val == 6 {...}
```

Konstrukce `let <vzor> = <výraz> else` obsahuje vždy pouze jednu větev, která se provede, pokud _výraz_ neodpovídá _vzoru_. Tato větev musí nějak zařídit, aby se kód za konstrukcí `let else` neprovedl: např. pomocí `return`, `break`, `continue`, `panic!` atd.

Pokud _výraz_ _vzoru_ odpovídá, pak lze využít vyzvednutí přidružených dat. Vyzvednutá data lze v tomto případě použít „za konstrukcí“.

```rust
fn double_or_zero_if_failed() -> u8 {
    let Some(number) = computation_that_may_fail() else {
        return 0;
    };

    // Pokud přiřazení v `let else` uspěje, můžeme zde použít proměnnou `number`
    number * 2  // Vracíme dvojnásobek
}
// Kdyby přiřazení neuspělo, `number` by neměla hodnotu,
// a proto je nutné ve větvi `else` zařídit, aby nedošlo k provádění kódu, který ji využívá.
```

### `Result<T, E>`: `Ok(T)` a `Err(E)`; `panic!`

`panic!` je makro, které je voláno, pokud program narazí na chybu, ze které se nelze zotavit. Toto makro je voláno buď implicitně při pokusu o&nbsp;provedení invalidní operace (dělení nulou, přístup na prvek mimo rozsah kolekce, ...), nebo explicitně použitím makra v aplikačním kódu. Po zavolání `panic!("xyz")` program vypíše `xyz` a další informace, uklidí po sobě a ukončí se.

Pro reakce na chyby, ze kterých se zotavit lze, slouží enum `Result<T, E>`, kde `T` je typ výsledku při úspěšném provedení a `E` je typ chyby, pokud se něco nepovede. Na výsledek operace vracející `Result` lze reagovat konstrukcí `match`. Metoda chyby `kind` vrací enum `ErrorKind`, díky němuž lze reagovat na konkrétní typy chyb.

```rust
let file_name = "some_file.txt";
let file_open_result = File::open(file_name);  // `file_open_result` je typu `Result<File, io::Error>`

// Rozvětvíme podle typu Result
let file = match file_open_result {
    // Pokud se soubor podařilo otevřít, rozbalíme jej z Result a přiřadíme do `file`; hotovo
    Ok(f) => f,
    // Pokud se soubor nepodařilo otevřít, rozbalíme chybu z Result, podle které můžeme dále rozvětvit
    Err(e) => match e.kind() {
        // Je-li chybou `NotFound`, zkusíme soubor vytvořit a rozvětvíme podle výsledku
        ErrorKind::NotFound => match File::create(file_name) {
            // Pokud se soubor podařilo vytvořit, rozbalíme jej z Result a přiřadíme do `file`; hotovo
            Ok(f) => f,
            // Pokud se soubor nepodařilo vytvořit, rozbalíme chybu z Result, vypíšeme ji a ukončíme program
            Err(e) => panic!("File not found and couldn't be created: {e:?}"),
        },
        // Nejedná-li se o chybu typu `NotFound`, tak ji vypíšeme a ukončíme program
        _ => panic!("File couldn't be opened: {e:?}"),
    },
};
```

↑ Tento kód obsahuje tři do sebe vnořené konstrukce `match` a nemusí být příliš čitelný. Zjednodušit by šel s využitím anonymních funkcí / uzávěrů (konkrétně `unwrap_or_else`), které budou probrány později.

Pro enum `Result` lze použít konstrukce `if let` a `let else` stejným způsobem, jako pro enum `Option`, tedy např. `if let Ok(x) = y {...}`.

#### `unwrap`, `expect` a operátor `?`

`unwrap` a `expect` jsou připravené metody, které lze použít v situaci, kdy pro daný výsledek typu `Result` chceme v případě `Ok` rozbalit a přiřadit hodnotu a v případě `Err` vyvolat paniku. Metoda `unwrap` nemá žádný vstupní parametr a metoda `expect` má jeden parametr, který určí zprávu makra `panic!`.

```rust
let file_open_result = File::open(file_name);
let file = file_open_result.unwrap();
// nebo
let file = file_open_result.expect("File couldn't be opened.");
```

Operátor `?` je trochu podobný metodě `unwrap`, ale v případě selhání namísto paniky danou chybu vrátí, jako kdybychom volali `return Err(E);`. Používá se tedy pro zkrácení zápisu v situaci, kdy definujeme funkci, která případné chyby vrací volajícímu.

```rust
pub fn read_file_relative_match(file_name: String) -> Result<String, io::Error> {
    // Zjistit aktuální pracovní adresář; může selhat např. z důvodu nedostatečných práv
    let path = match std::env::current_dir() {
        Ok(val) => val,
        Err(err) => return Err(err),
    };
    // Přečíst soubor v adresáři; může selhat z mnoha důvodů
    std::fs::read_to_string(path.join(file_name))  // Tohle už si zpracuje volající
}

pub fn read_file_relative_question_mark(file_name: String) -> Result<String, io::Error> {
    // Zjistit aktuální pracovní adresář; může selhat např. z důvodu nedostatečných práv
    let path = std::env::current_dir()?;  // Pokud Ok, přiřaď; jinak vrať Err
    // Přečíst soubor v adresáři; může selhat z mnoha důvodů
    std::fs::read_to_string(path.join(file_name))  // Tohle už si zpracuje volající
}

pub fn read_file_relative_even_shorter(file_name: String) -> Result<String, io::Error> {    
    std::fs::read_to_string(std::env::current_dir()?.join(file_name))
}
```

Kromě funkcí, které vracejí `Result<T, E>`, funguje operátor `?` i ve funkcích vracejících `Option<T>`: pokud je výsledek dané operace `Some(T)`, pak jej přiřadí; pokud je výsledkem `None`, pak jej okamžitě vrací.

## Kolekce

### Vector

Vektor je dynamická homogenní kolekce.

```rust
// = Vytvoření vektoru a naplnění hodnotami =
let mut v1: Vec<i32> = Vec::new();
v1.push(1);
v1.push(2);
v1.push(3);

// Makro pro rychlejší inicializaci vektoru s výchozími hodnotami
let v2 = vec![1, 2, 3];  // Implicitně Vec<i32>

// = Přístup k prvkům =
// Pomocí &v2[2] získáme referenci na prvek z `v2` s indexem 2; indexuje se od nuly
println!("{}", &v2[2]);     // → 3
//println!("{}", &v2[20]);  // Runtime error, index out of bounds

// Metoda `get` pro Vec<T> vrací Option<T>
let element = v2.get(20);
match element {
    Some(val) => println!("{}", val),
    None => println!("Out of bounds!"),  // → Out of bounds!
}

// = Pozor na pravidla pro práci s pamětí =
let v1element = &v1[0];     // Zde dochází k immutable výpůjčce
println!("{v1element}");    // → 1
v1.push(4);                 // Zde dochází k mutable výpůčce
//println!("{v1element}");  // Nelze, mutable výpůčka zneplatnila všechny předešlé výpůjčky

// = Iterace prvků přes referenci na vektor =
for el in &v1 {
    print!("{el} ");  // → 1 2 3 4
}

// Pokud chceme editovat prvky během iterace, pak iterujeme přes mutable referenci
for el in &mut v1 {
    *el *= 2;  // Dereference
}
// → 2 4 6 8

// = Výpis celého vektoru =
println!("{v1:?}");  // → [2, 4, 6, 8]

// = (Něco jako) heterogenního vektoru lze docílit pomocí enumů =
let locations = vec![
    Location::Coordinates {
        latitude: 50.7702648,
        longitude: 15.0583947,
    },
    Location::NotSpecified,
];

for loc in &locations {
    loc.print();  // →  Coordinates: 50.7702648, 15.0583947↲ Not specified↲
}
```

### Hash map

Funkci asociativního pole v jazyce Rust plní Hash map. Konkrétně `HasMap<K,V>`, kde `K` je typ klíče a `V` je typ hodnoty.

```rust
// = Import =
use std::collections::HashMap;

// = Vytvoření =
let mut ascii_map = HashMap::new();

// = Přidání dvojic =
ascii_map.insert(String::from("A"), 65);  // Po přidání první dvojice jsou implicitně nastaveny datové typy:
ascii_map.insert(String::from("B"), 66);  // klíčem je String a hodnotou je i32; tedy HashMap<String,i32>
//ascii_map.insert(1, 49);                // (Nelze, hashmapy jsou homogenní)

// = Získání hodnot =
// `get` pro dané `&K` vrací `Option<&V>`
// `copied` zkopíruje hodnotu z `Option<&V>` do `Option<V>`, které vrací
// `unwrap_or(x)` vrací `V`, pokud je daná `Option<V>` typu `Some(V)`; jinak vrací `x`
let a_code = ascii_map.get("A").copied().unwrap_or(-1);
println!("{a_code}");  // → 65

// = Iterace dvojic přes referenci na hashmapu =
for (key, value) in &ascii_map {
    print!("{key}: {value}, ");  // → A: 65, B: 66,
}

// = (Něco jako) default dictionary
let c_value = ascii_map.entry(String::from("C")).or_insert(65);
*c_value += 2;

if let Some(val) = ascii_map.get("C") {
    println!("{val}");  // → 67
}

// Pro změnu hodnoty pro daný klíč lze použít opět `insert`, tato funkce případně vrací Option<V>: Some(stará_hodnota) nebo None.
// Pro změnu závislou na staré hodnotě lze použít výše zmíněné `entry`, nebo `get_mut`:
ascii_map.insert(String::from("D"), 65);

if let Some(val) = ascii_map.get_mut("D") {
    *val += 3;
}
```

### String

String je kolekce bajtů, která slouží pro uložení textu.

```rust
// = Několik způsobů vytvoření =
let mut greet0 = String::new();
greet0.push_str("Dobrý den 0");

let greet1 = String::from("Dobrý den 1");

let greet2 = "Dobrý den 2".to_string();

// = Sčítání Stringů =
let part1 = String::from("Dobrý ");
let part2 = String::from("den 3");
let greet3 = part1 + &part2;  // Ke Stringu lze přičíst referenci na String

println!("{greet3}");   // → Dobrý den 3
println!("{part2}");    // → den 3
//println!("{part1}");  // Nelze, hodnota z `part1` byla přesunuta; (`part2` byl pouze vypůjčen, takže jej lze dál používat)

// = Makro `format!` =
let part1 = String::from("Dobrý");
let part2 = String::from("den");
let greet4 = format!("{part1} {part2} 4");
println!("{greet4}");           // → Dobrý den 4
// `format!` neprovádí přesuny, proto lze `part1` a `part2` používat nadále
println!("{part1} {part2} 4");  // → Dobrý den 4

// = UTF-8 komplikace =
let utf8showcase = String::from("čača");

println!("{}", utf8showcase.len());            // → 6
println!("{}", utf8showcase.chars().count());  // → 4

for b in utf8showcase.bytes() {
    print!("{b} ");   // → 196 141 97 196 141 97
}
for ch in utf8showcase.chars() {
    print!("{ch} ");  // → č a č a
}

for (i, ch) in utf8showcase.char_indices() {
    print!("{i}{ch} ");  // → 0č 2a 3č 5a
}
for (i, ch) in utf8showcase.chars().enumerate() {
    print!("{i}{ch} ");  // → 0č 1a 2č 3a
}
```

## Slices

Slice je typ reference, která ukazuje na spojitou sekvenci prvků v kolekci. Jelikož se jedná o referenci, tak nepřebírá vlastnictví. Obecně ji zapisujeme jako `&jmeno_kolekce[<pocatectni_index_inkluzivni>..<koncovy_index_exkluzivni>]` a její datový typ se označuje jako `&[T]`.

```rust
let array: [u8; 4] = [0, 1, 2, 3];

let slice = &array[1..3];

for el in slice {     // For `&u8` in `&[u8]`
    print!("{el} ");  // → 1 2
}
```

### String slices

String slice je reference ukazující na sekvenci prvků v kolekci typu String. Má speciální označení `&str`.

```rust
//                            012345678
let mut greet = String::from("Dobry den");
let p1 = &greet[0..5];  // Pokud jedeme od začátku, je také možné psát [..5]
let p2 = &greet[6..9];  // Pokud jedeme do konce, je také možné psát [6..]
println!("{p1} {p2}");  // → Dobry den

let all = &greet[..];   // Celý `String` je převeden na `&str`
println!("{all}");      // → Dobry den

greet.clear();          // Zde probíhá mutable výpůjčka

//println!("{all}");    // Nelze, string slice (reference) byla zneplatněna
```

_String literals_ jsou také typu `&str`.

```rust
fn gimme_string_ref(input: &String) {...}
fn gimme_string_slice(input: &str) {...}  // Častější

let string = String::from("gimme gimme");
let slice = "shock treatment";

gimme_string_ref(&string);
gimme_string_ref(&String::from(slice));

gimme_string_slice(slice);
gimme_string_slice(&string[..]);
gimme_string_slice(&string);  // Ekvivaletní
```

S UTF-8 znaky přicházejí komplikace.

```rust
//                               0 3
//                               1245
let utf8showcase = String::from("čača");

println!("{}", &utf8showcase[..1]);  // Runtime error, byte index 1 is not a char boundary; it is inside 'č' (bytes 0..2)
println!("{}", &utf8showcase[..2]);  // → č
println!("{}", &utf8showcase[..3]);  // → ča
println!("{}", &utf8showcase[..4]);  // Runtime error, byte index 4 is not a char boundary; it is inside 'č' (bytes 3..5)
println!("{}", &utf8showcase[..5]);  // → čač
println!("{}", &utf8showcase[..6]);  // → čača
```

## Traity a `dyn`

Traity (_traits_) jsou podobné rozhraním (_interfaces_) z jiných programovacích jazyků. Pokud více typů implementuje stejný trait, pak je zaručeno, že mají určité společné vlastnosti. Konkrétně se jedná o metody, které na tyto typy lze volat a které jsou stanoveny v definici daného traitu.

```rust
// Typ NPC
struct NPC {
    name: String,
    location: String,
    hitpoints: u32,
}

// Typ Creature
struct Creature {
    name: String,
    variant: String,
    hitpoints: u32,
}

// Trait, který říká, že entita lze nějak shrnout pomocí řetězce
trait EntitySummary {
    // Seznam metod (signatur), které musí typy implementující tento trait dodefinovat:
    fn summarize_entity(&self) -> String;
}

// Pro „klasické“ metody struktury `NPC` bychom použili `impl NPC {...}`
// Pro implementaci traitu použijeme blok `impl nazev_traitu for nazev_typu {...}`
impl EntitySummary for NPC {
    fn summarize_entity(&self) -> String {
        format!("{} from {} has {} HP", self.name, self.location, self.hitpoints)
    }
    // Tento blok musí obsahovat definice všech metod daného traitu (v tomto případě má EntitySummary pouze jednu metodu)
}

impl EntitySummary for Creature {
    fn summarize_entity(&self) -> String {
        format!("{} {} has {} HP", self.variant, self.name, self.hitpoints)
    }
}

// Typ implemtující trait pak lze použít jako typ vstupního parametru funkce pomocí `impl nazev_traitu`
fn print_entity_summary(ent: &impl EntitySummary) {
    println!("{}", ent.summarize_entity());
}

fn main() {
    let npc = NPC {
        name: String::from("Easy Pete"),
        location: String::from("Goodsprings"),
        hitpoints: 60,
    };

    let creature = Creature {
        name: String::from("Cazador"),
        variant: String::from("Young"),
        hitpoints: 100,
    };

    print_entity_summary(&npc);       // → Easy Pete from Goodsprings has 60 HP
    print_entity_summary(&creature);  // → Young Cazador has 100 HP
}
```

Při definici traitu lze také určit výchozí implementaci metody, která je použita, pokud daný typ nedodá svou vlastní logiku.

```rust
trait EntitySummary {
    fn summarize_entity(&self) -> String {
        String::from("Unknown entity")
    }
}

// Aby struktura `Creature` stále splňovala trait `EntitySummary`, musí existovat tento blok
impl EntitySummary for Creature { }
// (↑ Pokud by trait obsahoval více metod, pak tento blok musí obsahovat definice všech metod,
// u kterých nechceme použít výchozí implementaci, nebo pro ně výchozí implementace neexistuje.)

// ...
print_entity_summary(&creature);  // → Unknown entity
```

Poznámky:

* Výchozí implementace metod v traitu mohou volat ostatní metody traitu, a to včetně metod bez výchozí implementace.

* `print_entity_summary(ent: &impl EntitySummary)` je syntaktický cukr pro `print_entity_summary<T: EntitySummary>(ent: &T)`.

* Pro vstupní parametr, který vyžaduje implementaci více traitů, použijeme operátor `+`:
  * `fce(item: &(impl Tr1 + Tr2))` nebo `fce<T: Tr1 + Tr2>(item: &T)`

* Složitější situace může zpřehlednit zápis `fn fce<T>(item: &T) -> u8 where T: EntitySummary {...}`.

* Jazyk Rust má několik předdefinovaných traitů, jako např. [trait Default](https://doc.rust-lang.org/beta/std/default/trait.Default.html).

Se znalostí polymorfismu bychom čekali, že si nyní budeme moct vytvořit kolekci `Vec<EntitySummary>` a do ní vložit jak `npc`, tak `creature`. V&nbsp;jazyce Rust ale ještě musíme před název traitu přidat klíčové slovo `dyn`, které z něj udělá tzv. _trait objekt_. To je potřeba ve chvílích, kdy dochází k tzv. [dynamickému výběru](https://cs.wikipedia.org/wiki/Dynamick%C3%BD_v%C3%BDb%C4%9Br).

```rust
let mut entities: Vec<&dyn EntitySummary> = Vec::new();

entities.push(&npc);
entities.push(&creature);

for ent in &entities {
    print!("{}, ", ent.summarize_entity());  // → Easy Pete from Goodsprings has 60 HP, Unknown entity,
}
```

Klíčové slovo `dyn` musí být použito společně s ukazatelem: buď s referencí, nebo s nějakým smart pointerem. Samotné `dyn EntitySummary` se nezkompiluje. Jelikož reference pracují pouze s výpůjčkami a nedokáží tedy přesunout hodnotu, jediným způsobem změny vlastníka trait objektu je použití smart pointerů, z nichž nejjednodušší je _box_.

## Smart pointer `Box<T>`

Box, je smart pointer, který umožňuje alokovat data na haldě. Mezi situace, kdy se hodí využít box, patří:

* Na místě, kde je potřeba znát přesná velikost určitého typu, chceme využít typ, jehož velikost během kompilace nelze zjistit
* Chceme změnit vlastníka většího objemu dat s jistotou, že nedojde k větším přesunům/kopírování na zásobníku
* Chceme vlastnit hodnotu, u které nás nezajímá její přesný typ, ale zdali implementuje určitý trait

Toto je jednoduchá ukázka použití boxu, která slouží zejména k představení syntaxe. Jelikož se nejedná ani o jednu ze tří situací popsaných výše, je zde použití boxu zbytečné. V běžných případech nám stačí klasická reference.

```rust
fn print_double_box(input: &Box<i32>) {
    println!("{}", input.deref() * 2);
}

{
    let int_box = Box::new(5);   // Implicitně `Box<i32>`
    println!("{int_box}");       // → 5
    print_double_box(&int_box);  // → 10
}  // Zde je uklizena jak hodnota `5` na haldě, tak ukazatel `int_box` na zásobníku
```

Změnu vlastnictví trait objektu využijeme např. v této situaci: `fn summarizable_entity_factory() -> Box<dyn EntitySummary> {...}`.

Box je unikátním vlastníkem daných dat a nelze jej tedy klonovat.

## Anonymní funkce / uzávěry

Uzávěr v jazyce Rust je funkce, kterou lze uložit do proměnné (nebo ji předat jako parametr atd.) a která může pracovat s „okolními proměnnými“.

```rust
let x = 1;  // „Okolní proměnná“

// Vstupy zapisujeme mezi dvě svislítka
let double_plus_x = |num: i32| -> i32 {
    2 * num + x
};

// Výstupní hodnotu kompilátor pozná, lze vynechat; pak nejsou třeba závorky {}...
let double_plus_x = |num: i32|
    2 * num + x
;

// ...tedy pokud nemá funkce více řádků
let double_plus_x = |num: i32| {
    let two = 2;
    two * num + x
};

println!("{}", double_plus_x(5));  // → 11

// Pokud je to jednoznačné, tak si kompilátor dokáže odvodit i datové typy vstupních parametrů
let double_plus_x = |num| 2 * num + x;

let val1 = double_plus_x(5);  // Typ vstupního parametru odvozen na i32
// ↑ Zakomentováním tohoto řádku by nebylo z čeho odvozovat a kód by tedy nešel zkompilovat

//let val2 = double_plus_x("5");  // Nelze (Expected: i32; Found: &str)
```

Kompilátor si odvodí i to, zdali uzávěr pracující s okolní proměnnou na ni bude potřebovat mutable referenci, nebo mu bude stačit immutable reference.

```rust
let mut list = vec![1, 2, 3];
println!("{list:?}");    // → [1, 2, 3]
let mut impl_mut_borrow = || list.push(list.last().unwrap_or(&0) + 1);  // Při definici uzávěru došlo k mutable výpůjčce
// --- Jeden z těchto dvou řádků musí být zakomentován ---
//println!("{list:?}");  // Zde probíhá immutable výpůjčka, která zneplatní předchozí mutable výpůjčku 
impl_mut_borrow();       // Zde je využito mutable výpůjčky
// --- --- --- --- --- --- --- --- --- --- --- --- --- ---
println!("{list:?}");    // → [1, 2, 3, 4]
```

Přidáním klíčového slova `move` před svislítka říkáme, že všechny okolní proměnné, na které by daný uzávěr normálně použil referenci, mají být přesunuty.

```rust
let s = String::from("abc");
let _ = || println!("{s}");       // Zde je použita reference
println!("{s}");                  // Lze
let _ = move || println!("{s}");  // Zde dochází k přesunu
//println!("{s}");                // Nelze
```

## Iterátory

Pomocí iterátorů lze postupně provádět určitou operaci na sekvenci položek.

```rust
let coll = vec![1, 2, 3];

let iter1 = coll.iter();
for val in iter1 /*zde dochází k přesunu*/ {
    print!("{val} ");  // → 1 2 3
}
// Zde už `iter1` nelze použít

// Metodu `next()` lze volat přímo; vrací `Option<Iterator::Item>`
let mut iter2 = coll.iter();  // V tomto případě musí být mutable
println!("{}", iter2.next().unwrap_or(&-1));  // → 1
println!("{}", iter2.next().unwrap_or(&-1));  // → 2
println!("{}", iter2.next().unwrap_or(&-1));  // → 3
println!("{}", iter2.next().unwrap_or(&-1));  // → -1
println!("{}", iter2.next().unwrap_or(&-1));  // → -1
// Iterátor jsme takto „vyčerpali“
println!("{}", iter2.sum::<i32>());  // → 0

// Není povinné vytvářet samostatnou proměnnou držící iterátor
let iter3 = coll.iter();
println!("{}", iter3.sum::<i32>());        // → 6
println!("{}", coll.iter().sum::<i32>());  // → 6

// `map` transformuje položky v sekvenci a vrací nový iterátor; takový typ funkce nazýváme „adaptér iterátoru“
let iter4 = coll.iter().map(|x| 2 * x);
println!("{}", iter4.sum::<i32>());  // → 12

// Převod mezi kolekcí a iterátorem
let coll2: Vec<_> = coll.clone().into_iter().collect();
println!("{coll:?} {coll2:?}");  // → [1, 2, 3] [1, 2, 3]

// `iter_mut` poskytuje iterátor, který může měnit jednotlivé hodnoty
// `enumerate` umožňuje při procházení kolekce kromě prvků získat i index iterace
//                  0  1  2  3  4
//                  *  *  *  *  *
let mut coll = vec![1, 2, 3, 4, 5];
for (index, value) in coll.iter_mut().enumerate() {
    *value *= index;
}
println!("{coll:?}");  // → [0, 2, 6, 12, 20]
```

## Příloha

### Datové typy a převody mezi nimi

<table><tbody>
<tr><th colspan=2>Celočíselné datové typy</th></tr>
<tr><td>Unsigned</td><td>Signed</td></tr>
<tr><td><code>   u8</code>&emsp;0–255</td><td><code>   i8</code>&emsp;-128–127</td></tr>
<tr><td><code>  u16</code>&emsp;0–65535</td><td><code>  i16</code>&emsp;-32768–32767</td></tr>
<tr><td><code>  u32</code>&emsp;0–~4 ⋅ 10<sup>9</sup></td><td><b><code>  i32</code></b>&emsp;~ ± 2 ⋅ 10<sup>9</sup></td></tr>
<tr><td><code>  u64</code>&emsp;0–~1 ⋅ 10<sup>19</sup></td><td><code>  i64</code>&emsp;~ ± 9 ⋅ 10<sup>18</sup></td></tr>
<tr><td><code> u128</code>&emsp;0–~3 ⋅ 10<sup>38</sup></td><td><code> i128</code>&emsp;~ ± 1 ⋅ 10<sup>38</sup></td></tr>
<tr><td><code>usize</code>&emsp;závislé na architektuře</td><td><code>isize</code>&emsp;závislé na architektuře</td></tr>
<tr><th colspan=2>S plovoucí řádovou čárkou</th></tr>
<tr><td colspan=2><code>f32</code>&emsp;přesnost 6 číslic v rozsahu zhruba 10<sup>±38</sup></td></tr>
<tr><td colspan=2><b><code>f64</code></b>&emsp;přesnost 15 číslic v rozsahu zhruba 10<sup>±308</sup></td></tr>
<tr><th colspan=2>Ostatní skaláry</th></tr>
<tr><td colspan=2><code>bool</code>&emsp;<code>true</code> nebo <code>false</code></sup></td></tr>
<tr><td colspan=2><code>char</code>&emsp;4bajtový Unicode</td></tr>
</tbody></table>

> [!NOTE]  
> Zde jsou pro představu uvedeny některé užitečné metody některých datových typů ☺. Pro všechno ostatní má Rust povedenou [dokumentaci](https://doc.rust-lang.org/stable/std/#primitives).

* Celočíselné datové typy, např. [`i32`](https://doc.rust-lang.org/std/primitive.i32.html)
  * `abs` – absolutní hodnota
  * `abs_diff` – vrátí absolutní hodnotu rozdílu dvou čísel; `println!("{}", 15_i32.abs_diff(45));  // → 30`
  * `is_multiple_of` – je toto číslo násobkem daného čísla?
  * `pow` – umocní na dané číslo
  * `signum` – výsledek funkce signum

* [`bool`](https://doc.rust-lang.org/std/primitive.bool.html)
  * `then` – vrací výsledek daného uzávěru, pokud je `bool` `true`, jinak `None`; `true.then(|| ...)`

* [`char`](https://doc.rust-lang.org/std/primitive.char.html)
  * `is_ascii_whitespace` – je znak whitespace?
  * `is_ascii_digit` – je znak číslice 0 až 9?

* [`Option`](https://doc.rust-lang.org/std/option/enum.Option.html)
  * `is_some_and` – je hodnota `Some` a zároveň pro ni daný uzávěr vrací `true`?; `x.is_some_and(|x| ...)`

#### Převody

```rust
// Příklad: z `i32` na `f64`
let number = 102;

let f1: f64 = number.into();
let f2 = number as f64;
let f3 = f64::from(number);

assert!(f1 == f2 && f2 == f3);
// Také viz `try_from`, `try_into`

// Z řetězce na jiný typ
let four: u32 = "4".parse().unwrap();      // OK
let four = "4".parse::<u32>().unwrap();    // OK
let four: u32 = "čtyři".parse().unwrap();  // Error
// (V produkci samozřejmě místo `unwrap` kontrolujeme vstupy a/nebo reagujeme na chyby)

// Z čísla na řetězec
let four = 4;
let four_str = four.to_string(); // → 4, typ String

// Ze znaku na číslici
if '4'.is_digit(10) {
    let four = '4'.to_digit(10).unwrap();
}

// Z číslice na znak
let four_char = char::from_digit(4, 10);
```

### Kolekce/řetězce/slices a jejich metody

> [!NOTE]  
> Zde jsou pro představu uvedeny některé užitečné metody některých kolekcí. Metody nejsou exkluzivní pro danou kolekci, např. `sort` lze samozřejmě zavolat i na `Vec`. Pro všechno ostatní má Rust povedenou [dokumentaci](https://doc.rust-lang.org/std/collections), na úvod je dobré přečíst si sekci _„When Should You Use Which Collection?“_.

* [`String`](https://doc.rust-lang.org/std/string/struct.String.html) a/nebo [`&str`](https://doc.rust-lang.org/std/primitive.str.html)
  * `contains` – obsahuje takovýto podřetězec?
  * `chars` – vrací iterátor charů, pozor na UTF-8
  * `is_empty` – je řetězec délky 0?
  * `len` – vrací délku řetězce, pozor na UTF-8
  * `lines` – vrací iterátor jednotlivých řádků textu
  * `split` – vrací iterátor obsahující položky rozdělené podle delimiteru; splitovat lze i podle výsledku daného uzávěru
  * `split_once` – rozdělí podle prvního výskytu delimiteru, vrací `Option<(&str, &str)>`; splitovat lze i podle výsledku daného uzávěru
  * `split_ascii_whitespace` – vrací iterátor obsahující položky rozdělené podle whitespace znaků
  * `starts_with` – začíná řetězec tímto prefixem? (podobně `ends_with`)
  * `strip_prefix` – vrací řetězec bez daného prefixu (podobně `strip_suffix`)
  * `trim_ascii_start` – odebere všechny whitespace znaky, které předchází prvnímu ne-whitespace znaku; (podobně `trim_ascii_end` a `trim_ascii`, nebo bez „`ascii`“, kdy je použita jiná definice pro to, co je považováno za whitespace znak)

* [Slices obecně](https://doc.rust-lang.org/std/primitive.slice.html)
  * `chunk_by` – rozdělí slice podle uzávěru, který pracuje s aktuální a předchozí položkou
  * `join` – rozbalí a spojí vnořené kolekce daným separátorem
  * `concat` – v podstatě `join` bez separátoru
  * Řazení:
    * `sort` – seřadí položky
    * `sort_by` – seřadí položky podle dané porovnávací funkce
    * `sort_by_key` – seřadí podle hodnoty, kterou vrátí daný uzávěr
    * Metody mohou mít k dispozici nestabilní verzi (`sort_unstable`), která nezaručuje zachování pořadí stejných prvků
  * `windows` – vrací iterátor všech „oken“ o dané délce; např. pro délku okna 3 vrací všechny po sobě jdoucí trojice
    * Okna se překrývají, pětiprvková kolekce tedy obsahuje tři trojice
  * `chunks` – vrací iterátor všech „chunků“ o dané délce; oproti oknům se chunky nepřekrývají

* [`Vec`](https://doc.rust-lang.org/std/vec/struct.Vec.html)
  * `let vector_name = vec![...];` – makro pro vytvoření
    * `let vector_name = vec![false; 5];` – pětkrát false
  * `println({vector_name:?});` – výpis všech prvků
  * `for element_name in &vector_name {...}` – iterace přes prvky
  * `get` – vrací `Option<&T>` pro prvek na daném indexu; může přijímat i rozsah, pak se místo jednoho prvku řeší slice
  * `pop` – odebere a vrátí poslední prvek
  * `push` – přidá prvek na konec
  * `with_capacity` – vytvoří vektor s minimální kapacitou
  * `truncate` zkrátí vektor na danou délku
  * `dedup` – odebere po sobě jdoucí stejné prvky; v seřazené kolekci tedy odebere všechny duplikáty
  * Pomocí [`splice`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.splice) lze vložit více položek na konkrétní index

* [`HashMap`](https://doc.rust-lang.org/std/collections/struct.HashMap.html)
  * `insert` – vloží dvojici klíč-hodnota
  * Výpis všech dvojic pomocí `println!("{ascii:?}");` (jednořádkový), nebo `println!("{ascii:#?}");` (víceřádkový)
  * `for (key, value) in &map_name {...}` – iterace dvojic
  * `get` – pro dané `&K` vrací `Option<&V>`
  * `insert` – vloží pár
  * `contains_key` – existuje takový klíč?
  * `map.entry("klíč").and_modify(|e| { *e *= 2 }).or_insert(1);` – pokud klíč existuje, vynásob hodnotu dvěma, jinak pro tento klíč vlož hodnotu 1

### Iterátory a jejich metody

* Pro převod kolekce na iterátor:
  * `into_iter` přebírá vlastnictví a vrací iterátor
  * `iter` provádí výpůjčku a vrací iterátor
  * `iter_mut` provádí mutable výpůjčku a vrací iterátor
  * Kombinace s klonováním: `.iter().cloned()`, `.clone().into_iter()`
* `for_each` – zavolá daný uzávěr na všechny prvky iterátoru
  * Více idiomatické je namísto `y.for_each(|x|...)` používat `for x in y`, pokud možno

> [!NOTE]  
> Zde jsou pro představu uvedeny některé užitečné metody iterátorů ☺. Pro všechno ostatní má Rust povedenou [dokumentaci](https://doc.rust-lang.org/std/iter/trait.Iterator.html).

* `enumerate` – vrací iterátor dvojic (index iterace, aktuální prvek)
* `filter` – ponechá jen ty prvky, pro které daný uzávěr vrátí `true`
* `map` – vrací iterátor obsahující prvky vzniklé zavoláním daného uzávěru na původní prvky
* `filter_map` – daný uzávěr, který vrací `Option`, zavolá na všechny prvky a zahodí hodnoty `None`; funguje tedy jako `map`, který může vynechat některé hodnoty
* `map_while` – `map` s daným uzávěrem vracejícím `Option`, který skončí při prvním `None`
* `take` – vezme prvních 𝑛 hodnot a skončí; lze navázat na repeat: `iter::repeat_with(|| 4).take(4) // čtyři čtyřky`;
* `skip` – vrací iterátor, který přeskočí prvních 𝑛 položek
* `take_while` – v podstatě `filter`, který skončí u prvního `false` – obsahuje všechny položky, které vyhovují danému uzávěru, jakmile narazí na první neshodu, končí
* `skip_while` – v podstatě opak `take_while` – přeskočí několik prvních položek, které vyhovují danému uzávěru, jakmile narazí na první neshodu, přestane skipovat a zbytek je zahrnut
* `flatten` – rozbalí vnořené struktury: `[a b [c [d] e] f] → [a b c d e f]`
* `flat_map` – `map` následované `flatten`
* `chain` – spojí dva iterátory do jednoho, zřetězí je za sebe
* `peekable` – vrací iterátor, který může používat `peek` pro zjištění následující hodnoty bez jejího „vyčerpání“
* `rev` – obrátí směr konečného iterátoru
* `zip` – ze dvou iterací udělá jednu iteraci párů; délka výsledné iterace je rovna kratšímu ze vstupů

&nbsp;

* `all` – vrací všechny prvky `true` pro daný uzávěr?
* `is_sorted_by` – je seřazeno podle dané funkce?

&nbsp;

* [`fold`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.fold) – dostává uzávěr, který má kromě aktuálního prvku na vstupu i „perzistentní“ akumulátor, který je na konci vrácen
* `position` – vrátí index první položky, která pro daný uzávěr vrací `true`
* `product` – vynásobí všechny prvky iterátoru
* `sum` – sečte všechny prvky iterátoru
* Extrémy:
  * `min` – vrátí nejmenší prvek iterátoru
  * `min_by` – vrátí nejmenší prvek podle dané porovnávací funkce
  * `min_by_key` – vrátí nejmenší prvek, který vrací nejmenší hodnotu pro daný uzávěr
  * To samé pro `max`

## Použité zdroje

* [The Rust Programming Language by Steve Klabnik, Carol Nichols, and Chris Krycho, with contributions from the Rust Community](https://doc.rust-lang.org/book)

* [Rust’s standard library API documentation](https://doc.rust-lang.org/stable/std/index.html)

* [Difference between iter, into_iter and iter_mut by Smart Contract Programmer](https://youtu.be/g2NOUos4ExE)
