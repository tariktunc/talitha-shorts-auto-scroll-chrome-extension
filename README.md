<div align="center">

<img src="icons/icon128.png" alt="Talitha logosu" width="96" height="96">

# Talitha — Auto Shorts

**YouTube Shorts bitince otomatik olarak bir sonrakine geçen, hafif bir Chrome eklentisi.**

![Manifest V3](https://img.shields.io/badge/Manifest-V3-4285F4?logo=googlechrome&logoColor=white)
![Sürüm](https://img.shields.io/badge/s%C3%BCr%C3%BCm-1.3.0-2ea44f)
![Testler](https://img.shields.io/badge/testler-35%20birim%20%2B%2013%20u%C3%A7tan%20uca-brightgreen)
![Veri toplamaz](https://img.shields.io/badge/veri-toplanmaz-blue)
![Diller](https://img.shields.io/badge/dil-T%C3%BCrk%C3%A7e%20%7C%20English-orange)

[Özellikler](#özellikler) · [Kurulum](#kurulum) · [Nasıl çalışır](#nasıl-çalışır) · [Gizlilik](#gizlilik-ve-izinler) · [Geliştirme](#geliştirme) · [English](#english)

</div>

---

## Neden Talitha?

Shorts izlerken her videonun sonunda elinizi kaydırmaya götürmeniz gerekmez. Talitha videonun bitmesine çok az kala "Sonraki video" düğmesine kendisi basar. Bir tıkla açılır, bir tıkla kapanır.

Ad, Büyük Ayı'daki Talitha yıldızından gelir ("ceylanın üçüncü sıçrayışı"). Eklentinin işi de bir videodan diğerine sıçramak.

## Özellikler

- **Otomatik geçiş.** Video bitmeden hemen önce sonraki Shorts'a geçer.
- **Tek tıkla aç/kapat.** Araç çubuğundaki simgeye tıklayınca küçük bir pencere açılır, içindeki düğmeyle eklenti açılıp kapanır.
- **Durumu simgede gösterir.** YouTube sekmesinde ve düğme açıkken yeşil `ON`, YouTube dışındaki sekmelerde gri `OFF`.
- **Arka planda bekler.** Shorts sekmesi arka plandaysa kaydırma durur, sekmeye dönünce kaldığı yerden devam eder.
- **Yalnızca Shorts'ta çalışır.** Sadece `youtube.com/shorts/` adreslerinde etkindir. Ana sayfada, aboneliklerde veya normal videolarda hiçbir şey yapmaz.
- **İki dil.** Türkçe ve İngilizce. Chrome'un arayüz diline göre otomatik seçilir.
- **Veri toplamaz.** Hiçbir sunucuya bağlanmaz, hiçbir şey kaydetmez.

## Kurulum

Eklenti henüz Chrome Web Store'da yayınlanmadı, paketlenmemiş olarak yüklenir.

1. Bu repoyu indirin veya klonlayın.
   ```bash
   git clone https://github.com/tariktunc/talitha-shorts-auto-scroll-chrome-extension.git
   ```
2. Chrome'da `chrome://extensions` sayfasını açın.
3. Sağ üstten **Geliştirici modu**nu açın.
4. **Paketlenmemiş öğe yükle**ye tıklayıp proje klasörünü seçin.
5. Bir Shorts videosu açın. Eklenti açıkken simgede `ON` rozeti görünür. Kapatmak için simgeye tıklayıp düğmeyi çevirin.

> Kurulum için Node.js veya başka bir bağımlılık gerekmez. Sadece testleri çalıştırmak isterseniz [Geliştirme](#geliştirme) bölümüne bakın.

## Nasıl çalışır

```
adres /shorts/ mi?  ──►  ekranın ortasındaki video  ──►  bitmesine 0,35 sn kala  ──►  "Sonraki video" düğmesine tıkla
   (250 ms'de bir)         aktif video sayılır            (başa sarmadan önce)          bulunamazsa aşağı ok tuşu
```

1. Her 250 ms'de adresin `/shorts/` ile başlayıp başlamadığına bakar.
2. Ekranın ortasını kaplayan videoyu aktif video sayar.
3. YouTube videoyu döngüye alır ve bitince `ended` olayı vermeden başa sarar. Bu yüzden eklenti, bitmesine 0,35 saniye kala geçer. Başa sarmayı ve `ended` bayrağını yedek olarak izler.
4. Geçiş için "Sonraki video" düğmesine tıklar. Düğme bulunamazsa aşağı ok tuşunu gönderir.
5. Geçişler arasında 1,5 saniye bekler, aynı videoda iki kez geçmez.
6. Sekme görünür değilse hiç geçmez.

## Gizlilik ve izinler

| İzin | Neden |
|---|---|
| `storage` | Açık/kapalı durumunu saklamak için. |
| `https://www.youtube.com/*` | Rozet için sekmenin adresine bakmak ve Shorts sayfasında çalışmak için. Başka sitelerin adresini okuyamaz. |

- Hiçbir veri toplanmaz, hiçbir sunucuya istek gönderilmez.
- Analiz, reklam veya izleme kodu yoktur.
- Kaynak kodun tamamı bu repoda, okunabilir halde durur.

## Dosyalar

| Dosya | Görev |
|---|---|
| `manifest.json` | Eklenti tanımı (Manifest V3) |
| `logic.js` | Karar mantığı. DOM'a dokunmaz, Node'da test edilir |
| `content.js` | Sayfayı izler, videoyu bulur, düğmeye tıklar |
| `background.js` | `ON` / `OFF` rozeti |
| `popup.html` / `popup.css` / `popup.js` | Simgeye tıklayınca açılan aç/kapa penceresi |
| `_locales/en`, `_locales/tr` | Dil paketleri |
| `icons/` | Logo (`icon.svg` ve PNG boyutları) |
| `test/`, `e2e/` | Birim ve uçtan uca testler |

Yeni dil eklemek için `_locales/en/messages.json` dosyasını `_locales/<dil-kodu>/messages.json` olarak kopyalayıp metinleri çevirmeniz yeter.

## Geliştirme

Node.js 18 veya üstü gerekir. Birim testleri için ek paket gerekmez.

```bash
node --test            # 35 birim testi
```

Uçtan uca testler eklentiyi gerçek bir Chromium'a yükleyip sürer. Playwright'ın Chromium'u ve internet gerekir.

```bash
npm install
npx playwright-core install chromium
npm run test:e2e       # 13 uçtan uca test
```

Testler şunları kapsar: rozet kuralı (benzer görünen adresler `www.youtube.com.evil.com` ve `http://` sayılmaz), gizli sekmede kaydırma olmaması, geri saran kullanıcıda geçiş olmaması, geçişler arası bekleme, dil paketlerinin tutarlılığı ve gerçek YouTube Shorts'ta uçtan uca geçiş.

YouTube testleri internete bağlıdır. Shorts yüklenmezse test başarısız değil, atlandı olarak raporlanır.

## Bilinen sınırlar

- Ana sayfadaki veya abonelikler sayfasındaki Shorts şeridinde çalışmaz, yalnızca `/shorts/` sayfasında çalışır.
- YouTube sayfa yapısını değiştirirse "Sonraki video" düğmesinin seçicisi (`#navigation-button-down button`) güncellenmelidir.
- Chrome Web Store'da yayınlanmış değildir.

## Lisans

[MIT](./LICENSE) © 2026 Blakfy Studio

---

## English

**Talitha — Auto Shorts** is a tiny Chrome extension (Manifest V3) that automatically moves to the next YouTube Short when the current one ends.

- Presses "Next video" just before the clip finishes, only on `youtube.com/shorts/`.
- One-click on/off from the toolbar popup, with a green `ON` / grey `OFF` badge.
- Pauses scrolling while the tab is in the background.
- English and Turkish UI, chosen from Chrome's language.
- Collects no data and makes no network requests. Only permission: `storage` (to remember the on/off state), plus host access to `https://www.youtube.com/*`.

**Install (unpacked):** clone the repo, open `chrome://extensions`, enable *Developer mode*, click *Load unpacked*, and pick the project folder.

**Tests:** `node --test` (35 unit tests, no dependencies). End-to-end: `npm install && npx playwright-core install chromium && npm run test:e2e`.

Made by [Blakfy Studio](https://blakfy.com). Licensed under MIT.
