# Portfolio — Yasser Moudarir

Portfolio personnel de **Yasser Moudarir** — Ingénieur Électromécanique, spécialisé en performance énergétique.

Site statique (HTML/CSS/JS), data-driven via `data.json`, sans build step.

## Développement local

```bash
node serve.mjs
```

Ouvre http://localhost:3000 (Node 24+ requis ; serveur zero-dépendance avec support des Range requests pour la vidéo).

## Structure

- `index.html` — page unique (HTML + CSS + JS inline)
- `data.json` — contenu du portfolio (expériences, projets, certifications, skills)
- `assets/` — vidéos du hero
- `brand_assets/` — logos et photo
- `cv/` — CV PDF
- `serve.mjs` — serveur statique pour dev local

## Déploiement

Hébergé sur Vercel — chaque `git push origin main` redéploie automatiquement.
