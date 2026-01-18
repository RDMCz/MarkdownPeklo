# Floorp 12 jakožto náhrada Firefoxu

## Čistá instalace

* <https://floorp.app/en-US/download>
  * Nepodporučuji portable verzi (problémové nastavení jako výchozí prohlížeč webu, pdf, ...)
  * Doporučuji ponechat výchozí nastavení v instalátoru (veškerá cache apod. pak stejně půjde na céčko, tak tam raději i nainstalovat samotný program)

* Úvodního wizarda klidně zavřít, vše lze změnit později v nastavení

* Lze se přihlásit do _Mozilla account_ pro synchronizaci doplňků, záložek atd. (_Nastavení_ → _Synchronizace_ → _Přihlásit se k synchronizaci..._)

* Right-click na horní lištu, _Nastavení tlačítek a lišt..._
  
  * Stejně jako ve Firefoxu můžeme z lišty odebrat (jen některé) rušivé prvky
  
  * Oproti Firefoxu je v základu k dispozici _Kompaktní_ velikost lišty panelů (jedná se o „nejnižší“ velikost, což preferuji – více místa pro samotný obsah webu)

## Nastavení

* _Obecné_

  * _Zobrazit obrázek s náhledem při přejetí nad panelem_

  * _Vzhled webových stránek_ – podle toho se určuje, zdali budou weby preferovat dark/light mode; změní se tím (ale) i částečně téma samotného prohlížeče

  * _Stahování_ – implicitní složka pro stahování

  * _Zobrazovat přepínač do režimu obrazu v obraze_ – věc u videí, kterou jsem asi nikdy seriózně nepoužil

* _Vyhledávání_

  * _Vyhledávače_ – zde lze např. přiřadit Googlu písmeno `g`, nebo smazat Bing

* _Soukromí a zabezpečení_

  * _Navrhovat e-mailové masky Floorp Relay pro ochranu vaší e-mailové adresy_

### Floorp Hub

* _Tab & Appearance_

  * _Photon_ je „nejnižší“

* _Panel Sidebar_

  * Zde lze vypnout postranní panel

* _Workspaces_

  * Zde lze vypnout Workspaces

* _Keyboard Shortcuts_

  * Zde lze vypnout/nastavit klávesové zkratky

* _Web Apps_

  * Zde lze vypnout Web Apps

## about:config

* `gfx.font_rendering.cleartype_params.rendering_mode` → `5` – vyhlazenější font, Windows only

* `browser.translations.enable` → `false` – vypne podprůměrný Firefox překlad, pro překlad lze použít [TWP - Translate Web Pages](https://addons.mozilla.org/en-US/firefox/addon/traduzir-paginas-web/) ([nastavení](./floorp11.MD#nastavení-twp---translate-web-pages))

* `browser.taskbarTabs.enabled` → `false` – skryje tlačítko _Přidat panel na panel úloh_

* `network.protocol-handler.external.mailto` → `false` – vypne neúprosný popup _Přidat xyz.cz jako aplikaci pro odkazy typu mailto?_

## userChrome.css

* Před verzí 12.7:
  * ~~K souboru se dostaneme přes: `about:support` → _Složka s profilem_ → _Otevřít_ → `chrome` → `userChrome.css` a vlastní pravidla píšeme pod řádek `/* Please write your custom CSS under this line*/`~~
* Po verzi 12.7:
  * K souboru se dostaneme přes: `about:support` → _Složka s profilem_ → _Otevřít_ → `chrome` → `CSS` → `userChrome.css`
* Linux:
  * _Složka s profilem_ mi ukazuje neexistujícíc cestu, naštěstí lze soubor ve file manageru rychle vyhledat

```css
/* Panel 'Najít na stránce...' ve vrchní části prohlížeče */
.browserContainer > findbar { order: -1 !important; }

/* Skrytí tlačítka 'Sdílet tuto stránku s vaším telefonem' */
#QRCodeGeneratePageAction{ display: none !important; }

/* Skrytí tlačítka 'Seznam všech panelů' */
#alltabs-button { display: none !important; }
.titlebar-spacer[type="post-tabs"] { display: none !important; } 
```

## Moje Firefox Color theme

<https://color.firefox.com/?theme=XQAAAALkAQAAAAAAAABBqYhm849SCia-yK6EGccwS-xMDPr9KfClUsfTUp4mZMBLvzmxZf3j18vxZeIplEK58_CjoMc71C-lhk24F2NJpRB2pBgKPt67oKjV0_7jMKSWHUGWjWR2mBdoBQaUSzyX5bgAn7JZuaKQXG4XTBO3aucDJ1x-GaqRrkhubYrNYyClEZvW7PxWsH0pebujvVtBT49qKeXecsO417lZ38bJ4hrqLswCK4j4R6JhBy2negpG7-GJS_mdxH46ZM30mEFfOv7LPWR1nxT92CuOvTVG_-sO1I0MabDmAhci_-Z-Y0uKfBMJiSiiaZGsH1uwTZmuVA5HjdPlp3TJC-ih6N2E4MJB5KhVqcEGh6cfca3zXt3NssmW8zHYCss3yHLM33_-GRIm>
