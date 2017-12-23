---
layout: post
title: 'Rails SSL Hatası'
excerpt:
categories: articles
tags: [rails, ssl, error]
comments: true
share: true
---

Rails uygulamalarınızda başka bir uygulamaya yapmış olduğunuz API çağrılarında
aşağıdakine benzer hata alıyorsanız bahsettiğim çözüm yöntemi işinize
yarayabilir:

`OpenSSL::SSL::SSLError:SSL_connectreturned=1errno=0state=SSLv3readservercertificateB:certificateverifyfailed`

- [ca-bundle.crt](http://curl.haxx.se/ca/ca-bundle.crt) dosyasını `lib/` dizinine
indirin.

- Aşağıdaki kodları `config/initializers/fix_ssl.rb` dosyasına yazın:

{% highlight ruby %}
require 'open-uri'
require 'net/https'

module Net
  class HTTP
    alias_method :original_use_ssl=, :use_ssl=

    def use_ssl=(flag)
      self.ca_file = Rails.root.join('lib/ca-bundle.crt').to_s
      self.verify_mode = OpenSSL::SSL::VERIFY_PEER
      self.original_use_ssl = flag
    end
  end
end
{% endhighlight %}

Bu yöntem Ruby'yi bu CA bundle'ını kullanmaya zorlayacaktır.

Kaynak: [http://jimneath.org/2011/10/19/ruby-ssl-certificate-verify-failed.html](http://jimneath.org/2011/10/19/ruby-ssl-certificate-verify-failed.html)

