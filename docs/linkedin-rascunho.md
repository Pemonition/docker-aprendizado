# Rascunho — revisar antes de postar

Hoje, na aula do professor Rannyer, avancei dos primeiros comandos Docker para uma API .NET com PostgreSQL em contêineres.

O experimento que mais me ajudou foi executar a API de duas formas: dentro do Docker e diretamente no Windows, com as duas conectadas ao mesmo banco em contêiner. Criei um registro temporário por uma API e consultei pela outra.

Isso tornou mais concreta a diferença entre acessar uma aplicação de fora do Docker e executar essa aplicação fora dele. Também entendi melhor portas, nomes dos serviços no Compose e o papel dos volumes.

Organizei os comandos, dificuldades e resultados neste laboratório: https://github.com/Pemonition/docker-aprendizado

Estou aprendendo e usei assistência de IA para configurar, testar e documentar. Meu próximo exercício é verificar a persistência dos dados ao recriar os contêineres.

#Docker #DotNet #PostgreSQL #Aprendizado

## Ideias para próximos posts, somente após validar

1. Por que `localhost` muda de significado dentro do contêiner? Mostrar `db:5432` versus `localhost:5433`.
2. O dado continua quando o contêiner sai? Demonstrar volume nomeado com um registro fictício.
3. Aplicar um aprendizado ao projeto próprio: mudanças pequenas, diff e teste reproduzível.

Evitar capturas com tokens, dados de clientes ou detalhes de repositórios privados. Não afirmar implantação em produção ou domínio completo da ferramenta.
