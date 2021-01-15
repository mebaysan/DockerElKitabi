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


# Giriş
Docker öğrenirken aldığım notlar. 

Genellikle Özgür Öztürk'ün [Docker eğitimi](https://www.udemy.com/course/adan-zye-docker/)ni izlerken not aldım.

Elimin altında Türkçe kaynak olması için [Docker ve Konteyner Uygulamaları](https://www.amazon.com.tr/Docker-Konteyner-Uygulamalar%C4%B1-Deniz-Parlak/dp/6058060761/ref=sxts_sxwds-bia-wc-rsf-lq2a1_0?__mk_tr_TR=%C3%85M%C3%85%C5%BD%C3%95%C3%91&cv_ct_cx=docker&dchild=1&keywords=docker&pd_rd_i=6058060761&pd_rd_r=adeb9eba-433e-4a3e-86e0-e81304013581&pd_rd_w=yeCbD&pd_rd_wg=E8mQH&pf_rd_p=1afd11f1-1dc6-4ec0-a331-2a221d78fd95&pf_rd_r=ZQNNXBF5BV4D5Z92G71P&psc=1&qid=1610552031&sr=1-1-569cd1f4-72a6-4f1d-b3c5-cf7f1ae39fbb) kitabını almıştım ve bazen buradan faydalandım.
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
-  