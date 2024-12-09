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
2000-01-01 00:00:00,327 | INFO: ENTG1A_GYN | Conectando camera: 10.62.65.31:1
2000-01-01 00:00:00,328 | INFO: ENTG1B_GYN | Conectando camera: 10.62.65.32:1
2000-01-01 00:00:00,328 | INFO: ENT1718ESQ_GYN | Conectando camera: 10.62.65.33:1
2000-01-01 00:00:03,819 | INFO: ENTG1A_GYN | Camera conectada: 10.62.65.31
2000-01-01 00:00:03,820 | INFO: ENTG1A_GYN | Limiar de movimento: 10 | OCR por Segundo : 1
2000-01-01 00:00:07,053 | INFO: ENTG1B_GYN | Camera conectada: 10.62.65.32
2000-01-01 00:00:07,054 | INFO: ENTG1B_GYN | Limiar de movimento: 10 | OCR por Segundo : 1
2000-01-01 00:00:07,287 | INFO: ENTG1A_GYN | Frame número: 10 | Placa: SBZ3J28 | Confianca: 1.0
2000-01-01 00:00:09,271 | INFO: ENTG1A_GYN | Frame número: 12 | Placa: SBZ3J28 | Confianca: 1.0
2000-01-01 00:00:09,305 | INFO: ENT1718ESQ_GYN | Camera conectada: 10.62.65.33
2000-01-01 00:00:14,292 | INFO: ENTG1A_GYN | Frame número: 14 | Placa: SBZ3J28 | Confianca: 1.0
2000-01-01 00:01:29,448 | INFO: ENT1718ESQ_GYN | Frame número: 25 | Placa: NKX7007 | Confianca: 1.0
2000-01-01 00:01:29,775 | INFO: ENTG1B_GYN | Frame número: 91 | Placa: SDA6F87 | Confianca: 1.0
2000-01-01 00:01:30,651 | INFO: ENT1718ESQ_GYN | Frame número: 26 | Placa: NKX7007 | Confianca: 1.0
2000-01-01 00:01:31,291 | INFO: ENTG1B_GYN | Frame número: 93 | Placa: SDA6F87 | Confianca: 1.0
2000-01-01 00:01:35,773 | ERROR: SAIE2DIR | O IP 10.62.65.45 não respondeu. Tentando novamente em 30 segundos...
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
http://ip_do_servidor_lpr:5000/cgi-bin/running_cameras.cgi
```
**5.1** O retorno da requisição será algo do tipo:

<details>
<summary>Clique para expandir</summary>
<pre>
Cameras offline
"SAIE2DIR - 10.62.65.45 (Offline) - SAIE2DIR - 10.62.65.45 (Offline)"
Cameras online
"ENTG1A - 10.62.65.31 (Online) - ENTG1B - 10.62.65.32 (Online) -
 ENT1718ESQ - 10.62.65.33 (Online)  - ENT1718DIR - 10.62.65.34 (Online) -
 ENTE1 - 10.62.65.36 (Online) - ENTE2ESQ - 10.62.65.37 (Online)  -
 ENTE2DIR - 10.62.65.38 (Online) - SAI1718ESQ - 10.62.65.41 (Online) ...
 - SAIE1 - 10.62.65.44 (Online) - SAIE3 - 10.62.65.46 (Online)"
Total de cameras offline
2
Total de cameras online
24
</pre>
</details>

### Solicitando um snaphot de uma câmera

**6.** Para solicitar um snapshot, é necessário fazer uma requisição GET HTTP, no seguinte endpoint:
```
http://ip_do_servidor_lpr:5000/cgi-bin/snapshot.cgi
```
**6.1** É necessário passar também o argumento `wps_topic`, exemplo:
```
http://127.0.0.1:5000/cgi-bin/snapshot.cgi?wps_topic=TESTE
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
http://ip_do_servidor_lpr:5000/cgi-bin/threads.cgi
```
O retorno será a quantidade de threads em execução, usado para DEBUG.

### Reiniciando a aplicação

**8.** Para reiniciar o sistema WPS_LPR, utilize o endpoint abaixo:

```
http://ip_do_servidor_lpr:5000/cgi-bin/reboot_wpslpr.cgi
```

### Verificando vagas do modo _Segmentation_

**9.** Para obter a lista das vagas monitoradas pelas câmeras do tipo "**_segmentation_**":

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

**10.** Para obter uma atualização das vagas monitoradas pelas câmeras do tipo "**_segmentation_**":

```
http://ip_do_servidor_lpr:5000/parkingSpaces/status/x
```
> Onde "**x**" é o tempo em segundos considerados para a consulta

<strong><em></em></strong>

### Obtendo a placa com o modo Rekognition

**11.** Para submeter uma imagem única para reconhecer a placa, utilize o método "_**rekognition**_" 

```
http://ip_do_servidor_lpr:5000/cgi-bin/rekognition.cgi
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