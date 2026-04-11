# API proxy Idealwine (spécification MVP)

> Objectif: ne **jamais** exposer directement les identifiants Idealwine dans l'app iPhone.
> L'app mobile parle uniquement à ce proxy.

## Authentification

- Header requis: `Authorization: Bearer <user_token>`
- Le proxy mappe ce token vers un compte utilisateur interne.

## Endpoints

### `POST /wine/search`

Recherche de vin, région, côte et accords.

**Body**

```json
{
  "query": "pauillac",
  "mode": "all"
}
```

`mode` possible: `all | type | region | rating | pairing`

**Réponse (200)**

```json
{
  "source": "idealwine",
  "data": [
    {
      "id": "idw_123",
      "name": "Château Lynch-Bages",
      "producer": "Château Lynch-Bages",
      "type": "Rouge",
      "region": "Bordeaux",
      "subregion": "Pauillac",
      "appellation": "AOC Pauillac",
      "vintage": 2018,
      "rating": "96/100",
      "pairing": "Agneau rôti, gibier",
      "marketPrice": 165
    }
  ]
}
```

### `POST /wine/recognize-label`

Reconnaissance OCR d'étiquette (texte détecté côté iPhone, matching côté API).

**Body**

```json
{
  "text": "Château Lynch-Bages 2018"
}
```

**Réponse (200)**

```json
{
  "source": "idealwine",
  "data": [{ "id": "idw_123", "name": "Château Lynch-Bages", "vintage": 2018 }]
}
```

### `POST /cellar/items`

Sauvegarde d'une bouteille dans la cave (si backend persistant activé).

### `GET /cellar/items`

Récupération de la cave de l'utilisateur.

---

## Notes techniques recommandées

- Backend: Node.js + Express + PostgreSQL.
- OCR mobile: VisionKit (iOS natif) ou Tesseract.js (web).
- Sécurité:
  - rotation des tokens,
  - rate limiting,
  - journaux d'accès,
  - conformité CGU Idealwine.
