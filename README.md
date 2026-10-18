# Wan 2.5 API (`wan-2.5`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.0336 per second** on the APIMart route — ein einziger OpenAI-kompatibler Endpoint unter `https://api.apimart.ai/v1`, kein Monatsabo nötig. *(erfasst am 2026-10-12)*

**[API-Key erhalten](https://go.apimart.ai/k-f4f2cc)** · **[Live-Preise](https://go.apimart.ai/k-42a922)** · **[Modellseite](https://go.apimart.ai/k-fdb0c8)** · [⚡ Quickstart in 60 Sekunden](#quickstart)

**Warum Teams Wan 2.5 (`wan-2.5`) über APIMart nutzen**

- **Ein Key, der gesamte Katalog.** Dieselbe Base-URL `https://api.apimart.ai/v1` und derselbe `Authorization`-Header erreichen Wan 2.5 (`wan-2.5`) und über 300 weitere Bild-, Video- und Sprachmodelle — Sie wechseln das `model`-Feld, nicht Ihren Client.
- **$1 Minimum, Pay-as-you-go.** Kein Abo und kein Prepaid-Tarif, den Sie vorab dimensionieren müssen: ab $1 aufladen und in Calls verbrauchen.
- **Die Abrechnung kommt in der Antwort zurück.** Jede abgeschlossene Aufgabe meldet den berechneten Betrag (`cost` / `credits_cost`); eine als `failed` markierte Aufgabe wird erstattet.
- **Asynchron by Design.** Absenden, `task_id` entgegennehmen, `GET /v1/tasks/{id}` pollen — Batching und Retries sind gewöhnliche Queue-Arbeit, keine Sonderintegration.

<!-- /conv-kit:v1 -->



Wan 2.5 is Alibaba's text-to-video and image-to-video model with high visual quality and smooth motion.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `wan-2.5` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-fdb0c8) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| per generated second | — | see the live pricing page |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-42a922) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | the shot description; camera and subject motion belong here |
| `duration` | integer | route default | clip length in seconds — billing is per generated second |
| `resolution` | string | route default | 480P / 720P / 1080P or the route’s own tiers |
| `size` / `aspect_ratio` | string | `16:9` | output framing |
| `image_urls` | string[] | — | first frame, first + last frame, or style references, where supported |
| `seed` | integer | — | fix the seed to reproduce a take |

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-fdb0c8) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"wan-2.5","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "wan-2.5", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["videos"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Wie lautet die Modell-id von Wan 2.5?**

`wan-2.5`. Siehe Routentabelle oben.

**Wie wird abgerechnet?**

**from $0.0336 per second** on the APIMart route. Die abgeschlossene Aufgabe meldet den genauen Betrag im Feld `cost`; eine fehlgeschlagene Aufgabe wird erstattet.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-f4f2cc) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [wan-2-5-prompts-de](https://github.com/v1hg04otqq/wan-2-5-prompts-de)
- **Same model, other languages** — 10 more language builds: English `wan-2-5-api` · 中文 [wan-2-5-api-cn](https://github.com/k1zfb6fitl-5/wan-2-5-api-cn) · Español [wan-2-5-api-es](https://github.com/qceub/wan-2-5-api-es) · Français [wan-2-5-api-fr](https://github.com/pprt5t0rj4/wan-2-5-api-fr) · Relay/gateway `wan-2-5-api-gateway` · Bahasa [wan-2-5-api-id](https://github.com/hg3vk8gied/wan-2-5-api-id) · 日本語 [wan-2-5-api-ja](https://github.com/k1glyz2132in/wan-2-5-api-ja) · 한국어 `wan-2-5-api-ko` · Português [wan-2-5-api-pt](https://github.com/qyif-d7c729m00/wan-2-5-api-pt) · Русский `wan-2-5-api-ru`
- **More models in this language** — [midjourney-api-de](https://github.com/h5fd0cofy/midjourney-api-de) · [gemini-2-5-flash-image-preview-api-de](https://github.com/kn6f69s2b8j9/gemini-2-5-flash-image-preview-api-de) · [nano-banana-api-de](https://github.com/uh6cob8op/nano-banana-api-de) · [gemini-3-1-flash-image-preview-api-de](https://github.com/k8wq8pd46e5ku/gemini-3-1-flash-image-preview-api-de) · [nano-banana-2-api-de](https://github.com/mocpl3/nano-banana-2-api-de) · [gemini-3-pro-image-preview-api-de](https://github.com/cmpxx2w/gemini-3-pro-image-preview-api-de)
- **Topic hubs** — every build of this model, all languages: [wan-2-5](https://github.com/topics/wan-2-5) · [wan-2-5-api](https://github.com/topics/wan-2-5-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-fdb0c8> | `apimart.ai/de/model/wan-2-5` |
| Live pricing | <https://go.apimart.ai/k-42a922> | `apimart.ai/de/pricing` |
| Get an API key | <https://go.apimart.ai/k-f4f2cc> | `apimart.ai/de/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Wan 2.5 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
