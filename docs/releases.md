# Releases WPS_LPR

#### 2.9.2 - 2025-05-16

* Correção do envio de tipo pessoa como moto. Agora pessoa é enviado 
como pessoa e não moto como nas versões 2.9.0 e 2.9.1.

#### 2.9.1 - 2025-05-12

* Correção do modelo de motos que não era importado, ao selecionar o modo LATAM.  
  &nbsp;&nbsp;&nbsp;&nbsp;P.S: Só funciona com placas de motos brasileiras.

#### 2.9.0

* Novo método para detecção de placa e tipo de objeto;
* Adicionada verificação de status da câmera para tipos: segmentação e contexto;
* Nomear threads para facilitar a depuração;
* Correção de segmentação, e implementação de publicação de placas por vaga via MQTT;
* Modificação na verificação da existência de LEDs, que agora é realizada por threads para agilizar o processamento;
* Adicionado suporte para câmeras do fabricante Axis;

#### 2.7.3

* Correção do frame_ocr_rate recebido LPR Admin.

#### 2.7.2

* Correção do parking_spaces_quantity quando valor setado for 0.


#### 2.7.1

* Correção do Snapshot.

#### 2.7.0

* Separação do MQTT do Docker da aplicação WPS_LPR;
* Correção do problema de acesso a GPU pelo container após daemon-reload executado no host;
* Adicionado os modelos de detecção de marca e modelo de veículos para uso futuro;
* Correção do acesso a GPU pelo container após atualização do nvidia-container-toolkit;
* Correção do Snapshot das versões 2.6.x que quebravam após solicitação de Snaps;
* Envio pelo MQTT da placa reconhecida na solicitação do Snapshot, para LPR e WPS_360;
* Adição de timestamp nos logs salvos em /var/log/wpslpr/.


#### 2.6.6

* Correção do problema de acesso à GPU pelo container, por conta da atualização do nvidia-container-toolkit.

#### 2.6.5

* Removido as câmeras Hikvision e Intelbras(modelos com API recentes) do
  envio de configurações via ONVIF, para ser enviado por API;
* Corrigido a obtenção do modelo das câmeras Intelbras;
* Correção da resolução das câmeras, agora sempre usa a mais alta. Porém no engine sempre será 1280x720, 
inclusive o ROI é baseado nessa resolução.

#### 2.6.4

* Tentativa de solucionar o problema de "cannot convert float infinity to integer".

#### 2.6.3

* Correção do erro de publicação do MQTT, quando usuário insere valor
  inválido para o ocr_time.

#### 2.6.1

* Correção do detector de movimentos;
* Troca do modelo de detecção de placas de motos;

#### 2.6.0

* Inclusão de Novo Modelo de Detecção e OCR para placas de Motos;
* Inclusão de suporte para câmeras Pumatronix;
* Novo método de publicação das placas no MQTT, que agora será por tempo definido;
* Novo método de tempo de OCR, poderá ser executado mais de um por segundo, e 
será publicado apenas a melhor placa;
* Novo método de ativação do LED da câmera Hikvision, agora ele fica ativo por um 
tempo maior, e também é ativado no Snapshot;
* Alterado número fixo de vagas que a câmera no tipo Segmentação poderia monitorar, 
eram 3, e agora pode ser entre 1 e 4 vagas;
* Separado em Threads, Execução do OCR e Ativação do LED, para evitar atrasos por 
demora na resposta da API, e na execução do OCR;
* Nessa versão ao mudar o nível de LOG, ele já passa a ser exibido imediatamente, 
sem a necessidade de reiniciar a aplicação.

#### 2.5.1

* Correção do timeout de modelo da câmera Intelbras;
* Correção de aplicação de LED Hikvision, e controle da potência.

#### 2.5.0

* Implementado o reconhecimento de tipo de veículo para Carros e Motos;
* Criado um endpoint para envio de imagens para o WPS_LPR reconhecer a placa do veículo. /cgi-bin/rekognition.cgi

#### 2.4.1

* Corrigido o problema que a câmera não iniciava, após não receber a foto do snapshot de verificação. Caso ela não receba, abrirá o modo antigo, ou seja vídeo;
* Incluída uma configuração de protocolo de comunicação para o RTSP, podendo ser UDP ou TCP. A configuração é por câmera, na chave "protocol", os valores esperados são "udp" ou "tcp".

#### 2.4.0

* Incluído um observador no arquivo de configurações, para caso haja mudança, ele 
atualizar as variáveis que sofreram mudanças sem que haja necessidade de reiniciar a aplicação;
* Alterado o protocolo de conexão na câmera, de TCP para UDP, para evitar atrasos 
no vídeo, quando a rede for ruim e houver retransmissão de pacotes;
* Removido o suporte da câmera Pumatronix temporariamente.

#### 2.3.1

* Resolvido perda de conexão após reconexão da câmera;
* Removida a câmera TP-LINK;
* Alterado delay snapshot de 0,5seg para 1,0seg, para garantir que o veículo 
estará em um ponto melhor ao ser solicitado o snapshot;
* Alterada a confiança mínima do primeiro caractere, para garantir que mesmo 
* com um score menor, pode ser correto, sempre tenha placa disponível.

#### 2.3.0

* Controle de LED Hikvision;
* Frames por snapshot câmeras Hikvision e Intelbras.

#### 2.2.0

* WPS Segmentation Incluído.
* Criado a API de consulta das vagas, e as últimas alterações destas vagas.
* Correção no snapshot da Intelbras, para incluir qual o canal será feito.
