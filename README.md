<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0e75b6&height=180&section=header&text=Gestão%20de%20Devoluções&fontSize=40&fontColor=ffffff&animation=fadeIn&desc=Sistema%20completo%20de%20controle%20e%20rastreamento%20de%20devoluções&descAlignY=60&descSize=16" />

![Status](https://img.shields.io/badge/Status-Em%20Desenvolvimento-yellow?style=flat-square)
![Java](https://img.shields.io/badge/Java-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)

</div>

---

## 📋 Sobre o Projeto

O **Sistema de Gestão de Devoluções** é uma aplicação desenvolvida para otimizar e automatizar o processo de devolução de produtos, eliminando fluxos manuais e reduzindo o tempo de resolução de solicitações. O sistema permite rastrear cada etapa da devolução — da solicitação inicial até a resolução final — com visibilidade total para gestores e equipes operacionais.

---

## ✨ Funcionalidades

- 📥 **Registro de devoluções** — abertura e acompanhamento de solicitações de devolução
- 🔍 **Rastreamento em tempo real** — visibilidade do status de cada devolução
- 📊 **Dashboard gerencial** — relatórios e métricas de devoluções por período, produto e motivo
- 🔔 **Notificações automáticas** — alertas para equipes responsáveis
- 📁 **Histórico completo** — log de todas as ações realizadas por solicitação
- 👥 **Controle de acesso** — perfis de usuário com permissões diferentes (admin, operador, cliente)

---

## 🛠️ Tecnologias Utilizadas

| Camada | Tecnologia |
|--------|-----------|
| Backend | Java · Spring Boot |
| Banco de Dados | MySQL / PostgreSQL |
| Autenticação | Spring Security · JWT |
| Frontend | HTML · CSS · Thymeleaf |
| Testes | JUnit · Mockito |

---

## 🚀 Como Rodar o Projeto

### Pré-requisitos

- Java 17+
- Maven 3.8+
- MySQL 8+

### Instalação

```bash
# Clone o repositório
git clone https://github.com/ArthurCarneiro10/gestao-devolucao.git

# Entre na pasta
cd gestao-devolucao

# Configure o banco de dados em src/main/resources/application.properties
# spring.datasource.url=jdbc:mysql://localhost:3306/gestao_devolucao
# spring.datasource.username=seu_usuario
# spring.datasource.password=sua_senha

# Compile e rode
mvn spring-boot:run
```

Acesse em: `http://localhost:8080`

---

## 📁 Estrutura do Projeto

```
gestao-devolucao/
├── src/
│   ├── main/
│   │   ├── java/com/arthurdevolucao/
│   │   │   ├── controller/
│   │   │   ├── service/
│   │   │   ├── repository/
│   │   │   ├── model/
│   │   │   └── dto/
│   │   └── resources/
│   │       ├── application.properties
│   │       └── templates/
│   └── test/
├── pom.xml
└── README.md
```

---

## 📸 Screenshots

> Em breve — adicione capturas de tela do sistema aqui.

---

## 🤝 Contribuindo

Contribuições são bem-vindas! Sinta-se à vontade para abrir uma _issue_ ou enviar um _pull request_.

1. Fork o projeto
2. Crie sua branch (`git checkout -b feature/nova-funcionalidade`)
3. Commit suas mudanças (`git commit -m 'feat: adiciona nova funcionalidade'`)
4. Push para a branch (`git push origin feature/nova-funcionalidade`)
5. Abra um Pull Request

---

## 👤 Autor

**Arthur Carneiro**  
[![GitHub](https://img.shields.io/badge/GitHub-ArthurCarneiro10-000?style=flat&logo=github)](https://github.com/ArthurCarneiro10)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat&logo=linkedin)](https://linkedin.com/in/seu-linkedin)

---

<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0e75b6&height=80&section=footer" />
</div># gestao-devolucao
