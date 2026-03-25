📑 Documentação do Projeto: Motor Field Control ↔️ BI Real-Time
🎯 Objetivo
Automatizar a extração, tratamento e visualização de Ordens de Serviço (OS) do Field Control. O sistema elimina a necessidade de exportações manuais de planilhas, criando um espelho de dados em tempo real no Supabase para gestão estratégica via Looker Studio.

🏗️ Arquitetura do Sistema
O fluxo segue a seguinte esteira de dados:
Field Control (API/Webhook) ➔ Make.com (Orquestrador) ➔ Supabase (PostgreSQL) ➔ Looker Studio (BI)

🛠️ Componentes Técnicos
1. Integração e Inteligência (Make.com)
O cenário no Make foi desenhado com 6 etapas para garantir a integridade dos dados:

Gatilho (Webhook): Escuta eventos de criação e atualização de OS.

Enriquecimento (HTTP Get): Busca detalhes da OS, nome real do cliente e status das tarefas (Tasks), superando a limitação de dados brutos da API.

Processamento de Texto (Regex): Extração cirúrgica de dados inseridos manualmente pelos técnicos na descrição.

Campos Extraídos: Placa, Cooperado, Contato, Email, Login, Senha, Id_espelhamento e Satelital.

Segurança: Implementada "trava de linha" ([^\n\r]*) para evitar que campos vazios capturem dados da linha de baixo.

Carga de Dados (Upsert): Utiliza o id_os como Chave Primária. Se a OS já existe, ele atualiza; se é nova, ele insere.

2. Camada de Dados (Supabase)
Motor: PostgreSQL 15.

Segurança: Conexão via túnel SSL criptografado (Portas 5432/6543).

Estrutura: Tabela os_espelhamento com 17 colunas, incluindo backup da descricao_original para auditoria humana.

3. Visualização (Google Looker Studio)
Conexão: Direta via conector PostgreSQL com certificado prod-ca-2021.crt.

Tratamento de Interface: Campo calculado Status_Dashboard que traduz termos técnicos em emojis e português (ex: done ➔ ✅ Concluído).

Dashboard: Visão de KPIs (Total de OS, % Conclusão) e Tabela Operacional detalhada.

🚀 Como Manter o Sistema
Automação: O "Scheduling" deve permanecer em ON no Make.com.

Logs: Erros de processamento podem ser conferidos na aba History do Make.

Atualização de BI: O Looker Studio atualiza a cada 15 minutos (ou via botão "Atualizar dados" manual).

Analista Responsável: Davi Scholze# gerenciamento-os