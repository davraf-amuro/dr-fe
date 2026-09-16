# dr-fe

Pacchetto di linee guida per progetti frontend: organizzazione di componenti e cartelle (Vue, WPF/MVVM) e skill di audit del frontend.

## 🧩 Cosa contiene

| File | Tipo | A cosa serve |
|------|------|--------------|
| `.github/instructions/frontend-organization.instructions.md` | Istruzione | Impone un componente per file, cartelle `shared/` o `[dominio]/` in base alla dipendenza dal dominio, alberi di cartelle per Vue (store Pinia per dominio, composable, services) e per WPF/MVVM, props Vue tipizzate e `DependencyProperty` in WPF. La Regola 7 vieta di implementare la login nel frontend: lo schema utenti si sceglie lato API e il frontend si adegua (cookie `HttpOnly` o token bearer in memoria). |
| `.claude/skills/dr-audit-fe/SKILL.md` | Skill Claude Code | `/dr-audit-fe [focus]` rileva lo stack da `package.json` e analizza dead code, conformità ai pattern (React, Vue, Angular) e performance. Produce un report per severità (`ERROR`, `WARNING`, `INFO`) e non modifica nessun file. |

## 🔗 Dipendenze e domini

- Dipende da: nessuna dipendenza.
- Richiesto da: nessuno. Nessun pacchetto del catalogo dichiara `dr-fe` tra le sue dipendenze.
- Applicabilità nel catalogo: `appliesTo: node`.
- Dominio del catalogo: `web-frontend` (Frontend web, kind `node`). È l'unico pacchetto del dominio.
- Tipologie di progetto che lo suggeriscono: `vue-fe` (Frontend Vue 3 + Vite + TypeScript), in `suggestedPackages`.
- Vincoli della tipologia `vue-fe` (note del catalogo):
  - il progetto si crea con `npm create vue@latest` **prima** di installare il core, perché create-vue scrive propri `.editorconfig`, `.gitignore` e `.gitattributes`. La guida del core, partendo da una cartella vuota, installa invece il core per primo: `create-vue` scrive in una sottocartella nuova, ma la combinazione non è ancora stata provata sul campo;
  - lo script `lint` generato da create-vue usa `--fix`: modifica i file, quindi non vale come gate di verifica prima del push.
- Rimandi ad altri pacchetti: `frontend-organization.instructions.md` cita `code-organization.instructions.md` e `sensitive-data.instructions.md` del core, e `minimal-api-architecture.instructions.md` (sezione "Autenticazione") di `dr-minimalapi`. `dr-minimalapi` non è una dipendenza dichiarata.

## 🚀 Come si installa

Di solito non serve farlo a mano. Per una soluzione nuova si segue la guida del core [Creare una soluzione da zero](https://github.com/davraf-amuro/dr-guidelines/blob/main/docs/guida-nuova-soluzione.md): `/dr-scaffold` installa i pacchetti giusti da solo. Il flusso completo non è ancora stato provato sul campo.

A mano. Conviene installare prima il core `dr-guidelines`, che porta `CLAUDE.md`, configurazione e skill; l'installer però non lo impone. Prerequisiti: PowerShell 7, git, `gh auth status` autenticato (i repo sono Private). Esegui dalla **root del repository host**: l'installer usa la cartella corrente come destinazione e non avvisa se sbagli cartella.

L'installer clona `main` da GitHub in una cartella temporanea, copia i file nel progetto host e poi cancella la cartella temporanea.

Via core, un solo installer:

```powershell
Set-Location <root-del-progetto-host>
& ([scriptblock]::Create((gh api repos/davraf-amuro/dr-guidelines/contents/dr-guidelines-install.ps1 -H "Accept: application/vnd.github.raw" | Out-String))) -Package dr-fe
```

Oppure con l'installer del pacchetto:

```powershell
& ([scriptblock]::Create((gh api repos/davraf-amuro/dr-fe/contents/dr-fe-install.ps1 -H "Accept: application/vnd.github.raw" | Out-String)))
```

Nota: la forma breve `irm https://raw.githubusercontent.com/... | iex` funziona solo a repo Public. Oggi risponde 404.

## 📦 Cosa finisce nel progetto host

| Percorso nel progetto host | Contenuto |
|----------------------------|-----------|
| `.github/instructions/frontend-organization.instructions.md` | Copia dell'istruzione sull'organizzazione del frontend |
| `.claude/skills/dr-audit-fe/` | Cartella della skill, copiata per intero (`SKILL.md`) |
| `.ai/dr-guidelines-packages.json` | Voce `dr-fe` in `installed`: `package`, `installedAt` (data) e `commit` (commit di `main` installato). Il file si crea se manca. |

Nessuna modifica a `CLAUDE.md` né ai file di configurazione del core (`.editorconfig`, `.gitignore`, `.gitattributes`, `.claude/settings.json`, `.mcp.json`).

`LICENSE`, `.github/ISSUE_TEMPLATE/` e `dr-fe-install.ps1` restano in questo repo: non vengono copiati.

Senza `-Update` un file già presente viene saltato (`[SKIP]`). Per la skill vale la cartella: se `.claude/skills/dr-audit-fe/` esiste già, viene saltata per intero.

Dopo l'installazione riavvia Claude Code, oppure esegui `Developer: Reload Window` in VS Code: le skill si leggono all'avvio della sessione.

## 🔄 Aggiornare

Tutti i pacchetti del progetto: `/dr-get-latest`.

Solo questo pacchetto: stesso comando dell'installer del pacchetto, con `-Update` in coda:

```powershell
& ([scriptblock]::Create((gh api repos/davraf-amuro/dr-fe/contents/dr-fe-install.ps1 -H "Accept: application/vnd.github.raw" | Out-String))) -Update
```

Nota: `-Update` sovrascrive le copie locali. Si installa sempre l'ultimo `main` pushato: una modifica a questo repo non pushata su `main` non arriva nei progetti host.

Un file rimosso da questo repo resta nel progetto host: l'installer copia, non cancella.

## 🐞 Segnalare un problema o una miglioria

Non correggere la copia nel progetto host: si perde al primo `-Update`.

Dal progetto host usa `/dr-segnala-miglioria <descrizione>` (su Copilot il prompt `.github/prompts/dr-segnala-miglioria.prompt.md` del core). Apre la issue in questo repo.

| Modello | Quando |
|---------|--------|
| `.github/ISSUE_TEMPLATE/miglioria.md` | Richiesta evolutiva |
| `.github/ISSUE_TEMPLATE/problema.md` | Malfunzionamento |

---

*Documento aggiornato: Settembre 2026 — Revisione v1.0 — 2026-09-16 — claude-opus-5*
