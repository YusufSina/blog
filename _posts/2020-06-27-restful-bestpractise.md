---
layout: post
title: Web API, RESTful API Best Practise
date: 2020-06-27 02:52:00 +0300
description: Selçuk Ermaya'nın sunumundan aldığım notlarım # Add post description (optional)
img: rest.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [Web-API, RESTful-API-Best-Practise]
---

*Selçuk Ermaya'nın Web API, RESTful Best Practise sunumundan aldığım notlardır.*

Zapier, bir mail geldiğinde aksiyon üretmesini sağlayan bir uygulama.

## Efektif Uygulama Biçimleri

- **Semantic Naming:** Bir API ucuna  baktığım zaman o şu işi yapıyor diyebilmeliyim.
- **Reliation Endpoints:**  Bazı endpointler ilişkili olmalı, kullanıcının resimleri gibi,
- **Common Model:**  Cevap modelinin ortak olması gerekliliği
- **Common Security Layer:**  20 API için farklı farklı güvenlik yöntemleri uyguluyorsak, onu ortak bir katman haline getirmeliyiz.
- **Caching:**  Cacheleniyor olabilmesi gerekiyor, client tarafında da sunuc tarafında da, çok kullanılan API ler için
- **Seperated Request / DTO model:**  Bir DTOnun request modelinden farklı olması gerekiyor
- **Versioning:** Versiyonlama gerekiyor, API yi kullanan bir uygulamanın etkilenmemesi gerekir
- **Simplicity:** Basit olması gerekiyor
- **Less dependency:** Az bağımlı olması gerekiyor
- **Validation:**  Validation, ne hatası oluştuğunu bildirmesini sağlayacak

### İsimlendirme

> HTTP 1.0 da sadece get ve post vardı.
>
>Post ile hem post hem put yapabilirsin ama anlaşılabilir olmak için put kullan.
>
>PUT da tüm alanlar güncellenir, PATCH de sadece gönderilen alanlar güncellenir.
>
>/me gayet anlaşılır bir endpoint, /users/id/followers da olabilir ama /me daha iyi
>

### İlişkili API Uçları

### Ortak Cevap Modeli

    "success" : true,
    "code" : 200
    "message" : "user added"
    "internalMessage": "user added with id: 5"

Burada internal mesaj developer için message display için.

    "success" : true,
    "code" : 200
    "message" : "user added"
    "internalMessage": "user added with id: 5"
    "validations" : [
      "UserName" : "cannot be null",
      "UserName" : "Please enter a value "
    ]

Büyük firmalarda fb, twitter gibi firmalarda API larda modeller genellikle ortaktır.

<mark>Muhakkak ve muhakkak Request modelle DTO model birbirinden farklı olsun.</mark>

#### Status Kodları

- 200 Ok demek
- 304 no content
-  500 internal server, bende bir hata var
- 400 ve sonrası sende bir hata var, 404 ulaşmaya çalıştığın uç bende yok, 401 login olmamışsın/auth token göndermemişsin,  403 göndermiş olduğub auth token ile bu işlemi yapamazsın.

### Önbellekleme

İstenilen veri sürekli değişmeyen bir veri ise bu veriyi memoryde ya da redis gibi cache ortamlarında yada  cassandra ya da vb. ortamlarda tutun. Belirli bir süre (1-2 saat) veriyi cachle ve clientada verinin cacheden geldiğini söyle, cachein ne zaman yenileneceğini de söyle, eğer kullanıcı header ile cacheden istemediğini belirtirse canlı data alırsın. İki türlü cache var;

- Client Cache
- Server Cache

### Güvenlik

- Basic Authenticaton
- Token
- OAuth 2.0 -> token üretme mekanizması
- SSL

### Çoklu Dil Desteği

Çoklu dil desteğinde birimleri de kültüre göre gönderebilirsin.

### Versiyonlama

    /v1/users     1.0.0
    /v1.1/users   1.1.0
    /v1.1.3/users   1.1.3
    /users        latest

Semantic versiyonlama da ilk 3 numara önemli, ilk rakamın değişmesi breaking changes denilen uygulamanın çalışmasına engel olabilecek güncellemeler olduğunu gösterir. İkinci rakamda görülen şeyler çok kritik olmayan güncellemeler ama yinede bir bakımlık güncellemelerdir. Sonunca sayı ise genellikle iyileştirmeler ve eklemelerdir.

> Sil baştan diye bir kitap var (roman olan değil girişimcilik kitabı) tavsiye edildi.

## API Gateway

API gateway, API önüne koyulan ve proxyleme yapan bir arkadaşımız. Ne yapıyor? Bir şeyi diğer tarafa aktarır.

Validasyon ile zorunlulukları bildiririz.

Örnek ;

- Header ile token göndermek zorundasın.
- ContentType göndermek zorundasın. Ya application/json göndericeksin ya application/xml göndericeksin
- Bodynin sizeı çok büyük, sınırlayabilirsin.

Denilebilir.

Bunları her API ye yazmamak için MiddleWare e yazman gerekir.

Validasyon, loglama gibi şeyler API Gateway içerisinde barınır.

API Gatewayler;

- getkong
- tyk.io

Yardımcı Araçlar;

- HTTPie