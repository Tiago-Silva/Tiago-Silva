<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=1b1b3a&height=130&section=header&text=Tiago%20Silva&fontSize=42&fontColor=ffffff&animation=fadeIn" height="auto" />
</div>

<div align="center">
  <br/>
  <a href="https://git.io/typing-svg">
    <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=20&duration=2000&pause=1000&color=3ECF8E&center=true&vCenter=true&width=550&lines=Software+Engineer+Full-Stack;Criador+do+Ecossistema+Aura+ERP;Especialista+em+Integra%C3%A7%C3%B5es+%26+IoT;Focado+em+Sistemas+de+Alta+Performance" alt="Typing SVG" />
  </a>
</div>

---

### 👋 Sobre Mim

Sou estudante de **Engenharia de Software** e desenvolvedor **Full-Stack** focado no desenvolvimento de sistemas web altamente robustos, aplicativos móveis e integrações seguras de baixo nível com hardware e IoT. 

Tenho paixão por desenhar arquiteturas limpas, performáticas e resilientes — preferindo sempre a manutenibilidade e a solidez técnica a atalhos rápidos de entrega.

- 💻 **Portfólio Pessoal**: [portfolio-tiago-silva.vercel.app](https://portfolio-tiago-silva.vercel.app)
- 💼 **Trabalho Atual**: Construindo e evoluindo sistemas comerciais modulares e bridges de alta performance.
- 🌱 **Foco de Estudos**: Arquitetura de Software Avançada, Engenharia de Sistemas com Rust e Segurança da Informação.

---

### 🛡️ Nota de Privacidade & Engenharia (Recrutadores & Tech Leads)

> [!IMPORTANT]
> Os principais projetos que tenho desenvolvido recentemente fazem parte de um **ecossistema comercial privado (Aura ERP)**. Para respeitar a privacidade comercial do código-fonte e acordos de confidencialidade, mas ainda sim demonstrar minha capacidade técnica a **recrutadores e líderes de engenharia**, consolidei abaixo os estudos de caso, decisões de design de software e fluxos de arquitetura deste ecossistema.

---

### 🌐 O Ecossistema Aura ERP

O **Aura ERP** é uma suíte modular *SaaS multi-tenant* de alta escalabilidade. Seu principal diferencial de engenharia é a união de um **Core Cloud** resiliente com **Bridges Locais (Edge)** de alta velocidade que conversam diretamente com hardware e redes locais do cliente sem passar por intermediários centralizados na nuvem, garantindo latência próxima a zero e operação contínua.

#### Diagrama de Arquitetura do Ecossistema

```mermaid
graph TD
    classDef cloud fill:#1e1b4b,stroke:#818cf8,stroke-width:2px,color:#fff;
    classDef local fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff;
    classDef device fill:#111827,stroke:#10b981,stroke-width:2px,color:#fff;

    %% Nodes
    AuraDash["🛒 Aura Dash (Core SaaS - Cloud/Vite)"]:::cloud
    SupabaseDB[("⚡ Supabase DB (RLS Multi-tenant)")]:::cloud
    
    AuraPrint["🌬️ Aura Print Bridge (Local Rust Service)"]:::local
    AuraWeb["💬 Aura Web Bridge (Local Node.js WA Bridge)"]:::local
    
    ThermalPrinter["🖨️ Impressora Térmica (ESC/POS / A4)"]:::device
    WhatsAppAPI["💬 WhatsApp Web Gateway (Baileys)"]:::device

    %% Connections
    AuraDash -->|TanStack Query| SupabaseDB
    
    AuraDash -->|Silent Print Call (HMAC-SHA256)| AuraPrint
    AuraPrint -->|Nativo OS Spooler (WinSpool/CUPS)| ThermalPrinter
    
    AuraDash -->|Event Pull & Outbound (HMAC-SHA256)| AuraWeb
    AuraWeb -->|Persistent Connection Spool| WhatsAppAPI
```

---

### 📁 Estudos de Caso de Engenharia (Projetos Privados)

Abaixo estão os detalhes arquiteturais dos três componentes principais do ecossistema:

#### 1. 🛍️ Aura Dash — Core ERP & PDV SaaS
* **Stack**: `TypeScript 5.8`, `React 18.3`, `Vite`, `Tailwind CSS 3.4`, `Supabase (PostgreSQL)`, `shadcn/ui`, `TanStack Query`, `Dexie (IndexedDB)`.
* **O Desafio**: Operações de Ponto de Venda (PDVs) em comércios físicos não podem parar de funcionar caso a internet caia. Adicionalmente, por se tratar de um modelo SaaS, o isolamento absoluto dos dados de cada empresa (*multi-tenancy*) na base de dados é um requisito crítico de compliance e segurança.
* **A Solução de Engenharia**:
  * **Operação Offline-First**: Implementação do **Dexie (IndexedDB)** como um banco de dados relacional local integrado no navegador, permitindo vendas completas offline com sincronização em background quando a rede é restabelecida.
  * **Isolamento via RLS**: Configuração profunda de **Row-Level Security (RLS)** a nível de PostgreSQL no Supabase por meio de `company_id`. Mesmo que ocorram brechas ou bugs no frontend, o banco de dados rejeita qualquer requisição que não pertença ao tenant autenticado.
  * **Arquitetura Domain-Driven (DDD)**: O frontend segue uma separação estrita em camadas: `domain/` (lógica pura e tipos de negócio), `modules/` (features auto-contidas como `pdv`, `estoque`, `financeiro`), `core/` (serviços transversais como auth e API) e `shared/` (UI reaproveitável).

#### 2. 🌬️ Aura Print Bridge — Impressão Silenciosa de Ultra Velocidade
* **Stack**: `Rust 🦀`, `Axum` (Tokio/Hyper), `WinSpool API` (Windows), `lp` (CUPS no Linux), `HMAC-SHA256`.
* **O Desafio**: Navegadores rodam em sandboxes de segurança restritas e não possuem acesso de baixo nível ao hardware do computador. Abrir caixas de diálogo de impressão para o operador confirmar cada cupom térmico no PDV atrasa o atendimento e degrada a experiência do usuário.
* **A Solução de Engenharia**:
  * **Nativo & Extremamente Leve**: Desenvolvimento de um serviço nativo em **Rust** que roda em segundo plano consumindo menos de 10MB de RAM. O serviço recebe dados brutos do ERP e os envia diretamente ao spooler nativo de impressão do sistema operacional (`WinSpool` no Windows e `CUPS` no Linux) sem popups.
  * **Handshake & Assinatura Criptográfica**: Proteção de endpoints sensíveis (como listar impressoras locais e enviar dados de impressão) usando chaves simétricas negociadas em handshake automatizado (`/pair`). Todas as requisições subsequentes exigem assinaturas criptográficas **HMAC-SHA256 (`x-signature`)** para impedir invasões locais.
  * **Tray Application Self-Healing**: Uma aplicação de bandeja do Windows que monitora de forma ativa e resolve desvios (*drift*) de configuração de inicialização (*auto-start*) usando o **Task Scheduler** nativo do sistema operacional.

#### 3. 💬 Aura Web Bridge — Integração WhatsApp Local & Fila Resiliente
* **Stack**: `Node.js 23.x`, `Express`, `Baileys Library`, `HMAC-SHA256`, `useMultiFileAuthState`.
* **O Desafio**: A comunicação do ERP com clientes via WhatsApp precisa ser resiliente a oscilações de rede. Além disso, disparos simultâneos não controlados podem levar ao banimento do número de telefone pela plataforma por spam.
* **A Solução de Engenharia**:
  * **Fila Durável Local (Spool)**: Criação de um mecanismo durável persistido no disco local com controle de vazão randômico (*randomized throttling* configurado entre 120ms e 350ms) para simular o comportamento humano e evitar banimentos do número de telefone.
  * **Desacoplamento Inbound via Pull Event**: Implementação de um fluxo assíncrono onde o ERP faz *polling* periódico no Bridge local (`GET /api/new-messages`) para obter novos contatos recebidos, confirmando a entrega via fluxo de **ACK** (`POST /api/new-messages/ack`). Isso evita perda de mensagens mesmo se o ERP estiver inoperante.
  * **Mitigação de Replay Attacks**: Validação de assinaturas HMAC em conjunto com cabeçalhos de timestamp (`x-bridge-timestamp`) e controle rígido contra *replay attacks* armazenando nonces descartáveis em um banco de TTL persistido localmente.

---

### 🛠️ Matriz de Competências & Tecnologias

<table>
  <tr>
    <td valign="top" width="25%">
      <b>💻 Linguagens</b><br/><br/>
      • Rust 🦀<br/>
      • Java ☕<br/>
      • TypeScript<br/>
      • JavaScript<br/>
      • HTML5 & CSS3
    </td>
    <td valign="top" width="25%">
      <b>🎨 Frontend & Mobile</b><br/><br/>
      • React.js<br/>
      • Next.js<br/>
      • React Native<br/>
      • Tailwind CSS<br/>
      • shadcn/ui & Radix<br/>
      • Styled Components
    </td>
    <td valign="top" width="25%">
      <b>⚡ Backend & Cloud</b><br/><br/>
      • Node.js & Express<br/>
      • Axum (Rust)<br/>
      • Spring Boot (Java)<br/>
      • Supabase (PostgreSQL)<br/>
      • MySQL & SQLite<br/>
      • TanStack Query
    </td>
    <td valign="top" width="25%">
      <b>⚙️ Integrações & DevOps</b><br/><br/>
      • Tauri (Desktop)<br/>
      • Docker<br/>
      • Git & GitHub CI<br/>
      • Linux OS<br/>
      • OS Spooler (WinSpool / CUPS)<br/>
      • Protocolo Baileys (WA)
    </td>
  </tr>
</table>

---

### 📊 GitHub Stats

<p align="center">
  <img src="https://github-profile-summary-cards.vercel.app/api/cards/profile-details?username=Tiago-Silva&theme=dracula&cache_seconds=1800" alt="Profile Details" />
  <img src="https://github-profile-summary-cards.vercel.app/api/cards/stats?username=Tiago-Silva&theme=dracula&cache_seconds=1800" alt="Stats" />
  <img src="https://github-profile-summary-cards.vercel.app/api/cards/productive-time?username=Tiago-Silva&theme=dracula&utcOffset=-3&cache_seconds=1800" alt="Productive Time" />
</p>

<p align="center">
  <img src="https://github-profile-summary-cards.vercel.app/api/cards/repos-per-language?username=Tiago-Silva&theme=dracula&cache_seconds=1800" alt="Repos per language" />
  <img src="https://github-profile-summary-cards.vercel.app/api/cards/most-commit-language?username=Tiago-Silva&theme=dracula&cache_seconds=1800" alt="Most Commit Language" />
</p>

---

### 🤝 Conecte-se Comigo

* 💼 **LinkedIn**: [Conecte-se no LinkedIn](https://www.linkedin.com/in/tiagombts/) (Insira seu link personalizado)
* 💻 **Meu Portfólio**: [Visite meu Portfólio](https://portfolio-tiago-silva.vercel.app)
* 📧 **E-mail**: [tiago.mbts@gmail.com](mailto:tiago.mbts@gmail.com) (Substitua se desejar)

Agradeço por visitar meu perfil! 😊 Fique à vontade para entrar em contato para oportunidades de engenharia de software complexa!
