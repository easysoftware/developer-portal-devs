# Playwright reference

V této části se podíváme na to, jak úspěšně pracovat s Plawrightem v EasySoftware.
K psani playwright testu pouzivame originalni dokumentaci [https://playwright.dev/](https://playwright.dev/).
Dale je popsana struktura, rychli start, napojeni na GitlabCI a jak ziskat detailni informace o tom proc test spadl.

---

## Structure

Vsechny playwright testy pisem do slozky playwright v jadre.

Zde se nachazi slozky:
1. fixtures: 
  - Tato slozka obashuje JSON soubory reprezentujici entity naseho sytemu. To jak existujici v pouzivanem datasetu tak i nove.
2. pages:
  - K psani playwright testu pouzivame page pattern. Ve zkratce logika ziskavani elementu ve strance je uzavrena v souborech s koncovkou `.page`. Ty se nasledne naimportuji do testu a volaji se metody z nich.
3. tests:
  - Tato slozka obsahuje samotne playwright testy s patricnymi ocekavanimi.

---

## Rychli start

1. Pro testovani pouzivame databazi z [https://cypresssource-minor.easysoftware.com/](https://cypresssource-minor.easysoftware.com/).
**Tuto databazi je zakazano bez schaleni QA upravovat!**
   Databazi si tedy jednoduse dumpnem a dump importujem do sve lokalni databaze.
2. Ve vychozim stavu bezi playwright vuci localhostu `yarn|npx playwright test`.
   Tedy spustime localni server s testovaci databazi a testy spustime pres vyse uvedeny prikaz.
   Pokud chceme playwright spustit vuci deploji(musi obsahovat tesotvaci db) lze pouzit parametr BASE_URL.
3. Pokud nam testy spadnou playwright napovida at pouzijem prikaz `yarn|npx playwright show-trace` kteremu predame vygenerovany soubor zip.
   Lokalne nam playwright vse napovi, ovsem stejny postup lze pouzit i pro testy spadle v GitlabCI. 
   To totiz dane zip soubory uklada lze si je stahnout a spustit, detajlnejsi postup je v sekci napojeni na GitlabCI.

## Napojeni na GitlabCI

Pro spusteni playwright testu v GitlabCi pouzivame docker image.
Pro kazdou vetev se vytvari nova docker image, ktera je ulozena v Gitlabu v registrech [https://git.easy.cz/devel/devel/container_registry](https://git.easy.cz/devel/devel/container_registry).
Nazev image obsahuje nazev vetve pro kterou byla stvorena.

Kazda spadla pajplajna poskytuje moznost stazeni detailniho back-trace zip souboru v sekci 'Job artifacts'. 
Tento zip staci stahnout a lokalne spustit prikazem `yarn|npx playwright show-trace`.





