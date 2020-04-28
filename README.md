# Tietojärjestelmien perusteet Grafana docker-compose  

Tähän sisältönä tietojärjestelmien perusteet toteutuksen grafana asennus. Asennus toteutetaan docker kuvana, jossa käytetään grafana:latest sisältöä  

## Toiminta  

Tämä tarvitsee toimiakseen docker alustan sekä docker-compose työkalun. Tarvitaan myös toimiva reverse proxy rakenne nginx/apache2 ympäristöön.  

## Docker asennus:

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc  
```

Poistetaan ensin kaikki olemassa olevat docker viittaukset koneesta  

```bash
sudo apt-get update  
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common  
```

Päivitetään ja asennetaan uusimmat apuohjelma apt ympäristöön sekä paketinhallintaan  

```bash
curl -fsSL <https://download.docker.com/linux/ubuntu/gpg> | sudo apt-key add -  
```

Lisätään tarvittava avain tieto apt-key ympäristöön  

```bash
sudo add-apt-repository "deb [arch=amd64] <https://download.docker.com/linux/ubuntu> $(lsb_release -cs) stable"  
```

Päivitetään apt source listaus docker lähteellä, ja valitaan vain 64bit asennukset sekä oikea versio ubuntusta  

```bash
sudo apt-get update  
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose  
```

Asennetaan viimeisin docker ympäristö ubuntulle  
  
## Testaus:

```bash
sudo docker run hello-world  
```

Testataan perustoimivuus ajamalla root käyttäjänä hello-world container  

```bash
sudo usermod -aG docker $USER  
newgrp  
```

Lisätään käyttäjä docker ryhmään ja otetaan ryhmäjäsenyys käyttöön ilman uudelleen kirjatumista  

## Grafana asennus:  

```bash

sudo mkdir /opt/grafana  
```

luodaan sopiva sijainti grafanan tietokannalle ja jatkuvuuden takaamiseksi logitiedoille  

```bash
sudo git clone <https://github.com/tjarvenpaa/kev-t2020-grafana.git> /opt/grafana/  
```

Cloonataan tämä git luotuun kohteeseen  

```bash
sudo chown -R student:student /opt/grafana  
```

vaihdetaan käyttäjäoikeudet oikeampiin

```bash
docker-compose up -d -f /opt/grafana/docker-compose.yml --project-directory /opt/grafana/  
```

otetaan docker-compose ympäristössä uusi container käyttöön.  

## Apache2 lisäpalikat:

Apache tarvitsee toimiakseen konfiguraatio muutoksen, jolla kerrotaan mitä halutaan proxytä ja millä tavalla. Tämän onnistumiseksi apache2 tarvitseen muutaman moduulin aktivoiduksi.

```bash
sudo a2enmod proxy
sudo a2enmod proxy_http
```

Näiden moduulien aktivoinnin jälkeen, tarvitaan vielä konfiguraatio, jolla osoitetaan grafanaan:

```apacheconf
      RewriteEngine on
     <Location "/grafana">
            ProxyPass http://localhost:3000
     </Location>
     ProxyPassReverse /grafana http://localhost:3000
´´´

Tämän jälkeen apache prosessin uudelleen käynnistys ja testaus:

```bash
sudo systemctl restart apache2
```

## Nginx asennus reverse proxy asetuksilla

Tämä vaatii hiukan muutoksia rakenteeseen. Koska nginx palvelu muutettiin käyttämään www-data käyttäjää php-fpm ympäristön yhteensopivuuden takaamiseksi, vaihtui myös omistaja proxy-cachen kanssa. 

lisätään nginx default.conf tiedostoon seuraavat rakenteet  

```apacheconf
location /grafana/ {
    proxy_pass http://localhost:3000/grafana/;  
 }
```

lisätään myös oikeudet kuntoon nginx proxylle:

```bash
sudo chown -R www-data:root /var/cache/nginx/proxy_temp/
```

ja uudelleen käynnistetään nginx palvelu

```bash
sudo systemctl restart nginx
```

