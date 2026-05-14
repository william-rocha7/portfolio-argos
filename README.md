graph TD
    %% Estilos para Diferenciar Camadas
    classDef frontend fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef logic fill:#fff3e0,stroke:#e65100,stroke-width:2px;
    classDef server fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px;
    classDef database fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;

    subgraph "Camada de Interface (Front-end)"
        IDX[Index.html<br/>Carrega dependências, Bootstrap,<br/>FontAwesome e estrutura o SPA]:::frontend
        DS[DesignSystem.html<br/>Define a identidade visual,<br/>CSS customizado e animações]:::frontend
        CORE[Core.html<br/>Lógica do Front: showPage(),<br/>gestão de menus e loading overlays]:::frontend
        
        IDX --- DS
        IDX --- CORE
    end

    subgraph "Módulos Funcionais (Operação de Campo)"
        VST[VistoriaMG.html<br/>Coleta de dados técnicos,<br/>coordenadas e fotos de campo]:::logic
        MAP[MapaMG.html<br/>Motor Geoespacial: Renderiza a<br/>rede externa e pontos de auditoria]:::logic
        AUD[AuditoriaMG.html<br/>Painel de Conformidade: Dashboards<br/>de Alarmes Técnicos - TAs]:::logic
        CLN[CleanupMG.html<br/>Motor de Verificação: Algoritmo de<br/>saneamento e validação rigorosa]:::logic
    end

    %% Roteamento do Core para os Módulos
    CORE -->|Instancia| VST
    CORE -->|Instancia| MAP
    CORE -->|Instancia| AUD
    CORE -->|Instancia| CLN

    subgraph "Servidor (Google Apps Script API)"
        COD[Code.js<br/>Controlador: doGet(),<br/>processa pedidos e gere sessões]:::server
        STP[Setup.js<br/>Configurações Globais: IDs de pastas,<br/>chaves de API e variáveis de ambiente]:::server
        
        STP --> COD
    end

    %% Comunicação RPC (Remote Procedure Call)
    VST & MAP & AUD & CLN -.->|google.script.run| COD

    subgraph "Camada de Dados (Persistência)"
        DBJ[Database.js<br/>Camada de Abstração: readData(),<br/>writeData() e deleteData()]:::database
        ST[(Base de Dados / Sheets<br/>Repositório de Infraestrutura)]:::database
        
        COD <--> DBJ
        DBJ <--> ST
    end

    %% Fluxo de Saída (Resultados)
    ST --> OUT[Redução de 60% em TAs e Eficiência em OPEX]
