# Definicja wersji SDK, którą chcemy pobrać
ARG NEXO_VERSION="57_0_2"

# Builder oparty na Linuksie: pobiera i rozpakowuje pakiet nexoSDK
FROM ubuntu:24.04 AS builder
ENV DEBIAN_FRONTEND=noninteractive

# Zainstaluj narzędzia do pobierania i rozpakowywania
RUN apt-get update \
    && apt-get install -y --no-install-recommends curl p7zip-full ca-certificates \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /tmp

# Pobranie InsERT nexoSDK (plik .exe self-extracting) i zapis jako sdk.exe
RUN curl -fSL "https://ftp.insert.com.pl/pub/aktualizacje/InsERT_nexo/nexoSDK_${NEXO_VERSION}.exe" -o sdk.exe

# Rozpakowanie przy użyciu p7zip. 7z potrafi rozpakować wiele self-extracting .exe
RUN mkdir -p /sdk \
    && 7z x sdk.exe -o/sd k -y || true

# Jeśli p7zip wypakował katalog, znajdź pierwszy katalog i nazwij go 'nexoSDK'
RUN first=$(find /sdk -mindepth 1 -maxdepth 1 -type d | head -n1) \
    && if [ -n "$first" ]; then mv "$first" /sdk/nexoSDK || true; fi || true

# Usuń pobrany plik instalacyjny
RUN rm -f sdk.exe || true

# Finalny obraz — Linux z .NET SDK (wersja linuxowa)
FROM mcr.microsoft.com/dotnet/sdk:8.0

# Skopiuj zawartość katalogu Bin z rozpakowanego SDK do obrazu finalnego
COPY --from=builder /sdk/nexoSDK/Bin /InsertNexoSDK

# Ustaw prawa do odczytu/wykonywania potrzebne w Linuksie
RUN chmod -R a+rX /InsertNexoSDK || true

# Domyślny katalog roboczy
WORKDIR /app