# Redis Eğitimi – Redis’teki Veri Türleri

## Veri Türlerinin Önemi

- Redis sadece bir **cache sistemi** değil, aynı zamanda farklı veri türlerini yüksek performansla saklayabilen bir **NoSQL veritabanıdır**.  
- Redis, metinsel (string) veriler dışında; **list**, **set**, **sorted set**, **hash**, **streams** ve **geospatial indexes** gibi birçok farklı veri yapısını destekler.  
- Her bir veri türü, farklı problem senaryoları için optimize edilmiştir.  

---

## Redis’teki Veri Türleri

| Veri Türü | Açıklama |
|------------|-----------|
| **String** | Redis’in en temel ve basit veri türüdür. Metinsel değerlerin yanı sıra sayısal, dosya veya binary veriler de saklanabilir. |
| **List** | Değerleri koleksiyonel olarak tutan veri türüdür. Veriler sıralı bir şekilde eklenir. |
| **Set** | Benzersiz (unique) verileri rastgele bir düzende tutan küme yapısıdır. Sıralama yoktur. |
| **Sorted Set** | Set’in sıralı versiyonudur. Her elemana bir **skor (score)** atanır ve bu skorlar üzerinden sıralama yapılır. |
| **Hash** | Key-Value formatında verilerin saklandığı yapılardır. Nesne temsili için uygundur. |
| **Streams** | Olay (event) bazlı verilerin sıralı olarak kaydedilmesini ve işlenmesini sağlar. |
| **Geospatial Indexes** | Coğrafi koordinatların (enlem, boylam) saklanmasını sağlayan özel veri türüdür. |

> 💡 En çok kullanılan türler: **String**, **List**, **Set**, **Sorted Set**, **Hash**

---

## Redis Strings

Metinsel değerleri tutan en temel veri türüdür.  
Sadece metinsel değil, sayısal değerleri de depolayabilir.

| Komut | İşlev | Örnek |
|--------|--------|--------|
| **SET** | Ekleme | `SET name hilmi` |
| **GET** | Okuma | `GET name` → `hilmi` |
| **GETRANGE** | Karakter aralığını okuma | `GETRANGE name 1 2` → `"il"` |
| **INCR / INCRBY** | Sayısal değeri artırma | `INCR count` |
| **DECR / DECRBY** | Sayısal değeri azaltma | `DECR count` |
| **APPEND** | Mevcut değerin sonuna ekleme | `APPEND name celayir` |

> 🔹 String türü sadece metin değil, **integer** türünde sayısal işlemleri de destekler.  
> 🔹 Sayılar string olarak tutulur ancak aritmetik işlemler yapılabilir.

---

## Redis Lists

Koleksiyonel verileri tutan türdür.  
Listenin başına veya sonuna veri eklenebilir, belirli aralıklarda listelenebilir.

| Komut | İşlev | Örnek |
|--------|--------|--------|
| **LPUSH** | Listenin başına veri ekleme | `LPUSH names hilmi ahmet` |
| **RPUSH** | Listenin sonuna veri ekleme | `RPUSH names rıfkı` |
| **LRANGE** | Verileri listeleme | `LRANGE names 0 -1` |
| **LPOP** | İlk veriyi çıkarma | `LPOP names` |
| **RPOP** | Son veriyi çıkarma | `RPOP names` |
| **LINDEX** | Belirli indexteki veriyi getirme | `LINDEX names 1` |

> 🔹 List veri türü bir **queue (kuyruk)** veya **stack (yığın)** gibi davranabilir.  
> 🔹 Türkçe karakter desteği için PowerShell bağlantısında `--raw` parametresi kullanılmalıdır.

---

## Redis Set

Birbirinden farklı (unique) değerleri rastgele düzende tutan küme veri türüdür.

| Komut | İşlev | Örnek |
|--------|--------|--------|
| **SADD** | Ekleme | `SADD color red blue green` |
| **SREM** | Silme | `SREM color blue` |
| **SISMEMBER** | Arama | `SISMEMBER color red` → `1` |
| **SINTER** | İki set’in kesişimini getirir | `SINTER user1:roles user2:roles` |
| **SCARD** | Eleman sayısını getirir | `SCARD color` |

> 🔹 Set yapısı **unique (benzersiz)** elemanlardan oluşur.  
> 🔹 Sıralama yoktur.  
> 🔹 Kullanım örneği:  
>   Kullanıcı rollerini, etiketleri, benzersiz ID listelerini tutmak.

---

## Redis Sorted Set (ZSet)

- Set yapısına benzer fakat her elemana **skor (score)** atanır.  
- Elemanlar bu skor değerine göre sıralanır.  
- Duplicate (tekrar eden) veriler tutmaz.

| Komut | İşlev | Örnek |
|--------|--------|--------|
| **ZADD** | Ekleme | `ZADD teams 1 A 2 B 3 C` |
| **ZRANGE** | Sıralı verileri getirir | `ZRANGE teams 0 -1` |
| **ZREMRANGE** | Silme | `ZREM teams B` |
| **ZRANK** | Verinin sırasını getirir | `ZRANK teams C` |

> 🔹 Her elemana skor atanır → sıralama bu değere göre yapılır.  
> 🔹 Kullanım örneği: Puan tablosu, skor bazlı sıralamalar, liderlik listeleri.

---

## Redis Hash

Key-Value formatında verileri tutan veri türüdür.  
Bir Hash içinde birden fazla key-value çifti saklanabilir (örneğin bir nesnenin alanları gibi).

| Komut | İşlev | Örnek |
|--------|--------|--------|
| **HSET / HMSET** | Ekleme | `HSET employees username gncy`<br>`HSET employees password 123` |
| **HGET / HMGET** | Getirme | `HGET employees username` → `gncy` |
| **HDEL** | Silme | `HDEL employees username` |
| **HGETALL** | Tüm verileri getirir | `HGETALL employees` |

> 🔹 Hash yapısı, bir anahtar içinde **çoklu alan–değer çiftleri** saklar.  
> 🔹 Nesne tabanlı veri modellemesinde kullanışlıdır.  
> 🔹 Örnek kullanım: `users:{id}` → `{ username, email, password }`

---

## Redis Streams ve Geospatial Indexes (Genel Bilgi)

- **Streams:** Event (olay) verilerini sıralı şekilde kaydeden veri türüdür.  
  - Log takibi, olay bazlı sistemlerde kullanılır.  
- **Geospatial Indexes:** Coğrafi koordinatları (enlem, boylam) saklamayı sağlar.  
  - Konum bazlı aramalarda (ör. “en yakın kullanıcıyı bul”) kullanılır.

---

## Türkçe Karakter Desteği (PowerShell)

Docker üzerinden Redis konteynerına bağlanırken Türkçe karakterlerin doğru görünmesi için:

```bash
docker exec -it redis_container_id redis-cli --raw

📘 Kaynak:
Bu notlar, Gençay Yıldız tarafından hazırlanmış aşağıdaki eğitim videosu temel alınarak derlenmiştir:
🎥 [Redis Eğitimi] (https://www.youtube.com/playlist?list=PLQVXoXFVVtp3_UlZu9qibcUzfm9ve3yVO)