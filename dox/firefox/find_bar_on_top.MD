# Firefox – _Najít na stránce..._ ve vrchní části prohlížeče

Do [zprovozněného souboru userChrome.css](./userchrome.MD) připište:

```css
.browserContainer {
  display: flex !important;
  flex-direction: column !important;
}

.browserContainer > .browserStack {
  order: 2 !important;
}

.browserContainer > findbar {
  order: 1 !important;  
}
```

Soubor uložte a restartujte prohlížeč.

Po stisku <kbd>Ctrl</kbd> + <kbd>F</kbd> by se měl nyní „findbar“ nacházet ve vrchní části prohlížeče.

---

<details>

<summary>Zobrazit/skrýt userChrome.css kód pro starší verze prohlížeče</summary>

## Firefox verze 113 až 155

```css
.browserContainer > findbar {
  order: -1 !important;
}
```

## Firefox verze starší než 113

```css
.browserContainer > findbar {
  -moz-box-ordinal-group: 0;
}
```

</details>

---

<https://www.reddit.com/r/FirefoxCSS/>
