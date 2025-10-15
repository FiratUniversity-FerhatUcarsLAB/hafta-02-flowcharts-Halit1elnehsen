Merhaba, **Hastane Randevu ve Tahlil Sistemi** için Türkçe olarak istediğiniz pseudocode aşağıdadır. Bu yapı, belirttiğiniz adımları mantıksal bir sıra ile takip eder.

-----

## Hastane Randevu ve Tahlil Sistemi Pseudocode

```
BAŞLA

  // Sistemin ana döngüsü
  TEKRARLA
    // Hasta kimlik doğrulama
    EKRANA_YAZ "Hoş geldiniz. Lütfen TC Kimlik numaranızı giriniz:"
    TC_NO = KULLANICIDAN_GİRİŞ_AL

    EĞER TC_NO_VERİTABANINDA_VARSA(TC_NO) İSE
      EKRANA_YAZ "Kimlik doğrulama başarılı. Lütfen yapmak istediğiniz işlemi seçiniz:"
      EKRANA_YAZ "1- Randevu Al"
      EKRANA_YAZ "2- Tahlil Sonuçlarını Görüntüle"
      ISLEM_SECIMI = KULLANICIDAN_GİRİŞ_AL

      // İşlem seçimine göre yönlendirme
      EĞER ISLEM_SECIMI == 1 İSE
        // Randevu Alma Modülü
        RANDevu_AL()
      YOKSA EĞER ISLEM_SECIMI == 2 İSE
        // Tahlil Sonucu Görüntüleme Modülü
        TAHLIL_SONUCU_GORUNTULE(TC_NO)
      YOKSA
        EKRANA_YAZ "Geçersiz işlem seçimi. Lütfen 1 veya 2 giriniz."
      SON_EĞER
    YOKSA
      EKRANA_YAZ "Hatalı veya sistemde kayıtlı olmayan TC Kimlik numarası. Lütfen tekrar deneyiniz."
    SON_EĞER

    // Başka bir işlem yapma seçeneği
    EKRANA_YAZ "Başka bir işlem yapmak istiyor musunuz? (E/H)"
    DEVAM_SECIMI = KULLANICIDAN_GİRİŞ_AL
  BİTENE_KADAR (DEVAM_SECIMI == 'H' veya DEVAM_SECIMI == 'h')

SON

// --- Fonksiyonlar ---

FONKSİYON RANDevu_AL()
  // Poliklinik seçimi
  EKRANA_YAZ "Randevu almak istediğiniz polikliniği seçiniz:"
  POLIKLINIK_LISTESI_GETIR_VE_GOSTER()
  POLIKLINIK_SECIMI = KULLANICIDAN_GİRİŞ_AL

  // Doktor listesini gösterme
  TEKRARLA
    EKRANA_YAZ "Randevu almak istediğiniz doktoru seçiniz:"
    DOKTOR_LISTESI_GETIR_VE_GOSTER(POLIKLINIK_SECIMI)
    DOKTOR_SECIMI = KULLANICIDAN_GİRİŞ_AL
  BİTENE_KADAR DOKTOR_SECIMI_GECERLI_Mİ(DOKTOR_SECIMI)

  // Uygun saatleri bulma ve gösterme
  TEKRARLA
    EKRANA_YAZ "Uygun randevu saatleri aşağıdadır:"
    UYGUN_SAATLERI_GETIR_VE_GOSTER(DOKTOR_SECIMI)
    SAAT_SECIMI = KULLANICIDAN_GİRİŞ_AL
  BİTENE_KADAR SAAT_SECIMI_GECERLI_Mİ(SAAT_SECIMI)

  // Randevu onayı
  EKRANA_YAZ "Randevuyu onaylamak için 'EVET' yazın:"
  ONAY = KULLANICIDAN_GİRİŞ_AL
  EĞER ONAY == "EVET" İSE
    VERITABANINA_RANDEVU_YAZ(TC_NO, DOKTOR_SECIMI, SAAT_SECIMI)
    SMS_GONDER(TC_NO, "Randevunuz oluşturulmuştur...")
    EKRANA_YAZ "Randevunuz başarıyla oluşturulmuştur. Bilgiler SMS olarak gönderilmiştir."
  YOKSA
    EKRANA_YAZ "Randevu alma işlemi iptal edilmiştir."
  SON_EĞER
SON_FONKSİYON

FONKSİYON TAHLIL_SONUCU_GORUNTULE(TC_NO)
  // Tahlil kontrolü
  EĞER HASTAYA_AIT_TAHLIL_VAR_MI(TC_NO) İSE
    // Sonuç hazır mı kontrolü
    EĞER SONUC_HAZIR_MI(TC_NO) İSE
      EKRANA_YAZ "Tahlil sonuçlarınız aşağıdadır:"
      TAHLIL_SONUCLARINI_EKRANA_YAZDIR(TC_NO)

      // PDF indirme seçeneği
      EKRANA_YAZ "Tahlil sonuçlarını PDF olarak indirmek ister misiniz? (E/H)"
      PDF_SECIMI = KULLANICIDAN_GİRİŞ_AL
      EĞER PDF_SECIMI == 'E' veya PDF_SECIMI == 'e' İSE
        TAHLIL_SONUCU_PDF_OLARAK_INDIR(TC_NO)
        EKRANA_YAZ "PDF indirme işlemi başlatıldı."
      SON_EĞER
    YOKSA
      EKRANA_YAZ "Tahlil sonuçlarınız henüz hazır değil. Lütfen daha sonra tekrar kontrol ediniz."
    SON_EĞER
  YOKSA
    EKRANA_YAZ "Sistemde adınıza kayıtlı bir tahlil sonucu bulunmamaktadır."
  SON_EĞER
SON_FONKSİYON
```
