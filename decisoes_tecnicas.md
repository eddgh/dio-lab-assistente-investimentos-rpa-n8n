# 🧩 Decisões Técnicas

### 📌 Etapa *Code in JavaScript1*  
O sistema gera todas as mensagens possíveis para cada perfil (conservador, moderado, arrojado).  
No entanto, apenas a mensagem correspondente ao perfil do cliente é retornada no resultado final.  

Essa escolha garante:  
- Clareza na saída.  
- Evita confusão.  
- Mantém o foco no que realmente interessa para o cliente.  

A lógica completa continua visível no código, permitindo que o avaliador entenda o processo, mas o resultado exibido é enxuto e objetivo.  
**Comprovação:** verificar a imagem [07.jpeg](./img-mvp/07.jpeg) na pasta `/img-mvp`.

---

### 📌 Etapa *OpenAI – node Message a model – Preparação*  
Por falta de créditos na OpenAI, configurei o node para usar a API da **Groq**.  

**Configurações realizadas:**  
1. Mantive o node da OpenAI.  
2. Criei uma API Key na Groq.  
3. Inserida no campo *API KEY*.  
4. Alterei a *Base URL* para `https://api.groq.com/openai/v1`.  
5. Conexão realizada com sucesso.  

**Problema inicial:**  
- Erro 400: `"Field 'store' only supports false or null"`.  
- Causa: a Groq não implementa o parâmetro `store` usado pela OpenAI.  

**Solução:**  
- Desativei a opção *Store* nas configurações do node.  
- Ajustei o campo *Model* para “From list” e selecionei `OPENAI/GPT-OSS-120B`.  

✅ Resultado: o node funcionou corretamente e consegui usar a OpenAI via Groq sem bloqueio por falta de créditos.

---

### 📌 Etapa *OpenAI – node Message a model – Manutenção dos e-mails fictícios*  
Mesmo sabendo que os e‑mails são fictícios, optei por processá‑los neste node.  
Motivo: vivenciar melhor como seria o comportamento do n8n em produção real, sem precisar de dados verdadeiros.  

Mais à frente, esses e‑mails fictícios são tratados no node IF para garantir que não sejam enviados.

---

### 📌 Etapa *OpenAI – node Message a model – Preparação do campo de e‑mail*  
**Primeira tentativa:**  
```json
{
  "email_to":"...",
  "subject":"...",
  "text_body":"...",
  "html_body":"..."
}
```
Problema: alguns e‑mails apareciam no output e outros não, apesar de todos os clientes terem e‑mail preenchido.  

**Solução:**  
```json
{
  "email_to":{{ $json.email_to }},
  "subject":"...",
  "text_body":"...",
  "html_body":"..."
}
```
Resultado: todos os e‑mails foram obtidos corretamente na saída.

---

### 📌 Etapa *IF – Tratativa dos e‑mails fictícios*  
Decisão: manter os e‑mails fictícios até esta etapa e aplicar dupla validação.  

**Configuração:**  
- Condição *matches regex*: `^[^@\s]+@[^@\s]+\.[^@\s]+$` (validação de formato).  
- Condição *is false*: `{{ $json.ok }}`.  

Como o boolean `ok` vem sempre como `true`, o resultado do IF é forçado para o **False Branch**, impedindo o envio real pelo Gmail.  

✅ Resultado: simulação completa do fluxo, mas sem risco de disparar e‑mails para domínios reais.

---

### 📌 Etapa *Gmail node - Alterado o modo de Autorização das Credenciais de OAuth para Service Account*  
Decisão: substitui o uso de OAuth por Service Account, simplificando a renovação de credenciais do projeto, o que é mais adequado para processos que rodam em segundo plano.
