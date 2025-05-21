---

### Manual do método rekognition para Detecção de Placa (LPR) e Identificação de Veículo

Este manual descreve o processo de envio de imagens para o endpoint que processa uma imagem e retorna a placa do veículo, o tipo de veículo, e as coordenadas. O cliente pode enviar a imagem como um arquivo binário ou codificado em base64, utilizando a chave `image` no corpo da requisição.

---

#### **1. Introdução**
O endpoint processa uma imagem de um veículo utilizando um modelo de Reconhecimento Automático de Placas (LPR) para extrair a placa, identificar o tipo de veículo e retornar as coordenadas do objeto detectado.

#### **2. URL do Endpoint**
- **URL:** `http://ip_wpslpr:5000/api/v0/rekognition`
- **Método HTTP:** `POST`

---

#### **3. Requisitos**
- **Headers:**
  - `Content-Type: multipart/form-data` ou `application/json`
---

#### **4. Parâmetros da Requisição**

##### **Envio com Binário (multipart/form-data)**
Ao utilizar o formato `multipart/form-data`, a imagem é enviada diretamente no corpo da requisição, com a chave `image`.

```bash
POST /rekognition HTTP/1.1
Host: http://ip_wpslpr:5000/api/v0/rekognition`
Content-Type: multipart/form-data

{
  "image": <arquivo de imagem em formato binário>
}
```

##### **Envio com Base64 (application/json)**
Quando a imagem estiver codificada em base64, ela deve ser enviada no formato `application/json`, usando também a chave `image`.

```bash
POST /rekognition HTTP/1.1
Host: http://ip_wpslpr:5000/api/v0/rekognition
Content-Type: application/json

{
  "image": "<string base64 codificada>"
}
```

---

#### **5. Exemplo de Requisição**

##### **Envio de Imagem em Binário**

```bash
curl -X POST "http://ip_wpslpr:5000/api/v0/rekognition" \
-H "Content-Type: multipart/form-data" \
-F "image=@carro.jpg"
```

##### **Envio de Imagem em Base64**

```bash
curl -X POST "http://ip_wpslpr:5000/api/v0/rekognition" \
-H "Content-Type: application/json" \
-d '{
  "image": "/9j/4AAQSkZJRgABAQAAAQABAAD..."
}'
```

---

#### **6. Estrutura da Resposta**

A resposta inclui o timestamp da requisição, o tipo de objeto detectado (ex.: carro), as coordenadas do objeto, a placa reconhecida e o nível de confiança da predição.

##### **Resposta de Sucesso**

```json
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

##### **Respostas de Erro**

```json
{
  "status": "400",
  "mensagem": "Nenhuma imagem encontrada no formulário"
}
```
```json

{
  "status": "400",
  "mensagem": "Nenhum arquivo selecionado"
}
```
```json
{
  "status": "400",
  "mensagem": "Não foi possível decodificar a imagem"
}
```
```json
{
  "status": "400",
  "mensagem": "Nenhuma imagem em base64 encontrada no corpo da solicitação"
}
```
```json
{
  "status": "415",
  "mensagem": "Tipo de conteúdo não suportado"
}
```

---

#### **7. Tratamento de Erros**

- **200 OK:** Requisição processada com sucesso.
- **400 Bad Request:** Erro no formato ou no conteúdo da requisição.
- **415 Unsuported Media Type:** Erro no formato de Mídia, ou não aceito.
- **500 Internal Server Error:** Erro interno no servidor.

---

---

#### **8. Fluxo de Execução**

```plaintext
Cliente → Envia requisição com imagem (Binário ou Base64) usando a chave 'image'
     ↓
Servidor → Valida o Formato e o Tamanho da Imagem
     ↓
Servidor → Submete Imagem ao Modelo de LPR
     ↓
Servidor → Extrai Placa, Tipo de Veículo e Coordenadas
     ↓
Servidor → Retorna Resultado (JSON)
```
---

#### **9. Conclusão**
Este manual oferece um guia prático e claro para envio de imagens ao endpoint, visando obter a placa, tipo de veículo e coordenadas. Ao seguir este formato, o cliente pode enviar imagens como binário ou codificadas em base64, garantindo a flexibilidade de integração com diferentes plataformas.
