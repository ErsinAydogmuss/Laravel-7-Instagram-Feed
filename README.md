# Laravel-7-Instagram-Feed

LARAVEL INSTAGRAMDAN VERİ ÇEKME API’SI


[ÖNCELİKLE BU PAYLAŞIMI BENİM YAŞADIĞIM SORUNLARI YAŞAYABİLME POTANSİYELİ OLUP KAYNAK SIKINTISI ÇEKEBİLECEK İNSANLAR İÇİN YAPIYORUM. UMARIM İŞİNİZE YARAR BEN ÇOK SIKINTI ÇEKTİM FAZLA KAYNAK DA BULAMAMIŞTIM YAŞADIĞIM SORUNLARI BİR ARAYA GETİRİP ADIM ADIM YAPILACAK İŞLEMİ ANLATTIM.]

1- Terminale bunu yazarak apiyi kuruyoruz.
	composer require dymantic/laravel-instagram-feed

2- https://developers.facebook.com/apps/ sitesine gidip Facebook ile giriş yaptıktan sonra create App diyerek bir uygulama 		oluştur.

3-Uygulama oluştururken app type nothing seçebiliriz. Display name : Uygulama adı

4- Uygulama oluşturduktan sonra solda Settings -> Basic kısmına geliyoruz. 
	ÖRNEK OLARAK AŞŞAĞIDAKİ GİBİ DOLDURUYORUZ!!!
	Privacy Policy URL : http://anadolureklam.dijifikir.xyz/
	Terms of Service URL : http://anadolureklam.dijifikir.xyz/
	App Icon : max 1024x1024 boyutunda logo ekliyoruz.
	Eğer hesabı açık bütün kullanıcıların verilerini çekmek istiyorsak Verification yapmak zorundayız.(Kimlik fotoğrafı 			yüklüyoruz)
	En altta Add Platform a basıp http://anadolureklam.dijifikir.xyz/ siteyi ekliyoruz.

5- Solda Menüden Instagram Basic Display -> Basic Display kısmına giriyoruz. (TEST ETMEK İÇİN)
	add instagram tester a basarak verisini çekeceğimiz instagram hesabının adını yazıyoruz ve okeyliyoruz.
	Verileri çekilecek instagram hesabına web üzerinden girerek ayarlar -> uygulamalar ve internet siteleri -> Test kullanıcısı  	davetleri’ne girerek isteği onaylıyoruz.

6- Terminale bunu yazıyoruz ve Dymantic\InstagramFeed\InstagramFeedServiceProvider olanı seçiyoruz
	php artisan vendor:publish
	Ardından php artisan migrate ederek database ‘ e bu tabloların oluşmasını sağlıyoruz.
	Bu 2 tane tablo oluşturuyor 
	dymantic_instagram_basic_profiles : sadece instagram kullanıcı adını ve ıdsini tutuyor.
	dymantic_instagram_feed_tokens : bu tabloda izin alınan kullanıcının tokenini ve diğer bilgilerini tutuyor.

7- Laravel dosyalarından config -> instagram-feed.php kısmına gelip oluşturduğumuz Basic Display Api’nin sitesinden Instagram App Id yi kopyalayıp aşağıdaki ilgili kısıma yapıştırıyoruz. Aynı şekilde hemen yanında bulunan Instagram App Secret kısmınıda aşağıdaki ilgili kısıma yapıştırıyoruz.
	‘client_id’ => ‘YAPIŞTIR’
	‘client_secret’ => ‘YAPIŞTIR’

8- Oluşturduğumuz App’in sitesine girip ÖRNEĞİN
	Valid OAuth Redirect URIs : https://anadolureklam.dijifikir.xyz/instagram/auth/callback
	Deauthorize callback URL : https://anadolureklam.dijifikir.xyz/
	Data Deletion Request URL : https://anadolureklam.dijifikir.xyz/ 
	kısımlarını bu şekilde dolduruyoruz. Bu kısımlar eğer DEVELOPER izini yoksa test kullanıcılarından alınan izinler sonrası  		veya izni iptal etmek isterse bu sayfalara yönlendiriyor pek önemli değil ama doldurun.

9- Terminale gelerek izin alınan instagram adını yazarak tabloya bu profili eklemiş oluyoruz.
	ÖRNEK !!!
	php artisan instagram-feed:profile ersinaydogmuss
	
10- Terminale gelerek php artisan tinker yazarak modunu feed modunu açıyoruz.
	ORNEK !!!
	$profile = \Dymantic\InstagramFeed\Profile::where('username', ‘ersinaydogmuss’)->first()        :  Bu komut bu profili 		önbelleğe alıyor. Yani bundan sonra yazacağımız komut bu profil için geçerli olacak.

11- $profile->getInstagramAuthUrl().   : yazınca bir link çıkacak o linki chrome da açtığımızda bir izin sayfasına yönlendirecek 	izin almak istediğimiz profilin şifresini girerek izin veriyoruz. Ve bu da 2. Tablomuza token’ı kayıt ediyor.
	(BU KISIMDA OLASI OLARAK ALTTAKİ SORUNU YAŞAYABİLİRSİNİZ)
	Oluşan linkteki redirectUrl kısmında local host olarak görünüyor olabilir bu sorunda laravelde env dosyasına girip örneğin 	alttaki gibi doldurabilirsiniz : 

	APP_NAME=dijifikirr    {Oluşturduğumuz app adı}
	APP_URL=https://anadolureklam.dijifikir.xyz         {Site URL ‘si}

12- $profile->feed(10) Terminale bu kodu yazarsak önbellekteki izini alınan instagram hesabının son 10 postu çekilir. Bunun 		sınırı 1000 dir son 1000 posta kadar çekebilirsiniz.bunu eğer değiştirmek istersek $profile->refreshFeed(88) komutuyla 		değiştirebiliyoruz.

13- Şimdi bu çekilen verileri web sayfamızda göstermek için o sayfanın index Controller kısmına yada herhangi methoduna 

	$feed = \Dymantic\InstagramFeed\Profile::where('username', 'dijifikir')->first()->feed();

	return view('instagram', ['instagram' => $feed]);

	Bu kodları ekliyoruz. (İndex harici methoda eklerseniz route kısmını ona göre revize edin.)

14- Web sitemizde görmek için ise XXX.blade.php kısmına gelip

	@foreach($instagram as $post)
    		<img src="{{ $post['url']}}">
	@endforeach

	Örneğin bu kodu yazarsak bütün postlarını çekmiş oluruz

	Eğer örneğin 6. Postunu çekmek istersek :  <img src="{{ $instagram[5][‘url']}}">

	Yazarsak 6. Postunu çekmiş oluruz.


Temel olarak post çekme kısmı bu şekilde diğer olası özellikler için ve kaynaklar için aşağıdaki linklerden işinize yarayanı projenize ekleyebilirsiniz.



https://github.com/Dymantic/laravel-instagram-feed/tree/v2.6.0          -> API SAHIBI

https://www.youtube.com/watch?v=7SQpanLEhFU                 -> Yardımcı Video

https://www.youtube.com/watch?v=Wn5ur1PYfio&list=PL0glhsZ01I-AsN7PRMNWH9X9XJKVpGSWJ             -> Facebook Developer kısmını anlatan video



