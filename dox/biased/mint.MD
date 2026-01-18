# Mint poznámky

## Mint 22 Cinnamon x64

### Obecné problémy

#### MiKTeX nerozděluje (česká) slova

* (TeXStudio lze sehnat přímo z repozitáře; MiKTeX podle návodu na jejich webu; dodatečné balíčky (tul) umístíme do `TODO`)
* Problém: Dlouhá slova na konci řádku nejsou rozdělena a přetékají přes okraj
* Zkoušel jsem toho více a bohužel 100% nevím, jestli právě toto zabralo:

```bash
kpsewhich hyph-cs.tex # po té na mě vyskočilo MiKTeX dialog na instalaci balíčku, potvrdit
miktex fndb refresh
```

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
