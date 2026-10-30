<div align="center">

# 🐾 GuardianPet API
**Challenge Clyvo Vet — Sprint 4: DevOps & Cloud Computing**

![.NET 8.0](https://img.shields.io/badge/.NET-8.0-512BD4?style=for-the-badge&logo=dotnet&logoColor=white) ![Microsoft Azure](https://img.shields.io/badge/Microsoft_Azure-Web_App_Linux-0078D4?style=for-the-badge&logo=microsoft-azure&logoColor=white) ![Azure DevOps](https://img.shields.io/badge/Azure_DevOps-CI%2FCD_Pipelines-0078D7?style=for-the-badge&logo=azure-devops&logoColor=white) ![Oracle Database](https://img.shields.io/badge/Oracle_Database-19c_Cloud-F80000?style=for-the-badge&logo=oracle&logoColor=white) ![xUnit](https://img.shields.io/badge/Tests-xUnit_100%25_Passing-28A745?style=for-the-badge)

</div>

---

## 📖 Sobre o Projeto

Repositório oficial da entrega da **Sprint 4** da disciplina de **DevOps Tools & Cloud Computing** (Prof. João Menk — FIAP). A solução integra um backend robusto em **ASP.NET Core 8.0** com banco de dados **Oracle Cloud**, utilizando esteiras automatizadas de **CI/CD via Azure DevOps** para deploy contínuo em **Azure Web App (PaaS)**.

### 🎯 Objetivo e Valor de Negócio
O **GuardianPet** resolve o desafio da **Clyvo Vet**, centralizando a jornada de saúde dos pets (Tutores, Pets, Veterinários e Consultas) com foco em:

- 🚀 **Alta Disponibilidade:** Hospedagem PaaS no Azure App Service (Linux).
- ✅ **Qualidade Garantida:** Pipeline de CI com testes unitários xUnit (padrão AAA) em cada commit.
- ⚡ **Agilidade de Entrega:** CD automatizado reduzindo o *Lead Time* e eliminando erros manuais.
- 🔒 **Segurança:** Gestão de segredos via Azure DevOps e App Settings (JWT e Connection Strings).

---

## 👥 Equipe de Desenvolvimento

| RM | Nome Completo | Turma | Responsabilidade Principal |
| :--- | :--- | :---: | :--- |
| **RM563665** | João Victor Vendrameto | `2TDSPV` | DevOps Tools, Cloud Computing & Compliance/QA |
| **RM566552** | Gabriel Ambrósio Saraiva | `2TDSPV` | Relational/Non-Relational DB & IoT/IA |
| **RM561450** | Yuri Fuzinatto Garzoli Barreto | `2TDSPX` | Advanced Business Development with .NET |
| **RM562308** | Vinicius Romaguera Cardozo | `2TDSPX` | Java Advanced |
| **RM564205** | Nicolas de Oliveira Jacob | `2TDSPX` | Mobile Application Development |

---

## 🛠️ Stack Tecnológica

### 💻 Backend & Persistência
- **Framework:** C# / ASP.NET Core 8.0 Web API
- **ORM:** Entity Framework Core 8 (Migrações Automáticas)
- **Banco de Dados:** Oracle Database 19c Cloud
- **Segurança:** JWT Bearer (HMACSHA256) + PasswordHasher

### ⚙️ DevOps & Cloud (Azure)
- **CI/CD:** Azure Pipelines $\rightarrow$ Azure Releases
- **Hospedagem:** Azure Web App for Linux (Plan F1)
- **Infraestrutura:** Provisionada via Azure CLI
- **Observabilidade:** Swagger/OpenAPI, Health Checks, Serilog & OpenTelemetry

---

## 🏗️ Arquitetura e Fluxo CI/CD

![Diagrama da Arquitetura e Fluxo CI/CD](docs/diagrama-arquitetura-cicd.png)

```mermaid
flowchart LR
DEV["👨‍💻 Desenvolvedor\n(VS Code / Git)"] ==>|"1. git push\n(branch master)"| GH["🐙 GitHub\njoaovendrameto05/GuardianPetV2"]
GH ==>|"2. Trigger CI\n(Automático)"| CI["⚙️ Azure Pipelines (CI)\n1. Restore & Build (.NET 8)\n2. Testes xUnit (Variáveis Secretas)\n3. Publish Artefato (drop.zip)"]
CI ==>|"3. Novo Artefato\nPublicado"| CD["🚀 Azure Releases (CD)\nContinuous Deployment Trigger (⚡)\nDeploy Azure App Service"]
CD ==>|"4. Deploy ZIP\n(conexao-azure-sprint4)"| WEBAPP["☁️ Azure Web App (Linux)\nguardianpet-api-rm563665\n(rg-guardianpet-sprint4)"]
CLI["💻 Azure Cloud Shell\n(Scripts Azure CLI)"] -.->|"Provisiona Infra"| WEBAPP
WEBAPP <==>|"5. Persistência CRUD\n(EF Core / TCP 1521)"| ORACLE[("🗄️ Oracle Database Cloud\noracle.fiap.com.br:1521/ORCL\n(tb_users, tb_pets, tb_veterinarians)")]
USER["👩‍⚕️ Usuário Final / Clínica\n(Swagger UI / REST)"] <==>|"6. Requisições HTTPS\n(JWT Bearer)"| WEBAPP

🔄 Detalhamento do Pipeline

Push: Código enviado para a branch master no GitHub.
CI (Integração Contínua): Azure Pipelines executa dotnet restore $\rightarrow$ build $\rightarrow$ test (xUnit) $\rightarrow$ publish.
CD (Entrega Contínua): O artefato drop dispara a Release que implanta o pacote no Azure Web App via Service Connection.
Runtime: A API aplica migrações automaticamente e conecta-se ao Oracle Cloud via TCP 1521.

☁️ Provisionamento (Azure CLI)

Os recursos foram criados via Cloud Shell. Abaixo, o resumo dos comandos principais:

# Criação do Grupo de Recursos e Plano de Serviço

az group create --name rg-guardianpet-sprint4 --location brazilsouth
az appservice plan create --name plan-guardianpet --resource-group rg-guardianpet-sprint4 --sku F1 --is-linux

# Criação da Web App.NET 8

az webapp create --name guardianpet-api-rm563665 --resource-group rg-guardianpet-sprint4 --plan plan-guardianpet --runtime "DOTNETCORE:8.0"

# Configuração de App Settings (Segredos e Conexões)

az webapp config appsettings set --name guardianpet-api-rm563665 --resource-group rg-guardianpet-sprint4 --settings \
ASPNETCORE_ENVIRONMENT='Production' \
Jwt__Key='<CHAVE_SECRETA_PROTEGIDA>' \
ConnectionStrings__OracleConnection='Data Source=oracle.fiap.com.br:1521/ORCL;User Id=RM563665;Password=&lt;SENHA_PROTEGIDA&gt;;'

🧪 Validação e Testes

Swagger UI: Clique aqui para acessar
Health Check: Verificar Status do Oracle

🛠️ Guia de Teste CRUD

Autenticação: POST /api/users (Criar) $\rightarrow$ POST /api/auth/login (Obter Token).
Uso: Insira o token no botão Authorize 🔒 do Swagger.
Operações: Teste os endpoints de /api/pets e /api/users (GET, POST, PUT, DELETE).
Evidência: Valide a persistência via Oracle SQL Developer:

Dentro do Oracle SQL Developer

> SELECT * FROM "tb_users"; SELECT * FROM "tb_pets";


---

## 🔗 Links Oficiais

| Recurso | Link |
| **GitHub** | [joaovendrameto05/GuardianPetV2](https://github.com/joaovendrameto05/GuardianPetV2) |
| **Azure DevOps** | [Projeto Sprint 4](https://dev.azure.com/rm563365/Sprint%204%20-%20Azure%20DevOps) |
| **Vídeo Demo** | [Assistir no YouTube](INSERIR_LINK_DO_YOUTUBE_AQUI) |

