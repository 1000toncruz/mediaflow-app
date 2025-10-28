📦 MediaFlow — Repositório Inteligente de Mídias
🧠 Descrição

O MediaFlow é um aplicativo interno desenvolvido para centralizar, organizar e compartilhar as mídias de clientes (vídeos, fotos, capas, etc.) de uma empresa de marketing.
Inspirado em plataformas como Pipefy, mas com foco total em armazenamento hierárquico e colaborativo, o app simplifica o fluxo de criação e gestão de conteúdo entre membros da equipe.

🎯 Objetivo

Permitir que a equipe:

Faça upload de mídias organizadas por Cliente → Data → Tipo de Post (Reels, Story, Feed, Capa, etc);

Acesse e baixe arquivos de outros membros facilmente;

Visualize tudo em uma navegação hierárquica simples e intuitiva;

Tenha autenticação segura para acesso restrito;

Libere espaço automaticamente após 30 dias, com exclusão automática de arquivos antigos.

🧩 Funcionalidades Principais

✅ Cadastro e login individual (Firebase Auth);

✅ Upload e download de arquivos (Cloudflare R2);

✅ Organização hierárquica por Cliente, Data e Tipo;

✅ Armazenamento de metadados no MongoDB;

✅ Exclusão automática de mídias após 30 dias (Node Cron);

✅ Controle de acesso interno (somente equipe autenticada).

⚙️ Stack Técnica
Função	Tecnologia
Frontend (Mobile + Web)	React Native (Expo) + React Native Web
Backend / API	Node.js + Express
Banco de Dados (metadados)	MongoDB Atlas
Armazenamento de Arquivos	Cloudflare R2
Autenticação	Firebase Authentication (email/senha)
Agendamento de Limpeza	Node Cron (tarefas automáticas diárias)
Hospedagem Backend	Render ou Railway (Free Tier)
Controle de Versão	GitHub
CI/CD (posterior)	GitHub Actions
🗂 Estrutura de Dados (MongoDB)

Cada upload gera um documento no banco de dados com os metadados da mídia.

{
  "_id": "abcd1234",
  "client": "Cliente XPTO",
  "date": "2025-10-04",
  "type": "reels",
  "uploaded_by": "joao@empresa.com",
  "storage_url": "https://empresa-storage.r2.dev/uploads/cliente-xpto/reels_2025-10-04.mp4",
  "size": "35MB",
  "created_at": "2025-10-04T15:22:00Z",
  "expires_at": "2025-11-04T15:22:00Z"
}

🔐 Autenticação

Login e cadastro realizados via Firebase Authentication (email/senha).

Cada membro da equipe possui credenciais próprias.

Apenas usuários autenticados podem enviar ou baixar arquivos.

Todas as requisições ao backend exigem token válido.

🔄 Fluxo do Sistema
1. Login e Acesso

O usuário realiza login via Firebase Auth.
Após autenticação, é direcionado para a tela principal, onde vê os clientes existentes ou adiciona um novo.

2. Seleção de Cliente e Data

O usuário seleciona o cliente → data da sessão (ex: 04/10/2025) → tipo de post (Reels, Story, Feed, etc).

3. Upload de Arquivos

O app envia o arquivo para o Cloudflare R2.
Em seguida, o backend cria um registro no MongoDB Atlas com os metadados do upload.

4. Visualização e Download

Todos os membros autenticados podem visualizar os arquivos organizados hierarquicamente.
Ao clicar, o app utiliza a URL pública do R2 para download direto.

5. Exclusão Automática

Um cron job diário no backend verifica arquivos com expires_at vencido:

Remove o arquivo do Cloudflare R2;

Exclui o registro correspondente no MongoDB.

6. Segurança e Permissões

Tokens Firebase são validados a cada requisição;

Apenas uploads e downloads autenticados são permitidos;

Chaves de acesso R2 ficam protegidas no backend.

🧩 Fluxo Visual (Simplificado)
Usuário → Login (Firebase)
          ↓
Seleciona Cliente → Data → Tipo
          ↓
Faz Upload → (Cloudflare R2) + Salva registro → (MongoDB Atlas)
          ↓
Equipe Visualiza / Download
          ↓
(Agendador) → Remove arquivos antigos após 30 dias

🗺 Estrutura do Projeto (Simplificada)
mediaflow/
│
├── backend/
│   ├── src/
│   │   ├── models/        # Modelos MongoDB
│   │   ├── routes/        # Rotas Express
│   │   ├── controllers/   # Lógica de negócio
│   │   ├── services/      # Upload, autenticação, agendador
│   │   └── index.js       # Inicialização do servidor
│   └── package.json
│
├── frontend/
│   ├── src/
│   │   ├── screens/       # Telas React Native
│   │   ├── components/    # Componentes reutilizáveis
│   │   ├── services/      # Conexão API
│   │   └── App.js
│   └── package.json
│
├── .env.example           # Variáveis de ambiente
├── README.md
└── LICENSE


2. Estrutura geral do projeto
MediaFlowApp/
│
├── src/
│   ├── screens/
│   │   ├── LoginScreen.js
│   │   ├── Dashboard.js
│   │   ├── ClientMediaScreen.js
│   │   ├── UploadScreen.js
│   │   └── SettingsScreen.js
│   │
│   ├── components/
│   │   ├── MediaCard.js
│   │   ├── FolderView.js
│   │   ├── FileUploader.js
│   │   └── FileDownloader.js
│   │
│   ├── services/
│   │   ├── firebase.js  ← configurações do Firebase
│   │   ├── storage.js   ← upload/download
│   │   └── firestore.js ← clientes, datas e tipos
│   │
│   ├── utils/
│   │   └── dateUtils.js
│   │
│   └── App.js
│
├── README.md
└── package.json


3. Estrutura de dados (Firestore)
clientes (collection)
│
├── clienteId
│   ├── nome: string
│   ├── criadoEm: timestamp
│   ├── pastas (subcollection)
│   │   ├── dataId
│   │   │   ├── data: string (ex: 2025-10-04)
│   │   │   ├── midias (subcollection)
│   │   │   │   ├── tipo: 'reels' | 'story' | 'feed' | 'capa'
│   │   │   │   ├── url: string
│   │   │   │   ├── autor: string
│   │   │   │   ├── criadoEm: timestamp
│   │   │   │   └── expiraEm: timestamp (para exclusão automática)



💡 Futuras Funcionalidades

📊 Dashboard de uso (quantidade de uploads/downloads);

🔔 Notificações push de novos uploads;

📎 Compartilhamento via link temporário;

🧱 Filtros e busca por tipo, cliente e data.

🔧 Requisitos de Configuração

Antes de iniciar o projeto, é necessário criar contas e coletar credenciais nas seguintes plataformas:

Serviço	Função	Ação necessária
Firebase	Autenticação	Criar projeto → ativar Email/Password
MongoDB Atlas	Banco de dados	Criar cluster gratuito e obter URI
Cloudflare R2	Armazenamento	Criar bucket → gerar Access/Secret Key
Render ou Railway	Hospedagem backend	(opcional neste início) criar conta
👤 Autores

Desenvolvido por [1000ton]
Projeto interno da empresa, também utilizado como estudo e portfólio pessoal.

🧱 Status: Fase Inicial (Infraestrutura + Backend)