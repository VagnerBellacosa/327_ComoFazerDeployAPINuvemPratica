# Implantação contínua no Serviço de Aplicativo do Azure

- Artigo
- 04/06/2024
- 13 colaboradores

Comentários

Neste artigo[Preparar o repositório](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#prepare-your-repository)[Configurar a fonte de implantação](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#configure-the-deployment-source)[Desabilitar a implantação contínua](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#disable-continuous-deployment)[Quais são os provedores de compilação?](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#what-are-the-build-providers)Mostrar mais 3



 Observação

A partir de 1º de junho de 2024, todos os aplicativos recém-criados do Serviço de Aplicativo terão a opção de gerar um nome do host padrão exclusivo usando a convenção de nomenclatura `<app-name>-<random-hash>.<region>.azurewebsites.net`. Os nomes de aplicativos existentes permanecerão inalterados.

Exemplo: `myapp-ds27dh7271aah175.westus-01.azurewebsites.net`

Para obter mais detalhes, consulte [Nome do Host Padrão Exclusivo para o Recurso Serviço de Aplicativo](https://techcommunity.microsoft.com/t5/apps-on-azure-blog/public-preview-creating-web-app-with-a-unique-default-hostname/ba-p/4156353).

O [Serviço de Aplicativo do Azure](https://learn.microsoft.com/pt-br/azure/app-service/overview) permite a implantação contínua de repositórios [GitHub](https://help.github.com/articles/create-a-repo), [Bitbucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html) e [Azure Repos](https://learn.microsoft.com/pt-br/azure/devops/repos/git/creatingrepo) ao efetuar pull das atualizações mais recentes.



## Preparar o repositório

Para obter builds automáticos do servidor de build do Serviço de Aplicativo do Azure, verifique se a raiz do repositório tem os arquivos adequados no projeto.

Expandir a tabela

| Runtime                   | Arquivos do diretório raiz                                   |
| :------------------------ | :----------------------------------------------------------- |
| ASP.NET (somente Windows) | `*.sln`, `*.csproj` ou `default.aspx`                        |
| ASP.NET Core              | `*.sln` ou `*.csproj`                                        |
| PHP                       | `index.php`                                                  |
| Ruby (somente Linux)      | `Gemfile`                                                    |
| Node.js                   | `server.js`, `app.js` ou `package.json` com um script inicial |
| Python                    | `*.py`, `requirements.txt` ou `runtime.txt`                  |
| HTML                      | `default.htm`, `default.html`, `default.asp`, `index.htm`, `index.html` ou `iisstart.htm` |
| WebJobs                   | `<job_name>/run.<extension>` em `App_Data/jobs/continuous` para WebJobs contínuos ou `App_Data/jobs/triggered` para WebJobs disparados. Para mais informações, confira a [Documentação de WebJobs do Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funções                   | Confira [Implantação contínua para Azure Functions](https://learn.microsoft.com/pt-br/azure/azure-functions/functions-continuous-deployment#requirements). |

Para personalizar a implantação, você pode incluir um arquivo *.deployment* na raiz do repositório. Para mais informações, confira [Personalizar implantações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [Script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

 Observação

Se você usar o Visual Studio, permita que o [Visual Studio crie um repositório para você](https://learn.microsoft.com/pt-br/azure/devops/repos/git/creatingrepo?tabs=visual-studio). Seu projeto estará imediatamente pronto para implantação por meio do Git.



## Configurar a fonte de implantação

1. No [portal do Azure](https://portal.azure.com/), vá para a página de gerenciamento do seu aplicativo do serviço de aplicativo.

2. No painel esquerdo, selecione **Centro de Implantação**. Em seguida, selecione **Configurações**.

3. Na caixa **Origem**, selecione uma das opções de CI/CD:

   ![Captura de tela que mostra como escolher a origem da implantação.](https://learn.microsoft.com/pt-br/azure/app-service/media/app-service-continuous-deployment/choose-source.png)

Selecione a guia que corresponde ao seu provedor de compilação para continuar.

- [GitHub](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#tabpanel_1_github)
- [Bitbucket](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#tabpanel_1_bitbucket)
- [Git Local](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#tabpanel_1_local)
- [Azure Repos](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#tabpanel_1_repos)
- [Outros repositórios](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#tabpanel_1_others)

1. O [GitHub Actions](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=githubactions#what-are-the-build-providers) é o provedor de compilação padrão. Para alterar o provedor, selecione **Alterar provedor**>**Serviço de compilação do Serviço de Aplicativo**>**OK**.

2. Ao realizar a primeira implantação pelo GitHub, selecione **Autorizar** e siga os prompts de autorização. Para usar o repositório de um usuário diferente, selecione **Alterar conta**.

3. Depois de autorizar sua conta do Azure com o GitHub, selecione a **Organização**, o **Repositório** e o **Branch** desejados.

   Quando não é possível localizar uma organização ou um repositório, pode ser necessário habilitar mais permissões no GitHub. Para obter mais informações, consulte [Gerenciamento do acesso aos repositórios de sua organização](https://docs.github.com/organizations/managing-access-to-your-organizations-repositories).

4. Em **Tipo de autenticação**, selecione **Identidade atribuída pelo usuário** para obter uma melhor segurança. Para obter mais informações, confira as [perguntas frequentes](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#frequently-asked-questions).

    Observação

   Se sua conta do Azure tiver as [permissões necessárias](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#why-do-i-see-the-error-you-do-not-have-sufficient-permissions-on-this-app-to-assign-role-based-access-to-a-managed-identity-and-configure-federated-credentials) para a opção de **Identidade atribuída pelo usuário**, o Azure criará uma [identidade gerenciada atribuída pelo usuário](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#what-does-the-user-assigned-identity-option-do-for-github-actions) para você. Caso contrário, trabalhe com o administrador do Azure para criar uma [identidade com a função necessária no aplicativo](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#why-do-i-see-the-error-this-identity-does-not-have-write-permissions-on-this-app-please-select-a-different-identity-or-work-with-your-admin-to-grant-the-website-contributor-role-to-your-identity-on-this-app) e selecione-a aqui na lista suspensa.

5. (Opcional) Para ver o arquivo antes de salvar as alterações, selecione **Visualizar arquivo**. O Serviço de Aplicativo seleciona um modelo de fluxo de trabalho com base na [configuração de pilha de linguagem](https://learn.microsoft.com/pt-br/azure/app-service/configure-common#configure-language-stack-settings) do aplicativo e o confirma no repositório GitHub selecionado.

6. Selecione **Salvar**.

   Novas confirmações no repositório e no branch selecionados agora são implantadas continuamente em seu aplicativo do Serviço de Aplicativo. É possível acompanhar as confirmações e implantações na guia **Logs**.



## Desabilitar a implantação contínua

1. No [portal do Azure](https://portal.azure.com/), vá para a página de gerenciamento do seu aplicativo do serviço de aplicativo.

2. No painel esquerdo, selecione **Centro de Implantação**. Em seguida, selecione **Configurações**>**Desconectar**:

   ![Captura de tela que mostra como desconectar a sincronização de pastas de nuvem com seu aplicativo do Serviço de Aplicativo no portal do Azure.](https://learn.microsoft.com/pt-br/azure/app-service/media/app-service-continuous-deployment/disable.png)

3. Por padrão, o arquivo do fluxo de trabalho do GitHub Actions está preservado no seu repositório, mas continua a disparar a implantação no seu aplicativo. Para excluir o arquivo do repositório, selecione **Excluir arquivo de fluxo de trabalho**.

4. Selecione **OK**.



## Quais são os provedores de compilação?

Dependendo da origem da implantação no Centro de Implantação, você poderá ver algumas opções para selecionar para provedores de compilação. Os provedores de compilação ajudam você a criar uma solução de CI/CD com o Serviço de Aplicativo do Azure automatizando a compilação, o teste e a implantação.

Você não está limitado às opções de provedor de compilação encontradas no Centro de Implantação, mas o Serviço de Aplicativo permite que você as configure rapidamente e oferece uma experiência integrada de registro em log de implantação.

- [GitHub Actions](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#tabpanel_2_githubactions)
- [Serviço de compilação do Serviço de Aplicativo](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#tabpanel_2_appservice)
- [Azure Pipelines](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#tabpanel_2_pipelines)

O provedor de compilação do GitHub Actions está disponível apenas para [implantação do GitHub](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github#configure-the-deployment-source). Quando configurado do Centro de Implantação do aplicativo, ele conclui estas ações para configurar a CI/CD:

- Ele deposita um arquivo de fluxo de trabalho do GitHub Actions no repositório do GitHub para lidar com tarefas de compilação e implantação no serviço de aplicativo.
- Para autenticação básica, ele adiciona o perfil de publicação para o aplicativo como um segredo do GitHub. O arquivo de fluxo de trabalho usa esse segredo para autenticar-se no serviço de aplicativo.
- Para obter a identidade atribuída pelo usuário, confira [O que a opção de identidade atribuída pelo usuário faz para o GitHub Actions?](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#what-does-the-user-assigned-identity-option-do-for-github-actions)
- Captura informações dos [logs de execução de fluxo de trabalho](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) e as exibe na guia **Logs** no Centro de Implantação.

É possível personalizar o provedor de compilação GitHub Actions das formas a seguir:

- Personalize o arquivo de fluxo de trabalho depois que ele for gerado no repositório GitHub. Para saber mais, consulte [Sintaxe de fluxo de trabalho para o GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Certifique-se de que o fluxo de trabalho seja implantado no Serviço de Aplicativo com a ação [azure/webapps-deploy](https://github.com/Azure/webapps-deploy).
- Se a ramificação selecionada estiver protegida, você ainda poderá visualizar o arquivo de fluxo de trabalho sem salvar a configuração e, em seguida, adicioná-lo manualmente ao repositório. Esse método não fornece integração de log com o portal do Azure.
- Em vez de usar a autenticação básica ou uma identidade atribuída pelo usuário, você também pode implantar usando uma [entidade de serviço](https://learn.microsoft.com/pt-br/azure/app-service/deploy-github-actions?tabs=userlevel) no Microsoft Entra ID. Isso não pode ser configurado no portal.



## O que acontece com meu aplicativo durante a implantação?

Todos os métodos de implantação oficialmente suportados fazem alterações nos arquivos da pasta */home/site/wwwroot* do seu aplicativo. Esses arquivos são usados para executar o aplicativo. Portanto, a implantação pode falhar por causa de arquivos bloqueados. O aplicativo também pode se comportar de modo imprevisível durante a implantação, porque nem todos os arquivos são atualizados ao mesmo tempo. Esse comportamento não é desejável para um aplicativo voltado ao cliente. Há algumas maneiras de evitar esses problemas:

- [Execute o aplicativo diretamente do pacote ZIP](https://learn.microsoft.com/pt-br/azure/app-service/deploy-run-package), sem descompactá-lo.
- Interrompa o aplicativo ou habilite o modo offline durante a implantação. Para obter mais informações, confira [Lidar com arquivos bloqueados durante a implantação](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implantar um [slot de preparo](https://learn.microsoft.com/pt-br/azure/app-service/deploy-staging-slots) com [troca automática](https://learn.microsoft.com/pt-br/azure/app-service/deploy-staging-slots#configure-auto-swap) ativada.



## Perguntas frequentes

- [O provedor de compilação do GitHub Actions funcionará com autenticação básica se a autenticação básica estiver desabilitada?](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#does-the-github-actions-build-provider-work-with-basic-authentication-if-basic-authentication-is-disabled)
- [O que a opção de identidade atribuída pelo usuário faz para o GitHub Actions?](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#what-does-the-user-assigned-identity-option-do-for-github-actions)
- [Por que vejo o erro: "Essa identidade não tem permissões de gravação neste aplicativo. Selecione uma identidade diferente ou trabalhe com o administrador para conceder a função Colaborador do Site à sua identidade neste aplicativo"?](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#why-do-i-see-the-error-this-identity-does-not-have-write-permissions-on-this-app-please-select-a-different-identity-or-work-with-your-admin-to-grant-the-website-contributor-role-to-your-identity-on-this-app)
- [Por que vejo o erro: "Essa identidade não tem permissões de gravação neste aplicativo. Selecione uma identidade diferente ou trabalhe com o administrador para conceder a função Colaborador do Site à sua identidade neste aplicativo"?](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#why-do-i-see-the-error-this-identity-does-not-have-write-permissions-on-this-app-please-select-a-different-identity-or-work-with-your-admin-to-grant-the-website-contributor-role-to-your-identity-on-this-app)



#### O provedor de compilação do GitHub Actions funcionará com autenticação básica se a autenticação básica estiver desabilitada?

Não. Tente usar o GitHub Actions com a opção de **identidade atribuída pelo usuário**.

Para obter mais informações, confira [Implantação sem autenticação básica](https://learn.microsoft.com/pt-br/azure/app-service/configure-basic-auth-disable#deployment-without-basic-authentication).



#### O que a opção de identidade atribuída pelo usuário faz para o GitHub Actions?

Ao selecionar a **identidade atribuída pelo usuário** na origem do **GitHub Actions**, o Serviço de Aplicativo configurará todos os recursos necessários no Azure e no GitHub para habilitar a autenticação recomendada do OpenID Connect com o GitHub Actions.

Especificamente, o Serviço de Aplicativo faz as seguintes operações:

- [Cria uma de credencial federada](https://learn.microsoft.com/pt-br/entra/workload-id/workload-identity-federation-create-trust-user-assigned-managed-identity?pivots=identity-wif-mi-methods-azp) entre uma identidade gerenciada atribuída pelo usuário no Azure e seu repositório e branch selecionados no GitHub.
- Cria os segredos `AZURE_CLIENT_ID`, `AZURE_TENANT_ID` e `AZURE_SUBSCRIPTION_ID` da credencial federada no repositório GitHub selecionado.
- Atribui a identidade ao seu aplicativo.

Em um fluxo de trabalho do GitHub Actions em seu repositório GitHub, você pode usar a ação [Azure/logon](https://github.com/Azure/login) para autenticar com seu aplicativo usando o OpenID Connect. Para obter exemplos, confira [Adicionar o arquivo de fluxo de trabalho ao repositório GitHub](https://learn.microsoft.com/pt-br/azure/app-service/deploy-github-actions#3-add-the-workflow-file-to-your-github-repository).

Se sua conta do Azure tiver as [permissões necessárias](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#why-do-i-see-the-error-you-do-not-have-sufficient-permissions-on-this-app-to-assign-role-based-access-to-a-managed-identity-and-configure-federated-credentials), o Serviço de Aplicativo criará uma identidade gerenciada atribuída pelo usuário e a configurará para você. Essa identidade não será mostrada na página **Identidades** do aplicativo. Se sua conta do Azure não tiver as permissões necessárias, você deverá selecionar uma [identidade existente com a função necessária](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#why-do-i-see-the-error-this-identity-does-not-have-write-permissions-on-this-app-please-select-a-different-identity-or-work-with-your-admin-to-grant-the-website-contributor-role-to-your-identity-on-this-app).



#### Por que vejo o erro: "Você não tem permissões suficientes neste aplicativo para atribuir acesso baseado em função a uma identidade gerenciada e configurar credenciais federadas"?

A mensagem indica que sua conta do Azure não tem as permissões necessárias para criar uma identidade gerenciada atribuída pelo usuário para o GitHub Actions. As permissões necessárias (com escopo para seu aplicativo) são:

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.ManagedIdentity/userAssignedIdentities/write`

Por padrão, as funções **Administrador de Acesso do Usuário** e **Proprietário** já têm essas permissões, mas a função **Colaborador** não. Se você não tiver as permissões necessárias, trabalhe com o administrador do Azure para criar uma identidade gerenciada atribuída pelo usuário com a função [Colaborador do Site](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#why-do-i-see-the-error-this-identity-does-not-have-write-permissions-on-this-app-please-select-a-different-identity-or-work-with-your-admin-to-grant-the-website-contributor-role-to-your-identity-on-this-app). No Centro de Implantação, você pode selecionar a identidade na lista suspensa **GitHub**>**Identidade**.

Para obter mais informações sobre as etapas alternativas, confira [Implantar no Serviço de Aplicativo usando o GitHub Actions](https://learn.microsoft.com/pt-br/azure/app-service/deploy-github-actions).



#### Por que vejo o erro: "Essa identidade não tem permissões de gravação neste aplicativo. Selecione uma identidade diferente ou trabalhe com o administrador para conceder a função Colaborador do Site à sua identidade neste aplicativo"?

A mensagem indica que a identidade gerenciada atribuída pelo usuário selecionada não tem a função necessária para habilitar o [OpenID Connect](https://learn.microsoft.com/pt-br/azure/app-service/deploy-continuous-deployment?tabs=github%2Cgithubactions#what-does-the-user-assigned-identity-option-do-for-github-actions) entre o repositório do GitHub e o aplicativo do Serviço de Aplicativo. A identidade deve ter uma das seguintes funções no aplicativo: **Proprietário**, **Colaborador** e **Colaborador de Sites**. A função menos privilegiada que a identidade precisa é a de **Colaborador de Sites**.



## Mais recursos

- [Usar PowerShell do Azure](https://learn.microsoft.com/pt-br/powershell/azure/)

------

## Comentários

Esta página foi útil?

YesNo

[Fornecer comentários sobre o produto ](https://feedback.azure.com/d365community/forum/b09330d1-c625-ec11-b6e6-000d3a4f0f1c)|

[Obter ajuda no Microsoft Q&A](https://learn.microsoft.com/answers/tags/436/azure-app-service/)