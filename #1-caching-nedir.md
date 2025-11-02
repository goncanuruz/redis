# Redis Eğitimi #1 – Caching Nedir?

## Caching Nedir?
- Yazılım süreçlerinde verilere daha hızlı erişebilmek için bu verilerin bellekte saklanması işlemine **caching** denir.  
- Bilgisayarlarda kullanılan bellek türleri (RAM, disk vb.) hız ve kapasite açısından farklılık gösterir.  
  - RAM → Anlık işlem yapılabilen, erişim süresi düşük, hızlı bellek türüdür.  
  - Bu nedenle sık kullanılan verilerin RAM üzerinde tutulması performansı ciddi ölçüde artırır.  
- Örneğin, bir e-ticaret sitesinde ürünlerin RAM’de cache’lenmesi veritabanına göre çok daha hızlıdır.  
- Bu yaklaşım belirli verisel işlemler sürecinde **hız kazandırır** ve **performans optimizasyonu** sağlar.

---

## Caching’in Yazılıma Katkıları Nelerdir?

### 1. Veri Erişimini Hızlandırır  
- Verilere doğrudan bellekten erişildiği için erişim süresi ciddi ölçüde azalır.

### 2. Performans Artışı  
- Özellikle yavaş ve maliyetli veritabanı sorgularının sonuçlarını cache’den almak büyük performans farkı yaratır.  

### 3. Sunucu Yükünü Azaltır  
- Aynı verilerin tekrar tekrar veritabanından çekilmesi yerine cache’den alınması sunucu yükünü hafifletir.  

### 4. Çevrimiçi Uygulamalar İçin Kritik Öneme Sahiptir  
- Kullanıcıların veriye hızlı erişebilmesi ve işlem yapabilmesi gereken online sistemlerde cache hayati önem taşır.

---

## Ne Tarz Veriler Cache’lenir?

- Cache’lenecek veriler **sıklıkla** ve **hızlı şekilde erişilecek** veriler olmalıdır.  
- Örnekler:
  - Sık kullanılan veritabanı sorgu sonuçları  
  - Konfigürasyon verileri  
  - Menü bilgileri  
  - Yetkiler  
  - Statik dosyalar (resim, video vb.)

### ❌ Cache’lenmemesi Gereken Veriler:
- Sürekli **güncellenen veriler**  
- **Kişisel veriler**  
- **Güvenlik riski taşıyan veriler**  
- **Özel veriler**  
- **Geçici (temporary) veriler**

---

## Caching’in Zararları

### 1. Bellek Yükü  
Verileri bellekte tutmak RAM kullanımını artırır ve kapasitesi sınırlı belleklerde performansı olumsuz etkileyebilir.

### 2. Güncellik Sorunu  
Cache’deki veri ile veritabanındaki veri senkronize edilmezse **tutarsızlık** oluşabilir.

### 3. Güvenlik Sorunları  
Kritik veya hassas verilerin cache’lenmesi güvenlik riski oluşturabilir.

### 4. Yasadışı Kullanım  
Kimlik, finans, sağlık gibi kişisel bilgilerin cache’lenmesi **hukuki problemlere** yol açabilir.

---

## Bir Cache Mekanizmasının Temel Bileşenleri

1. **Cache Belleği:**  
   Verilerin saklandığı ve hızlı erişim için kullanılan bellek.

2. **Cache Bellek Yönetimi:**  
   Cache’deki verilerin ne kadar süreyle, hangi koşullarda tutulacağını ve silineceğini belirleyen yönetim yapısı.  
   - Saklama süresi  
   - Silinme sıklığı  
   - Güncellik durumu  

3. **Cache Algoritması:**  
   Verilerin cache’e nasıl ekleneceğini ve silineceğini belirleyen algoritmadır.  
   (Örneğin: LRU – Least Recently Used)


 **Not:** Cache verilerinin her zaman belirli bir yaşam süresi vardır.  
Sıklıkla değişen veriler için **kısa süreli**, sabit yapıdaki veriler için **uzun süreli** cache tercih edilir.

---

## Cache Olmazsa Ne Olur?

> ❓ “Peki caching kullanmaksızın tüm verileri veritabanından okursak ne olur?”

- Uygulama performans açısından çok daha **yavaş çalışacaktır.**  
- Veritabanı, verileri saklamak için optimize edilmiştir fakat okuma süreci belleğe kıyasla **daha yavaştır.**  
- Büyük veri setlerinde bu fark **ciddi performans kayıplarına** yol açar.  

---

## Caching Yaklaşımları

Caching temelde iki farklı şekilde uygulanır:

### 1. **In-Memory Caching**
- Veriler, uygulamanın çalıştığı bilgisayarın RAM’inde tutulur.
- Tek bir makinede çalışan uygulamalar için uygundur.

### 2. **Distributed Caching**
- Veriler birden fazla fiziksel makineye dağıtılır.
- Yüksek hacimli verilerde ve ölçeklenebilir sistemlerde daha güvenli ve performanslıdır.  
- Büyük veri setleri için **daha ideal yaklaşımdır.**

---

## Distributed Caching Yapıları

Distributed caching için kullanılabilecek farklı teknolojiler:

| Teknoloji | Açıklama |
|------------|-----------|
| **Redis** | Açılımı *Remote Dictionary Server*’dır. Bellek tabanlı, yüksek performanslı, açık kaynak NoSQL veritabanıdır. Caching yanında *message broker* olarak da kullanılabilir. |
| **Memcached** | Açık kaynak, key-value modelinde çalışan caching yazılımıdır. |
| **Hazelcast** | Java tabanlı, açık kaynak, key-value cache çözümüdür. |
| **Apache Ignite** | Open source, key-value tabanlı cache yapısıdır. |
| **EHCache** | Java tabanlı açık kaynak cache yazılımıdır. |
| **vs...** | Farklı alternatifler de mevcuttur. |

>  Uygulamanın ihtiyaç ve özelliklerine göre farklı cache sistemleri seçilebilir,  
> ancak genel kullanımda **Redis** en çok tercih edilen yapıdır.

---

## Özet

- **Caching**, verilerin bellekte tutulmasıyla performans kazancı sağlar.  
- **Redis**, bu iş için en yaygın kullanılan açık kaynak cache sunucusudur.  
- Cache kullanımı:
  - Performansı artırır  
  - Sunucu yükünü azaltır  
  - Online uygulamalarda hız kazandırır  
- Ancak yanlış veri seçimi durumunda:
  - Bellek yükü  
  - Tutarsız veri  
  - Güvenlik riskleri doğabilir  
- Cache’in ömrü sınırlıdır; verinin değişim sıklığına göre TTL (Time To Live) belirlenir.  
- Yaklaşımlar: **In-Memory** ve **Distributed**  
- Redis, distributed cache yapısında **yüksek performans** ve **esneklik** sağlar.  



📘 Kaynak:
Bu notlar, Gençay Yıldız tarafından hazırlanmış aşağıdaki eğitim videosu temel alınarak derlenmiştir:
🎥 [Redis Eğitimi] (https://www.youtube.com/playlist?list=PLQVXoXFVVtp3_UlZu9qibcUzfm9ve3yVO)