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
- 
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

