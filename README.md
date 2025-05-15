# vlees VPN

Два варианта:

* Keenetic
* Openwrt


## Openwrt

Подходящие модели (их много в основном все современные роутеры кроме тех у кого процессор qualcom)\nВот свежая [статья](https://habr.com/ru/articles/842210/) которая поможет выбрать роутер  

Xiaomi Router AX3000T (256mb MediaTek Filogic 820) -- [Видео](https://www.youtube.com/watch?v=Cx7kpqfwJP0&ab_channel=3DHOST) или [Текстовая инструкция](https://3dh.su/posts/370/ustanovka-3dhost-proxy-na-router) отдельный удобный метод прошивки

Остальные роутеры будут шиться иначе, лучше искать видео инструкции под те роутеры которые есть возможность купить, а не покупать и уже потом искать

**ВАЖНЫМ АСПЕКТОМ ЯВЛЯЯЕТЬСЯ ОБЪЁМ ПАМЯТИ НА РОУТЕРЕ 256** mb желательный минимум для v2ray

По настройкам делаем все как на видео за исключением шага с настройкой v2rayA
![Настройки](https://github.com/Rinapy/vpn_bt/raw/main/image.png)


## Keenetic

[mobaxterm](https://mobaxterm.mobatek.net/)

[Видео](https://www.youtube.com/watch?v=rKbENP80ECo&ab_channel=RockBlack)
[Текстовая инструкция](https://rockblack.pro/vpn/dopolnitelno/vless-keenetic)
05 и 03 берём ниже
04 генерируем из vlees ссылки

```json
// 05_routing.json
{
  "routing": {
    "rules": [

      // Блокировка  |   Уязвимые UDP порты
      {
        "inboundTag": ["redirect", "tproxy"],
        "outboundTag": "block",
        "type": "field",
        "network": "udp",
        "port": "135, 137, 138, 139"
      },

      // Прямые подключение  |  Доменные имена
      {
        "inboundTag": ["redirect", "tproxy"],
        "outboundTag": "direct",
        "type": "field",
        "domain": [
          "ext:geosite_v2fly.dat:category-gov-ru",
          "ext:geosite_v2fly.dat:private",
          "ext:geosite_v2fly.dat:yandex",
          "ext:geosite_v2fly.dat:steam",
          "ext:geosite_v2fly.dat:vk"
        ]
      },

      // Прямое подключение  |  Торрент
      {
        "inboundTag": ["redirect", "tproxy"],
        "outboundTag": "direct",
        "type": "field",
        "protocol": ["bittorrent"]
      },
      
      // VPS подключение
      {
        "inboundTag": ["redirect", "tproxy"],
        "outboundTag": "vless-reality",
        "type": "field"
      }

    ]
  }
}
```


```json
// 03_inbounds.json
{
  "inbounds": [

    {
      "tag": "redirect",
      "port": 61219,
      "protocol": "dokodemo-door",
      "settings": {
        "network": "tcp",
        "followRedirect": true
      },
      "sniffing": {
        "enabled": true,
        "destOverride": [
          "http",
          "tls"
        ]
      }
    },
	
    {
      "tag": "tproxy",
      "port": 61219,
      "protocol": "dokodemo-door",
      "settings": {
        "network": "udp",
        "followRedirect": true
      },
      "streamSettings": {
        "sockopt": {
          "tproxy": "tproxy"
        }
      },
      "sniffing": {
        "enabled": true,
        "destOverride": [
          "http",
          "tls"
        ]
      }
    }

  ]
}
```
[Генератор outbound из vlees](https://rockblack.pro/xkeen_generator)
