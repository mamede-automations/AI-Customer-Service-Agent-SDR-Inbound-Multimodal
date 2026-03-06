# 🤖 AI Customer Service Agent & SDR Inbound (Multimodal)

This is an autonomous AI Agent focused on initial contact and qualification of inbound leads. It acts as a virtual assistant that understands the lead's needs, consults company materials to send the correct response, qualifies the opportunity, and, when necessary, seamlessly transfers the contact to a human SDR.

*(Este é um Agente de IA autônomo focado no primeiro atendimento e qualificação de leads Inbound. Ele atua como uma assistente virtual que entende a demanda do lead, consulta os materiais da empresa para enviar a resposta correta, qualifica a oportunidade e transfere o atendimento de forma fluida para um SDR humano.)*

### 🛠️ Tech Stack & Tools / Ferramentas Utilizadas

* **n8n:**

Workflow orchestration.

*(Orquestração de todo o fluxo lógico.)*

* **WhatsApp APIs:**

Currently running on Evolution API, but the architecture is fully compatible with the Official Meta API and other unofficial providers.

*(Rodando atualmente na Evolution API, mas a arquitetura é totalmente compatível com a API Oficial da Meta e outros provedores.)*

* **CRM (Baserow & Zoho CRM):**

Lead capture and management. This specific flow uses Baserow for agile database creation, backed by solid experience in robust Zoho CRM integrations.

*(Captura e gestão de leads. Este fluxo usa Baserow para criação ágil de banco de dados, com experiência sólida em integrações complexas também com Zoho CRM.)*

* **Chatwoot:**

Omnichannel platform for human handoff, team management, and multi-account operation.

*(Plataforma omnichannel para transbordo humano, gestão de times e operação multi-contas.)*

* **Google Gemini (Flash & Pro):**

The brain of the agent, handling text, audio transcription, and image analysis.

*(Cérebro do agente, lidando com texto, transcrição de áudio e análise de imagem.)*

* **Qdrant (Vector DB):**

RAG (Retrieval-Augmented Generation) for precise company data retrieval.

*(Banco de dados vetorial para buscar informações reais dos serviços.)*

* **Redis:**

Queue management and state control (Handoff lock).

*(Gerenciamento de fila de mensagens e controle de estado (trava de Handoff).)*

* **PostgreSQL:**

Chat memory storage.

*(Armazenamento da memória da conversa.)*

---

### ⚙️ Architecture & Workflow / Arquitetura e Fluxo

#### 1. Reception & CRM Registration / Recepção e Registro

The system captures the incoming message via Webhook, checks if the client exists in the database, and registers new leads instantly. It also checks Redis to see if a human is already talking to this lead, preventing the AI from interrupting.

*(O sistema captura a mensagem via Webhook, verifica se o cliente existe no banco e cadastra novos leads instantaneamente. Também checa no Redis se um humano já está falando com esse lead, evitando que a IA atrapalhe.)*

> `[COLOQUE A IMAGEM AQUI: Print dos primeiros nós do n8n, mostrando o Webhook, Filtro de Dados e o Baserow]`

#### 2. Message Queue & Anti-Spam (Redis) / Fila de Mensagens

To prevent the AI from generating separate answers when a lead sends multiple short messages (e.g., "Hi", "How are you?", "Price?"), Redis creates a buffer. The bot waits, groups everything into a single context, and then processes it.

*(Para evitar que a IA responda várias vezes quando o lead manda mensagens curtas (ex: "Oi", "Tudo bem?", "Preço?"), o Redis cria um buffer. O robô aguarda, junta tudo em um único contexto e só então processa.)*

> `[COLOQUE A IMAGEM AQUI: Print da etapa de fila com Redis e Wait]`

#### 3. Multimodal Processing / Processamento Multimodal

A Switch node checks the media type. If it's audio, it downloads and uses Gemini to transcribe it. If it's an image, Gemini Vision analyzes the content so the AI understands the context.

*(Um nó Switch verifica o tipo de mídia. Se for áudio, baixa e usa o Gemini para transcrever. Se for imagem, o Gemini Vision analisa o conteúdo para a IA entender o contexto.)*

> `[COLOQUE A IMAGEM AQUI: Print do Switch de áudio/texto/imagem e a transcrição no Gemini]`

#### 4. AI Agent & RAG / Agente de IA e RAG

The core of the operation. The Agent accesses a Vector Database (Qdrant) containing company scripts and project scopes. It crafts standard responses with portfolio links and asks strategic questions to extract budget, dates, and target audience.

*(O coração da operação. O Agente acessa um banco de dados vetorial (Qdrant) com os scripts e escopos da empresa. Ele formula respostas com links de portfólio e faz perguntas estratégicas para extrair orçamento, datas e público-alvo.)*

> `[COLOQUE A IMAGEM AQUI: Print mostrando o Agente, Postgres Memory e Qdrant]`

#### 5. Human Handoff & Omnichannel Management / Transbordo Humano (Chatwoot)

If the lead confirms the generated summary or asks something out of scope, the AI pauses itself (via Redis lock) and assigns the conversation to Chatwoot. Chatwoot allows the operation to scale by dividing service into specific teams (e.g., SDR, Support, Sales), managing multiple accounts/inboxes, and providing a clean interface for human agents to take over effortlessly.

*(Se o lead confirma o resumo gerado ou foge do escopo, a IA se pausa (via trava no Redis) e atribui a conversa ao Chatwoot. O Chatwoot permite escalar a operação dividindo o atendimento em times (ex: SDR, Suporte, Vendas), gerenciando múltiplas contas/caixas de entrada, e fornecendo uma interface limpa para os humanos assumirem o controle.)*

> `[COLOQUE A IMAGEM AQUI: Print do final do fluxo no n8n indo pro Chatwoot]`

> `[COLOQUE A IMAGEM AQUI: Print da tela do próprio CHATWOOT, mostrando as caixas de entrada e times]`

---

### 🚀 Roadmap / Próximas Implementações

* [ ] Integration with automated meeting scheduling systems

*(Integração com sistema de agendamento automático de reuniões.)*

* [ ] CRM status update automation

*(Automação de atualização de status do lead dentro do CRM.)*
