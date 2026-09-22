# PowerShell scripting tahák

> [!NOTE]  
> Toto zdaleka není veškerý výčet funkcí a vlastností daného programovacího jazyka.<br>Jedná se pouze o&nbsp;mé zápisky vzniklé během řešení [AoC](https://github.com/RDMCz/AdventOfCode) a/nebo při (samo)studiu.<br><br>Zároveň se zde vůbec nezabývám administrací OS Windows z prostředí PowerShell,<br>ale pouze prostředky pro psaní „běžných“ algoritmů.<br><br>Psáno při používání PowerShellu ve verzi 5.1 na OS Windows 10.

* [Přivítání](#přivítání)
* [Proměnné a jejich výpis](#proměnné-a-jejich-výpis)
* [Řetězce a backtick](#řetězce-a-backtick)
* [Operace s řetězci](#operace-s-řetězci)
* [Datové typy a převody](#datové-typy-a-převody)
* [Podmínky a nearitmetické operátory](#podmínky-a-nearitmetické-operátory)
* [Cykly](#cykly)
* Kolekce
  * [Pole](#pole)
  * [Vícerozměrná pole](#vícerozměrná-pole)
  * [List](#list)
  * [Asociativní pole](#asociativní-pole)
* [OOP](#oop)

## Přivítání

```ps1
Write-Host -Object "Hello world!"

Write-Host "Hello world!"
Write-Host "Hello world!" -ForegroundColor Black -BackgroundColor White
```

Příkaz („cmdlet“) `Write-Host` vypisuje hodnotu parametru `-Object` do terminálu. Parametr `-Object` není třeba uvádět, tedy `Write-Host -Object "abc"` se chová stejně jako `Write-Host "abc"`. Příkaz má k dispozici další parametry, jejichž hodnoty můžeme definovat, např. barva popředí a pozadí. Obecně tedy píšeme `Příkaz -Parametr Hodnota`.

Kompletní info o příkazu lze nalézt v MS dokumentaci, v tomto případě [Write-Host](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/write-host). Názvy příkazů splňují konvenci pojmenování `Verb-Noun`: verb říká, jaká akce bude provedena (_Write_), a noun říká, s čím bude tato akce provedena (_Host_).

Příkaz `Write-Host` ve skutečnosti nepotřebuje ani uvozovky; dokud nenarazí na parametr, tak se snaží vše převést na text. Proto funguje také `Write-Host Hello world!`, určitě se ale nejedná o best practice.

## Proměnné a jejich výpis

```ps1
<#
Název proměnné začíná dolarem.
#>
$number5 = 5
$number6 = 6
$text = "ooga booga"

<#
Řádek obsahující samotný název proměnné vypíše její hodnotu, případně ji i vrací.
Chování je stejné jako kdybychom napsali název proměnné do terminálu – odpovědí je hodnota.
#>
Write-Host $number5  # → 5
$number5             # → 5

<#
Příkaz Write-Host implicitně nevyhodnocuje výrazy, je potřeba použít závorky.
#>
$number5 + $number6               # → 11
Write-Host $number5 + $number6    # → 5 + 6
Write-Host ($number5 + $number6)  # → 11

Write-Host ($text + " " + $text)          # → ooga booga ooga booga
Write-Host ($text + $number5 + $number6)  # → ooga booga56
Write-Host ($number5 + $text)             # Error

<#
Pro "string interpolation" samostatné proměnné ideálně použít ${}.
Pro výrazy je třeba $().
#>
Write-Host "Hodnota jest $number5..."                # → Hodnota jest 5...
Write-Host "Hodnota jest ${number5}..."              # → Hodnota jest 5...
Write-Host "Hodnota jest ($number5 + $number6)..."   # → Hodnota jest (5 + 6)...
Write-Host "Hodnota jest $($number5 + $number6)..."  # → Hodnota jest 11...
Write-Host "Hodnota jest $($number5)..."             # → Hodnota jest 5...

<#
Pomocí New-Variable, Get-Variable, Set-Variable lze s proměnnými pracovat „dynamicky“...
#>
$var1name = "x1"
$var1val = 100
$var2name = "x2"
$var2val = 200

New-Variable -Name $var1name -Value $var1val
$x2 = $var2val

Write-Host $x1                                  # → 100
Write-Host (Get-Variable $var2name -ValueOnly)  # → 200
```

## Řetězce a backtick

PowerShell používá backtick (`` ` ``) jako escape character.

```ps1
Write-Host "Row 1`nRow 2`nRow 3"
Write-Host "1`" = 2.54 cm"
<# →
Row 1
Row 2
Row 3
1" = 2.54 cm
#>
```

Řetězec v jednoduchých uvozovkách (`'`) se nazývá _verbatim string_ a nefunguje v něm žádná interpolace ani escapování.

```ps1
Write-Host 'Row 1`nRow 2`nRow 3'
Write-Host '1" = 2.54 cm'
Write-Host 'Jednoducha uvozovka ('') ve verbatim stringu escapuje sama sebe.'
<# →
Row 1`nRow 2`nRow 3
1" = 2.54 cm
Jednoducha uvozovka (') ve verbatim stringu escapuje sama sebe.
#>
```

Víceřádkové řetězce lze psát pomocí tzv. _here-strings_, které berou v potaz nové řádky ve zdrojovém kódu. Musí začínat otevírací značkou `@'` nebo `@"` následovanou novým řádkem a končit uzavírací značkou `'@` nebo `"@`, které předchází nový řádek.

```ps1
Write-Host @'
Row 1
Row 2
Row 3
'@
Write-Host @"
Row 4
Row ${number5}
Row ${number6}
"@
<# →
Row 1
Row 2
Row 3
Row 4
Row 5
Row 6
#>
```

## Operace s řetězci

### Substring

```ps1
$str = "abcdefghi"

# Vypiš znaky od indexu 2 do konce
Write-Host ($str.Substring(2))  # → cdefghi

# Vypiš 3 znaky počínaje indexem 2
Write-Host ($str.Substring(2, 3))  # → cde

# ($str[2..4]) vrací kolekci znaků od indexu 2 do indexu 4 (včetně)
# ($str[2..4] -join "") spojí prvky kolekce do řetězce (mezi jednotlivé prvky je vloženo "", tedy nic)
# Pokud „joinujeme ničím“, můžeme použít kratší unární variantu (-join $str[2..4])
Write-Host (-join $str[2..4])      # → cde
Write-Host ($str[2..4] -join ",")  # → c,d,e

# Pro dosažení funkce příkazu $str.Substring(2) pomocí hranatých závorek potřebujeme
# zjistit délku, nebo použít libovolné dostatečně velké číslo – out of bounds nevadí
Write-Host (-join $str[2..($str.Length - 1)])  # → cdefghi
Write-Host (-join $str[2..99])                 # → cdefghi

# Indexace odzadu
Write-Host (-join $str[-7..-5])  # → cde

# Indexace pozpátku
Write-Host (-join $str[4..2])  # → edc

# Indexování rozsahem není to samé jako plnohodnotný slicing, více v sekci o polích.
```

### -replace

```ps1
$str = "powershell (mocna lastura)"               #   powershell (mocna lastura)   # Původní řetězec
Write-Host ($str -replace "lastura", "skorapka")  # → powershell (mocna skorapka)  # Nahraď slovo
Write-Host ($str -replace ".{8}$", "skorapka)")   # → powershell (mocna skorapka)  # Nahraď posledních 8 znaků
Write-Host ($str -replace "[^a-z ]")              # → powershell mocna lastura     # Smaž vše krom malých písmen a mezer

# V regulárních výrazech escapujeme backslashem, nikoliv backtickem.
# Pro nahrazení nalezenou skupinou regulárního výrazu je třeba jako druhý parametr použít verbatim string,
# ve kterém případně dolar escapuje sám sebe.
Write-Host ($str -replace ".*\((\w+) (\w+)\)", '$2 $1')      # → lastura mocna
Write-Host ($str -replace ".*\((\w+) (\w+)\)", '$2 $$1')     # → lastura $1

# Alternativně escapnout backtickem dolar v běžném stringu. 
Write-Host ($str -replace ".*\((\w+) (\w+)\)", "`$2 `$1")    # → lastura mocna
Write-Host ($str -replace ".*\((\w+) (\w+)\)", "`$2 `$`$1")  # → lastura $1
```

Více o regulárních výrazech („regex“) v PowerShellu na [MSLearn/about_Regular_Expressions](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_regular_expressions?view=powershell-5.1).

### -split

```ps1
function Demonstruj($splitted) {
    Write-Host ($splitted -join " | ")
}

$text = "a,b c,d  e,,f"

# Samotný split dělí podle mezer a více mezer za sebou je stále jeden split
Demonstruj (-split $text)  # → a,b | c,d | e,,f

# Ve variantě s hodnotou parametru dělíme podle zadaného podřetězce, více výskytů za sebou je ale splitováno zvlášť
Demonstruj ($text -split ",")  # → a | b c | d  e |  | f

# Pro zachování delimiteru ho obalíme do závorek; escapujeme případně backslashem
Demonstruj ($text -split "(,)")  # → a | , | b c | , | d  e | , |  | , | f

# Lze si napsat vlastní podmínku; $_ obsahuje aktuálně zkoumanou hodnotu
Demonstruj ($text -split { $_ -eq " " -or $_ -eq "," })  # → a | b | c | d |  | e |  | f
Demonstruj ($text -split { $_ -in @(" ", ",") })         # → a | b | c | d |  | e |  | f

# Pro case sensitive splitování potřebujeme -csplit
Demonstruj ("aAaAaAa" -split "A")   # →  |  |  |  |  |  |  |
Demonstruj ("aAaAaAa" -csplit "A")  # → a | a | a | a
```

## Datové typy a převody

```ps1
# Proměnné nemusí mít pevně nastavený datový typ
$real = 1.2
Write-Host $real.GetType()  # → System.Double
$real = "1.2"
Write-Host $real.GetType()  # → System.String

# Pro převod lze použít tzv. casting, pozor při ukládání do proměnné
$real = [float]1.2
Write-Host $real.GetType()  # → System.Single
$real++
Write-Host $real.GetType()  # → System.Double

# Pro pevně nastavený datový typ proměnné castujeme identifikátor, nikoliv hodnotu
[float]$real = 1.2
Write-Host $real.GetType()  # → System.Single
$real++
Write-Host $real.GetType()  # → System.Single

# Pro ověření datového typu lze použít operátor -is
Write-Host $(64 -is [int])  # → True

# Před castováním vhodno použít -as a výsledek porovnat s $null pomocí -eq
Write-Host $("64" -as [int])                # → 64
Write-Host $("abcd" -as [int])              # (Nic)
Write-Host $([int]"abcd")                   # Error
Write-Host $($null -eq ("abcd" -as [int]))  # → True

# Pozor na aritmetiku řetězců
Write-Host ("50" + "3")  # → 503
Write-Host ("50" - "3")  # → 47
Write-Host ("50" / "3")  # → 16.6666666666667
Write-Host ("50" * "3")  # → 505050
```

## Podmínky a nearitmetické operátory

```ps1
if ($num -eq 1) { <#...#> }
elseif ($num -eq 2) { <#...#> }
else { <#...#> }
```

Podmínky fungují podobně jako ve většině „běžných“ jazycích, rozdíl je ale v operátorech. Krom toho, že se nepoužívají symboly, má většina operátorů dvě varianty lišící se v tom, zdali řeší či neřeší velikosti písmen při porovnávání řetězců.

```ps1
if ("lalilulelo" -eq "LaLiLuLeLo") {   # -eq neboli case-insensitive equality
    # Tohle se provede
}
if ("lalilulelo" -ceq "LaLiLuLeLo") {  # -ceq neboli case-sensitive equality
    # Tohle už ne
}
```

Pokud porovnáváme různé datové typy, nedostaneme chybu, ale výsledek nemusí být na první pohled zřejmý. PowerShell se totiž snaží převést druhý operand na typ toho prvního.

```ps1
Write-Host ("5" -eq 5)     # → True, "5" == "5"
Write-Host ("50" -gt 30)   # → True, řetězec "50" je někde za řetězcem "30" (pokud je seřadíme „abecedně“)
Write-Host ("50" -gt 300)  # → True, "50" je i za řetězcem "300"
Write-Host ("50" -gt 70)   # → False, "50" předchází řetězci "70"
Write-Host ("50" -gt 7)    # → False, "50" předchází i řetězci "7"
```

Základní relační operátory jsou `-eq`, `-ne`, `-gt`, `-ge`, `-lt`, `-le` a jejich case-sensitive verze, které začínají písmenem `c`. Datové typy lze porovnávat pomocí `-is` a `-isnot`. Logické operátory jsou pak `-and`, `-or`, `-not` (lze použít alias `!`) a `-xor`. K dispozici jsou také bitové operace, např. `band` nebo `shl`.

### -like a -match

```ps1
# `*` je libovolný počet znaků, `?` je jeden znak
"Windows 10 x64" -like "Windows * x??"
# -like, -notlike, -clike, -cnotlike

# Regex
"Windows 10 x64" -match "Windows .* x\d\d"
# -match, -notmatch, -cmatch, -cnotmatch
```

### Přiřazení ifu

```ps1
$num = if ($podminka) {
    return 1
} else {
    return 0
}
```

### Switch

Switch pro vstupní hodnotu postupně zkouší všechny své podmínky a u těch vyhovujících provede příkazy v jejich těle. Pro ukončení vyhodnocování lze použít `continue` nebo `break` (některé vlastnosti switche začnou dávat smysl až při použití s kolekcemi). Pro porovnávání řetězců jsou k dispozici parametry `-CaseSensitive` (v defaultu není), `-Wildcard` (analogické k `-like`) a `-Regex` (analogické k `-match`).

```ps1
$num = 5

switch ($num) {
    4 {  # Nic
        Write-Host "4"
    }
    5 {  # Match
        Write-Host "5"
    }
    6 {  # Nic
        Write-Host "6"
    }
    { $_ % 2 -ne 0 } {  # Match, pro vyhodnocení lze použít scriptblock; $_ obsahuje aktuálně zkoumanou hodnotu
        Write-Host '$_ % 2 -ne 0'
    }
    "5" {  # Match, { 5 -eq "5" } je vyhodnoceno jako $true; museli bychom použít { $_ -is [string] -and $_ -eq "5" }
        Write-Host '"5"'
    }    
    5 {  # Match, stejná podmínka nevadí
        Write-Host "5"
    }
    Default {  # Nic, platné pouze pokud nebyla použita žádná jiná větev
        Write-Host "Default"
    }
}

<# →
5
$_ % 2 -ne 0
"5"
5
#>
```

## Cykly

* For &ensp;`for ($i = 1; $i -lt 5; $i++) { ... }`
* While &ensp;`while ($i -lt 5) { ... }`
* Do-while &ensp;`do { ... } while ($i -lt 5)`
* Do-until &ensp;`do { ... } until ($i -ge 5)`
* Foreach, switch
* `break`, `continue`

```ps1
:label while ($podminka1) {
    while ($podminka2) {
        ...
        break label  # Vyskočí z cyklu označeného jako "label"
    }
}
```

## Kolekce

### Pole

#### [Pole] Vytvoření, výpis

```ps1
# Pole jsou ve výchozím stavu heterogenní, resp. pole objektů
$pole = @(1, 2, "Three", 4.01)
Write-Host $pole               # → 1 2 Three 4.01
Write-Host ($pole -join ", ")  # → 1, 2, Three, 4.01
Write-Host $pole.Count         # → 4
Write-Host $pole.GetType()     # → System.Object[]

# POZOR na násobení uvnitř výčtu položek
Write-Host @(1, 2, 3 * 5)    # → 1 2 3 1 2 3 1 2 3 1 2 3 1 2 3
Write-Host @(1, 2, (3 * 5))  # → 1 2 15

# Místo čárek lze použít nové řádky
$pole = @(
    1
    2
    "Three"
    4.01
)

# Další kuriozita je podmínka uvnitř výčtu
$podminka = $false
$pole = @(1, 2, $(if ($podminka) { 3, 4 } else { 5, 6 }))
Write-Host $pole  # → 1 2 5 6

# Kromě výčtu položek lze vytvořit i prázdné pole s pevně danou délkou
$pole = [Object[]]::new(4)  # Čtyřtečkou přistupujeme ke statickým členům

# Pevně nastavený datový typ už umíme
[int[]]$pole = @(0, 1, 2, 3, 4, 5, 6, 7)

# Výčet rozsahem
[int[]]$pole = @(0..7)
```

#### [Pole] Indexace

```ps1
[int[]]$pole = @(0, 1, 2, 3, 4, 5, 6, 7)
Write-Host $pole[1]  # → 1

# Lze indexovat více položek najednou, nebo rozsah (inkluzivní)
Write-Host $pole[1, 2, 3]  # → 1 2 3
Write-Host $pole[1, 0, 1]  # → 1 0 1
Write-Host $pole[1..4]     # → 1 2 3 4
Write-Host $pole[4..1]     # → 4 3 2 1

# Lze indexovat od konce
Write-Host $pole[-1]  # → 7
Write-Host $pole[-2]  # → 6

<#
POZOR!
Indexování rozsahem není to samé, jako plnohodnotný slicing.
Od [1..-2] bychom mohli čekat oříznutí o jeden prvek z obou stran,
ve skutečnosti je ale vygenerována sekvence [1,0,-1,-2].
#>
Write-Host $pole[1..-2]  # → 1 0 7 6

# Co s tím?
Write-Host $pole[1..($pole.Count - 2)]                                  # → 1 2 3 4 5 6
Write-Host $pole[1..($pole.GetUpperBound(0) - 1)]                       # → 1 2 3 4 5 6
Write-Host ($pole | Select-Object -Skip 1 | Select-Object -SkipLast 1)  # → 1 2 3 4 5 6

# Out of bounds v PowerShellu vrací $null, žádné errory
Write-Host ($null -eq $pole[10])  # → True

# To samé ale neplatí o nastavování hodnot
$pole[2] = 20    # OK
$pole[10] = 100  # IndexOutOfRangeException

# Indexovat lze případně i jiným polem
$indexes = @(4, 5, 6)
Write-Host $pole[$indexes]  # → 4 5 6
```

#### [Pole] Iterace

Iterovat pole stylem _foreach_ lze třemi způsoby. `$_` je vestavěná proměnná obsahující aktuální prvek.

```ps1
foreach ($item in $pole) {
    Write-Host (2 * $item)
}

$pole | ForEach-Object {
    Write-Host (2 * $_)
}

$pole.ForEach({
    Write-Host (2 * $_)
})
```

Co když chceme i index aktuálního prvku?

```ps1
# ForEach-Object může mít až tři části: `ForEach-Object -Begin {} -Process {} -End {}`
# Zde nám vyhovuje implicitní určení bloků, kde v Begin nastavíme index a v Process inkrementujeme
$pole | ForEach-Object { $index = 0 } {
    Write-Host "${index}: ${_}"
    $index++
}

# Vygenerování sekvence pomocí rozsahu a indexování pole
0..($pole.Count - 1) | ForEach-Object {
    Write-Host "${_}: $($pole[$_])"
}

# Nebo lze použít klasický for cyklus
for ($index = 0; $index -lt $pole.Count; $index++) {
    Write-Host "${index}: $($pole[$index])"
}
```

Přiřazením cyklu foreach do proměnné můžeme vytvořit nové pole.

```ps1
# Vytvoření pole pomocí foreach
$pole = 0..9 | ForEach-Object {
    return 2 * $_
}
Write-Host $pole  # → 0 2 4 6 8 10 12 14 16 18
```

Switch umožňuje reagovat na jednotlivé hodnoty v poli.

```ps1
switch ($pole) {
    { $_ % 2 -eq 0 } {
        Write-Host "Sude"        
    }
    Default {
        Write-Host "Liche"
    }
}
```

Ve větvích switche můžeme používat `continue` a `break`, které fungují analogicky k ostatním cyklům. Také lze využívat metody automaticky spravované proměnné `$switch`, např. pro vrácení následující hodnoty v kolekci.

#### [Pole] Sčítání, násobení

Pole lze spojovat operací součtu. K poli lze přičíst jednu položku nebo druhé pole. Přičtením se zvětší jeho délka – do proměnné je přiřazeno nové pole, které je výsledkem součtu. Pole lze také násobit kladným číslem, díky tomu lze snadno vytvořit pole s výchozí hodnotou.

```ps1
[int[]]$pole1 = @(0, 1)
[int[]]$pole2 = @(2, 3)

# Sčítání polí
[int[]]$pole3 = $pole1 + $pole2
Write-Host "${pole3} `t[$($pole3.Count)]"  # → 0 1 2 3         [4]
$pole3 += 4
Write-Host "${pole3} `t[$($pole3.Count)]"  # → 0 1 2 3 4       [5]
$pole3 += @(5, 6)
Write-Host "${pole3} `t[$($pole3.Count)]"  # → 0 1 2 3 4 5 6   [7]

# Vynásobení pole číslem
$pole3 *= 2
Write-Host "${pole3} `t[$($pole3.Count)]"  # → 0 1 2 3 4 5 6 0 1 2 3 4 5 6     [14]
Write-Host (@(1) * 14)                     # → 1 1 1 1 1 1 1 1 1 1 1 1 1 1
```

#### [Pole] Seřazení

```ps1
[int[]]$pole_unsorted = @(0, 3, 5, 2, 6, 7, 4, 1)
[int[]]$pole_sorted = ($pole_unsorted | Sort-Object)
Write-Host $pole_sorted  # → 0 1 2 3 4 5 6 7
```

#### [Pole] Odstranění prvku

Z polí nelze přímo odstraňovat prvky – mají pevnou délku – a neexistuje ani operace odčítání polí. Můžeme však hodnoty pole filtrovat a přidávat do pole nového, nebo sčítat vybrané části pole.

```ps1
# Mazání podle hodnoty
[int[]]$pole_unfiltered = @(0, 1, 2, 3, 0, 1, 2, 3)
$pole_filtered1 = ($pole_unfiltered | Where-Object { $_ -ne 0 })
Write-Host $pole_filtered1  # → 1 2 3 1 2 3
# „Smaže“ všechny nuly; přidáním pomocné proměnné a podmínky lze mazat jen první výskyt
# Takto jednoduchý filtr lze zapsat kompaktněji (probráno v následující sekci), toto je ale obecnější

# Mazání podle indexu DIY
$index_to_remove = 2
$pole_filtered2 =
    [int[]]($pole_unfiltered | Select-Object -SkipLast ($pole_unfiltered.Count - $index_to_remove)) +
    ($pole_unfiltered | Select-Object -Skip ($index_to_remove + 1))
Write-Host $pole_filtered2  # → 0 1 3 0 1 2 3
```

### Pole a relační operátory

```ps1
<#
Použití operátorů -eq, -ne, -gt, ... na kolekci vrací všechny její prvky, které jsou vyhodnoceny jako true.
#>
[int[]]$pole_unfiltered = @(0, 1, 2, 3, 0, 1, 2, 3)
$pole_filtered = $pole_unfiltered -ne 0
Write-Host $pole_filtered  # → 1 2 3 1 2 3

<#
-contains, -notcontains, -ccontains, -cnotcontains, -in, -notin, -cin, -cnotin
#>
Write-Host (@("a", "b", "c") -contains "B")  # → True
Write-Host ("B" -cnotin @("a", "b", "c"))    # → True
```

### Vícerozměrná pole

Pole lze samozřejmě vnořovat – „zubatá“ pole tak lze vytvořit snadno.

```ps1
# Zavináče u vnořených polí nejsou povinné
$hyperpole = @(0, 1, (21, 22, 23, 24), 3, (41, 42, 43, 44))

Write-Host $hyperpole[1]        # → 1
Write-Host $hyperpole[2]        # → 21 22 23 24
Write-Host $hyperpole[2][1]     # → 22
Write-Host $hyperpole[2][1][1]  # (→ $null)

# Pole je klasicky typu System.Object[], jak to ale vypadá s typy uvnitř?
Write-Host "$($hyperpole[0].GetType()), $($hyperpole[2].GetType()), $($hyperpole[2][1].GetType())"
<#
→ int, System.Object[], int
Pokud bychom chtěli vnořená pole explicitně otypovat, je lepší před každé napsat [int[]],
než se pokoušet o [int[][]] apod. před celým polem.
#>

# Vnořená pole lze také generovat pomocí foreach
1..5 | ForEach-Object { return , @($_, ($_ * 2)) }
# Vygeneruje [ [ 1 2 ] [ 2 4 ] [ 3 6 ] [ 4 8 ] [ 5 10 ] ]

# Čárka mezi `return` a polem je nutná, jinak bude PowerShell pole „rozbalovat“
1..5 | ForEach-Object { return @($_, ($_ * 2)) }
# Vygeneruje [ 1 2 2 4 3 6 4 8 5 10 ]
```

Pro „tradičnější“ vícerozměrná pole s pevnými rozměry lze použít příkaz `New-Object`, který vrací instanci vyžádaného .NET objektu.

```ps1
$matrix = New-Object "int[,]" 6, 4

$height = $matrix.GetLength(0)  # 6
$width = $matrix.GetLength(1)   # 4

0..($height - 1) | ForEach-Object {
    $y = $_                                     # 0000
    0..($width - 1) | ForEach-Object {          # 0000
        $x = $_                                 # 0000
        Write-Host -NoNewline $matrix[$y, $x]   # 0000
    }                                           # 0000
    Write-Host                                  # 0000
}
```

### List

Z kolekcí frameworku .NET si můžeme vypůjčit List – homogenní a gumový.

```ps1
$list = [System.Collections.Generic.List[string]]::new()
$list = [System.Collections.Generic.List[int]]@(0, 1, 2, 3)
$list.Add(4)
[void]$list.Remove(2)  # Bez [void] vypisuje True do konzole
Write-Host $list       # → 0 1 3 4
```

### Asociativní pole

#### Hashtable

Úlohu asociativního pole v PowerShellu nativně zastupuje heterogenní key-value úložiště zvané _hashtable_.

```ps1
<# Metody pro vytvoření hashtable a nastavení jeho hodnot #>
$hshtbl = @{}                 # Prázdná hashtable
$hshtbl[0] += 5               # Přičítání k neexistujícímu klíči nevadí
$hshtbl["abc"] = "def"        # String klíč; to, že už jsme použili int klíč, nevadí
$conv = @{"1" = 1; "2" = 2 }  # Hashtable s výčtem párů; je-li každý pár zapsán na novém řádku, pak lze středníky vynechat
$conv.Add("3", 3)             # Lze použít i metodu Add
$conv."4" = 4                 # Nebo metodu přístupu ke členům

<# Metody pro přístup k prvkům hashtable #>
Write-Host $hshtbl[0]         # → 5
Write-Host $hshtbl["abc"]     # → def
Write-Host $hshtbl[99]        # (→ $null)
Write-Host $hshtbl.0          # → 5
Write-Host $hshtbl."abc"      # → def

# Pro zjištění všech dostupných metod a vlasností použijeme `$hshtbl | Get-Member`
```

Hashtable je příklad objektu, kdy se výpis pomocí `Write-Host` příliš nevyplatí, pro formátovaný výpis proto pouze uvedeme název proměnné.

```ps1
Write-Host $hshtbl
# → System.Collections.DictionaryEntry System.Collections.DictionaryEntry

$hshtbl
<# →
Name                           Value
----                           -----
abc                            def
0                              5
#>

# Pro nested hashtables se hodí
$hshtbl | ConvertTo-Json -Depth 1
```

Pomocí `ForEach-Object` iterujeme hashtable. Např. přes klíče s využitím `Keys` nebo přes dvojice s využitím `GetEnumerator()`.

```ps1
$hshtbl.Keys | ForEach-Object {
    $key = $_
    $value = $hshtbl[$_]
}

$hshtbl.GetEnumerator() | ForEach-Object {
    $key = $_.Key
    $value = $_.Value
}
```

Stejně jako u polí funguje u hashtables sčítání. Pro odebrání prvku slouží metoda `Remove`.

```ps1
$hshtbl += @{"ghi"="jkl"}
$hshtbl.Remove("ghi")
```

Hashtables nemají žádné interní pořadí. Pokud chceme pracovat s jejich seřazenými hodnotami, seřadíme jejich enumerátor.

```ps1
$p.GetEnumerator() | Sort-Object -Property Key
$p.GetEnumerator() | Sort-Object -Property Value -Descending
```

#### Dictionary

Chceme-li v našem asociativním poli pevné datové typy, vypůjčíme si z frameworku .NET Dictionary.

```ps1
$dict = [System.Collections.Generic.Dictionary[string, int]]::New()
$dict["0"] = 0
$dict["1"]++
Write-Host $dict  # → [0, 0] [1, 1]
```

### Další kolekce

* HashSet – `[System.Collections.Generic.HashSet[𝑇]]`
* SortedSet – `[System.Collections.Generic.SortedSet[𝑇]]`
* Queue – `[System.Collections.Generic.Queue[𝑇]]`
* Stack – `[System.Collections.Generic.Stack[𝑇]]`
* LinkedList – `[System.Collections.Generic.LinkedList[𝑇]]`

## OOP

### PSCustomObject

_PSCustomObject_ nám umožňuje vytvářet objekty na míru. Na první pohled může vypadat podobně jako _hashtable_, je ale mocnější. Vhodnější je např. v situaci, kdy chceme mít více objektů stejného typu v kolekci. Namísto dvojic klíč-hodnota má vlastnosti, které nabývají určitých hodnot, a metody, které s nimi mohou pracovat. (Pro plnohodnotnější OOP v PowerShellu je pak k dispozici ještě _Class_.)

```ps1
# Deklarace je podobná hashtable
$album = [PSCustomObject]@{
    Name = "Katastrofalni selhani"
    Artist = "FilthwAre"
    NTracks = 7
}

# Přístup pomocí tečky
$album.NTracks = 8

# Kompaktní výpis, pokud použijeme Write-Host; nebo tabulkový výpis, pokud napíšeme pouze název proměnné
Write-Host $album  # → @{Name=Katastrofalni selhani; Artist=FilthwAre; NTracks=8}

# Pro přidání vlastnosti je třeba použít Add-Member
$album.Year = 2025  # Error
$album | Add-Member -MemberType NoteProperty -Name "Year" -Value 2025

# Odebrání vlastnosti
$album.psobject.Properties.Remove("Year")

# Získání seznamu vlastností
$album | Get-Member -MemberType NoteProperty | Select-Object -ExpandProperty Name  # Seřazeno abecedně
$album.psobject.Properties.Name  # Seřazení odpovídá pořadí při definici

# K vlastnostem lze přistupovat řetězcem, jehož hodnota je názvem vlastnosti
Write-Host $album."NTracks"  # → 8

# Díky tomu lze iterovat dvojice vlastnost-hodnota
$album.psobject.Properties.Name | ForEach-Object {
    Write-Host "${_} = $($album.$_)"
}

# Objektu můžeme přidat vlastní metodu; k vlastnostem přistupujeme pomocí $this
$album | Add-Member ScriptMethod -Name CustomToString -Value {
    param($delimiter)
    return "$($this.Artist) ${delimiter} $($this.Name)"
}
$album.CustomToString("::") # → FilthwAre :: Katastrofalni selhani

# PSCustomObject je referenční datový typ, pro vytvoření kopie tedy nepostačí přiřazení do další proměnné
# K dispozici je metoda pro vytvoření shallow copy
$shallowCopy = $album.psobject.Copy()
```

* `Add-Member` přidá k objektu vlastní vlastnosti a metody
* `Compare-Object` porovná objekty
* `Get-Member` získá vlastnosti a metody z objektu
* `Get-Unique` vrátí unikátní prvky ze __seřazené__ kolekce
* `Group-Object` seskupí objekty podle společných hodnot určitých vlastností
* `Measure-Object` provede základní statistickou analýzu nad objekty (zkoumá číselné hodnoty a řetězce)
* `Select-Object` vybere konkrétní vlastnosti objektů
* `Select-String` hledá text, obdoba `grep`
* `Sort-Object` seřadí objekty podle určité vlastnosti
* `Where-Object` vybere objekty podle hodnot jejich určitých vlastností

### Class

[MSLearn/about_Classes](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_classes?view=powershell-5.1)

### Enum

[MSLearn/about_Enum](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_enum?view=powershell-5.1)
