# 🐾 Patinhas em Dia API

> API REST para gerenciamento de saúde e cuidados de pets, desenvolvida com ASP.NET Core (.NET 9) e Oracle Database.

---

## 📋 Descrição do Projeto

O **Patinhas em Dia** é uma aplicação backend que permite o controle completo de tutores, seus pets e os eventos de cuidado associados a cada animal (consultas, vacinas, banhos, medicamentos etc.).

A API oferece endpoints para:
- Cadastrar e gerenciar **tutores** (donos dos pets)
- Cadastrar e gerenciar **pets** vinculados aos tutores
- Registrar e acompanhar **eventos de cuidado** (com status, prioridade e data prevista)
- Consultar um **resumo de saúde** de cada pet
- Identificar eventos **atrasados** automaticamente

A documentação interativa está disponível via **Swagger UI** assim que a aplicação é executada.

---

## 🛠️ Tecnologias Utilizadas

| Tecnologia | Versão |
|---|---|
| .NET | 9.0 |
| ASP.NET Core Web API | 9.0 |
| Entity Framework Core | 9.0.4 |
| Oracle.EntityFrameworkCore | 9.23.60 |
| Swashbuckle (Swagger) | 6.9.0 |
| Oracle Database | ORCL |

---

## 🗂️ Modelo de Dados

```
TUTOR (1) ──── (N) PET (1) ──── (N) EVENTO_CUIDADO
```

### Tutor
| Campo | Tipo | Descrição |
|---|---|---|
| `ID_TUTOR` | int (PK) | Identificador único |
| `NOME` | string (100) | Nome do tutor — obrigatório |
| `EMAIL` | string (100) | E-mail do tutor |
| `TELEFONE` | string (20) | Telefone de contato |

### Pet
| Campo | Tipo | Descrição |
|---|---|---|
| `ID_PET` | int (PK) | Identificador único |
| `NOME` | string (100) | Nome do pet — obrigatório |
| `ESPECIE` | string (50) | Espécie (ex: Cão, Gato) |
| `RACA` | string (50) | Raça do animal |
| `IDADE` | int | Idade em anos |
| `ID_TUTOR` | int (FK) | Referência ao tutor |

### EventoCuidado
| Campo | Tipo | Descrição |
|---|---|---|
| `ID_EVENTO` | int (PK) | Identificador único |
| `ID_PET` | int (FK) | Referência ao pet |
| `TIPO_CUIDADO` | string (50) | Tipo do evento — obrigatório |
| `DATA_PREVISTA` | DateTime | Data agendada |
| `STATUS` | string (30) | `PENDENTE`, `CONCLUIDO` ou `ATRASADO` |
| `PRIORIDADE` | string (20) | Nível de prioridade |
| `OBSERVACAO` | string (500) | Observações adicionais |

---

## 🔌 Documentação das Rotas

> Base URL: `https://localhost:{porta}/api`  
> A porta é definida automaticamente pelo ambiente. Acesse o Swagger em `/swagger` para testar interativamente.

---

### 👤 Tutores — `/api/Tutores`

| Método | Rota | Descrição |
|---|---|---|
| `GET` | `/api/Tutores` | Lista todos os tutores com seus pets |
| `GET` | `/api/Tutores/{id}` | Busca um tutor pelo ID |
| `POST` | `/api/Tutores` | Cria um novo tutor |
| `PUT` | `/api/Tutores/{id}` | Atualiza os dados de um tutor |
| `DELETE` | `/api/Tutores/{id}` | Remove um tutor pelo ID |

#### Exemplo — `POST /api/Tutores`
```json
{
  "nome": "Maria Silva",
  "email": "maria@email.com",
  "telefone": "11999990000"
}
```

#### Exemplo — Resposta `GET /api/Tutores/{id}`
```json
{
  "id": 1,
  "nome": "Maria Silva",
  "email": "maria@email.com",
  "telefone": "11999990000",
  "pets": []
}
```

---

### 🐶 Pets — `/api/Pets`

| Método | Rota | Descrição |
|---|---|---|
| `GET` | `/api/Pets` | Lista todos os pets com seus tutores |
| `GET` | `/api/Pets/{id}` | Busca um pet pelo ID |
| `GET` | `/api/Pets/tutor/{tutorId}` | Lista todos os pets de um tutor |
| `GET` | `/api/Pets/{id}/resumo-saude` | Retorna resumo de eventos do pet |
| `POST` | `/api/Pets` | Cadastra um novo pet |
| `PUT` | `/api/Pets/{id}` | Atualiza os dados de um pet |
| `DELETE` | `/api/Pets/{id}` | Remove um pet pelo ID |

#### Exemplo — `POST /api/Pets`
```json
{
  "nome": "Rex",
  "especie": "Cão",
  "raca": "Labrador",
  "idade": 3,
  "tutorId": 1
}
```

#### Exemplo — Resposta `GET /api/Pets/{id}/resumo-saude`
```json
{
  "nome": "Rex",
  "totalEventos": 5,
  "pendentes": 2,
  "concluidos": 2,
  "atrasados": 1
}
```

---

### 📅 Eventos de Cuidado — `/api/Eventos`

| Método | Rota | Descrição |
|---|---|---|
| `GET` | `/api/Eventos` | Lista todos os eventos com seus pets |
| `GET` | `/api/Eventos/pet/{petId}` | Lista todos os eventos de um pet |
| `GET` | `/api/Eventos/status/{status}` | Filtra eventos por status |
| `GET` | `/api/Eventos/atrasados` | Lista eventos com data passada e status PENDENTE |
| `POST` | `/api/Eventos` | Registra um novo evento de cuidado |
| `PUT` | `/api/Eventos/{id}` | Atualiza um evento existente |
| `DELETE` | `/api/Eventos/{id}` | Remove um evento pelo ID |

#### Exemplo — `POST /api/Eventos`
```json
{
  "petId": 1,
  "tipoCuidado": "Vacina V10",
  "dataPrevista": "2026-06-15T10:00:00",
  "status": "PENDENTE",
  "prioridade": "ALTA",
  "observacao": "Reforço anual"
}
```

#### Valores válidos para `status`
| Valor | Descrição |
|---|---|
| `PENDENTE` | Evento ainda não realizado |
| `CONCLUIDO` | Evento já realizado |
| `ATRASADO` | Evento marcado como atrasado manualmente |

> **Nota:** O endpoint `GET /api/Eventos/atrasados` detecta automaticamente eventos com `DataPrevista` anterior à data atual e status `PENDENTE`.

---

### 📊 Códigos de Resposta HTTP

| Código | Significado |
|---|---|
| `200 OK` | Requisição bem-sucedida |
| `201 Created` | Recurso criado com sucesso |
| `204 No Content` | Atualização ou exclusão bem-sucedida |
| `400 Bad Request` | Dados inválidos na requisição |
| `404 Not Found` | Recurso não encontrado |

---

## 🚀 Instalação e Execução

### Pré-requisitos

Antes de começar, certifique-se de ter instalado:

- [.NET 9 SDK](https://dotnet.microsoft.com/download/dotnet/9.0)
- [Git](https://git-scm.com/)
- Acesso à rede da FIAP (para conectar ao banco Oracle em `oracle.fiap.com.br`)

---

### 1. Clonar o repositório

```bash
git clone https://github.com/seu-usuario/patinhasemdia.git
cd patinhasemdia
```

---

### 2. Configurar a string de conexão

Abra o arquivo `appsettings.json` e verifique (ou ajuste) as credenciais do banco Oracle:

```json
{
  "ConnectionStrings": {
    "OracleConnection": "User Id=SEU_RM;Password=SUA_SENHA;Data Source=oracle.fiap.com.br:1521/ORCL"
  }
}
```

> ⚠️ **Nunca suba credenciais reais para o repositório.** Use variáveis de ambiente em produção.

---

### 3. Restaurar as dependências

```bash
dotnet restore
```

---

### 4. Aplicar as migrations (criar as tabelas no banco)

```bash
dotnet ef database update
```

> Caso o `dotnet ef` não esteja instalado:
> ```bash
> dotnet tool install --global dotnet-ef
> ```

---

### 5. Executar a aplicação

```bash
dotnet run
```

A API estará disponível em:
```
http://localhost:5000
https://localhost:5001
```

---

### 6. Acessar o Swagger

Com a aplicação rodando, abra no navegador:

```
http://localhost:5000/swagger
```

Você verá a interface interativa com todas as rotas documentadas e poderá testá-las diretamente pelo navegador.

---

## 📁 Estrutura do Projeto

```
patinhasemdia/
├── Controllers/
│   ├── EventosController.cs      # Endpoints de eventos de cuidado
│   ├── PetsController.cs         # Endpoints de pets
│   └── TutoresController.cs      # Endpoints de tutores
├── Data/
│   └── AppDbContext.cs           # Contexto do Entity Framework
├── Migrations/
│   └── 20260430112854_InitialCreate.cs  # Migration inicial
├── Models/
│   ├── EventoCuidado.cs          # Model de evento de cuidado
│   ├── Pet.cs                    # Model de pet
│   └── Tutor.cs                  # Model de tutor
├── appsettings.json              # Configurações da aplicação
├── appsettings.Development.json  # Configurações de desenvolvimento
├── Program.cs                    # Ponto de entrada e configuração da API
└── patinhasemdia.csproj          # Arquivo de projeto .NET
```

---

## 👥 Integrantes do Grupo

| Nome | RM |
|Yasmin Nathalin Miranda dos Santos|RM561365|
|Lucas Da Silva Lima | RM562118 |
|Riquelme Nasciemnto | RM565468 |
|Enzo Franchin De Souza | RM565677 |


---

## 📄 Licença

Este projeto foi desenvolvido para fins acadêmicos — FIAP.
