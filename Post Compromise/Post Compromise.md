# Post Compromise (Erişim Sonrası)

Bu bölümde ilk erişimden sonra yapılması gerekenler ve neler yapabileceğimiz yer almaktadır.

## Güvenlik Çözümleri ve Teknolojileri

Kırmızı takımın katılımı sırasında güvenliği ihlal edilmiş bir makineye ilk erişiminizin olduğu ortama aşina olmanız çok önemlidir. Bu nedenle keşif ve enumerating yapılması önemli bir kısımdır ve öncelikli amaç bir sonraki aşamada kullanılmak üzere mümkün olduğunca fazla bilgi toplamaktır.

Başlangıç ​​noktasının oluşturulmasıyla birlikte sömürü sonrası(post-exploitation) süreci başlıyor!


Bu bölümde yaygın olarak kullanılan kavramlar, teknolojiler ve farkında olmamız gereken güvenlik ürünleri tanıtılmaktadır.

Bu bölümde, makineye zaten erişim sağladığımız ve aşağıdakileri sıralayarak çevre hakkındaki bilgimizi daha da genişletmeye hazır olduğumuz varsayılmaktadır:

+ Ağ altyapısı
+ Active Directory Ortamı
+ Kullanıcılar ve Gruplar
+ Host tabanlı(host-based) güvenlik çözümleri
+ Ağ tabanlı(network-based) güvenlik çözümleri
+ Uygulamalar ve hizmetler

### Ağ Altyapısı

Bilinmeyen bir ağa ulaştığımızda ilk hedefimiz nerede olduğumuzu ve neye ulaşabileceğimizi belirlemektir. Kırmızı ekip çalışması sırasında hangi hedef sistemle karşı karşıya olduğumuzu, makinenin hangi hizmeti sağladığını, ne tür bir ağda olduğumuzu anlamamız gerekir. Bu nedenle, ele geçirilen makinenin ilk erişim sağlandıktan sonra enumerate edilmesi bu sorulara yanıt vermenin anahtarıdır. Bu bölüm, katılım sırasında karşılaşabileceğimiz yaygın ağ türleri tartışılacaktır.

Ağ segmantasyonu, birden fazla alt ağa bölünmüş ekstra bir ağ güvenliği katmanıdır. Ağın güvenliğini ve yönetimini geliştirmek için kullanılır. Örneğin müşteri verileri, mali kayıtlar vb. kurumsal en değerli varlıklara yetkisiz erişimi önlemek için kullanılır.

(Virtual Local Area Networks) Sanal Yerel Alan Ağları (VLAN'lar), yerel ağdaki yayın sorunları gibi ağ sorunlarını kontrol etmek ve güvenliği artırmak için ağ segmantasyonunda kullanılan bir ağ tekniğidir. VLAN içindeki host'lar yalnızca aynı VLAN ağındaki diğer hostlarla iletişim kurabilir.

#### Internal (Dahili) Ağlar

Dahili Ağlar, dahili cihazın önemine veya verilerinin erişilebilirliğinin önemine göre bölümlere ayrılan alt ağlardır. Dahili ağ(lar)ın temel amacı, bir kuruluş içindeki bilgileri, daha hızlı ve daha kolay iletişimi, işbirliği araçlarını, operasyonel sistemleri ve ağ hizmetlerini paylaşmaktır. Kurumsal bir ağda, ağ yöneticileri, ağ trafiğini kontrol etmek, ağ performansını optimize etmek ve güvenlik durumunu iyileştirmek dahil olmak üzere çeşitli nedenlerle ağ bölümlendirmeyi kullanmayı amaçlamaktadır.

![internal.png](img/internal.png)

Yukarıdaki diyagram, ağ iki ağa bölündüğü için basit ağ bölümleme konseptinin bir örneğidir. Bunlardan ilki çalışanların workstation'ları ve kişisel cihazları içindir. İkincisi, DNS, dahili web, e-posta hizmetleri vb. gibi dahili hizmetler sağlayan özel ve dahili ağ cihazları içindir.

#### Demilitarized Zone (DMZ) (Arındırılmış/İzole Alan)

DMZ Ağı, bir şirketin dahili yerel alan ağını güvenilmeyen trafiğe karşı koruyan ve ona ekstra bir güvenlik katmanı ekleyen bir uç ağdır. DMZ'nin ortak tasarımı, genel internet ile dahili ağlar arasında yer alan bir alt ağdır.

Şirket içinde bir ağ tasarlamak, onun gereksinimlerine ve ihtiyacına bağlıdır. Örneğin, bir şirketin web sitesi, DNS, FTP, Proxy, VPN vb. kamu hizmetleri sağladığını varsayalım. Bu durumda, genel ağ trafiğini, güvenilmeyen trafiği izole etmek ve erişim kontrolünü etkinleştirmek için bir DMZ ağı tasarlayabilirler.

![dmz.png](img/dmz.png)

Yukarıdaki diyagramda, DMZ ağına giden, güvenilmeyen (doğrudan internetten gelen) ağ trafiğini kırmızı renkle gösteriyoruz. İç ağ arasındaki yeşil ağ trafiği, bir veya birden fazla ağ güvenlik cihazından/cihazlarından geçebilen kontrollü trafiktir.

Sistemin ve iç ağın enumerate edilmesi, saldırganın sistem ve iç ağ hakkında bilgi edinmesine olanak sağlayan keşfetme aşamasıdır. Elde edilen bilgilere dayanarak, bunu sistem veya AD ortamında daha fazla yetki kazanmak için yanal hareketi veya yetki yükseltmek için kullanırız.

#### Ağ Enumeration

TCP ve UDP portları ve kurulan bağlantılar, yönlendirme tabloları, ARP tabloları vb. gibi ağ oluşturma hususlarıyla ilgili olarak kontrol edilmesi gereken çeşitli şeyler vardır.

Hedef makinenin TCP ve UDP açık portlarını kontrol etmeye başlayalım. Bu, aşağıda gösterildiği gibi netstat komutu kullanılarak yapılabilir.

![netstat.png](img/netstat.png)

Çıktı, kurulu bağlantıların yanı sıra açık portları da ortaya çıkarır. Daha sonra ağdaki hedef makinelerle iletişim kuran bilgisayarların IP adresini ve fiziksel adresini içeren ARP tablosunu listeleyelim. Bu, diğer makineleri açık portlarını ve güvenlik açıklarına karşı taramak için ağ içindeki iletişimleri görmenize yardımcı olabilir.

![arp.png](img/arp.png)

#### Dahili Ağ Servisleri

Dahili ağ cihazları için özel ve dahili ağ iletişim erişimi sağlar. Ağ hizmetlerine örnek olarak dahili DNS, web sunucuları, özel uygulamalar vb. gösterilebilir. Dahili ağ hizmetlerine ağ dışından erişilemeyeceğini unutmamak önemlidir. Ancak, bu ağ hizmetlerine erişen ağlardan birine ilk erişimimiz olduğunda, bu ağlara ulaşılabilir ve iletişim için uygun olacaktır.

### Active Directory Genel Bilgi

Veri nesnelerini iç ağ ortamına depolayan ve sağlayan Windows tabanlı bir dizin hizmetidir. Kimlik doğrulama ve yetkilendirmenin merkezi yönetimine olanak tanır. AD, kullanıcılar, bilgisayarlar, yazıcılar vb. dahil olmak üzere ağ ve çevre hakkında temel bilgileri içerir. Örneğin, AD, iş unvanı, telefon numarası, adres, şifreler, gruplar, izinler vb. gibi kullanıcıların ayrıntılarına sahip olabilir.

![ad.png](img/ad.png)

Diyagram, Active Directory'nin nasıl tasarlanabileceğinin olası bir örneğidir. AD Controller(Denetleyicisi), sunucular için bir alt ağa yerleştirilir (yukarıda sunucu ağı olarak gösterilmiştir) ve ardından AD istemcileri, domain'e katılabilecekleri ve güvenlik duvarı aracılığıyla AD hizmetlerini kullanabilecekleri ayrı bir ağ üzerindedir.

Aşağıda aşina olmamız gereken Active Directory bileşenlerinin bir listesi bulunmaktadır:

+ Domain Denetleyicileri
+ Organizasyon Birimleri
+ AD nesneleri
+ AD Domainleri
+ Forest
+ AD Servis Hesapları: Yerleşik yerel kullanıcılar, domain kullanıcıları, Yönetilen servis hesapları
+ Domain Yöneticileri

Domain Denetleyicisi, Active Directory hizmetleri sağlayan ve domainin tamamını denetleyen bir Windows sunucusudur. Kullanıcı verilerinin şifrelenmesinin yanı sıra kullanıcılar, gruplar, politikalar ve bilgisayarlar da dahil olmak üzere bir ağa erişimi kontrol eden bir merkezi kullanıcı yönetimi biçimidir. Ayrıca kaynak erişimine ve paylaşımına da olanak tanır. Bunların hepsi, saldırganların bir domaindeki domain denetleyicisini hedeflemesinin nedenleridir, çünkü bu denetleyici çok fazla yüksek değerli bilgi içerir.

![ad2.png](img/ad2.png)

Organizational Units (Organizasyon Birimleri) (OU'lar), AD alanı içindeki hiyerarşik yapıya sahip kapsayıcılardır.

Active Directory Nesneleri tek bir kullanıcı veya grup olabileceği gibi bilgisayar veya yazıcı gibi bir donanım bileşeni de olabilir. Her domain, aşağıdakiler de dahil olmak üzere bir AD ortamı oluşturan nesne kimlik bilgilerini içeren bir veritabanına sahiptir:

+ Kullanıcılar - Domaindeki makinelerde kimlik doğrulaması yapılmasına izin verilen bir güvenlik sorumlusu
+ Bilgisayarlar - Özel bir kullanıcı hesabı türü
+ GPO'lar - Diğer AD nesnelerine uygulanan politika koleksiyonları

AD domainleri, bir AD ağı içindeki Microsoft bileşenlerinin bir koleksiyonudur.

AD Forest, birbirine güvenen domainlerden oluşan bir koleksiyondur.

![ad3.png](img/ad3.png)

İlk Erişim sağlandıktan sonra kurumsal bir ağda bir AD ortamı bulmak önemlidir, çünkü Active Directory ortamı, katılan kullanıcılara ortam hakkında birçok bilgi sağlar. Kırmızı takım oyuncusu olarak, AD ortamını enumerate ederek ve daha sonra yanal hareket aşamasında kullanılabilecek çeşitli ayrıntılara erişim sağlayarak bundan yararlanıyoruz.

Windows makinesinin AD ortamının bir parçası olup olmadığını kontrol etmek için bir yol olarak komut istemi systeminfo komutunu kullanabiliriz. Systeminfo çıktısı, işletim sistemi adı ve sürümü, host adı ve diğer donanım bilgilerinin yanı sıra AD alanı da dahil olmak üzere makine hakkında bilgi sağlar.

![ad4.png](img/ad4.png)

Yukarıdaki çıktıdan, bilgisayar adının, AD ortamının bir parçası olduğunu doğrulayan domain adı olarak thmredteam.com olan bir AD olduğunu görebiliriz.

Domain bölümünde WORKGROUP çıktısı alırsak, bunun, bu makinenin yerel bir çalışma grubunun parçası olduğu anlamına geldiğini unutmayın.

### Active Directory Temelleri

Kendinizi yalnızca beş bilgisayar ve beş çalışandan oluşan küçük bir işletme ağını yönettiğinizi hayal edin. Bu kadar küçük bir ağda, muhtemelen her bilgisayarı ayrı ayrı sorunsuz bir şekilde yapılandırabileceksiniz. Her bilgisayara manuel olarak giriş yapacak, onları kullanacak kişiler için kullanıcılar oluşturacak ve her çalışanın hesabı için özel yapılandırmalar yapacaksınız. Bir kullanıcının bilgisayarı çalışmayı durdurursa, muhtemelen onun yerine gidip bilgisayarı yerinde tamir edeceksiniz.

Bu çok rahat bir yaşam tarzı gibi görünse de işletmenizin aniden büyüdüğünü ve artık dört farklı ofise yayılmış 157 bilgisayarı ve 320 farklı kullanıcısı olduğunu varsayalım. Yine de her bilgisayarı ayrı bir varlık olarak yönetebilir, ağdaki her kullanıcı için politikaları manuel olarak yapılandırabilir ve herkese yerinde destek sağlayabilir misiniz? Cevap büyük olasılıkla hayır.

Bu sınırlamaların üstesinden gelmek için bir Windows domain kullanabiliriz. Basitçe söylemek gerekirse, Windows domain, belirli bir işletmenin yönetimi altındaki bir grup kullanıcı ve bilgisayardır. Bir domainin arkasındaki ana fikir, Windows bilgisayar ağının ortak bileşenlerinin yönetimini Active Directory (AD) adı verilen tek bir depoda merkezileştirmektir. Active Directory hizmetlerini çalıştıran sunucu, Domain (Controller)(Denetleyicisi) (DC) olarak bilinir.

![ad5.png](img/ad5.png)

Yapılandırılmış bir Windows domaine sahip olmanın ana avantajları şunlardır:

+ Merkezi kimlik yönetimi: Ağdaki tüm kullanıcılar, minimum çabayla Active Directory'den yapılandırılabilir.
+ Güvenlik ilkelerini yönetme: Güvenlik ilkelerini doğrudan Active Directory'den yapılandırabilir ve bunları gerektiğinde ağdaki kullanıcılara ve bilgisayarlara uygulayabilirsiniz.

Gerçek Dünyadan Bir Örnek

Bu biraz kafa karıştırıcı geliyorsa muhtemelen okulunuzda, üniversitenizde veya iş yerinizde bir noktada bir Windows domainle etkileşime geçmişsinizdir.

Okul/üniversite ağlarında, genellikle kampüsteki bilgisayarlardan herhangi birinde kullanabileceğiniz bir kullanıcı adı ve şifre sağlanır. Kimlik bilgileriniz tüm makineler için geçerlidir çünkü bunları bir makineye her girdiğinizde, kimlik doğrulama işlemi kimlik bilgilerinizin kontrol edileceği Active Directory'ye iletilecektir. Active Directory sayesinde kimlik bilgilerinizin her makinede bulunmasına gerek yoktur ve ağ genelinde kullanılabilir.

Active Directory aynı zamanda okulunuzun/üniversitenizin, okul/üniversite makinelerinizdeki kontrol paneline erişiminizi kısıtlamasına olanak tanıyan bileşendir. Politikalar genellikle ağ genelinde dağıtılacak ve böylece bu bilgisayarlar üzerinde yönetici ayrıcalıklarına sahip olmayacaksınız.
__________________

Herhangi bir Windows Domain çekirdeği, Active Directory Domain servisidir (AD DS). Bu servis, ağınızda bulunan tüm "nesnelerin" bilgilerini tutan bir katalog görevi görür. AD tarafından desteklenen birçok nesnenin arasında kullanıcılarımız, gruplarımız, makinelerimiz, yazıcılarımız, paylaşımlarımız ve daha birçokları var. Bunlardan bazılarına bakalım:

#### Kullanıcılar

Kullanıcılar Active Directory'deki en yaygın nesne türlerinden biridir. Kullanıcılar, güvenlik sorumluları olarak bilinen nesnelerden biridir; bu, domain tarafından kimliklerinin doğrulanabileceği ve dosyalar veya yazıcılar gibi kaynaklar üzerinde ayrıcalıkların atanabileceği anlamına gelir. Bir güvenlik sorumlusunun ağdaki kaynaklar üzerinde işlem yapabilen bir nesne olduğunu söyleyebilirsiniz.

Kullanıcılar iki tür varlığı temsil etmek için kullanılabilir:

+ Kişiler: Kullanıcılar genellikle kuruluşunuzdaki çalışanlar gibi ağa erişmesi gereken kişileri temsil eder.
+ Hizmetler: IIS veya MSSQL gibi hizmetler tarafından kullanılacak kullanıcıları da tanımlayabilirsiniz. Her bir hizmetin çalıştırılması için bir kullanıcı gerekir, ancak hizmet kullanıcıları normal kullanıcılardan farklıdır çünkü yalnızca kendi özel hizmetlerini çalıştırmak için gereken ayrıcalıklara sahiptirler.

#### Makineler

Makineler, Active Directory içindeki başka bir nesne türüdür; Active Directory domainine katılan her bilgisayar için bir makine nesnesi oluşturulacaktır. Makineler aynı zamanda "güvenlik sorumluları" olarak kabul edilir ve herhangi bir normal kullanıcı gibi bir hesaba atanır. Bu hesabın domain adı içinde bir miktar sınırlı hakları vardır.

Makine hesaplarının kendisi, atanan bilgisayardaki yerel yöneticilerdir; bunlara genellikle bilgisayarın kendisi dışında kimsenin erişmemesi gerekir, ancak diğer hesaplarda olduğu gibi, parolanız varsa, oturum açmak için bunu kullanabilirsiniz.

Not: Makine Hesabı şifreleri otomatik olarak dönüşümlü olarak gerçekleştirilir ve genellikle 120 rastgele karakterden oluşur.

Makine hesaplarını tanımlamak nispeten kolaydır. Belirli bir adlandırma şemasını takip ederler. Makine hesap adı, bilgisayarın adı ve ardından bir dolar işareti gelir. Örneğin, DC01 adlı bir makinenin DC01$ adında bir makine hesabı olacaktır.

#### Güvenlik Grupları

Windows'a aşina iseniz, muhtemelen dosyalara veya diğer kaynaklara erişim haklarını tek kullanıcılar yerine tüm gruplara atamak için kullanıcı grupları tanımlayabileceğinizi biliyorsunuzdur. Bu, kullanıcıları mevcut bir gruba ekleyebileceğiniz ve bu kullanıcıların grubun tüm ayrıcalıklarını otomatik olarak devralacağı için daha iyi yönetilebilirlik sağlar. Güvenlik grupları aynı zamanda güvenlik sorumluları olarak kabul edilir ve bu nedenle ağdaki kaynaklar üzerinde ayrıcalıklara sahip olabilirler.

Gruplarda hem kullanıcılar hem de makineler üye olabilir. Gerektiğinde gruplara başka gruplar da dahil edilebilir.

Bir domainde, kullanıcılara belirli ayrıcalıklar vermek için kullanılabilecek çeşitli gruplar varsayılan olarak oluşturulur. Örnek olarak, bir domaindeki en önemli gruplardan bazıları şunlardır:
+ Domain Yöneticileri : Bu grubun kullanıcıları, domainin tamamı üzerinde yönetici ayrıcalıklarına sahiptir. Varsayılan olarak etki alanındaki DC'ler de dahil olmak üzere herhangi bir bilgisayarı yönetebilirler.
+ Sunucu Operatörleri : Bu gruptaki kullanıcılar Domain Controller'ı yönetebilirler. Herhangi bir idari grup üyeliğini değiştiremezler.
+ Yedekleme Operatörleri : Bu gruptaki kullanıcıların, izinleri göz ardı edilerek herhangi bir dosyaya erişmelerine izin verilir. Bilgisayarlardaki verilerin yedeklemesini gerçekleştirmek için kullanılırlar.
+ Hesap Operatörleri : Bu gruptaki kullanıcılar, domainde başka hesaplar oluşturabilir veya değiştirebilir.
+ Domain Kullanıcıları : Domaindeki tüm mevcut kullanıcı hesaplarını içerir.
+ Domain Bilgisayarları : Domaindeki tüm mevcut bilgisayarları içerir.
+ Domain Denetleyicileri(Controller) : Domaindeki tüm mevcut DC'leri içerir.

#### Active Directory Kullanıcıları ve Bilgisayarları

Active Directory'deki kullanıcıları, grupları veya makineleri yapılandırmak için Domain Denetleyicisine giriş yapmamız ve başlat menüsünden "Active Directory Kullanıcıları ve Bilgisayarları"nı çalıştırmamız gerekir:

![ad6.png](img/ad6.png)

Bu, domainde bulunan kullanıcıların, bilgisayarların ve grupların hiyerarşisini görebileceğiniz bir pencere açacaktır. Bu nesneler, kullanıcıları ve makineleri sınıflandırmanıza olanak tanıyan kapsayıcı nesneler olan Organizational Units (OU'lar) halinde düzenlenir. Organizational Units esas olarak benzer politika gereksinimlerine sahip kullanıcı gruplarını tanımlamak için kullanılır. Örneğin, kuruluşunuzun Satış departmanındaki kişilerin, BT'deki kişilerden farklı politikalara sahip olması muhtemeldir. Bir kullanıcının aynı anda yalnızca tek bir organizational unit'in parçası olabileceğini unutmayın.

Makinemizi kontrol ettiğimizde BT, Yönetim, Pazarlama ve Satış departmanları için dört alt kuruluş birimine sahip THM adında bir kuruluş biriminin zaten bulunduğunu görebiliriz. Tüm departmanlar için geçerli olan temel politikaların verimli bir şekilde dağıtılmasına olanak tanıdığından, organizational unit'in iş yapısını taklit ettiğini görmek çok normaldir. Çoğu zaman beklenen model bu olsa da organizational unitleri isteğe bağlı olarak tanımlayabileceğinizi unutmayın.

![ad7.png](img/ad7.png)

Herhangi bir organizational unit'i açarsanız içerdikleri kullanıcıları görebilir ve bunları gerektiği gibi oluşturma, silme veya değiştirme gibi basit görevleri gerçekleştirebilirsiniz. Gerekirse şifreleri de sıfırlayabilirsiniz (helpdesk için oldukça faydalıdır.):

![ad8.png](img/ad8.png)

Muhtemelen THM OU dışında başka varsayılan kapsayıcıların da olduğunu fark etmişsinizdir. Bu kapsayıcılar Windows tarafından otomatik olarak oluşturulur ve aşağıdakileri içerir:

+ Builtin (Yerleşik): Herhangi bir Windows hostunun kullanabileceği varsayılan grupları içerir.
+ Bilgisayarlar: Ağa katılan herhangi bir makine varsayılan olarak buraya konulacaktır. Gerekirse bunları taşıyabilirsiniz.
+ Etki Alanı Denetleyicileri: Ağınızdaki DC'leri içeren varsayılan OU.
+ Kullanıcılar: Domain çapında bir bağlama uygulanan varsayılan kullanıcılar ve gruplar.
+ Yönetilen Hizmet Hesapları: Windows domaininizdeki hizmetler tarafından kullanılan hesapları tutar.

__Güvenlik Grupları ve Organizational Unit Karşılaştırması__

Her ikisi de kullanıcıları ve bilgisayarları sınıflandırmak için kullanılsa da amaçları tamamen farklıdır:

+ Organizational Unit, kuruluştaki belirli rollerine bağlı olarak kullanıcı kümeleriyle ilgili belirli yapılandırmaları içeren kullanıcılara ve bilgisayarlara ilkeler uygulamak için kullanışlıdır. Tek bir kullanıcıya iki farklı politika kümesi uygulamaya çalışmanın mantıklı olmayacağından, bir kullanıcının aynı anda yalnızca tek bir organizational unit üyesi olabileceğini unutmayın.
+ Güvenlik Grupları ise kaynaklar üzerinde izin vermek için kullanılır. Örneğin, bazı kullanıcıların paylaşılan bir klasöre veya ağ yazıcısına erişmesine izin vermek istiyorsanız grupları kullanacaksınız. Bir kullanıcı, birden fazla kaynağa erişim izni vermek için gerekli olan birçok grubun parçası olabilir.