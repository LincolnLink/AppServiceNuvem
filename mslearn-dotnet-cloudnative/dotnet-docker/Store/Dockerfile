FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /app

# Copiar arquivos do projeto frontend
COPY Store/Store.csproj Store/
WORKDIR /app/Store
RUN dotnet restore

# Copiar o restante dos arquivos
COPY Store/ .

# Publicar a aplicação
RUN dotnet publish -c Release -o /out

FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
EXPOSE 32000

# Copiar os arquivos publicados
COPY --from=build /out .

# Verifique se o arquivo DataEntities.dll está disponível no frontend
COPY --from=productservice /app/public/DataEntities.dll /app/public/

# Iniciar a aplicação
ENTRYPOINT ["dotnet", "Store.dll"]
