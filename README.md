# analisa-site-regras

Regras públicas do app **Analisa Site**. Só regras genéricas; sem nomes de sites.
Receitas específicas usam apenas o **SHA-256 do host** (minúsculas, sem `www.`).

- `rules.json` — regras (esquema 1). O app só aceita se `version` for maior ou igual à que já tem.
- `rules.json.sig` — assinatura ECDSA P-256 (base64) do `rules.json`. Sem assinatura válida o app ignora o arquivo.

## Como adicionar uma regra
1. Acrescente um item em `detectors` e aumente `version`:
```json
{"id": "r_exemplo", "section": "bloqueios", "severity": "media",
 "title": "Exemplo: site exige Referer", "explain": "O servidor recusa sem Referer.",
 "advice": "Mandar o Referer da página.", "fixes": ["send_referer"],
 "when": {"all": [{"status": [403]}, {"header": "server", "re": "nginx"}]}}
```
2. Assine de novo: `tools/sign-rules.sh rules.json` (no repositório do app; a chave privada não fica aqui).
3. Faça commit de `rules.json` e `rules.json.sig`.

Condições (`when`): `all`, `any`, `not`, `status` (número ou lista), `header` (+ `re`), `html` (regex),
`cookie` (regex no nome), `request` (regex nos pedidos), `signal` (ex.: `no_media`, `drm`, `preroll`,
`service_worker`, `media_forbidden`, `popups`, `cookie_banner`, `live_challenge`), `hostHash`.
Correções válidas: `ua_desktop`, `ua_mobile`, `ua_firefox`, `ua_safari`, `send_referer`, `persist_cookies`,
`accept_cookies`, `autoplay_muted`, `wait_long`, `enable_blocker`, `block_popups`, `disable_sw`, `block_preroll`, `ytdlp_check`.
Outros campos: `fixOrder`, `adPatterns`, `blocklist.add` / `blocklist.remove`, `recipes` (`{"<sha256>": {receita v1}}`).
