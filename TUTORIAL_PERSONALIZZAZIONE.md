# Run server live

bundle exec jekyll serve --livereload

# Guida alla Personalizzazione del Blog Chirpy

Questa guida ti spiega passo-passo come modificare il sito basato sul tema Jekyll Chirpy.

## 1. Cambiare le Informazioni Principali

Apri il file `_config.yml` nella cartella principale e modifica:
- `title`: il titolo del sito
- `tagline`: la descrizione breve
- `url`: l'URL del sito (quando sarà online)
- `author`: il tuo nome o nickname
- `lang`: la lingua (es. `it` per italiano)

Esempio:
```yaml
title: Il mio blog di cybersecurity
tagline: Notizie, guide e approfondimenti sulla sicurezza informatica
author: Alessandro Cimino
lang: it
```

## 2. Modificare il Menu di Navigazione

Apri `_data/navigation.yml` e cambia, aggiungi o rimuovi voci del menu.

Esempio:
```yaml
- title: Home
  url: /
- title: Articoli
  url: /posts/
- title: Contatti
  url: /contact/
```

## 3. Aggiornare i Social e i Contatti

- Modifica `_data/social.yml` per i tuoi social.
- Modifica `_data/contact.yml` per email o altri contatti.

## 4. Gestire gli Autori

Modifica `_data/authors.yml` per aggiungere o cambiare autori. Esempio:
```yaml
alessandro:
  name: Alessandro Cimino
  bio: Esperto di cybersecurity
  url: https://tuosito.it
```

## 5. Aggiungere o Modificare Articoli

Gli articoli sono file Markdown (`.md`) nella cartella `_posts/`.
- Il nome del file deve essere nel formato `YYYY-MM-DD-titolo-articolo.md`.
- All’inizio del file inserisci il front matter:

```markdown
---
layout: post
title:  "Titolo dell'articolo"
date:   2025-09-01 10:00:00 +0200
author: alessandro
categories: [tutorial, cybersecurity]
tags: [cybersecurity, guida]
---

Contenuto dell’articolo...
```

## 6. Cambiare Immagini e Logo

Sostituisci le immagini nella cartella `assets/img/`.
Per il logo, cambia il file `assets/img/logo.png`.

## 7. Personalizzare la Home Page

Modifica il file `index.html` per cambiare la struttura della home.

## 8. Vedere le Modifiche in Locale

1. Apri il terminale nella cartella del progetto.
2. Esegui:
   ```
   bundle exec jekyll serve
   ```
3. Visita `http://127.0.0.1:4000/` nel browser.

## 9. Compilare gli Asset (JS/CSS)

Se modifichi file JS o CSS:
1. Assicurati di avere Node.js installato.
2. Esegui:
   ```
   npm install
   npm run build
   ```

---

Per altre personalizzazioni consulta la [documentazione ufficiale Chirpy](https://chirpy.cotes.page/posts/getting-started/).
