<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=1e1e2e&height=120&section=header&text=Tiago%20Silva&fontSize=38&fontColor=cdd6f4&animation=fadeIn" height="auto" />
</div>

<div align="center">
  <br/>
  <a href="https://git.io/typing-svg">
    <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=18&duration=2000&pause=1000&color=a6e3a1&center=true&vCenter=true&width=500&lines=Desenvolvedor+Full-Stack;Estudante+de+Engenharia+de+Software;Focado+em+Solu%C3%A7%C3%B5es+Pr%C3%A1ticas+%26+IoT;Construindo+o+Ecossistema+Aura+ERP" alt="Typing SVG" />
  </a>
</div>

---

### 👋 Sobre Mim

Olá! Sou estudante de **Engenharia de Software** e atuo como **Desenvolvedor Full-Stack**. Meu foco está em aprender e aplicar conceitos sólidos de arquitetura, desenvolvimento web moderno e integrações práticas de hardware/IoT. 

Minha filosofia de trabalho é baseada em ser transparente, **prometer menos e entregar mais**. Gosto de entender como as coisas funcionam por baixo do capô — seja lidando com segurança no banco de dados, criando pontes locais de comunicação com periféricos ou otimizando a sincronização offline de sistemas comerciais.

- 💻 **Meu Portfólio**: [portfolio-tiago-silva.vercel.app](https://portfolio-tiago-silva.vercel.app)
- 💼 **Foco Atual**: Estudos práticos aplicados em microsserviços locais e web, buscando constante melhoria de código limpo e padrões de mercado.
- 🌱 **Aprendendo Sempre**: Arquitetura de software estruturada, boas práticas de testes, desenvolvimento nativo com Rust e segurança da informação básica.

---

### 🛡️ Nota de Privacidade & Engenharia (Recrutadores & Tech Leads)

> [!NOTE]
> **Sobre os projetos do Ecossistema Aura ERP:**
> Os projetos destacados abaixo são sistemas comerciais sob desenvolvimento privado. Por questões de confidencialidade e ética com os clientes, o código-fonte original não é público. Contudo, para demonstrar minhas decisões de fluxo de dados, lógica de negócio e habilidades reais de resolução de problemas, descrevi de forma aberta a arquitetura geral e os desafios técnicos solucionados.

---

### 🌐 O Ecossistema Aura ERP

O **Aura ERP** é uma iniciativa prática focada em resolver dores reais do comércio. Trata-se de uma suíte de vendas que une um **Core Cloud** com **Bridges Locais (Edge)** que rodam na máquina do cliente. Isso permite falar direto com impressoras locais e gateways de mensageria com latência reduzida e alta resiliência.

#### Diagrama de Arquitetura de Integração (Estável)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                           AURA ERP ECOSYSTEM                            │
│                       Arquitetura de Integração                         │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                       [ 🛒 AURA DASH (Core Cloud) ]
                         React / Vite / Supabase DB
                                     │
                ┌────────────────────┼────────────────────┐
                │ (TanStack Query)   │ (HTTP HMAC-SHA256) │ (HTTP HMAC-SHA256)
                ▼                    ▼                    ▼
        [ ⚡ SUPABASE DB ]    [ 🌬️ AURA PRINT ]    [ 💬 AURA WEB ]
         Multi-tenant RLS     Rust Axum local      Node Express local
                                     │                    │
                                     ▼                    ▼
                             [ 🖨️ IMPRESSORA ]    [ 💬 WHATSAPP ]
                              Térmica (ESC/POS)     Baileys Gateway
```

---

### 📁 Estudos de Caso Práticos (Projetos Privados)

#### 1. 🛍️ Aura Dash — Core ERP & PDV
* **Stack**: `TypeScript 5.8`, `React 18.3`, `Vite`, `Tailwind CSS 3.4`, `Supabase (PostgreSQL)`, `shadcn/ui`, `TanStack Query`, `Dexie (IndexedDB)`.
* **O Desafio**: Um Ponto de Venda (PDV) físico não pode parar a operação de vendas se a internet oscilar ou cair. Além disso, o sistema SaaS precisa garantir isolamento estrito de dados entre diferentes empresas inquilinas (*multi-tenancy*).
* **Minha Abordagem Técnica**:
  * **Módulos Auto-Contidos (DDD)**: Estruturei o código em domínios auto-contidos (`pdv`, `estoque`, `vendas`, `financial`, `crm`, `delivery`). No módulo de `pdv`, por exemplo, isolei a lógica e regras inegociáveis (*invariantes*) do Caixa, como impedir sessões simultâneas de caixa aberto para o mesmo operador e exigir fundo de abertura positivo.
  * **Banco Local com IndexedDB**: Implementação do **Dexie (IndexedDB)** para salvar vendas localmente no navegador offline, realizando sincronização em segundo plano assim que a conexão retorna.
  * **Isolamento via RLS**: Configuração de **Row-Level Security (RLS)** a nível de tabelas no Supabase via `company_id`. O próprio banco rejeita requisições ilegítimas, blindando os dados dos inquilinos mesmo contra eventuais falhas de frontend.
  * **Consumo de Server State**: Uso sistemático do **TanStack Query** para gerenciar dados do servidor, tirando proveito de cache inteligente e evitando requisições manuais repetidas via hooks genéricos.

#### 2. 🌬️ Aura Print Bridge — Impressão Silenciosa
* **Stack**: `Rust 🦀`, `Axum` (Tokio/Hyper), `WinSpool API` (Windows), `lp` (CUPS no Linux), `HMAC-SHA256`.
* **O Desafio**: Navegadores rodam em ambientes seguros e isolados (*sandboxes*) e não possuem acesso direto a periféricos locais. Abrir a caixa de diálogo de impressão do navegador para cada cupom gerado no PDV deixa a operação do caixa lenta.
* **Minha Abordagem Técnica**:
  * **Binário Otimizado**: Desenvolvi um serviço local super leve em **Rust** (consumindo menos de 10MB de RAM) que recebe os dados do ERP e envia diretamente para o spooler nativo do sistema operacional (`WinSpool` / `lp`), possibilitando a impressão rápida e silenciosa.
  * **Segurança Baseada em HMAC**: Proteção das APIs locais por meio de handshake automático de pareamento (`/pair`) e geração de assinaturas **HMAC-SHA256 (`x-signature`)** no cabeçalho das requisições, validando a autenticidade de quem envia a impressão.
  * **Aplicação em Tray e Auto-Start**: Criação de menu de bandeja que verifica e corrige drifts de inicialização do serviço no logon usando o **Task Scheduler** nativo do Windows.

#### 3. 💬 Aura Web Bridge — Integração WhatsApp
* **Stack**: `Node.js 23.x`, `Express`, `Baileys Library`, `HMAC-SHA256`, `useMultiFileAuthState`.
* **O Desafio**: Enviar mensagens e comprovantes automáticos para clientes sem sobrecarregar a conexão local e respeitando taxas de envio razoáveis para evitar bloqueios de chip.
* **Minha Abordagem Técnica**:
  * **Fila Durável Local (Spool)**: Fila persistida no disco local com retentativa automática em falhas e controle de tempo randômico (*throttling* de 120ms-350ms) entre mensagens para simular o comportamento de envio humano.
  * **Desacoplamento Inbound via Pull**: O Core ERP busca novas interações locais realizando *polling* assíncrono (`GET /api/new-messages`) e remove-as da fila local chamando a rota de **ACK**, garantindo estabilidade e evitando a perda de dados de clientes.
  * **Proteção contra Replay**: Validação de tempo (*timestamp*) e controle de nonces persistidos localmente para impedir replay attacks nas APIs locais expostas.

---

### 🛠️ Matriz de Competências & Tecnologias

<table>
  <tr>
    <td valign="top" width="25%">
      <b>💻 Linguagens</b><br/><br/>
      • TypeScript<br/>
      • JavaScript<br/>
      • Rust 🦀 (Básico/Prático)<br/>
      • Java ☕<br/>
      • HTML5 & CSS3
    </td>
    <td valign="top" width="25%">
      <b>🎨 Frontend & UI</b><br/><br/>
      • React.js & Hooks<br/>
      • Next.js (Estudos)<br/>
      • Tailwind CSS<br/>
      • shadcn/ui & Radix UI<br/>
      • Styled Components<br/>
      • Layouts Responsivos
    </td>
    <td valign="top" width="25%">
      <b>⚡ Backend & Cloud</b><br/><br/>
      • Node.js & Express<br/>
      • Axum (Rust / Básico)<br/>
      • Spring Boot (Java)<br/>
      • Supabase (PostgreSQL)<br/>
      • MySQL & SQLite<br/>
      • TanStack Query (Server State)
    </td>
    <td valign="top" width="25%">
      <b>⚙️ Ferramentas & DevOps</b><br/><br/>
      • Docker (Containers)<br/>
      • Git & GitHub Flows<br/>
      • Linux (Ubuntu/Debian)<br/>
      • OS Spooler (WinSpool/CUPS)<br/>
      • Protocolo Baileys (WA)<br/>
      • Dexie (IndexedDB)
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

* 💼 **LinkedIn**: [tiagombts](https://www.linkedin.com/in/tiagombts/)
* 💻 **Meu Portfólio**: [Visite meu Portfólio](https://portfolio-tiago-silva.vercel.app)
* 📧 **E-mail**: [tiago.mbts@gmail.com](mailto:tiago.mbts@gmail.com)

Agradeço pela visita ao meu perfil! 😊 Estou sempre aberto a trocar ideias e em busca de oportunidades de crescimento e novos desafios técnicos.
