# Java tahák

> [!NOTE]  
> Toto zdaleka není veškerý výčet funkcí a vlastností daného programovacího jazyka.<br>Jedná se pouze o&nbsp;mé zápisky vzniklé během řešení [AoC](https://github.com/RDMCz/AdventOfCode) a/nebo při (samo)studiu.

## Strukturované paradigma

### Datové typy

```java
// char to long
long l = Character.getNumericValue(ch);

// char to String
String s = Character.toString('s');
String s = 's' + "";

// long to int (safe)
int i = Math.toIntExact(l);

// long to String
String s = Long.toString(l);

// String to int
int num = Integer.parseInt("20");

// String to long
long num = Long.parseLong("20");
```

### Číselné soustavy

```java
// binary to decimal
int num = Integer.parseInt("110", 2);

// decimal to binary
String binNum = Integer.toBinaryString(6);
```

### Řetězce

```java
// split String
String[] parts = "a b c".split(" ");

// split String – speciální znaky (tečka, rouřítko, ...)
String[] parts = "a|b|c".split("\\|");

// contains
"abcd".contains("a")
"abcd".contains(c + "") // pro char c = 'a';

// count chars in String
long count = "elephant".chars().filter(ch -> ch == 'e').count();
```

### Kolekce

```java
// array of arrays (~2D array)
String[][] arrays = new String[5][]; // (lze definovat i velikost "druhé dimenze")

// array
char[] chars = "abcd".toCharArray();
Arrays.sort(chars);
Arrays.equals(chars, chars2)

// List
List<String> list = new ArrayList<>();
list.add("item");
list.size();
list.get(index);
Collections.sort(list);
Collections.reverse(list);

// Stack
Stack<Character> stack = new Stack<>();
stack.push('a');
char ch = stack.pop();

// HashMap ("Dictionary")
HashMap<String, Integer> map = new HashMap<>();
map.put("abcd", 3);
for (String s : map.keySet()) { System.out.println("key: " + s + " value: " + map.get(s)); }
for (int i : map.values()) { System.out.println("value: " + s) }
map.containsKey("abcd");
map.put("abcd", map.get("abcd") + 1); // Zvyš value o jedna
```

### String.format()

* `%[flags][width][.precision]conversion-character`
* Conversion characters:
  * `%s` string
  * `%d` decimal integers
  * `%f` floating-point numbers
  * `%t` date/time
  * `%b` boolean
  * `%c` character

```java
float f;
System.out.printf("%.2f%n", f);     // Zaokrouhlí na 2 desetinná místa
System.out.printf("%10.2f%n", f);   // Zaokrouhlí na 2 desetinná místa + minimální délka celého výpisu je 10
                                    // (doplní vlevo mezery, pokud je třeba)

int i;
System.out.printf("%04d%n", i);     // Mininální délka vypsaného čísla je 4 – doplní zleva nulami, pokud je třeba

String s;
System.out.printf("%20s")           // Minimální délka výpisu je 20, doplní zleva mezerami
System.out.printf("%-20s")          // Minimální délka výpisu je 20, doplní zprava mezerami
System.out.printf("%20.10s")        // Minimální délka výpisu je 20 a maximální délka řetězce je 10
                                    // (řetězec oříznut zprava, pokud je příliš dlouhý)
```

* `System.out.printf` a `System.out.format` nedávají na konec výpisu znak pro konec řádku, je tedy třeba použít `\n` nebo `%n`

### Locale

```java
Scanner sc = new Scanner(System.in);
Locale loc = new Locale("cs", "cz"); // ("en", "us")
sc.useLocale(loc);
System.out.printf(loc, "%f%n", f);
```

### Matice

```java
int[][] mx = new int[cols][rows];
// Hlavní diagonála: i == j
// Vedlejší diagonála i + j == rows - 1
```

### Random

```java
// Náhodné číslo v rozmezí min a max (obě inkluzivní)
Random rand = new Random();
int randomNumber = rand.nextInt((max - min) + 1) + min;
```

## Objektové paradigma

## Knihovní třída

* `public final` – třída je viditelná pro všechny, z třídy nelze dědit
* Privátní konstruktor – nelze tvořit instance
* Všechny metody statické

```java
public final class Library {
    private Library() {}
    // public static metody
}
```

## Porovnání podle Stringu v češtině

```java
public class Human implements Comparable<Human> {
    private String name;

    @Override
    public int compareTo(Human other) {
        Collator collator = Collator.getInstance(new Locale("cs","cz"));
        return collator.compare(this.name, other.name);
    }
}

// Poté můžeme řadit:
ArrayList<Human> humans = new ArrayList<>( ... );
Collections.sort(humans);
// Objekty typu Human budou v humans seřazeny podle atributu name s ohledem na českou abecedu
// (Collections.sort využívá metody compareTo ve třídě Human)
```

## Další metody řazení

### Předdefinovaný komparátor

```java
public static final Collator col = Collator.getInstance(new Locale("cs","cz"));
public static final Comparator<Human> COMP_BY_NAME = (Human h1, Human h2) -> {
    return col.compare(h1.getName(), h2.getName());
};

public void sortByNameConst() {
    Collections.sort(humans, COMP_BY_NAME)
}
```

### Vytvoření třídy Comparator

```java
public class ComparatorHumanByName implements Comparator<Human> {
    @Override
    public int compare(Human h1, Human h2) {
        return h1.getName().compareTo(h2.getName());
    }
}

// Poté můžeme řadit:
public void sortByName() {
    Collections.sort(humans, new ComparatorHumanByName());
}
```

### Anonymní vnitřní třída

```java
public void sortByNameAnonymous() {
    Collections.sort(humans, new Comparator<Human>(){
        @Override
        public int compare(Human h1, Human h2) {
            return h1.getName().compareTo(h2.getName());
        }
    });
}
```

### Lambda výraz

```java
public void sortByNameLambda() {
    Collections.sort(humans, (Human h1, Human h2) -> h1.getName().compareTo(h2.getName()));
}
```

## Práce se soubory

* Preferujeme konstrukci _try-with-resources_
* Proudy obvykle vyhazují výjimky, třeba odchytit

### Čtení textových souborů

#### BufferedReader

```java
File input = new File("D:\\text.txt");

try (BufferedReader br = new BufferedReader(new FileReader(input))) {
    String line;
    // Vypisujeme řádky dokud nenarazíme na prázdný
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
}
```

#### Scanner

```java
try (Scanner sc = new Scanner(input)) {
    //sc.useDelimiter(",");
    while(sc.hasNextLine()) {
        System.out.println(sc.nextLine());
    }
}
```

### Zápis textových souborů

#### PrintWriter

```java
File output = new File("D:\\text_out.txt");

try (PrintWriter pw = new PrintWriter(new BufferedWriter(new FileWriter(output)))) {
    pw.printLine(" ... ");
}
```

Druhým parametrem _FileWriteru_ může být `boolean append`.

### Zápis binárních souborů

```java
try (DataOutputStream out = new DataOutputStream(new FileOutputStream(binaryFile))) {
    out.writeUTF("Text");
    out.writeBoolean(false);
    out.writeInt(50);
}
```

Mezi proudy ještě můžeme přidat `BufferedOutputStream`.

### Čtení binárních souborů

```java
try (DataInputStream in = new DataInputStream(new FileInputStream(binaryFile))) {
    boolean end = false;
    while (!end) {
        try {
            System.out.println(in.readUTF());
            System.out.println(in.readBoolean());
            System.out.println(in.readInt());                    
        }
        catch (EOFException ex) {
            end = true;
        }
    }
}
```

Mezi proudy ještě můžeme přidat `BufferedInputStream`.

## Misc

<details>
  <summary>Největší společný dělitel, Bubble sort, Taylor sinus</summary>

### Největší společný dělitel

```java
// Největší společný dělitel čísel x1 a x2
int ogX1 = x1;
int ogX2 = x2;

// Eukleidův algoritmus
while (x1 != x2) {
    if (x1 > x2) {
        x1 -= x2;
    }
    else {
        x2 -= x1;
    }
}
int delitel = x1;

// Nejmenší společný násobek
int nasobek = (ogX1 * ogX2) / delitel;
```

### Bubble sort

```java
for (int i = 0; i < pole.length; i++) {
    for (int j = 0; j < pole.length - i - 1; j++) {
        if (pole[j] > pole[j + 1]) {
            int temp = pole[j + 1];
            pole[j + 1] = pole[j];
            pole[j] = temp;
        }
    }
}
```

### Taylor sinus

```java
double result = 0;
double citatel = x;
double jmenovatel = 1;
double number = 1;
double plusMinus = 1;
while (true) {
    zlomek = citatel/jmenovatel;
    if (Math.abs(zlomek) < eps) break;
    result += zlomek * plusMinus;
    plusMinus *= -1;
    jmenovatel *= ++number * ++number;            
    citatel = Math.pow(x, number);
}
System.out.println(result);
```

</details>
