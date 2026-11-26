# ItaliaDataMap · dati ISTAT

Ogni notte un lavoro automatico (GitHub Actions) scarica da ISTAT il catalogo dei dataset,
la struttura dei dataset divisi per territorio e l'ultimo valore dei dataset principali,
e li pubblica su GitHub Pages:

    https://marcotravasoni-md.github.io/italiadatamap-dati/

| File | Contenuto |
| --- | --- |
| `catalogo.json` | tutti i dataset ISTAT in forma compatta, con quali sono già pronti |
| `strutture/<id>.json` | dimensioni e valori possibili di un dataset |
| `valori/<id>.json` | ultimo valore di ogni territorio, con i filtri predefiniti (SDMX-CSV di ISTAT) |
| `stato.json` | cosa è stato scaricato e quando |

L'app [ItaliaDataMap](https://github.com/marcotravasoni-md/italiadatamap) li legge per primi e
interroga ISTAT dal vivo solo per ciò che manca.

## Come funziona

- Ogni notte riscarica **solo i dataset che ISTAT ha aggiornato** (nota `LAST_UPDATE` del catalogo),
  poi usa le richieste rimaste per aggiungere altri dataset con territori.
- Rispetta il limite ISTAT (5 richieste al minuto per IP): 15 secondi tra una richiesta e l'altra,
  al massimo 200 richieste e 5 ore per notte. Se ISTAT risponde 429 si ferma e pubblica ciò che ha.
- Il ramo `gh-pages` viene riscritto ogni volta con un solo commit, così il repository non cresce.

## Avvio a mano

Actions → **Dati ISTAT** → **Run workflow** (si può scegliere il numero massimo di richieste).

In locale:

```bash
node aggiorna-dati.mjs --out sito --budget 20
```

## Fonte e licenza

Dati: **ISTAT – Istituto Nazionale di Statistica**, [esploradati.istat.it](https://esploradati.istat.it),
licenza [Creative Commons BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.it).
Qui sono solo riformattati, senza modificare i valori.

`aggiorna-dati.mjs` è generato dal repository dell'app (`npm run dati:bundle`): non modificarlo a mano.
