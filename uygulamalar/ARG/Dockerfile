FROM ubuntu:latest

WORKDIR /sallamasyon

ARG VERSION=3.7.1
# build olurken VERSION adında bir environment gelecek, bu sayede aşağıdaki gibi
# dinamik olarak yönetim yapabileceğiz
# varsayılan olarak değer atadık, eğer bu parametre gelmezse bu değeri kullanır
ADD https://www.python.org/ftp/python/${VERSION}/Python-${VERSION}.tgz .

CMD ls -al
# docker image build -t mytest2 --build-arg VERSION=3.8.1 . -> bu şekilde imaj oluştururken kullanabiliriz
