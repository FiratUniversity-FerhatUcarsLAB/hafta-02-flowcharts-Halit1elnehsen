BAŞLA

SİSTEMİ başlat
SENSÖRLERİ kalibre et
KAMERA durumunu "PASİF" yap
ALARM_DURUMU = "KAPALI"

DÖNGÜ (SÜREKLİ)
    HAREKET = hareket_sensörü_oku()
    KAPI = kapi_sensörü_oku()
    PENCERE = pencere_sensörü_oku()
    EV_SAHİBİ_EVDE = ev_sahibi_kontrol()

    EĞER (HAREKET = VAR) VEYA (KAPI = AÇIK) VEYA (PENCERE = AÇIK) İSE
        KAMERA_AKTİF_ET()

        EĞER (EV_SAHİBİ_EVDE = HAYIR) İSE
            ALARM_SEVIYESI = alarm_seviyesi_belirle(HAREKET, KAPI, PENCERE)
            ALARM_DURUMU = "AKTİF"
            
            EĞER (ALARM_SEVIYESI = 1) İSE
                BİLDİRİM_GÖNDER("Düşük seviye alarm", "App")
            DEĞİLSE EĞER (ALARM_SEVIYESI = 2) İSE
                BİLDİRİM_GÖNDER("Orta seviye alarm", "SMS + App")
            DEĞİLSE EĞER (ALARM_SEVIYESI = 3) İSE
                BİLDİRİM_GÖNDER("Yüksek seviye alarm!", "SMS + App + Email")
                SİREN_AKTİF_ET()
            SON
        DEĞİLSE
            YANLIŞ_ALARM()
            ALARM_DURUMU = "KAPALI"
        SON
    DEĞİLSE
        KAMERA_DURUMU = "PASİF"
        ALARM_DURUMU = "KAPALI"
    SON

    BEKLE(5 saniye)
    ALARM_SIFIRLA_VEYA_DEVAM_ET()
SON DÖNGÜ

BİTİR
