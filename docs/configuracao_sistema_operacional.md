<style>
:root {
  --primary-color: #ff5722;
}

[data-md-color-scheme="default"] {
  --tab-bg: #f9f9f9;
  --tab-hover: #eeeeee;
  --tab-active: var(--primary-color);
  --tab-content-bg: #fefefe;
  --tab-border: #ddd;
  --tab-text: #333;
  --tab-active-text: white;
}

[data-md-color-scheme="slate"] {
  --tab-bg: #2e2e2e;
  --tab-hover: #3a3a3a;
  --tab-active: var(--primary-color);
  --tab-content-bg: #1f1f1f;
  --tab-border: #444;
  --tab-text: #eee;
  --tab-active-text: white;
}

.tab-container {
  border-radius: 6px;
  margin-bottom: 0.2rem;
}

.tab-buttons {
  display: flex;
  gap: 4px;
  padding: 6px;
}

.tab-buttons button {
  background-color: var(--tab-bg);
  border: 1px solid var(--tab-border);
  border-radius: 6px;
  padding: 10px 20px;
  font-size: 13px;
  cursor: pointer;
  color: var(--tab-text);
  transition: background-color 0.3s, color 0.3s;
}

.tab-buttons button:hover {
  background-color: var(--tab-hover);
}

.tab-buttons button.active {
  background-color: var(--tab-active);
  color: var(--tab-active-text);
}

.tab-content {
  display: none;
  padding: 2px;
  border-radius: 0 0 6px 6px;
  color: var(--tab-text);
  margin-bottom: 1rem;
}

.tab-content.active {
  display: block;
}

.tab-content pre {
  background-color: #272822;
  color: #f8f8f2;
  padding: 2px;
  border-radius: 6px;
  overflow-x: auto;
}
</style>



<h1>Configuração Sistema Operacional</h1>
<p>Nesse manual iremos abordar sobre a instalação dos pacotes necessários no sistema operacional que o serviço ParkingPlus:WPS_LPR irá rodar.</p>

<h2>Pré-Requisitos</h2>
<ul>
  <li>Ubuntu Server 22.04 LTS (Recomendado)</li>
  <li>CentOS 7</li>
  <li>Debian 12</li>
</ul>
<p>Entretanto, por se tratar de container Docker, também pode funcionar em outras distribuições e plataformas, porém não foi testado.</p>

<h2>Instalação</h2>

<div class="tab-container">
  <div class="tab-buttons">
    <button class="active" onclick="showTab('ubuntu')">Ubuntu</button>
    <button onclick="showTab('centos')">CentOS 7</button>
  </div>

  <div id="ubuntu" class="tab-content active">
    <h4>1. Criação de pastas</h4>
    <pre><code>sudo mkdir -p /instalacoes /WPSBrasil</code></pre>

    <h4>2. Atualização do sistema</h4>
    <pre><code>sudo apt-get update && apt upgrade</code></pre>

    <h4>3. Instalação de pacotes</h4>
    <pre><code>apt --fix-broken install
apt-get install wget nano ntpdate vim mlocate default-jdk pciutils \
apt-transport-https ca-certificates curl software-properties-common python3 python3-pip -y
pip3 install --upgrade pip 
pip install glances gdown py3nvml</code></pre>

    <h4>4. Docker e Nvidia Toolkit</h4>
    <h6>Docker</h6>
<pre><code>curl -fsSL https://get.docker.com/ | sh</code></pre>

    <h6>Docker-compose</h6>
<pre><code>sudo curl -L https://github.com/docker/compose/releases/download/v2.3.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose</code></pre>

    <h6>NVIDIA Container Toolkit</h6>
<pre><code>curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
&& curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list \
&& sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
systemctl restart docker</code></pre>

    <h4 id="configuracao-docker">5. Configuração do Docker</h4>
<pre><code>nvidia-ctk runtime configure --runtime=docker --config=/etc/docker/daemon.json
systemctl restart docker.service</code></pre>

    <h4>6. Instalação Drivers Nvidia</h4>
    <pre><code>sudo apt install nvidia-driver-535 nvidia-dkms-535 -y</code></pre>

    <h4>7. Variáveis de Ambiente</h4>

    <h6>Com GPU</h6>
<pre><code>echo "export NVIDIA_VISIBLE_DEVICES=all" >> ~/.bashrc</code></pre>

    <h6>Sem GPU</h6>
<pre><code>echo "export NVIDIA_VISIBLE_DEVICES=void" >> ~/.bashrc</code></pre>

    <h6>Nome da máquina</h6>
<pre><code>echo "PS1='\[\033[1;31m\]\u\[\033[0;32m\]@WPS_LPR\[\e[m\] \[\e[1;34m\]\w\[\e[m\] \[\e[1;32m\]\$\[\e[m\] \[\e[1;37m\]'" >> ~/.bashrc 
source ~/.bashrc</code></pre>

    <h4>8. Desabilitar duplicação de logs</h4>
    <pre><code>echo "if \$programname == 'WpsLprConcentrator' then /dev/null" >> /etc/rsyslog.conf
sed -i -e 's/^$ModLoad imuxsock/#$ModLoad imuxsock/' -e 's/^$ModLoad imjournal/#$ModLoad imjournal/' /etc/rsyslog.conf
sed -i -e 's/^module(load="imuxsock")/#module(load="imuxsock")/' -e 's/^module(load="imjournal")/#module(load="imjournal")/' /etc/rsyslog.conf
systemctl restart rsyslog</code></pre>

    <h4>9. Modo de Performance</h4>
    <pre><code>gdown 1NxFukxgIMhfG5xfh076dxFkglCtHjO4S
dpkg -i set-performance_1.0-2_amd64.deb
systemctl start set_performance.service
systemctl enable set_performance.service</code></pre>
  </div>

  <div id="centos" class="tab-content">
    <h4>1. Criação de pastas</h4>
    <pre><code>sudo mkdir -p /instalacoes /WPSBrasil</code></pre>

    <h4>2. Repositórios</h4>
    <pre><code>yum install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm -y
yum install epel-release -y</code></pre>

    <h4>3. Atualização do sistema</h4>
    <pre><code>sudo yum update -y</code></pre>

    <h4>4. Instalação de pacotes</h4>
    <pre><code>yum install -y wget nano ntpdate vim mlocate java-1.8.0-openjdk \
default-jdk pciutils python3 python3-pip 
pip3 install --upgrade pip 
pip install glances gdown py3nvml</code></pre>

    <h4>5. Docker e Nvidia Toolkit</h4>

    <h6>Docker</h6>
<pre><code>curl -fsSL https://get.docker.com/ | sh</code></pre>

    <h6>Docker-compose</h6>
<pre><code>sudo curl -L https://github.com/docker/compose/releases/download/v2.3.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose</code></pre>

    <h6>NVIDIA Container Toolkit</h6>
<pre><code>curl -s -L https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo | \
sudo tee /etc/yum.repos.d/nvidia-container-toolkit.repo
sudo yum install -y nvidia-container-toolkit
systemctl restart docker</code></pre>

    <h4>6. Configuração do Docker</h4>
    <pre><code>nvidia-ctk runtime configure --runtime=docker --config=/etc/docker/daemon.json
systemctl restart docker.service</code></pre>

    <h4>7. Instalação Drivers Nvidia</h4>
    <pre><code>yum -y install nvidia-detect
yum install -y kmod-nvidia
yum remove -y xorg-x11-drivers xorg-x11-drv-nouveau</code></pre>

    <h4>8. Variáveis de Ambiente</h4>
    <h6>Com GPU</h6>
    <pre><code>echo "export NVIDIA_VISIBLE_DEVICES=all" >> ~/.bashrc</code></pre>

    <h6>Sem GPU</h6>
<pre><code>"export NVIDIA_VISIBLE_DEVICES=void" >> ~/.bashrc</code></pre>

    <h6>Nome da máquina</h6>
<pre><code>echo "PS1='\[\033[1;31m\]\u\[\033[0;32m\]@WPS_LPR\[\e[m\] \[\e[1;34m\]\w\[\e[m\] \[\e[1;32m\]\$\[\e[m\] \[\e[1;37m\]'" >> ~/.bashrc 
source ~/.bashrc</code></pre>

    <h4>9. Desabilitar duplicação de logs</h4>
    <pre><code>echo "if \$programname == 'WpsLprConcentrator' then /dev/null" >> /etc/rsyslog.conf
sed -i -e 's/^$ModLoad imuxsock/#$ModLoad imuxsock/' -e 's/^$ModLoad imjournal/#$ModLoad imjournal/' /etc/rsyslog.conf
sed -i -e 's/^module(load="imuxsock")/#module(load="imuxsock")/' -e 's/^module(load="imjournal")/#module(load="imjournal")/' /etc/rsyslog.conf
systemctl restart rsyslog</code></pre>

    <h4>10. Modo de Performance</h4>
    <pre><code>gdown 1Roq8tn0COhY-OSrUy_YRNEQuZzdnM4Uu
rpm -ivh set_performance-1.0-1.x86_64.rpm  
systemctl start set_performance.service
systemctl enable set_performance.service</code></pre>
  </div>
</div>

<script>
  function showTab(id) {
    document.querySelectorAll('.tab-content').forEach(e => e.classList.remove('active'));
    document.querySelectorAll('.tab-buttons button').forEach(b => b.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    event.target.classList.add('active');
  }
</script>
