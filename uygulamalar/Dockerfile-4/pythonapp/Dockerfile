FROM python:alpine
# oluşturduğumuz ilk Dockerfile'da önce ubuntuyu kurup sonra içinde python kurmuştuk. 
# Aslında buna gerek yoktur. Python'un resmi imajını arıyoruz ve 
# bize işletim sisteminin üzerine kurulmuş olan bir imaj geliyor, bu sayede tek tek ikisini
# kurmak ile uğraşmıyoruz
COPY . /app
# . -> bu klasördeki tüm dosyaları, imaj içindeki app folderine kopyala
WORKDIR /app
# imaj içindeki app klasörüne geçiyoruz
RUN pip install -r requirements.txt
# gerekli paketleri yüklüyoruz
EXPOSE 5000
# konteynır dışından konteynıra 5000 portundan ulaşılacağını belirtiyoruz
CMD ["python","app.py"]
# python dosyasını çalıştırıyoruz