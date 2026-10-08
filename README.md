# ZABALENO – Google Sheets database + Render

Tato verze používá Google Sheets jako databázi. **Uživatel aplikace se do Google účtu nepřihlašuje.** Přístup ke Google Sheets probíhá pouze ze serveru na Renderu přes Google service account.

## Důležitá bezpečnostní zásada

Nedávej URL tabulky ani service-account JSON do frontendového JavaScriptu a nikdy je necommituj do GitHubu.

Samotné `SHEET_ID` není tajný údaj, ale v této verzi je také uložený pouze jako Render Environment Variable, aby odkaz na tabulku nebyl zbytečně v repozitáři.

### Doporučené nastavení Google Sheet

1. Otevři Google Cloud Console.
2. Vytvoř projekt a zapni **Google Sheets API**.
3. Vytvoř **Service Account**.
4. Vytvoř jeho JSON klíč.
5. Z JSON vezmi `client_email`.
6. V Google Sheet klikni **Sdílet** a přidej tento `client_email` jako **Editor**.
7. Doporučuji ponechat **Obecný přístup = Omezený**. Aplikace má k tabulce přístup přes service account, takže tabulka nemusí být veřejná.

> Pokud skutečně nastavíš „Kdokoli s odkazem – Editor“, kdokoli, kdo URL tabulky získá, může tabulku měnit. To nedoporučuji pro produkční databázi.

## Render

V Renderu nastav:

- `SHEET_ID` = `1nwy8le2nTIHJ3_vRIbAlsfesgzt-mYzyqp_u7Xi962A`
- `GOOGLE_SERVICE_ACCOUNT_JSON` = celý obsah service-account JSON jako jednu environment variable
- `JWT_SECRET` = dlouhý náhodný řetězec
- `ADMIN_EMAIL`, `ADMIN_PASSWORD`
- `PARTNER_EMAIL`, `PARTNER_PASSWORD`
- `NODE_ENV=production`

`render.yaml` tyto proměnné připravuje jako secret/environment variables.

## GitHub

Do repozitáře patří:

- `server.js`
- `package.json`
- `render.yaml`
- `.env.example`
- `.gitignore`
- složka `public/` s frontendem

**Nikdy** necommituj:

- `.env`
- service-account JSON
- hesla
- privátní klíče

## Datová struktura

Server při prvním spuštění vytvoří listy:

`products`, `orders`, `sales`, `tasks`, `notifs`, `settings`

Sloupec A obsahuje ID, sloupec B JSON zdroj pravdy a další sloupce jsou čitelný přehled.

Server načítá data do paměti a změny zapisuje do Google Sheets. Ruční změny v tabulce se periodicky načítají zpět.

## Co znamená „bez přihlášení“

- **Google přihlášení uživatele:** není potřeba.
- **Přístup serveru ke Google Sheets:** řeší service account automaticky.
- **Přihlášení do samotné aplikace:** zůstává zachované přes vlastní email/heslo a httpOnly JWT cookie. To je oddělené od Google účtu.

Pokud chceš, aby byla bez přihlášení i samotná aplikace, lze odstranit `/api/login` a všechny auth guardy, ale pro produkční provoz to nedoporučuji.

## Spuštění

```bash
npm install
npm start
```

Server poslouchá na `PORT`, případně na `3000`.
