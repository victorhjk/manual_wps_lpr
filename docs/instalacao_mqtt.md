# Configuração ParkingPlus-MQTT

## Pré-Requisitos
-   Sistema operacional instalado e configurado.

## Instalação
Nesta etapa considera que todas as demais dependências do Sistema Operacional escolhido já foram 
devidamente instaladas, para consultar quais são elas e o procedimento de instalação vá até a 
seção [Configuração Sistema Operacional](configuracao_sistema_operacional.md). Siga estas etapas para importar a imagem 
do ParkingPLUS-MQTT:

Versâo Atual: 
```bash
"1.0.0"
```
### ⚠️ Atenção
Essa imagem não é retrocompatível com outras versões, caso deseje voltar para uma
versão anterior, o ParkingPlus-MQTT precisa ser desabilitado.

* Baixe a imagem ParkingPlus:MQTT mais recente disponível no respositório.

Arquivos:
```
- ParkingPlus-MQTT-1.0.0.tgz
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
- **OBJECT_NAME**: nome do arquivo (ex.: `ParkingPlus-MQTT-1.0.0.tgz`)
- **SAVE_TO_LOCATION**: local onde deseja salvar o arquivo


```bash
curl -X GET \ -H "Authorization: Bearer <access_token>" \ 
-o "<SAVE_TO_LOCATION>" \ 
"https://storage.googleapis.com/storage/v1/b/<BUCKET_NAME>/o/<OBJECT_NAME>?alt=media"
```

#### Exemplo de Uso
```bash
curl -X GET \
  -H "Authorization: Bearer $token" \
  -o "/instalacoes/ParkingPlus-MQTT-1.0.0.tgz" \
  "https://storage.googleapis.com/storage/v1/b/lpr-wps-v1/o/ParkingPlus-MQTT-1.0.0.tgz?alt=media"
```

### Instalação de pacotes necessários

* #### Instale o python3.11 (Ubuntu)

[Instalação Python 3.11](instalacao.md#instale-o-python311-ubuntu)

* #### Instale o pkplus-cli (Ubuntu)

[Instalação pkplus-cli svc](instalacao.md#instale-o-pkplus-cli-ubuntu)



* #### Carregue a imagem com o pkplus-cli (disponível para Ubuntu e CentOS7)
```bash
pkplus-cli svc import-file ParkingPlus-MQTT-1.0.0.tgz
```

* #### Após a importação inicie a imagem do MQTT com pkplus-cli
```bash
pkplus-cli svc enable ParkingPlus:MQTT:1.0.0
```

<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Caixa de Alerta</title>
    <style>
        .alert-box {
            border: 2px solid #ff4c4c;
            border-radius: 8px;
            background-color: #2d2f33;
            color: #ccc;
            padding: 16px;
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 20px 0;
        }

        .alert-header {
            display: flex;
            align-items: center;
            background-color: #3b3b3b;
            padding: 8px 16px;
            border-radius: 6px 6px 0 0;
            font-weight: bold;
            color: #fff;
        }

        .alert-header .icon {
            color: #ff4c4c;
            font-size: 1.2em;
            margin-right: 8px;
        }

        .alert-content {
            padding-top: 12px;
            font-size: 0.95em;
            line-height: 1.5;
        }
    </style>
</head>
<body>

<div class="alert-box">
    <div class="alert-header">
        <span class="icon">❗</span>
        CentOS7
    </div>
    <div class="alert-content">
        O procedimento de instalação está descrito no 
<a href="https://intranet.parkingplus.com.br/pub/Parking%20Plus/CentOS_7/Instalacao_pkplus-cli/01-Instalacao.mp4" 
target="_blank">manual próprio do sistema operacional</a>, porém, caso já esteja instalado, basta importar o arquivo.
<p>É importante que essa imagem seja importada pelo pkplus-cli, pois ele gerencia as redes do Docker, sem a necessidade de
fazer isso manualmente.</p>
    </div>
</div>

</body>
</html>


------------
