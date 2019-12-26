---
title:  "Github Pages ile Blog Oluşturma"
date:   2019-09-16 10:05:00
categories: [GitHub]
tags: [GitHub]
---

GitHub Pages ile ücretsiz blog oluşturulabilir. Şimdi bunun nasıl yapıldığına beraber bakalım.

İlk önce GitHub'dan yeni bir depo(repository) oluştruyoruz. Oluşturduğumuz bu deponun adını kullanıcıadı.github.io şeklinde adlandırıyoruz.

![](/images/blog-olusturma/blog-olusturma-1.png)

Bu işlemi yaptıktan sonra Github'un bize önerdiği temalardan veya kendi oluşturduğunuz temayı deponuza ekleyip sitenizi aktif kullanır hale getirebilirsiniz.

Bu işlemleri yaptıktan sonra isterseniz sitemizin alan adını değiştirebilir. Bu işlemi yapmak içinde bize alan adı verebilen sitelerden istediğimiz alan adını bulmamız gerekiyor. Ben bu işlem için [Dot.TK](http://dot.tk) sitesinden kendime uygun bir alan adı buldum. Buradan alan adını aldıtan sonra  sitenin güvenliği artırmak adına [Cloudflare.com](http://cloudflare.com) sitesinden bir üyelik alıp aldığımız alan adını buraya yönledireceğiz. Bunun için yapılması gereken işlem aldığımız alan adını cloudflare eklemek.

![](/images/blog-olusturma/blog-olusturma-2.png)

Ekledikten sonra  GitHub'ın bize vermiş olduğu 4 Ip adresinden birini Cloudflare DNS sekmesindeki alana ekliyoruz.

##### Ip Adresleri :
	* 185.199.108.153
	* 185.199.109.153
	* 185.199.110.153
	* 185.199.111.153
	
![](/images/blog-olusturma/blog-olusturma-3.png)

Ip adresi eklendikten sonra Cloudflare bize iki adet nameserver verecektir. Bu nameseverleri Dot.TK'nın  yönlendimiş olduğu [freenom.com](http://freenom.com) sitesine ekliyoruz.

![](/images/blog-olusturma/blog-olusturma-4.png)

![](/images/blog-olusturma/blog-olusturma-5.png)

Bu işlemleri yaptıktan sonra GitHub'da depomuzun bulunduğu yerde yeni bir dosya oluşturuyoruz ve adını uzantısız bir şekilde **CNAME** olararak adlandırıyoruz. Bu dosyanın içine aldığımız alan adını ekliyoruz.

![](/images/blog-olusturma/blog-olusturma-6.png)
