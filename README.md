# Dünya Savaşı — Çok Oyunculu

Tek oyunculu "Dünya Savaşı" oyununun sunucu yetkili (authoritative) çok oyunculu sürümü. Harita, binalar, birimler, kaynaklar ve yapay zekâ aynı; fark, tüm simülasyonun sunucuda dönmesi ve oyuncuların oda kodu ile aynı dünyaya bağlanması.

## Dosyalar

```
server.js          Sunucu: statik dosya + WebSocket + oyun simülasyonu
public/index.html  İstemci: arayüz, harita çizimi, WebSocket bağlantısı
package.json       Bağımlılıklar (ws) ve başlatma komutu
package-lock.json  Kilitlenmiş sürümler
render.yaml        Render.com tek tıkla dağıtım
.gitignore         node_modules vb. hariç tutma
```

## Yerel çalıştırma

```bash
npm install
npm start
```

Tarayıcıda `http://localhost:3000` aç. Port değiştirmek için:

```bash
PORT=8080 npm start        # macOS / Linux
set PORT=8080 && npm start # Windows cmd
```

Node 18 veya üzeri gerekir.

## Render'a dağıtım

**Yol 1 — render.yaml ile (önerilen)**
1. Bu klasörü bir Git deposuna koy (GitHub/GitLab).
2. Render → **New → Blueprint** → depoyu seç. `render.yaml` otomatik okunur.
3. Dağıtım bitince verilen `https://...onrender.com` adresi oyunun adresidir.

**Yol 2 — elle Web Service**
1. Render → **New → Web Service** → depoyu bağla.
2. Ayarlar:
   - Runtime: **Node**
   - Build Command: `npm install`
   - Start Command: `npm start`
   - Health Check Path: `/healthz`
3. Ortam değişkeni `NODE_VERSION=20.11.0` ekle (isteğe bağlı ama önerilir).

WebSocket aynı port üzerinden çalıştığı için ek ayar gerekmez; `PORT` değişkenini Render kendisi verir ve sunucu onu okur.

## Nasıl oynanır

1. Ad, ülke adı ve renk gir.
2. **Oda kur**: oda adı, oyuncu sayısı (2–8) ve yapay zekâ sayısı (0–3) seç. Oluşan 4 haneli kod herkese açıktır.
3. Kod bilen herkes **Koda katıl** ile girer. Oyun başladıktan sonra katılan da yeni bir ülke olarak dünyaya eklenir.
4. Oda kuran kişi çıksa bile oda silinmez; oyuncu kalmayan odalar 30 dakika sonra temizlenir.
5. Ülkeni yıkan son kişi kalınca savaş biter; **Yeniden başlat** odadaki herkesi tekrar lobiye alır.

Odadaki herhangi bir oyuncu oyunu başlatabilir. Oyun bittiğinde "Yeniden başlat" da herkese açıktır ve odayı yeniden lobiye alır.

## Notlar

- Odalar bellekte tutulur. Sunucu yeniden başlarsa (Render ücretsiz plan uykuya geçerse) odalar ve kayıtlar silinir.
- Render ücretsiz planda servis 15 dakika trafiksiz kalınca uyur; ilk açılış 30–60 saniye sürebilir.
- Simülasyon 10 Hz, durum paketleri saniyede 5 kez gönderilir; istemci konumları yumuşatır.
- Oyun içi kayıt özelliği yoktur; ilerleme sunucu belleğindedir.
