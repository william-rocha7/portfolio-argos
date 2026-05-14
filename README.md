```mermaid
graph TD
    %% Estilos de Camadas
    classDef server fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef client fill:#e3f2fd,stroke:#1565c0,stroke-width:2px;
    classDef modules fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    classDef storage fill:#ffffff,stroke:#37474f,stroke-width:2px,stroke-dasharray: 5 5;

    %% Camada do Servidor (Google Apps Script Backend)
    subgraph Servidor ["Backend - Google Apps Script Engine"]
        STP["Setup.js - Configuracao de Propriedades e IDs Globais"]:::server
        DBJ["Database.js - Matrizes Estaticas, Rotas e Malha Geo"]:::server
        COD["Code.js - Roteador de Templates e Endpoints RPC"]:::server
    end

    %% Camada do Cliente - Core e Estilo
    subgraph EstruturaBase ["Componentes de Inclusao e Estilo"]
        DS["DesignSystem.html - Variaveis CSS e Elementos UI"]:::client
        COR["Core.html - Modais Globais e Manipulacao de Imagem"]:::client
    end

    %% Camada de Modulos Operacionais (Front-End)
    subgraph Modulos ["Modulos e Telas do Sistema"]
        IDX["Index.html - Dashboard Hub e Links de Navegacao"]:::modules
        VST["VistoriaMG.html - Form de Campo e Captura de Fotos"]:::modules
        CLN["CleanupMG.html - Validacao e Saneamento de Registos"]:::modules
        AUD["AuditoriaMG.html - Checklist de Qualidade de Reparo"]:::modules
        MAP["MapaMG.html - Motor Leaflet e Clusters Geograficos"]:::modules
    end

    %% Persistencia
    GS[("Google Sheets - Base de Dados Operacional MG")]:::storage

    %% Fluxo de Ciclo de Vida e Dependencias
    STP --> COD
    DBJ -->|"Injeta Dados via JSON nas variaveis do Template"| COD
    
    %% Roteamento do Servidor para as Telas
    COD -->|"doGet renderiza a pagina solicitada"| IDX
    COD -->|"doGet renderiza a pagina solicitada"| VST
    COD -->|"doGet renderiza a pagina solicitada"| CLN
    COD -->|"doGet renderiza a pagina solicitada"| AUD
    COD -->|"doGet renderiza a pagina solicitada"| MAP

    %% Inclusao Estatica de CSS e Componentes Comuns
    IDX --- DS
    VST --- DS
    CLN --- DS
    AUD --- DS
    MAP --- DS

    IDX --- COR
    VST --- COR
    CLN --- COR
    AUD --- COR
    MAP --- COR

    %% Links de Redirecionamento de UI (Navegacao por Parametro)
    IDX -->|"Acesso via webAppUrl?page=VistoriaMG"| VST
    IDX -->|"Acesso via webAppUrl?page=CleanupMG"| CLN
    IDX -->|"Acesso via webAppUrl?page=AuditoriaMG"| AUD
    IDX -->|"Acesso via webAppUrl?page=MapaMG"| MAP

    %% Execucao Assincrona Back-to-Front
    VST -->|"google.script.run"| COD
    CLN -->|"google.script.run"| COD
    AUD -->|"google.script.run"| COD
    MAP -->|"google.script.run"| COD

    %% Persistencia de Dados
    COD <--> GS
