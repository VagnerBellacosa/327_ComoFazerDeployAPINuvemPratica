# Publicar uma API Web do ASP.NET Core no Gerenciamento de API do Azure com o Visual Studio

- Artigo
- 06/11/2024
- 7 colaboradores

Comentários

Neste artigo[Configuração](https://learn.microsoft.com/pt-br/aspnet/core/tutorials/publish-to-azure-api-management-using-vs?view=aspnetcore-9.0#set-up)[Criar uma API Web do ASP.NET Core](https://learn.microsoft.com/pt-br/aspnet/core/tutorials/publish-to-azure-api-management-using-vs?view=aspnetcore-9.0#create-an-aspnet-core-web-api)[Explore o código](https://learn.microsoft.com/pt-br/aspnet/core/tutorials/publish-to-azure-api-management-using-vs?view=aspnetcore-9.0#explore-the-code)[Publicar a API Web no Serviço de Aplicativo do Azure](https://learn.microsoft.com/pt-br/aspnet/core/tutorials/publish-to-azure-api-management-using-vs?view=aspnetcore-9.0#publish-the-web-api-to-azure-app-service)Mostrar mais 2

Por [Matt Soucoup](https://twitter.com/codemillmatt)

Neste tutorial, você aprenderá a criar um projeto de API Web do ASP.NET Core usando o Visual Studio, garantir que ele tenha suporte a OpenAPI e, em seguida, publicar a API Web no Serviço de Aplicativo do Azure e no Gerenciamento de API do Azure.



## Configuração

Para concluir o tutorial, você precisará de uma conta do Azure.

- Abra uma [conta do Azure gratuita](https://azure.microsoft.com/free/dotnet/) se você não tiver uma.



## Criar uma API Web do ASP.NET Core

O Visual Studio permite que você crie facilmente um novo projeto de API Web do ASP.NET Core a partir de um modelo. Siga estas instruções para criar um novo projeto de API Web do ASP.NET Core:

- No menu Arquivo, selecione **Novo**>**Projeto**.
- Insira **API Web** na caixa de pesquisa.
- Selecione o modelo da **API Web do ASP.NET Core** e **Avançar**.
- Na caixa de diálogo **Configurar o novo projeto**, nomeie o projeto **WeatherAPI** e selecione **Avançar**.
- Na caixa de diálogo **Informações adicionais**:
- Confirme se o Framework é o **.NET 6.0 (suporte de longo prazo)**.
- Confirme se a caixa de seleção para **Usar controladores (desmarcar para usar APIs mínimas)** está marcada.
- Confirme se a caixa de seleção **Habilitar suporte a OpenAPI** está marcada.
- Selecione **Criar**.



## Explore o código

As definições do Swagger permitem que Gerenciamento de API do Azure leiam as definições de API do aplicativo. Ao marcar a caixa de seleção **Habilitar suporte ao OpenAPI** durante a criação do aplicativo, o Visual Studio adiciona automaticamente o código para criar as definições do Swagger. Abra o arquivo `Program.cs` que exibe o seguinte código:

C#

```csharp
...

builder.Services.AddSwaggerGen();

...

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI(); // Protected by if (env.IsDevelopment())
}

...
```



### Verifique se as definições do Swagger são sempre geradas

O Gerenciamento de API do Azure precisa que as definições do Swagger estejam sempre presentes, independentemente do ambiente do aplicativo. Para garantir que eles sejam sempre gerados, tire `app.UseSwagger();` do bloco `if (app.Environment.IsDevelopment())`.

O código atualizado:

C#

```csharp
...

app.UseSwagger();

if (app.Environment.IsDevelopment())
{
    app.UseSwaggerUI();
}

...
```



### Alterar o roteamento de API

Altere a estrutura de URL necessária para acessar a ação `Get` do `WeatherForecastController`. Conclua as seguintes etapas:

1. Abra o arquivo `WeatherForecastController.cs` .

2. Substitua o atributo de nível de classe `[Route("[controller]")]` por `[Route("/")]`. A definição de classe atualizada:

   C#

   ```csharp
   [ApiController]
   [Route("/")]
   public class WeatherForecastController : ControllerBase
   ```



## Publicar a API Web no Serviço de Aplicativo do Azure

Conclua as seguintes etapas para publicar a API Web ASP.NET Core no Gerenciamento de API do Azure:

1. Publique o aplicativo de API no Serviço de Aplicativo do Azure.
2. Publique o aplicativo de API Web do ASP.NET Core na instância do serviço Gerenciamento de API do Azure.



### Publicar o aplicativo de API no Serviço de Aplicativo do Azure

Conclua as seguintes etapas para publicar a API Web ASP.NET Core no Gerenciamento de API do Azure:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**.

2. Na caixa de diálogo **Publicar**, selecione **Azure** e selecione o botão **Avançar**.

3. Selecione **Serviço de Aplicativo do Azure (Windows)** e escolha o botão **Avançar**.

4. Selecione **Criar um Serviço de Aplicativo do Azure**.

   A caixa de diálogo **Criar Serviço de Aplicativo** é exibida. Os campos de entrada **Nome do Aplicativo**, **Grupo de Recursos** e **Plano do Serviço de Aplicativo** serão populados. Você pode manter esses nomes ou alterá-los.

5. Selecione o botão **Criar**.

6. Depois que o serviço de aplicativo for criado, selecione o botão **Avançar**.

7. Selecione **Criar um serviço de Gerenciamento de API**.

   A caixa de diálogo **Criar Serviço de Gerenciamento de API** será exibida. Não é necessário alterar os campos de entrada **Nome da API**, **Nome da Assinatura** e **Grupo de Recursos**. Selecione o **novo** botão ao lado da entrada **Serviço de Gerenciamento de API** e insira os campos necessários nessa caixa de diálogo.

   Selecione o botão **OK** para criar o serviço de Gerenciamento de API.

8. Selecione o botão **Criar** para prosseguir com a criação do serviço de Gerenciamento de API. Esse passo pode levar vários minutos para ser concluído.

9. Quando for concluído, selecione o botão **Concluir**.

10. A caixa de diálogo é fechada e uma tela de resumo é exibida com informações sobre a publicação. Clique no botão **Publicar**.

    A API Web publica no Serviço de Aplicativo do Azure e no Gerenciamento de API do Azure. Uma nova janela do navegador será exibida e mostrará a API em execução no Serviço de Aplicativo do Azure. Você pode fechar essa janela.

11. Abra o portal do Azure em um navegador da Web e navegue até a instância de Gerenciamento de API que você criou.

12. Selecione a opção **APIs** no menu à esquerda.

13. Selecione a API criada na seção anterior. Agora ela está preenchida e você pode explorar.



### Configurar o nome da API publicada

Observe que o nome da API é *WeatherAPI*; no entanto, gostaríamos de chamá-lo de *Previsões meteorológicas*. Conclua as etapas a seguir para atualizar o nome:

1. Adicione o seguinte a `Program.cs` logo após `servies.AddSwaggerGen();`

   C#

   ```csharp
   builder.Services.ConfigureSwaggerGen(setup =>
   {
       setup.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo
       {
           Title = "Weather Forecasts",
           Version = "v1"
       });
   });
   ```

2. Publique novamente a API Web do ASP.NET Core e abra a instância de Gerenciamento de API do Azure no portal do Azure.

3. Atualize a página no navegador. Você verá que o nome da API agora está correto.



### Verifique se a API Web está funcionando

Você pode testar a API Web do ASP.NET Core implantada no Gerenciamento de API do Azure no portal do Azure com as seguintes etapas:

1. Abra a guia **Testar**.
2. Selecione **/** ou a operação **Get**.
3. Selecione **Enviar**.



## Limpeza

Quando você concluir o teste do aplicativo, acesse o [portal do Azure](https://portal.azure.com/) e exclua o aplicativo.

1. Selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos criado.
2. Na página **Grupos de recursos**, selecione **Excluir**.
3. Insira o nome do grupo de recursos e selecione **Excluir**. O aplicativo e todos os outros recursos criados neste tutorial agora foram excluídos do Azure.



## Recursos adicionais

- [Gerenciamento de API do Azure](https://learn.microsoft.com/pt-br/azure/api-management/api-management-key-concepts)
- [Serviço de Aplicativo do Azure](https://learn.microsoft.com/pt-br/azure/app-service/app-service-web-overview)

 Colaborar conosco no GitHub

A fonte deste conteúdo pode ser encontrada no GitHub, onde você também pode criar e revisar problemas e solicitações de pull. Para obter mais informações, confira o [nosso guia para colaboradores](https://learn.microsoft.com/contribute/content/dotnet/dotnet-contribute).

![img](https://learn.microsoft.com/media/logos/logo_net.svg)

Comentários do ASP.NET Core

O ASP.NET Core é um projeto código aberto. Selecione um link para fornecer comentários:

[ Abrir um problema de documentação](https://github.com/dotnet/aspnetcore.docs/issues/new?template=customer-feedback.yml&pageUrl=https%3A%2F%2Flearn.microsoft.com%2Fpt-br%2Faspnet%2Fcore%2Ftutorials%2Fpublish-to-azure-api-management-using-vs%3Fview%3Daspnetcore-9.0&pageQueryParams=%3Fview%3Daspnetcore-9.0&contentSourceUrl=https%3A%2F%2Fgithub.com%2Fdotnet%2FAspNetCore.Docs%2Fblob%2Fmain%2Faspnetcore%2Ftutorials%2Fpublish-to-azure-api-management-using-vs.md&documentVersionIndependentId=19c98d91-e5fe-5832-6c75-23e677e469a3&feedback= [Insira+comentários+aqui] &author=@codemillmatt&metadata=*+ID%3A+d70145c2-e2ba-77fb-050d-44d18354e370+ *+Service%3A+**aspnet-core** *+Sub-service%3A+**tutorials**&labels=Source+-+Docs.ms%2C%3Awatch%3A+Not+Triaged)[
  ](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md)