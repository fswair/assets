### Hakkında

Bu depo, [SunoAI-API](https://github.com/SunoAI-API/Suno-API)'dan çatallanmıştır ve [fswair](https://github.com/fswair) tarafından geliştirilmiş/konuşlanmıştır.

### FastAPI Uygulama Dokümantasyonu

#### Genel Bakış

Bu FastAPI uygulaması, müzik oluşturma, beslemeleri alma ve kullanıcı oturumlarını yönetme gibi çeşitli uç noktalar sağlar. CORS ve oturum ara yazılımı kullanarak çapraz kaynak taleplerini ve kullanıcı oturumlarını güvenli bir şekilde yönetir.

### İçindekiler

- [Genel Bakış](#genel-bakış)
- [Ara Yazılım Konfigürasyonu](#ara-yazılım-konfigürasyonu)
- [Uç Noktalar](#uç-noktalar)
  - [Ana Sayfa (`/`)](#ana-sayfa-)
  - [Müzik Oluştur (`/generate`)](#müzik-oluştur-generate)
  - [Açıklama Modu ile Müzik Oluştur (`/generate/description-mode`)](#açıklama-modu-ile-müzik-oluştur-generatedescription-mode)
  - [Besleme Al (`/feed/{aid}`)](#besleme-al-feedaid)
  - [Şarkı Sözü Oluştur (`/generate/lyrics`)](#şarkı-sözü-oluştur-generatelyrics)
  - [Şarkı Sözlerini Getir (`/lyrics/{lid}`)](#şarkı-sözlerini-getir-lyricslid)
  - [Kredi Bilgilerini Al (`/get_credits`)](#kredi-bilgilerini-al-get_credits)
  - [Kimlik Bilgilerini Sıfırla (`/reset`)](#kimlik-bilgilerini-sıfırla-reset)
  - [Kimlik Bilgilerini Ayarla (GET) (`/setup`)](#kimlik-bilgilerini-ayarla-get-setup)
  - [Kimlik Bilgilerini Ayarla (POST) (`/setup`)](#kimlik-bilgilerini-ayarla-post-setup)

### Ara Yazılım Konfigürasyonu

```python
app.add_middleware(
    SessionMiddleware,
    secret_key=SECRET_KEY,
    same_site="none",
    max_age=86400 * 30,
    https_only=True,
    session_cookie="session"
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

Bu konfigürasyon, oturumları yönetmek için `SessionMiddleware` ve CORS ayarlarını yönetmek için `CORSMiddleware` içerir.

### Uç Noktalar

#### Ana Sayfa (`/`)

- **Yöntem:** `GET`
- **Açıklama:** Hoş geldiniz mesajı ve oturum durumu döner.
- **İstek Parametreleri:** Yok
- **Yanıt:**
  ```json
  {
    "message": "Welcome to Suno API",
    "status": "alive",
    "user": {
      "status": "logged in at {date}" or "not logged in yet",
      "uuid": "{uuid}",
      "session_id": "{session_id}"
    }
  }
  ```

#### Müzik Oluştur (`/generate`)

- **Yöntem:** `POST`
- **Açıklama:** Verilen model parametrelerine göre müzik oluşturur.
- **İstek Gövdesi:**
  ```json
  {
    "model": {
      // schemas.CustomModeGenerateParam'a göre model parametreleri
    }
  }
  ```
- **Yanıt:** Oluşturulan müziği veya hata mesajını döner.

#### Açıklama Modu ile Müzik Oluştur (`/generate/description-mode`)

- **Yöntem:** `POST`
- **Açıklama:** Şarkı açıklamasına göre müzik oluşturur.
- **İstek Gövdesi:**
  ```json
  {
    "model": {
      // schemas.DescriptionModeGenerateParam'a göre model parametreleri
    }
  }
  ```
- **Yanıt:** Oluşturulan müziği veya hata mesajını döner.

#### Besleme Al (`/feed/{aid}`)

- **Yöntem:** `GET`
- **Açıklama:** Belirli bir `aid` için beslemeyi alır.
- **İstek Parametreleri:**
  - `aid` (path parametresi): Alınacak beslemenin `aid`'i.
- **Yanıt:** Besleme verilerini veya hata mesajını döner.

#### Şarkı Sözü Oluştur (`/generate/lyrics`)

- **Yöntem:** `POST`
- **Açıklama:** Verilen prompt'a göre şarkı sözleri oluşturur.
- \*\*İstek Gövdesi
