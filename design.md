```mermaid
graph TD
    %% 用户接入层
    User[商户用户] -->|Web访问| FE_Web[Vercel: Web管理端]
    User -->|侧边栏插件| FE_Ext[Chrome插件: 微信/WhatsApp助手]
    
    %% 外部数据源
    Customer[C端客户] -->|WhatsApp消息| WA_API[WhatsApp API]
    External[外部数据] -->|爬虫/API| Mkt_Data[Google Trends/节假日数据]

    %% 后端服务层 (Render/Railway)
    subgraph "Backend Services (Python/FastAPI)"
        Gateway[API Gateway]
        
        %% 核心业务模块
        Service_CRM[CRM服务: 档案/订单]
        Service_BI[BI预测服务: 排产/爆品]
        Service_Mkt[营销服务: 方案生成/激活]
        
        %% AI Agent层
        Agent_NLU[意图识别 Agent]
        Agent_Extract[信息抽取 Agent]
        Agent_Reason[推理预测 Agent]
        
        Gateway --> Service_CRM
        Gateway --> Service_BI
        Gateway --> Service_Mkt
        
        Service_CRM <--> Agent_NLU
        Service_CRM <--> Agent_Extract
        Service_BI <--> Agent_Reason
        Service_Mkt <--> Agent_Reason
    end

    %% 数据存储层 (Supabase)
    subgraph "Data Layer (Supabase)"
        DB_SQL[(PostgreSQL: 业务数据)]
        DB_Vec[(pgvector: 向量知识库)]
        Auth[Supabase Auth]
        Storage[Supabase Storage: PDF/图片]
    end

    %% 连接关系
    FE_Web --> Gateway
    FE_Ext --> Gateway
    WA_API --> Gateway
    Mkt_Data --> Gateway

    Service_CRM <--> DB_SQL
    Service_BI <--> DB_SQL
    Service_Mkt <--> DB_SQL
    
    Agent_Reason <--> DB_Vec
    Agent_Reason <--> LLM[大模型 API (OpenAI/DeepSeek)]