FROM  mcr.microsoft.com/dotnet/sdk:3.1 as build
WORKDIR /app
COPY *.csproj .
RUN dotnet restore
COPY . . #tüm dosyalarý app içerisine taþýnmasýna yarýyor.
RUN dotnet publish AspnetCoreMvc.csproj -c Release -o out
FROM mcr.microsoft.com/dotnet/aspnet:3.1
WORKDIR /app
COPY --from=build /app/out .
ENV ASPNETCORE_URLS="http://*:4500"
ENTRYPOINT ["dotnet","AspnetCoreMvc.dll"]

#docker-ignore: taþýnmasýný istemediðimiz dosyalarý dahil etmememizi saðlar.