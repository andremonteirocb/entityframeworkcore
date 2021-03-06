
#### Gerando script
dotnet ef migrations script -p .\Fundamentos.EFCore\Fundamentos.EFCore.csproj -o .\Fundamentos.EFCore\Data\Sql\Initial.sql

#### Gerando script idempotente
dotnet ef migrations script -p .\Fundamentos.EFCore\Fundamentos.EFCore.csproj -o .\Fundamentos.EFCore\Data\Sql\Initial.sql -i

#### Adicionando a migrations
dotnet ef migrations add Initial --p .\Fundamentos.EFCore\Fundamentos.EFCore

#### Revertendo a migrations
dotnet ef migrations remove -p .\Fundamentos.EFCore\Fundamentos.EFCore.csproj

#### Aplindo migrations
dotnet ef migrations update -p .\Fundamentos.EFCore\Fundamentos.EFCore.csproj -v

#### Instalando pacotes nuget
 dotnet add .\Fundamentos.EFCore\Fundamentos.EFCore.csproj package nome_pacote_nuget --version 3.1.5

### Para utilizar o Code First, execute as etapas abaixo:
1 - Faça Build do projeto
2 - Abra o Package Manager Console
3 - Execute o comando 
	add-migration <identificacaoDaMudancaNoBanco>
4 - Crie um arquivo .sql na pasta Scripts com o mesmo nome da classe gerada no passo anterior.
	<dataHoraEidentificacaoDaMudancaNoBanco>.sql
5 - Execute o comando
	Update-Database -Script -SourceMigration <MigrationOrigem> -TargetMigration <MigrationDestino>
6 - Copie o script gerado e insira-o no arquivo gerado no passo 5.
7 - Quando a alteração for publicada em produção, execute os scripts gerados, em ordem.
	Para checar qual foi o último script gerado, faça uma consulta na tabela __MigrationHistory

### Explicações:
## 1 - Faça Build do projeto
		
## 2 - Abra o Package Manager Console
Caminho: Tools/Nuget Package Manager/Package Manager Console

## 3 - Execute o comando abaixo
add-migration <identificacao da mudanca no banco>
Ex:
add-migration inclusaoProfessores
add-migration alteracaoHorarios
add-migration criacaoViewAlunos

Esse comando produzirá uma classe dentro da pasta Migrations no seguinte padrão:
<data e hora><identificacao da mudanca no banco>.cs

Essa classe registra todas as alterações do banco de dados realizadas no domínio e determina a sequência de operações a serem executadas pelo Entity.
Caso necessário, apesar de incomum, é possível alterar o comportamento de algumas instruções nessa classe, personalizando algumas ações e alterando o comportamento padrão do Entity.

## 4 - Para efeito de rastreio, crie um arquivo .sql na pasta Scripts com o mesmo nome da classe gerada no passo anterior.
<data e hora><identificacao da mudanca no banco>.sql

## 5 - Identifique as seguintes classes da pasta Migrations:

<Classe_1> : A classe de Migrations anterior à alteração que você quer aplicar (sem a extensão.cs)
<Classe_2> : A classe de Migrations que você quer aplicar (no caso, a classe <data e hora><identificacao da mudanca no banco>.cs ) (sem a extensão.cs)

Update-Database -Script -SourceMigration <Classe_1> -TargetMigration <Classe_2>

Exemplo:
Update-Database -Script -SourceMigration: 201906122112332_agendamentosBase -TargetMigration: 201907111440070_criterioavaliativo

Esse comando gerará as instruções SQL baseadas nas alterações que você aplicou no domínio tendo como base a última atualização aplicada no banco de dados destino.
É importante usar o parâmetro "-Script" para que o Entity não aplique as mudanças diretamente no banco configurado no Web.config. Fazendo isso, conseguimos aplicar
as alterações no banco de produção mesmo sem ter acesso direto ao servidor do mesmo.

## 6 - Copie o script gerado e insira-o no arquivo gerado no passo 5.

## 7 - Quando a alteração for publicada em produção, execute os scripts gerados, em ordem.
Para checar qual foi o último script gerado, faça uma consulta na tabela __MigrationHistory

SELECT * FROM __MigrationHistory ORDER BY 1 DESC


