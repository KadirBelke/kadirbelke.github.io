---
title:  "OTP ve 2FA Nedir ve Nasıl Çalışır?"
date:   2019-10-01 10:05:00
categories: [2FA]
tags: [2FA]
---

**OTP Nedir?**

OTP *(One Time Password)* tek kullanımlık parola anlamına gelir. Tek kullanımlık şifre nedir şimdi ona bakalım.

Kullanıcıların, kullanım güvenliğini artırmak için son zamanda çeşitli yöntemler kullanılmaya başlanmıştır. Bu yöntemlerden biride OTP'dir. OTP, isminden de anlaşılacağı üzere kullanıcıya tek kullanımlık bir parola sağlar. Örneğin bir sisteme giriş yaptıktan sonra, bu sistem online bankacılık olabilir, size sms veya mail olarak bir kod gönderilir. Bu kod ile sisteme giriş yapılmasını ister. Bu sayede sistemin kullanıcı tarafını daha güvenilir hale getirmiş olur.

Peki bu tek kullanımlık kodlar nasıl üretilir. Sizin sistemde bulunan kişisel bilgileriniz ile sistemin belirledi anahtar bir değer belirlenen bir algoritma sayesinde oluşturulur.

**2FA Nedir?**
2FA (Two Factor Authendication) çift aşamalı doğrulama anlamına gelmektedir.

2FA, OTP'de olduğu gibi mantık aynıdır aslında. Kullanıcıların, kullanım güvenliğini artırmak için kullanılır. 2FA'daki mantık ise, sizin sitemde bu özelliği aktif ettiğiniz taktir de size bir kod yada QR kod verir. Bu kodu yardımcı bir uygulamaya dahil ettiğiniz de size, belirli süre aralığında sisteme gireceğiniz bir referans kod verir. Ve bu kod ile sisteme giriş yapmanıza olanak sağlar. Yardımcı uygulamaya örnek olarak *Google Authenticator* verilebilir.

Kodların üretim mantığında ise yine OTP'de olduğu gibi sizin kişisel bilgileriniz ve sistemin içinde bulunan anahtar değer ile bir algoritma kurarak sistem bir kod veya QR kod üretir. Yardımcı programa entegre edilen bu kod ise başka bir algoritma ile size belirli aralıkta referans kodları sağlar. Asıl sistemde ise aynı algoritma çalıştığından kodların doğruluğu kontrol edilerek sisteme giriş yapılmasını olanak gösterir.

Gelin şimdi bu bilgileri uygulamaya dökelim. Rails'de oluşturulmuş bir projeye entegre edelim. Ama başlamadan önce projenizde daha önceden [Devise](https://github.com/plataformatec/devise) kütüphanesi ekleyip dosyaları oluşturmanız gerekmektedir.

Oluşturulmuş olan projeye  [Devise-Two-Factor Authentication](https://github.com/tinfoil/devise-two-factor) ve [RQRCode](https://github.com/whomwah/rqrcode) kütühenelerini ekliyoruz.

Gemfile dosyasının içine  aşağıda satırları ekleyelim.
```ruby
gem 'devise-two-factor'
gem 'rqrcode'
```
Ardından terminalde aşağıdaki komutu çalıştıralım.
```
bundle
```
Daha sonra daha önceden oluşturulmuş *devise* modelinin(genel olarak bu modelin adı *user.rb* oluyor) içine entegre edelim.

```ruby
devise :two_factor_authenticatable,
         :otp_secret_encryption_key => ENV['YOUR_ENCRYPTION_KEY_HERE']
```
*ENV['YOUR_ENCRYPTION_KEY_HERE']* kısmının yerine sistemin kendimiz bir değer girmeliyiz.

Bu işlemin ardından terminalde aşağıdaki komutu çalıştıralım.
```
rails generate devise_two_factor User ENVIRONMENT_VARIABLE
```
*ENVIRONMENT_VARIABLE* kısmına yukarıda oluşturduğumuz değeri girelim.

Bu işlemi yaptıktan sonra *application_controller.rb* dosyasının içine ekleyelim.
```ruby
before_action :configure_permitted_parameters, if: :devise_controller?

...

protected

def configure_permitted_parameters
  devise_parameter_sanitizer.permit(:sign_in, keys: [:otp_attempt])
end
```
Daha sonra bir controller dosyası ekleyelim. Bu dosya ile 2FA etkinleştireceğiz. Dosyayı oluşturmak için aşağıdaki komutu terminalde çalıştıralım.
```
rails g controlle Users
```
Ve oluşturduğumuz dosyanın içine kodları ekleyelim.

```ruby
def enable_otp
 current_user.otp_secret = User.generate_otp_secret
 current_user.otp_required_for_login = true
 current_user.save!
 redirect_to root_path
end
def disable_otp
 current_user.otp_required_for_login = false
 current_user.save!
 redirect_to root_path
end
 ```

Daha sonra kullanıcının verilen kodu sisteme girmesi için bir alan oluşturalım. Bu alan şimdilik giriş ekranında ayarlayalım.
*app/views/users/registrations/edit.html.erb* dosyasının içine aşağıdaki kodu ekleyelim.

```ruby
<% if !current_user.otp_required_for_login %>
 <%= button_to "Enable 2FA", users_enable_otp_path, :method => :post %>
<% end %>
<% if current_user.otp_required_for_login %>
 <%= button_to "Disable 2FA", users_disable_otp_path, :method => :post %>
 <br>
 <%=  RQRCode::QRCode.new(current_user.otp_provisioning_uri(current_user.email, issuer: "Devise-Two-Factor-Demo")).as_svg(offset: 0, color: '000',
 shape_rendering: 'crispEdges',
 module_size: 3).html_safe %>
 <br>
<% end %>
<%= link_to "Log out", destroy_user_session_path, :method => :delete %>
```
*app/views/users/sessions/new.html.erb* dosyasının içinede aşağıdaki kodu ekleyelim.

```ruby
 <div class="field">
 <%= f.label :otp_attempt %><br />
 <%= f.text_field :otp_attempt %> </div>
 </div>
```

 *config/initializers/devise.rb* dosyasında
```ruby
Devise.setup do |config|
```
kod bloğunun içine
```ruby
config.warden do |manager|
	manager.default_strategies(:scope => :user).unshift :two_factor_authenticatable
end
```
 kodu ekleyelim.
 *config/initializers/filter_parameter_logging.rb* dosyasında
```ruby
 `Rails.application.config.filter_parameters  +=  [:password]`
```
 kodu
```ruby
Rails.application.config.filter_parameters  +=  [:password,:otp_attmept]
```
şeklinde değiştirelim.
*config/routes.rb* dosyasına aşağıdaki kodu ekleyelim.
```ruby
 post 'users/enable_otp'
 post 'users/disable_otp'
 devise_for :users,  controllers: {  sessions: 'users/sessions'  }
```

Ve routes ayarlarını yaptıktan sonra sistemimiz çalışır hale gelecektir.
Sisteme kayıt olup 2FA'yı etkin hale getirdikten sonra sistem bir QR kod üretip size verecektir. Bu QR kodu Google Authenticator uygulamasında kaydettiğimizde bize her 30 saniye bir referans kodu üretecektir.
