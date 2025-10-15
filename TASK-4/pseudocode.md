BAŞLA

    // Veri Yapıları Örnekleri (Basitleştirilmiş)
    YAPISAL TIP Ogrenci:
        ogrenci_no : TAMSAYI
        sifre : METİN
        gpa : GERÇEK SAYI
        alinan_dersler : LİSTE<Ders Kodu>
        toplam_kredi : TAMSAYI

    YAPISAL TIP Ders:
        ders_kodu : METİN
        ders_adi : METİN
        kontenjan_mevcut : TAMSAYI
        kontenjan_max : TAMSAYI
        on_kosul_dersleri : LİSTE<Ders Kodu>
        kredi : TAMSAYI
        saatler : LİSTE<Gün/Saat> // Örn: Pzt 10:00-12:00
        
    // Global Değişkenler
    SABİT MAKS_KREDİ = 35
    SABİT GPA_ONAY_SINIRI = 2.5
    
    // ----------- 1. ÖĞRENCİ GİRİŞİ -----------
    FONKSİYON OgrenciGiris() : Ogrenci
        DO
            ÇIKIŞ("Öğrenci Numarası Girin: ")
            GİRİŞ(no)
            ÇIKIŞ("Şifre Girin: ")
            GİRİŞ(parola)

            // Varsayımsal veritabanı sorgusu
            ogrenci = VERITABANI.OgrenciBul(no, parola)

            EĞER ogrenci BULUNDU İSE:
                ÇIKIŞ("Giriş başarılı. Hoş geldiniz, " + ogrenci.ad)
                GERİ DÖN ogrenci
            DEĞİLSE:
                ÇIKIŞ("Hatalı öğrenci numarası veya şifre.")
        WHILE (ogrenci BULUNAMADI)
    SON FONKSİYON
    
    // ----------- ANA KAYIT AKIŞI -----------
    
    SECİLİ_OGRENCI = OgrenciGiris()
    
    KAYIT_DEVAM_ET = DOĞRU
    
    WHILE KAYIT_DEVAM_ET DO
        
        // ----------- 2. DERS LİSTESİNİ GÖRÜNTÜLEME -----------
        ÇIKIŞ("\n--- MEVCUT DERSLER LİSTESİ ---")
        DERS_LİSTESİ = VERITABANI.TümDersleriGetir()
        
        FOR HER DERS IN DERS_LİSTESİ DO
            ÇIKIŞ("Kod: " + DERS.ders_kodu + 
                  " | Ad: " + DERS.ders_adi +
                  " | Kredi: " + DERS.kredi +
                  " | Kontenjan: " + DERS.kontenjan_mevcut + "/" + DERS.kontenjan_max)
        END FOR
        ÇIKIŞ("---------------------------------")
        
        // ----------- 3. DERS EKLEME/ÇIKARMA DÖNGÜSÜ -----------
        
        ÇIKIŞ("\nİşlem Seçin: (E)kle / (Ç)ıkar / (O)naylayıp Çık")
        GİRİŞ(ISLEM_SECİMİ)
        
        EĞER ISLEM_SECİMİ == 'E' İSE:
            
            ÇIKIŞ("Eklemek istediğiniz dersin kodunu girin:")
            GİRİŞ(SECİLEN_KOD)
            SECİLEN_DERS = VERITABANI.DersBul(SECİLEN_KOD)
            
            EĞER SECİLEN_DERS BULUNDU İSE:
                
                // --- KONTROL: Kontenjan Kontrolü ---
                EĞER SECİLEN_DERS.kontenjan_mevcut < SECİLEN_DERS.kontenjan_max İSE:
                    
                    // --- KONTROL: Ön Koşul Dersi Kontrolü ---
                    ON_KOSULLAR_TAMAM = KONTROL_OnKosul(SECİLİ_OGRENCI, SECİLEN_DERS)
                    EĞER ON_KOSULLAR_TAMAM İSE:
                        
                        // --- KONTROL: Zaman Çakışması Kontrolü ---
                        CAKISMA_YOK = KONTROL_ZamanCakismasi(SECİLİ_OGRENCI, SECİLEN_DERS)
                        EĞER CAKISMA_YOK İSE:
                            
                            // --- KONTROL: Kredi Limiti Kontrolü ---
                            YENİ_KREDİ = SECİLİ_OGRENCI.toplam_kredi + SECİLEN_DERS.kredi
                            EĞER YENİ_KREDİ <= MAKS_KREDİ İSE:
                                
                                // Ders Ekleme Başarılı
                                SECİLİ_OGRENCI.alinan_dersler.EKLE(SECİLEN_KOD)
                                SECİLİ_OGRENCI.toplam_kredi = YENİ_KREDİ
                                SECİLEN_DERS.kontenjan_mevcut = SECİLEN_DERS.kontenjan_mevcut + 1
                                ÇIKIŞ(SECİLEN_DERS.ders_adi + " dersi başarıyla eklendi.")
                                
                            DEĞİLSE:
                                ÇIKIŞ("HATA: Kredi limiti aşıldı. (Maksimum " + MAKS_KREDİ + ")")
                            END EĞER // Kredi Limiti
                            
                        DEĞİLSE:
                            ÇIKIŞ("HATA: Seçilen ders, mevcut derslerinizle zaman çakışması yaşıyor.")
                        END EĞER // Zaman Çakışması
                        
                    DEĞİLSE:
                        ÇIKIŞ("HATA: Ön koşul derslerini tamamlamadınız.")
                    END EĞER // Ön Koşul
                    
                DEĞİLSE:
                    ÇIKIŞ("HATA: Dersin kontenjanı dolmuştur.")
                END EĞER // Kontenjan
                
            DEĞİLSE:
                ÇIKIŞ("HATA: Geçersiz ders kodu.")
            END EĞER
            
        EĞER ISLEM_SECİMİ == 'Ç' İSE:
            // Basitçe çıkarma işlemi
            ÇIKIŞ("Çıkarmak istediğiniz dersin kodunu girin:")
            GİRİŞ(CİKARILACAK_KOD)
            CİKARILAN_DERS = VERITABANI.DersBul(CİKARILACAK_KOD)
            
            EĞER CİKARILAN_DERS BULUNDU VE SECİLİ_OGRENCI.alinan_dersler.İÇERİYOR(CİKARILACAK_KOD) İSE:
                SECİLİ_OGRENCI.alinan_dersler.SİL(CİKARILACAK_KOD)
                SECİLİ_OGRENCI.toplam_kredi = SECİLİ_OGRENCI.toplam_kredi - CİKARILAN_DERS.kredi
                CİKARILAN_DERS.kontenjan_mevcut = CİKARILAN_DERS.kontenjan_mevcut - 1
                ÇIKIŞ(CİKARILAN_DERS.ders_adi + " dersi başarıyla çıkarıldı.")
            DEĞİLSE:
                ÇIKIŞ("HATA: Ders listede yok veya kod geçersiz.")
            END EĞER
            
        EĞER ISLEM_SECİMİ == 'O' İSE:
            KAYIT_DEVAM_ET = YANLIŞ // Döngüden çık
        END EĞER
        
    END WHILE // Ders ekleme/çıkarma döngüsü sonu

    // ----------- 4. KAYIT ÖZETİ GÖSTERME VE ONAYLAMA -----------
    
    ÇIKIŞ("\n--- KAYIT ÖZETİ ---")
    TOPLAM_KREDİ = 0
    FOR KOD IN SECİLİ_OGRENCI.alinan_dersler DO
        DERS = VERITABANI.DersBul(KOD)
        ÇIKIŞ("-> " + DERS.ders_adi + " (" + DERS.kredi + " kredi)")
        TOPLAM_KREDİ = TOPLAM_KREDİ + DERS.kredi
    END FOR
    ÇIKIŞ("Toplam Kredi: " + TOPLAM_KREDİ)
    ÇIKIŞ("-------------------")

    // --- KONTROL: Danışman Onayı Kontrolü ---
    DANISMAN_ONAYI_GEREKLİ = (SECİLİ_OGRENCI.gpa < GPA_ONAY_SINIRI)
    
    EĞER DANISMAN_ONAYI_GEREKLİ İSE:
        ÇIKIŞ("GPA'nız (" + SECİLİ_OGRENCI.gpa + ") " + GPA_ONAY_SINIRI + 
              " altında olduğu için danışman onayı *gereklidir*.")
        ÇIKIŞ("Danışman onayını bekleyenler listesine eklendi. (Durum: Beklemede)")
        // Onay mekanizması burada çağrılabilir, ancak pseudocode'da basitleştirildi.
        
    DEĞİLSE:
        ÇIKIŞ("GPA'nız (" + SECİLİ_OGRENCI.gpa + ") yeterli. Danışman onayı *gerekli değildir*.")
        ÇIKIŞ("Kayıt işleminiz *onaylanmıştır*.")
        
        // Veritabanı Kayıt Güncellemesi
        VERITABANI.KayıtOnayla(SECİLİ_OGRENCI)
        VERITABANI.DersKontenjanlarınıGüncelle(SECİLİ_OGRENCI.alinan_dersler)

    END EĞER // Danışman Onayı

    ÇIKIŞ("\nİşlem Tamamlandı. Çıkış yapılıyor.")

SON

// Yardımcı Fonksiyonlar (Ayrıntılar burada verilmemiştir)

FONKSİYON KONTROL_OnKosul(ogrenci, ders) : MANTIKSAL // Gerekli ön koşulların öğrenci tarafından alınıp alınmadığını kontrol eder
    // Tüm ön koşul derslerinin ogrenci.alinan_dersler (veya geçmiş dersler) içinde olup olmadığını kontrol et
    GERİ DÖN DOĞRU_VEYA_YANLIŞ
SON FONKSİYON

FONKSİYON KONTROL_ZamanCakismasi(ogrenci, yeni_ders) : MANTIKSAL // Seçilen dersin saatlerinin mevcut derslerle çakışıp çakışmadığını kontrol eder
    // ogrenci.alinan_dersler'deki her dersin saatleri ile yeni_ders.saatler karşılaştırılır
    GERİ DÖN DOĞRU_VEYA_YANLIŞ
SON FONKSİYON
