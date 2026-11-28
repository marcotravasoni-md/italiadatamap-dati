# ItaliaDataMap · dati ISTAT

Ogni notte un lavoro automatico (GitHub Actions) scarica da ISTAT il catalogo dei dataset,
la struttura dei dataset divisi per territorio e l'ultimo valore dei dataset principali,
e li pubblica su GitHub Pages:

    https://mtrava-metodo-software-ai.github.io/IDM-DATI/

| File | Contenuto |
| --- | --- |
| `catalogo.json` | tutti i dataset ISTAT in forma compatta, con quali sono già pronti |
| `strutture/<id>.json` | dimensioni e valori possibili di un dataset |
| `valori/<id>.json` | ultimo valore di ogni territorio, con i filtri predefiniti (SDMX-CSV di ISTAT) |
| `stato.json` | cosa è stato scaricato e quando |

L'app [ItaliaDataMap](https://mappe.metodo-software.com) li legge per primi e
interroga ISTAT dal vivo solo per ciò che manca.

## Come funziona

- Parte da solo ogni notte alle 02:17 UTC.
- Prima prepara gli indicatori «Pronti da usare» dell'app, poi riscarica **solo i dataset che ISTAT
  ha aggiornato** e usa le richieste rimaste per aggiungerne altri con territori.
- Le richieste passano dall'intermediario di ItaliaDataMap (`mappe.metodo-software.com/api/istat`),
  perché ISTAT non risponde bene alle richieste che arrivano da GitHub.
- Rispetta il limite ISTAT (circa 5 richieste al minuto): 15 secondi tra una richiesta e l'altra,
  al massimo 200 richieste e 5 ore per notte. Se ISTAT chiede di aspettare, aspetta e riprova.
- Se i dati scaricati sono zero o meno di quelli già pubblicati, **non pubblica** e lascia online i precedenti.
- Il ramo `gh-pages` viene riscritto ogni volta con un solo commit, così il repository non cresce.

## Avvio a mano

Actions → **Dati ISTAT** → **Run workflow** (si può scegliere il numero massimo di richieste).

In locale:

```bash
node aggiorna-dati.mjs --out sito --budget 20 --base https://mappe.metodo-software.com/api/istat
```

## Fonte e licenza

Dati: **ISTAT – Istituto Nazionale di Statistica**, [esploradati.istat.it](https://esploradati.istat.it),
licenza [Creative Commons BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.it).
Qui sono solo riformattati, senza modificare i valori.

`aggiorna-dati.mjs` è generato dal repository dell'app (`npm run dati:bundle`): non modificarlo a mano.

Un prodotto di [Metodo](https://metodo-software.com).
