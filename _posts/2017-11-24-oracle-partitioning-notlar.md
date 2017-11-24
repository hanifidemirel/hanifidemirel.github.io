---
layout: post
published: true
title: Oracle Partitioning Notları
date: '2017-11-24'
subtitle: Partitioning
---
## Genel Bakış

Partitioning, Oracle'da böl ve fethet mantığıyla çalışan, büyük tabloların ve indeksleri yönetimini kolaylaştıran bir mekanizmadır. Gelen veriyi belli kurallar çerçevesinde farklı partisyonlarda tutmayı sağlar. Bu mekanizmanın faydaları:

- Veriye hızlı erişim: OLTP, veri ambarı..vs tüm sistemler için geçerli bir avantaj
- Büyük miktarda veri kümesiyle yapılan işlemler(bulk) kolaylaşır. Zira 100GB lık bir tabloyu güncellemek aynı işlemi 10GB lık 10 tane tabloda yapmaktan daha az efor gerektirir.
- Bazı scriptlerde performans artışı: Özellikle veri ambarlarında önemli hız artışı sağlar zira geniş bir aralıktaki veriyi hiç erişmeye gerek kalmadan eleme imkanı sağlıyor.
Bunları biraz daha ayrıntılı inceleyelim şimdi.

	### 1- Erişilebilirlik
    
   Eğer üzerinde çalışılan tabloda partitioning yapılmışsa, tablo üzerinde herhangi bir sorgu çalıştırılırken optimizasyon mekanizması bunu anlar ve erişilmesine gerek
olmayan partisyonları sorgu planından çıkarır. O partisyonlarda şayet bir bozukluk varsa bile, plandan çıkarıldıkları için sorgu sorunsuz bir şekilde çalışacaktır.
   İkinci bir senaryo olarak ise elimizde 100GB boyutunda bir tablo ve partitioning yaparak 50 tane 2Gb boyutunda tablolar elde ettiğimizi düşünelim. Olası bir hata durumunda eğer partitioning yapmamışsak 100GB boyutundaki tabloyu düzeltmeye çalışacaz. Diğer durumda ise sorunun meydana geldiği partisyonlardan birini düzeltecez sadece ve bu sistemin çok daha kısa sürede tekrar ayağa kalkmasına olanak sağlayacak. Ayrıca diğer partisyonlar çalışmaya devam edecektir. Dolayısıyla erişilebilirlik artacaktır.
    
    ### 2- Yönetim Yükünün Azalması
    
    Veritabanında 10GB index olduğu bir durumu düşünelim. Eğer partition yoksa ve indeksi yeniden oluşturmamız gerekiyorsa bunu tek seferde yapmak
zorundayız ve en az 10GB boş alanımız olması gerekir. Ama 1GB lık partisyonlar halinde saklasaydık bu indeksi, öncekinin %10 u kadar bir alan yeterli olacaktı. Ayrıca bu şekilde işlem çok daha hızlı sonuçlanacaktır. 
Diğer bir avantaj da olası bir çökme durumunda en fazla %10 luk bir efor kaybımız olacaktır ve dolayısıyla sil baştan başlamak zorunda kalmayacağız.

Veri ambarları ve arşivleme sistemleri özelinde düşünürsek, partisyonların önemli bir faydası da sliding windows() uygulamalarında ortaya çıkıyor. Diyelim ki son 12 aylık
veriyi tutuyorsunuz sürekli. Partisyonlar olmadan böyle bir sistemi güncellemek büyük bir miktarda insert() ve delete() gerektirecektir. Partisyonlar olursa yapılması gereken
kısaca şu kadar:
   - Yeni veriyi içeren ayrı bir tablo yarat
   - İndeks oluştur
   - Bu tabloyu partisyonlu tabloya ALTER TABLE EXCHANGE PARTITION komutuyla hızlıca ekle.
   - Eski partisyonu at
    
    
   ### 3- Sorgu Performansı
    
   2 türlü iyileştirme sağlanabilir: Partisyon eleme ve paralel çalıştırma ancak bu iyileştirmeler sistemden sisteme değişkenlik gösterir.OLTP sistemlerde partitioning büyük performans artışları sağlamadığı gibi bazen negatif etki de yapabilir. Geleneksel OLTP sistemlerinde sonuçların anlık olarak dönmesi    beklenir ve geri dönen değerler oldukça küçük indeks aralıklarından taranarak getirilmiştir. Büyük objelerde full scan yapılmadığı için partisyon anlamsızlaşıyor.(partisyon büyük parçaların full scan yapılmasını gereksiz kılıyordu) Partisyonların paralel çalıştırma avantajı da burada geçersiz çünkü oltp sistemlerde kaynaklar başka işler için saklanmalı. Ayrıca zaten hızlı indeks erişimleri sorguyu hızlıca döndürecek şekilde tasarlanmıştır OLTP'de.

   Veri ambarlarında ise partitioning hem yönetim tarafında hem performans tarafında oldukça önemli bir araç. Sorguları hızlandırma konusunda indeks kullanmaktan çok daha iyi sonuçlar verir partitioning. Ayrıca veri ambarları paralel çalıştırma yapmaya da müsait sistemlerdir.
    
### Tablo Partisyon Şemaları    

1.Range partitioning:
    Belli bir kolondaki veriyi belli aralıklara ayırıp ona göre depolanmasını sağlayabilirsiniz bu yöntemde. Mesela Ocak 2010 tarihli satırların hepsini bir partisyona, 
    Şubat 2010 tarihi satırların hepsini İkinci bir partisyona gönderebilirsiniz. En sık kullanılan partisyon şeması budur.
    
2.Hash partitioning:
    Bir yada birkaç sütundaki değeri belli bir hash fonksiyonuna sokar ve çıktıları aynı olanları aynı partisyonda saklar.
    
3.List partitioning Bu yöntemde her partisyona belli elementler içeren bir liste atanmıştır. Gelen veride seçilen sütundaki veriye bakılır. O veri hangi partisyonun listesindeyse, veri o partisyona kaydedilir. Mesela tabloda şehir ismi varsa ve veriyi şehir şehir saklamak istiyorsak, bu şemayı kullanabiliriz.

4.Interval partitioning
    Bu şema da aslında Range partitioning e çok benziyor. Avantajı şu ki burada gelen veriye göre yeni partisyon otomatik olarak yaratılabilir. Range partitioningda DBA partisyonları önceden yaratmak zorundaydı.
    
    
    
    
    
    
    
    
    
    
    
    