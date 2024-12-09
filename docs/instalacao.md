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
"2.7.0"
```

* Baixe a imagem ParkingPlus:WPS_LPR mais recente disponível no respositório.

Arquivos:
```
- ParkingPlus-WPSLPR-2.7.0.tgz
- ParkingPlus-WPSLPR-2.7.0_cpu.tgz
```


### Gerar token para autenticação:

[OAuth Playground](https://developers.google.com/oauthplayground/?hl=pt-br)

#### Escolher a API:
**Cloud Storage API v1**

-   `https://www.googleapis.com/auth/devstorage.read_only`

1. Clique em **Authorize API**.
2. Escolha sua conta da **wps-sa**.
3. Clique em **Exchange authorization code for tokens**.
4. Copie o **Access Token** e substitua em `access_token` na chamada do `curl` abaixo.

### Parâmetros para download da imagem
- **BUCKET_NAME**: `lpr-wps-v1`
- **OBJECT_NAME**: nome do arquivo (ex.: `ParkingPlus-WPSLPR-2.7.0.tgz`)
- **SAVE_TO_LOCATION**: local onde deseja salvar o arquivo

```bash
curl -X GET \ -H "Authorization: Bearer <access_token>" \ 
-o "<SAVE_TO_LOCATION>" \ 
"https://storage.googleapis.com/storage/v1/b/<BUCKET_NAME>/o/<OBJECT_NAME>?alt=media"
```

#### Exemplo de Uso
```bash
curl -X GET \
  -H "Authorization: Bearer yasdslk...00198" \
  -o "/instalacoes/ParkingPlus-WPSLPR-2.7.0.tgz" \
  "https://storage.googleapis.com/storage/v1/b/lpr-wps-v1/o/ParkingPlus-WPSLPR-2.7.0.tgz?alt=media"
```

### Instalação de pacotes necessários

* #### Instale o python3.11 (Ubuntu)
```bash
sudo apt install python3.11
```
* #### Instale o pkplus-cli (Ubuntu)
```bash
wget https://intranet.parkingplus.com.br/pub/Parking%20Plus/pkplus-cli/3.0.0/pkplus-cli_3.0.0-1_all.deb \
--no-check-certificate --http-user='seu_usuario' --ask-password
apt install ./pkplus-cli_3.0.0-1_all.deb
mkdir -p /var/lib/pkplus-svc
```
* Carregue a imagem com o pkplus-cli (disponível para Ubuntu, e CentOS7 **sem** **GPU**)
```bash
pkplus-cli svc import-file ParkingPlus:WPSLPR-x.y.z.tgz
```
* Nas versões com **CentOS7 com GPU** importe o tgz.
> 👍 P.S caso seja a versão CPU, o pkplus-cli é capaz de rodá-la também
```bash
tar -xf ParkingPlus:WPSLPR-x.y.z.tgz
docker load -i images.tar
```
* Após a importação inicie a imagem com pkplus-cli
```bash
pkplus-cli svc enable ParkingPlus:WPSLPR:x.y.z
```
* No CentOS
```bash
docker-compose up -d
```
> ⚠️ **Atenção!**
> 
> Caso após executar o container receba um erro de:
> **_`unknown or invalid runtime name: nvidia`_**, consulte a seção 
> [configuração do daemon do docker](configuracao_sistema_operacional.md#51-execute-o-seguinte-comando-para-configurar-o-daemon-do-docker).

Ao iniciar o container, o sistema já estará em funcionamento, lendo o arquivo `config.json`, para 
executar as câmeras cadastradas, para adicionar, alterar ou remover câmeras, siga para a próxima seção.

------------
