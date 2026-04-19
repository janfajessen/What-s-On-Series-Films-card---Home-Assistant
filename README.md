<div align="center">

# What's On Series & Films Card
## Stream and Cinema Guide <br> Home Assistant Lovelace Card

..........   <img src="brands/logo@2x.png" width="550"/>

![Version](https://img.shields.io/badge/version-1.5.24-blue?style=for-the-badge)
![HA](https://img.shields.io/badge/Home%20Assistant-2024.1+-orange?style=for-the-badge&logo=home-assistant)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3.12+-3776AB?style=for-the-badge&logo=python)
![HACS](https://img.shields.io/badge/HACS-Custom-41BDF5?style=for-the-badge&logo=homeassistantcommunitystore&logoColor=white)
[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-Donate-yellow?style=for-the-badge&logo=buymeacoffee)](https://www.buymeacoffee.com/janfajessen)
[![Patreon](https://img.shields.io/badge/Patreon-Support-red?style=for-the-badge&logo=patreon)](https://www.patreon.com/janfajessen)
<!--[![Ko-Fi](https://img.shields.io/badge/Ko--Fi-Support-teal?style=for-the-badge&logo=ko-fi)](https://ko-fi.com/janfajessen)
[![GitHub Sponsors](https://img.shields.io/badge/GitHub%20Sponsors-Support-pink?style=for-the-badge&logo=githubsponsors)](https://github.com/sponsors/janfajessen)


[![PayPal](https://img.shields.io/badge/PayPal-Donate-blue?style=for-the-badge&logo=paypal)](https://paypal.me/janfajessen)-->


<sub>
Qué hacen en streaming y en el cine - ما الذي يعرض في البث المباشر والسينما - Nə var streaming və kinoda - Što rade na streaming-u i u kinu - Какво правят в стрийминг и кино - Što rade na streaming-u i u kinu - Què fan en streaming i al cinema - Co dělají na streamování a v kině - Hvad laver de på streaming og i biografen - Was machen sie beim Streaming und im Kino - Τι κάνουν στο streaming και στον κινηματογράφο - Qué hacen en streaming y en el cine - Quoi qu'ils font en streaming et au cinéma - Que fan en streaming e no cine - מה הם עושים בסטרימינג ובקולנוע - स्ट्रीमिंग और सिनेमा में क्या करते हैं - Mit csinálnak a streamingben és a moziban - Zer egiten dute streaming-ean eta zinean - در استریم و سینما چه کار می‌کنند - Mitä he tekevät suoratoistossa ja elokuvissa - Apa yang mereka lakukan di streaming dan bioskop - Cosa fanno in streaming e al cinema - Что делают в стриминге и кино - Co robią w streamingu i kinie - O que eles fazem no streaming e no cinema - Ce fac în streaming și la cinema - Що роблять у стрімінгу та кіно - Vad gör de på streaming och bio - Dizilerde ve filmlerde ne yapıyorlar - 流媒体和电影院在放什么
</sub>

</div>

---

A Lovelace card for the [What's On Series & Films](https://github.com/janfajessen/What-s-On-Series-Films---Stream-Cinema-Guide) Home Assistant integration. Shows movies in theaters, new releases on streaming platforms, and tracks your followed TV series via TVmaze — all in one beautiful scrollable card.

![Card Preview](preview.png)

---

## ✨ Features

- 🎬 **Cinema** — Now playing & upcoming releases in your country
- 📡 **Streaming platforms** — New movies and series/docs grouped by platform (Netflix, Amazon, Disney+, etc.)
- 📺 **TVmaze Following** — Track your series with poster, channel, rating, last/next episode
- 🌍 **49 languages** — Auto-detects your HA language
- 🎨 **Fully customizable** — Dark/light theme, 8 accent color presets + custom HEX/RGB
- ⚙️ **Visual editor** — Add/remove specific sensors, no YAML needed
- 🖱️ **Smooth horizontal scroll** — Mouse wheel and touch support
- 💡 **Auto-discovery** — Finds all `sensor.whatson_*` sensors automatically

---

## 📦 Installation

### Via HACS (recommended)
1. Open HACS → Frontend → **+ Explore & Download Repositories**
2. Search for **What's On TV Series & Films Card**
3. Download and restart HA

### Manual
1. Copy `whatson-series-films-card.js` to `/config/www/`
2. Go to **Settings → Dashboards → Resources**
3. Add `/local/whatson-series-films-card.js` as **JavaScript Module**
4. Copy `whatson_tv_icon.png` to `/config/www/`

---

## 🚀 Usage

### Minimal (auto-discover everything)
```yaml
type: custom:whatson-series-films-card
```

### With options
```yaml
type: custom:whatson-series-films-card
title: "What's On"
theme: dark        # dark | light
accent: "#e8872a"  # any hex color
```

### Specific sensors only
```yaml
type: custom:whatson-series-films-card
entities:
  - sensor.whatson_series_films_es_cinema_now_playing
  - sensor.whatson_series_films_es_new_movies_on_netflix_standard_with_ads
  - sensor.whatson_series_films_lupin_status
```

---

## ⚙️ Configuration options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `title` | string | *(auto from HA language)* | Card title |
| `theme` | `dark`\|`light` | `dark` | Color theme |
| `accent` | string | `#e8872a` | Accent color (HEX or `R,G,B`) |
| `entities` | list | *(auto-discover)* | Specific sensors to display |

---

## 🔧 Automations & Scripts

### Notify when a new movie appears in theaters
```yaml
alias: "What's On — New cinema release"
trigger:
  - platform: template
    value_template: >
      {{ state_attr('sensor.whatson_series_films_es_cinema_now_playing', 'movies')
         | selectattr('release_date', 'ge', now().strftime('%Y-%m-%d'))
         | list | count > 0 }}
action:
  - service: notify.telegram_jan
    data:
      message: >
        🎬 New in theaters today:
        {% for m in state_attr('sensor.whatson_series_films_es_cinema_now_playing', 'movies')
           if m.release_date == now().strftime('%Y-%m-%d') %}
        • {{ m.title }} {% if m.vote_average > 0 %}(⭐{{ m.vote_average }}){% endif %}
        {% endfor %}
```

### Notify when a followed series has a new episode
```yaml
alias: "What's On — New episode available"
trigger:
  - platform: state
    entity_id: sensor.whatson_series_films_lupin_next_episode
    from: "No upcoming episode"
action:
  - service: notify.telegram_jan
    data:
      message: >
        📺 New episode of **Lupin**!
        {{ states('sensor.whatson_series_films_lupin_next_episode') }}
```

### Send weekly streaming highlights to Telegram with poster
```yaml
alias: "What's On — Weekly highlights"
trigger:
  - platform: time
    at: "09:00:00"
  - platform: template
    value_template: "{{ now().weekday() == 4 }}"  # Friday
action:
  - variables:
      movies: "{{ state_attr('sensor.whatson_series_films_es_new_movies_on_netflix_standard_with_ads', 'movies') }}"
  - service: telegram_bot.send_message
    data:
      target:
        - "{{ states('input_text.telegram_chat_jan') }}"
      message: >
        🎬 *New on Netflix this week:*
        {% for m in movies[:5] %}
        • {{ m.title }}{% if m.vote_average > 0 %} ⭐{{ m.vote_average }}{% endif %}
        {% endfor %}
```

### Display now-playing count as a sensor badge
```yaml
template:
  - sensor:
      - name: "Cinema now playing count"
        state: >
          {{ state_attr('sensor.whatson_series_films_es_cinema_now_playing', 'movies') | count }}
        icon: mdi:ticket-confirmation-outline
```

---

## 🗺️ Available countries


### 🎬 Cinema (TMDB Now Playing / Upcoming)



<table border="0" cellspacing="0" cellpadding="6" width="100%">
<tr>
<td width="20%" align="left"><sub>🇪🇸 España / Espanya / Espainia</sub></td>
<td width="20%" align="left"><sub>🇦🇩 Andorra</sub></td>
<td width="20%" align="left"><sub>🇦🇱 Shqipëria *</sub></td>
<td width="20%" align="left"><sub>🇦🇷 Argentina</sub></td>
<td width="20%" align="left"><sub>🇦🇲 Հայաստան *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇦🇺 Australia</sub></td>
<td width="20%" align="left"><sub>🇦🇹 Österreich</sub></td>
<td width="20%" align="left"><sub>🇦🇼 Aruba *</sub></td>
<td width="20%" align="left"><sub>🇸🇦 المملكة العربية السعودية</sub></td>
<td width="20%" align="left"><sub>🇧🇾 Беларусь *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇧🇪 België / Belgique / Belgien</sub></td>
<td width="20%" align="left"><sub>🇧🇴 Bolivia *</sub></td>
<td width="20%" align="left"><sub>🇧🇦 Bosna i Hercegovina / Босна и Херцеговина *</sub></td>
<td width="20%" align="left"><sub>🇧🇷 Brasil</sub></td>
<td width="20%" align="left"><sub>🇧🇬 България *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇨🇦 Canada</sub></td>
<td width="20%" align="left"><sub>🏝️ Caribbean *</sub></td>
<td width="20%" align="left"><sub>🇨🇱 Chile</sub></td>
<td width="20%" align="left"><sub>🇨🇳 中国 *</sub></td>
<td width="20%" align="left"><sub>🇨🇴 Colombia</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇨🇷 Costa Rica *</sub></td>
<td width="20%" align="left"><sub>🇨🇮 Côte d'Ivoire *</sub></td>
<td width="20%" align="left"><sub>🇭🇷 Hrvatska *</sub></td>
<td width="20%" align="left"><sub>🇨🇿 Česká republika</sub></td>
<td width="20%" align="left"><sub>🇨🇾 Κύπρος / Kıbrıs *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇩🇰 Danmark</sub></td>
<td width="20%" align="left"><sub>🇩🇴 Rep. Dominicana *</sub></td>
<td width="20%" align="left"><sub>🇪🇨 Ecuador *</sub></td>
<td width="20%" align="left"><sub>🇪🇬 مصر *</sub></td>
<td width="20%" align="left"><sub>🇸🇻 El Salvador *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇪🇪 Eesti *</sub></td>
<td width="20%" align="left"><sub>🇫🇴 Færøerne / Føroyar *</sub></td>
<td width="20%" align="left"><sub>🇫🇮 Suomi / Finland</sub></td>
<td width="20%" align="left"><sub>🇫🇷 France</sub></td>
<td width="20%" align="left"><sub>🇬🇪 საქართველო *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇩🇪 Deutschland</sub></td>
<td width="20%" align="left"><sub>🇬🇭 Ghana *</sub></td>
<td width="20%" align="left"><sub>🇬🇷 Ελλάδα</sub></td>
<td width="20%" align="left"><sub>🇬🇹 Guatemala *</sub></td>
<td width="20%" align="left"><sub>🇭🇳 Honduras *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇭🇰 Hong Kong / 香港</sub></td>
<td width="20%" align="left"><sub>🇭🇺 Magyarország</sub></td>
<td width="20%" align="left"><sub>🇮🇪 Ireland / Éire</sub></td>
<td width="20%" align="left"><sub>🇮🇸 Ísland *</sub></td>
<td width="20%" align="left"><sub>🇮🇳 Bharat भारत</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇮🇩 Indonesia</sub></td>
<td width="20%" align="left"><sub>🇮🇱 יִשְׂרָאֵל</sub></td>
<td width="20%" align="left"><sub>🇮🇹 Italia</sub></td>
<td width="20%" align="left"><sub>🇯🇲 Jamaica *</sub></td>
<td width="20%" align="left"><sub>🇯🇵 日本</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇰🇿 Қазақстан *</sub></td>
<td width="20%" align="left"><sub>🇰🇪 Kenya *</sub></td>
<td width="20%" align="left"><sub>🇱🇻 Latvija *</sub></td>
<td width="20%" align="left"><sub>🇱🇧 لبنان *</sub></td>
<td width="20%" align="left"><sub>🇱🇮 Liechtenstein *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇱🇾 Libya / ليبيا *</sub></td>
<td width="20%" align="left"><sub>🇱🇹 Lietuva *</sub></td>
<td width="20%" align="left"><sub>🇱🇺 Lëtzebuerg / Luxembourg / Luxemburg *</sub></td>
<td width="20%" align="left"><sub>🇲🇴 Macao / 澳門 *</sub></td>
<td width="20%" align="left"><sub>🇲🇬 Madagasikara *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇲🇼 Malawi *</sub></td>
<td width="20%" align="left"><sub>🇲🇾 Bahasa Melayu *</sub></td>
<td width="20%" align="left"><sub>🇲🇹 Malta *</sub></td>
<td width="20%" align="left"><sub>🇲🇺 Mauritius *</sub></td>
<td width="20%" align="left"><sub>🇲🇽 México</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇲🇰 Северна Македонија *</sub></td>
<td width="20%" align="left"><sub>🇲🇳 Монгол *</sub></td>
<td width="20%" align="left"><sub>🇲🇪 Crna Gora / Črna Гора *</sub></td>
<td width="20%" align="left"><sub>🇲🇦 المغرب *</sub></td>
<td width="20%" align="left"><sub>🇲🇨 Monaco / Mónegue *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇲🇿 Moçambique *</sub></td>
<td width="20%" align="left"><sub>🇳🇦 Namibia *</sub></td>
<td width="20%" align="left"><sub>🇳🇱 Nederland</sub></td>
<td width="20%" align="left"><sub>🇳🇨 Nouvelle-Calédonie *</sub></td>
<td width="20%" align="left"><sub>🇳🇿 New Zealand / Aotearoa</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇳🇮 Nicaragua *</sub></td>
<td width="20%" align="left"><sub>🇳🇬 Nigeria *</sub></td>
<td width="20%" align="left"><sub>🇳🇴 Norge / Noreg</sub></td>
<td width="20%" align="left"><sub>🇵🇰 Pakistan *</sub></td>
<td width="20%" align="left"><sub>🇵🇦 Panamá *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇵🇾 Paraguay *</sub></td>
<td width="20%" align="left"><sub>🇵🇪 Perú *</sub></td>
<td width="20%" align="left"><sub>🇵🇭 Pilipinas *</sub></td>
<td width="20%" align="left"><sub>🇵🇱 Polska</sub></td>
<td width="20%" align="left"><sub>🇵🇸 فلسطين *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇵🇹 Portugal</sub></td>
<td width="20%" align="left"><sub>🇵🇷 Puerto Rico *</sub></td>
<td width="20%" align="left"><sub>🇶🇦 قطر *</sub></td>
<td width="20%" align="left"><sub>🇷🇴 România</sub></td>
<td width="20%" align="left"><sub>🇷🇺 Россия</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇷🇸 Gora / Црна Гора *</sub></td>
<td width="20%" align="left"><sub>🇸🇲 San Marino *</sub></td>
<td width="20%" align="left"><sub>🏴󠁧󠁢󠁳󠁣󠁴󠁿 Scotland *</sub></td>
<td width="20%" align="left"><sub>🇸🇬 Singapore / 新加坡 / சிங்கப்பூர் / Singapura</sub></td>
<td width="20%" align="left"><sub>🇸🇰 Slovensko *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇸🇮 Slovenija *</sub></td>
<td width="20%" align="left"><sub>🇿🇦 South Africa *</sub></td>
<td width="20%" align="left"><sub>🇰🇷 대한민국</sub></td>
<td width="20%" align="left"><sub>🇸🇪 Sverige</sub></td>
<td width="20%" align="left"><sub>🇨🇭 Schweiz / Suisse / Svizzera / Svizra</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇹🇼 Taiwan / 臺灣</sub></td>
<td width="20%" align="left"><sub>🇹🇭 ประเทศไทย</sub></td>
<td width="20%" align="left"><sub>🇹🇷 Türkiye</sub></td>
<td width="20%" align="left"><sub>🇺🇬 Uganda *</sub></td>
<td width="20%" align="left"><sub>🇺🇦 Украïна *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇦🇪 Al-Imarat *</sub></td>
<td width="20%" align="left"><sub>🇬🇧 United Kingdom / Cymru / Alba</sub></td>
<td width="20%" align="left"><sub>🇺🇸 United States</sub></td>
<td width="20%" align="left"><sub>🇺🇾 Uruguay *</sub></td>
<td width="20%" align="left"><sub>🇺🇿 Oʻzbekiston *</sub></td>
</tr>
<tr>
<td width="20%" align="left"><sub>🇻🇦 Città del Vaticano *</sub></td>
<td width="20%" align="left"><sub>🇻🇪 Venezuela</sub></td>
<td width="20%" align="left"><sub>🇻🇳 Việt Nam *</sub></td>
<td width="20%" align="left"><sub>🇿🇲 Zambia *</sub></td>
<td width="20%" align="left"><sub>🇿🇼 Zimbabwe *</sub></td>
</tr>
</table>...
* 🌍 Only worldwide platforms




### 📡 Streaming platforms availability

Availability varies by country. The most common platforms supported:

| Platform | Main countries |
|----------|---------------|
| Netflix | 🌍 Worldwide (190+ countries) |
| Amazon Prime Video | 🌍 Worldwide (200+ countries) |
| Disney+ | 🇺🇸🇬🇧🇪🇸🇫🇷🇩🇪🇮🇹🇦🇺🇨🇦🇯🇵 + more |
| HBO Max / Max | 🇺🇸🇬🇧🇪🇸🇵🇹🇳🇱🇸🇪🇩🇰🇳🇴🇫🇮 + more |
| Apple TV+ | 🌍 Worldwide |
| Hulu | 🇺🇸 |
| Peacock | 🇺🇸 |
| Paramount+ | 🇺🇸🇬🇧🇪🇸🇫🇷🇩🇪🇮🇹🇦🇺🇨🇦 + more |
| Movistar+ | 🇪🇸 |
|3Cat | 🇪🇸 <sub>(Catalunya)</sub> |
| RTVE Play | 🇪🇸 |
| Atresplayer | 🇪🇸 |

---

## 📋 Requirements

- Home Assistant 2024.1+
- [What's On Series & Films](https://github.com/janfajessen/What-s-On-Series-Films---Stream-Cinema-Guide) installed
- TMDB API key (free at [themoviedb.org](https://www.themoviedb.org/settings/api))
- TVmaze API key (optional, for series tracking)

---

## 🤝 Related cards

- [What's On TV EPG Card](https://github.com/janfajessen/What-s-On-TV-EPG-TV-Guide-Card) — Full TV guide / EPG
- [What's On TV Notify Card](https://github.com/janfajessen/whatsontv-notify-card) — Search & notifications

---

## 📄 License

MIT License — © janfajessen
