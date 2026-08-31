# Galeria de Imagens — Sync Ibirapuera

Galeria de imagens para injeção via script no 3DVista, hospedada no AWS S3.

**Projeto:** Sync Ibirapuera — São Paulo, SP  
**Incorporadora:** CANOPUS

---

## URLs de produção

| Arquivo | URL |
|---------|-----|
| Galeria | `https://skylineip.s3.sa-east-1.amazonaws.com/Tour+Virtual/Canopus/galeria-canopus-sync/index.html` |
| Script  | `https://skylineip.s3.sa-east-1.amazonaws.com/Tour+Virtual/Canopus/galeria-canopus-sync/inject.js` |

**S3 path:** `s3://skylineip/Tour Virtual/Canopus/galeria-canopus-sync/`

---

## Estrutura de arquivos

```
galeria-canopus-sync/
├── index.html              ← galeria completa (auto-suficiente)
├── inject.js               ← loader leve para injeção no 3DVista
├── video-gallery.html      ← galeria de vídeos
└── assets/
    ├── Imagens/
    │   ├── Fachada/
    │   ├── Areas Comuns/
    │   └── Apartamentos/
    ├── Plantas/
    └── videos/
```

---

## Categorias da galeria

### Modo `imagens`

| Categoria | Label | Pasta |
|-----------|-------|-------|
| `fachada` | Fachada | `assets/Imagens/Fachada/` |
| `areas-comuns` | Áreas Comuns | `assets/Imagens/Areas Comuns/` |
| `apartamentos` | Apartamentos | `assets/Imagens/Apartamentos/` |

### Modo `plantas`

| Categoria | Label | Pasta |
|-----------|-------|-------|
| `plantas` | Plantas | `assets/Plantas/` |

---

## Deploy AWS S3

### Sync completo

```bash
aws s3 sync . "s3://skylineip/Tour Virtual/Canopus/galeria-canopus-sync/" \
  --exclude "*" \
  --include "index.html" \
  --include "inject.js" \
  --include "video-gallery.html" \
  --include "assets/Imagens/*" \
  --include "assets/Plantas/*" \
  --include "assets/videos/*" \
  --exclude "*/Thumbs.db"
```

### Atualizar só index.html e inject.js

```bash
aws s3 cp index.html "s3://skylineip/Tour Virtual/Canopus/galeria-canopus-sync/index.html" \
  --content-type "text/html" --cache-control "no-cache,no-store,must-revalidate"

aws s3 cp inject.js "s3://skylineip/Tour Virtual/Canopus/galeria-canopus-sync/inject.js" \
  --cache-control "no-cache,no-store,must-revalidate"
```

> **`--cache-control "no-cache,no-store,must-revalidate"`** — garante que o browser nunca sirva uma versão cacheada.

---

## Integração 3DVista

### Passo 1 — Loader (JavaScript global do projeto)

```js
(function(){
  var s = document.createElement('script');
  s.src = 'https://skylineip.s3.sa-east-1.amazonaws.com/Tour+Virtual/Canopus/galeria-canopus-sync/inject.js?v=' + Date.now();
  document.head.appendChild(s);
})();
```

> **`?v=` + `Date.now()`** — cache-busting: força o browser a baixar sempre a versão mais recente.

### Passo 2 — Acionar nos hotspots/botões

```js
// Abre galeria de perspectivas (Fachada + Áreas Comuns)
GaleriaImagens(1);

// Fecha galeria de imagens
GaleriaImagens(0);

// Abre galeria de plantas
GaleriaPlantas(1);

// Fecha galeria de plantas
GaleriaPlantas(0);
```

---

## Cores e tipografia

| Token CSS | Valor |
|-----------|-------|
| `--primary` (botão ativo) | `#5C6B92` |
| `--dark` (texto/UI) | `#000000` |
| `--bg` (fundo) | `#FFF5F3` |
| Fonte títulos | Cormorant Garamond |
| Fonte UI | Inter |
