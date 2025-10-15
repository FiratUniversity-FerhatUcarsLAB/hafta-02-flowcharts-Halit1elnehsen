BAŞLA

  // --- Kart ve PIN işlemleri ---
  KART_TAK
  PIN_DOGRU ← FALSE
  PIN_DENEME ← 0
  MAKS_PIN_DENEME ← 3

  TEKRARLA
      PIN_GIR ← KULLANICIDAN_PIN_AL()
      EĞER PIN_GIR = DOGRU_PIN İSE
          PIN_DOGRU ← TRUE
      DEĞİLSE
          PIN_DENEME ← PIN_DENEME + 1
          EKRANA_YAZ("Hatalı PIN. Kalan deneme: ", MAKS_PIN_DENEME - PIN_DENEME)
      SON
  PIN_DOGRU = TRUE VEYA PIN_DENEME = MAKS_PIN_DENEME OLANA KADAR TEKRARLA

  EĞER PIN_DOGRU = FALSE İSE
      EKRANA_YAZ("Kart bloke oldu. Lütfen banka ile iletişime geçiniz.")
      KART_IADESI()
      DUR
  SON

  // --- Ana işlem döngüsü ---
  DEVAM_ET ← TRUE
  GÜNLÜK_LIMIT ← 5000
  GÜNLÜK_CEKILEN ← 0

  SÜRECEK ← TRUE
  İKEN SÜRECEK
      EKRANA_YAZ("1 - Bakiye Sorgula")
      EKRANA_YAZ("2 - Para Çek")
      EKRANA_YAZ("3 - Kartı İade Et")
      SECIM ← KULLANICIDAN_SECIM_AL()

      EĞER SECIM = 1 İSE
          EKRANA_YAZ("Mevcut bakiye: ", BAKIYE)
      DEĞİLSE_EĞER SECIM = 2 İSE
          EKRANA_YAZ("Çekmek istediğiniz tutarı giriniz:")
          TUTAR ← KULLANICIDAN_TUTAR_AL()

          // --- Yetersiz bakiye kontrolü ---
          EĞER TUTAR > BAKIYE İSE
              EKRANA_YAZ("Yetersiz bakiye.")
          
          // --- 20 TL katı kontrolü ---
          DEĞİLSE_EĞER TUTAR MOD 20 ≠ 0 İSE
              EKRANA_YAZ("Tutar 20 TL'nin katı olmalıdır.")
          
          // --- Günlük limit kontrolü ---
          DEĞİLSE_EĞER (GÜNLÜK_CEKILEN + TUTAR) > GÜNLÜK_LIMIT İSE
              EKRANA_YAZ("Günlük para çekme limitinizi aştınız.")
          
          // --- Para çekme işlemi ---
          DEĞİLSE
              BAKIYE ← BAKIYE - TUTAR
              GÜNLÜK_CEKILEN ← GÜNLÜK_CEKILEN + TUTAR
              PARA_VER(TUTAR)
              FIS_YAZDIR(TUTAR, BAKIYE)
              EKRANA_YAZ("İşlem başarılı. Yeni bakiye: ", BAKIYE)
          SON

      DEĞİLSE_EĞER SECIM = 3 İSE
          SÜRECEK ← FALSE
      DEĞİLSE
          EKRANA_YAZ("Geçersiz seçim.")
      SON

      EĞER SÜRECEK = TRUE İSE
          EKRANA_YAZ("Başka işlem yapmak ister misiniz? (E/H)")
          CEVAP ← KULLANICIDAN_CEVAP_AL()
          EĞER CEVAP = "H" VEYA "h" İSE
              SÜRECEK ← FALSE
          SON
      SON

  SON_DÖNGÜ

  KART_IADESI()
  EKRANA_YAZ("Teşekkür ederiz. İyi günler dileriz.")

BİTİR
