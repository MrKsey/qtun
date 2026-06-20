# qtun

[![Build Release](https://github.com/MrKsey/qtun/actions/workflows/build-release.yml/badge.svg)](https://github.com/MrKsey/qtun/actions/workflows/build-release.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**English** | [Русский](#qtun-русский)

---

## 📖 Description

**qtun** is a high-performance SIP003 plugin for Shadowsocks that implements the IETF-QUIC protocol. It provides a secure, efficient tunneling solution with excellent congestion control and low resource consumption.

QUIC (Quick UDP Internet Connections) is a modern transport protocol developed by Google and standardized by the IETF. It offers several advantages over traditional TCP-based connections:

- **Reduced latency** through connection multiplexing without head-of-line blocking
- **Improved security** with built-in TLS 1.3 encryption
- **Better congestion control** using modern algorithms like BBR
- **Connection migration** support for mobile networks

## ✨ Major Features

- **IETF-QUIC Protocol** - Full implementation of the standardized QUIC protocol
- **ACME Compatible** - Automatic certificate management with Let's Encrypt and other ACME providers
- **BBR Congestion Control** - Advanced congestion control algorithm for optimal throughput
- **Low Resource Usage** - Written in Rust for memory safety and efficiency
- **Cross-Platform** - Pre-built binaries for Windows, macOS, Linux (x86, x86_64, ARM, ARM64, MIPS)
- **SIP003 Plugin** - Seamless integration with Shadowsocks clients and servers

## 📦 Installation

### From Pre-built Binaries

Download the latest release from the [Releases page](https://github.com/MrKsey/qtun/releases) for your platform.

### From Source

```bash
# Install Rust toolchain
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Install qtun from GitHub
cargo install --git https://github.com/MrKsey/qtun
```

### Build Requirements

- Rust 1.70 or later
- For cross-compilation: `cross` (`cargo install cross`)

## 🔧 Configuration

### Issue a TLS/QUIC Certificate

qtun looks for TLS certificates managed by [acme.sh](https://github.com/acmesh-official/acme.sh) by default. Here's an example using CloudFlare DNS:

```bash
# Install acme.sh
curl https://get.acme.sh | sh

# Issue certificate for your domain
~/.acme.sh/acme.sh --issue --dns dns_cf -d mydomain.me
```

For other DNS providers, refer to the [acme.sh wiki](https://github.com/acmesh-official/acme.sh/wiki).

### Server Configuration

Run shadowsocks server with qtun plugin:

```bash
ssserver -s 0.0.0.0:443 -k your_password -m aes-256-gcm \
    --plugin ./qtun-server \
    --plugin-opts "acme_host=example.com"
```

**Server Options:**
| Option | Description |
|--------|-------------|
| `acme_host` | Domain name for ACME certificate |
| `host` | Custom host for TLS SNI |
| `port` | Custom listening port (default: 443) |

### Client Configuration

Run shadowsocks client with qtun plugin:

```bash
sslocal -s example.com:443 -k your_password -m aes-256-gcm \
    --plugin ./qtun-client \
    --plugin-opts "host=example.com"
```

**Client Options:**
| Option | Description |
|--------|-------------|
| `host` | Server hostname for verification |
| `port` | Server port (default: 443) |
| `insecure` | Skip certificate verification (not recommended) |

## 📚 Usage Examples

### Basic Setup

1. **Server side** (VPS with public IP):
```bash
./qtun-server --acme_host your-domain.com
```

2. **Client side** (local machine):
```bash
./qtun-client --host your-domain.com
```

### Shadowsocks Integration

**config.json (client):**
```json
{
    "server": "your-domain.com",
    "server_port": 443,
    "password": "your_password",
    "method": "aes-256-gcm",
    "plugin": "qtun-client",
    "plugin_opts": "host=your-domain.com"
}
```

### Advanced: Custom Certificate

If you have existing certificates:

```bash
# Server with custom cert
./qtun-server --cert /path/to/cert.pem --key /path/to/key.pem
```

## 🏗️ Architecture

```
┌─────────────┐     QUIC      ┌─────────────┐     TCP     ┌─────────────┐
│   Client    │◄─────────────►│  qtun-client│◄───────────►│   Internet  │
│  Application│               │  (Plugin)   │             │             │
└─────────────┘               └─────────────┘             └─────────────┘
                                      │
                                      │ QUIC (UDP/443)
                                      ▼
┌─────────────┐     TCP     ┌─────────────┐     QUIC      ┌─────────────┐
│   Service   │◄────────────►│ qtun-server│◄─────────────►│   Network   │
│   Backend   │               │  (Plugin)  │             │             │
└─────────────┘               └─────────────┘             └─────────────┘
```

## 🔍 Troubleshooting

### Common Issues

**Certificate validation fails:**
- Ensure the domain resolves to your server IP
- Check that port 443 (UDP) is open in firewall
- Verify acme.sh has issued the certificate correctly

**Connection timeout:**
- Check server is listening: `netstat -ulnp | grep qtun`
- Verify firewall allows UDP traffic on the specified port
- Test basic connectivity: `ping your-domain.com`

**High latency:**
- Try different congestion control algorithms
- Check network path with `mtr` or `traceroute`
- Consider using a closer server location

## 📊 Performance

qtun is optimized for:
- **Low latency** connections
- **High throughput** data transfer
- **Minimal CPU** and memory footprint
- **Stable** connections on unreliable networks

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

The MIT License (MIT)

Copyright (c) 2020 Max Lv
Copyright (c) 2024 MrKsey

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## 🔗 Links

- [Shadowsocks SIP003 Plugin Spec](https://shadowsocks.org/doc/sip003.html)
- [IETF QUIC RFC 9000](https://datatracker.ietf.org/doc/html/rfc9000)
- [acme.sh - ACME client](https://github.com/acmesh-official/acme.sh)
- [Rust Programming Language](https://www.rust-lang.org/)

---

# qtun (Русский)

[English](#qtun) | **Русский**

---

## 📖 Описание

**qtun** — это высокопроизводительный SIP003 плагин для Shadowsocks, реализующий протокол IETF-QUIC. Он обеспечивает безопасное туннелирование с отличным контролем перегрузок и низким потреблением ресурсов.

QUIC (Quick UDP Internet Connections) — современный транспортный протокол, разработанный Google и стандартизированный IETF. Он предлагает несколько преимуществ перед традиционными TCP-соединениями:

- **Сниженная задержка** благодаря мультиплексированию соединений без блокировки начала очереди
- **Улучшенная безопасность** со встроенным шифрованием TLS 1.3
- **Лучший контроль перегрузок** с использованием современных алгоритмов, таких как BBR
- **Миграция соединений** для мобильных сетей

## ✨ Основные возможности

- **Протокол IETF-QUIC** — Полная реализация стандартизированного протокола QUIC
- **Совместимость с ACME** — Автоматическое управление сертификатами с Let's Encrypt и другими ACME-провайдерами
- **Контроль перегрузок BBR** — Продвинутый алгоритм для оптимальной пропускной способности
- **Низкое потребление ресурсов** — Написан на Rust для безопасности памяти и эффективности
- **Кроссплатформенность** — Готовые бинарники для Windows, macOS, Linux (x86, x86_64, ARM, ARM64, MIPS)
- **SIP003 плагин** — Бесшовная интеграция с клиентами и серверами Shadowsocks

## 📦 Установка

### Из готовых бинарников

Скачайте последнюю версию со страницы [Releases](https://github.com/MrKsey/qtun/releases) для вашей платформы.

### Из исходного кода

```bash
# Установка инструментария Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Установка qtun из GitHub
cargo install --git https://github.com/MrKsey/qtun
```

### Требования для сборки

- Rust 1.70 или новее
- Для кросс-компиляции: `cross` (`cargo install cross`)

## 🔧 Настройка

### Получение TLS/QUIC сертификата

qtun по умолчанию использует сертификаты, управляемые [acme.sh](https://github.com/acmesh-official/acme.sh). Пример с DNS CloudFlare:

```bash
# Установка acme.sh
curl https://get.acme.sh | sh

# Получение сертификата для домена
~/.acme.sh/acme.sh --issue --dns dns_cf -d mydomain.me
```

Для других DNS-провайдеров обратитесь к [wiki acme.sh](https://github.com/acmesh-official/acme.sh/wiki).

### Настройка сервера

Запуск сервера shadowsocks с плагином qtun:

```bash
ssserver -s 0.0.0.0:443 -k ваш_пароль -m aes-256-gcm \
    --plugin ./qtun-server \
    --plugin-opts "acme_host=example.com"
```

**Опции сервера:**
| Опция | Описание |
|-------|----------|
| `acme_host` | Доменное имя для ACME сертификата |
| `host` | Пользовательский хост для TLS SNI |
| `port` | Пользовательский порт прослушивания (по умолчанию: 443) |

### Настройка клиента

Запуск клиента shadowsocks с плагином qtun:

```bash
sslocal -s example.com:443 -k ваш_пароль -m aes-256-gcm \
    --plugin ./qtun-client \
    --plugin-opts "host=example.com"
```

**Опции клиента:**
| Опция | Описание |
|-------|----------|
| `host` | Имя хоста сервера для проверки |
| `port` | Порт сервера (по умолчанию: 443) |
| `insecure` | Пропустить проверку сертификата (не рекомендуется) |

## 📚 Примеры использования

### Базовая настройка

1. **На стороне сервера** (VPS с публичным IP):
```bash
./qtun-server --acme_host ваш-домен.com
```

2. **На стороне клиента** (локальная машина):
```bash
./qtun-client --host ваш-домен.com
```

### Интеграция с Shadowsocks

**config.json (клиент):**
```json
{
    "server": "ваш-домен.com",
    "server_port": 443,
    "password": "ваш_пароль",
    "method": "aes-256-gcm",
    "plugin": "qtun-client",
    "plugin_opts": "host=ваш-домен.com"
}
```

### Продвинутое: Пользовательский сертификат

Если у вас есть существующие сертификаты:

```bash
# Сервер с пользовательским сертификатом
./qtun-server --cert /путь/к/cert.pem --key /путь/к/key.pem
```

## 🏗️ Архитектура

```
┌─────────────┐     QUIC      ┌─────────────┐     TCP     ┌─────────────┐
│   Клиент    │◄─────────────►│ qtun-client│◄───────────►│  Интернет   │
│  Приложение │               │  (Плагин)   │             │             │
└─────────────┘               └─────────────┘             └─────────────┘
                                      │
                                      │ QUIC (UDP/443)
                                      ▼
┌─────────────┐     TCP     ┌─────────────┐     QUIC      ┌─────────────┐
│   Сервис    │◄────────────►│ qtun-server│◄─────────────►│   Сеть      │
│   Бэкенд    │               │  (Плагин)  │             │             │
└─────────────┘               └─────────────┘             └─────────────┘
```

## 🔍 Решение проблем

### Частые проблемы

**Ошибка проверки сертификата:**
- Убедитесь, что домен разрешается в IP вашего сервера
- Проверьте, что порт 443 (UDP) открыт в брандмауэре
- Убедитесь, что acme.sh правильно выпустил сертификат

**Таймаут соединения:**
- Проверьте, что сервер слушает: `netstat -ulnp | grep qtun`
- Убедитесь, что брандмауэр разрешает UDP-трафик на указанном порту
- Проверьте базовую связность: `ping ваш-домен.com`

**Высокая задержка:**
- Попробуйте разные алгоритмы контроля перегрузок
- Проверьте сетевой путь с помощью `mtr` или `traceroute`
- Рассмотрите использование более близкого расположения сервера

## 📊 Производительность

qtun оптимизирован для:
- Соединений с **низкой задержкой**
- Передачи данных с **высокой пропускной способностью**
- **Минимального потребления** CPU и памяти
- **Стабильных** соединений в ненадёжных сетях

## 🤝 Вклад в проект

Вклады приветствуются! Не стесняйтесь отправлять Pull Request.

1. Форкните репозиторий
2. Создайте ветку для вашей функции (`git checkout -b feature/amazing-feature`)
3. Закоммитьте изменения (`git commit -m 'Add amazing feature'`)
4. Отправьте в ветку (`git push origin feature/amazing-feature`)
5. Откройте Pull Request

## 📄 Лицензия

Лицензия MIT (MIT)

Copyright (c) 2020 Max Lv  
Copyright (c) 2024 MrKsey

Данная лицензия разрешает лицам, получившим копию данного программного обеспечения и сопутствующей документации (в дальнейшем именуемыми «Программное Обеспечение»), безвозмездно использовать Программное Обеспечение без ограничений, включая право на использование, копирование, изменение, слияние, публикацию, распространение, сублицензирование и/или продажу копий Программного Обеспечения, а также лицам, которым предоставляется данное Программное Обеспечение, при соблюдении следующих условий:

Вышеупомянутый уведомление об авторском праве и данное уведомление о разрешении должны быть включены во все копии или существенные части Программного Обеспечения.

ПРОГРАММНОЕ ОБЕСПЕЧЕНИЕ ПРЕДОСТАВЛЯЕТСЯ «КАК ЕСТЬ», БЕЗ КАКИХ-ЛИБО ГАРАНТИЙ, ЯВНЫХ ИЛИ ПОДРАЗУМЕВАЕМЫХ, ВКЛЮЧАЯ, НО НЕ ОГРАНИЧИВАЯСЬ ГАРАНТИЯМИ ТОВАРНОЙ ПРИГОДНОСТИ, СООТВЕТСТВИЯ ПО ЕГО КОНКРЕТНОМУ НАЗНАЧЕНИЮ И ОТСУТСТВИЯ НАРУШЕНИЙ. НИ В КОЕМ СЛУЧАЕ АВТОРЫ ИЛИ ПРАВООБЛАДАТЕЛИ НЕ НЕСУТ ОТВЕТСТВЕННОСТИ ПО ИСКАМ О ВОЗМЕЩЕНИИ УЩЕРБА, УБЫТКОВ ИЛИ ДРУГИМ ТРЕБОВАНИЯМ, ПО ДЕЙСТВИЯМ ДОГОВОРА, ДЕЛИКТУ ИЛИ ИНЫМ ОБСТОЯТЕЛЬСТВАМ, ВОЗНИКАЮЩИМ ИЗ ПРОГРАММНОГО ОБЕСПЕЧЕНИЯ ИЛИ В СВЯЗИ С ПРОГРАММНЫМ ОБЕСПЕЧЕНИЕМ, ИСПОЛЬЗОВАНИЕМ ИЛИ ИНЫМИ ДЕЙСТВИЯМИ В ПРОГРАММНОМ ОБЕСПЕЧЕНИИ.

## 🔗 Ссылки

- [Спецификация Shadowsocks SIP003 Plugin](https://shadowsocks.org/doc/sip003.html)
- [IETF QUIC RFC 9000](https://datatracker.ietf.org/doc/html/rfc9000)
- [acme.sh - ACME клиент](https://github.com/acmesh-official/acme.sh)
- [Язык программирования Rust](https://www.rust-lang.org/)
