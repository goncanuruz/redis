# Redis – In-Memory Cache & Distributed Cache

## 1. In-Memory Cache

### İşlem Sırası

**[In-Memory Cache İşlem Sırası]**
1. `AddMemoryCache` servisini uygulamaya ekleyin.  
2. `IMemoryCache` referansını **inject** edin.  
3. `Set` metodu ile veriyi cache’leyin, `Get` metodu ile cache’ten okuyun.  
4. `Remove` fonksiyonu ile cache’lenmiş veriyi silin.  
5. `TryGetValue` ile kontrollü veri okuyun.

---

### Absolute & Sliding Expiration

| Tür | Açıklama | Örnek Kod |
|-----|-----------|-----------|
| **Absolute Time** | Verinin cache’te kalacağı mutlak süreyi belirler. Süre dolunca veri otomatik silinir. | `entry.AbsoluteExpiration = DateTime.Now.AddSeconds(30);` |
| **Sliding Time** | Veriye erişildikçe ömrü uzar. Erişim olmazsa belirlenen sürede silinir. | `entry.SlidingExpiration = TimeSpan.FromSeconds(5);` |

**Birlikte Kullanım (Karma):**  
Veri en fazla 30 saniye tutulur, ancak her erişimde 5 saniye uzar.  
5 saniye erişim yapılmazsa veri silinir.

```csharp
public class ValuesController : ControllerBase
{
    private readonly IMemoryCache _memoryCache;

    public ValuesController(IMemoryCache memoryCache)
    {
        _memoryCache = memoryCache;
    }

    [HttpGet("set")]
    public IActionResult SetCache()
    {
        _memoryCache.Set("name", "gncy");
        return Ok("Cache set edildi");
    }

    [HttpGet("get")]
    public IActionResult GetCache()
    {
        if (_memoryCache.TryGetValue("name", out string name))
            return Ok(name);
        return NotFound("Cache boş");
    }
}
```

---

## 2. Distributed Cache (Redis)

- **In-Memory Cache**’in aksine, verilerin **uygulamadan bağımsız** bir ortamda saklanması.  
- Aynı veriye birden fazla **sunucu veya servis** tarafından erişilebilmesi.  
- Uygulama restart olsa bile verilerin kaybolmaması.

---

### İşlem Sırası

1. `StackExchange.Redis` kütüphanesini yükleyin.  
2. `AddStackExchangeRedisCache` servisini uygulamaya ekleyin.  
3. `IDistributedCache` referansını **inject** edin.  
4. `SetString` (metinsel) veya `Set` (binary) metoduyla veriyi Redis’e cache’leyin.  
5. `GetString` veya `Get` ile veriyi alın.  
6. `Remove` ile cache’lenmiş veriyi silin.

---

### Servis Tanımı

```csharp
builder.Services.AddStackExchangeRedisCache(options =>
    options.Configuration = "localhost:1453");
```

> Farklı port veya uzak sunucu kullanıyorsan, bağlantı bilgilerini burada güncellemelisin.  
> Örneğin:  
> `options.Configuration = "192.168.1.10:6379";`

---

### Controller Örneği

```csharp
[Route("api/[controller]")]
public class ValuesController : ControllerBase
{
    private readonly IDistributedCache _distributedCache;

    public ValuesController(IDistributedCache distributedCache)
    {
        _distributedCache = distributedCache;
    }

    [HttpGet("set")]
    public async Task<IActionResult> SetCacheAsync()
    {
        await _distributedCache.SetStringAsync("name", "gncy", new()
        {
            AbsoluteExpiration = DateTime.Now.AddSeconds(30),
            SlidingExpiration = TimeSpan.FromSeconds(5)
        });

        await _distributedCache.SetAsync("surname", Encoding.UTF8.GetBytes("yldz"));
        return Ok("Redis cache set edildi");
    }

    [HttpGet("get")]
    public async Task<IActionResult> GetCacheAsync()
    {
        var name = await _distributedCache.GetStringAsync("name");
        var surnameBinary = await _distributedCache.GetAsync("surname");
        var surname = Encoding.UTF8.GetString(surnameBinary);
        return Ok(new { name, surname });
    }
}
```

---

### Expiration (Veri Ömrü) Davranışları

Redis üzerinde **AbsoluteExpiration** ve **SlidingExpiration** doğrudan uygulanabilir.  
Veri hem mutlak bir ömre, hem de erişime göre uzayan süreye sahip olabilir.

```csharp
await _distributedCache.SetStringAsync("date", DateTime.Now.ToString(), new()
{
    AbsoluteExpiration = DateTime.Now.AddSeconds(30),
    SlidingExpiration = TimeSpan.FromSeconds(5)
});
```

| Özellik | Açıklama |
|----------|-----------|
| **AbsoluteExpiration** | Verinin cache’te kalacağı kesin süre. Süre dolunca otomatik silinir. |
| **SlidingExpiration** | Belirli aralıklarla erişim oldukça süresi uzar, erişim olmazsa silinir. |

🔹 30 saniyelik mutlak ömür  
🔹 5 saniyelik sliding süresi  
🔹 5 saniye erişim yapılmazsa veri silinir

---

## 📘 Kaynak
Bu notlar, **Gençay Yıldız** tarafından hazırlanmış aşağıdaki eğitim videosu temel alınarak derlenmiştir:  
🎥 [Redis Eğitimi](https://www.youtube.com/playlist?list=PLQVXoXFVVtp3_UlZu9qibcUzfm9ve3yVO)
