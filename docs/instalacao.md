# Configuração ParkingPlus-WPS_LPR

## Pré-Requisitos
-   Sistema operacional instalado e configurado.
-   Docker do MQTT importado e em execução.
-   wps-ip-notifier Instalado
-   VPN apontando para o LPR Admin
-   LPR-Stream-Provider instalado e configurado

## Instalação
Nesta etapa considera que todas as demais dependências do Sistema Operacional escolhido já foram 
devidamente instaladas, para consultar quais são elas e o procedimento de instalação vá até a 
seção [Configuração Sistema Operacional](configuracao_sistema_operacional.md). Siga estas etapas para configurar o ambiente de
desenvolvimento e executar o container com o sistema WPS_LPR:

Versâo Atual: 
```bash
"3.2.0"
```

* Baixe a imagem ParkingPlus:WPS_LPR mais recente disponível no respositório.

Arquivos:
```
- ParkingPlus-WPSLPR-3.2.0_gpu.tgz
- ParkingPlus-WPSLPR-3.2.0_cpu.tgz
```

[//]: # (### Gerar token para autenticação:)

[//]: # ()
[//]: # ([OAuth Playground]&#40;https://developers.google.com/oauthplayground/?hl=pt-br&#41;)

[//]: # ()
[//]: # (#### Escolher a API:)

[//]: # (**Cloud Storage API v1**)

[//]: # ()
[//]: # (-   `https://www.googleapis.com/auth/devstorage.read_only`)

[//]: # ()
[//]: # (1. Clique em **Authorize API**.)

[//]: # (2. Escolha sua conta da **wps-sa**.)

[//]: # (3. Clique em **Exchange authorization code for tokens**.)

[//]: # (4. Copie o **Access Token** e substitua em `access_token` na chamada do `curl` abaixo. Ou crie um variavel de ambiente com o conteúdo, token.)

[//]: # ()
[//]: # ()
[//]: # (### Parâmetros para download da imagem)

[//]: # (- **BUCKET_NAME**: `lpr-wps-v1`)

[//]: # (- **OBJECT_NAME**: nome do arquivo &#40;ex.: `ParkingPlus-WPSLPR-3.2.0_gpu.tgz`&#41;)

[//]: # (- **SAVE_TO_LOCATION**: local onde deseja salvar o arquivo)

[//]: # ()
[//]: # (```bash)

[//]: # (curl -X GET \ -H "Authorization: Bearer <access_token>" \ )

[//]: # (-o "<SAVE_TO_LOCATION>" \ )

[//]: # ("https://storage.googleapis.com/storage/v1/b/<BUCKET_NAME>/o/<OBJECT_NAME>?alt=media")

[//]: # (```)
<!-- CSS para abas -->
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
  
  --bash-bg: #f0f0f0;
  --bash-text: #333;
  --bash-green: #2e7d32;
}

[data-md-color-scheme="slate"] {
  --tab-bg: #2e2e2e;
  --tab-hover: #3a3a3a;
  --tab-active: var(--primary-color);
  --tab-content-bg: #1f1f1f;
  --tab-border: #444;
  --tab-text: #eee;
  --tab-active-text: white;
  
  --bash-bg: #1e1e1e;
  --bash-text: #eee;
  --bash-green: #66bb6a;
}

.tab {
  overflow: hidden;
  margin-bottom: 0;
}

.tab button {
  background-color: var(--tab-bg);
  border: none;
  outline: none;
  padding: 10px 24px;
  cursor: pointer;
  transition: 0.3s;
  font-size: 12px;
  color: var(--tab-text);
  border-radius: 6px;
}

.tab button:hover {
  background-color: var(--tab-hover);
}

.tab button.active {
  background-color: var(--tab-active);
  color: var(--tab-active-text);
}

.tabcontent-platform,
.tabcontent-system {
  display: none;
  padding: 2px;
  border-radius: 0 0 6px 6px;
  color: var(--tab-text);
  margin-bottom: 1rem;
}

.tabcontent-platform pre,
.tabcontent-system pre {
  background-color: #272822;
  color: #f8f8f2;
  padding: 2px;
  border-radius: 6px;
  overflow-x: auto;
}

.bash-output {
  background-color: var(--bash-bg);
  color: var(--bash-text);
  padding: 1em;
  border-radius: 6px;
  font-family: monospace;
  font-size: 14px;
  overflow-x: auto;
  white-space: pre-wrap;
}

.bash-output .service {
  color: var(--bash-green);
  font-weight: bold;
}

.bash-output .active {
  color: var(--bash-green);
  font-weight: bold;
}

.bash-output .enabled {
  color: var(--bash-green);
}
</style>

<script>
function openTabSystemA(evt, tabName) {
  var i, tabcontent, tablinks;
  tabcontent = document.getElementsByClassName("tabcontent-system-A");
  for (i = 0; i < tabcontent.length; i++) {
    tabcontent[i].style.display = "none";
  }
  tablinks = document.getElementsByClassName("tablinks-system-A");
  for (i = 0; i < tablinks.length; i++) {
    tablinks[i].className = tablinks[i].className.replace(" active", "");
  }
  document.getElementById(tabName).style.display = "block";
  evt.currentTarget.className += " active";
}

function openTabSystemB(evt, tabName) {
  var i, tabcontent, tablinks;
  tabcontent = document.getElementsByClassName("tabcontent-system-B");
  for (i = 0; i < tabcontent.length; i++) {
    tabcontent[i].style.display = "none";
  }
  tablinks = document.getElementsByClassName("tablinks-system-B");
  for (i = 0; i < tablinks.length; i++) {
    tablinks[i].className = tablinks[i].className.replace(" active", "");
  }
  document.getElementById(tabName).style.display = "block";
  evt.currentTarget.className += " active";
}

document.addEventListener("DOMContentLoaded", function () {
  document.getElementById("defaultSystem")?.click();   // Grupo A
  document.getElementById("defaultSystem2")?.click();  // Grupo B
});
</script>

### Configuração da VPN para o LPR Admin</h3>

<!-- Abas para Ubuntu/CentOS -->
<div class="tab">
  <button class="tablinks-system-A" onclick="openTabSystemA(event, 'Ubuntu')" id="defaultSystem">Ubuntu</button>
  <button class="tablinks-system-A" onclick="openTabSystemA(event, 'CentOS')">CentOS 7</button>
</div>

<!-- Conteúdo Ubuntu -->
<div id="Ubuntu" class="tabcontent-system-A">
  <p><strong></strong> Instale o OpenVPN:</p>
  <pre><code class="language-bash">apt-get update
apt install openvpn</code></pre>

  <p><strong></strong> Coloque os arquivos da VPN em:</p>
  <pre><code class="language-bash">/etc/openvpn</code></pre>

  <p><strong></strong> Configure o arquivo <code>.conf</code> conforme necessário:</p>
  <pre><code class="language-bash">nano /etc/openvpn/client_cameras_vnc.conf</code></pre>

  <p><strong></strong> Inicie a VPN:</p>
  <pre><code class="language-bash">systemctl start openvpn@client_cameras_vnc</code></pre>

  <p>Verifique o status:</p>
  <pre><code class="language-bash">systemctl status openvpn@client_cameras_vnc</code></pre>
</div>

<!-- Conteúdo CentOS -->
<div id="CentOS" class="tabcontent-system-A">
  <p><strong></strong> Instale o OpenVPN:</p>
  <pre><code class="language-bash">yum install openvpn</code></pre>

  <p><strong></strong> Coloque os arquivos da VPN em:</p>
  <pre><code class="language-bash">/etc/openvpn</code></pre>

  <p><strong></strong> Configure o arquivo <code>.conf</code> conforme necessário:</p>
  <pre><code class="language-bash">nano /etc/openvpn/client_cameras_vnc.conf</code></pre>

  <p><strong></strong> Inicie a VPN:</p>
  <pre><code class="language-bash">systemctl start openvpn@client_cameras_vnc</code></pre>

  <p>Verifique o status:</p>
  <pre><code class="language-bash">systemctl status openvpn@client_cameras_vnc</code></pre>
</div>


### Instalação do LPR-Stream-Provider
<p><strong></strong> Baixe o pacote da versão mais atual:</p>
<pre style="font-size: 0.8em"><code class="language-bash">https://intranet.parkingplus.com.br/pub/Parking%20Plus/LPR/lpr-stream-provider/3.0.15/lpr-stream-provider-3.0.15.tgz</code></pre>

<p><strong></strong> Importe o serviço usando o <code>pkplus-cli</code>:</p>
<pre><code class="language-bash">pkplus-cli svc import-file lpr-stream-provider-3.0.15.tgz</code></pre>

<p><strong></strong> Habilite o serviço <strong>LPR-Stream-Provider</strong>:</p>
<pre><code class="language-bash">pkplus-cli svc enable WPS_LPR:lpr-stream-provider:3.0.15</code></pre>

<p><strong></strong> Configure o <code>car_park</code> da garagem no arquivo de configuração:</p>
<pre><code class="language-bash">/etc/ParkingPlus/lpr-stream-provider/config/default.json</code></pre>

<p>Abra o arquivo em um editor de sua preferência e ajuste os campos <code>car_park_id</code> <code>car_park_username</code> <code>car_park_password</code>conforme necessário.</p>
 
<h4>Exemplo de <code>default.json</code></h4>
<pre style="font-size: 0.85em"><code class="language-json">{
    "app": {
        "port": 3000,
        "car_park_id": "1cbcba85-f10f-452f-b025-3a96b3c33638",
        "config_place": "cloud",
        "car_park_username": "622c8cad-ea27-4666-acee-f81c5c8ecaa2",
        "car_park_password": "977c9ed0be6ca29f7be9a114e5235fb303035c1c17c85fc75b85d67756263f84"
    },
    "camera": {
        "region": "brazil",
        "config_path": "/var/lib/ParkingPlus/WpsLpr/config",
        "config_file_name": "config.json",
        "video_codec": "libx265",
        "frame-rate": 0,
        "delete_json": false
    },
    "logger": {
        "enabled": true,
        "level": "trace",
        "type": "console",
        "path": "/var/opt"
    },
    "brokerMQTT": {
        "url": "mqtt://192.168.135.158:1883",
        "user": "wps",
        "password": "wps@2021"
    },
    "lpr_svc_api_url": "https://lprapi.dev.wpsenterprise.com.br",
    "parkingplus_wps_lpr_url": "http://127.0.0.1:5000",
    "schedules": {
        "status_update": {
            "enabled": true,
            "expression": "0 */1 * * * *",
            "name": "Camera Status Update"
        },
        "config_update": {
            "enabled": false,
            "expression": "0 */1 * * * *",
     	    "name": "Config Update"
        }
    }
}</code></pre>


### Arquivos

<!-- Abas para GPU/CPU -->
<div class="tab">
  <button class="tablinks-platform" onclick="openTabPlatform(event, 'GPU')" id="defaultPlatform">GPU</button>
  <button class="tablinks-platform" onclick="openTabPlatform(event, 'CPU')">CPU</button>
</div>

<!-- Conteúdo GPU -->
<div id="GPU" class="tabcontent-platform">
  <pre style="font-size: 0.85em"><code class="language-bash">https://intranet.parkingplus.com.br/pub/Parking%20Plus/LPR/WpsLpr/3.2.0/ParkingPlus-WPSLPR-3.2.0_gpu.tgz</code></pre>
</div>

<!-- Conteúdo CPU -->
<div id="CPU" class="tabcontent-platform">
  <pre style="font-size: 0.85em"><code class="language-bash">https://intranet.parkingplus.com.br/pub/Parking%20Plus/LPR/WpsLpr/3.2.0/ParkingPlus-WPSLPR-3.2.0_cpu.tgz</code></pre>
</div>

<hr />

### Instalação de pacotes necessários e carregamento da imagem

<!-- Abas para Ubuntu/CentOS -->
<div class="tab">
  <button class="tablinks-system-B" onclick="openTabSystemB(event, 'Ubuntu2')" id="defaultSystem2">Ubuntu</button>
  <button class="tablinks-system-B" onclick="openTabSystemB(event, 'CentOS2')">CentOS 7</button>
</div>

<!-- Conteúdo Ubuntu -->
<div id="Ubuntu2" class="tabcontent-system-B">
    <h4>Instale o wps-ip-notifier</h4>
    <pre style="font-size: 0.9em"><code class="language-bash">https://intranet.parkingplus.com.br/pub/Parking%20Plus/LPR/services/1.0.3/wps-ip-notifier-1.0.3-1.deb
apt install ./wps-ip-notifier-1.0.3-1.deb
</code></pre>
<h4>Confira a instalação</h4>
<pre style="font-size: 1em"><code class="language-bash">systemctl status wps-ip-notifier.service</code></pre>
<div class="md-typeset">
  <div class="admonition warning">
    <p class="admonition-title">Verificação do serviço</p>
    <p>Confira se o serviço está rodando e se está habilitado para iniciar automaticamente com o sistema.</p>
    <p>Caso <strong>não esteja rodando</strong>, execute o comando abaixo para acompanhar os logs em tempo real:</p>
    <pre><code class="language-bash">journalctl -u wps-ip-notifier -f</code></pre>
  </div>
</div>
<div class="bash-output" style="user-select: text; font-size: 0.7em;"><span class="service">●</span> wps-ip-notifier.service - WPS IP Notifier Service  
     Loaded: loaded (/etc/systemd/system/wps-ip-notifier.service; <span class="enabled">enabled</span>; vendor preset: <span class="enabled">enabled</span>)  
     Active: <span class="active">active (running)</span> since Tue 2025-07-22 09:43:12 -03; 22h ago  
   Main PID: 2028 (wps_ip_notifier)  
      Tasks: 3 (limit: 38016)  
     Memory: 316.0K  
        CPU: 58.979s  
     CGroup: /system.slice/wps-ip-notifier.service  
             └─2028 /usr/local/bin/wps_ip_notifier
</div>

  <h4>Instale o python3.11</h4>
  <pre><code class="language-bash">sudo apt install python3.11</code></pre>
  <h4>Instale o pkplus-cli</h4>
   <pre style="font-size: 0.85em"><code class="language-bash">wget https://intranet.parkingplus.com.br/pub/Parking%20Plus/pkplus-cli/3.0.0/pkplus-cli_3.0.0-1_all.deb \
--http-user='seu_usuario' \
--ask-password
apt install ./pkplus-cli_3.0.0-1_all.deb
mkdir -p /var/lib/pkplus-svc</code></pre>
  <h4>Carregue a imagem com o pkplus-cli</h4>
  <pre><code class="language-bash">pkplus-cli svc import-file /instalacoes/ParkingPlus-WPSLPR-3.2.0.tgz</code></pre>
  <h5>Após a importação inicie a imagem com o pkplus-cli</h5>
  <pre><code class="language-bash">pkplus-cli svc enable ParkingPlus:WPS_LPR:3.2.0</code></pre>
</div>

<!-- Conteúdo CentOS -->
<div id="CentOS2" class="tabcontent-system-B">
<h4>Instale o wps-ip-notifier</h4>
   <pre style="font-size: 0.9em"><code class="language-bash">https://intranet.parkingplus.com.br/pub/Parking%20Plus/LPR/services/1.0.3/wps-ip-notifier-1.0.3-1.el7.x86_64.rpm
yum localinstall wps-ip-notifier-1.0.3-1.el7.x86_64.rpm
</code></pre>
<h4>Confira a instalação</h4>
<pre style="font-size: 1em"><code class="language-bash">systemctl status wps-ip-notifier.service</code></pre>
<div class="md-typeset">
  <div class="admonition warning">
    <p class="admonition-title">Verificação do serviço</p>
    <p>Confira se o serviço está rodando e se está habilitado para iniciar automaticamente com o sistema.</p>
    <p>Caso <strong>não esteja rodando</strong>, execute o comando abaixo para acompanhar os logs em tempo real:</p>
    <pre><code class="language-bash">journalctl -u wps-ip-notifier -f</code></pre>
  </div>
</div>
<div class="bash-output" style="user-select: text; font-size: 0.7em;"><span class="service">●</span> wps-ip-notifier.service - WPS IP Notifier Service  
     Loaded: loaded (/etc/systemd/system/wps-ip-notifier.service; <span class="enabled">enabled</span>; vendor preset: <span class="enabled">enabled</span>)  
     Active: <span class="active">active (running)</span> since Tue 2025-07-22 09:43:12 -03; 22h ago  
   Main PID: 2028 (wps_ip_notifier)  
      Tasks: 3 (limit: 38016)  
     Memory: 316.0K  
        CPU: 58.979s  
     CGroup: /system.slice/wps-ip-notifier.service  
             └─2028 /usr/local/bin/wps_ip_notifier
</div>
<h4>Instale o pkplus-cli</h4>
 <pre style="font-size: 0.9em"><code class="language-bash">wget -r -np -nH --cut-dirs=5 --accept rpm \
--http-user='seu-usuario' \
--ask-password \
-P /instalacoes/pkplus-cli \
"https://intranet.parkingplus.com.br/pub/Parking%20Plus/ParkingPlus%20InstallServer/stable/1.6.4/"
yum localinstall /instalacoes/pkplus-cli/*.rpm
mkdir -p /var/lib/pkplus-svc</code></pre>
  <h4>Nas versões com <strong>GPU</strong> importe o tgz.</h4>
  <h5>👍 P.S caso seja a versão CPU, o pkplus-cli é capaz de rodá-la também</h5>
  <h4>GPU</h4>
  <pre><code class="language-bash">cd /instalacoes
tar -xOzf ParkingPlus-WPSLPR-3.2.0_gpu.tgz images.tar | docker load
tar -xzvf ParkingPlus-WPSLPR-3.2.0_gpu.tgz docker-compose.yml && tar -czvf ParkingPlus-WPSLPR-3.2.0_gpu_compose.tgz docker-compose.yml && rm docker-compose.yml
pkplus-cli svc import-file ParkingPlus-WPSLPR-3.2.0_gpu_compose.tgz</code></pre>
  <h4>Inicie a imagem com o pkplus-cli</h4>
  <pre><code class="language-bash">pkplus-cli svc enable ParkingPlus:WPS_LPR:3.2.0</code></pre>
  <h4>CPU</h4>
  <pre><code class="language-bash">pkplus-cli svc import-file /instalacoes/ParkingPlus-WPSLPR-3.2.0.tgz</code></pre>
  <h5>Após a importação inicie a imagem com pkplus-cli</h5>
  <pre><code class="language-bash">pkplus-cli svc enable ParkingPlus:WPS_LPR:3.2.0</code></pre>
</div>

<!-- Scripts separados para abas -->
<script>
function openTabPlatform(evt, tabName) {
  var i, tabcontent, tablinks;
  tabcontent = document.getElementsByClassName("tabcontent-platform");
  for (i = 0; i < tabcontent.length; i++) {
    tabcontent[i].style.display = "none";
  }
  tablinks = document.getElementsByClassName("tablinks-platform");
  for (i = 0; i < tablinks.length; i++) {
    tablinks[i].className = tablinks[i].className.replace(" active", "");
  }
  document.getElementById(tabName).style.display = "block";
  evt.currentTarget.className += " active";
}

function openTabSystem(evt, tabName) {
  var i, tabcontent, tablinks;
  tabcontent = document.getElementsByClassName("tabcontent-system");
  for (i = 0; i < tabcontent.length; i++) {
    tabcontent[i].style.display = "none";
  }
  tablinks = document.getElementsByClassName("tablinks-system");
  for (i = 0; i < tablinks.length; i++) {
    tablinks[i].className = tablinks[i].className.replace(" active", "");
  }
  document.getElementById(tabName).style.display = "block";
  evt.currentTarget.className += " active";
}

document.addEventListener("DOMContentLoaded", function () {
  document.getElementById("defaultPlatform").click();
  document.getElementById("defaultSystem").click();
});
</script>



> ⚠️ **Atenção!**
> 
> Caso após executar o container receba um erro de:
> **_`unknown or invalid runtime name: nvidia`_**, consulte a seção 
> [configuração do daemon do docker](configuracao_sistema_operacional.md#configuracao-docker)


Ao iniciar o container, o sistema já estará em funcionamento, lendo o arquivo `config.json`, para 
executar as câmeras cadastradas, para adicionar, alterar ou remover câmeras, siga para a próxima seção.

------------