---
layout: post
title: "Dropbox Ruby Gem Kullanımı"
modified:
categories: articles
excerpt:
tags: [ruby, gem, dropbox]
comments: true
share: true
---

Üzerinde uğraştığım bir projede, kullanıcılardan alınacak büyük boyutlu dosyaları dropbox'ın "dosya isteği"
bağlantısı üzerinden toplamaya karar verdik. Bunun için Dropbox'ın Ruby Gem'ine ihtiyaç duymuş olduk.

### Senaryo
Kullanıcılar, kendilerine verilecek olan dosya isteme bağlantısından istediği dosyayı, istenilen dosya ismi
formatında yükler. Örnek dosya ismi formatı: `12345678_Emre_Can.zip`. Burada önemli olan dosya isminin başında bulunan başvuru id’sidir.
Kullanıcı dosyasını yükledikten sonra, `/File requests/Users` dizini altında belirtilen id ile başlayan dosya var mı
diye kontrol edilir. Eğer dosya varsa, bu dosya `/Users` dizini altına taşınır. Gün sonunda çalıştırılacak bir görev
ile `/File requests/Users` dizini altında 48 saat süresince kalan dosyalar silinir. Bu sayede dosya ismi yanlış
formatlanmış dosyalar temizlenir.

### Global İstemci Oluşturma
Eğer Ruby on Rails üzerinde çalışıyorsanız dosya yolu: `config/initializers/dropbox.rb`

```ruby
require 'dropbox_sdk'
DROPBOX = DropboxClient.new(SECRET.dropbox['access_token'])
```

### Dosya Arama

```ruby
pry(main)> DROPBOX.search('/File requests/Users', 'Emre')
=> [{"rev"=>"34c62de7b",
  "thumb_exists"=>false,
  "path"=>"/File requests/Users/Emre Can - Form.pdf",
  "is_dir"=>false,
  "client_mtime"=>"Fri, 02 Sep 2016 09:42:34 +0000",
  "icon"=>"page_white_acrobat",
  "read_only"=>false,
  "modifier"=>nil,
  "bytes"=>29995,
  "modified"=>"Fri, 02 Sep 2016 09:42:34 +0000",
  "size"=>"29.3 KB",
  "root"=>"dropbox",
  "mime_type"=>"application/pdf",
  "revision"=>3}]
```

### Dosya Taşıma

```ruby
pry(main)> DROPBOX.file_move('/File requests/Users/Emre Can - Form.pdf', '/Users/Emre Can - Form.pdf')
=> {"read_only"=>false,
 "revision"=>7,
 "bytes"=>29995,
 "thumb_exists"=>false,
 "rev"=>"74c62de7b",
 "modified"=>"Mon, 19 Sep 2016 08:01:12 +0000",
 "mime_type"=>"application/pdf",
 "size"=>"29.3 KB",
 "path"=>"/Users/Emre Can - Form.pdf",
 "is_dir"=>false,
 "modifier"=>nil,
 "root"=>"dropbox",
 "client_mtime"=>"Fri, 02 Sep 2016 09:42:34 +0000",
 "icon"=>"page_white_acrobat"}
```

### Dosya Silme

```ruby
pry(main)> DROPBOX.file_delete('/File requests/Users/test test - IMG_20160831_110840.jpg')
=> {"size"=>"0 bytes",
 "bytes"=>0,
 "thumb_exists"=>true,
 "path"=>"/File requests/Users/test test - IMG_20160831_110840.jpg",
 "photo_info"=>"pending",
 "is_dir"=>false,
 "client_mtime"=>"Wed, 31 Dec 1969 23:59:59 +0000",
 "icon"=>"page_white_picture",
 "read_only"=>false,
 "is_deleted"=>true,
 "rev"=>"d4c62de7b",
 "modified"=>"Mon, 19 Sep 2016 08:21:07 +0000",
 "modifier"=>nil,
 "root"=>"dropbox",
 "mime_type"=>"image/jpeg",
 "revision"=>13}
```

### Dosya Bağlantısı

```ruby
pry(main)> DROPBOX.media('/File requests/Users/Emre Can - Form.pdf')
=> {"url"=>"https://dl.dropboxusercontent.com/1/view/a0vptjabjhebrc6/File%20requests/Users/Emre%20Can%20-%20Form.pdf", "expires"=>"Mon, 19 Sep 2016 12:30:48 +0000"}
```

### Kaynaklar

1. [https://www.dropbox.com/developers-v1/core/start/ruby](https://www.dropbox.com/developers-v1/core/start/ruby)
2. [https://github.com/dropbox/dropbox-sdk-ruby/blob/master/lib/dropbox_sdk.rb](https://www.dropbox.com/developers-v1/core/start/ruby)
