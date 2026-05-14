```mermaid
graph TD
    %% Estilos de UX de Alta Fidelidade
    classDef init fill:#eceff1,stroke:#37474f,stroke-width:2px,color:#37474f
    classDef UI fill:#e3f2fd,stroke:#1565c0,stroke-width:2px,color:#0d47a1
    classDef router fill:#fff3e0,stroke:#e65100,stroke-width:2px,color:#e65100
    classDef coreLogic fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#4a148c
    classDef apiGateway fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px,color:#1b5e20
    classDef dataAccess fill:#e0f2f1,stroke:#004d40,stroke-width:2px,color:#004d40
    classDef storage fill:#ffffff,stroke:#263238,stroke-width:2px,color:#263238,stroke-dasharray: 4 4

    %% --- CAMADA 1: INICIALIZAÇÃO (CLIENT-SIDE) ---
    subgraph Inicializacao ["🌐 1. Inicialização do App"]
        A["Index.html<br/>Ponto de Entrada Global"]:::init
        B["DesignSystem.html<br/>Injeção Estática de CSS / UI"]:::init
    end

    %% --- CAMADA 2: ORQUESTRADOR SPA & ROTAS ---
    subgraph Orquestrador ["⚙️ 2. Motor de Navegação e Estado"]
        C["Core.html<br/>Script de Controle Global"]:::router
        C1["showPage - Função<br/>Alterna exibições no DOM"]:::router
        C2["Loading Overlay<br/>Bloqueio de UI Pós-Clique"]:::router
    end

    %% --- CAMADA 3: PARALELISMO DE MÓDULOS (RAMIFICAÇÕES DE NEGÓCIO) ---
    subgraph Modulos ["🛠️ 3. Módulos Funcionais e Sub-Rotinas"]
        %% Rota Vistoria
        subgraph SubVistoria ["Módulo Vistoria"]
            V1["VistoriaMG.html<br/>UI de Captura"]:::UI
            V2["Coleta de Imagens<br/>e Inputs de Campo"]:::coreLogic
            V3["validaFormVistoria<br/>Validação de Campos"]:::coreLogic
        end

        %% Rota Mapa
        subgraph SubMapa ["Módulo Mapa"]
            M1["MapaMG.html<br/>UI Espacial"]:::UI
            M2["Leaflet / GIS Engine<br/>Renderizador de Cluster"]:::coreLogic
            M3["Filtro de Raio<br/>Cálculo Geográfico"]:::coreLogic
        end

        %% Rota Auditoria
        subgraph SubAuditoria ["Módulo Auditoria"]
            U1["AuditoriaMG.html<br/>Dashboard UI"]:::UI
            U2["Processamento de Alarmes<br/>Controle de KPIs / TAs"]:::coreLogic
        end

        %% Rota Cleanup
        subgraph SubCleanup ["Módulo Saneamento"]
            L1["CleanupMG.html<br/>UI de Saneamento"]:::UI
            L2["Motor de Higienização<br/>Filtro de Inconsistências"]:::coreLogic
        end
    end

    %% --- CAMADA 4: CONTROLADOR E ENDPOINTS (SERVER-SIDE) ---
    subgraph Servidor ["☁️ 4. API Controller & Orquestração Serverless"]
        D1["Setup.js<br/>Environment & Variáveis Globais"]:::apiGateway
        D2["Code.js<br/>Gateway Central (doGet/doPost)"]:::apiGateway
        
        %% Endpoints Internos do Code.js
        E1["salvarDadosCampo<br/>Endpoint de Escrita"]:::apiGateway
        E2["buscarMalhaRede<br/>Endpoint de Leitura Geo"]:::apiGateway
        E3["executarSaneamento<br/>Endpoint de Regras"]:::apiGateway
    end

    %% --- CAMADA 5: PERSISTÊNCIA ---
    subgraph Dados ["💾 5. Camada de Abstração de Dados (DAL)"]
        F1["Database.js<br/>Funções CRUD Isoladas"]:::dataAccess
        G1[("Google Sheets / Cloud<br/>Repositório de Infraestrutura")]:::storage
    end

    %% ================= RAMIFICAÇÕES E FLUXOS CRÍTICOS =================

    %% Ligação de Inicialização
    A --- B
    A ==> C
    C --> C1
    
    %% Ramificações de Rotas (Escolha do Usuário na UI)
    C1 -->|Caminho A| V1
    C1 -->|Caminho B| M1
    C1 -->|Caminho C| U1
    C1 -->|Caminho D| L1

    %% Fluxo Interno Vistoria
    V1 --> V2 --> V3
    V3 -->|Dados Válidos| C2
    
    %% Fluxo Interno Mapa
    M1 --> M2 --> M3
    M3 --> C2

    %% Fluxo Interno Auditoria e Cleanup
    U1 --> U2 --> C2
    L1 --> L2 --> C2

    %% Chamadas Assíncronas Inter-Camadas (google.script.run)
    C2 -.->|Acesso RPC Assíncrono| D2
    D1 -.->|Injeta Variáveis de Escopo| D2
    
    %% Roteamento Interno no Servidor (Code.js direcionando para a função correta)
    D2 --> E1
    D2 --> E2
    D2 --> E3

    %% Conexão com a Data Access Layer (Database.js)
    E1 & E2 & E3 ==> F1
    F1 <-->|Queries / Appends / Updates| G1

    %% O Loop de Feedback (A resposta assíncrona voltando para a UI)
    G1 -.->|Retorna JSON / Status| F1
    F1 -.->|Callback de Sucesso| H1["withSuccessHandler()<br/>Desbloqueia UI e Renderiza Dados"]:::UI
    F1 -.->|Callback de Erro| H2["withFailureHandler()<br/>Dispara Alerta de Exceção"]:::UI

    %% Retorno Final ao Orquestrador
    H1 & H2 --> C1
