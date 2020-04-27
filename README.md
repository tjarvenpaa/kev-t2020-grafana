# kev-t2020-grafana
Tietojärjestelmien perusteet Grafana docker-compose
Tähän sisältönä tietojärjestelmien perusteet toteutuksen grafana asennus. Asennus toteutetaan docker kuvana, jossa käytetään grafana:latest sisältöä

Toiminta

Tämä tarvitsee toimiakseen docker alustan sekä docker-compose työkalun. Tarvitaan myös toimiva reverse proxy rakenne apache2 ympäristöön

Docker asennus:
$ sudo apt-get remove docker docker-engine docker.io containerd runc
$ sudo apt-get update
$ sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose

Testaus:
$ sudo docker run hello-world
$ sudo usermod -aG docker $USER
$ newgroup

Grafana asennus:
$ sudo mkdir /opt/grafana
$ sudo git clone 
$ docker-compose -d up 
