# Início Rápido: Implantar o aplicativo de API RESTful nos Aplicativos Spring do Azure

- Artigo
- 04/12/2024
- 4 colaboradores

Comentários

Selecionar um plano dos Aplicativos Spring do Azure

EmpresaConsumo padrão e dedicado (versão prévia)

Neste artigo[1. Pré-requisitos](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#1-prerequisites)[2. Preparar o projeto Spring](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#2-prepare-the-spring-project)[3. Preparar o ambiente de nuvem](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#3-prepare-the-cloud-environment)[4. Implantar o aplicativo em Aplicativos Spring do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#4-deploy-the-app-to-azure-spring-apps)Mostrar mais 3

 Observação

Os planos **Básico**, **Standard** e **Enterprise** serão preteridos a partir de meados de março de 2025, com um período de desativação de 3 anos. Recomendamos a transição para os Aplicativos de Contêiner do Azure. Para mais informações, confira o [anúncio de desativação dos Aplicativos Spring do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/basic-standard/retirement-announcement).

O plano **consumo e dedicado Standard** será preterido a partir de 30 de setembro de 2024, com um desligamento completo após seis meses. Recomendamos a transição para os Aplicativos de Contêiner do Azure. Para mais informações, confira [Migrar o plano Standard de consumo e dedicado dos Aplicativos Spring do Azure para os Aplicativos de Contêiner do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/consumption-dedicated/overview-migration).

Este artigo descreve como implantar um aplicativo de API RESTful protegido pelo [Microsoft Entra ID](https://learn.microsoft.com/pt-br/entra/fundamentals/whatis) nos Aplicativos Spring do Azure. O projeto de exemplo é uma versão simplificada com base no aplicativo Web [Simple Todo](https://github.com/Azure-Samples/ASA-Samples-Web-Application), que fornece apenas o serviço de back-end e usa o Microsoft Entra ID para proteger as APIs RESTful.

Essas APIs RESTful são protegidas pela aplicação do RBAC (controle de acesso baseado em função). Usuários anônimos não podem acessar dados e não têm permissão para controlar o acesso de usuários diferentes. Os usuários anônimos só têm as três permissões a seguir:

- Leitura: com essa permissão, um usuário pode ler os dados do ToDo.
- Gravação: com essa permissão, um usuário pode adicionar ou atualizar os dados do ToDo.
- Exclusão: com essa permissão, um usuário pode excluir os dados do ToDo.

Depois que a implantação for bem-sucedida, você poderá exibir e testar as APIs por meio da interface do usuário do Swagger.

[![Captura de tela da interface do usuário do Swagger que mostra o documento da API.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/swagger-ui.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/swagger-ui.png#lightbox)

O diagrama a seguir mostra a arquitetura do sistema:

[![Diagrama que mostra a arquitetura de um aplicativo Web Spring.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/diagram.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/diagram.png#lightbox)

Este artigo descreve as seguintes opções para criar recursos e implantá-los nos Aplicativos Spring do Azure:

- A opção do **portal do Azure + plug-in do Maven** fornece uma maneira mais convencional de criar recursos e implantar aplicativos passo a passo. Esta opção é adequada para os desenvolvedores do Spring que estão usando os serviços de nuvem do Azure pela primeira vez.
- A opção **CLI do Azure** é uma ferramenta de linha de comando poderosa para gerenciar recursos do Azure. Essa opção é adequada para os desenvolvedores do Spring que estão familiarizados com os serviços de nuvem do Azure.



## 1. Pré-requisitos

- [Portal do Azure + plug-in do Maven](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#tabpanel_1_Azure-portal-maven-plugin-ent)
- [CLI do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#tabpanel_1_Azure-CLI)

- Uma assinatura do Azure. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

- Uma das seguintes funções:

  - Administrador Global ou Administrador de Funções com Privilégios, para conceder o consentimento aos aplicativos que solicitam qualquer permissão em qualquer API.
  - Administrador de Aplicativos de Nuvem ou Administrador de Aplicativos, para fornecer consentimento aos aplicativos que solicitam qualquer permissão em qualquer API, exceto as funções de aplicativo do Microsoft Graph (permissões de aplicativo).
  - Uma função de diretório personalizada que inclui a [permissão para conceder permissões a aplicativos](https://learn.microsoft.com/pt-br/entra/identity/role-based-access-control/custom-consent-permissions), para as permissões exigidas pelo aplicativo.

  Para obter mais informações, consulte [Permitir consentimento de administrador em todo locatário para um aplicativo](https://learn.microsoft.com/pt-br/entra/identity/enterprise-apps/grant-admin-consent?pivots=portal).

- Se você estiver implantando a instância do plano Enterprise do Aplicativos Spring do Azure pela primeira vez na assinatura de destino, confira a seção [Requisitos](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/how-to-enterprise-marketplace-offer#requirements) do [plano Enterprise no Azure Marketplace](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/how-to-enterprise-marketplace-offer).

- [Git](https://git-scm.com/downloads).

- [Kit de Desenvolvimento Java (JDK)](https://learn.microsoft.com/pt-br/java/azure/jdk/), versão 17.

- Um locatário do Microsoft Entra. Para obter instruções sobre como criar um, consulte [Início Rápido: Criar um novo locatário no Microsoft Entra ID](https://learn.microsoft.com/pt-br/entra/fundamentals/create-new-tenant).



## 2. Preparar o projeto Spring

Para implantar o aplicativo de API RESTful, a primeira etapa é preparar o projeto Spring para ser executado localmente.

Use as etapas a seguir para clonar e executar o aplicativo localmente:

1. Use o seguinte comando para clonar o projeto de exemplo do GitHub:

   Bash

   ```bash
   git clone https://github.com/Azure-Samples/ASA-Samples-Restful-Application.git
   ```

2. Se você quiser executar o aplicativo localmente, conclua as etapas nas seções [Expor RESTful APIs](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#35-expose-restful-apis) e [Atualizar configuração do aplicativo](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#36-update-the-application-configuration) primeiro e use o seguinte comando para executar o aplicativo de exemplo com o Maven:

   Bash

   ```bash
   cd ASA-Samples-Restful-Application
   ./mvnw spring-boot:run
   ```



## 3. Preparar o ambiente de nuvem

Os principais recursos necessários para executar esse aplicativo de exemplo são uma instância dos Aplicativos Spring do Azure e uma instância do Banco de Dados do Azure para PostgreSQL. As seções a seguir descrevem como criar esses recursos.

- [Portal do Azure + plug-in do Maven](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#tabpanel_1_Azure-portal-maven-plugin-ent)
- [CLI do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#tabpanel_1_Azure-CLI)



### 3.1. Entre no Portal do Azure

Acesse o [portal do Azure](https://portal.azure.com/) e insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.



### 3.2. Criar uma instância do Azure Spring Apps

Use o seguinte comando para criar uma instância de serviço do Aplicativos Spring do Azure:

1. Selecione **Criar um recurso** no canto do portal do Azure.

2. Selecione **Computação**>**Aplicativos Spring do Azure**.

3. Preencha o formulário **Básico** com as seguintes informações:

   Expandir a tabela

   | Configuração                        | Valor sugerido                          | Descrição                                                    |
   | :---------------------------------- | :-------------------------------------- | :----------------------------------------------------------- |
   | **Assinatura**                      | O nome da sua assinatura.               | A assinatura do Azure que você deseja usar para o servidor. Caso você tenha várias assinaturas, escolha a assinatura na qual você deseja receber a cobrança do recurso. |
   | **Grupo de recursos**               | *myresourcegroup*                       | Um novo nome do grupo de recursos ou um existente de sua assinatura. |
   | **Nome**                            | *myasa*                                 | Um nome exclusivo que identifica o serviço Aplicativos Spring do Azure. O nome deve ter entre 4 e 32 caracteres e pode conter apenas letras minúsculas, números e hifens. O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número. |
   | **Plano**                           | **Empresa**                             | O plano de preços determina os recursos e o custo associados à sua instância. |
   | **Região**                          | A região mais próxima de seus usuários. | A localização mais próxima dos usuários.                     |
   | **Com Redundância de Zona**         | Não selecionado                         | A opção para criar o serviço do Aplicativos Spring do Azure em uma zona de disponibilidade do Azure. Atualmente, não há suporte para esse recurso em todas as regiões. |
   | **Plano IP de software**            | Pago conforme o uso                     | O plano de preços que permite pagar conforme o uso do Aplicativos Spring do Azure. |
   | **Terms**                           | Selecionadas                            | A caixa de seleção do contrato associada à [Oferta do Marketplace](https://aka.ms/ascmpoffer). Você precisa selecionar essa caixa de seleção. |
   | **Implantar um projeto de exemplo** | Não selecionado                         | A opção para usar o aplicativo de exemplo interno.           |

4. Selecione **Revisar e criar** para revisar suas seleções. Em seguida, selecione **Criar** para provisionar a instância dos Aplicativos Spring do Azure.

5. Na barra de ferramentas, selecione o ícone (sino) **Notificações** para monitorar o processo de implantação. Após a conclusão da implantação, selecione **Fixar no painel**, que cria um bloco para esse serviço no painel do portal do Azure como um atalho para a página de **Visão geral** do serviço.

   [![Captura de tela do portal do Azure que mostra o painel de Notificações para a criação do Aplicativos Spring do Azure.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/notifications.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/notifications.png#lightbox)

6. Selecione **Ir para o recurso** para ir para a página **Visão geral dos Aplicativos Spring do Azure**.



### 3.3. Preparar a instância do PostgreSQL

Use as etapas a seguir para criar um servidor do Banco de Dados do Azure para PostgreSQL:

1. Vá para o portal do Azure e selecione **Criar um recurso**.

2. Selecione **Bancos de Dados**>**Banco de Dados do Azure para PostgreSQL**.

3. Selecione a opção de implantação **Servidor flexível**.

   [![Captura de tela do portal do Azure que mostra a página da opção de implantação Selecionar Banco de Dados do Azure para PostgreSQL.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/postgresql-select-deployment-option.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/postgresql-select-deployment-option.png#lightbox)

4. Preencha a guia **Básico** com as seguintes informações:

   - **Nome do servidor**: *my-demo-pgsql*
   - **Região**: **Leste dos EUA**
   - **Versão do PostgreSQL**: *14*
   - **Tipo de carga de trabalho**: **Desenvolvimento**
   - **Habilitar alta disponibilidade**: não selecionado
   - **Método de autenticação**: **somente autenticação PostgreSQL**
   - **Nome de usuário do administrador**: *myadmin*
   - **Senha** e **Confirmar senha**: Insira uma senha.

5. Use as seguintes informações para configurar a guia **Rede**:

   - **Método de conectividade**: **Acesso público (endereços IP permitidos)**
   - **Permitir acesso público de qualquer serviço do Azure a este servidor**: selecionado

6. Selecione **Examinar + criar** para examinar suas seleções e, em seguida, selecione **Criar** para provisionar o servidor. Essa operação poderá levar alguns minutos.

7. Acesse o servidor PostgreSQL no portal do Azure. Na página **Visão geral**, procure o valor do **nome do servidor** e registre-o para uso posterior. Você precisa dele para configurar as variáveis de ambiente para o aplicativo nos Aplicativos Spring do Azure.

8. Selecione **Bancos de dados** no menu de navegação para criar um banco de dados, por exemplo, *todo*.

   [![Captura de tela do portal do Azure que mostra a página Bancos de Dados com o painel Criar Banco de Dados aberto.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/postgresql-create-database.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/postgresql-create-database.png#lightbox)



### 3.4. Conectar a instância de aplicativo à instância do PostgreSQL

Use as etapas a seguir para conectar as instâncias de serviço:

1. Vá para a sua instância do Azure Spring Apps no portal do Azure.

2. No menu de navegação, abra **Aplicativos** e selecione **Criar Aplicativo**.

3. Na página **Criar aplicativo**, preencha o nome do aplicativo como *simple-todo-api* e selecione **Artefatos Java** como o tipo de implantação.

4. Selecione **Criar** para concluir a criação do aplicativo e selecione o aplicativo para exibir os detalhes.

5. Acesse o aplicativo que você criou no portal do Azure. Na página **Visão geral**, selecione **Atribuir ponto de extremidade** para expor o ponto de extremidade público do aplicativo. Salve a URL para acessar o aplicativo após a implantação.

6. Selecione **Conector de serviço** no painel de navegação e selecione **Criar** para criar uma nova conexão de serviço.

   [![Captura de tela do portal do Azure que mostra a página Conector de Serviço do plano Enterprise com o botão Criar realçado.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/restful-api-app-service-connector-enterprise.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/restful-api-app-service-connector-enterprise.png#lightbox)

7. Preencha a guia **Básico** com as seguintes informações:

   - **Tipo de serviço**: **servidor flexível do BD para PostgreSQL**
   - **Nome da conexão**: um nome gerado automaticamente é preenchido e ele também pode ser modificado.
   - **Assinatura**: Selecione sua assinatura.
   - **Servidor flexível do PostgreSQL**: *my-demo-pgsql*
   - **Banco de dados PostgreSQL**: selecione o banco de dados criado por você.
   - **Tipo de cliente**: **SpringBoot**

   [![Captura de tela do portal do Azure que mostra a guia Básico do painel Criar conexão para conexão com o Barramento de Serviço.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/app-service-connector-basics.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/app-service-connector-basics.png#lightbox)

8. Configure a guia **Próximo: autenticação** com as seguintes informações:

    Observação

   A Microsoft recomenda usar o fluxo de autenticação mais seguro disponível. O fluxo de autenticação descrito nesse procedimento, como para bancos de dados, caches, mensagens ou serviços de IA, exige um grau muito alto de confiança no aplicativo e traz riscos não presentes em outros fluxos. Use esse fluxo somente quando opções mais seguras, como identidades gerenciadas para conexões sem senha ou sem chave, não forem viáveis. Para operações de máquinas locais, prefira identidades de usuário para conexões sem senha ou sem chave.

   - **Selecione o tipo de autenticação que você gostaria de usar entre o serviço de computação e o serviço de destino**: selecione **Cadeia de conexão**.
   - **Continuar com...**: selecione **Credenciais do banco de dados**
   - **Nome de usuário**: *myadmin*
   - **Senha**: insira a senha.

   [![Captura de tela do portal do Azure que mostra a guia Autenticação do painel Criar conexão com a opção Cadeia de conexão realçada.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/app-service-connector-authentication.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/app-service-connector-authentication.png#lightbox)

9. Selecione **Avançar: Rede**. Use a opção padrão **Configurar regras de firewall para habilitar o acesso ao serviço de destino**.

10. Selecione **Próximo: examinar e criar** para examinar suas seleções e, em seguida, selecione **Criar** para criar a conexão.



### 3.5. Expor APIs RESTful

Use as seguintes etapas para expor suas APIs RESTful no Microsoft Entra ID:

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Se você tiver acesso a vários locatários, use o filtro **Diretório + assinatura** (![img](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/portal-directory-subscription-filter.png)) para selecionar o locatário no qual quer registrar um aplicativo.

3. Pesquise e selecione **Microsoft Entra ID**.

4. Em **Gerenciar**, selecione **Registros de aplicativo**>**Novo registro**.

5. Insira um nome para seu aplicativo no campo **Nome**, por exemplo, *Todo*. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.

6. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório da organização (qualquer diretório do Microsoft Entra — com vários locatários) e contas Microsoft pessoais**.

7. Selecione **Registrar** para criar o aplicativo.

8. Na página **Visão Geral** do aplicativo, localize o valor da **ID do aplicativo (cliente)** e registre-o para uso posterior. Você precisa dele para configurar o arquivo de configuração YAML para este projeto.

9. Em **Gerenciar**, selecione **Expor uma API**, localize o **URI de ID do aplicativo** no início da página e selecione **Adicionar**.

10. Na página **Editar URI do ID do aplicativo**, aceite o URI do ID do aplicativo proposto (`api://{client ID}`) ou use um nome significativo em vez do ID do cliente, como `api://simple-todo`, e selecione **Salvar**.

11. Em **Gerenciar**, selecione **Expor uma API**>**Adicionar um escopo** e, em seguida, insira as seguintes informações:

    - Para o **nome do escopo**, insira *ToDo.Read*.
    - Para **Quem pode consentir?**: selecione **somente administradores**.
    - Para o **nome de exibição de consentimento do administrador**, insira *Ler os dados de ToDo*.
    - Para **descrição de consentimento do administrador**, insira *Permitir que os usuários autenticados leiam os dados do ToDo.*.
    - Para **Estado**, mantenha-o habilitado.
    - Selecione **Adicionar escopo**.

12. Repita as etapas anteriores para adicionar os dois outros escopos: *ToDo.Write* e *ToDo.Delete*.

    [![Captura de tela do portal do Azure que mostra a página Expor uma API de um aplicativo API RESTful.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/expose-an-api.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/expose-an-api.png#lightbox)



### 3.6. Atualizar a configuração do aplicativo

Use as seguintes etapas para atualizar o arquivo YAML para usar suas informações de aplicativo registrado do Microsoft Entra e estabelecer uma relação com o aplicativo de API RESTful:

1. Localize o arquivo *src/main/resources/application.yml* para o aplicativo `simple-todo-api`. Atualize a configuração na seção `spring.cloud.azure.active-directory` para corresponder ao exemplo a seguir. Substitua os espaços reservados pelos valores criados anteriormente.

   YAML

   ```yaml
   spring:
     cloud:
       azure:
         active-directory:
           profile:
             tenant-id: <tenant>
           credential:
             client-id: <your-application-ID-of-ToDo>
           app-id-uri: <your-application-ID-URI-of-ToDo>
   ```

    Observação

   Em tokens v1.0, a configuração requer a ID do cliente da API, enquanto em tokens v2.0, você pode usar a ID do cliente ou o URI da ID do aplicativo na solicitação. Você pode configurar ambos para concluir corretamente a validação do público-alvo.

   Os valores permitidos para `tenant-id` são: `common`, `organizations`, `consumers` ou a ID do locatário. Para obter mais informações sobre esses valores, consulte a seção [Uso do ponto de extremidade incorreto (contas pessoais e de organização)](https://learn.microsoft.com/pt-br/troubleshoot/azure/active-directory/error-code-aadsts50020-user-account-identity-provider-does-not-exist#cause-3-used-the-wrong-endpoint-personal-and-organization-accounts) do [Error AADSTS50020 – A conta de usuário do provedor de identidade não existe no locatário](https://learn.microsoft.com/pt-br/troubleshoot/azure/active-directory/error-code-aadsts50020-user-account-identity-provider-does-not-exist). Para saber como converter seu aplicativo de locatário único, consulte [Converter aplicativo de locatário único em multilocatário no Microsoft Entra ID](https://learn.microsoft.com/pt-br/entra/identity-platform/howto-convert-app-to-be-multi-tenant).

2. Use o comando a seguir para recompilar o projeto de exemplo:

   Bash

   ```bash
   ./mvnw clean package
   ```



## 4. Implantar o aplicativo em Aplicativos Spring do Azure

- [Portal do Azure + plug-in do Maven](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#tabpanel_2_Azure-portal-maven-plugin-ent)
- [CLI do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#tabpanel_2_Azure-CLI)

Agora você pode implantar o aplicativo em Aplicativos Spring do Azure.

Use as etapas a seguir para implantar usando o [plug-in Maven para Aplicativos Spring do Azure](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Spring-Apps):

1. Navegue até o diretório *completo* e execute o seguinte comando para configurar o aplicativo em Aplicativos Spring do Azure:

   Bash

   ```bash
   ./mvnw com.microsoft.azure:azure-spring-apps-maven-plugin:1.19.0:config
   ```

   A lista a seguir descreve as interações de comando:

   - **Logon do OAuth2**: autorize a entrada no Azure com base no protocolo OAuth2.
   - **Selecionar assinatura:** selecione o número da lista de assinaturas da instância dos Aplicativos Spring do Azure que você criou, que usa como padrão a primeira assinatura na lista. Se você usar o número padrão, pressione Enter diretamente.
   - **Usar o Aplicativos Spring do Azure existente**: pressione S para usar a instância existente do Aplicativos Spring do Azure.
   - **Selecionar os Aplicativos Spring do Azure para implantação**: selecione o número da instância dos Aplicativos Spring do Azure que você criou. Se você usar o número padrão, pressione Enter diretamente.
   - **Use o aplicativo existente no Aplicativos Spring do Azure <nome da sua instância>**: pressione S para usar o aplicativo criado.
   - **Confirme para salvar todas as configurações acima**: pressione S. Se você pressionar n, a configuração não será salva nos arquivos POM.

2. Use o seguinte comando para implantar o aplicativo:

   Bash

   ```bash
   ./mvnw azure-spring-apps:deploy
   ```

   A lista a seguir descreve a interação de comando:

   - **Logon do OAuth2**: autorize a entrada no Azure com base no protocolo OAuth2.

   Depois que o comando for executado, você poderá pelas mensagens de log que a implantação foi bem-sucedida:

Saída

```output
[INFO] Deployment Status: Running
[INFO]   InstanceName:simple-todo-api-default-15-xxxxxxxxx-xxxxx  Status:Running Reason:null       DiscoverStatus:N/A       
[INFO] Getting public url of app(simple-todo-api)...
[INFO] Application url: https://<your-Azure-Spring-Apps-instance-name>-simple-todo-api.azuremicroservices.io
```



## 5. Validar os aplicativos

- [Portal do Azure + plug-in do Maven](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#tabpanel_3_Azure-portal-maven-plugin-ent)
- [CLI do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#tabpanel_3_Azure-CLI)

Agora, você pode acessar a API RESTful para conferir se ela funciona.



### 5.1. Solicitar um token de acesso

As APIs RESTful atuam como um servidor de recursos, que é protegido pelo Microsoft Entra ID. Antes de adquirir um token de acesso, você deverá registrar outro aplicativo no Microsoft Entra ID e conceder permissões ao aplicativo cliente, que é chamado `ToDoWeb`.



#### Registrar o aplicativo cliente

Use as seguintes etapas para registrar um aplicativo no Microsoft Entra ID, que é usado para adicionar as permissões para o aplicativo `ToDo`:

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Se você tiver acesso a vários locatários, use o filtro **Diretório + assinatura** (![img](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/portal-directory-subscription-filter.png)) para selecionar o locatário no qual quer registrar um aplicativo.

3. Pesquise e selecione **Microsoft Entra ID**.

4. Em **Gerenciar**, selecione **Registros de aplicativo**>**Novo registro**.

5. Insira um nome para seu aplicativo no campo **Nome** – por exemplo, *ToDoWeb.* Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.

6. Para **tipos de conta com suporte**, use o valor padrão **Contas somente neste diretório organizacional**.

7. Selecione **Registrar** para criar o aplicativo.

8. Na página **Visão Geral** do aplicativo, localize o valor da **ID do aplicativo (cliente)** e registre-o para uso posterior. Isso será necessário para adquirir um token de acesso.

9. Selecione **Permissões de API**>**Adicionar uma permissão**>**Minhas APIs**. Selecione o aplicativo `ToDo` que você registrou anteriormente e, em seguida, selecione as permissões **ToDo.Read**, **ToDo.Write**e **ToDo.Delete**. Selecione **Adicionar permissões**.

10. Selecione **Conceder consentimento do administrador< para >your-tenant-name** para conceder o consentimento do administrador para as permissões adicionadas.

    [![Captura de tela do portal do Azure que mostra as permissões de API de um aplicativo Web.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/api-permissions.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/api-permissions.png#lightbox)



#### Adicionar usuário para acessar as APIs RESTful

Use as etapas a seguir para criar um usuário membro em seu locatário do Microsoft Entra. Em seguida, o usuário pode gerenciar os dados do aplicativo por meio de APIs RESTful `ToDo`.

1. Em **Gerenciar**, selecione **Usuários**>**Novo usuário>****Criar novo usuário**.

2. Na página **Criar novo usuário**, insira as seguintes informações:

   - **Nome da entidade de usuário**: insira um nome para o usuário.
   - **Nome de exibição**: insira um nome de exibição para o usuário.
   - **Senha**: copie a senha gerada automaticamente fornecida na caixa **Senha**.

    Observação

   Os novos usuários devem concluir a primeira autenticação de entrada e atualizar suas senhas, caso contrário, você receberá um erro `AADSTS50055: The password is expired` ao obter o token de acesso.

   Quando um novo usuário faz logon, ele recebe um prompt de **Ação Necessária**. Eles podem escolher **Pedir mais tarde** para ignorar a validação.

3. Selecione **Revisar + criar** para revisar suas seleções. Selecione **Criar** para criar o usuário.



#### Atualizar a configuração do OAuth2 para autorização da interface do usuário do Swagger

Use as etapas a seguir para atualizar a configuração do OAuth2 para autorização da interface do usuário do Swagger. Em seguida, você pode autorizar os usuários a adquirir tokens de acesso por meio do aplicativo `ToDoWeb`.

1. Abra o locatário do **Microsoft Entra ID** no portal do Azure e acesse o aplicativo registrado `ToDoWeb`.

2. Em **Gerenciar**, selecione **Autenticação**, Selecione **Adicionar uma plataforma** e selecione **Aplicativo de página única**.

3. Use o formato `<your-app-exposed-application-URL-or-endpoint>/swagger-ui/oauth2-redirect.html` como a URL de redirecionamento OAuth2 no campo **Redirecionamento de URIs** e selecione **Configurar**.

   [![Captura de tela do portal do Azure que mostra a página Autenticação do Microsoft Entra ID.](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/single-page-app-authentication.png)](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/media/quickstart-deploy-restful-api-app/single-page-app-authentication.png#lightbox)



#### Obter o token de acesso

Use as etapas a seguir para usar o método de [fluxo de código de autorização do OAuth 2.0](https://learn.microsoft.com/pt-br/entra/identity-platform/v2-oauth2-auth-code-flow) para obter um token de acesso com o Microsoft Entra ID e, em seguida, acessar as APIs RESTful do aplicativo `ToDo`:

1. Abra a URL exposta pelo aplicativo e selecione **Autorizar** para preparar a autenticação OAuth2.
2. Na janela **Autorizações disponíveis**, insira a ID do cliente do aplicativo `ToDoWeb` no campo **client_id**, selecione todos os escopos do campo **Escopos**, ignore o campo **client_secret** e selecione **Autorizar** para redirecionar para a página de entrada do Microsoft Entra.

Depois de concluir a entrada com o usuário anterior, você será retornado para a janela **Autorizações disponíveis**.



### 5.2. Acessar as APIs RESTful

Use as seguintes etapas para acessar as APIs RESTful do aplicativo `ToDo` na interface do usuário do Swagger:

1. Selecione a API **POST /api/simple-todo/lists** e, em seguida, selecione **Experimentar**. Insira o corpo da solicitação a seguir e selecione **Executar** para criar uma lista ToDo.

   JSON

   ```json
   {
     "name": "My List"
   }
   ```

   Depois que a execução for concluída, você verá o seguinte **Corpo da resposta**:

   JSON

   ```json
   {
     "id": "<ID-of-the-ToDo-list>",
     "name": "My List",
     "description": null
   }
   ```

2. Selecione a API **POST /api/simple-todo/lists/{listId}/items** e selecione **Experimentar**. Para **listId**, insira a ID da lista ToDo que você criou anteriormente, insira o corpo da solicitação a seguir e selecione **Executar** para criar um item ToDo.

   JSON

   ```json
   {
     "name": "My first ToDo item", 
     "listId": "<ID-of-the-ToDo-list>",
     "state": "todo"
   }
   ```

   Esta ação retorna o seguinte item ToDo:

   JSON

   ```json
   {
     "id": "<ID-of-the-ToDo-item>",
     "listId": "<ID-of-the-ToDo-list>",
     "name": "My first ToDo item",
     "description": null,
     "state": "todo",
     "dueDate": "2023-07-11T13:59:24.9033069+08:00",
     "completedDate": null
   }
   ```

3. Selecione a API **GET /api/simple-todo/lists** e, em seguida, selecione **Executar** para consultar listas de ToDo. Esta ação retorna as seguintes listas de ToDo:

   JSON

   ```json
   [
     {
       "id": "<ID-of-the-ToDo-list>",
       "name": "My List",
       "description": null
     }
   ]
   ```

4. Selecione a API **GET /api/simple-todo/lists/{listId}/items** e selecione **Experimentar**. Para **listId**, insira a ID da lista ToDo que você criou anteriormente e selecione **Executar** para consultar os itens ToDo. Esta ação retorna o seguinte item ToDo:

   JSON

   ```json
   [
     {
       "id": "<ID-of-the-ToDo-item>",
       "listId": "<ID-of-the-ToDo-list>",
       "name": "My first ToDo item",
       "description": null,
       "state": "todo",
       "dueDate": "2023-07-11T13:59:24.903307+08:00",
       "completedDate": null
     }
   ]
   ```

5. Selecione a API **PUT /api/simple-todo/lists/{listId}/items/{itemId}** e selecione **Experimentar**. Para **listId**, insira a ID da lista ToDo. Para **itemId**, insira a ID do item ToDo, insira o corpo da solicitação a seguir e selecione **Executar** para atualizar o item ToDo.

   JSON

   ```json
   {
     "id": "<ID-of-the-ToDo-item>",
     "listId": "<ID-of-the-ToDo-list>",
     "name": "My first ToDo item",
     "description": "Updated description.",
     "dueDate": "2023-07-11T13:59:24.903307+08:00",
     "state": "inprogress"
   }
   ```

   Esta ação retorna o seguinte item ToDo atualizado:

   JSON

   ```json
   {
     "id": "<ID-of-the-ToDo-item>",
     "listId": "<ID-of-the-ToDo-list>",
     "name": "My first ToDo item",
     "description": "Updated description.",
     "state": "inprogress",
     "dueDate": "2023-07-11T05:59:24.903307Z",
     "completedDate": null
   }
   ```

6. Selecione a API **DELETE /api/simple-todo/lists/{listId}/items/{itemId}** e selecione **Experimentar**. Para **listId**, insira a ID da lista ToDo. Para **itemId**, insira a ID do item ToDo e, em seguida, selecione **Executar** para excluir o item ToDo. Você deve ver que o código de resposta do servidor é `204`.



## 6. Limpar os recursos

Você pode excluir o grupo de recursos do Azure, que inclui todos os recursos no grupo de recursos.

- [Portal do Azure + plug-in do Maven](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#tabpanel_1_Azure-portal-maven-plugin-ent)
- [CLI do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/enterprise/quickstart-deploy-restful-api-app?tabs=Azure-portal-maven-plugin-ent%2CAzure-portal-maven-plugin&pivots=sc-enterprise#tabpanel_1_Azure-CLI)

Use as seguintes etapas para excluir todo o grupo de recursos, incluindo o serviço recém-criado:

1. Encontre o grupo de recursos no portal do Azure.
2. No menu de navegação, selecione **Grupos de recursos**. Em seguida, selecione o nome do grupo de recursos, por exemplo, **myresourcegroup**.
3. Na página do grupo de recursos, selecione **Excluir**. Insira o nome do grupo de recursos, como o exemplo *myresourcegroup*, na caixa de texto para confirmar a exclusão. Em seguida, selecione **Excluir**.



## 7. Próximas etapas

[Início Rápido: Implantar um aplicativo controlado por evento nos Aplicativos Spring do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/basic-standard/quickstart-deploy-event-driven-app?toc=/azure/spring-apps/enterprise/toc.json&bc=/azure/spring-apps/enterprise/breadcrumb/toc.json)

[Início Rápido: Como implantar aplicativos de microsserviço nos Aplicativos Spring do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/basic-standard/quickstart-deploy-microservice-apps?toc=/azure/spring-apps/enterprise/toc.json&bc=/azure/spring-apps/enterprise/breadcrumb/toc.json)

[Log do aplicativo estruturado para o Azure Spring Apps](https://learn.microsoft.com/pt-br/azure/spring-apps/basic-standard/structured-app-log?toc=/azure/spring-apps/enterprise/toc.json&bc=/azure/spring-apps/enterprise/breadcrumb/toc.json)

[Mapear um domínio personalizado existente para os Aplicativos Spring do Azure](https://learn.microsoft.com/pt-br/azure/spring-apps/basic-standard/how-to-custom-domain?toc=/azure/spring-apps/enterprise/toc.json&bc=/azure/spring-apps/enterprise/breadcrumb/toc.json)

[Usa a CI/CD do Azure Spring Apps com o GitHub Actions](https://learn.microsoft.com/pt-br/azure/spring-apps/basic-standard/how-to-github-actions?toc=/azure/spring-apps/enterprise/toc.json&bc=/azure/spring-apps/enterprise/breadcrumb/toc.json)

[Automatizar implantações de aplicativos no Azure Spring Apps](https://learn.microsoft.com/pt-br/azure/spring-apps/basic-standard/how-to-cicd?toc=/azure/spring-apps/enterprise/toc.json&bc=/azure/spring-apps/enterprise/breadcrumb/toc.json)

Para obter mais informações, consulte os seguintes artigos:

- [Exemplos de Aplicativos Spring do Azure ](https://github.com/Azure-Samples/azure-spring-apps-samples).
- [Spring no Azure](https://learn.microsoft.com/pt-br/azure/developer/java/spring/)
- [Azure Spring Cloud](https://learn.microsoft.com/pt-br/azure/developer/java/spring-framework/)

------

## Comentários

Esta página foi útil?

YesNo

[Fornecer comentários sobre o produto ](https://feedback.azure.com/d365community/forum/79b1327d-d925-ec11-b6e6-000d3a4f06a4)|

[Obter ajuda no Microsoft Q&A](https://learn.microsoft.com/answers/tags/424/azure-spring-apps/)