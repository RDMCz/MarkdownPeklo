# Mint poznámky

## Mint 22 Cinnamon x64

### Obecné problémy

#### Interní disk je odpojen, dokud se na něj neklikne v průzkumníku souborů

* Problém: Spustíme-li po startu systému program, který chce načíst něco z nesystémového disku, pak hlásí, že taková cesta neexistuje. Jakmile v průzkumníku disk otevřeme, tak cesta už začne být viditelná pro daný program.
* [Řešení](https://forums.linuxmint.com/viewtopic.php?t=407003): Toto je výchozí chování, lze jej ale změnit např. pomocí programu _Disks_, který je součástí systému:
  * Otevřeme _Disk_, v něm vybereme problémový disk a jeho partition
  * Klikneme na ozubená kola (_Additional partition options_) a vybereme _Edit Mount Options_
  * Vypneme možnost _User Session Defaults_
  * To by mělo stačit (_Mount at system startup_ a _Show in user interface_ musí zůstat zapnuté)
  * Dále je asi vhodné změnit mount point:
    * Dle definice nejsou `/mnt` ani `/media` vhodné na permanentní interní disk
    * Doporučuji kořen systému souborů nebo domovský adresář (např. `/ssd2` nebo `/home/username/ssd2`)

#### MiKTeX nerozděluje (česká) slova

* (TeXStudio lze sehnat přímo z repozitáře; MiKTeX podle návodu na jejich webu; dodatečné balíčky (tul) umístíme do `TODO`)
* Problém: Dlouhá slova na konci řádku nejsou rozdělena a přetékají přes okraj
* Zkoušel jsem toho více a bohužel 100% nevím, jestli právě toto zabralo:

```bash
kpsewhich hyph-cs.tex # po té na mě vyskočil MiKTeX dialog pro instalaci balíčku → potvrdit
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
# then reboot
```

#### Nevyřešeno (Dell laptop)

* Firefox/Floorp: Gesta zpět/vpřed na touchpadu nefungují (na Waylandu údajně funguje)
* Touchpad méně tolerantní (klik třemi prsty: někdy detekuje pouze dva, někdy vůbec nic)
