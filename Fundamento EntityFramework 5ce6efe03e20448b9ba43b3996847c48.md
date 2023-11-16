# Fundamento EntityFramework

O ENTITY É USADO MAIS PARA GRANDES PROCESSAMENTO DE DADOS E O DAPPER É USADO NORMALMENTE PARA PROCESSAMENTO DE DADOS MAIS LEVE E SIMPLES.

---

# O QUE É METADADOS

                             Em um contexto do Entity Framework, metadados se referem a informações sobre o modelo de dados que descrevem a estrutura das entidades, os mapeamentos para as tabelas do banco de dados e outros detalhes relevantes para a persistência e recuperação de dados. Os metadados são essenciais para o funcionamento correto do Entity Framework, pois permitem que ele entenda como as entidades estão relacionadas com as tabelas do banco de dados e como as consultas e operações de persistência devem ser traduzidas para comandos SQL.

resumindo ajuda a entender todo os processos que foi feito de update, delete e outros é uma informação há mais para auxiliar o entity, e isso tudo é armazenado no tracking, e o AsNoTracking no código ajuda a desabilitar toda a consulta referente ao metadados quando queremos fazer uma simples query

---

# Slides para entender a parte conceitual

![Untitled](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled.png)

![Untitled](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%201.png)

![Untitled](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%202.png)

![Untitled](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%203.png)

---

# Criando Models

ESSES MODELS QUE FOI CRIADO COM O NOME DAS TABLES QUE FICA NO BANCO DE DADOS

![Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%204.png](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%204.png)

---

# Propriedades dentro da Class Post

AS PROPRIEDADES SAO OS NOMES DAS COLUNAS QUE ESTÃO DENTRO DAS TABLES

![Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%205.png](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%205.png)

---

# Adicionando os pacotes entity

![Untitled](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%206.png)

```csharp
dotnet add package Microsoft.EntityFrameworkCore
```

![Untitled](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%207.png)

```csharp
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

---

# CRUD básico com Entity

AQUI VOU MOSTRAR O CODIGO DE UM CRUD BASICO NO ENTITY

ESSA É A CLASSE

```sql
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;

namespace blogEntity.Data {
    public class BlogDataContext : DbContext{ /* ele precisa de herdar do dbContext, tem uma obs sobre o significado dela caso queira saber, basicamente é uma classe que contem uma
biblioteca extensa para facilitar a conexão com banco de dados */

        public DbSet<Category> Categories { get; set; }

        public DbSet<Post> Posts { get; set; }

       // public DbSet<PostTag> PostsTags { get; set; }

        public DbSet<Role> Roles { get; set; }

        public DbSet<Tag>  Tags { get; set; }

        public DbSet<User> Users { get; set; }

      //  public DbSet<UserRole> UserRoles { get; set; }

        protected override void OnConfiguring(
            DbContextOptionsBuilder options)
          =>  options.UseSqlServer("Server=localhost,1433;Database=Blog;User ID=sa;Password=1q2w3e4r@#$; TrustServerCertificate=True");

    }
}

```

CLASSE TAG PARA MODIFICAR DADOS

```sql
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace blogEntity.Models {

    [Table("Tag")]
    public class Tag {

        [Key]
        public int Id { get; set; }

        public string Name { get; set; }

        public string Slug { get; set; }
    }
}

```

PROGRAM MAIN COM FUNÇÃO DO CREATE

VEJA QUE ELE PRECISA INSTANCIAR E CRIAR UM NOVO OBJETO EX: (’NEW’)

```sql
using blogEntity.Data;
using blogEntity.Models;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {

            using ( var context = new BlogDataContext()) {

                var tag = new Tag { Name = "caio", Slug = "cainho" }; // dados que inseri no banco
                context.Tags.Add(tag);
                context.SaveChanges(); // esse item salva no banco

            }

        }
    }
}

```

PROGRAM MAIN COM FUNÇÃO DO UPDATE

COMO ELE JA TEM UM OBJETO CRIADO PELO CREATE, É SÓ SETAR A TAG E A COLUNA QUE DESEJA ALTERAR

```sql
using blogEntity.Data;
using blogEntity.Models;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {

            using ( var context = new BlogDataContext()) {

                var tag = context.Tags.FirstOrDefault(x=>x.Id == 1004);
                tag.Name = ">NET";
                tag.Slug = "dotnet";
                context.Update(tag);
                context.SaveChanges();
            }

        }
    }
}

```

PROGRAM MAIN COM FUNÇÃO DE DELETE

AQUI SOMENTE VERIFICAR O ID QUE DESEJA DELETAR E A TABLE SELECIONADA (’TAG’)

```sql
using blogEntity.Data;
using blogEntity.Models;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {

            using ( var context = new BlogDataContext()) {

                var tag = context.Tags.FirstOrDefault(x => x.Id == 1004);

                context.Remove(tag);
                context.SaveChanges();

            }

        }
    }
}

```

```sql
var tag = context.Tags.FirstOrDefault(x => x.Id == 1004);

// Faz uma consulta ao banco de dados utilizando o Entity Framework (EF) para recuperar a primeira entidade da classe Tag onde o valor da propriedade Id seja igual a 1004.
/* ELE LER OS DADOS ANTERIOR E MODIFICA SOMENTE AQUILO QUE REALMENTE FOI ALTERADO, ELE É
SUFICIENTE INTELIGENTE PARA SABER AONDE DEVE SER REALIDADO ALTERAÇÃO SEM MEXER NAS OUTRAS COLUNAS IGUAIS
*/

```

---

# To List

TO LIST EXECUTANDO O BANCO

```sql
using blogEntity.Data;
using blogEntity.Models;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {

            using ( var context = new BlogDataContext()) {

                var tags = context.Tags
                    .Where(x => x.Name.Contains("caio"))
                    .ToList();

                foreach (var tag in tags) {

                    Console.WriteLine(tag.Name);

                }
            }

            /* devemos sempre filtrar os dados primeiro para depois excutar o banco, isso influencia demais na performace*/

            /* executa no banco, se colocar antes do where ele vai executar o banco antes e filtrar depois, é como se a pessoa usasse um SELECT *, e todos esses dados vai para o sevirdor e acaba travando o servidor da empresa*/

            /* esse exemplo seria o pior dos casos

            var tags = context.Tags
                    .ToList();
                    .Where(x => x.Name.Contains("caio"))

            */
        }
    }
}

```

---

# ASNOTRACKING

```sql
using blogEntity.Data;
using blogEntity.Models;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {
            
            using ( var context = new BlogDataContext()) {

                var tags = context.Tags
                    .Where(x => x.Name.Contains("caio"))
											 .AsNoTracking()
                    .ToList();

                foreach (var tag in tags) { 

                    Console.WriteLine(tag.Name);

                }
            }

            /* com o asnotracking conseguimos otimizar mais ainda a leitura do banco, porque 
ele tem a função de verificar as alterações anteriores no banco, e nisso tiramos esse 
processamentos durante a leitura, não podemos usar ele de forma nenhuma quando for feito o
update e delete no banco, no create podemos utiliza-lo*/
        }
    }
}
```

Em resumo, o tracking no Entity Framework é a capacidade de rastrear automaticamente as alterações feitas em objetos de entidade enquanto eles estão em memória, permitindo que essas alterações sejam persistidas no banco de dados quando você chamar o método **`SaveChanges()`**.

```sql
using blogEntity.Data;
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {
            
            using ( var context = new BlogDataContext()) {

                var tag = context
                    .Tags
                    .AsNoTracking()
                    .FirstOrDefault(x => x.Id ==1005); // pega esse id, se o item não existir ele traz como nullo 

                Console.WriteLine(tag?.Name); // ? garante que nao passa nenhum campo nulo do banco
                                                

            }

        }

    }
 }
```

---

# Slides do mapeamento

![Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%208.png](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%208.png)

![Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%209.png](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%209.png)

---

# TABLE, KEY, INDENTITY E NAVIGATION PROPERTIES

```sql
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace blogEntity.Models {

    [Table("Post")] // sinalizando para ele reconhecer a table Post
    public class Post {

        [Key]
        [DatabaseGenerated(DatabaseGeneratedOption.Identity)] /* indicando que ele é uma chaveprimaria*/
        public int Id { get; set; }

        public string Title { get; set; }

        public string Summary { get; set; }

        public string Body { get; set; }

        public string Slug { get; set; }

        public DateTime CreateDate { get; set; }

        public DateTime LastUpdateDate { get; set; }

        [ForeignKey("CategoryId")]
        public int CategoryId { get; set; }

        public Category Category { get; set; } // propriedade de navegação, indicando aonde ele deve ir

        [ForeignKey("AuthorId")]
        public int AuthorId { get; set; }

        public User Author { get; set; }  // propriedade de navegação, indicando aonde ele deve ir

    }
}

```

---

# REQUIRED, MAXLENGHT E COLUMN

```sql
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace blogEntity.Models {

    [Table("Category")] // serve para indentificar com mais facilidade a table
    public class Category {

        [Key]   /*Esse atributo é usado para identificar uma propriedade como chave primária da entidade. */
        [DatabaseGenerated(DatabaseGeneratedOption.Identity)] /*Esse atributo é usado para indicar que o valor da chave primária será gerado automaticamente pelo banco de dados. O parâmetro DatabaseGeneratedOption.Identity especifica que o valor será gerado automaticamente à medida que novos registros forem adicionados à tabela.*/

        public int Id { get; set; }

        [Required] // como se fosse o isNotNull no SQL
        [MinLength(3)] // quantidade minima de caracteres
        [MaxLength(80)] // quantidade maxima de caracteres
        [Column("Name",TypeName = "NVARCHAR")] // tipo primitivo da coluna name
        public string Name { get; set; }

        [Required] // como se fosse o isNotNull no SQL
        [MinLength(3)] // quantidade minima de caracteres
        [MaxLength(80)] // quantidade maxima de caracteres
        [Column("Slug", TypeName = "VARCHAR")] // tipo primitivo da coluna name

        public string Slug { get; set; }
    }
}

```

---

# TRABALHANDO COM SUBCONJUNTOS, AQUI TBM FOI FEITO UM CREATE

```sql
using blogEntity.Data;
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {

            using var context = new BlogDataContext();

            var user = new User() {

                Name = "caio zim",
                Slug = "caiozim",
                Email = "caiozim@gmail.com",
                Bio = "eu sou o milior kkk",
                Image = "<https://balta.io>",
                PasswordHash = "123456789"

            };

            var category = new Category {

                Name = "Backend",
                Slug = "backend"

            };

            var post = new Post {

                Author = user,     // referencia para localizar
                Category = category,  // referencia para localizar
                Body = "<p>Hello world</p>",
                Slug = "comecando-com-ef-core",
                Summary = "Neste artigo vamos aprender EF core",
                Title = "Começando com EF Core",
                CreateDate = DateTime.Now,
                LastUpdateDate = DateTime.Now,

            };

            context.Posts.Add(post);
            context.SaveChanges();

        }

    }
 }

```

---

# INCLUDE

```sql
using blogEntity.Data;
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {

            using var context = new BlogDataContext();

            var posts = context
                .Posts
                .AsNoTracking()
                .Include(x => x.Author) // inclue o nome
                .Where(x => x.AuthorId ==2002)
                .OrderByDescending(x => x.LastUpdateDate)
                .ToList();

            foreach (var post in posts)
                Console.WriteLine($"{post.Title} escrito por {post.Author?.Name}");
        }

    }
 }

```

---

# ANALISANDO OS LOGS

```sql
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;

namespace blogEntity.Data {
    public class BlogDataContext : DbContext{

        public DbSet<Category> Categories { get; set; }

        public DbSet<Post> Posts { get; set; }

        public DbSet<User> Users { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder options) {

            options.UseSqlServer("Server=localhost,1433;Database=Blog;User ID=sa;Password=1q2w3e4r@#$; TrustServerCertificate=True");
            options.LogTo(Console.WriteLine); // ajuda a ver o log das modificações

        }

    }
}

```

LOG 1

```sql
using blogEntity.Data;
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {

            using var context = new BlogDataContext();

            var posts = context
                .Posts
                .AsNoTracking()
                .Where(x => x.AuthorId ==2002)
                .OrderByDescending(x => x.LastUpdateDate)
                .ToList();

            foreach (var post in posts)
                Console.WriteLine($"{post.Title} escrito por {post.Author?.Name} em {post.Category?.Name}");
        }

    }
 }

```

RESULTADO NO SQL RAIZ

```sql
SELECT [p].[Id], [p].[AuthorId], [p].[Body], [p].[CategoryId], [p].[CreateDate], [p].[LastUpdateDate], [p].[Slug], [p].[Summary], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[AuthorId] = 2002
      ORDER BY [p].[LastUpdateDate] DESC

```

LOG 2

```sql
using blogEntity.Data;
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {

            using var context = new BlogDataContext();

            var posts = context
                .Posts
                .AsNoTracking()
                .Include(x => x.Author)
                .Where(x => x.AuthorId ==2002)
                .OrderByDescending(x => x.LastUpdateDate)
                .ToList();

            foreach (var post in posts)
                Console.WriteLine($"{post.Title} escrito por {post.Author?.Name} em {post.Category?.Name}");
        }

    }
 }

```

RESULTADO NO SQL RAIZ

```sql
SELECT [p].[Id], [p].[AuthorId], [p].[Body], [p].[CategoryId], [p].[CreateDate], [p].[LastUpdateDate], [p].[Slug], [p].[Summary], [p].[Title], [u].[Id], [u].[Bio], [u].[Email], [u].[Image], [u].[Name], [u].[PasswordHash], [u].[Slug]
      FROM [Post] AS [p]
      INNER JOIN [User] AS [u] ON [p].[AuthorId] = [u].[Id]
      WHERE [p].[AuthorId] = 2002
      ORDER BY [p].[LastUpdateDate] DESC

```

LOG 3

```sql
using blogEntity.Data;
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {

            using var context = new BlogDataContext();

            var posts = context
                .Posts
                .AsNoTracking()
                .Include(x => x.Author)
                .Include(x => x.Category)
                 // .ThenInclude(x=>x.)   devemos evitar ao maximo, pois ele faz a função do subselect no SQL
                .Where(x => x.AuthorId ==2002)
                .OrderByDescending(x => x.LastUpdateDate)
                .ToList();

            foreach (var post in posts)
                Console.WriteLine($"{post.Title} escrito por {post.Author?.Name} em {post.Category?.Name}");
        }

    }
 }

```

RESULTADO NO SQL RAIZ

```sql
SELECT [p].[Id], [p].[AuthorId], [p].[Body], [p].[CategoryId], [p].[CreateDate], [p].[LastUpdateDate], [p].[Slug], [p].[Summary], [p].[Title], [u].[Id], [u].[Bio], [u].[Email], [u].[Image], [u].[Name], [u].[PasswordHash], [u].[Slug], [c].[Id], [c].[Name], [c].[Slug]
      FROM [Post] AS [p]
      INNER JOIN [User] AS [u] ON [p].[AuthorId] = [u].[Id]
      INNER JOIN [Category] AS [c] ON [p].[CategoryId] = [c].[Id]
      WHERE [p].[AuthorId] = 2002
      ORDER BY [p].[LastUpdateDate] DESC

```

PODEMOS VER QUE CADA VEZ QUE COLOCAMOS O INCLUDE, NA RAIZ DO SQL ELE FAZ UM INNER JOIN, ENTÃO DEVEMOS TER CUIDADO AO USAR, POIS PODE INFLUENCIAR MUITO NA PERFORMANCE

---

# FAZENDO UPDATE E ALTERANDO ALGUNS SUBCONJUNTOS

```sql
using blogEntity.Data;
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;

namespace blogEntity {
    internal class Program {
        static void Main(string[] args) {

            using var context = new BlogDataContext();

            var post = context
                .Posts
                //.AsNoTracking() PRECISAMOS DO TRACKING PARA ALTERAR OS DADOS
                .Include(x => x.Author)
                .Include(x => x.Category)
                .OrderByDescending(x => x.LastUpdateDate)
                .FirstOrDefault(); // pegando primeiro item

            post.Author.Name = "Teste";

            context.Posts.Update(post);
            context.SaveChanges();
        }

    }
 }

```

---

# MAPEAMENTO INTRODUÇÃO

PRIMERA COISA É CRIAR UMA PASTA E UMA CLASSE CONFORME O PRINT

![Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%2010.png](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%2010.png)

SEGUE O CODIGO CATEGORYMAP

```jsx
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;

namespace blogEntity.Data.Mappings {
    public  class CategoryMap : IEntityTypeConfiguration<Category>{

        public int MyProperty { get; set; }

        public void Configure(EntityTypeBuilder<Category> builder) {

            //tabela
            builder.ToTable("Category");

            //chave primaria
            builder.HasKey(x => x.Id);

            //identity
            builder.Property(x => x.Id)
                .ValueGeneratedOnAdd()
                .UseIdentityColumn();
        }
    }
}

```

ESSA PARTE É O MAPEAMENTO DE PROPRIEDADES

```jsx
using blogEntity.Models;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;

namespace blogEntity.Data.Mappings {
    public  class CategoryMap : IEntityTypeConfiguration<Category>{

        public int MyProperty { get; set; }

        public void Configure(EntityTypeBuilder<Category> builder) {

            //tabela
            builder.ToTable("Category");

            //chave primaria
            builder.HasKey(x => x.Id);

            //identity
            builder.Property(x => x.Id)
                .ValueGeneratedOnAdd()
                .UseIdentityColumn();

            // Outras propriedades
            builder.Property(x => x.Name)
                .IsRequired()
                .HasColumnName("Name")            /* aqui basicamente estamos escrevendo o script sql atraves do entity */
                .HasColumnType("NVARCHAR")          // isso é uma api fluente
                .HasMaxLength(80);

            builder.Property(x => x.Slug)
                .IsRequired()
                .HasColumnName("Slug")
                .HasColumnType("VARCHAR")
                .HasMaxLength(80);
            // Indices
            builder.HasIndex(x => x.Slug, "IX_Category_Slug")
                .IsUnique();

        }
    }
}

```

---

# RELACIONAMENTO UM PARA MUITOS

```jsx
builder.HasOne(x => x.Category)
                .WithMany(x => x.Posts)
                .HasConstraintName("FK_Post_Category")
                .OnDelete(DeleteBehavior.Cascade);

```

Esse trecho de código também faz parte do mapeamento de entidades usando o Entity Framework. Vou explicar cada parte:

1. **builder.HasOne(x => x.Category):** Isso está configurando um relacionamento um-para-muitos entre a entidade **`Post`** e a entidade **`Category`**. Isso significa que um post está associado a uma única categoria, mas uma categoria pode ter vários posts.
2. **.WithMany(x => x.Posts):** Isso indica que a entidade **`Category`** está associada a vários posts. A propriedade **`Posts`** na entidade provavelmente é uma coleção que armazena os posts pertencentes a essa categoria.
3. **.HasConstraintName("FK_Post_Category"):** Isso define um nome para a chave estrangeira que será criada no banco de dados para relacionar a tabela de posts com a tabela de categorias. O nome da chave estrangeira será "FK_Post_Category".
4. **.OnDelete(DeleteBehavior.Cascade):** Isso define o comportamento de exclusão em cascata. Quando uma **`Category`** é excluída, todos os **`Posts`** associados a essa categoria também serão excluídos (cascata) para manter a integridade dos dados.

Resumindo, esse trecho de código configura um relacionamento um-para-muitos entre a entidade **`Post`** e a entidade **`Category`**, onde cada post está associado a uma única categoria, mas uma categoria pode ter vários posts. Quando uma categoria é excluída, todos os posts associados a essa categoria também são excluídos. Isso é feito usando o Entity Framework para mapear as relações entre as entidades e definir o comportamento de exclusão.

---

# RELACIONAMENTO MUITOS PARA MUITOS

```jsx
builder.HasMany(x => x.Tags)
                .WithMany(x => x.Posts)
                .UsingEntity<Dictionary<string, object>>(
                    "PostTag",
                   post => post.HasOne<Tag>()
                    .WithMany()
                    .HasForeignKey("PostId")
                    .HasConstraintName("FK_PostTag_PostId")
                    .OnDelete(DeleteBehavior.Cascade),
                   tag => tag.HasOne<Post>()
                   .WithMany()
                   .HasForeignKey("TagId")
                   .HasConstraintName("FK_PostTag_TagId")
                   .OnDelete(DeleteBehavior.Cascade));

```

Esse código parece ser um trecho de código C# usando o Entity Framework, uma biblioteca de mapeamento objeto-relacional (ORM) para trabalhar com bancos de dados em aplicativos .NET. Vou explicar cada parte desse código:

1. **builder.HasMany(x => x.Tags).WithMany(x => x.Posts):** Isso define uma relação muitos-para-muitos entre as entidades **`Post`** e **`Tag`**. Um post pode ter várias tags, e uma tag pode estar associada a vários posts.
2. **.UsingEntity<Dictionary<string, object>>("PostTag", ...):** Isso define uma entidade intermediária chamada "PostTag" para representar a relação muitos-para-muitos entre **`Post`** e **`Tag`**. Essa entidade intermediária é usada para armazenar os relacionamentos entre posts e tags, pois os bancos de dados relacionais não suportam diretamente relações muitos-para-muitos.
3. **post => post.HasOne<Tag>().WithMany()...:** Isso configura o relacionamento da entidade **`Post`** com a entidade intermediária "PostTag". Está dizendo que um post pode estar associado a várias tags. A chave estrangeira "PostId" é definida aqui e relacionada à tabela "Post".
4. **tag => tag.HasOne<Post>().WithMany()...:** Isso configura o relacionamento da entidade **`Tag`** com a entidade intermediária "PostTag". Está dizendo que uma tag pode estar associada a vários posts. A chave estrangeira "TagId" é definida aqui e relacionada à tabela "Tag".
5. **.OnDelete(DeleteBehavior.Cascade):** Isso define o comportamento de exclusão em cascata. Quando um **`Post`** é excluído, todas as entradas correspondentes na tabela "PostTag" também serão excluídas (cascata) para manter a integridade dos dados. O mesmo se aplica quando uma **`Tag`** é excluída.

Portanto, esse trecho de código está configurando as relações muitos-para-muitos entre as entidades **`Post`** e **`Tag`** usando uma entidade intermediária "PostTag" para armazenar os relacionamentos. Isso é feito usando o Entity Framework, que simplifica a interação com bancos de dados relacionais em aplicativos .NET.

---

# Projeto do modulo 3 (Mapeamento)

ORGANIZAÇÃO DE PASTAS E CLASSES

![Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%2011.png](Fundamento%20EntityFramework%205ce6efe03e20448b9ba43b3996847c48/Untitled%2011.png)

[Program](https://www.notion.so/Program-833046959c254ec6b4ee2b17f9cb38ea?pvs=21)

[Data](https://www.notion.so/Data-a3b0a65734f74f13bfda560cc14825a3?pvs=21)

[Mappings](https://www.notion.so/Mappings-c3e87d8609424bf99eda98e098ccb6f8?pvs=21)

[Models](https://www.notion.so/Models-fbefc962b1c3465493b545a5ec83497b?pvs=21)

---

# Migrations

[Configurando o migrations](https://www.notion.so/Configurando-o-migrations-a6711bad6339456fa9b161d6c5db0ee5?pvs=21)

[Criando Migrations](https://www.notion.so/Criando-Migrations-67e51de3ae1948bd9e7f022f881177c8?pvs=21)

[Resultado da migração](https://www.notion.so/Resultado-da-migra-o-5d69dacab925423881e9fc46eaf7b0dc?pvs=21)

[Gerando nova versão do banco](https://www.notion.so/Gerando-nova-vers-o-do-banco-28dab5680c504eed82c8a0e7e0bc5fc9?pvs=21)

[Criando script para o migrations](https://www.notion.so/Criando-script-para-o-migrations-70fe43d754e34b61bc61a5e30b291612?pvs=21)

---

# Performance

[AsNoTracking](https://www.notion.so/AsNoTracking-101a9699046e4b7f91de66c663825fd8?pvs=21)

[Async e await](https://www.notion.so/Async-e-await-18793a23d2c7429d802a9a7877d4ead0?pvs=21)

[**Eager Loading VS Lazy Loading**](https://www.notion.so/Eager-Loading-VS-Lazy-Loading-0b1ac27564bf4db18f5f0bc35fbe4ff9?pvs=21)

[**Skip, Take e Paginação de dados**](https://www.notion.so/Skip-Take-e-Pagina-o-de-dados-a1531ae88cb24aa2820192fed4071048?pvs=21)

[**ThenInclude**](https://www.notion.so/ThenInclude-52203902a0c64568bc878d35fdaf801b?pvs=21)

[**Mapeando Queries Puras e Views**](https://www.notion.so/Mapeando-Queries-Puras-e-Views-5de13477546c4e1c97aac0fa48ece8cc?pvs=21)