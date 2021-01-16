FROM python:alpine
# base image'den bir imaj oluştur
WORKDIR /myapp
# dizin değiştir
RUN echo "print('Merhaba dünya ben container uygulamasiyim')" > "app.py"
# app.py dosyası oluştur ve içerisine kodu yaz
CMD ["python","app.py"]
# app.py dosyasını çalıştır
# bu container başlatıldığında bir şey denmemişse CMD komutunu (python app.py) çalıştır
# eğer runtime'da farklı bir komut çalıştırılması söylenirse onu çalıştır
