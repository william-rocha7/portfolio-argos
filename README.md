graph TD
    %% Estilos de Identificação
    classDef ui fill:#f0f4c3,stroke:#827717,stroke-width:2px;
    classDef logic fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef server fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px;
    classDef data fill:#fff3e0,stroke:#e65100,stroke-width:2px;

    subgraph "Camada de Interface & Estilo"
        IDX[Index.html: Estrutura Base e Scripts Externos]:::ui
        DS[DesignSystem.html: Definições de CSS e Componentes Visuais]:::ui
        IDX --- DS
    end

    subgraph "Motor de Navegação (Front-end)"
        CORE[Core.html: Gestor de Roteamento e Troca de Ecrãs SPA]:::logic
        IDX --> CORE
    end

    subgraph "Módulos de Operação (Lógica de Negócio)"
        VIST[VistoriaMG.html: Formulários de Campo e Recolha de Evidências]:::logic
        MAPA[MapaMG.html: Visualização Geoespacial de Infraestrutura]:::logic
        AUDT[AuditoriaMG.html: Painel de Conformidade e Alertas Técnicos]:::logic
        CLEAN[CleanupMG.html: Algoritmo de Verificação e Saneamento de Dados]:::logic
    end

    CORE --> VIST
    CORE --> MAPA
    CORE --> AUDT
    CORE --> CLEAN

    subgraph "Orquestração de Servidor (Apps Script)"
        CODE[Code.js: Controlador Central e Endpoints da API]:::server
        SETUP[Setup.js: Configuração de Variáveis de Ambiente e Permissões]:::server
        SETUP --> CODE
    end

    %% Chamadas de Funções Remotas
    VIST -.->|google.script.run| CODE
    MAPA -.->|google.script.run| CODE
    AUDT -.->|google.script.run| CODE
    CLEAN -.->|google.script.run| CODE

    subgraph "Camada de Persistência"
        DB[Database.js: Interface de Acesso ao Banco de Dados - CRUD]:::data
        STOR[(Base de Dados: Google Sheets / Cloud Storage)]:::data
        CODE <--> DB
        DB <--> STOR
    end
