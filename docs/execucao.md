# Execução

<p style="text-align: justify;">
    Siga essas etapas para executar o sistema de reconhecimento de placas de veículos WPS_LPR:
</p>

### Configuração das câmeras

<p style="text-align: justify;">
    <strong>1.</strong> Configure a conexão da(s) câmera(s) no arquivo:
</p>
<pre style="text-align: justify; font-family: 'Courier New', monospace;">
<code>/var/lib/ParkingPlus/WpsLpr/config/config.json</code>
</pre>
<p style="text-align: justify;">
    <strong>1.1</strong> Aqui está um exemplo de como o arquivo <code>config.json</code> deve ser configurado para o 
funcionamento do sistema <em>WPS_LPR</em>, podem ser adicionadas várias câmeras para o processamento dentro do limite de
processamento da máquina.
</p>

```json
[
  {
    "camera_id": "1",
    "camera_type": "lpr",
    "camera_brand": "intelbras",
    "wps_topic": "WPS_LPR",
    "camera_ip": "192.168.1.108",
    "user": "wps",
    "password": "wpsbrasil2021",
    "channel_dvr": "1",
    "frame_ocr_rate": 10,
    "roi_size_x": 1280,
    "roi_size_y": 720,
    "roi_x": 0,
    "roi_y": 0,
    "limiar": 20,
    "plate_size_min": 0,
    "plate_size_max": 0,
    "image_size": 0,
    "ocr_mode": 0,
    "ocr_time": 1, 
    "sector_id": 1,
    "parking_name": "",
    "protocol": "udp",
    "power_led": 40,
    "publish_interval": 1000,
    "parking_spaces_quantity": 1,
    "park_ids": []
  }
]
```
<blockquote style="text-align: justify;">
    Remova quaisquer comentários caso use esse exemplo.
    <ul>
        <li><strong>camera_id</strong> é um número sequencial que identifica a câmera no arquivo e seus demais atributos;</li>
        <li><strong>camera_ip</strong> é o ip da câmera que será conectada;</li>
        <li><strong>wps_topic</strong> tópico do MQTT que as mensagens serão publicadas;</li>
        <li><strong>user</strong> usuário da câmera;</li>
        <li><strong>password</strong> senha da câmera;</li>
        <li><strong>channel_dvr</strong> canal para conexão com a câmera, geralmente é "1", quando for utilizar conexão via
        DVR ou NVR verificar o número do canal para se conectar;</li>
        <li><strong>camera_brand</strong> é a marca da câmera, os modelos homologados são:
            <ul>
                <li><em>"intelbras"</em>;</li>
                <li><em>"hikvision"</em>;</li>
                <li><em>"positivo"</em>;</li>
                <li><em>"pumatronix"</em>.</li>
            </ul>
        </li>
        <li><strong>frame_ocr_rate</strong> é a taxa de fps que será passada para a câmera, o WPS_LPR por default faz o OCR
        em uma imagem por segundo de cada câmera, porém ele não está mais vinculado a essa chave. <strong>PS</strong>: Para câmeras
        Hikvision e Intelbrás mais atuais, utilize 25 ou 30;</li>
        <li><strong>roi_size</strong> é o tamanho da imagem que será passada para o WPS_LPR fazer o OCR na imagem, o padrão
        é 1280 para a largura e 720 para a altura;</li>
        <li><strong>roi</strong> são os pontos iniciais para desenho de um retângulo na imagem, para limitar a área de
        atuação do OCR na imagem. Um objetivo por exemplo é que não seja feito OCR em pistas vizinhas;</li>
        <li><strong>ocr_mode</strong> é o tipo possível de OCR nos tipos de veículos, que são:
            <ul>
                <li><em>"0"</em> somente carro;</li>
                <li><em>"1"</em> somente moto;</li>
                <li><em>"2"</em> carro e moto.</li>
            </ul>
        </li>
        <li><strong>camera_type</strong> é o tipo de utilização que a câmera terá no sistema WPS_LPR, que poderá ser:
            <ul>
                <li><em>"lpr"</em>: Para reconhecimento das placas de veículos;</li>
                <li><em>"context"</em>: Para capturar fotos de câmeras de contexto, câmeras que serão posicionadas com o
                intuito de registrar diversas faces dos veículos e do usuário do estacionamento;</li>
                <li><em>"segmentation"</em>: Para utilização de reconhecimento de placas em vagas específicas do
                estacionamento, bem como identificar se a vaga está livre ou ocupada. Cada câmera poderá monitorar de 1 a 4 vagas.</li>
            </ul>
        </li>
        <li><strong>limiar</strong> é a quantidade de movimento para ativar o LPR, ou seja submeter o frame à biblioteca de
        reconhecimento, caso a quantidade de movimento seja menor que o limiar, o frame será descartado,
        até que a quantidade seja alcançada. Caso a quantidade seja superior ao limiar, e nos frames
        seguintes seja menor, o sistema verificará no último frame submetido, se houve placa detectada,
        caso sim, ele envia mais um frame, até que nenhuma placa seja detectada e não haja quantidade de
        movimento superior ao limiar.</li>
        <li><strong>image_size</strong> é o tamanho das imagens que serão enviadas na mensagem, os valores são:
            <ul>
                <li><em>"0"</em> 1280 x 720;</li>
                <li><em>"1"</em> 800 x 450;</li>
                <li><em>"2"</em> 640 x 360;</li>
                <li><em>"9"</em> Sem imagem.</li>
            </ul>
        </li>
        <li><strong>plate_size_min</strong> é o tamanho mínimo da placa para ser submetido ao OCR (0 é desligado).</li>
        <li><strong>plate_size_max</strong> é o tamanho máximo da placa para ser submetido ao OCR (0 é desligado). Esse valor deve ser 
        obrigatoriamente maior que o "plate_size_min".</li>
        <strong><em></em></strong>
        <li><strong>ocr_time</strong> é a quantidade de imagens por segundo que serão submetidas ao OCR no modo "<em>lpr</em>", este valor varia
        de 1 a 4 imagens por segundo, o padrão é 1 por segundo, só aumente esse número caso tenha reserva de recursos de 
        processamento no Servidor de LPR. Já no modo "<em>Segmentation</em>" é o tempo em que uma imagem será submetida ao OCR,
        esse tempo é entre 10 e 40 segundos, o padrão é 15 segundos.</li>
        <li><strong>sector_id</strong> é o ID do setor onde a câmera "<em>segmentation</em>" está instalada, isso reflete na tarifação do setor.</li>
        <li><strong>parking_name</strong> é o nome do setor, é opcional.</li>
        <strong><em></em></strong>
        <li><strong>protocol</strong> é o tipo de protocolo que será usado para fazer o streaming das câmeras, o padrão é UDP, só use o
        protocolo TCP para fazer debug no sistema.</li>
        <strong><em></em></strong>
        <li><strong>power_led</strong> é a potência do LED para câmeras Pumatronix, que contam com a luz branca, o padrão é 40, é importante
        verificar o funcionamento para evitar que a imagem da placa "estoure".</li>
        <strong><em></em></strong>
        <li><strong>publish_interval</strong> é o tempo que a "melhor" placa será publicada no MQTT, como agora a quantidade de OCR por
        segundo pode ser alterada, é importante não "inundar" o terminal com várias publicações de placas, sendo assim, mesmo
        que a quantidade de OCR seja 4, ele irá publicar apenas 1 por segundo caso o tempo aqui seja 1000, e também com o
        intuito de melhorar a performance dos terminais com Mini PCs defasados, pode-se aumentar esse tempo, sem correr o 
        risco de perder imagens para OCR como no método antigo, antes era possível fazer 1 OCR a cada 2 segundos, agora é
        possível fazer 2 OCRs em 2 segundos, e enviar apenas 1 placa a cada 2 segundos. Por padrão o tempo é 1000, e ele varia
        entre 800 e 2000.</li>
        <strong><em></em></strong>
        <li><strong>parking_spaces_quantity</strong> é a quantidade de vagas que serão monitoradas no método "<em>segmentation</em>", entre 1 e 4 vagas.</li>
        <li><strong>park_ids</strong> são os ids das vagas que serão monitoradas no método "<em>segmentation</em>", entre 1 e 4 vagas.</li>
    </ul>
</blockquote>

<p style="text-align: justify;">
<strong>1.2</strong> Para adicionar outras câmeras basta seguir o layout, e repetir os parâmetros com seus devidos
valores.
</p>
<p style="text-align: justify;">
<strong>1.3</strong> Caso seja uma migração e exista um arquivo supervisord.conf com as configurações das câmeras,
pode-se utilizar um conversor de supervisor para json, execute os passos abaixo para usá-lo.
</p>
```bash
gdown 1UVEM4WeaugcTujOQ7G2MPVBP3RPlPsp_
chmod +x converter_sup_to_docker
./converter_sup_to_docker
```
> Isso irá gerar um ``config.json`` na pasta /instalacoes, que poderá ser copiado para o docker 
> fazer a leitura das câmeras.  

### Funcionamento do sistema WPS_LPR

<p style="text-align: justify;">
<strong>2.</strong> O sistema assim que o container for iniciado já estará funcionando com as câmeras cadastradas no 
arquivo <code>/var/lib/ParkingPlus/WpsLpr/config/config.json</code>, qualquer alteração no arquivo o sistema
processará as alterações, e fará a devida ação, que poderá ser adicionar uma nova câmera, remover 
uma câmera, alterar algum parâmetro de alguma câmera:
</p>

### Consultando Logs do Sistema

**3.** As informações do sistema WPS_LPR, poderão ser consultadas pelo log *journal*, pelo comando:

```bash
journalctl CONTAINER_TAG=ParkingPlus:WPS_LPR
```

> Oportunamente pode se passar o parâmetro -f, para visualizar o log em tempo real.  

> ```
> journalctl -f CONTAINER_TAG=ParkingPlus:WPS_LPR --output cat
> ```

<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        .code-block {
            font-size: 0.7em; /* Ajusta o tamanho da fonte */
            white-space: pre-wrap; /* Permite quebras de linha no conteúdo pre */
        }
    </style>
    <title>Exemplo de Código</title>
</head>
<body>
    <p style="text-align: justify;">
        <strong>3.1</strong> A saída do comando será conforme abaixo:
    </p>
    <details>
        <summary>Clique para expandir</summary>
        <pre class="code-block">
2000-01-01 00:00:54.033 | INFO: ENT_PRINC_3_FRONTAL | [SNAPSHOT] | Placa detectada: PRZ4B93 | Tipo de Veículo: CAR | Marca/Modelo do Veículo: VOLKSWAGEN - POLO
2000-01-01 00:00:54.525 | INFO: ENT_PRINC_2_FRONTAL | [SNAPSHOT] | Placa detectada: JIR0H44 | Tipo de Veículo: CAR
2000-01-01 00:01:01.109 | INFO: ENT_PRINC_1_FRONTAL | [SNAPSHOT] | Placa detectada: JJV7842 | Tipo de Veículo: CAR
2000-01-01 00:01:04.525 | INFO: ENT_PRINC_1_TRASEIRA | [SNAPSHOT] | Placa detectada: JJV7842 | Tipo de Veículo: CAR
2000-01-01 00:01:05.330 | INFO: ENT_PRINC_1_FRONTAL | [SNAPSHOT] | Placa detectada: JJV7842 | Tipo de Veículo: CAR
2000-01-01 00:01:08.327 | INFO: ENT_SUL_1_FRONTAL | [SNAPSHOT] | Placa detectada: PRO6225 | Tipo de Veículo: CAR | Marca/Modelo do Veículo: TOYOTA - YARIS
2000-01-01 00:01:11.355 | INFO: ENT_SUL_1_TRASEIRA | [SNAPSHOT] | Placa detectada: PRO6225 | Tipo de Veículo: CAR
2000-01-01 00:01:12.203 | INFO: ENT_SUL_1_FRONTAL | [SNAPSHOT] | Placa detectada: PRO6225 | Tipo de Veículo: CAR | Marca/Modelo do Veículo: TOYOTA - YARIS
2000-01-01 00:01:20.439 | INFO: ENT_TOK_STOK_2_FRONTAL | [SNAPSHOT] | Placa detectada: SDK5G47 | Tipo de Veículo: CAR
2000-01-01 00:01:33.051 | INFO: ENT_SUL_2_FRONTAL | [SNAPSHOT] | Placa detectada: SSG1C35 | Tipo de Veículo: CAR
2000-01-01 00:01:36.270 | INFO: ENT_SUL_2_TRASEIRA | [SNAPSHOT] | Placa detectada: SSG1C35 | Tipo de Veículo: CAR
2000-01-01 00:01:37.016 | INFO: ENT_SUL_2_FRONTAL | [SNAPSHOT] | Placa detectada: SSG1C35 | Tipo de Veículo: CAR
2000-01-01 00:01:38.034 | INFO: SAI_2_SUL_FRENTE | [SNAPSHOT] | Placa detectada: SYV8D43 | Tipo de Veículo: CAR
2000-01-01 00:01:41.138 | INFO: SAI_2_SUL_TRASEIRA | [SNAPSHOT] | Placa detectada: SYV8D43 | Tipo de Veículo: CAR
2000-01-01 00:01:41.974 | INFO: SAI_2_SUL_FRENTE | [SNAPSHOT] | Placa detectada: SYV8D43 | Tipo de Veículo: CAR | Marca/Modelo do Veículo: FIAT - ARGO
2000-01-01 00:01:42.363 | INFO: ENT_TOK_STOK_1_FRONTAL | [SNAPSHOT] | Placa detectada: SCQ1H08 | Tipo de Veículo: CAR
2000-01-01 00:01:46.859 | INFO: ENT_TOK_STOK_1_FRONTAL | [SNAPSHOT] | Placa detectada: SCQ1H08 | Tipo de Veículo: CAR
2000-01-01 00:01:47.560 | INFO: SAI_SUL_3_TRASEIRA | [SNAPSHOT] | Placa detectada: PQH2967 | Tipo de Veículo: CAR
2000-01-01 00:01:49.070 | INFO: SAI_SUL_3_FRONTAL | [SNAPSHOT] | Placa detectada: PQH2967 | Tipo de Veículo: CAR
2000-01-01 00:01:57.925 | INFO: ENT_PRINC_2_FRONTAL | [SNAPSHOT] | Placa detectada: NDO7C97 | Tipo de Veículo: CAR
2000-01-01 00:01:58.670 | INFO: ENT_3_NORTE_FRENTE | [SNAPSHOT] | Placa detectada: QUH3A03 | Tipo de Veículo: CAR
2000-01-01 00:01:59.684 | INFO: ENT_PRINC_2_TRASEIRA | [SNAPSHOT] | Placa detectada: NDO7C97 | Tipo de Veículo: CAR
2000-01-01 00:02:01.278 | INFO: ENT_3_NORTE_TRASEIRA | [SNAPSHOT] | Placa detectada: QUH3A03 | Tipo de Veículo: CAR
2000-01-01 00:03:28.210 | INFO: ENT_MOTO_1 | Frame número: 705 | Placa: SCN3A36 | Confianca: 1.0
2000-01-01 00:03:29.215 | INFO: ENT_MOTO_1 | Frame número: 706 | Placa: SCN3A36 | Confianca: 1.0
2000-01-01 00:03:30.218 | INFO: ENT_MOTO_1 | Frame número: 707 | Placa: SCN3A36 | Confianca: 1.0
2000-01-01 00:03:31.224 | INFO: ENT_MOTO_1 | Frame número: 708 | Placa: SCN3A36 | Confianca: 1.0
        </pre>
    </details>
</body>
</html>

#### Alterando nível de log

**4.** Para alterar o nível de log, altere o arquivo:
```bash
/var/lib/ParkingPlus/WpsLpr/config/settings.xml
```
<p style="text-align: justify;">
Caso o arquivo não exista, utilize o sample na pasta para criá-lo, e altere o nível caso seja 
necessário. O log vem configurado com nível <em>INFO</em>, que exibe as informações conforme acima, caso 
queira depurar alguma informação, como o tempo gasto em cada etapa, altere para <em>DEBUG</em> ou <em>TRACE</em>,
e após as analises realizadas, retorne para <em>INFO</em>.
</p>
```xml
<settings>
  <log_level>INFO</log_level>
</settings>
```
<p style="text-align: justify;">
<em></em>
<strong>4.1</strong> A partir da versão 2.6.0 a mudança no nível do LOG será dinâmica, ou seja ao alterar no arquivo o LOG já passará a 
exibir o LOG nesse nível sem a necessidade de reiniciar a aplicação.
</p>

### Verificando câmeras online no sistema

**5.** Para verificar quantas e quais câmeras são, é necessário fazer uma requisição GET HTTP,
no seguinte endpoint:

```bash
http://ip_do_servidor_lpr:5000/api/v0/running_cameras
```
**5.1** O retorno da requisição será algo do tipo:

<details>
<summary>Clique para expandir</summary>
<pre>
{
  "Cameras online": {
    "LPR": [
      "ENT_MOTO_2 - 192.168.8.127 (Online)",
      "SAI_MOTO_1 - 192.168.8.143 (Online)",
      "SAI_MOTO_2 - 192.168.8.144 (Online)",
      "ENT_MOTO_1 - 192.168.8.126 (Online)"
    ],
    "WPS 360": [
      "ENT_PRINC_2_TRASEIRA - 192.168.20.199 (Online)",
      "ENT_PRINC_3_TRASEIRA - 192.168.20.190 (Online)",
...
      "SAI_SUL_3_FRONTAL - 192.168.20.196 (Online)",
      "SAI_PRINC_3_FRONTAL - 192.168.20.219 (Online)"
    ]
  },
  "Total de cameras online": 54,
  "Cameras offline": {
    "WPS 360": [
      "SAI_LOJISTA_FRONTAL - 192.168.20.38 (Offline)",
      "ENT_LOJISTA_TRASEIRA - 192.168.20.112 (Offline)"
    ]
  },
  "Total de cameras offline": 2
}

</pre>
</details>

### Solicitando um snapshot de uma câmera

**6.** Para solicitar um snapshot, é necessário fazer uma requisição GET HTTP, no seguinte endpoint:
```
http://ip_do_servidor_lpr:5000/api/v0/snapshot
```
**6.1** É necessário passar também o argumento `wps_topic`, exemplo:
```
http://127.0.0.1:5000/api/v0/snapshot?wps_topic=TESTE
```

O sistema retornará imediatamente a última imagem salva em buffer referente ao `wps_topic` fornecido.

Segue um exemplo de resposta da solicitação enviada:

```json
{
    "timestamp": timestamp, 
    "object_type": object_type,
        "imagem": {
            "data": snapshot_lpr,
            "type": "image/jpg",
            }
}
```
> As imagens estão codificadas em base64, com resolução 1280x720 e formato JPG.

### Verificando quantidade de Threads

**7.** Para verificar quantas threads do sistema WPS_LPR estão ativas, basta fazer uma requisição 
GET HTTP no endpoint abaixo:

```bash
http://ip_do_servidor_lpr:5000/api/v0/threads
```
O retorno será a quantidade de threads em execução, usado para DEBUG.

**7.1** Para verificar o nome das threads em execução no sistema WPS_LPR, faça uma requisição GET HTTP no endpoint:
```bash
http://ip_do_servidor_lpr:5000/api/v0/threads_details
```
O retorno será o detalhamento das threads, para ajudar no processo de DEBUG.

<details>
<summary>Clique para expandir</summary>
<pre>
{
  "thread_details": [
    {
      "name": "MainThread",
      "id": 139885484580992
    },
    {
      "name": "MonitorLogLevel",
      "id": 139885465700032
    },
    {
      "name": "pymongo_server_monitor_thread",
      "id": 139883041511104
    },
    {
      "name": "pymongo_kill_cursors_thread",
      "id": 139883145090752
    },
    {
      "name": "pymongo_server_rtt_thread",
      "id": 139882454316736
    },
    {
      "name": "Inotify (InotifyBuffer)",
      "id": 139884727596736
    },
    {
      "name": "Inotify (InotifyEmitter)",
      "id": 139884735989440
    },
    {
      "name": "Inotify (InotifyObserver)",
      "id": 139884878599872
    },
    {
      "name": "JSON monitoring",
      "id": 139884886992576
    },
    {
      "name": "Webserver WPS_LPR",
      "id": 139885328078528
    },
    {
      "name": "Schedule",
      "id": 139885319685824
    },
    {
      "name": "ThreadPoolExecutor-3_0",
      "id": 139885172213440
    },
    {
      "name": "ThreadPoolExecutor-3_1",
      "id": 139884895385280
    },
    {
      "name": "ThreadPoolExecutor-3_2",
      "id": 139883058296512
    },
    {
      "name": "ThreadPoolExecutor-3_3",
      "id": 139883049903808
    },
    {
      "name": "MQTT: SAI_MOTO_1",
      "id": 139875802146496
    },
    {
      "name": "MQTT: ENT_MOTO_1",
      "id": 139880675927744
    },
    {
      "name": "MQTT: ENT_MOTO_2",
      "id": 139878863976128
    },
    {
      "name": "Thread-826",
      "id": 139878880761536
    }
  ],
  "thread_types": {
    "MainThread": 1,
    "Daemon Threads": 8,
    "ThreadPoolExecutor": 4,
    "Inotify": 3,
    "Others": 3
  },
  "total_active_threads": 19
}

</pre>
</details>

### Exibindo a versão do WPS_LPR

**8.** Para verificar a versão do sistema, faça uma requisição GET HTTP no endpoint abaixo:

```bash
http://ip_do_servidor_lpr:5000/api/v0/version
```
```
{
  "version": "2.9.0"
}
```

### Verificando as conexões no WPS_LPR

**9.** Para verificar todas as conexões configuradas no sistema WPS_LPR, faça uma requisição GET HTTP no endpoint:

```bash
http://ip_do_servidor_lpr:5000/api/v0/connections
```

O retorno será a listagem das conexões configuradas no sistema.

<details>
<summary>Clique para expandir</summary>
<pre>
{
  "lpr": [
    {
      "camera_id": "1",
      "wps_topic": "ENT_MOTO_1",
      "camera_ip": "192.168.8.126",
      "ocr_time": "1/seg",
      "ocr_mode": "Moto",
      "publish_interval": "1000 ms"
    },
    {
      "camera_id": "2",
      "wps_topic": "ENT_MOTO_2",
      "camera_ip": "192.168.8.127",
      "ocr_time": "1/seg",
      "ocr_mode": "Moto",
      "publish_interval": "1000 ms"
    },
    {
      "camera_id": "3",
      "wps_topic": "SAI_MOTO_1",
      "camera_ip": "192.168.8.143",
      "ocr_time": "1/seg",
      "ocr_mode": "Moto",
      "publish_interval": "1000 ms"
    },
    {
      "camera_id": "4",
      "wps_topic": "SAI_MOTO_2",
      "camera_ip": "192.168.8.144",
      "ocr_time": "1/seg",
      "ocr_mode": "Moto",
      "publish_interval": "1000 ms"
    }
  ],
  "context": [
    {
      "camera_id": "5",
      "wps_topic": "ENT_PRINC_1_FRONTAL",
      "camera_ip": "192.168.20.193",
      "ocr_time": "1/seg",
      "ocr_mode": "Carro",
      "publish_interval": "1000 ms"
    },
...
    {
      "camera_id": "55",
      "wps_topic": "SAI_2_SUL_FRENTE",
      "camera_ip": "192.168.20.225",
      "ocr_time": "0/seg",
      "ocr_mode": "Carro",
      "publish_interval": "0 ms"
    },
    {
      "camera_id": "56",
      "wps_topic": "SAI_2_SUL_TRASEIRA",
      "camera_ip": "192.168.20.195",
      "ocr_time": "1/seg",
      "ocr_mode": "Carro e Moto",
      "publish_interval": "1000 ms"
    }
  ]
}

</pre>
</details>

### Reiniciando a aplicação

**10.** Para reiniciar o sistema WPS_LPR, utilize o endpoint abaixo:

```
http://ip_do_servidor_lpr:5000/api/v0/reboot_wpslpr
```

### Verificando vagas do modo _Segmentation_

**11.** Para obter a lista das vagas monitoradas pelas câmeras do tipo "**_segmentation_**":

```
http://ip_do_servidor_lpr:5000/general/spaces
```
> O sistema retornará algo como:

```
{
    "id": 1,
    "name": WPS_LPR Vaga 1",
    "sectorId": 1,
    "sectorName": WPS,
    "levelId": 1,
    "levelName": G1,
    "parkingId": 1,
    "parkingName": WPS G1,
    "tariffZone": 1,
    "disabled": False,
    "handicap": False,
    "xCoord": 0,
    "yCoord": 0
}
```

**12.** Para obter uma atualização das vagas monitoradas pelas câmeras do tipo "**_segmentation_**":

```
http://ip_do_servidor_lpr:5000/parkingSpaces/status/x
```
> Onde "**x**" é o tempo em segundos considerados para a consulta

<strong><em></em></strong>

### Obtendo a placa com o modo Rekognition

**13.** Para submeter uma imagem única para reconhecer a placa, utilize o método "_**rekognition**_" 

```
http://ip_do_servidor_lpr:5000/api/v0/rekognition
```
<p style="text-align: justify;">
A imagem pode estar nas extensões, <strong>bmp</strong>, <strong>jpg</strong> ou <strong>png</strong>, também é aceito 
em <strong>base64</strong>. Para enviar, utilize o método POST, no corpo da mensagem coloque <em>image</em> 
na <em>key</em>, e a imagem que quer submeter em <em>value</em>. O retorno da solicitação será o seguinte:
</p>
```
{
    "timestamp": "2024-11-07 14:47:28.931",
    "object_type": "CAR",
    "coor_object": {
        "xmin": 183,
        "xmax": 857,
        "ymin": 171,
        "ymax": 558
    },
    "plate_number": "SDH1I20",
    "confidence": 1.0,
    "car_model": "RAM - rampage"
}
```