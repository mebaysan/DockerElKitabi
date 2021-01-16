FROM python:alpine
# base image'den bir imaj oluştur
WORKDIR /myapp
# dizin değiştir
RUN echo "print('Merhaba dünya ben container uygulamasiyim')" > "app.py"
# app.py dosyası oluştur ve içerisine kodu yaz
ENTRYPOINT ["python","app.py"]
# app.py dosyasını çalıştır
# bu container başlatıldığında bu komutu çalıştır, runtime'da farklı bir şey denmesi entrypoint'i bağlamaz
