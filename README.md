graph TD
    %% Definições de Estilo
    classDef entry fill:#eceff1,stroke:#37474f,stroke-width:2px;
    classDef style fill:#f8bbd0,stroke:#880e4f,stroke-width:2px;
    classDef logic fill:#e1bee7,stroke:#4a148c,stroke-width:2px;
    classDef module fill:#fff9c4,stroke:#f57f17,stroke-width:2px;
    classDef backend fill:#c8e6c9,stroke:#1b5e20,stroke-width:2px;
    classDef database fill:#b3e5fc,stroke:#01579b,stroke-width:2px;

    subgraph "1. Camada Base (Single Page Application - SPA)"
        IDX["Index.html<br/>Ponto de entrada. Estrutura o esqueleto HTML da aplicação e importa dependências iniciais."]:::entry
        DS["DesignSystem.html<br/>Gerencia todo o CSS, variáveis de cor, responsividade e componentes visuais padronizados da interface."]:::style
        CR["Core.html<br/>Motor JavaScript do front-end. Controla o roteamento, estado da sessão e a troca dinâmica de telas."]:::logic
        
        IDX --> DS
        IDX --> CR
    end

    subgraph "2. Módulos Operacionais (Regras de Negócio e UI)"
        CR -->|Renderiza via DOM| VST["VistoriaMG.html<br/>Módulo de campo: Formulários técnicos, captura de coordenadas e evidências."]:::module
        CR -->|Renderiza via DOM| MAP["MapaMG.html<br/>Módulo Geoespacial: Renderiza a planta externa e clusters de infraestrutura."]:::module
        CR -->|Renderiza via DOM| AUD["AuditoriaMG.html<br/>Painel gerencial: Visualização de conformidades, falhas e indicadores (TAs)."]:::module
        CR -->|Renderiza via DOM| CLN["CleanupMG.html<br/>Motor de Validação: Processo de verificação rigorosa antes da consolidação dos dados."]:::module
    end

    subgraph "3. Controladores Back-end (Google Apps Script)"
        STP["Setup.js<br/>Inicializa configurações globais, variáveis de ambiente e parâmetros do sistema."]:::backend
        COD["Code.js<br/>API Controller: Recebe requisições do front-end (doGet/doPost) e orquestra a lógica no servidor."]:::backend
        
        STP --> COD
    end

    subgraph "4. Camada de Persistência e Dados"
        DBJ["Database.js<br/>Data Access Layer (DAL): Executa as operações de CRUD, filtragem e queries no banco."]:::database
        BD[("Base de Dados Operacional<br/>(Google Sheets / Fabric / GCP)")]:::database
        
        COD <--> DBJ
        DBJ <--> BD
    end

    %% Integração Front-End <-> Back-End via API do Google Apps Script
    VST -.->|google.script.run.salvarVistoria()| COD
    MAP -.->|google.script.run.buscarDadosGeograficos()| COD
    AUD -.->|google.script.run.gerarRelatorioAuditoria()| COD
    CLN -.->|google.script.run.processarCleanup()| COD
