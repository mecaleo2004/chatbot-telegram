# Chatbot Telegram de Consulta de Temperatura com n8n

## 1. Descrição do projeto

Este projeto consiste em um **chatbot para Telegram desenvolvido com n8n** que permite consultar a temperatura atual de uma cidade utilizando a API do **OpenWeather**.

O usuário envia ao bot uma cidade no formato:

```text
Cidade,UF,BR
```

Exemplo:

```text
Quixadá,CE,BR
```

O workflow recebe a mensagem pelo Telegram, trata o texto informado, consulta a API OpenWeather, valida a resposta e retorna a temperatura atual da cidade.

Exemplo de retorno:

```text
🌤️ A temperatura em Quixadá é de 34°C.
```

Caso a cidade não seja encontrada ou a consulta não retorne os dados esperados, o chatbot envia:

```text
❌ Cidade não encontrada. Use o formato Cidade,UF,BR (ex.: São Paulo,SP,BR).
```

---

## 2. Funcionamento do workflow

O fluxo é composto pelas seguintes etapas:

1. **Telegram Trigger** — recebe a mensagem enviada pelo usuário.
2. **Edit Fields** — captura o texto, armazena-o no campo `queue` e realiza a formatação necessária.
3. **HTTP Request** — envia a cidade para a API OpenWeather.
4. **IF** — verifica se a requisição foi bem-sucedida e se a temperatura foi retornada.
5. **Edit Fields** — formata a mensagem de sucesso e arredonda a temperatura.
6. **Send a text message** — envia a temperatura ao usuário no Telegram.
7. **Send a text message (erro)** — envia a mensagem de cidade não encontrada quando a validação falha.

A variável interna `queue` contém a cidade tratada. Na requisição para o OpenWeather, ela é enviada pelo parâmetro `q`, que é o nome esperado pela API.

---

## 3. Arquivo do workflow

O workflow deve estar disponível no repositório com o nome:

```text
workflow-telegram-chatbot.json
```

Esse arquivo pode ser importado diretamente no n8n.

> **Importante:** o arquivo JSON publicado no repositório não deve conter tokens, chaves de API, senhas ou outras credenciais reais.

---

## 4. Como importar o workflow no n8n

1. Acesse sua instância do **n8n**.
2. Crie ou abra a área de workflows.
3. Utilize a opção de **Import from File / Importar de arquivo**.
4. Selecione o arquivo:

```text
workflow-telegram-chatbot.json
```

5. Após a importação, abra o workflow.
6. Configure as credenciais do Telegram nos nós correspondentes.
7. Configure a chave do OpenWeather.
8. Salve o workflow.
9. Ative/publique o workflow para que o Telegram Trigger possa receber mensagens.

Após a importação, as credenciais precisam ser configuradas na instalação do n8n em que o workflow será executado.

---

## 5. Variáveis esperadas

O projeto utiliza as seguintes variáveis:

```env
OPENWEATHER_API_KEY=
TELEGRAM_BOT_TOKEN=
```

### `OPENWEATHER_API_KEY`

Armazena a chave utilizada para autenticação nas requisições à API OpenWeather.

### `TELEGRAM_BOT_TOKEN`

Armazena o token utilizado para autenticar o bot do Telegram.

Os valores reais dessas variáveis **não devem ser enviados ao GitHub**.

---

## 6. Configuração da credencial do Telegram no n8n

Para utilizar o chatbot é necessário possuir um bot criado no Telegram e seu respectivo token.

A variável esperada pelo projeto é:

```env
TELEGRAM_BOT_TOKEN=SEU_TOKEN_DO_TELEGRAM
```

No n8n:

1. Acesse **Credentials**.
2. Crie uma nova credencial do tipo **Telegram API**.
3. Informe o token do bot no campo solicitado pela credencial.
4. Salve a credencial.
5. Abra o nó **Telegram Trigger** e selecione essa credencial.
6. Abra o nó responsável pela mensagem de sucesso e selecione a mesma credencial.
7. Abra o nó responsável pela mensagem de erro e selecione a mesma credencial.

O token real não deve ser escrito no arquivo `README.md` nem incorporado ao JSON publicado no repositório.

---

## 7. Configuração da credencial do OpenWeather no n8n

É necessário possuir uma chave válida da API OpenWeather.

A variável esperada pelo projeto é:

```env
OPENWEATHER_API_KEY=SUA_CHAVE_OPENWEATHER
```

A variável deve estar disponível no ambiente em que o n8n é executado.

No nó **HTTP Request**, a consulta utiliza o endpoint:

```text
https://api.openweathermap.org/data/2.5/weather
```

Os parâmetros utilizados são:

| Parâmetro | Valor |
|---|---|
| `q` | cidade formatada armazenada em `queue` |
| `appid` | chave da API OpenWeather |
| `units` | `metric` |
| `lang` | `pt_br` |

Para uma instalação em que o acesso a variáveis de ambiente esteja habilitado nas expressões do n8n, o parâmetro `appid` pode utilizar:

```text
{{ $env.OPENWEATHER_API_KEY }}
```

A chave real da OpenWeather não deve ser publicada no repositório.

---

## 8. Exemplo de configuração das variáveis

As variáveis esperadas podem ser documentadas em um arquivo `.env.example`:

```env
OPENWEATHER_API_KEY=
TELEGRAM_BOT_TOKEN=
```

O arquivo contendo os valores reais, como `.env`, deve permanecer fora do controle de versão.

Exemplo de `.gitignore`:

```gitignore
.env
.env.*
!.env.example
```

---

## 9. Como executar o chatbot

Depois de importar o workflow, configurar as credenciais e ativá-lo:

1. Abra o Telegram.
2. Localize o bot configurado no workflow.
3. Envie uma cidade no formato:

```text
Cidade,UF,BR
```

### Exemplo de teste

Envie:

```text
Quixadá,CE,BR
```

O chatbot deverá consultar o OpenWeather e retornar uma mensagem semelhante a:

```text
🌤️ A temperatura em Quixadá é de 34°C.
```

O valor da temperatura depende das condições meteorológicas no momento da consulta.

### Teste de cidade inválida

Envie, por exemplo:

```text
CidadeInexistente,XX,BR
```

O retorno esperado é:

```text
❌ Cidade não encontrada. Use o formato Cidade,UF,BR (ex.: São Paulo,SP,BR).
```

---

## 10. Formato da entrada

Utilize preferencialmente:

```text
Cidade,UF,BR
```

Onde:

- `Cidade` representa o nome da cidade;
- `UF` representa a sigla do estado;
- `BR` representa o código do Brasil.

Exemplos:

```text
Fortaleza,CE,BR
Quixadá,CE,BR
São Paulo,SP,BR
Belo Horizonte,MG,BR
```

---

## 11. Segurança

Antes de enviar os arquivos para o GitHub, verifique se não existem informações sensíveis no repositório.

Não publique:

- `OPENWEATHER_API_KEY` com valor real;
- `TELEGRAM_BOT_TOKEN` com valor real;
- arquivos `.env`;
- senhas;
- tokens;
- outras credenciais.

O repositório deve conter apenas referências às variáveis e exemplos sem valores reais.

---

## 12. Estrutura sugerida do repositório

```text
.
├── workflow-telegram-chatbot.json
├── README.md
├── .env.example
└── .gitignore
```

O arquivo `workflow-telegram-chatbot.json` contém o workflow exportado do n8n e o `README.md` contém as instruções necessárias para instalação, configuração e execução do chatbot.
