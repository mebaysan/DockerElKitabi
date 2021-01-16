# daha detaylı: https://github.com/ozgurozturknet/AdanZyeDocker/blob/master/kisim5/bolum52/hello-docker/Dockerfile
FROM nginx:latest

LABEL maintainer="@mebaysan"
LABEL version="1.0"
LABEL name="merhaba-docker"
# bu bilgielre container içinden erişemeyiz


ENV KULLANICI="Dunyali"


RUN printf "deb http://archive.debian.org/debian/ jessie main\ndeb-src http://archive.debian.org/debian/ jessie main\ndeb http://security.debian.org jessie/updates main\ndeb-src http://security.debian.org jessie/updates main" > /etc/apt/sources.list
# bu imajda uyumluluk sorunları var ve o nedenle update olmuyor 
# Bu url'leri sources.list'e ekleyerek update olabilmesini sağlıyoruz



RUN apt-get update

RUN apt-get install curl -y
# debian-slim'de bir çok olmadığından; curl kullanmamız gerektiği için curl yüklüyoruz


WORKDIR /usr/share/nginx/html
# nginx web sayfalarını /usr/share/nginx/html folder'ında barındırıyor, bu folder'a geçiyoruz


COPY index.html /usr/share/nginx/html

# sistemin düzgün çalıştığını ve nginx daemon'ının web sitesini publish etmekte bir sorun yaşamadığını test ediyoruz
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 CMD curl -f http://localhost/ || exit 1
# interval -> 30 saniyede bir çalıştır
# timeout -> 10 saniye bekle
# start-period -> 5 saniye bekle sonra healthcheck çalıştır
# retries -> kaç deneme ard arda başarısız olursa bu container'ı unhealthy olarak işaretle
# CMD curl -f http://localhost/  -> çalışacak komut / localhost'a http üzerinden erişmeye çalışıyorum, eğer ulaşamazsan process'i öldür ve hata dön (exit 1)
# Bu komutun meali = container başladıktan sonra 5 sn bekle (start-period), sonra healthcheck komutunu çalıştır (CMD ..), 
#   bu komutun sonucunu not et, sonra 30 sn bekle(interval), container çalıştığı sürece bunu tekrar et eğer bu işlemler sırasında
#   ardı ardına 3 deneme sonunda olumsuz sonuç alırsan container'ı unhealthy olarak işaretle


CMD sed -e s/Kullanici/"$KULLANICI"/ index.html > index1.html && sed -e s/Hostname/"$HOSTNAME"/ index1.html > index.html ; rm index1.html; nginx -g 'daemon off;'
# index.html'i tara, orda Kullanici adında bir şey görürsen onu $KULLANICI (ENV) ile değiştir ve bu dosyayı index1.html olarak yaz
# index1.html'i tara Hostname görürsen onu $HOSTNAME (ENV) ile değiştir ve bu dosyayı index.html olarak yaz
# index1.html i sil
# nginx daemon'u çalıştır
# -!! burada env değerlerini almamızın sebebi; CMD container oluştuktan sonra çalışır, yani container oluşurken parametre olarak gönderdiğimiz --env değerlerini burada yakalayabiliriz

