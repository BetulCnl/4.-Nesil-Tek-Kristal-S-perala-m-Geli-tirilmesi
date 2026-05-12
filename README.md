# 4. Nesil Ni-Bazlı Süperalaşımın ML ile Tasarımı

## Projenin Kısa Açıklaması
Uçak motorlarının en sıcak yerinde (1000 °C üstü) çalışan türbin kanatları
**süperalaşım** denilen özel metal alaşımlardan yapılır. En gelişmiş olanları
"4. nesil" diye geçer ve içlerinde **Ruthenium** adlı çok pahalı (altın
fiyatında) bir element vardır. Bu yüzden bu alaşımlar laboratuvarda harika
çalışsa da endüstriyel olarak kullanmak çok maliyetli.

Bu projenin amacı: **makine öğrenmesi kullanarak az Ruthenium içeren ama
performansı yine de yüksek olan yeni bir süperalaşım bileşimi tasarlamak.**

Mantığı şu:
1. Yüzlerce farklı süperalaşımın **bileşimini** (içinde hangi elementten
   ne kadar var) ve **test sonuçlarını** (ne kadar dayanıklı, ne kadar
   oksitleniyor) bir veri setinde toplarım.
2. Bir makine öğrenmesi modelini bu veriyle eğitirim. Model şunu öğrenir:
   *"Bileşim böyle olunca dayanım şu kadar oluyor."*
3. Sonra modeli **ters çevirip** sorarım: *"Ben şöyle bir performans
   istiyorum ve Ruthenium az olsun, hangi bileşim olmalı?"* Model bana
   aday bileşimler önerir.

## Veri seti toplamak neden zor?

Bu işin **en uzun ve en zor kısmı** veri seti hazırlamak. Hazır veri seti
yok çünkü:

- **Veri her yere dağılmış:** Bilgi 30+ farklı bilimsel dergide, binlerce
  makalede gömülü. Her makaleyi tek tek okuyup tablodan veri çekmek aylar alır.
- **Çoğu makale paywall arkasında:** Açık erişim değil, parayla. Üniversite
  aboneliği ile bunu aşıyorum.
- **Her makale farklı yazıyor:** Biri sıcaklığı Kelvin, biri Celsius
  yazıyor. Biri X testini yapıyor, öbürü Y testini. Bazı bilgiler
  makalenin tablosunda, bazıları metnin içinde gömülü.
- **Her makalede her bilgi yok:** Bir makale alaşımın bileşimini verir ama
  oksidasyon testi yoktur, başkası tam tersi. Eksik veri çok normal —
  modelin de bu eksikle başa çıkmasını öğretmek lazım.

Bunları aşmak için **otomatik bir veri toplama sistemi** kurdum:
makaleleri kendisi buluyor, indiriyor ve içlerinden bir LLM (büyük dil
modeli) yardımıyla yapılandırılmış veri çekiyor. Sistemin en kritik kuralı:
*"makalede yazmıyorsa boş bırak, asla tahmin etme."* Çünkü ML'de
**yanlış veri, eksik veriden çok daha tehlikelidir.**

## Modelin girdisi ne, çıktısı ne?

**Girdi (özellikler):**
- Alaşımın bileşimi (Ni, Cr, Co, Al, Ti, Ta, W, Mo, Re, Ru, Hf, vb. her
  birinin ağırlıkça yüzdesi)
- Isıl işlem parametreleri (hangi sıcaklıkta ne kadar bekletilmiş)
- Mikroyapı verileri (alaşımın iç yapısı hakkında ölçümler)
- Test koşulları (hangi sıcaklıkta, hangi atmosferde test edilmiş)

**Çıktı (tahmin edilecek):**
- **Sürünme kopma süresi** — alaşım yüksek sıcaklıkta yük altında ne
  kadar dayanıyor (saat)
- **Süneklik** — koparken ne kadar uzayabiliyor (alan daralması %)
- **Oksidasyon ağırlık değişimi** — yüksek sıcaklıkta havada bekledikçe
  ne kadar bozuluyor (mg/cm²)

Bu üç çıktının hepsini aynı anda iyi yapan bir alaşım = "iyi alaşım".

> Veri toplama script'leri ve ham veri telif hakkı nedeniyle bu repoda
> paylaşılmıyor. Model ve sonuç kodları sonraki aşamalarda eklenecek.
