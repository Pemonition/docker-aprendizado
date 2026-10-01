# Docker na prática: meu laboratório de aprendizado

Registro da aula com o professor Rannyer e dos testes locais realizados em 30/09/2026. Configuração de desenvolvimento, não de produção.

## O que foi validado

- Docker Desktop com WSL no Windows e execução de `hello-world`.
- Construção de imagem com Node 24 e Angular CLI 22; geração de um projeto Angular.
- Construção da API de produtos em .NET 10 com Dockerfile em dois estágios.
- API em contêiner na porta 8080 e PostgreSQL 17 em contêiner, publicado na porta 5433.
- Acesso à API pelo PowerShell do Windows: 48 produtos no banco da aula.
- A mesma API executada pelo `dotnet run` no Windows na porta 5099, usando o banco no Docker.
- Criação de produto temporário pela API no Windows, leitura pela API no Docker e exclusão do registro de teste. Os dados anteriores foram preservados.

## Três conceitos diferentes

| Cenário | Onde roda a API | Endereço do banco usado pela API |
|---|---|---|
| Navegador/PowerShell acessa Docker | Contêiner | `db:5432` |
| `dotnet run` no Windows | Windows | `localhost:5433` |
| Angular no Docker acessado pelo navegador | Contêiner | Não usa banco diretamente neste laboratório |

`localhost` dentro de um contêiner aponta para aquele contêiner. `EXPOSE` documenta a porta da imagem; quem publica a porta no computador é `ports` ou `-p`.

## Reproduzir a API de produtos

Pré-requisitos: Git, Docker com contêineres Linux; SDK .NET 10 somente para a execução fora do Docker.

```powershell
git clone https://github.com/rannyer/aula_api_produto_08_09.git
Copy-Item ./produtos/Dockerfile ./aula_api_produto_08_09/Dockerfile
cd aula_api_produto_08_09
docker compose up -d --build
docker compose ps
Invoke-RestMethod http://localhost:8080/api/Produtos
```

O Compose é o do repositório do professor. Swagger: http://localhost:8080/swagger. Confira a revisão usada: este laboratório foi testado com o projeto declarando `net10.0`; o README original ainda mencionava .NET 8.

### API fora do Docker, banco dentro

No PowerShell, na pasta da API:

```powershell
$env:ConnectionStrings__Postgres = 'Host=localhost;Port=5433;Database=produtosdb;Username=postgres;Password=postgres123'
$env:ASPNETCORE_ENVIRONMENT = 'Development'
dotnet run --project ProdutosApi.csproj --no-launch-profile --urls http://127.0.0.1:5099
```

As credenciais acima são exemplos de desenvolvimento do Compose da aula. Não reutilizar em serviços públicos. Em outro PowerShell: `Invoke-RestMethod http://localhost:5099/api/Produtos`. Ctrl+C encerra apenas a API do Windows.

## Reproduzir o ambiente Angular

```powershell
cd angular
docker compose build
docker compose run --rm ng ng new meu-app
docker compose run --rm -p 127.0.0.1:4200:4200 ng bash
```

Dentro do contêiner:

```bash
cd /app/meu-app
ng serve --host 0.0.0.0 --poll 2000
```

Abrir http://localhost:4200. O bind mount mantém o projeto no computador. Nesta aula foram escolhidos Tailwind e SSR/SSG; isso não é requisito de Docker.

## Comandos que aprendi

| Comando | Efeito |
|---|---|
| `docker compose build` | Constrói a imagem |
| `docker compose up -d` | Cria/inicia os serviços em segundo plano |
| `docker compose ps` | Mostra o estado e as portas |
| `docker compose logs -f api` | Acompanha os logs; Ctrl+C sai dos logs |
| `docker compose stop` / `start` | Para/inicia sem remover os contêineres |
| `docker compose down` | Remove contêineres e rede; preserva volumes nomeados |

Não usar `down -v` quando quiser preservar o banco: essa opção remove os volumes.

## Origem e autoria

API de referência: [rannyer/aula_api_produto_08_09](https://github.com/rannyer/aula_api_produto_08_09). Outra referência da aula: [rannyer/AulaAuth01](https://github.com/rannyer/AulaAuth01). O código das APIs não é apresentado como trabalho autoral meu nem foi copiado para este repositório. Aqui registro configurações, experimentos e explicações, preparados com assistência de IA e conferidos por execução quando indicado.

Próximos exercícios: verificar persistência após recriar contêineres, testar falha do banco e melhorar health checks. Esses exercícios ainda não foram validados nesta sessão.
