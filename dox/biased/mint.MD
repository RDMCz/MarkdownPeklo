# Mint poznámky

## Mint 22 Cinnamon x64

### Jak...?

#### Výstřižky

<kbd>Shift</kbd> + <kbd>PrintScreen</kbd>

### Přidání funkcionality

#### Clipboard history, historie schránky

* Balík _Parcellite_ nainstalovat v software managerovi
* V trayi bude ikonka → right click → _Preferences_
* V sekci _Hotkeys_ lze nastavit zkratky
  * Bohužel `<Hyper>V` na Cinnamonu blbne, je potřeba double tapnout Hyper a pak až stisknout V
  * Zatím jsem se spokojil s `<Ctrl><Alt>V` (pro _History key combination_)
* Funguje to trochu jinak než na Windows: po zobrazení historie se vybere položka, která se umístí do schránky, a tu pak lze vkládat klasicky pomocí Ctrl+V
* V sekci _Display_ by měl jít změnit vzhled ikonky v trayi (asi dodat cestu k vlastní ikonce?), to jsem zatím nezkoušel
* Existují i jiné balíky, často zmiňovaný je _Diodon_, ten mi ale moc nefungoval; pak _Clipit_, _GPaste_, ...

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

### Problémy na Dell Inspiron 15 (7000) Gaming

#### Nefunkční zvuk

* Problém: Někdy nefunguje zvuk a ve výstupních audio zařízeních se objevuje pouze _Dummy output_
* [Řešení](https://forums.linuxmint.com/viewtopic.php?t=437758): Je potřeba zablokovat kernel modul zvaný _snd\_soc\_avs_

```bash
echo "blacklist snd_soc_avs" | sudo tee /etc/modprobe.d/blacklist-snd_soc_avs.conf
sudo update-initramfs -u
# Poté restartovat PC
```

#### Nevyřešeno (Dell laptop)

* Firefox/Floorp: Gesta zpět/vpřed na touchpadu nefungují (na Waylandu údajně funguje)
* Touchpad méně tolerantní (klik třemi prsty: někdy detekuje pouze dva, někdy vůbec nic)
