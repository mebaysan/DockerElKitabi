# hangi base imajdan container kuracaksın
FROM ubuntu:18.04 
# container **oluşturulurken** çalıştırılacak komut
RUN apt-get update -y
# sisteme python kur
RUN apt-get install python3 -y
RUN apt-get install python3-pip -y
# myapp adındaki path'e geç eğer yoksa oluştur
WORKDIR /ilkuygulama
# /myapp -> host üzerinde bu dosyalar nerede duruyor, Dockerfile ile aynı dizindeki proje klasörü
# . -> imaj içerisinde bu dosyaları nereye koyacağız, zaten yukarıda /ilkuygulama adındaki path'e geçmiştik
COPY /myapp .
# container **oluşturulduktan sonra** çalıştırılacak komut (app.py dosyasını çalıştıracak)
CMD ["python3", "app.py"]