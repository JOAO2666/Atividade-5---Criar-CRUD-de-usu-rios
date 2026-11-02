# Atividade 5 - Criar CRUD de usuários (SENAI)

Esta atividade consiste na implementação completa das operações **CRUD (Create, Read, Update e Delete)**, busca por ID e método de Login para a entidade `Usuario` no projeto **Exo.WebApi**, utilizando **.NET 6.0**, **Entity Framework Core** e **SQL Server**, com validação das rotas HTTP através do **Insomnia** (ou **Postman**).

---

## 📁 Estrutura do Projeto

```plaintext
├── cria-db.sql                          # Script SQL para criação do banco ExoApi e tabelas Projetos/Usuarios
├── Insomnia_ExoApi_Collection.json      # Coleção pronta para importação no Insomnia com todas as requisições CRUD de Usuários
├── Exo.WebApi/
│   ├── Contexts/
│   │   └── ExoContext.cs                # Contexto do Entity Framework Core (DbSet<Projeto> e DbSet<Usuario>)
│   ├── Controllers/
│   │   ├── ProjetosController.cs        # Controller REST de Projetos (/api/projetos)
│   │   └── UsuariosController.cs        # Controller REST de Usuários (/api/usuarios)
│   ├── Models/
│   │   ├── Projeto.cs                   # Entidade Projeto (Id, NomeDoProjeto, Area, Status)
│   │   └── Usuario.cs                   # Entidade Usuario (Id, Email, Senha)
│   ├── Repositories/
│   │   ├── ProjetoRepository.cs         # Camada Repository de Projetos
│   │   └── UsuarioRepository.cs         # Camada Repository de Usuários (Login, Listar, Cadastrar, BuscaPorId, Atualizar, Deletar)
│   ├── Properties/
│   │   └── launchSettings.json          # Configurações de execução e portas (https://localhost:7154)
│   ├── appsettings.json                 # Configurações da aplicação
│   ├── Exo.WebApi.csproj                # Arquivo de projeto .NET 6.0 e dependências NuGet
│   └── Program.cs                       # Configuração de DI (ExoContext, ProjetoRepository, UsuarioRepository) e pipeline HTTP
└── README.md
```

---

## 🛠️ Tecnologias Utilizadas

- **C# / .NET 6.0 (ou .NET 7.0)**
- **ASP.NET Core Web API**
- **Entity Framework Core 6.0 (`Microsoft.EntityFrameworkCore.SqlServer`)**
- **SQL Server / SQL Server Management Studio (SSMS)**
- **Insomnia / Postman** (Testes de integração dos endpoints REST)

---

## 🗄️ 1. Configuração do Banco de Dados (SSMS)

1. Abra o **SQL Server Management Studio (SSMS)**.
2. Abra o arquivo [`cria-db.sql`](cria-db.sql) e clique em **Executar** (`F5`).
3. O script criará o banco de dados `ExoApi`, a tabela `Projetos` e a tabela `Usuarios` (com 2 usuários iniciais: `email@sp.br` e `email_dois@sp.br`).
4. Verifique a string de conexão em [`Exo.WebApi/Contexts/ExoContext.cs`](Exo.WebApi/Contexts/ExoContext.cs) de acordo com sua instância local do SQL Server (ex.: `Server=localhost\SQLEXPRESS;Database=ExoApi;Trusted_Connection=True;`).

---

## 💻 2. Implementações Realizadas

### `Models/Usuario.cs`
- Propriedades `Id` (`int`), `Email` (`string`) e `Senha` (`string`).

### `Repositories/UsuarioRepository.cs`
- **`Login(string email, string senha)`**: retorna o usuário correspondente ao e-mail e senha informados (`_context.Usuarios.FirstOrDefault(u => u.Email == email && u.Senha == senha)`).
- **`Listar()`**: retorna `List<Usuario>` com todos os usuários (`_context.Usuarios.ToList()`).
- **`Cadastrar(Usuario usuario)`**: adiciona um novo usuário (`_context.Usuarios.Add(usuario)`) e salva as alterações (`_context.SaveChanges()`).
- **`BuscaPorId(int id)`**: busca um usuário pela chave primária (`_context.Usuarios.Find(id)`).
- **`Atualizar(int id, Usuario usuario)`**: localiza o usuário existente pelo `id`, atualiza `Email` e `Senha`, marca com `_context.Usuarios.Update(usuarioBuscado)` e executa `_context.SaveChanges()`.
- **`Deletar(int id)`**: localiza o usuário pelo `id`, remove via `_context.Usuarios.Remove(usuarioBuscado)` e executa `_context.SaveChanges()`.

### `Controllers/UsuariosController.cs` (`api/usuarios`)
| Método HTTP | Rota | Método no Controller | Status de Sucesso | Descrição |
| :--- | :--- | :--- | :---: | :--- |
| `GET` | `/api/usuarios` | `Listar()` | `200 OK` | Lista todos os usuários cadastrados |
| `POST` | `/api/usuarios` | `Cadastrar(Usuario usuario)` | `201 Created` | Cadastra um novo usuário |
| `GET` | `/api/usuarios/{id}` | `BuscarPorId(int id)` | `200 OK` / `404 Not Found` | Busca um usuário específico pelo ID |
| `PUT` | `/api/usuarios/{id}` | `Atualizar(int id, Usuario usuario)` | `204 No Content` | Atualiza os dados de um usuário pelo ID |
| `DELETE` | `/api/usuarios/{id}` | `Deletar(int id)` | `204 No Content` / `400 Bad Request` | Remove um usuário pelo ID |

### `Contexts/ExoContext.cs` e `Program.cs`
- Adicionado `public DbSet<Usuario> Usuarios { get; set; }` em `ExoContext`.
- Registrado o serviço `builder.Services.AddTransient<UsuarioRepository, UsuarioRepository>();` em `Program.cs`.

---

## 🚀 3. Executando a Aplicação

No terminal, dentro da pasta `Exo.WebApi`:

```bash
cd Exo.WebApi
dotnet restore
dotnet build
dotnet run
```

A aplicação ficará disponível em:
- `https://localhost:7154/api/usuarios`
- `http://localhost:5038/api/usuarios`

---

## 🧪 4. Roteiro de Testes no Insomnia

Você pode importar diretamente o arquivo [`Insomnia_ExoApi_Collection.json`](Insomnia_ExoApi_Collection.json) no **Insomnia** ou executar manualmente as requisições abaixo:

### 4.1. Listar Usuários (`GET`)
- **URL:** `https://localhost:7154/api/usuarios/`
- **Resposta esperada (`200 OK`):** Lista com os usuários iniciais (IDs 1 e 2).

### 4.2. Cadastrar Novo Usuário (`POST`)
- **URL:** `https://localhost:7154/api/usuarios/`
- **Body (JSON):**
  ```json
  {
      "email": "email_tres@sp.br",
      "senha": "1234"
  }
  ```
- **Resposta esperada:** `201 Created`

### 4.3. Buscar Usuário por ID (`GET`)
- **URL:** `https://localhost:7154/api/usuarios/3`
- **Resposta esperada (`200 OK`):**
  ```json
  {
      "id": 3,
      "email": "email_tres@sp.br",
      "senha": "1234"
  }
  ```

### 4.4. Atualizar Usuário (`PUT`)
- **URL:** `https://localhost:7154/api/usuarios/3`
- **Body (JSON):**
  ```json
  {
      "email": "email_quatro@sp.br",
      "senha": "4321"
  }
  ```
- **Resposta esperada:** `204 No Content`

### 4.5. Deletar Usuário (`DELETE`)
- **URL:** `https://localhost:7154/api/usuarios/3`
- **Resposta esperada:** `204 No Content`
