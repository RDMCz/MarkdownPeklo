# Dart tahák

> [!NOTE]  
> Toto zdaleka není veškerý výčet funkcí a vlastností daného programovacího jazyka.<br>Jedná se pouze o&nbsp;mé zápisky vzniklé během řešení [AoC](https://github.com/RDMCz/AdventOfCode) a/nebo při (samo)studiu.

## Proměnné a typy

```dart
var x = 50;
x++;
print("x = $x");            // → x = 51
print("x + 1 = ${x + 1}");  // → x + 1 = 52

//x = "huh";  // Nelze: A value of type 'String' can't be assigned to a variable of type 'int'.

final y = 60;
//y++;  // Nelze: The final variable 'y' can only be set once.

print(y.runtimeType);  // → int

final double z = 70;  // Lze specifikovat datový typ
```

### Null

```dart
final n1 = null;              // Typ `dynamic`
//final double n2 = null;     // Nelze: A value of type 'Null' can't be assigned to a variable of type 'double'.
final double? n3 = null;      // Otazník znamená nullable typ
final double n4 = n3 ?? 4.0;  // Operátor `??` zde dosadí 4.0 do n4, pokud n3 je null, jinak dosadí n3
final double n5 = n3!;        // Vykřičníkem kompilátoru říkáme, že hodnota není null (v tomto případě vede na runtime error)
if (n3 != null) {
  final double n6 = n3;       // V tomto případě není vykřičník potřeba, kompilátor bere v potaz podmínku
}

double parsedNum = double.tryParse("2.5") ?? 0;
print(2 * parsedNum);  // → 5.0

parsedNum = double.tryParse("???") ?? 0;
print(2 * parsedNum);  // → 0.0
```

### String

* [String](https://api.dart.dev/dart-core/String-class.html) – sekvence znaků UTF-16
* [StringBuffer](https://api.dart.dev/dart-core/StringBuffer-class.html) – efektivní budování Stringu

### List

```dart
final numbers = [];
numbers.add(int.parse("10"));
numbers.add("10");
print("${numbers.runtimeType} $numbers");  // → List<dynamic> [10, 10]
numbers[0]++;
//numbers[1]++;  // Nelze: type 'int' is not a subtype of type 'String'
//numbers[2]++;  // Nelze: RangeError

final numbersStrict = <int>[];
numbersStrict.add(20);
//numbersStrict.add("20");  // Nelze
print("${numbersStrict.runtimeType} $numbersStrict");  // → List<int> [20]
// Kaskáda `..` umožňuje provést sekvenci operací na jednom objektu
numbersStrict..add(30)..add(40);

// Tři tečky `...` umožňují vložit všechny prvky kolekce do jiné kolekce
print([...numbers, "|", ...numbersStrict]); // → [11, 10, |, 20, 30, 40]

// Pro generaci listu lze použít tzv. control flow collection
final tripleStrict = [for (final item in numbersStrict) 3 * item];
print(tripleStrict); // → [60, 90, 120]

// Procházení listu s indexem
for (final (index, item) in tripleStrict.indexed) {
  print("$index $item");
  // → 0 60
  // → 1 90
  // → 2 120
}

// Lze také používat "iterátorové" funkce jako take, skip, map apod.
```

### Map

```dart
final Map<String, int> wordCount = {};
final sentence = "the quick brown fox jumps over the brown fox";

for (final word in sentence.split(" ")) {
  // Zvyš hodnotu klíče o jedna; pokud klíč neexistuje, tak jej nastav na jedna
  wordCount.update(word, (v) => v + 1, ifAbsent: () => 1);
}
print(wordCount);  // → {the: 2, quick: 1, brown: 2, fox: 2, jumps: 1, over: 1}

final sortedByVal = wordCount.entries.toList()
  ..sort((a, b) => b.value.compareTo(a.value));

for (final entry in sortedByVal) {  
  stdout.write("${entry.key} x ${entry.value}, ");  // (print bez nového řádku)
  // → the x 2, brown x 2, fox x 2, quick x 1, jumps x 1, over x 1, 
}

// Pro tvorbu mapy lze také použít control flow collection
final powers2 = {for (int i = 1; i < 6; i++) i: pow(2, i)};
print(powers2);  // → {1: 2, 2: 4, 3: 8, 4: 16, 5: 32}
```

## Podprogramy

```dart
// Pro jednořádkové funkce lze použít kompaktní zápis
int dvojnasobek(int i) => 2 * i;

// Pojmenované parametry umisťujeme do {}, musí mít výchozí hodnotu, nebo být povinné či nullable (`required` či `?`)
int nasobek(int i, {required int kolika, bool vypis = false}) {
  final result = kolika * i;
  if (vypis) {
    print(result);
  }
  return result;
}

print(dvojnasobek(20));               // → 40
nasobek(20, kolika: 3, vypis: true);  // → 60
```

## Třídy

```dart
class Counter {
  // = Atributy =
  final String name;
  int value;
  // Atributy a metody začínající podtržítkem jsou viditelné pouze ve stejné library (obvykle stejný .dart soubor)
  final bool _doLog;

  // = Konstruktory =
  // V závorce s `this` nastavuje uživatel, zbytek za dvojtečkou nastavuje třída
  Counter(this.name, this._doLog) : value = 0;
  // Použití konstruktoru uvnitř třídy k vytvoření statického objektu
  static Counter globalCnt = Counter("Global counter", true);
  // Pojmenovaný konstruktor odkazující se na jiný konstruktor pomocí `this`
  Counter.withoutLogging(String name) : this(name, false);

  // = Metody =
  void increment() {
    value++;
    if (_doLog) {
      _log();
    }
  }

  void _log() {
    print("Counter '$name' was incremented to $value.");
  }

  @override
  String toString() => "$name :: $value";

  // Je také možné implementovat gettery a settery, viz https://dart.dev/language/methods#getters-and-setters
}

var c = Counter.withoutLogging("my_cnt");
c.increment();
print(c.value);  // → 1
print(c);        // → my_cnt :: 1

Counter.globalCnt.increment();  // → Counter 'Global counter' was incremented to 1.
```
