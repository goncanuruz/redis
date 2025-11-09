# Redis Pub/Sub Özelliği
---

## Redis ve Mesaj Broker Özelliği
Redis her ne kadar caching süreçlerinde yaygın olarak kullanılsa da, özünde bir **pub/sub işlemi yapabilen message broker** özelliği barındırır.  
Bu özellik, farklı servisler veya uygulama bileşenleri arasında **asenkron mesajlaşmayı** mümkün kılar.

---

## Redis’te Pub/Sub İşlemi Yapmak İçin Yollar

Redis’te pub/sub işlemleri birkaç farklı şekilde yapılabilir:

1. **Redis CLI**  
   Redis CLI üzerinden `publish` ve `subscribe` komutları ile mesajlaşma yapılabilir.  
   Genellikle test amaçlı veya hızlı denemeler için tercih edilir.

2. **Redis API**  
   Farklı diller için uygun kütüphaneler mevcuttur:  
   - Node.js → `redis` paketi  
   - Python → `redis-py`  
   - .NET Core → `StackExchange.Redis`  
   Bu derste .NET Core üzerinde **StackExchange.Redis** kullanılmaktadır.

3. **Redis Insight**  
   Redis Insight arayüzü üzerinden kanallar izlenebilir, mesajlar gönderilebilir veya abone olunabilir.

---

## Redis CLI ile Pub/Sub İşlemleri

### 1️⃣ Kurulum ve Bağlantı
PowerShell’de iki ayrı pencere açılır ve her ikisinde Redis CLI’a bağlanılır:

```bash
docker exec -it <container_id> redis-cli --raw
```

---

### 2️⃣ Subscriber (Abone) Oluşturma
Bir pencere **consumer (subscriber)** görevindedir:

```bash
subscribe mychannel
```

---

### 3️⃣ Publisher (Yayıncı) Oluşturma
Diğer pencere **publisher** görevindedir:

```bash
publish mychannel "merhaba"
```

Bu durumda “mychannel” kanalına gönderilen “merhaba” mesajı, abone olan pencerede görüntülenir.

---

### Kullanım Senaryosu
Sunucudaki herhangi bir kanaldaki mesaj akışını izlemek veya test etmek istiyorsanız, Redis CLI üzerinden hızlıca takip yapılabilir.

---

## Redis Insight ile Pub/Sub İşlemleri

1. Redis Insight’ı açın ve sol menüden **Pub/Sub** sekmesine gidin.  
2. Buradan tüm kanallardaki mesaj akışlarını izleyebilir, yeni kanallara mesaj gönderebilirsiniz.

Redis Insight üzerinden `mychannel` gibi bir kanala mesaj gönderildiğinde, o kanala abone olmuş tüm subscriber’lar bu mesajı anında görür.

---

## ⚙️ Redis API ile Pub/Sub İşlemleri (.NET Core)

### 🧩 Gerekli Paket
`StackExchange.Redis`

### 🧱 Kod Yapısı
```csharp
using StackExchange.Redis;

// 1. Redis bağlantısı
ConnectionMultiplexer redis = await ConnectionMultiplexer.ConnectAsync("localhost:1453");

// 2. Subscriber oluşturma
ISubscriber subscriber = redis.GetSubscriber();

// 3. Publisher davranışı
await subscriber.PublishAsync("example-channel", message);

// 4. Subscriber davranışı
await subscriber.SubscribeAsync("example-channel", (channel, message) =>
{
    Console.WriteLine(message);
});
```

### Açıklama

| İşlem | Açıklama |
|--------|-----------|
| **PublishAsync** | Mesaj yayınlama |
| **SubscribeAsync** | Kanaldan mesaj okuma |

Uygulama ikiye ayrılır:  
- **Publisher:** Mesajları yayınlar  
- **Subscriber:** Mesajları dinler  

---

## Redis Pattern-Matching Subscription

Redis, **Pattern-Matching Subscription** modeli sayesinde abonelerin belirli kalıplara (pattern) uyan kanallardan mesaj almasını sağlar.

### Örnek
```csharp
await subscriber.SubscribeAsync("stock.*", (channel, message) =>
{
    Console.WriteLine(message);
});
```

### Açıklama
Bu durumda:
- `stock.apple`, `stock.google`, `stock.amazon` kanallarındaki mesajlar alınır.
- Ancak `news.tech` kanalındaki mesajlar filtrelenir.

### Kullanım Alanı
Birden fazla benzer kanal ismiyle çalışan sistemlerde (örneğin “stock.*”) mesajları toplu dinlemek için kullanılır.  
Desene uymayan mesajlar filtrelenir.

---

## Redis Pub/Sub ile RabbitMQ Arasındaki Farklar

| Özellik | Redis Pub/Sub | RabbitMQ |
|----------|----------------|-----------|
| **Kuyruk Mantığı** | Yok (sadece kanal tabanlı) | Var (Queue yapısı ile) |
| **Mesaj Saklama** | Kalıcı değil (geçici) | Kalıcı (persisted) |
| **Performans** | Hafif, hızlı | Daha gelişmiş ve yönetilebilir |
| **Kullanım Alanı** | Basit, anlık mesajlaşma | Gelişmiş dağıtık sistemler |

Redis Pub/Sub, özellikle **basit ve hızlı mesaj senaryolarında** tercih edilir.  
Kalıcı mesajlaşma gerektiren durumlarda **RabbitMQ** veya **Kafka** gibi çözümler daha uygundur.

---

## Kullanım Senaryosu

- Uygulamalar arası basit mesaj iletişimi  
- Cache güncellemelerini servisler arasında bildirmek  
- Replikasyon yapılmadığı durumlarda veri değişikliklerini dinlemek  
- Kritik olmayan asenkron olay bildirimleri  

---

##  Özet

| Başlık | Açıklama |
|--------|-----------|
| **Pub/Sub Mantığı** | Yayıncı (Publisher) – Abone (Subscriber) ilişkisiyle mesaj aktarımı |
| **Redis CLI** | `subscribe` ve `publish` komutlarıyla basit testler yapılabilir |
| **Redis Insight** | Görsel arayüzle kanalları takip etme |
| **Redis API (C#)** | `StackExchange.Redis` ile programatik kullanım |
| **Pattern Matching** | “stock.*” gibi desenlerle kanal filtreleme |
| **Avantajı** | Basit, hızlı, konfigürasyonsuz iletişim |
| **Dezavantajı** | Mesaj kalıcılığı yok, queue yapısı bulunmuyor |

---

## 📘 Kaynak
Bu notlar, **Gençay Yıldız** tarafından hazırlanmış eğitim videosu temel alınarak derlenmiştir.  

🎥 [Redis Eğitimi – NG Akademi](https://www.youtube.com/playlist?list=PLQVXoXFVVtp3_UlZu9qibcUzfm9ve3yVO)
