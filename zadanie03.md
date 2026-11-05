# zadanie 1
docker inspect --format '{{json .Config.Labels}}' jakub-frontend
{"autor":"Jakub Gajewy","data":"2026-11-05","maintainer":"90028969+GajewyJ@users.noreply.github.com"}

docker history jakub-frontend:v1
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
96239caa48c2   16 minutes ago   EXPOSE [80/tcp]                                 0B        buildkit.dockerfile.v0
<missing>      16 minutes ago   RUN /bin/sh -c htmlhint /usr/share/nginx/htm…   4.1kB     buildkit.dockerfile.v0
<missing>      16 minutes ago   LABEL autor=Jakub Gajewy data=2026-11-05        0B        buildkit.dockerfile.v0
<missing>      16 minutes ago   COPY . /usr/share/nginx/html # buildkit         32.8kB    buildkit.dockerfile.v0
<missing>      17 minutes ago   RUN /bin/sh -c apk add --no-cache nodejs npm…   99.3MB    buildkit.dockerfile.v0
<missing>      17 minutes ago   LABEL maintainer=90028969+GajewyJ@users.nore…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c set -x     && apkArch="$(cat …   52.4MB    buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV ACME_VERSION=0.4.1                          0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV NJS_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV NJS_VERSION=1.0.2                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY 30-tune-worker-processes.sh /docker-ent…   16.4kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY 20-envsubst-on-templates.sh /docker-ent…   12.3kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY 15-local-resolvers.envsh /docker-entryp…   12.3kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY 10-listen-on-ipv6-by-default.sh /docker…   12.3kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY docker-entrypoint.sh / # buildkit          8.19kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c set -x     && addgroup -g 101…   5.75MB    buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV DYNPKG_RELEASE=1                            0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV PKG_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV NGINX_VERSION=1.31.8                        0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ADD alpine-minirootfs-3.24.3-x86_64.tar.gz /…   9.08MB    buildkit.dockerfile.v0

Warstwy od samego dołu (z czasem 2 weeks ago, od ADD alpine-minirootfs... do LABEL maintainer=NGINX...) pochodzą z obrazu bazowego NGINX Alpine, natomiast górne 5 warstw (16-17 minutes ago) wygenerował Dockerfile (instrukcje LABEL, RUN, COPY, EXPOSE). Instrukcje takie jak LABEL, EXPOSE czy ENV odpowiadają wyłącznie za zmianę metadanych kontenera, dlatego ich rozmiar wynosi 0B, w przeciwieństwie do instrukcji instalujących pakiety (RUN) lub dodających pliki (COPY). W wyjściu docker inspect widać wyłącznie wartość maintainer, ponieważ klucze etykiet w Dockerze są unikalne – zdefiniowanie klucza LABEL maintainer=... w Dockerfile nadpisało wcześniejszą wartość z obrazu bazowego NGINX.

# zadanie 2
build.txt z kontenera 1: Thu Nov  5 08:11:27 UTC 2026
build.txt z kontenera 2: Thu Nov  5 08:11:27 UTC 2026

docker ps
CONTAINER ID   IMAGE               COMMAND                  CREATED              STATUS              PORTS                      NAMES
86ce6103c985   jakub-frontend:v1   "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   frontend2
c640089bf56d   jakub-frontend:v1   "/docker-entrypoint.…"   2 minutes ago        Up 2 minutes        0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   frontend1

Czas w build.txt jest taki sam, ponieważ jest to czas utworzenia obrazu a nie uruchomienia kontenerów

# zadanie 3
root   /usr/share/nginx/html;

Linia ta określa domyślny katalog główny (root directory), z którego serwer Nginx serwuje pliki statyczne dla przychodzących żądań HTTP, dlatego skopiowanie tam naszych plików sprawia, że są one automatycznie udostępniane przez kontener.

# zadanie 4
docker exec frontend1 ls /usr/share/nginx             
html
index.html
script.js
style.css

docker exec frontend2 ls /usr/share/nginx               
html

htmlhint sprawdził domyślny plik /usr/share/nginx/html/index.html pochodzący z obrazu bazowego Nginx

Instrukcja COPY . /usr/share/nginx/ skopiowała pliki projektu (index.html, script.js, style.css) piętro wyżej, bezpośrednio do katalogu /usr/share/nginx/ (co widać na kontenerze frontend1). W podkatalogu /usr/share/nginx/html nadal znajdował się fabryczny, poprawny składniowo plik index.html z obrazu Nginx. Wynik walidacji był pozytywny, więc build przeszedł