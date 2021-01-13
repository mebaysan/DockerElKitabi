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


# Giriş

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


## Container Temelleri-3
- `docker container run --name websunucu -p 80:80 -d ozgurozturknet/adanzyedocker` 
  - websunucu adında bir container'ı adanzyedocker imajından oluşturdu ve arka planda çalıştırdı (127.0.0.1'e giderek görebilirsiniz)
  - `docker container exec -it <ContainerName> <command>`
    - `it` -> interaktif olarak container'a bağlan demek
    - `docker container exec -it websunucu sh`
      - websunucu container'ına bağlan ve sh komutunu çalıştır (shelle bağlanır)


## Docker Katmanlı Dosya Sistemi Yapısı
- Docker depolama alt yapısında **union file system** adı verilen bir yapı kullanır
- Docker imajları mevcut bir base imaj üzerine inşa edilir
- Bu imaj üzerine yapılan her değişiklik birer katman olarak eklenir ve bu katmanlar birer dosya olarak saklanır
- 