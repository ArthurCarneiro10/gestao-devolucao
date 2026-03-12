<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=2563a8&height=180&section=header&text=GestãoRev&fontSize=48&fontColor=ffffff&animation=fadeIn&desc=Sistema%20de%20Devoluções%20%26%20Trocas&descAlignY=62&descSize=18" />

![Status](https://img.shields.io/badge/Status-Ativo-44ffaa?style=flat-square)
![HTML](https://img.shields.io/badge/HTML5-E34F26?style=flat-square&logo=html5&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-323330?style=flat-square&logo=javascript&logoColor=F7DF1E)
![Firebase](https://img.shields.io/badge/Firebase-FFCA28?style=flat-square&logo=firebase&logoColor=black)
![Chart.js](https://img.shields.io/badge/Chart.js-FF6384?style=flat-square&logo=chartdotjs&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)

</div>

---

## 📋 Sobre o Projeto

O **GestãoRev** é uma aplicação web completa para gerenciamento de devoluções, trocas e vendas erradas. Com autenticação de usuários, dashboard interativo com gráficos em tempo real e sistema de comentários por registro, o sistema oferece visibilidade total das operações para gestores e equipes de atendimento.

> Interface dark mode com design moderno usando as fontes **Syne** e **DM Mono**.

---

## ✨ Funcionalidades

- 🔐 **Autenticação** — login/logout com Firebase Auth, controle de acesso por perfil (admin / operador)
- 📊 **Dashboard interativo** — KPIs de devoluções, trocas, vendas erradas e pendências com tendência vs. período anterior
- 📈 **Gráficos em tempo real** — gráfico de linha por período, donut por tipo e donut por status (Chart.js)
- 🏆 **Top produtos** — ranking dos produtos com mais ocorrências no período
- 🔄 **Filtros por período** — semana, mês ou histórico completo
- 💬 **Sistema de comentários** — anotações por registro com avatar, autor e timestamp
- 🔔 **Status por registro** — pendente, em andamento, resolvido
- ⚡ **Atualizações em tempo real** — dados sincronizados via Firestore `onSnapshot`

---

## 🛠️ Tecnologias Utilizadas

| Categoria | Tecnologia |
|-----------|-----------|
| Frontend | HTML5 · CSS3 · JavaScript (Vanilla) |
| Banco de Dados | Firebase Firestore |
| Autenticação | Firebase Auth |
| Gráficos | Chart.js 4.4 |
| Tipografia | Syne · DM Mono (Google Fonts) |

---

## 🚀 Como Rodar o Projeto

### Pré-requisitos

- Navegador moderno (Chrome, Firefox, Edge)
- Conta no [Firebase](https://firebase.google.com)

### Configuração do Firebase

1. Crie um projeto no [Firebase Console](https://console.firebase.google.com)
2. Ative o **Firestore Database** e o **Authentication** (Email/Senha)
3. Copie suas credenciais do projeto

### Instalação

```bash
# Clone o repositório
git clone https://github.com/ArthurCarneiro10/gestao-devolucao.git

# Entre na pasta
cd gestao-devolucao
```

4. Abra o arquivo `index.html` e substitua o bloco `firebaseConfig` com suas credenciais:

```javascript
const firebaseConfig = {
  apiKey:            "SUA_API_KEY",
  authDomain:        "SEU_PROJETO.firebaseapp.com",
  projectId:         "SEU_PROJETO",
  storageBucket:     "SEU_PROJETO.firebasestorage.app",
  messagingSenderId: "SEU_SENDER_ID",
  appId:             "SEU_APP_ID"
};
```

5. Abra o `index.html` no navegador — ou use o [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) no VS Code.

---

## 📁 Estrutura do Projeto

```
gestao-devolucao/
├── index.html        # Aplicação completa (HTML + CSS + JS)
└── README.md
```

---

## 🗃️ Estrutura do Firestore

```
registros/
  {registroId}/
    - tipo: "devolucao" | "troca" | "venda"
    - produto: string
    - status: "pendente" | "andamento" | "resolvido"
    - timestamp: number
    - data: string
    comentarios/
      {comentarioId}/
        - texto: string
        - autorUid: string
        - autorNome: string
        - autorEmail: string
        - timestamp: number

usuarios/
  {uid}/
    - nome: string
    - email: string
    - perfil: "admin" | "operador"
```

---

## 📸 Screenshots

> <img width="1648" height="891" alt="Captura de Tela 2026-03-12 às 14 13 20" src="https://github.com/user-attachments/assets/950791ce-0368-4c46-8243-6bce51532b1d" />

---

## 🗺️ Roadmap

- [ ] Exportar relatórios em PDF/Excel
- [ ] Notificações por e-mail para registros pendentes
- [ ] Filtro por responsável / operador
- [ ] App mobile (PWA)
- [ ] Integração com sistemas ERP

---

## 👤 Autor

**Arthur Carneiro**  
[![GitHub](https://img.shields.io/badge/GitHub-ArthurCarneiro10-000?style=flat&logo=github)](https://github.com/ArthurCarneiro10)
[![LinkedIn]([https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat&logo=linkedin)](https://linkedin.com/in/seu-linkedin](https://www.linkedin.com/in/arthur-brassarote-550187270/))

---

<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=2563a8&height=80&section=footer" />
</div>
