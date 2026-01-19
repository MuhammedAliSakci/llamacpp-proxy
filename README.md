# 🦙 llama.cpp Proxy Nedir?

**llama.cpp Proxy**, çalışan bir **llama.cpp sunucusunu**, **OpenAI API ile aynı şekilde** kullanabilmeni sağlayan bir **ters proxy (aracı sunucu)** yazılımıdır.

Yani:

* Normalde OpenAI API nasıl kullanılıyorsa
* Aynı kodları kullanarak
* Kendi bilgisayarındaki **llama.cpp modelini** kullanabilirsin

👉 OpenAI’ye bağlı kalmadan, **yerel (local) LLM** çalıştırmış olursun.

---

## 🚀 Ne İşe Yarar?

Bu proxy sayesinde llama.cpp sunucun:

* `/v1/completions`
* `/v1/chat/completions`

gibi **OpenAI ile birebir aynı endpoint’leri** destekler.

Yani:

* `openai.ChatCompletion.create()`
* `openai.Completion.create()`

kodları **hiç değişmeden** çalışır.

---

## ✨ Özellikler

Bu proje şunları destekler:

* ✅ **OpenAI API uyumlu endpoint’ler**
* 🧩 **Özelleştirilebilir chat template (Jinja2 ile)**
* 🔐 **API key doğrulama**
* ⏱️ **Rate limit (istek sınırı)**
* 📡 **Streaming (parça parça cevap)**
* 📐 **Grammar constraint**
  (llama.cpp’nin belirli formatta cevap verme özelliği)

---

## 🧱 Gereksinimler

Başlamadan önce şunlar gerekli:

* 🐍 **Python 3.11 veya üstü**
* 🦙 **Çalışan bir llama.cpp server**

---

## 📦 Kurulum

Proje klasörüne girip şunu çalıştır:

```bash
pip install -e .
```

> `-e` parametresi geliştirme (editable) modu içindir.

---

## ⚙️ Ayarlar (Environment Variables)

API güvenliği için **en az 1 tane API key tanımlamalısın**:

```bash
# Limitsiz API anahtarı (rate limit yok)
UNLIMITED_API_KEY=senin-limitsiz-api-keyin

# Limitli API anahtarı (rate limit var)
LIMITED_API_KEY=senin-limitli-api-keyin
```

---

## ▶️ Sunucuyu Çalıştırma

### 1️⃣ Komut satırı ile:

```bash
llamacpp-proxy-server \
  --llamacpp-server http://localhost:8080 \
  --chat-template-jinja path/to/template.jinja
```

### 2️⃣ Python modülü olarak:

```bash
python -m llamacpp_proxy.main \
  --llamacpp-server http://localhost:8080 \
  --chat-template-jinja path/to/template.jinja
```

---

## 🔧 Önemli Parametreler

| Parametre                   | Açıklama                                         |
| --------------------------- | ------------------------------------------------ |
| `--host`                    | Sunucunun dinleyeceği IP (varsayılan: `0.0.0.0`) |
| `--port`                    | Proxy portu (varsayılan: `8000`)                 |
| `--llamacpp-server`         | llama.cpp server adresi                          |
| `--chat-template-jinja`     | Chat format şablonu                              |
| `--rate-limit-window`       | Rate limit süresi (saniye)                       |
| `--rate-limit-max-requests` | Süre başına max istek                            |

---

## 🧪 API Nasıl Kullanılır?

### Python ile OpenAI gibi kullanma 👇

```python
import openai

openai.api_key = "senin-api-keyin"
openai.api_base = "http://localhost:8000/v1"

# Chat Completion
response = openai.ChatCompletion.create(
    model="model-adi",
    messages=[
        {"role": "user", "content": "Merhaba!"}
    ]
)

# Text Completion
response = openai.Completion.create(
    model="model-adi",
    prompt="Bir zamanlar",
    max_tokens=100
)
```

👉 Kod **OpenAI ile birebir aynı**, sadece `api_base` değişiyor.

---

## 🧩 Chat Template (Jinja2)

llama.cpp modelleri farklı chat formatları kullanır.
Bu yüzden **Jinja2 template** ile mesajları biçimlendiriyoruz.

### Örnek Template:

```jinja
{%- if messages[0]['role'] == 'system' %}
 {%- set system_message = messages[0]['content'] %}
 {%- set loop_messages = messages[1:] %}
{%- else %}
 {%- set loop_messages = messages %}
{%- endif %}

{%- for message in loop_messages %}
 {%- if message['role'] == 'user' %}
 {{- '[INST] ' + message['content'] + ' [/INST]' }}
 {%- elif message['role'] == 'assistant' %}
 {{- ' ' + message['content'] + eos_token}}
 {%- endif %}
{%- endfor %}
```

📌 Bu template:

* User mesajlarını `[INST]` içine alır
* Assistant cevaplarını düzgün kapatır
* llama.cpp’nin beklediği formatı üretir

---

## 🛠️ Geliştirme (Developer Modu)

### 1️⃣ Test bağımlılıkları:

```bash
pip install -e ".[test]"
```

### 2️⃣ Testleri çalıştır:

```bash
pytest
```

### 3️⃣ Kod kapsama raporu:

```bash
pytest --cov --cov-report=html
```

---

## 📄 Lisans

Bu proje **Apache License 2.0** ile lisanslanmıştır.

🔗 Detaylar için:

* `LICENSE` dosyasına
* veya [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0) adresine bakabilirsin.

---

## 🎯 Kısaca Özet

✔ Kendi LLM’ini çalıştır
✔ OpenAI API gibi kullan
✔ Yerel, hızlı ve kontrol sende
✔ Cybersecurity / AI lab ortamları için ideal

İstersen bir sonraki adımda:

* **Gerçek kurulum senaryosu**
* **Docker ile kullanım**
* **Cybersecurity projelerinde kullanım**
* **Burp Suite + LLM entegrasyonu**

anlatabilirim 🔥
