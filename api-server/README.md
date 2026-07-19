# MeetNote AI — API Server

Toplantı metnini analiz eden Express servisi. **Kurulum sonrası hiçbir ek ayar yapmadan çalışır** — sunucu üzerinde çalışan kendi ücretsiz NLP motorumuzu (`src/lib/textAnalyzer.ts`) kullanır, herhangi bir API anahtarı gerektirmez.

## Kurulum

```bash
npm install
cp .env.example .env
npm run dev
```

Sunucu `http://localhost:8080` üzerinde ayağa kalkar ve doğrudan çalışır — `.env` içindeki `GEMINI_API_KEY` alanını boş bırakabilirsiniz.

- `GET /api/healthz` — sağlık kontrolü
- `POST /api/analyze` — `{ "text": "..." }` gönderin, `{ summary, decisions, actions }` döner

`meetnote-ai` frontend'i (Vite dev proxy ile) `/api/*` isteklerini otomatik olarak bu sunucuya yönlendirir — ayrıca bir yapılandırma gerekmez.

## (İsteğe bağlı) Google Gemini ile yükseltme

`GEMINI_API_KEY` tanımlanırsa sunucu, daha yüksek kaliteli sonuçlar için Google Gemini'nin ücretsiz katmanını dener; anahtar yoksa veya istek başarısız olursa otomatik olarak yerel motora döner. **Bu adım tamamen isteğe bağlıdır, atlayabilirsiniz** — site anahtarsız da tam çalışır.

Almak isterseniz:

1. https://aistudio.google.com/apikey adresine gidin (Google hesabınızla giriş yapın).
2. "Create API key" deyin — kredi kartı istemez.
3. Anahtarı `.env` dosyasındaki `GEMINI_API_KEY=` satırına yapıştırın.
4. **Faturalandırmayı (billing) etkinleştirmeyin.** Böylece ücretsiz kotanın üzerine asla ücret yansımaz; kota dolduğunda istekler sadece hata döner, kimseye fatura kesilmez.

## Kötüye kullanım koruması

`/api/analyze` uç noktası IP başına 10 dakikada 20 istekle sınırlıdır (`express-rate-limit`). Bu, herkese açık bir demo linkinin (Gemini anahtarı tanımlıysa) paylaşılan ücretsiz kotasının tek bir kötü niyetli kullanıcı tarafından tüketilmesini engeller.

## Canlı Ortam

Bu servis şu an [Render.com](https://render.com) ücretsiz katmanında yayında: `https://meetnote-api-server.onrender.com`. Frontend ([meetnote.site](https://meetnote.site), Vercel'de barındırılıyor) `/api/*` isteklerini otomatik olarak buraya yönlendiriyor (`meetnote-ai/vercel.json`).
