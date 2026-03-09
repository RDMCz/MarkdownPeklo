# Mint poznámky

## Mint 22 Cinnamon x64

### Tipy a upozornění

#### NTFS

* Linux dokáže pracovat s NTFS oddíly, a proto se především při přechodu z Windows může jevit jako validní možnost ponechat si na nesystémových discích NTFS
* NTFS ale nedoporučuji, přichází s ním problémy, které vychází především z odlišného systému oprávnění, jako:
  * Ve správci souborů se po pokusu otevření libovolného souboru zjeví dialog, zdali chceme soubor otevřít, nebo spustit, což je otravný krok navíc
  * Hry nainstalované přes Steam na NTFS disk nepůjdou bez dodatečné konfigurace spustit

### Jak...?

#### Klávesová zkratka pro správce úloh

* Klávesové zkratky jsou přenastavitelné, nastavení některých akcí je ponecháno na uživatelích
* Správce úloh (zde zvaný _System monitor_) je aplikace jako každá jiná, chceme tedy přidat zkratku, která ji spustí
* Jdeme do _Menu_ → _Preferences_ → _Keyboard_ → _Shortcuts_
* Dole klikneme na tlačítko _Add custom shortcut_
  * _Name_ nastavíme např. na `System Monitor`
  * _Command_ musíme nastavit na na `gnome-system-monitor`
* V záložce _Custom shortcuts_ pak najdeme naši novou zkratku a v sekci _Keyboard bindings_ ji můžeme přiřadit klávesy, např. <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>Esc</kbd>

#### Výstřižky

<kbd>Shift</kbd> + <kbd>PrintScreen</kbd>

#### Logitech bezdrátová zařízení

* Logitech bezdrátová zařízení by měla fungovat sama od sebe, nelze ale zobrazit stav baterie nebo přenastavit např. funkce tlačítek na myši
* Zobrazit stav baterie a změnit některá nastavení zařízení dokáže program [Solaar](https://github.com/pwr-Solaar/Solaar)

### Přidání funkcionality

#### Clipboard history (historie schránky)

* Balík _Parcellite_ nainstalovat v software managerovi
  * (Existují i jiné balíky, často zmiňovaný je _Diodon_, ten mi ale moc nefungoval; pak _Clipit_, _GPaste_, ...)
* V trayi bude ikonka → right click → _Preferences_
* V sekci _Hotkeys_ lze nastavit zkratky
  * Bohužel `<Hyper>V` na Cinnamonu blbne, je potřeba double tapnout Hyper a pak až stisknout V
  * Zatím jsem se spokojil s `<Ctrl><Alt>V` (pro _History key combination_)
* Funguje to trochu jinak než na Windows: po zobrazení historie se vybere položka, která se umístí do schránky, a tu pak lze vkládat klasicky pomocí Ctrl+V
* V sekci _Display_ lze změnit vzhled ikonky v trayi, je ale potřeba dodat vlastní ikonu a zde napsat její název, viz [parcellite monochrome icon](./parcellite_monochrome_icon/)

### Obecné problémy

#### Interní disk je odpojen, dokud se na něj neklikne v průzkumníku souborů

* Problém: Spustíme-li po startu systému program, který chce načíst něco z nesystémového disku, pak hlásí, že taková cesta neexistuje. Jakmile v průzkumníku disk otevřeme, tak cesta už začne být viditelná pro daný program.
* [Řešení](https://forums.linuxmint.com/viewtopic.php?t=407003): Toto je výchozí chování, lze jej ale změnit např. pomocí programu _Disks_, který je součástí systému:
  * Otevřeme _Disks_, v něm vybereme problémový disk a jeho partition
  * Klikneme na ozubená kola (_Additional partition options_) a vybereme _Edit Mount Options_
  * Vypneme možnost _User Session Defaults_
  * To by mělo stačit (možnosti _Mount at system startup_ & _Show in user interface_ musí zůstat zapnuté)
  * Dále je vhodné změnit mount point:
    * Dle definice nejsou `/mnt` ani `/media` vhodná místa pro permanentní interní disk
    * Doporučuji kořen systému souborů nebo domovský adresář (např. `/ssd2` nebo `/home/username/ssd2`)

#### "Start" menu obsahuje odkazy na již neexistující soubory

* Problém: Používáme-li nějaký soubor, a později jej smažeme, odkaz na něj se může stále nacházet v menu. Po kliknutí píše, že soubor neexistuje, ale odkaz nelze nijak smazat.
* [Řešení](https://forums.linuxmint.com/viewtopic.php?t=401945): Asi se jedná o chybu, odkazy na nedávno použité soubory lze vypnout v _Privacy_ → _Remember recently accessed files_.

#### MiKTeX nerozděluje (česká) slova

* (TeXStudio lze sehnat přímo z repozitáře; MiKTeX podle návodu na jejich webu; dodatečné balíčky (tul) umístíme do `/home/<username>/.miktex/texmfs/install/tex/latex`)
* Problém: Dlouhá slova na konci řádku nejsou rozdělena a přetékají přes okraj
* Zkoušel jsem toho více a bohužel 100% nevím, jestli právě toto zabralo:

```bash
kpsewhich hyph-cs.tex # poté na mě vyskočil MiKTeX dialog pro instalaci balíčku → potvrdit
miktex fndb refresh
```

<!--
miktex languages configure
initexmf --mklangs
miktex packages install hyph-utf8
-->

#### MiKTeX nemůže najít OTF font

* Problém: I přes to, že ostatní programy OTF font vidí, LaTeXu se nedaří jej najít
  * Chybová hláška v může vypadat dost krypticky, podrobnější informace se mohou náchazet v logu přímo vedle daného .tex souboru (a ne tam, kam nás navádí chybová hláška)
    * Konkrétně u mě chybová hláška vypadala takto: ``hbf2gf (CJK ver. 4.8.4) `OT' can't be a subfont created by hbf2gf``
    * V log souboru už lépe: `! Package fontspec Error: (fontspec) The font "xyz" cannot be found; ...`
  * Nevím jestli je to problém pouze u OTF fontů, nebo obecně s „nevhodně nainstalovanými fonty“
* Řešení: Vložit font do speciální složky, kde ho MiKTeX určitě najde:

```bash
kpsewhich --show-path=.otf 
# ↑ Tento příkaz nám vrátí seznam cest, kde se hledají OTF fonty

# Jednu z těchto cest si vybereme, a nakopírujeme do ní daný font

# Pak stačí obnovit "File Name Database":
miktex fndb refresh
```

#### Nevyřešeno (obecné problémy)

* Floorp: drag&drop do GDrive nefunguje (Firefox OK)

### Problémy na Dell Inspiron 15 (7000) Gaming

#### Nefunkční zvuk

* Problém: Někdy nefunguje zvuk a ve výstupních audio zařízeních se objevuje pouze _Dummy output_
* [Řešení](https://forums.linuxmint.com/viewtopic.php?t=437758): Je potřeba zablokovat kernel modul zvaný _snd\_soc\_avs_

```bash
echo "blacklist snd_soc_avs" | sudo tee /etc/modprobe.d/blacklist-snd_soc_avs.conf
sudo update-initramfs -u
# Poté restartovat PC
```

#### Hry

##### Ovladače?

* Ovladač pro NVIDIA GPU nainstalujeme v programu _Driver Manager_
* V liště se nám po restartu objeví _NVIDIA Settings_, kde lze přepínat mezi profily: _Intel (Power Saving Mode)_, _NVIDIA On-Demand_ a _NVIDIA (Performance Mode)_
* Po přepnutí profilu je třeba restart systému
* Pro běžné používání stačí On-Demand, pro hraní je zřejmě nutný Performance Mode
  * Při testování na Flatout 2 hra v profilu On-Demand padala, v Performance Mode nikoliv

##### Steam nejde zapnout v profilu _NVIDIA (Performance Mode)_

* Problém: Při zapnutém profilu _NVIDIA (Performance Mode)_ a pokusu a zapnutí Steamu z menu nebo plochy nás systém odhlásí
* Řešení: Steam by měl jít spustit z terminálu příkazem `steam`
* Příkaz `steam` se hodí i pro řešení dalších problémů, protože do terminálu vypisuje informace za běhu

#### Nevyřešeno (Dell laptop)

* Firefox/Floorp: Gesta zpět/vpřed na touchpadu nefungují (na Waylandu údajně funguje)
* Touchpad méně tolerantní (klik třemi prsty: někdy detekuje pouze dva, někdy vůbec nic)
