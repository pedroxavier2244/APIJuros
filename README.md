Arquitetura do Projeto

A solu��o � dividida em quatro camadas, seguindo a regra de depend�ncia onde as camadas externas apontam para as internas.

ApiJuros.Domain: A camada mais interna. Cont�m as entidades e a l�gica de neg�cio mais pura, sem depend�ncias externas. (Neste projeto simples, ela est� vazia, mas serve como base para futuras regras de neg�cio complexas).

ApiJuros.Application: Cont�m a l�gica da aplica��o (casos de uso). Define interfaces que s�o implementadas pelas camadas externas e utiliza os DTOs (Data Transfer Objects) para trafegar dados.

ApiJuros.Infrastructure: Implementa as interfaces definidas na camada de Application. � respons�vel por detalhes t�cnicos como acesso a banco de dados, consumo de outras APIs, etc. (Neste projeto, a implementa��o do servi�o est� na camada de Application por simplicidade, mas em um caso real, ela estaria aqui).

ApiJuros.Presentation: A camada de entrada da aplica��o. No nosso caso, � uma API Web ASP.NET Core que exp�e os endpoints para o mundo externo e depende da camada de Application para executar as a��es.


ApiJuros.Presentation -> ApiJuros.Infrastructure -> ApiJuros.Application -> ApiJuros.Domain


Tecnologias Utilizadas

.NET 8 (ou superior)

ASP.NET Core: Para a constru��o da API.

Swagger (Swashbuckle): Para documenta��o interativa da API.

FluentValidation: Para valida��o declarativa e robusta dos dados de entrada.


# Instale as depend�ncias necess�rias para valida��o e documenta��o.

# Adicione o FluentValidation ao projeto Application
dotnet add ApiJuros.Application/ApiJuros.Application.csproj package FluentValidation

# Adicione o Swagger e a integra��o do FluentValidation ao projeto Presentation
dotnet add ApiJuros.Presentation/ApiJuros.Presentation.csproj package Swashbuckle.AspNetCore
dotnet add ApiJuros.Presentation/ApiJuros.Presentation.csproj package FluentValidation.AspNetCore

Executar a Aplica��o
Antes de executar pela primeira vez, � crucial confiar no certificado de desenvolvimento local para usar HTTPS.

#  Navegue para a pasta da API
cd ApiJuros.Presentation

#  Execute a aplica��o
dotnet run

O terminal mostrar� que o servidor est� rodando, geralmente em https://localhost:7xxx e http://localhost:5xxx.


Como Usar a API
Ap�s executar a aplica��o, abra seu navegador e acesse a documenta��o interativa do Swagger.

URL do Swagger: https://localhost:7255/swagger (a porta pode variar).

Endpoint de C�lculo
POST /api/FinancialCalculator/calculate-compound-interest

Calcula o valor final e os juros totais de um investimento com base em um valor inicial, uma taxa de juros mensal e um per�odo em meses.

Exemplo de Sucesso
Corpo da Requisi��o (Request Body):

{
  "initialValue": 1000,
  "monthlyInterestRate": 1.5,
  "timeInMonths": 24
}

Resposta de Sucesso (C�digo 200 OK):

{
  "investedAmount": 1000,
  "finalAmount": 1429.50,
  "totalInterest": 429.50,
  "timeInMonths": 24
}

Se voc� enviar dados inv�lidos (ex: valores negativos), a API retornar� um erro 400 Bad Request com uma mensagem clara.

Corpo da Requisi��o (Inv�lido):

{
  "initialValue": -100,
  "monthlyInterestRate": 1,
  "timeInMonths": 12
}

Resposta de Erro (C�digo 400 Bad Request):

{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "InitialValue": [
      "O valor inicial do investimento deve ser positivo."
    ]
  }
}