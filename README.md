# Proje Takip Panosu

Iglesias Tour Turkey'nin projelerini (WhatsApp Tour Sales Assistant, WhatsApp Gateway API, TourPilot Platformu, AI Remarketing...) tek yerden takip etmek için canlı, statik bir dashboard. Veri düz JSON dosyalarında tutulur, `index.html` bunları tarayıcıda okuyup render eder. GitHub Pages'e push ettiğinizde site otomatik güncellenir.

## Canlı site nasıl açılır (ilk kurulum)

1. Bu klasörü GitHub'da **public** bir repo olarak push edin (private repoda GitHub Pages, Free planda desteklenmiyor — Settings → Pages'te "public" uyarısı görürseniz bu yüzdendir).
2. GitHub'da repo sayfasında **Settings → Pages** açın.
3. **Source**: "Deploy from a branch" → **Branch**: `main` → **Folder**: `/ (root)` seçip kaydedin.
4. Birkaç dakika içinde site şu adreste yayına girer: `https://<kullanici-adiniz>.github.io/<repo-adi>/`

Her `git push` sonrası GitHub Pages otomatik olarak yeniden derler — ekstra bir işlem gerekmez.

## Klasör yapısı

```
proje-takip-panosu/
├── index.html              ← Dashboard'un kendisi (HTML/CSS/JS, dış bağımlılık yok)
├── site.json                ← Başlık, alt başlık, üstteki uyarı banner'ı, son güncelleme tarihi
├── .nojekyll                ← GitHub Pages'in Jekyll işlemesini atlamasını sağlar
└── projects/
    ├── manifest.json         ← Hangi proje dosyalarının okunacağının listesi
    ├── whatsapp-tour-sales.json
    ├── whatsapp-gateway-api.json
    ├── tourpilot-platform.json
    └── ai-remarketing.json
```

## Panoyu nasıl güncellersiniz

**Mevcut bir projeyi güncellemek** için ilgili `projects/<proje>.json` dosyasını düzenleyip commit + push edin. Örneğin bir görev bittiğinde onu `phases.doing` listesinden çıkarıp `phases.done` listesine taşıyın, `changelog` dizisine yeni bir kayıt ekleyin.

**Yeni bir proje eklemek** için:
1. `projects/` altına yeni bir `<proje-id>.json` dosyası oluşturun (şablon için mevcut dosyalardan birini kopyalayın).
2. `projects/manifest.json` dizisine bu dosyanın adını ekleyin.
3. Commit + push — yeni proje kartı otomatik olarak panoda belirir.

### JSON şeması (her proje dosyası için)

```json
{
  "id": "kisa-benzersiz-id",
  "name": "Proje Adı",
  "statusLabel": "Kısa durum etiketi",
  "statusColor": "green | yellow | red | gray",
  "desc": "Bir-iki cümlelik açıklama",
  "progress": 0-100 veya null,
  "repoNote": "Kod/veri nerede duruyor (opsiyonel açıklama)",
  "phases": { "done": [], "doing": [], "todo": [] },
  "issues": [ { "severity": "high", "title": "...", "note": "..." } ],
  "integrations": [ { "name": "...", "status": "green", "label": "..." } ],
  "changelog": [ { "date": "...", "title": "...", "body": "..." } ]
}
```

`phases.*` dizilerindeki her öğe `{ "title": "...", "tag": "opsiyonel-etiket" }` şeklindedir.

## Yerelde önizleme

`index.html` dosyayı doğrudan çift tıklayarak (file://) açarsanız tarayıcı güvenlik kısıtları yüzünden JSON dosyaları yüklenmez. Yerel bir HTTP sunucusuyla açın:

```bash
cd proje-takip-panosu
python3 -m http.server 8080
# tarayıcıda http://localhost:8080 açın
```

## Bu pano nasıl oluşturuldu

15 Ağustos 2026'da Claude ile: n8n Cloud hesabına (iglesiastourturkey.app.n8n.cloud) canlı bağlanılarak WhatsApp Tour Sales Assistant projesindeki 14 workflow, tüm node'lar ve execution logları incelendi; ardından bilgisayardaki ilgili kod depoları (`iglesias-whatsapp-chatbot`, `tourops-ai`) taranarak mimarinin geri kalanı (gerçek WhatsApp köprüsü, TourPilot platformu, communications webhook'unun kaynak kodu) doğrulandı. TourPilot 403 hatasının kök nedeni (`Tenant mismatch`) bu incelemede kaynak koddan kesin olarak tespit edildi.
