---
title:  "Ruby ile Telegram Botu Oluşturma"
date:   2019-10-01 10:05:00
categories: [Telagram]
tags: [Telagram]
---

Birden fazla Telegram botu bulunmaktadır. Biz bunun içinden Bot Father'ı kullanacağız. Bot Father ile Telegram botu nasıl oluşturulur gelin beraber bakalım.
İlk önce Telegram'ımız da arama kısmına **BotFather** yazıyoruz. Karşımıza çıkan mavi tikli hesaba tıklıyoruz. Sohbet kısmına

```
 /start
```

 yazıyoruz. BotFather  bize cevap olarak bazı komutlar yolluyor.

![](/images/telegram-botu-olusturma/tbo-1.png)

Biz karşımıza gelen bu komutlardan

```
/newbot
```

komutunu kullanıyoruz. BotFather bizden oluşturmak istediğimiz botun sırası ile adı ve kullanıcı adını isteyecektir. Misal olarak adını *Mezarcı*, kullanıcı adını ise *mezarci_bot* koyalım. Bize cevap olarak bota erişebileceğimiz bir token verecektir.

![](/images/telegram-botu-olusturma/tbo-2.png)

Bu token ile bota erişim sağlayıp, değişlikler yapabiliriz.

Bu işlemleri hallettikten sonra Ruby ile oluşturduğumuz *Bot'u* kontrol edeceğiz. Sistemimize veya projemize [telegram-bot-ruby](https://github.com/atipugin/telegram-bot-ruby) kütüphanesini yüklüyoruz.  Projemize eklemek için *Gemfile* dosyasına :

```console
gem 'telegram-bot-ruby'
```

satırını ekliyoruz. Ardından terminal ekranında

```console
bundle
```

komutunu çalıştırıyoruz. Sitemimize yüklemek için ise terminal ekranına

```console
gem install telegram-bot-ruby
```

komutunu çalıştırıyoruz.

Kütüphaneyi ekledikten sonra bir tane *Ruby* dosyası oluşturuyoruz. Oluşturduğumuz dosyaya aşağıdaki kodları ekliyoruz.

```ruby
require 'telegram/bot'
require 'openssl'

OpenSSL::SSL::VERIFY_PEER = OpenSSL::SSL::VERIFY_NONE
token = EVN["TELEGRAM_BOT_TOKEN"]

Telegram::Bot::Client.run(token) do |bot|
  bot.listen do |
    message|
    case message.text
    when '/start'
      bot.api.send_message(chat_id: message.chat.id, text: "Hello, #{message.from.first_name}")
      puts  message.chat.id
    when '/stop'
      bot.api.send_message(chat_id: message.chat.id, text: "Bye, #{message.from.first_name}")
    end
  end
end
```
Oluşturduğumuz botun bize vermiş olduğu *token* değerini koddaki *token* değişkenine tanımlıyoruz. Terminal ekranında

```console
ruby dosya_adı.rb
```

komutunu çalıştırıyoruz. *dosya_adı* kısmına *Ruby* kodlarının bulunduğu dosyanın adını veriyoruz. Bu işlemi yaptıktan sonra Telegram'da arama kısmına oluşturduğumuz botun adını yazıyoruz. Sonra ise

```console
/start
```

komutunu oluşturduğumuz bota mesaj olarak gönderelim. Botumuzun bize cevap verdiğini göreceğiz. Oluşturduğumuz kodun içinde değişiklikler yaparak botumuzu istediğimiz gibi kullanabiliriz.
