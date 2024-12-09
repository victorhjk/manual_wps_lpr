# Configuração Sistema Operacional  
  
Nesse manual iremos abordar sobre a instalação dos pacotes necessários no sistema operacional que 
o serviço ParkingPlus:WPS_LPR irá rodar.  
  
## Pré-Requisitos  
- CentOS 7  
- Ubuntu Server 22.04 LTS (Recomendado)  
- Debian 12  

Entretanto por se tratar de container docker, também pode ser que funcione em outras distribuições e plataformas, porém não foi testado.
  
## Instalação  
  
### Sistema_Operacional  
Os seguintes procedimentos são necessários para o correto funcionamento do sistema *WPS_LPR*  
  
### 1. Criação de pastas  
  
* /instalacoes  
* /WPSBrasil  

```bash  
sudo mkdir -p /instalacoes /WPSBrasil
```  
  
### 2. Instalação de repositórios  

- elrepo (centos)  
```bash  
yum install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm -y
```  
- epel (centos)  
```bash  
yum install epel-release -y
```  
### 3. Atualização dos repositórios e sistema operacional  
  
**CentOS**  
```bash  
sudo yum update -y
```  
**Ubuntu**  
```bash  
sudo apt-get update && apt upgrade
```  
### 4. Instalação de pacotes necessários  
- wget  
- nano  
- ntpdate  
- vim  
- mlocate  
- java-1.8.0-openjdk  
- default-jdk  
- pciutils
- python3
- glances
- gdown
  
**CentOS**  
```bash  
yum install -y wget nano ntpdate vim mlocate java-1.8.0-openjdk \
default-jdk pciutils python3 python3-pip 
```  
```bash  
pip3 install --upgrade pip 
pip install glances gdown py3nvml
``` 
**Ubuntu**  
```bash  
apt --fix-broken install
apt-get install wget nano wget ntpdate vim mlocate default-jdk pciutils \
apt-transport-https ca-certificates curl software-properties-common python3 python3-pip -y  
```  
```bash  
pip3 install --upgrade pip 
pip install glances gdown py3nvml
``` 
### 5. Instalação do Docker, docker-compose, nvidia-docker-container  
  
#### **CentOS**  
  
* docker  
```bash  
curl -fsSL https://get.docker.com/ | sh  
```  
* docker-compose  
```bash  
sudo curl -L https://github.com/docker/compose/releases/download/v2.3.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```  
* nvidia-container-toolkit  
```bash  
curl -s -L https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo | \
sudo tee /etc/yum.repos.d/nvidia-container-toolkit.repo
sudo yum install -y nvidia-container-toolkit
systemctl restart docker
```  
#### **Ubuntu**  
  
* docker  
```bash  
curl -fsSL https://get.docker.com/ | sh 
```  
* docker-compose  
```bash  
sudo curl -L https://github.com/docker/compose/releases/download/v2.3.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```  
* nvidia-container-toolkit  
```bash  
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
&& curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list \
&& \  
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
systemctl restart docker
```  
#### 5.1. Execute o seguinte comando para configurar o daemon do docker:
```bash
nvidia-ctk runtime configure --runtime=docker --config=/etc/docker/daemon.json
systemctl restart docker.service
```

### 6. Instalação de Drivers Nvidia (Quando GPU disponível)  
  
**Centos**  
```bash  
yum -y install nvidia-detect
yum install -y kmod-nvidia
yum remove -y xorg-x11-drivers xorg-x11-drv-nouveau
```  
**Ubuntu**  
```bash  
sudo apt install nvidia-driver-535 nvidia-dkms-535 -y
```  
### 7. Exportar várivel que controla uso de GPU ou não.

#### 7.1. Exportar variável de ambiente para o docker enxergar se existe placa de vídeo instalada na máquina, somente em caso de **GPU**.  
```bash  
echo "export NVIDIA_VISIBLE_DEVICES=all" >> ~/.bashrc
```  
#### 7.2. Exportar variável de ambiente caso não haja **GPU**.  
```bash  
echo "export NVIDIA_VISIBLE_DEVICES=void" >> ~/.bashrc
```  
#### 7.3. Exportando o nome da máquina para o bashrc.
* Você pode alterar o nome da máquina para corresponder ao nome da garagem, basta substituir WPS_LPR_Dev, por WPS_LPR_(nome_da_garagem)
```bash  
echo "PS1='\[\033[1;31m\]\u\[\033[0;32m\]@WPS_LPR_Dev\[\e[m\] \[\e[1;34m\]\w\[\e[m\] \[\e[1;32m\]\$\[\e[m\] \[\e[1;37m\]'" >> ~/.bashrc 
 >> ~/.bashrc
source ~/.bashrc
```

### 8. Desabilitando a duplicação de logs do journal para o syslog
```bash
echo "if \$programname == 'WpsLprConcentrator' then /dev/null" >> /etc/rsyslog.conf
sed -i -e 's/^$ModLoad imuxsock/#$ModLoad imuxsock/' -e 's/^$ModLoad imjournal/#$ModLoad imjournal/' /etc/rsyslog.conf
sed -i -e 's/^module(load="imuxsock")/#module(load="imuxsock")/' -e 's/^module(load="imjournal")/#module(load="imjournal")/' /etc/rsyslog.conf
systemctl restart rsyslog
```

### 9. Habilitando modo de performance da máquina (**importante**)  
**CentOS**  
```bash  
gdown 1Roq8tn0COhY-OSrUy_YRNEQuZzdnM4Uu
rpm -ivh set_performance-1.0-1.x86_64.rpm  
systemctl start set_performance.service
systemctl enable set_performance.service  
```  
**Ubuntu**
```bash  
gdown 1NxFukxgIMhfG5xfh076dxFkglCtHjO4S
dpkg -i set-performance_1.0-2_amd64.deb
systemctl start set_performance.service
systemctl enable set_performance.service  
```  
