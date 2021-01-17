# İçindekiler
- [Giriş](#giriş)
  - [Neden Docker](#neden-docker)
  - [Sanallaştırma](#sanallaştırma)
  - [Container (Linux)](#container-linux)
  - [Docker Engine](#docker-engine)
  - [Image ve Container (Docker)](#image-ve-container-docker)
  - [Image Registry](#image-registry)
  - [Kurulum Yapmadan Docker Kullanmak](#kurulum-yapmadan-docker-kullanmak)
- [Container 101](#container-101)
  - [Docker CLI](#docker-cli)
  - [Container Temelleri-1](#container-temelleri-1)
  - [Container Temelleri-2](#container-temelleri-2)
  - [Container Temelleri-3](#container-temelleri-3)
  - [Docker Katmanlı Dosya Sistemi Yapısı](#docker-katmanlı-dosya-sistemi-yapısı)
  - [Docker Container Yaşam Süresi (Lifecycle)](#docker-container-yaşam-süresi-lifecycle)
  - [Docker Volume-Container Dışı Veri Saklama](#docker-volume-container-dışı-veri-saklama)
  - [Docker Boş-Dolu Volume Davranışları](#docker-boş-dolu-volume-davranışları)
  - [Bind Mounts](#bind-mounts)
- [Container 102](#container-102)
  - [Docker Network Driver](#docker-network-driver)
  - [Docker Network Objeleri-1](#docker-network-objeleri-1)
  - [Docker Network Objeleri-2-Port Publish](#docker-network-objeleri-2-port-publish)
  - [Docker Network Objeleri-3](#docker-network-objeleri-3)
  - [Logging Uygulama Günlükleri](#logging-uygulama-günlükleri)
  - [Docker Stats ve Top](#docker-stats-ve-top)
  - [Container Cpu ve Memory Limitleri](#container-cpu-ve-memory-limitleri)
  - [Environment Variables (Ortam Değişkenleri)](#environment-variables-ortam-değişkenleri)
- [Image ve Registry](#image-ve-registry)
  - [Image İsimlendirme ve Tag Yapısı](#image-i̇simlendirme-ve-tag-yapısı)
  - [Image oluşturma](#image-oluşturma)
    - [Dockerfile-1](#dockerfile-1)
      - [Dockerfile Parametreleri](#dockerfile-parametreleri)
    - [Dockerfile-2](#dockerfile-2)
    - [Dockerfile-3](#dockerfile-3)
    - [Dockerfile-4](#dockerfile-4)
    - [Linux Shell Trickleri](#linux-shell-trickleri)
    - [Dockerfile-5](#dockerfile-5)
    - [ADD ve COPY Farkı](#add-ve-copy-farkı)
    - [ENTRYPOINT ve CMD Farkı](#entrypoint-ve-cmd-farkı)
    - [Exec Form ve Shell Form Farkı](#exec-form-ve-shell-form-farkı)
    - [Multi-stage Build](#multi-stage-build)
    - [ARG](#arg)
    - [Docker Commit](#docker-commit)
    - [Docker Save-Load](#docker-save-load)
    - [Registry](#registry)
- [Compose ve Swarm](#compose-ve-swarm)
  - [Docker Compose](#docker-compose)
    - [Docker Compose CLI](#docker-compose-cli)
    - [Docker Compose Yaml Dosyası](#docker-compose-yaml-dosyası)
    - [Docker Compose Build](#docker-compose-build)
  - [Container Orchestration](#container-orchestration)
    - [Docker Swarm](#docker-swarm)
    - [Swarm Manager ve Worker Node](#swarm-manager-ve-worker-node)
      - [Manager Node](#manager-node)
      - [Worker Node](#worker-node)
    - [Docker Swarm init](#docker-swarm-init)
    - [Swarm Service](#swarm-service)
    - [Overlay Network](#overlay-network)
- [Docker Secret](#docker-secret)


# Giriş
Docker öğrenirken aldığım notlar. 

Genellikle Özgür Öztürk'ün [Docker eğitimi](https://www.udemy.com/course/adan-zye-docker/)ni izlerken not aldım.

Elimin altında Türkçe kaynak olması için [Docker ve Konteyner Uygulamaları](https://www.amazon.com.tr/Docker-Konteyner-Uygulamalar%C4%B1-Deniz-Parlak/dp/6058060761/ref=sxts_sxwds-bia-wc-rsf-lq2a1_0?__mk_tr_TR=%C3%85M%C3%85%C5%BD%C3%95%C3%91&cv_ct_cx=docker&dchild=1&keywords=docker&pd_rd_i=6058060761&pd_rd_r=adeb9eba-433e-4a3e-86e0-e81304013581&pd_rd_w=yeCbD&pd_rd_wg=E8mQH&pf_rd_p=1afd11f1-1dc6-4ec0-a331-2a221d78fd95&pf_rd_r=ZQNNXBF5BV4D5Z92G71P&psc=1&qid=1610552031&sr=1-1-569cd1f4-72a6-4f1d-b3c5-cf7f1ae39fbb) kitabını almıştım ve bazen buradan faydalandım.

*Metinlerin bir bölümünü Özgür Hoca'nın sunumundan aldım.

*Naçizane; [eğitimi](https://www.udemy.com/course/adan-zye-docker/) şiddetle önerebilirim
## Neden Docker

Yeni nesil IT sistemleri Docker üzerinde koşuyor. En çok kullanılmak istenen platformlar listesinde üst sıralarda yer alıyor. İş ilanlarında aranan bir özellik :stuck_out_tongue_winking_eye:

## Sanallaştırma

- Temelde bir fiziksel makina üzerinde birden fazla sanal makina kurup, kaynak dağıtımını ve ortak kaynak kullanımını sağlayan sisteme verilen isimdir.
- Bu sayede fiziksel sunuculardaki atıl kapasiteyi minimuma indirebiliriz.
  - Kaynak israfı önlenir
  - Her uygulama izole edilmiştir, bu sayede bir uygulamada çıkan sorun diğer uygulamaları etkilemeyecektir

## Container (Linux)

- Sanallaştırma her ne kadar kaynak israfını minimuma indirse de yine de kaynak israfı mevcuttur. Bu sebeple container'lar hayatımıza girdi.
- Linux containerlar sayesinde uygulamalar izole bir şekilde çalıştırılabildi fakat bu kolay bir işlem değildi.
- **Namespaces**
  - Linux çekirdeğinde bulunan yapı taşlarındandır. Prensibi çekirdek kaynaklarrının bölümlere ayrılmasıdır. Container izolasyonunun sağlanmasındaki birincil yapıdır.
  - Bazı namespaces'ler
    - Net: Ağ arayüzlerini yönetir
    - PID: Süreçlerin izolasyonunu sağlar
    - Mnt: Dosya sistemindeki mount noktalarını yönetir
- **Cgroups**
  - Linux çekirdeğinin kullandığı temel bileşenlerdendir. Donanım kaynaklarının sınırlandırılması, kısıtlama ve limit özelliklerinin sağlanması için kullanılır
- Container'lar; host makinede izole olarak çalışan, kullanıcı tarafından her şekilde müdahale edilebilen **sanal birimdir**.
- Aynı sistem üzerinde çalışırlar, o sistemin çekirdeğini kullanırlar fakat birbirlerinden tamamen izole durumdalardır.

## Docker Engine
- Docker engine; client-server mimarisinde bir uygulamadır
- Docker platformunun kalbidir
- Docker Daemon, Docker Client, REST API bileşenlerinden (temel) oluşur
- **Docker Daemon**
  - REST API isteklerini karşılar
  - Docker objelerini üretmemizi ve yönetmemizi sağlar (Container, image, volume vb bileşenler)
- **Rest API**
  - Diğer uygulamalar rest api sayesinde docker daemon ile konuşur
- **Docker CLI (Client)**
  - Rest API'nin en önemli kullanıcısı Docker CLI'dır
- Bir docker engine kurduğumuzda o makinede hem docker server (daemon) hem de docker client (docker cli) kurulmuş olur
- İstersek bu client (docker cli) uygulamasını localimize kurarak, cloudda kurduğumuz bir server (docker daemon)'ı yönetebiliriz. Yani bu 2 uygulama birbirinden farklıdır.

![docker-components](./static/giris/giris-1.png)


## Image ve Container (Docker)
- Docker image'lerde kernel'e gerek yoktur
- Image'ler birer şablondur
- Bir image'i bir yerde depolayabilirsem, docker engine sayesinde bu image'i çalıştırabilirim
- Container'lar sayesinde image'leri depolayabiliriz
- Container dediğimiz şey aslında bir nevi image'lerin çalışır halidir. Container image şablonunun çalışan kopyasıdır olarak düşünebiliriz
- Bu sayede her sistemde her seferinde aynı sonucu alırız. Bir yerde çalışan container her yerde çalışır

## Image Registry
- Container Registry, Container Image Registry, Docker Registry olarak da bilinir
- Docker imajlarını depolayıp dağıtabildiğimiz bir alt yapı servisidir
- En bilineni Docker Engine'ın default olarak baktığı yer olan [Docker Hub](https://hub.docker.com/)'dır


## Kurulum Yapmadan Docker Kullanmak
- [Play With Docker](https://labs.play-with-docker.com/) hizmeti sayesinde cloud ortamında Docker denemeleri yapabiliriz
- Tek kötü yanı bir oturumda en fazla 4 saat kullanmamıza izin vermesidir


# Container 101
## Docker CLI
- Docker Daemon'u yönetmemizi sağlar
- `docker version`  -> Docker kurulumu ile ilgili temel bilgilere erişiriz
- `docker info` -> Docker ile ilgili temel bilgilere erişiriz (kaç container çalışıyor, drivers vb.)
- `docker`  -> Docker CLI'da kullanabileceğimiz komutları listeler
  - **Options**
    - Docker Daemon'a bağlanırken kullanacağımız parametreler gözükür
  -  **Management Commands**
     -  Yönetebileceğimiz bileşenlerin listesi
  -  **Commands**
     -  Yönetmek istediğimiz bileşen için çalıştırmak istediğimiz komut
- `docker image --help`  -> image bileşeni ile kullanabileceğimiz komutları listeler

## Container Temelleri-1
- `docker container --help` ->  container bileşeni ile kullanabileceğim komutlar
- `docker container run` -> parametre olarak verdiğimiz isimde bir container varsa başlatır yoksa önce oluşturur sonra başlatır
- `docker container run --name ilkcontainer ozgurozturknet/app1` -> **ilkcontainer** adında bir container oluşturdu ve docker hub'da ozgurozturknet altındaki varsayılan (app1) uygulamasını çalıştırdı
- `docker container ls -a` -> sistemdeki tüm container'ları listeler. -a olmasaydı sadece çalışan container'ları listelerdi
- **Container içerisindeki varsayılan olarak çalışması için ayarlanan uygulama durduğunda container'da kapatılır**


## Container Temelleri-2
- `docker container run <IMAGE>`
- `docker container run -p 80:80 ozgurozturknet/adanzyedocker`
  - 80:80 portunda çalışan bir web servisi çalıştırır (hazır imaj)
  - `docker container ls` komutu ile bu container'ı görebiliriz. Çünkü bu çalışıp kapanan bir uygulama değildir
- `docker container logs <ContainerID>` -> ID'si verilen container'a ait logları gösterir
- `docker container stop <ContainerID>` -> ID'si verilen container'ı durdurur
- `docker container start <ContainerID>` -> ID'si verilen container'ı başlatır
- `docker container run -d -p 80:80 ozgurozturknet/adanzyedocker`
  - **-d** detach'ten gelmektedir. Container direkt arka planda çalışmaya başlar (terminalimize bağlamaz)
- `docker container rm <ContainerID>` -> ID'si verilen container'ı siler
  - illa tam id vermek zorunda değiliz id'nin ilk harflerini versek de olur
  - Çalışan bir container silinemez bunu öncelikle durdurmamız gerekir ya da **-f** parametresi kullanılarak **force** ederek sileriz
  - `docker container prune` 
    - sistemdeki çalışmayan tüm container'ları siler


## Container Temelleri-3
- `docker container run --name websunucu -p 80:80 -d ozgurozturknet/adanzyedocker` 
  - websunucu adında bir container'ı adanzyedocker imajından oluşturdu ve arka planda çalıştırdı (127.0.0.1'e giderek görebilirsiniz)
  - `docker container exec -it <ContainerName> <command>`
    - `it` -> interaktif olarak container'a bağlan demek
    - `docker container exec -it websunucu sh`
      - websunucu container'ına bağlan ve sh komutunu çalıştır (shelle bağlanır)
    - `ctrl + p q`  kombinasyonu ile Container'ı kapatmadan arka plana alabiliriz
- `docker cp basitapp:/app .` -> **container'ın kapanmış olması önemli değil yeter ki silinmemiş olsun.** basitapp adındaki container'ın app klasörünü host makinadaki bu dizine kopyala dedik.


## Docker Katmanlı Dosya Sistemi Yapısı
- Docker depolama alt yapısında **union file system** adı verilen bir yapı kullanır
- Docker imajları mevcut bir base imaj üzerine inşa edilir
- Bu imaj üzerine yapılan her değişiklik birer katman olarak eklenir ve bu katmanlar birer dosya olarak saklanır


## Docker Container Yaşam Süresi (Lifecycle)
- Tüm ayarlamalar image oluşturma aşamasında yapılandırılır
- Tek bir uygulama çalıştırması için dizayn edilir
- Bir sorun olursa saniyeler içerisinde yeni bir kopya oluşturulabilir
- Sorun container'a bağlanılarak çözülmez
- Container içerisinde çalışan ana uygulama durduğu zaman container'da durdurulur
- Container'da bir sorun ile karşılaşılırsa bu container'a bağlanılarak değil yeni bir container oluşturularak çözülür
- Container'lar yeniden oluşturulduğunda container içerisindeki veriler sıfırlanır, bunları container dışında tutmamız gerekir


## Docker Volume-Container Dışı Veri Saklama
- Ne demiştik? Docker lifecycle sonlandığında container içerisindeki her şey sıfırlanır, yani yazdığımız dosyalar vb hiç olmamış gibi ilk hale döner
- Volume'ler Container'lar dışında veri saklamak istediğimiz zaman çok kullanışlıdır
- `docker volume create <VolumeName>` -> parametre olarak verilen isimde bir volume oluşturur
  - `docker volume create ilkvolume`
- `docker volume inspect <VolumeName>` -> volume detaylarını verir
  - `docker volume inspect ilkvolume`
- `docker container run -it -v ilkvolume:/uygulama alpine sh`
  - alpine imajından bir container oluşturuyorum
  - **-it** ile container'a interaktif olarak bağlanıp **sh** komutunu çalıştırıyorum
  - **-v** ile ilkvolume adındaki volume'u container içerisindeki /uygulama adındaki klasöre bağlıyorum
    - `-v <VolumeName>:<ContainerİçindekiKlasör>`
- Örneğimiz gereği aşağıdaki işlemleri gerçekleştiriyoruz
  - `cd uygulama`
  - `touch test`
  - `echo "naber nasılsın?" > test`
- `exit` ile conteiner'dan çıktık ve container'ı siliyoruz (volume'u test edeceğiz bakalım yeni bir container ile oluşturduğumuz test dosyasına ulaşabilecek miyiz)
- `docker volume ls` -> komutu ile volume'leri listeleriz
- `docker container run --name mydebiancontainer -it -v ilkvolume:/uygulama debian sh`
  - debian imajından mydebiancontainer adında container oluştur, /uygulama klasörüne ilkvolume volume'unu bağla ve sh ile container'a bağlan
  - burdan aynı şekilde klasöre gidip cat ile dosya içerisine bakarsak yazdığımız verinin orda bulunduğunu görebiliriz


## Docker Boş-Dolu Volume Davranışları
- Container içerisinde volume adında bir klasör yoksa, o isimde bir klasör oluşturulur ve içerisine mount edilir. Peki volume'leri bir klasör içerisine mount etmek istersek?
- Eğer volume imaj içinde bulunan mevcut klasöre mount edilirse:
  - Klasör boşsa o anda volume içinde hangi dosyalar varsa bu klasörde de o dosyaları görürüz
  - Klasörde dosya varsa ve volume boşsa klasördeki **dosyalar volume'e kopyalanır**
  - Klasörde dosya var ya da yok fakat volume boş değilse, klasörün içinde volume'de ne varsa onu görürüz



## Bind Mounts
- Host makinada (kendi local makinamız) bulunan bir klasörü veya dosyayı Container içerisinde map etmeye **Bind Moun** denir
- `docker container run -d -p 80:80 --name mynginx nginx` -> basit bir web sunucu oluşturduk
- `docker container exec -it mynginx sh` -> container'a bağlanıyoruz
- `cd /usr/share/nginx/html` -> bize default olarak serve edilen dosya
- Şimdi bu container'ı siliyoruz ve aşağıdaki komutu çalıştırıyoruz
- `docker container run -d -p 80:80 -v /Users/mebaysan/Desktop/mydockertest:/usr/share/nginx/html nginx`
  - -d ile container'ın arka planda çalışmasını sağlıyoruz 
  - -p ile port set ediyoruz
  - -v ile aynı Volume bağlar gibi local makinamızdaki klasör path'ini veriyoruz ve dedik ki; container içerisindeki /usr/share/nginx/html klasörü localimdeki ilgili path'ten çeksin
  - nginx -> imaj adı
- Unutmamalıyız ki bu yöntem development aşamasında kullanılır, kesinlikle production ortamında kullanmamalıyız!


# Container 102

## Docker Network Driver
- Ek protokol vb getirmeden ağ işlemlerini gerçekleştirebiliriz
- Container sistemlerinin iletişim altyapısını network driver'ları ile sağlıyoruz
- Temel 5 driver vardır
  - Bridge
    - Varsayılan driver'dır
    - Network objesi oluşturulurken özellikle başka bir driver belirtilmezse bridge driver ile oluşturulur
    - Her Docker kurulu host üstünde bridge driver ile oluşturulmuş "Bridge" adında network bulunur ve container oluşturulduğunda farklı bir driver belirtilmediği sürece default olarak buna bağlanılır
  - Host
    - Her sistemde host driver ile oluşturulmuş "Host" adında bir driver vardır
    - Bu network'e bağlı container'da network izolasyonu olmaz. O host üzerinde çalışan bir process'mişcesine host'un ağ kaynaklarını kullanır
  - Macvlan
    - Bu driver ile oluşturulan network objeleri ile container'lar fiziksel ağlara kendi mac adreslerine sahip birer fiziksel ağ adaptörüne sahipmişcesine bağlanabilir
  - None
    - Container'a hiç bir şekilde ağ bağlantısı olmasın istersek None driver kullanılır
  - Overlay
    - Ayrı hostlar üzerindeki container'ların aynı ağda çalışıyormuş gibi çalışması istendiği zaman Overlay network'ler kullanılır



## Docker Network Objeleri-1
- `docker network ls`  -> sistemdeki kurulu network objeleri listeler
- `docker network inspect bridge` -> bridge NAME'ine sahip objenin özelliklerini listeler
- `docker container run --net <NetworkObject> <CONTAINER>`
  - `--net` ile network objesini vererek ilgili network'e bağlanabiliriz
  - `docker container run --net none <CONTAINER>`  -> container'a ağ bağlantısı olmaz (none)



## Docker Network Objeleri-2-Port Publish
-  Ayni bridge üzerindeki container'lar birbirleriyle iletişim kurabilirler Fakat biz dış dünyadan container içerisindeki servislere erişmek istersek **port publish** denilen işlem sayesinde erişebiliriz.
-  **Örnek olarak** container içerisindeki servis 80 portundan dinliyor. Biz `-p` ya da `--publish` parametresi ile bu portu belirleriz ve bu sayede host makineye 80 portundan gelen istekleri container'ın 80 portuna iletiriz.
-  Bu işleme **port publish** denir. Notasyon şu şekildedir
   - `-p <HostPort>:<ContainerPort>`
   - `-p 80:80` -> örnek
   - `-p 8080:80 -p 8043:443` -> birden fazla port publish yapabiliriz
   - **Default olarak TCP portları açılır. Eğer istersek UDP portları açabiliriz**
     - `-p 80:80/udp`


## Docker Network Objeleri-3
- Container'lar arası network izolasyonu sağlamak istersek ayrı bridge'ler oluşturarak bunu sağlayabiliriz
  - Default bridge network içerisinde dns çözümlemesi yok
- Varsayılan dışında ip aralıkları tanımlayabiliriz
- Kullanıcı tanımlı bridge network'e bağlı container'lar birbirleriyle isimler üzerinden haberleşebilirler. Dns çözümlemesi sağlar
- Container'lar çalışır durumdayken de kullanıcı tanımlı bridge network'lere bağlanıp, bağlantıyı kesebilirler
- `docker container run -d --name websunucu ozgurozturknet/adanzyedocker ` -> websunucu adında bir container oluşturuyorum
- `docker container run -it -d --name database1 ozgurozturknet/adanzyedocker sh` -> database1 adında bir container oluşturup içine giriyorum.
  - `ping websunucu1` ile ilk container'a erişmeye çalışıyorum
  - `/usr/src/myapp # ping websunucu`
    - `ping: websunucu: Name does not resolve` -> o container'a direkt container adı ile erişemiyorum; çünkü default bridge network'de dns çözümlemesi yok
    - Fakat IP adresi üzerinden erişebiliriz yani aralarında bağlantı var
- Şimdi container'ları siliyoruz ve kendi bridge'mizi oluşturup container'ları yeniden oluşturacağız
- `docker network create mybridge` -> mybridge adında bir bridge network oluşturduk. Opsiyon girmediğimiz için default olarak bridge oluşturdu
  - `docker network create --driver=host myhost` -> host network'u oluşturur
  - `docker network ls` -> network driver'lar listelenir
- `docker network inspect mybridge` -> network driver'ımızı inceliyoruz
- `docker container run -d --name websunucu --net mybridge ozgurozturknet/adanzyedocker` -> websunucu adında bir container oluşturduk ve mybridge driver'ına bağladık
- `docker container run -d --name database --net mybridge ozgurozturknet/adanzyedocker` -> database adında bir container oluşturduk ve mybridge driver'a bağladık
- `docker network inspect mybridge` -> mybridge adındaki driver'a bakıyoruz ve bağlı olan container'ları görüyoruz
- `docker container exec -it websunucu sh` -> websunucu container'ına bağlanıyoruz
  - `ping database` -> database adındaki container'ı pingliyoruz ve bu sefer başarılı oluyoruz. Çünkü kendimiz bir bridge oluşturduk ve bunun üzerinde container'ları iletişime geçiriyoruz
- `docker network create --driver=bridge --subnet=10.10.0.0/16 --ip-range=10.10.10.0/24 --gateway=10.10.10.10 mybridge2`  -> istersek oluşturduğumuz bridge'in ağ özelliklerini belirleyebiliriz
- Kullanıcı tanımlı bridge'lerin en önemli özelliklerinden birisi de container'lar çalışırken bu bridge'lere bağlanabilirler
- Notasyon şu şekildedir:
  - `docker network connect <BridgeName> <ContainerName>`
  - `docker network connect mybridge2 database` -> yukarda oluşturduğumuz database container'ını mybridge2 adındaki network'e bağlıyoruz. Unutmayalım şu an hala çalışan bir container!
- `docker attach database` -> çalışan bir container olan database'e bağlanıyoruz
  - `ifconfig`  -> yazdığımızda göreceğiz ki **eth1** (mybridge2) gelmiş
- `docker network disconnect mybridge2 database` -> database container'ı mybridge2'den düşürüyoruz
- `docker network rm mybridge2` -> driver'ı sildik (bağlı bir container olmaması gerek)


## Logging Uygulama Günlükleri
- Docker araçlarını kullanarak loglara erişebiliriz
- `docker container run -d --name mycont1 ozgurozturknet/app1` -> arka planda mycont1 adında bir container oluştur
- `docker logs mycont1` -> mycont1 adındaki container'a ait loglar
- `docker container run -d --name mycont2 -p 80:80 nginx` -> container oluşturup 127.0.0.1 adresine gidip 3-5 kere refresh yapıyorum
  - `docker logs mycont2` -> container'a ait loglar


## Docker Stats ve Top
- Container; tek bir uygulamanın ya da servisin paketlenmiş halidir, sürekli olarak container içerisine girmeyiz.
- `docker top <ContainerName>` -> ilgili container içerisinde çalışan process'leri listeler
- `docker stats <ContainerName>` -> ilgili container'ın ne kadar kaynak kullandığını gösterir
  - `docker stats` -> tüm container'ların kaynak kullanımını gösterir



## Container Cpu ve Memory Limitleri
- **Eğer container oluştururken limitler koymazsak her container; sistem kaynaklarını sınırsız olarak kullanır**
- `docker container run -dit --name mycont alpine` -> bir container oluşturuyorum
  - `docker attach mycont` -> komutu ile container'a bağlanabilirim
  - `ctrl + p q` kombinasyonu ile arka plana atabilirim (çalışmaya devam eder)
- `docker container run -d --memory=100m alpine` -> max 100mb kullanabilecek bir container oluşturdu
- `docker container run -dit --name mytest --memory=100m alpine` -> mytest adında bir container oluşturuldu, max 100m kullanabilecek
  - `m` -> megabyte
  - `g` -> gigabyte
  - `k` -> kilobyte
  - `b` -> byte
- `docker container run -dit --memory=100m --memory-swap=200m alpine` -> eğer memory limiti aşılsa bile swap olarak 200m daha kullanabilecek
- `docker container run -dit --cpus="1.5" alpine` -> oluşturduğumuz container sistem içerisinde ne kadar core varsa onun sadece 1.5 tanesini kullanabilecek
- `docker container run -dit --cpus="1.5" --cpuset-cpus="0,3" alpine` -> sadece cpu 0 ve cpu 3'ü kullanabil dedik



## Environment Variables (Ortam Değişkenleri)
- İşletim sistemi bazında tanımlanan ve her yerden çağrılabilen değişkenlerdir
- Linux sistemlerde:
  - `printenv` ile tüm ortam değişkenlerini gösterir
  - `echo $<EnvName>` -> ilgili değişkenin değerini verir
  - `export <EnvName>=<EnvValue>` -> değişken tanımlamamızı sağlar
- `docker container run -it --env VAR1=deneme1 --env VAR2=deneme2 alpine sh` -> container'a 2 adet environment tanımladık. **Environment'ler büyük küçük harf duyarlıdır**. 
  - Container içerisinde `printenv` kullanırsak (alpine linux olduğundan) tanımladığımız değişkenleri görebiliriz
- `docker container run -it --env HOME alpine sh` -> eğer environment'e değer atamazsak sistem üzerinde o environment'i arar ve onun değerini atayıp container'a gönderir
- Eğer istersek bir dosya içerisinde tüm ortam değişkenlerini tanımlayıp tek komut ile de bunu gönderebiliriz
  - `touch env.list`
  - `echo "VAR1=deneme1" >> env.list`
  - `echo "VAR2=deneme1" >> env.list`
  - `echo "VAR3=deneme1" >> env.list`
  - Değişkenleri dosya içerisine ekledim
  - `docker container run -it --env-file ./env.list alpine sh` -> **--env-file** parametresi sayesinde liste olarak environment'leri gönderdik



# Image ve Registry

## Image İsimlendirme ve Tag Yapısı
- Docker imajına verilen isim aynı zamanda o imajın nerede depolandığını da belirtir
- Docker imaj adı ile container oluşturabildiğimiz gibi imaj ID'sini de kullanarak imajlardan container oluşturabiliriz
- Docker imaj yapısı:
  - `<RegistryURL>/<repository>:<tag>`
  - `docker.io/mebaysan/test:latest`
    - **docker.io** olmasının sebebi varsayılan olarak docker hub registry'den imaj çekilmesidir. Eğer istersek farklı registry'leri belirtebiliriz:
      - `docker image pull gcr.io/google-containers/busybox` -> örnek olarak google image registry'den busy box adındaki imajı çektik
- Tag'ler sayesinde tek repository içinde birden fazla versiyon tutabiliriz
- `docker image pull <ImageName>` -> komutu sayesinde local sistemimize bir imajı çekeriz
  - Tag belirtmediğimiz müddetçe default olarak `latest` tag'ı kullanılır
- `docker tag ubuntu mebaysan/deneme` -> çektiğimiz ubuntu imajını localde yeniden adlandırdık (öncesinde pull edilmeli tabii). Artık aynı imaja mebaysan/deneme diyerek erişebiliriz.



## Image oluşturma

### Dockerfile-1
- Docker imajı oluşturmak istersek öncelikle Dockerfile adında bir dosya oluşturmamız gerekir

#### Dockerfile Parametreleri
- Daha detaylı bilgi için [buraya](https://github.com/ozgurozturknet/AdanZyeDocker/blob/master/kisim5/bolum47/Dockerfile%20Talimatlari%20Aciklamasi.txt) Özgür hocanın repository'sine bakınız. Bu parametreleri ve açıklamalarını onun reposundan aldım.
- **FROM** 
  - Oluşturulacak imajın hangi imajdan oluşturulacağını belirten talimat. Dockerfile içerisinde geçmesi mecburi tek talimat budur. Mutlaka olmalıdır. 
  - Ör: FROM ubuntu:18.04
- **LABEL** 
  - İmaj metadata’sına key=value şeklinde değer çiftleri eklemek için kullanılır. Örneğin team=development şeklinde bir etiket eklenerek bu imajın development ekibinin kullanması için yaratıldığı belirtilebilir.
  - Ör: LABEL version:1.0.8
- **RUN**
  - İmaj oluşturulurken shell’de bir komut çalıştırmak istersek bu talimat kullanılır. Örneğin apt-get install xxx ile xxx isimli uygulamanın bu imaja yüklenmesi sağlanabilir. 
  - Ör: RUN apt-get update
- **WORKDIR**
  - cd xxx komutuyla ile istediğimiz klasöre geçmek yerine bu talimat kullanılarak istediğimiz klasöre geçer ve oradan çalışmaya devam ederiz. 
  - Ör: WORKDIR /usr/src/app
- **USER** 
  - gireceğimiz komutları hangi kullanıcı ile çalıştırmasını istiyorsak bu talimat ile onu seçebiliriz. 
  - Ör: USER poweruser
- **COPY**
  - İmaj içine dosya veya klasör kopyalamak için kullanırız
  - Ör: COPY /source /user/src/app
- **ADD**
  - COPY ile aynı işi yapar yani dosya ya da klasör kopyalarsınız. Fakat ADD bunun yanında dosya kaynağının bir url olmasına da izin verir. Ayrıca ADD ile kaynak olarak bir .tar dosyası belirtilirse bu dosya imaja .tar olarak sıkıştırılmış haliyle değil de açılarak kopyalanır. 
  - Ör: ADD https://wordpress.org/latest.tar.gz /temp
- **ENV**
  - Imaj içinde environment variable tanımlamak için kullanılır
  - Ör: ENV TEMP_FOLDER="/temp"
- **ARG**
  - ARG ile de variable tanımlarsınız. Fakat bu variable sadece imaj oluşturulurken yani build aşamasında kullanılır. Imajın oluşturulmuş halinde bu variable bulunmaz. ENV ile imaj oluşturulduktan sonra da imaj içinde olmasını istediğiniz variable tanımlarsınız, ARG ile sadece oluştururken kullanmanız gereken variable tanımlarsınız.
  - Ör: ARG VERSION:1.0
- **VOLUME**
  - Imaj içerisinde volume tanımlanamızı sağlayan talimat. Eğer bu volume host sistemde varsa container bunu kullanır. Yoksa yeni volume oluşturur. 
  - Ör: VOLUME /myvol
- **EXPOSE**
  - Bu imajdan oluşturulacak containerların hangi portlar üstünden erişilebileceğini yani hangi portların yayınlanacağını bu talimatla belirtirsiniz. 
  - Ör: EXPOSE 80/tcp
- **ENTRYPOINT**
  - Bu talimat ile bir containerın çalıştırılabilir bir uygulama gibi ayarlanabilmesini sağlarsınız.
  - Ör: ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
- **CMD**
  - Bu imajdan container yaratıldığı zaman varsayılan olarak çalıştırmasını istediğiniz komutu bu talimat ile belirlersiniz. 
  - Ör: CMD java merhaba
- **HEALTHCHECK**
  - Bu talimat ile Docker'a bir konteynerin hala çalışıp çalışmadığını kontrol etmesini söylebiliriz. Docker varsayılan olarak container içerisinde çalışan ilk processi izler ve o çalıştığı sürece container çalışmaya devam eder. Fakat process çalışsa bile onun düzgün işlem yapıp yapmadığına bakmaz. HEALTHCHECK ile buna bakabilme imkanına kavuşuruz.
  - Ör: HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ || exit 1
- **SHELL**
  - Dockerfile'ın komutları işleyeceği shell'in hangisi olduğunu belirtiriz. Linux için varsayılan shell ["/bin/sh", "-c"],Windows için ["cmd", "/S", "/C"]. Bunları SHELL talimatı ile değiştirebiliriz. 
  - Ör: SHELL ["powershell", "-command"]



### Dockerfile-2
- Aşağıdaki örnek senaryo için [buraya](./uygulamalar/Dockerfile-2/Dockerfile) bakabilirsiniz. Comment line'lar ile desteklenmeye çalışıldı.
  - Ubuntu 18.04 yüklü bir sistem kur
  - İşletim sistemini güncelle
  - Python kur
  - Uygulamayı sisteme kopyala
  - Sistem başladığında uygulamanın başlamasını ayarla
- `docker image build -t <ImageTag> -f <DockerFileName> .`
  - `docker image build -t mebaysan/ilkimaj .`
    - ilkimaj adında bir imaj oluşturur
    - **.** -> build context'i belirtiyoruz, eğer Dockerfile içinde COPY vb bir komut varsa bunlar için gerekli dosyaları sen bu çalıştığın klasör içerisinde ara
    - Dockerfile'ın olduğu dizinde bir adet Dockerfile olduğundan ekstra -f parametresini girmeme gerek yok
- `docker image ls` komutu ile sistemimizdeki mevcut imajları görebiliriz
- `docker container run mebaysan/ilkimaj` komutu ile oluşturduğumuz imajdan bir container oluşturduk ve ekran çıktısnı gördük
- `docker image history <ImageTag>` komutu ile imajın geçmişini görebiliriz
  - `docker image history mebaysan/ilkimaj`


### Dockerfile-3
- Localde oluşturduğumuz bir Docker imajını hub'a göndermek için öncelikle CLI'da login olmamız gerekir
  - `docker login` -> komutu ile login olabiliriz
- `docker image push <Image>` komutu ile localdeki istediğimiz image'i hub'a pushlayabiliriz
  - `docker image push mebaysan/ilkimaj` -> ilgili imajı pushlar



### Dockerfile-4
- Bir flask uygulamasını container'da çalıştırıyoruz. Daha detaylı bilgi için [buraya](./uygulamalar/Dockerfile-4/pythonapp/) bakabilirsiniz.
  - O klasördeki Docker imajı oluşturmak için:
    - `docker image build -t mebaysan/basitflaskimaj .` komutunu kullanıyorum
  - Oluşturduğum imajdan bir container oluşturmak için:
    - `docker container run --rm -p 80:5000 mebaysan/basitflaskimaj`
      - `--rm` -> container kapanınca otomatik olarak sil (bir daha silmekle uğraşmayalım)
      - `-p` -> bu makinaya 80 portundan gelen istekleri container'ın 5000 portuna yönlendir. Flask uygulaması 5000 portundan ayağa kalktığı için



### Linux Shell Trickleri
- `echo` komutu sayesinde bir değeri çıktı olarak verir
- `>` sayesinde bir komutun çıktısını yönlendiririz
  - `echo "deneme" > deneme.txt`
- `&` komutun sonuna eklersek komutu arka plana atar, arkada çalışır
- `|` 1. komutun çıktısını 2. komuta iletmeye sağlar
- `cat dosya | grep "naber"` dosya içeriğini okur çıktıyı grep'e yollar, içerisinde naber geçenleri filtreler
- `ls ; date` -> **;** sayesinde aynı anda birden fazla komut çalıştırmamızı sağlar
- `&&` -> VE
- `||` -> VEYA



### Dockerfile-5
- Healthcheck konusunu gördüğümüz dosyaya [buradan](./uygulamalar/Dockerfile-5/Dockerfile) bakabilirsiniz
- İmaj oluşturmak için:
  - `docker image build -t mebaysan/basichealthcheck .`
- Bu imajdan container oluşturmak için:
  - `docker container run -d --name mycont -p 80:80 mebaysan/basichealthcheck`
- ENV örneği ile container oluşturmak için:
  - `docker container run -d --name mycont --env KULLANICI="BAYSAN" --env HOSTNAME="MyHOST" -p 80:80 mebaysan/basichealthcheck` -> ortam değişkenleri set ederek container oluşturuyoruz, detaylı açıklama [Dockerfile](./uygulamalar/Dockerfile-5/Dockerfile) içerisindedir



### ADD ve COPY Farkı
- COPY -> belirttiğimiz dosya veya klasörü belirttiğimiz PATH'e atar
- ADD -> COPY ile aynı işlevi yapar fakat dosya kaynağının bir url olmasına izin verir. Aynı zamanda kaynak dosya bir .tar dosyası ise bunu hedef klasöre decompress olarak atar
- Detaylar için [buraya](uygulamalar/ADD-COPY-FARKI/Dockerfile) bakabilirsiniz
- Oluşturduğumuz Dockerfile ile imaj oluşturup aşağıdaki komut ile container oluşturuyoruz ve ADD sonucunu görüyoruz
  - `docker container run -it mebaysan/wpadd sh`



### ENTRYPOINT ve CMD Farkı
- CMD için [buraya](uygulamalar/ENTRYPOINT-CMD-FARKI/cmd/Dockerfile) bakabilirsiniz
- ENTRYPOINT için [buraya](uygulamalar/ENTRYPOINT-CMD-FARKI/entrypoint/Dockerfile) bakabilirsiniz
- CMD runtime'da değişebilirken ENTRYPOINT runtime'da değiştirilemez
- Her image'de en az 1 adet CMD veya ENTRYPOINT olmalıdır
- Eğer hem CMD hem de ENTRYPOINT varsa, CMD'de yazılanları ENTRYPOINT'e parametre olarak geçer


### Exec Form ve Shell Form Farkı
- Exec Form => `CMD ["python", "app.py"]`
- Shell Form => `CMD python app.py`
- Eğer komut Shell formunda girilirse bu imajdan container oluşturulduğu zaman bu komutu varsayılan shell'i çalıştırarak onun içerisinde çalıştırır. Bu nedenle container'da çalışan 1. process (pid 1) bu shell process olur
- Eğer komut Exec formunda girildiyse herhangi bir shell çalıştırılmaz ve komut direk process olarak çalışır. Container'ın pid 1'i o process olur
- Exec formunda çalıştırılan komutlar herhangi bir shell processi çalışmadığı için Environment Variable gibi bazı değerlere erişemezler.
- Eğer ENTRYPOINT ve CMD birlikte kullanılacaksa Exec form kullanılmalıdır. Shell formda CMD'deki komutlar ENTRPOINT'e parametre olarak aktarılmaz



### Multi-stage Build
- İmaj oluştururken, oluşturma aşamalarını kademelere bölmemize ve ilk kademede yarattığımız imaj içerisindeki dosyaları bir sonraki kademede oluşturacağımız imaja kopyalayabilmemize imkan sağlar. Bu sayede son imajımızın boyutunun küçülmesi sağlanır.
  - Ör: Bir Go uygulaması yazdık ve bunu derleyip sunucuya göndereceğiz, imaj oluştururken önce kodu derler çalışan bir halini hazırlarız. Sonrasında bu çalışan derlenmiş dosyayı 2. aşamada nihai imaja koyar ve imajı oluştururuz. Bu sayede gereksiz yere kaynak kodlarını veya go derleyicisini imaj içerisinde tutmamıza gerek kalmaz
- Örnekler için [buraya](uygulamalar/MultiStageBuild/Dockerfile) bakabilirsiniz



### ARG
- İmaj oluştururken kullanbildiğimiz bir diğer parametre ise ARG'dır. Sadece build aşamasında kullanılabilir ve sonrasında container ayağa kalktığında erişilemez. 
- Örnek için [buraya](uygulamalar/ARG/Dockerfile) bakabilirsiniz.
- Örnek bir Build Arg gönderimi
  - `docker image build -t mytest2 --build-arg VERSION=3.8.1 .`





### Docker Commit
- Dockerfile olmadan da docker imaj oluşturabiliriz
- Doğru kullanım Dockerfile olsa da tek yöntem bu değildir
- Öncelikle localimizde bir container oluşturur ve içerisinde gerekli kurulumları vs yaparız
- Ardından `docker commit <LocalContainerName> <NewImageTag>` komutu ile o container'dan bir imaj oluşturmuş oluruz
  - Ör: `docker commit localcont1 mebaysan/prodcont1:latest`
- `docker commit -c 'CMD ["command 1", "command 2"]' <LocalContainerName> <NewImageTag>` -> -c parametresi ile CMD, EXPOSE vb. komutları bu imaja ekleyebiliriz


### Docker Save-Load
- Save komutu sayesinde localde oluşturduğumuz bir imajı `.tar` dosyası olarak kayıt edebiliriz, ve internet erişimi olmasa bile (hub'dan çekemiyor demektir) sunucuya atabiliriz
  - `docker save <LocalImage> -o <filename>.tar`
    - `docker save mebaysan/test -o mebaysantestimaj.tar`
-  Load komutu ile `.tar` olarak gelen docker dosyasını sisteme imaj olarak yükleyebiliriz
   -  `docker load -i <TarFile>`
      -  `docker load -i mebaysantestimaj.tar`




### Registry
- Kendi ağımızda ücretsiz olarak bir docker deposu oluşturabiliriz
- Bunun için yine Docker Hub üzerinden :blush:  [registry](https://hub.docker.com/_/registry) imajını kullanacağız
  - Bu imaj sayesinde bir container çalıştıracağız. İçerisinde docker hub'a benzer bir yazılım çalıştırıyor. 5000 portundan ayağa kalkıyor.
  - `docker pull registry` imajı locale çekiyoruz
  - `docker container run -d -p 5000:5000 --restart always --name registry registry` 
    - restart parametresi container down olduğunda ne yapılacağını alır
      - `always` -> down olunca ne olursa olsun restart at
      - `no` -> down olunca bir şey yapma
      - `on-failure` -> sadece hata olur da kapanırsa yeniden başlat fakat biz elimizle manuel kapatırsak yeniden başlatma
      - `unless-stopped` -> manuel olarak stop edersek başlatmaz fakat geri kalan her durumda başlatır
    - 5000 portunu 5000 portuna publish ettik
    - registy adındaki container'ı registry imajından oluşturduk
  - `http://127.0.0.1:5000/` altında çalışmaya başlar
  - `http://127.0.0.1:5000/v2/_catalog` altında kayıtlı repoları gösterir,
  - `localcont1` adında bir imaj oluşturduğumuzu varsayalım
    - `docker push localhost:5000/localcont1`  -> localdeki imajı registry'e kayıt etti
    - `docker pull localhost:5000/localcont1` bu adrese erişebilen makinalar; komutu ile local registry'deki imajı çekebilir


# Compose ve Swarm


## Docker Compose
- Compsoe, çoklu Docker uygulamalarını oluşturmak ve çalıştırmak için kullanılan bir araçtır
- Compose ile uygulamanın hizmetlerini yapılandırmak için bir YAML dosyası kullanılır
- Daha sonra tek bir komut ile tüm hizmetleri yapılandırmaya başlar ve sistemi başlatırız
- Compose production için çok uygun bir araç değildir. Development aşaması için idealdir
- Compose, Docker Engine ile birlikte gelen bir araç değildir. Bunu ayrıca sisteme kurmamız gerekir.
  - Mac ve Windows sistemlerde Docker Desktop kurduğumuzdan bu uygulama ile birlikte kurulu olarak Compose gelmektedir
- `docker-compose.yml` dosyası içerisinde gerekli konfigürasyonlar tutulur
- `docker-compose up -d` ile .yml dosyası kullanılarak sistem ayağa kaldırılır
- `docker compose down` ile sistem kapatılır



### Docker Compose CLI
- docker-compose komutlarını kullanmak için yml dosyası ile aynı dizinde olmamız önemlidir
- `docker-compose up` -> **aynı dizindeki** docker-compose.yml dosyasını kullanarak sistemi başlatır
  - `docker-compose up -d` -> arka planda (detach) çalıştırır
  - docker objelerini isimlendirirken; sistemdeki diğer objeler ile çakışmasın diye docker-compose.yml dosyasının bulunduğu klasör adıyla başlayan isimlendirme yapar -> `klasorAdi_objeAdi`
- `docker-compose down` -> tüm servisleri (docker objeleri) kapatır ve siler
  - **her şeyi siler fakat volume'leri silmez**
- `docker-compose config` ile docker-config.yml dosyasını gösterir
- `docker-compose images` -> servislerin hangi imajlarla oluştuğunu gösterir
- `docker-compose logs` -> tüm servislerin loglarını listeler
- `docker-compose exec` -> compose ile oluşturduğumuz servisin içinde komut çalıştırır
  - `docker-compose exec <ServiceName> <Command>`




### Docker Compose Yaml Dosyası
- Detaylar için [buraya](uygulamalar/Compose&Swarm/intro/docker-compose.yml) bakabilirsiniz
- İlgili dizinde `docker-compose up -d` komutu sayesinde bu config dosyasından sistemi ayağa kaldırıyorum. `http://127.0.0.1/` adresine gittiğimizde başarılı bir şekilde wordpress kurulumunu görmüş olacağız



### Docker Compose Build
- İstersek **imajımızı** compose ayağa kalkarken oluşacak şekilde ayarlayabiliriz
- Bunun için docker-compose.yml dosyası ile aynı dizinde bir Dockerfile oluşturmamız gerekir. Dockerfile içerisinde istediğimiz imajı hazırlarız.
- Compose file içinde ise servisi hazırlarken image yerine `build` key'ini kullanırız ve build olacak Dockerfile'ı aynı dizinde olduğundan `.` ile belirtiriz
![compose-build](./static/compose/Screen%20Shot%202021-01-17%20at%2012.17.21.png)
- Daha detaylı bilgi için [buraya](https://github.com/ozgurozturknet/AdanZyeDocker/tree/master/kisim6/bolum68) bakabiliriz
- Kod içerisinde güncelleme yaptığımızda ise önce imajı güncellememiz gerekir. Bunun için de `docker-compose build` komutunu kullanır önce build aldığımız imajı güncelleriz. Sonrasında `docker-compose up` ile servisleri ayağa kaldırırız




## Container Orchestration
- Container Orchestration, containerların dağıtımını, yönetimini, ölçeklendirilmesini ve ağ oluşturulmasını otomatikleştirme işlemine denir
- Yüzlerce container'ı bir arada yönetmek için kullanılan yazılımlardır
- En bilinen araçlar:
  - **Swarm**
    - Docker engine ile gelen bir araçtır. Docker tarafından geliştirilmiştir
    - Bir Docker ana bilgisayar havuzunu tek bir sanal ana bilgisayara dönüştürür
  - **Kubernetes**
    - Neredeyse sektör standardı haline gelmiştir
    - Container uygulamalarının dağıtımını, ölçeklendirilmesini ve yönetimini otomatikleştirmek için oluşturulmuş açık kaynaklı bir sistemdir
    - Kolay yönetim ve keşif için uygulamayı oluşturan container'ları mantıksal birimler halinde gruplandırır
    - Google'da 15 yıldan fazla süreye sahip olan container deneyiminin açık kaynak topluluk dünyası üyelerinin tecrübelerine eklenerek oluşturulmuştur



### Docker Swarm
- Docker Engine'e gömülü bir container orchestration çözümüdür
- Bir docker ana bilgisayar havuzunu tek bir sanal ana bilgisayara dönüştürür
- Hostlar arasında şu portlar açık olmalıdır
  - TCP port 2377 -> Cluster yönetimi
  - TCP ve UDP port 7946 -> Nodelar arası iletişim
  - UDP port 4789 -> Overlay network



### Swarm Manager ve Worker Node
#### Manager Node
- Bir adet manager tarafından diğer hostlar yönetilir
- docker swarm init komutunu çalıştırdığımızda üzerinde bulunduğumuz host sistemi engine moddan swarm mode geçirir
- Swarm birden fazla manager node destekler ve bu sayede yüksek erişilebilirlik sağlar. Bir managerda sorun olursa diğer manager devreye girer ve iş yürümeye devam eder
- Manager nodelardan yalnızca 1 tanesi lider olarak seçilidir ve tüm yönetim lider tarafından yapılır. Diğer manager nodelar pasif durumdadır. Pasif manager nodelardan birine bir komut verip iş yapmasını istersek bu sadece proxy görevi görür ve komutu lider node'a iletir
- Birden fazla manager olan ortamlarda bir adet lider seçilmelidir. Swarm bunu otomatik halleder ve bunun için Raft Consensus algoritmasını kullanır. Raft algoritması; lider seçimi için kuralları belirlemeye yarar. Mesela ortamda 5 manager olan durumda bir şekilde lider olan nodea erişilemezse belirli bir zaman sonra kalan 4 node kendi aralarında oylama yaparak lider belirler. Artık swarm-cluster'ın yönetimi bu lider tarafından yapılır
- Raft algoritması yalnızca (n-1)/2 sayıda nodeun devre dışı kalmasını tolere edebilir. (5 node için max 2 hata tolere) Aksi halde yönetim altyapısı çalışmayacaktır
- Raft algoritmasının düzgün çalışabilmesi için ve lider seçiminin sorunsuz olması için ortamın her zaman tek sayıda manager nodela kurulmuş olması gerekir. 7'den fazla manager olduğu durumlarda ortamda daha fazla sorun çıkması muhtemeldir.

#### Worker Node
- Swarm cluster kurulur, ve cluster'a worker node olarak dahil ol deriz
- Manager node'dan sertifika çekerler ve emir beklerler
- İstediğimiz sayede worker node ekleyebiliriz

### Docker Swarm init
- Bu bölüm için [labs.play-with-docker.com](https://labs.play-with-docker.com/) adresine gidiyorum ve 5 adet instance oluşturuyorum
- Bir hostu swarm moda alacağız
  - `docker swarm init --advertise-addr 192.168.0.8` bu hostun swarm modunu aktif ediyoruz (192.168.0.8 play with docker tarafından kullandığımız hosta verilen IP)
    - advertise-addr -> host üzerindeki kullanmak istediğimiz network kartına ait adres
  - `docker swarm join-token manager` -> bir nodeu cluster'a manager olarak eklemek için gerekli olan komutu ve tokeni verecek
    - manager olarak eklemek istediğimiz node'a (hosta,makinaya,cihaza) gidip ilgili komutu yapıştırdığımızda cluster'a manager olarak eklenecek
  - `docker swarm join-token worker` -> bir node'u cluster'a worker olarak eklemek için gerekli olan komutu ve tokeni verecek
    - aynı şekilde worker olarak eklemek istediğimiz node'da çalıştırıp nodeu worker olarak cluster'a ekleyeceğiz
- swarm mode'u aktif ettiğimiz node'a gidip `docker node ls` komutu ile cluster hakkında bilgi sahibi olabiliriz
- swarm manager olan node'a gidip `docker service create --name test --replicas=5 -p 8080:80 nginx` komutu ile
  - adı test olan (--name)
  - 8080 portu 80 portuna publish edilmiş (-p)
  - nginx imajından
  - 5 adet container oluştur (--replicas=5)
- `docker service ps <ServiceName>` -> adı verilen service hakkında bilgi getirir




### Swarm Service
- Swarm service'lerinde 2 mod vardır.
  - **Replicated**
    - Aksini belirtmediğimiz sürece bu modda servis oluşturulur
    - Oluşturmak istediğimiz servisin kaç replica içereceğini belirtiriz
    - Swarm uygun nodelarda o sayıda replica oluşturur
  - **Global**
    - Servisin kaç replica içereceğini belirtmeyiz. Swarm altındaki her node üzerinde 1 replica oluşturur-
- `docker service create --name test nginx` -> nginx imajından test adında servis oluştur
  - replika sayısını belirtmediğimiz için 1 adet oluşturdu
- `docker service ls` -> service listesini görebiliriz
- `docker service ps test` -> test adındaki servisi altındaki tasklara ait detayları görebiliriz
- `docker service logs test` -> test servisi altındaki tüm container'lara ait logları gösterir
- `docker service inspect test` -> test servisine ait detaylar
- Swarm servislerinin en önemli özelliklerinden biri servislere hızlıca yeni replikalar oluşturabiliriz
  - `docker service scale test=3` ile test servisimize yeni 2 replika daha (zaten 1 replika ile oluşturmuştuk) ekliyor. Aynı şekilde azaltabiliriz de
- `docker service rm test` -> test adındaki servisi sileriz
- `docker service create --name myglb --mode=global nginx` -> servisi global modda oluşturur (her node üzerinde 1 replika)
- Update işlemi
  - update yapınca servisteki containerlardan birini siler yerine yeni güncellenmiş container'ı koyar ve sıradaki eski container'a geçer, siler yerine güncellenmişi koyar ve diğerine geçer ...
  - `docker service create --name websrv --network over-net -p 8080:5000 --replicas=10 mebaysan/basitflaskimaj`
    - adı websrv olan
    - over-net adında oluşturulmuş network driver'a bağlı
    - 8080:5000 port publish yapılmış olan
    - 10 adet replikaya sahip bir servis oluştur (play with docker platformu üzerinde over-net adında bir overlay network oluşturdum & 3 manager 2 workerlı bir cluster oluşturmuştum)
  - `--update-delay` -> bu parametre, güncellemeler arasında ne kadar bekleyeceğini belirtmemizi sağlar (bir containeri sil gyerien güncellenmişi koy parametre kadar bekle diğerine geç gibi)
  - `--update-parallelism` -> bu parametre sayesinde aynı kanda kaç container'ın güncelleneceğini söyleriz (aynı anda 2 container sil güncelle 5 sn bekle diğer ikisine geç gibi)
  - `docker service update --detach --update-delay 5s --update-parallelism 2 --image mebaysan/basitflaskimaj websrv`
    - arkaplanda (detach)
    - 5 saniyede bir (update-delay)
    - aynı anda 2 container olacak şekilde
    - mebaysan/basitflaskimaj adındaki imajdan
    - websrv adındaki servisi güncelle
- `docker service rollback --detach websrv` -> arkaplanda websrv adındaki servisi bir önceki haline getir


### Overlay Network
- Overlay network aslında bir network driver'dır
- Farklı merkezlerde bulunan nodelar sanki aynı merkezdeymiş gibi haberleşebilir
- Bir swarm cluster oluşturulduğu zaman ingress adında bir overlay network de otomatik olarak oluşturulur. Aksini belirtmediğimiz sürece oluşturduğumuz servisler bu overlay network'e bağlanır
- İstersek user defined overlay networkler de oluşturabiliriz
- Overlay networklerin temel yönetim katmanının haberleşme altyapısı encryptedir. Fakat buraya bağladığımız containerların birbirleriyle iletişimi varsayılan olarak encrypted değildir. Overlay network oluştururken `--opt encrypted` parametresini kullanarak bu trafiğin de encrypted olmasını sağlayabiliriz. Bu opt encrypted overlay netowrk trafiğini biraz yavaşlatacaktır.
- Aynı overlay network'e bağlı servislerin containerları birbirleriyle herhangi bir port kısıtlaması olmaksızın haberleşebilirler ve sanki aynı ağdaymış gibi çalışırlar.
- Swarm altında yaratılan servisler aynı overlay network üzerinde birbirlerine servis isimleriyle ulaşabilir. Docker burada hem dns çözümlemesi hizmeti hem de load balancing hizmeti sağlar
- Overlay network üzerinde port publsh de yapabiliriz. Swarm overlat networklerde ingress routing mesh destekler. Port publish edip Docker host üzerinden o porta erişirsek Docker o host üstünde o portun publish olduğu bir container bulunmasa bile bulunan bir host'a trafiği yönlendirecek ve cevap verecektir
- `docker network create -d overlay over-net` -> adı over-net olan overlay bir network oluştur



# Docker Secret
- Conteinar'larda plain text olarak tutmamızın güvenlik zafiyeti oluşturabileceği username ve password gibi verileri secret objeleri şeklinde encrypted olarak transfer edebiliriz
- Docker Swarm, aynı volume ve container'lar gibi birer Docker objesidir
- Swarm altında bir isim ve değer şeklinde secretler oluşturabiliriz. Bu objeler Swarm cluster'ın raft deposunda encrypted olarak saklanıyor. Bu secretleri istediğimiz servislere bağlayabiliyoruz. Secret, Swarm manager'dan servisin çalışacağı container'a transfer ediliyor (encrypted). Bağlandığı service container'larının /run/secrets klasöründe erişilebilecek şekilde map ediliyor. O container bu path içindeki objeye erişiyor ve kullanıyor
- `docker swarm init` diyerek swarm cluster oluşturuyoruz
- Secret oluşturmanın iki yolu vardır. Birincisi metin dosyalarından oluşturmaktır
  - username.txt ve password.txt dosyalarımın olduğu dizine gidiyorum. `docker secret create <SecretName> <FilePath>` komutu ile secret oluşturuyoruz
    - `docker secret create username ./username.txt`
    - `docker secret create password ./password.txt`
  - `docker secret ls` ile Secret'leri listeleriz
  - `docker secret rm <SecretName>` ile Secret sileriz
  - `docker secret inspect <SecretName>` ile Secret inceleyebiliriz
- İkinci yöntem ise terminalden oluşturmaktır
  - `echo "<SECRET>" | docker create <SecretName> -`
    - `echo "Bu bir secrettir" | docker secret create deneme -`
- `docker service create -d --name secrettest --secret username --secret password mebaysan/basitflaskimaj` -> bir servis oluşturduk ve içerisine cluster'daki secretları gönderdik
- `docker exec -it 0c sh` -> oluşturduğumuz container'a bağlandık
- `cd /run/secrets` -> secretların tutulduğu dizine gidiyoruz ve orada oluşturduğumuz secret isimleriyle dosyalar görüyoruz. Bunların değerleri de secretların değerleridir
- Secret'i güncellemek istersek service'i güncellememiz gerekir
- Önce cluster'da yeni bir secret oluşturuyoruz (varsayalım ki username2 adında olsun)
  - `docker service update --secret-rm username --secret-add username2 secrettest`
    - `--secret-rm username` secrettest servisindeki username adlı secreti sil
    - `--secret-add username2` ilgili servisteki username secret'in yeni değerini cluster'daki username2 secretinin değeri olarak güncelle






