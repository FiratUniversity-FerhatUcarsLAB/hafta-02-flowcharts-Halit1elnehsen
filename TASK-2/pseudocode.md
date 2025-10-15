BAŞLA

// --- 1. Kullanıcı Girişi ---
EKRANA "Kullanıcı adı giriniz:" YAZ
KULLANICI_ADI ← GİRİŞ
EKRANA "Şifre giriniz:" YAZ
SIFRE ← GİRİŞ

EĞER (KULLANICI_ADI ve SIFRE doğruysa) İSE
    EKRANA "Giriş başarılı, hoş geldiniz!" YAZ
DEĞİLSE
    EKRANA "Giriş başarısız. Lütfen tekrar deneyin." YAZ
    PROGRAMI SONLANDIR
BİTİŞ_EĞER


// --- 2. Ürün Kategorilerinde Gezinme ---
KATEGORİ_LISTESİ ← ["Elektronik", "Giyim", "Ev", "Kitap", "Kozmetik"]
SEPET ← BOŞ_LİSTE

TEKRARLA
    EKRANA "Kategoriler: ", KATEGORİ_LISTESİ YAZ
    EKRANA "Bir kategori seçin (veya 'çıkış' yazın): " YAZ
    SEÇİLEN_KATEGORİ ← GİRİŞ

    EĞER (SEÇİLEN_KATEGORİ == "çıkış") İSE
        ÇIK
    BİTİŞ_EĞER

    // --- 3. Ürün Listeleme ve Sepete Ekleme ---
    ÜRÜN_LISTESİ ← KATEGORİYE_GÖRE_ÜRÜNLERİ_GETİR(SEÇİLEN_KATEGORİ)

    DÖNGÜ i = 1 → UZUNLUK(ÜRÜN_LISTESİ)
        EKRANA i, " - ", ÜRÜN_LISTESİ[i].AD, " (", ÜRÜN_LISTESİ[i].FİYAT, " TL)" YAZ
    SON_DÖNGÜ

    EKRANA "Sepete eklemek istediğiniz ürün numarasını girin (veya 0 ile geçin): " YAZ
    SECİM ← GİRİŞ

    EĞER (SECİM == 0) İSE
        DEVAM_ET
    BİTİŞ_EĞER

    SEÇİLEN_ÜRÜN ← ÜRÜN_LISTESİ[SECİM]

    // --- 4. Stok Kontrolü ---
    EĞER (SEÇİLEN_ÜRÜN.STOK > 0) İSE
        SEPETE_EKLE(SEPET, SEÇİLEN_ÜRÜN)
        SEÇİLEN_ÜRÜN.STOK ← SEÇİLEN_ÜRÜN.STOK - 1
        EKRANA "Ürün sepete eklendi!" YAZ
    DEĞİLSE
        EKRANA "Üzgünüz, bu ürün stokta yok." YAZ
    BİTİŞ_EĞER

SON_TEKRARLA (KULLANICI çıkış diyene kadar)


// --- 5. Sepeti Görüntüleme ve Düzenleme ---
TEKRARLA
    EKRANA "Sepetiniz:" YAZ
    DÖNGÜ i = 1 → UZUNLUK(SEPET)
        EKRANA i, " - ", SEPET[i].AD, " | ", SEPET[i].FİYAT, " TL" YAZ
    SON_DÖNGÜ

    EKRANA "Toplam: ", SEPET_TOPLAM_TUTAR(SEPET), " TL" YAZ
    EKRANA "Düzenleme yapmak ister misiniz? (evet/hayır)" YAZ
    CEVAP ← GİRİŞ

    EĞER (CEVAP == "evet") İSE
        EKRANA "Silmek istediğiniz ürün numarasını girin:" YAZ
        SİL_NUM ← GİRİŞ
        SEPETTEN_SİL(SEPET, SİL_NUM)
    DEĞİLSE
        ÇIK
    BİTİŞ_EĞER
SON_TEKRARLA (CEVAP == "hayır" olana kadar)


// --- 6. İndirim Kodu Uygulama ---
EKRANA "İndirim kodunuz var mı? (evet/hayır)" YAZ
KOD_VAR ← GİRİŞ

EĞER (KOD_VAR == "evet") İSE
    EKRANA "İndirim kodunu girin:" YAZ
    KOD ← GİRİŞ
    EĞER (KOD == "INDIRIM10") İSE
        İNDİRİM ← 0.10 * SEPET_TOPLAM_TUTAR(SEPET)
    DEĞİLSE
        İNDİRİM ← 0
        EKRANA "Geçersiz kod." YAZ
    BİTİŞ_EĞER
DEĞİLSE
    İNDİRİM ← 0
BİTİŞ_EĞER

TOPLAM_TUTAR ← SEPET_TOPLAM_TUTAR(SEPET) - İNDİRİM


// --- 7. Minimum 50 TL Kontrolü ---
EĞER (TOPLAM_TUTAR < 50) İSE
    EKRANA "Minimum sipariş tutarı 50 TL olmalıdır." YAZ
    PROGRAMI SONLANDIR
BİTİŞ_EĞER


// --- 8. Kargo Ücreti Hesaplama ---
EĞER (TOPLAM_TUTAR >= 200) İSE
    KARGO ← 0
    EKRANA "200 TL üzeri ücretsiz kargo!" YAZ
DEĞİLSE
    KARGO ← 25
    EKRANA "Kargo ücreti: 25 TL" YAZ
BİTİŞ_EĞER

GENEL_TOPLAM ← TOPLAM_TUTAR + KARGO


// --- 9. Ödeme Yöntemi Seçimi ---
EKRANA "Ödeme yöntemi seçin: (1) Kredi Kartı (2) Havale (3) Kapıda Ödeme" YAZ
ODEME_SECIM ← GİRİŞ

EĞER (ODEME_SECIM == 1) İSE
    ODEME_YONTEMI ← "Kredi Kartı"
EĞER (ODEME_SECIM == 2) İSE
    ODEME_YONTEMI ← "Havale"
EĞER (ODEME_SECIM == 3) İSE
    ODEME_YONTEMI ← "Kapıda Ödeme"
BİTİŞ_EĞERLER


// --- 10. Sipariş Onayı ---
EKRANA "Siparişi onaylıyor musunuz? (evet/hayır)" YAZ
ONAY ← GİRİŞ

EĞER (ONAY == "evet") İSE
    EKRANA "Siparişiniz alınmıştır." YAZ
    EKRANA "Toplam Tutar: ", GENEL_TOPLAM, " TL" YAZ
    EKRANA "Ödeme Yöntemi: ", ODEME_YONTEMI YAZ
DEĞİLSE
    EKRANA "Sipariş iptal edildi." YAZ
BİTİŞ_EĞER

BİTİR
