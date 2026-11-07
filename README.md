# FLOP devnet miner hazırlığı · Vast.ai

Bu Türkçe hazırlık notu, tek GPU’lu bir Vast VM’de FLOP devnet miner denemesi planlayanlar için. Devnet test FLOP’u gelir sağlamaz. Aşağıdaki kontroller belge ve kaynak kod incelemesine dayanıyor. Canlı kurulum, süre ve maliyet sonuçları testten sonra eklenecek.

## Kiralamadan önce

1. [Resmî miner dokümanından](https://ask.flop.finance/docs/run-miner) yayımlanan paket bağlantısını ve SHA-256 değerini al. Arşivi çalıştırmadan önce hash’i karşılaştır. Başarısızsa dur.
2. Doğrulanmış paketin README’sini, Compose dosyalarını ve çalıştıracağın betikleri incele. CLI, worker ve backend için onaylı, uyumlu sürüm ve digest’leri belirle. Arşivin checksum’ı ile container image digest’leri ayrı kontrollerdir. [Paket README’si](https://flop.network/releases/cli/miner-pack-devnet-ts-20261106/README.md) ve [resmî release manifesti](https://flop.network/releases.json).
3. Vast’ta Docker çalıştırabilen bir NVIDIA **VM** seç. GPU ve VRAM, sürücü, Docker Compose, kalıcı disk ve ağ gereksinimlerini kontrol et. GPU, disk ve trafik fiyatını ayrı ayrı kaydet. Kalibrasyon süresini varsayarak açık uçlu kiralama yapma.
4. Kalıcı bir HTTPS adı planla. Geçici Quick Tunnel adresi yeniden başlatmada değişebilir. Backend portunu yayımlama. Worker’ın HTTPS proxy arkasındaki yerel portunu kullan. [Cloudflare Quick Tunnel sınırları](https://developers.cloudflare.com/tunnel/get-started/quick-tunnels/).

## UfukNode rehberini kullanırken

[UfukNode’un Türkçe rehberi](https://github.com/UfukNode/flop-miner-setup/blob/main/Turkce-Kurulum.md) VM seçimi, GPU kontrolü ve bileşenlerin sırası için bir saha örneği. Image referanslarında sürüm ve digest zaten var. Compose, backend portunu yayımlamıyor ve worker portunu loopback’e bağlıyor.

- **Gizli anahtarlar.** Mnemonic’i terminale basan adımları atla. Yalnız devnete ayrılmış bir kimlik kullan. Seed, signer, keystore parolası, API anahtarı ve session token’ı ekran görüntüsüne, log paylaşımına veya repoya girmez. Mevcut session seed’ini tekrar kurulum sırasında üzerine yazmaktan koru.
- **Sponsor.** Üçüncü taraf sponsor betiği signer’ı okuyup imzalı kayıt isteği gönderir. Resmî paketteki akışı incele ve kayıt öncesinde sponsor durumuna bak. Kayıt ve varsa stake tamamlama işlemlerini kullanıcı onayıyla ayrı yürüt.
- **Model ve policy.** Checkpoint’in zincirde kayıtlı kökünü ve BF16 ayarını doğrula. Paketin policy çözümleyicisiyle hash’in finalized zincirde ilgili model için izinli olduğunu kontrol et. Hash uyuşmazlığı çözülmeden worker’ı başlatma.
- **Kalibrasyon.** Rehberdeki 12 dakika tek bir test gözlemi. Bütçe ve test penceresini canlı devnet koşullarına göre belirle.

## Kurulum kanıtları

| Kontrol | Paylaşılabilir çıktı |
|---|---|
| Paket bütünlüğü | Resmî hash ile yerel arşiv hash’inin eşleşmesi |
| VM ve Docker | GPU modeli, VRAM ve Docker içinden GPU görünürlüğü |
| Backend | `measured` model kimliği, model kökü, BF16 ve policy hash’i |
| HTTPS | Token göndermeden `/session/info` için beklenen HTTP `401` |
| Miner | Public Explorer’da kayıt ve kabul edilmiş kalibrasyon |
| Kapanış | Süre, GPU/disk/trafik maliyeti ve faturalama durumu |

`401` yalnızca HTTPS yolunun isteği aldığını ve yetkisiz erişimi reddettiğini gösterir. Miner hazırlığını zincirdeki kalibrasyon ve worker’ın yerelde kontrol edilen `is_ready` alanıyla doğrula.

Ekran görüntülerinde sadece ilgili çıktı kalsın. Hesap bilgisi, IP, SSH bağlantı ayrıntıları ve kimlik bilgilerini kadraj dışında bırak. Kapanışta resmî miner çıkış adımlarını kontrol et. Vast’ta Stop sonrasında disk ücreti sürebilir. VM silindikten sonra faturalama durumunu kontrol et.
