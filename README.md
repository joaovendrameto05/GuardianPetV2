# 🐾 GuardianPet API — Challenge Clyvo Vet (Sprint 4: DevOps & Cloud Computing)

![.NET 8.0](https://img.shields.io/badge/.NET-8.0-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)
![Microsoft Azure](https://img.shields.io/badge/Microsoft_Azure-Web_App_Linux-0078D4?style=for-the-badge&logo=microsoft-azure&logoColor=white)
![Azure DevOps](https://img.shields.io/badge/Azure_DevOps-CI%2FCD_Pipelines-0078D7?style=for-the-badge&logo=azure-devops&logoColor=white)
![Oracle Database](https://img.shields.io/badge/Oracle_Database-19c_Cloud-F80000?style=for-the-badge&logo=oracle&logoColor=white)
![xUnit](https://img.shields.io/badge/Tests-xUnit_100%25_Passing-28A745?style=for-the-badge)

Repositório oficial da entrega da **Sprint 4** da disciplina de **DevOps Tools & Cloud Computing** (Prof. João Menk — FIAP), integrando a solução de backend em **ASP.NET Core 8.0 (`GuardianPetV2`)** com banco de dados em nuvem **Oracle Database** e esteiras completas e automatizadas de **CI/CD no Azure DevOps** realizando deploy em **Azure Web App (PaaS)**.

---

## 👥 1. Integrantes do Grupo

| RM | Nome Completo | Turma | Responsabilidade Principal |
| :--- | :--- | :---: | :--- |
| **RM563665** | João Victor Vendrameto | `2TDSPV` | DevOps Tools, Cloud Computing & Compliance/QA |
| **RM566552** | Gabriel Ambrósio Saraiva | `2TDSPV` | Mastering Relational/Non-Relational Database & IoT/IA |
| **RM561450** | Yuri Fuzinatto Garzoli Barreto | `2TDSPX` | Advanced Business Development with .NET |
| **RM562308** | Vinicius Romaguera Cardozo | `2TDSPX` | Java Advanced |
| **RM564205** | Nicolas de Oliveira Jacob | `2TDSPX` | Mobile Application Development |

---

## 🎯 2. Descrição da Solução e Benefícios para o Negócio

### Sobre o GuardianPet (Desafio Clyvo Vet)
O **GuardianPet** é uma API RESTful desenvolvida em **ASP.NET Core 8.0** projetada para solucionar o desafio proposto pela **Clyvo Vet**: o acompanhamento contínuo, preventivo e integrado da jornada de saúde dos pets. A solução centraliza o gerenciamento de **Tutores/Usuários (`tb_users`)**, **Pets (`tb_pets`)**, **Médicos Veterinários (`tb_veterinarians`)** e **Consultas Clínicas (`tb_consultations`)**.

### Benefícios para o Negócio
* **Disponibilidade e Escalabilidade em Nuvem:** Hospedagem gerenciada em PaaS no **Azure App Service (Linux)** integrada ao banco de dados **Oracle em Nuvem**, permitindo acesso imediato por clínicas veterinárias e aplicativos móveis via HTTPS.
* **Qualidade Assegurada por Automação (CI):** Cada alteração enviada à branch `master` aciona automaticamente a compilação e a bateria de **testes unitários automatizados com xUnit (padrão AAA)**, impedindo que códigos com falhas gerem artefatos de produção.
* **Entrega Contínua Sem Intervenção Manual (CD):** A publicação de um novo artefato aciona automaticamente o provisionamento e atualização da API no **Azure Web App**, reduzindo o tempo de entrega (*Lead Time*) e eliminando erros humanos de deploy.
* **Segurança e Conformidade:** Credenciais sensíveis de banco de dados e chaves criptográficas JWT são isoladas em variáveis secretas protegidas no Azure DevOps e nas configurações de ambiente do Azure Web App.

---

## 🛠️ 3. Stack de Tecnologias Utilizadas

* **Backend & Framework:** C# / ASP.NET Core 8.0 Web API (`net8.0`)
* **Persistência & ORM:** Entity Framework Core 8 + Migrações Automáticas (`ApplyMigrationsAsync`)
* **Banco de Dados em Nuvem:** Oracle Database (`oracle.fiap.com.br:1521/ORCL`)
* **Segurança & Autenticação:** Autenticação e Autorização via Token **JWT Bearer** (`HMACSHA256`) + `PasswordHasher`
* **Qualidade & Testes Automatizados:** **xUnit** (`GuardianPet.Tests.Unit` e `GuardianPet.Tests.Integration`)
* **Observabilidade & Documentação:** Swagger / OpenAPI (`Swashbuckle`), Health Checks (`/health` e `/health/ready`), Serilog e OpenTelemetry
* **Infraestrutura Cloud (Microsoft Azure):**
  * **Resource Group:** `rg-guardianpet-sprint4` (`Brazil South`)
  * **App Service Plan:** `plan-guardianpet` (`Linux`)
  * **Azure Web App:** `guardianpet-api-rm563665` (`DOTNETCORE:8.0`)
* **DevOps & CI/CD:** GitHub (Branch `master`) + **Azure DevOps** (`Azure Pipelines`, `Azure Artifacts` e `Azure Releases`)

---

## 🏗️ 4. Diagrama da Arquitetura + Fluxo CI/CD

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