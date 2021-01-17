FROM mcr.microsoft.com/java/jdk:8-zulu-alpine AS derleyici
# derleyici adında bir stage oluşturduk, bu bir aşama oldu ve diğer FROM komutuna kadar olan kodlar bu stage'e aittir

COPY /source /usr/src/uygulama

WORKDIR /usr/src/uygulama

RUN javac uygulama.java
# java kodunu derledik

# yukarıdaki tüm kodlar buraya kadar bir stage'dir


FROM mcr.microsoft.com/java/jre:8-zulu-alpine

WORKDIR /uygulama

COPY --from=derleyici /usr/src/uygulama .
# derleyici stage'indeki /usr/src/uygulama altındaki dosyaları, buradaki /uygulama ya kopyala

CMD ["java","uygulama"]


# bu sayede nihai imaj 2. From'dan oluştu ve uygulamayı çalıştırmak için gereksiz
# olan jdk (derleyici kiti)'ı bu son imaja eklemek zorunda kalmadık
