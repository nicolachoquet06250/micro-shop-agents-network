---
name: media-pipeline
description: Règles pour servir, redimensionner et sécuriser les médias.
---

# Skill `media-pipeline`

## API

Pattern recommandé :

```text
GET /api/images/:id?size=<width>x<height>
```

## Validation

- valider ID ;
- valider `size` ;
- valider MIME ;
- limiter dimensions et charge ;
- éviter les transformations non bornées ;
- définir un comportement de cache.

## Transformation

Utiliser la bibliothèque adaptée au runtime, par exemple Sharp sous Node.

Ne jamais déformer une image sauf exigence explicite.
