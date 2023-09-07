# ASPNETCORE-DOCKER-LEARN
Bu eğitimde udemy tarafında izlediğim temel anlamda bilgiler edindiğim ve notlar çıkardığım kurs içeriği bulunmaktadır.
docker build -t udemyconsoleapp:v1 .

".":bulunduğu dizini belirtmek için kullanılır.Eğerki komut istemcisinde dll bulunan dosyada isek "." kullanmak yeterli olacaktır.
":v1": tag vermek istersek böyle bir kullanım mevcut eğerki vermezsek tag latest olarak belirlenecektir.
"-t":isimlendirme yapmak için kullanılır.

docker images

-image dosyalarımızı görüntülemek için kullanılan komuttur.

docker create --name udemyconsole_container udemyconsoleapp

"--name": container isimlendirmek için kullanılır
"create": container oluşturmak için kullanılan komut
docker ps -a
"ps": tek başını kullanıldığında çalışan dockerları listeler
"-a": var olan containerları listeler


docker start udemyconsole_container (idsini kullanarakta başlatılabilir ilk 3 hanesini yazmak yeterlidir)
başlatmak için kullanılır

docker stop udemyconsole_container
durdurmak için kullanılır

docker attach f44 
ilgili containerdeki çıktıları görmek için kullanılır.


docker run --name udemyconsole_container2 udemyconsoleapp

"run" create,start ve attach işlemlerini içeren bir komuttur. sırasıyla gerçekleştirir.

docker rm ab3

"rm":container silmek için kullanılır.

docker run --rm --name myapp udemyconsoleapp

"--rm":containerin oluşturulup çalıştırdıktan sonra stop komutu verildiğinde silinmesini sağlar.

docker rmi udemyconsoleapp

"rmi": ilgili image silme işlemini gerçekleştirir.


docker rm udemyconsoleapp --force

"--force": çalışan bir container silmek istiyorsak bu komut kullanılır.

docker rmi udemyconsoleapp --force
burada image silerken bağlı container var ise force kullanmak gerekiyor ve containerin durmuş olması gerekiyor. çalışır durumda ise force komutu işlem yapmaz.

docker pull mcr.microsoft.com/dotnet/sdk:6.0
Docker görüntülerini (images) Docker Hub veya başka bir Docker Registry'den çekmek (indirmek) için kullanılır.

docker tag busybox umutatras/udemyrepository:v1

tag ile busybox images yeni bir tag veriliyor.

docker push umutatras/udemyrepository:v1

"push": imageı docker huba yüklemek için kullanılan bir komut. docker hub'ın bize verdiği adrese göre yapmamız gerekir.


docker run -d -p 5000:80 udemyaspcoremvc:v1

"-d": deattach işlemi yapar yani consolede bilgiler gözükmez.

docker run -d --name myaspcontainer -p 5000:80 udemyaspcoremvc:v1

"--name": verdiğimiz isimle container oluşturulur.
"-p":port yönlendirmesi

.dockerignore:**/bin/ #projemdeki tüm klasörleri ara ve bin dosyasını kopyalama dahil etme demek.

docker build --no-cache -t udemyaspcoremvc:v4 . 

"--no-cache": cachete bulunan dosyadan bir image yaratmamasını baştan image yaratır.

docker rm id id --force
2 tane ayrı container veya image ard arda yazarak silinebilir.

docker run -d -p 5000:4500 --mount type=bind,source"dosyayolu",target="/app/wwwroot/images" containerid

"-d":deattach olmasını sağlıyor
"-p":port ayarlaması
"--mount": projelermizde kaydettğimiz dataları başka bir containerde görüntüleyebilsin diye kullanılan bir parametre.

mount ve volume arasındaki fark
volume docker tarafında yönetilebilir bir dosya yapısı sunuyor ve bulut sistemlere taşınması sağlanabilir.

docker volume create images:dockerda volume oluşturma komutu

docker run -d -p 5000:45000 --name mycon -v images:dosyayolu/app/wwwroot/images

"-v":volume ile birlikte çalışmasını sağlar 

docker volume rm images:volume silme komut


docker run -p 5000:4500 --env ASPNETCORE_ENVIROMENT=DEVELOPMENT --name mycon cd0

"--env": uygulamamızın hangi modda ayağa kalkacağını belirleyen komut.

docker images -f "dangling=true"
"-f":filtreleme komutu
<none> imagları listeleyen komut

docker rmi $(docker images -f "dangling=true" -q)

"-q":images ait idleri dönen komut parametresi

FROM mcr.microsoft.com/dotnet/aspnet:3.1 AS base

"AS":bu parametre ile isimlendirme yapabiliriz.

EXPOSE:Containerların birbirleri ile haberleşmesini sağlar.
EXPOSE 80= 80 numaralı portu dışarıya açık yapar.

docker version:dockerin versiyonu verir


-------------------------------DOCKER COMPOSE-----------------------------------------------------

docker compose version:bu komut satırı docker compose versiyon sürümünü verir.


FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base //runtime image sürümü belirtme
WORKDIR /app //app klasörünü oluşturma
EXPOSE 80 //expose oluşturulan image docker içersisindeki farklı container,imagelara  bağlantı izni verilmesini sağlar eğerki olmazsa sadece o container tarafından erişelebilir.
EXPOSE 443 //https portunu açmak için gereken kod 

FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build //sdk image için gerekli komut
WORKDIR /src //src klasörüne yüklenmesi için dosya oluştur komutu
COPY ["MicroService1.Api/MicroService1.Api.csproj", "MicroService1.Api/"] //srcnin altında MicroService1.Api/klasörü içerisine .csproj kopyala
RUN dotnet restore "MicroService1.Api/MicroService1.Api.csproj"  //restore projemize yüklediğimiz kütüphane vb gibi dosyaların çalışmasını sağlayacak komut



FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src
COPY ["MicroService1.Api/MicroService1.Api.csproj", "MicroService1.Api/"]
RUN dotnet restore "MicroService1.Api/MicroService1.Api.csproj"
COPY . .
WORKDIR "/src/MicroService1.Api"
RUN dotnet build "MicroService1.Api.csproj" -c Release -o /app/build

Buradaki komutlarda direkt docker build -t microservice1 . komutunu yazarsak hata alırız ama compose ile çağırsaydık böyle bir hata almazdık 
Buradaki düzeltilmesi gereken nokta MicroService1.Api/ klasörünün aslında projeye eklenmemiş olması eğer buradaki noktayı silersek problem düzelecektir. docker için compose tarafında silmemize gerek yok.

Yani yeni komut satırları böyle olmalıdır;
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src
COPY ["MicroService1.Api.csproj", "MicroService1.Api/"]
RUN dotnet restore "MicroService1.Api/MicroService1.Api.csproj"
COPY . ./MicroService1.Api/
WORKDIR "/src/MicroService1.Api"
RUN dotnet build "MicroService1.Api.csproj" -c Release -o /app/build

İLK COPYNİN AMACI YENİ BİR PAKET EKLEMESİ OLMADIĞI ZAMAN CACHETEN GELEN DOSYALAR İLE OLUŞTURUP HIZ KAZANDIRIYOR 

FROM build AS publish 
RUN dotnet publish "MicroService1.Api.csproj" -c Release -o /app/publish /p:UseAppHost=false

"/p:UseAppHost=false" Bu komut exe oluşturulmamasını sağlıyor.

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "MicroService1.Api.dll"]

yukarıdaki komutta özetle base imageından bir örnek al app klasörüne at copyle  ve entrypoint ile çalıştır.


docker compose build : imageların oluşturulması için gerekli komut satırı

docker compose create : eğer ki imagelar oluşturulmuş ise containerların oluşturulması sağlanır.

docker compose start: oluşturulan containerların ayağa kalkmasını sağlayan komut satırı 

docker compose stop : containerların durdurulmasını sağlayan komut satırı

docker compose rm : containerları silmeyi sağlayan komut satırı 

docker compose up: containerları oluşturur ve ayağa kaldırır

docker compose down: containleri durdurur ve siler(volume gibi kısımlarıda siler)

docker compose stop servisadı :servisadına ait uygulamayı durdurur belleği temizler.

docker compose pause servisadı : servisadına ait uygulamayı durdurur. bellekteki değerler durur 

docker compose exec servisadi /bin/bash: komut çalıştırmak için kullanırız (pwd vb)

docker compose up --scale servisadı=3 --scale servisadı2=2 : servislere ait belirtilen adet kadar uygulama ayağa kalkar

docker compose push: docker huba containerları göndermek için kullanılan komut.

docker compose up sqlserver: sql server image çekme komutu



















