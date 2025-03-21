# PAwChO-lab3
Pliki załączone do sprawozdania nr 3
# Proces utworzenia obrazu na systemie Linux


# 1. Utworzenie katalogu i certyfikatu
```bash
mkdir -p certs
openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -addext "subjectAltName = DNS:myregistry.domain.com" -x509 -days 365 -out certs/domain.crt
```
# 2. Utworzenie obrazu z certyfikatem
```bash
docker run -d \
  --restart=always \
  --name registry \
  -v "$(pwd)"/certs:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -p 443:443 \
  registry:2
```
# 3. Pobranie i otagowanie obrazu Ubuntu
```bash
docker pull ubuntu:16.04
docker tag ubuntu:16.04 myregistry.domain.com/my-ubuntu
```
# 4. Skopiowanie certyfikatu do odpowiedniego katalogu
```bash
sudo mkdir -p /etc/docker/certs.d/myregistrydomain.com:443
sudo cp certs/domain.crt /etc/docker/certs.d/myregistrydomain.com:443/ca.crt
```
# 5. Dodanie wpisu do pliku /etc/hosts
```bash
sudo nano /etc/hosts
```
# Dodanie linii:
```bash
# 127.0.0.1 myregistry.domain.com
```
# 6. Dodanie certyfikatu do lokalnych certyfikatów
```bash
sudo cp certs/domain.crt /usr/local/share/ca-certificates/myregistry.crt
```
# 7. Aktualizacja certyfikatów
```bash
sudo update-ca-certificates
```
# 8. Sprawdzenie katalogu repozytoriów
```bash
curl -X GET https://myregistry.domain.com/v2/_catalog
```
# 9. Otagowanie i przesłanie obrazu do registry
```bash
docker tag ubuntu:16.04 myregistry.domain.com/my-ubuntu
docker push myregistry.domain.com/my-ubuntu
```
# 10. Otagowanie obrazu registry:2 i udostępnienie
```bash
docker tag registry:2 extremical/lab3
docker push extremical/lab3
```
