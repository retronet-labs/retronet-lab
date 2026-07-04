# retronet-lab

Docker Lab dell'ecosistema [RetroNet](https://github.com/retronet-labs/retronet):
avvia `retronet-api` e `retronet-ui` con un solo comando, senza dover clonare o
compilare a mano i singoli repository Go.

Questo repo non contiene codice applicativo: orchestra via `docker compose` le
immagini costruite direttamente dai sorgenti Git di ciascun modulo
(`retronet-api`, `retronet-ui`, `retronet-4004`).

## Requisiti

- Docker Engine con supporto BuildKit (Docker 23+) e plugin `docker compose`.
- Connessione a Internet al primo avvio (i servizi vengono compilati a partire
  dai repository GitHub del rispettivo modulo).

## Quick start

```bash
cp .env.example .env
docker compose up --build
```

Poi apri il browser su:

```text
http://localhost:18081
```

La UI parla con l'API su `http://localhost:8080` (CORS pre-configurato).
Le porte sono personalizzabili in `.env` (`API_PORT`, `UI_PORT`).

Per fermare tutto:

```bash
docker compose down
```

## Demo CPU 4004 (standalone)

`retronet-4004` non e' (ancora) collegato a `retronet-ui`/`retronet-api`: gira
come demo a se stante della CLI dell'emulatore Intel 4004. E' attivabile con il
profilo `demo-4004`:

```bash
docker compose --profile demo-4004 run --rm retronet-4004
```

Esegue di default la demo BCD con trace (vedi il
[Dockerfile di retronet-4004](https://github.com/retronet-labs/retronet-4004/blob/main/Dockerfile)
per gli argomenti disponibili).

## Come funziona

Ogni servizio in `docker-compose.yml` usa come build context l'URL Git del
repository corrispondente (feature nativa di BuildKit: `context: https://...git#main`),
quindi non serve clonare nulla a mano — `docker compose up` scarica e compila
la revisione `main` corrente di ciascun modulo:

- `api` -> [`retronet-api`](https://github.com/retronet-labs/retronet-api)
- `ui` -> [`retronet-ui`](https://github.com/retronet-labs/retronet-ui)
- `retronet-4004` -> [`retronet-4004`](https://github.com/retronet-labs/retronet-4004)

Per usare invece un checkout locale (es. per sviluppo cross-repo), sostituisci
`context` con il percorso relativo al repo clonato come sibling e ricostruisci
con `docker compose build`.

## Profili futuri

Come da [roadmap](https://github.com/retronet-labs/retronet/blob/main/ROADMAP.md),
sono previsti profili aggiuntivi per BBS, web storico e laboratorio completo,
via via che i rispettivi moduli (`retronet-bbs`, `retronet-http`,
`retronet-browser`, ...) verranno pubblicati.

## Licenza

MIT.
