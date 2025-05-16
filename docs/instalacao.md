# Configuração ParkingPlus-WPS_LPR

## Pré-Requisitos
-   Sistema operacional instalado e configurado.
-   Docker do MQTT importado e em execução.

## Instalação
Nesta etapa considera que todas as demais dependências do Sistema Operacional escolhido já foram 
devidamente instaladas, para consultar quais são elas e o procedimento de instalação vá até a 
seção [Configuração Sistema Operacional](configuracao_sistema_operacional.md). Siga estas etapas para configurar o ambiente de
desenvolvimento e executar o container com o sistema WPS_LPR:

Versâo Atual: 
```bash
"2.9.2"
```

* Baixe a imagem ParkingPlus:WPS_LPR mais recente disponível no respositório.

Arquivos:
```
- ParkingPlus-WPSLPR-2.9.2_gpu.tgz
- ParkingPlus-WPSLPR-2.9.2_cpu.tgz
```


### Gerar token para autenticação:

[OAuth Playground](https://developers.google.com/oauthplayground/?hl=pt-br)

#### Escolher a API:
**Cloud Storage API v1**

-   `https://www.googleapis.com/auth/devstorage.read_only`

1. Clique em **Authorize API**.
2. Escolha sua conta da **wps-sa**.
3. Clique em **Exchange authorization code for tokens**.
4. Copie o **Access Token** e substitua em `access_token` na chamada do `curl` abaixo. Ou crie um variavel de ambiente com o conteúdo, token.


### Parâmetros para download da imagem
- **BUCKET_NAME**: `lpr-wps-v1`
- **OBJECT_NAME**: nome do arquivo (ex.: `ParkingPlus-WPSLPR-2.9.2_gpu.tgz`)
- **SAVE_TO_LOCATION**: local onde deseja salvar o arquivo

```bash
curl -X GET \ -H "Authorization: Bearer <access_token>" \ 
-o "<SAVE_TO_LOCATION>" \ 
"https://storage.googleapis.com/storage/v1/b/<BUCKET_NAME>/o/<OBJECT_NAME>?alt=media"
```
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

.tab {
  overflow: hidden;
  # border-radius: 6px 6px 0 0;
  # background-color: var(--tab-bg);
  # border: 1px solid var(--tab-border);
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
  border-radius: 6px 6px 6px 6px;
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
  # border: 1px solid var(--tab-border);
  border-radius: 0 0 6px 6px;
  # background-color: var(--tab-content-bg);
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


</style>

<h4>Exemplo de Uso</h4>

<!-- Abas para GPU/CPU -->
<div class="tab">
  <button class="tablinks-platform" onclick="openTabPlatform(event, 'GPU')" id="defaultPlatform">GPU</button>
  <button class="tablinks-platform" onclick="openTabPlatform(event, 'CPU')">CPU</button>
</div>

<!-- Conteúdo GPU -->
<div id="GPU" class="tabcontent-platform">
  <pre><code class="language-bash">curl -X GET \
  -H "Authorization: Bearer $token" \
  -o "/instalacoes/ParkingPlus-WPSLPR-2.9.2_gpu.tgz" \
  "https://storage.googleapis.com/storage/v1/b/lpr-wps-v1/o/ParkingPlus-WPSLPR-2.9.2_gpu.tgz?alt=media"</code></pre>
</div>

<!-- Conteúdo CPU -->
<div id="CPU" class="tabcontent-platform">
  <pre><code class="language-bash">curl -X GET \
  -H "Authorization: Bearer $token" \
  -o "/instalacoes/ParkingPlus-WPSLPR-2.9.2_cpu.tgz" \
  "https://storage.googleapis.com/storage/v1/b/lpr-wps-v1/o/ParkingPlus-WPSLPR-2.9.2_cpu.tgz?alt=media"</code></pre>
</div>

<hr />

<h4>Instalação de pacotes necessários e carregamento da imagem</h4>

<!-- Abas para Ubuntu/CentOS -->
<div class="tab">
  <button class="tablinks-system" onclick="openTabSystem(event, 'Ubuntu')" id="defaultSystem">Ubuntu</button>
  <button class="tablinks-system" onclick="openTabSystem(event, 'CentOS')">CentOS 7</button>
</div>

<!-- Conteúdo Ubuntu -->
<div id="Ubuntu" class="tabcontent-system">
  <h4>Instale o python3.11</h4>
  <pre><code class="language-bash">sudo apt install python3.11</code></pre>
  <h4>Instale o pkplus-cli</h4>
  <pre><code class="language-bash">wget https://intranet.parkingplus.com.br/pub/Parking%20Plus/pkplus-cli/3.0.0/pkplus-cli_3.0.0-1_all.deb \
--no-check-certificate --http-user='seu_usuario' --ask-password
apt install ./pkplus-cli_3.0.0-1_all.deb
mkdir -p /var/lib/pkplus-svc</code></pre>
  <h4>Carregue a imagem com o pkplus-cli</h4>
  <pre><code class="language-bash">pkplus-cli svc import-file /instalacoes/ParkingPlus:WPSLPR-2.9.2.tgz</code></pre>
  <h5>Após a importação inicie a imagem com o pkplus-cli</h5>
  <pre><code class="language-bash">pkplus-cli svc enable ParkingPlus:WPSLPR:2.9.2</code></pre>
</div>

<!-- Conteúdo CentOS -->
<div id="CentOS" class="tabcontent-system">
  <h4>Nas versões com <strong>GPU</strong> importe o tgz.</h4>
  <h5>👍 P.S caso seja a versão CPU, o pkplus-cli é capaz de rodá-la também</h5>
  <h4>GPU</h4>
  <pre><code class="language-bash">mkdir "/instalacoes/ParkingPlus-WPSLPR-2.9.2"
mv /instalacoes/ParkingPlus-WPSLPR-2.9.2_gpu.tgz /instalacoes/ParkingPlus-WPSLPR-2.9.2
cd /instalacoes/ParkingPlus-WPSLPR-2.9.2
tar -xf ParkingPlus:WPSLPR-2.9.2_gpu.tgz
docker load -i images.tar</code></pre>
  <h4>Inicie a imagem com o docker-compose</h4>
  <pre><code class="language-bash">docker compose up -d</code></pre>
  <h4>CPU</h4>
  <pre><code class="language-bash">wget https://intranet.parkingplus.com.br/pub/Parking%20Plus/pkplus-cli/3.0.0/pkplus-cli_3.0.0-1_all.deb \
--no-check-certificate --http-user='seu_usuario' --ask-password
apt install ./pkplus-cli_3.0.0-1_all.deb
mkdir -p /var/lib/pkplus-svc</code></pre>
  <pre><code class="language-bash">pkplus-cli svc import-file /instalacoes/ParkingPlus:WPSLPR-2.9.2.tgz</code></pre>
  <h5>Após a importação inicie a imagem com pkplus-cli</h5>
  <pre><code class="language-bash">pkplus-cli svc enable ParkingPlus:WPSLPR:2.9.2</code></pre>
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
> [configuração do daemon do docker](configuracao_sistema_operacional.md#51-execute-o-seguinte-comando-para-configurar-o-daemon-do-docker).

Ao iniciar o container, o sistema já estará em funcionamento, lendo o arquivo `config.json`, para 
executar as câmeras cadastradas, para adicionar, alterar ou remover câmeras, siga para a próxima seção.

------------