# Mecanismo do Docker no Windows

- Artigo
- 20/03/2023
- 19 colaboradores

Comentários

Neste artigo[Instalar o Docker](https://learn.microsoft.com/pt-br/virtualization/windowscontainers/manage-docker/configure-docker-daemon#install-docker)[Configurar o Docker com um arquivo de configuração](https://learn.microsoft.com/pt-br/virtualization/windowscontainers/manage-docker/configure-docker-daemon#configure-docker-with-a-configuration-file)[Configurar o Docker no serviço do Docker](https://learn.microsoft.com/pt-br/virtualization/windowscontainers/manage-docker/configure-docker-daemon#configure-docker-on-the-docker-service)[Configuração comum](https://learn.microsoft.com/pt-br/virtualization/windowscontainers/manage-docker/configure-docker-daemon#common-configuration)Mostrar mais 2

> Aplica-se a: Windows Server 2022, Windows Server 2019 e Windows Server 2016

O mecanismo do Docker e o cliente não estão incluídos no Windows e precisarão ser instalados e configurados individualmente. Além disso, o mecanismo do Docker pode aceitar muitas configurações personalizadas. Alguns exemplos incluem a configuração de como o daemon aceita solicitações de entrada, as opções de rede padrão e as configurações de depuração/log. No Windows, essas configurações podem ser especificadas em um arquivo de configuração ou usando Gerenciador de Controle de Serviços do Windows. Este documento mostra detalhadamente como instalar e configurar o mecanismo do Docker e também fornece alguns exemplos de configurações usadas normalmente.



## Instalar o Docker

Você precisa do Docker para trabalhar com contêineres do Windows. O Docker é composto pelo mecanismo de Docker (dockerd.exe) e pelo cliente de Docker (docker.exe). A maneira mais fácil de instalar tudo está no guia de início rápido, que ajudará você a configurar tudo e a executar seu primeiro contêiner.

- [Instalar o Docker](https://learn.microsoft.com/pt-br/virtualization/windowscontainers/quick-start/set-up-environment)

Para instalações com script, consulte [Usar um script para instalar o Docker EE](https://docs.mirantis.com/docker-enterprise/v3.1/dockeree-products/mcr/mcr-windows.html).

Para usar o Docker, você precisará instalar as imagens de contêiner. Para obter mais informações, confira [docs para nossas imagens base de contêiner](https://learn.microsoft.com/pt-br/virtualization/windowscontainers/manage-containers/container-base-images).



## Configurar o Docker com um arquivo de configuração

O método preferencial para configurar o Mecanismo do Docker no Windows é usar um arquivo de configuração. O arquivo de configuração pode ser encontrado em 'C:\ProgramData\Docker\config\daemon.json'. Você pode criar esse arquivo se ele ainda não existe.

 Observação

Nem todas as opções de configuração disponíveis do Docker são aplicáveis ao Docker no Windows. O exemplo a seguir mostra a as opções de configuração que se aplicam. Para obter mais informações sobre a configuração do mecanismo do Docker, confira o [Arquivo de configuração do daemon do Docker](https://docs.docker.com/engine/reference/commandline/dockerd/#/windows-configuration-file).

JSON

```json
{
    "authorization-plugins": [],
    "dns": [],
    "dns-opts": [],
    "dns-search": [],
    "exec-opts": [],
    "storage-driver": "",
    "storage-opts": [],
    "labels": [],
    "log-driver": "",
    "mtu": 0,
    "pidfile": "",
    "data-root": "",
    "cluster-store": "",
    "cluster-advertise": "",
    "debug": true,
    "hosts": [],
    "log-level": "",
    "tlsverify": true,
    "tlscacert": "",
    "tlscert": "",
    "tlskey": "",
    "group": "",
    "default-ulimits": {},
    "bridge": "",
    "fixed-cidr": "",
    "raw-logs": false,
    "registry-mirrors": [],
    "insecure-registries": [],
    "disable-legacy-registry": false
}
```

Você precisa adicionar apenas a alterações desejadas ao arquivo de configuração. Por exemplo, o exemplo a seguir configura o mecanismo do Docker para aceitar conexões de entrada na porta 2375. Todas as outras opções de configuração usarão os valores padrão.

JSON

```json
{
    "hosts": ["tcp://0.0.0.0:2375"]
}
```

Da mesma forma, o exemplo a seguir configura o daemon do Docker para manter imagens e contêineres em um caminho alternativo. Se não for especificado, o padrão será `c:\programdata\docker`.

JSON

```json
{   
    "data-root": "d:\\docker"
}
```

O exemplo a seguir configura o daemon do Docker para aceitar somente conexões seguras pela porta 2376.

JSON

```json
{
    "hosts": ["tcp://0.0.0.0:2376", "npipe://"],
    "tlsverify": true,
    "tlscacert": "C:\\ProgramData\\docker\\certs.d\\ca.pem",
    "tlscert": "C:\\ProgramData\\docker\\certs.d\\server-cert.pem",
    "tlskey": "C:\\ProgramData\\docker\\certs.d\\server-key.pem",
}
```



## Configurar o Docker no serviço do Docker

O mecanismo do Docker também pode ser configurado modificando o serviço do Docker com `sc config`. Usando esse método, os sinalizadores do mecanismo do Docker são definidos diretamente no serviço do Docker. Execute o comando a seguir em um prompt de comando (cmd.exe não PowerShell):

Prompt de comando do Windows

```cmd
sc config docker binpath= "\"C:\Program Files\docker\dockerd.exe\" --run-service -H tcp://0.0.0.0:2375"
```

 Observação

Você não precisará executar esse comando se o arquivo daemon.json já contiver a entrada `"hosts": ["tcp://0.0.0.0:2375"]`.



## Configuração comum

Os exemplos de arquivo de configuração a seguir mostram as configurações comuns do Docker. Eles podem ser combinados em um único arquivo de configuração.



### Criação de rede padrão

Para configurar o mecanismo do Docker para que ele não crie uma rede NAT padrão, use a seguinte configuração.

JSON

```json
{
    "bridge" : "none"
}
```

Para mais informações, consulte [Gerenciar Redes do Docker](https://learn.microsoft.com/pt-br/virtualization/windowscontainers/container-networking/network-drivers-topologies).



### Definir o grupo de segurança de Docker

Quando você estiver conectado ao host do Docker e estiver executando comandos localmente, esses comandos serão executados por um pipe nomeado. Por padrão, somente os membros do grupo Administradores podem acessar o mecanismo do Docker por meio do pipe nomeado. Para especificar um grupo de segurança que tenha esse acesso, use o sinalizador `group`.

JSON

```json
{
    "group" : "docker"
}
```



## Configuração proxy

Para configurar informações de proxy para `docker search` e `docker pull`, crie uma variável de ambiente do Windows com o nome `HTTP_PROXY` ou `HTTPS_PROXY`, e um valor das informações de proxy. Isso pode ser concluído com o PowerShell usando um comando semelhante a este:

PowerShell

```powershell
[Environment]::SetEnvironmentVariable("HTTP_PROXY", "http://username:password@proxy:port/", [EnvironmentVariableTarget]::Machine)
```

Quando a variável tiver sido definida, reinicie o serviço do Docker.

PowerShell

```powershell
Restart-Service docker
```

Para obter mais informações, confira o [Arquivo de Configuração do Windows em Docker.com](https://docs.docker.com/engine/reference/commandline/dockerd/#/windows-configuration-file).



## Como desinstalar o Docker

Esta seção informa como desinstalar o Docker e realizar uma limpeza completa dos componentes do sistema Docker no seu sistema Windows 10 ou Windows Server 2016.

 Observação

Você precisa executar todos os comandos nestas instruções em uma sessão do PowerShell com privilégios elevados.



### Preparar o sistema para a remoção do Docker

Antes de desinstalar o Docker, verifique se não há contêineres em execução no sistema.

Execute os seguintes cmdlets para verificar se há contêineres em execução:

PowerShell

```powershell
# Leave swarm mode (this will automatically stop and remove services and overlay networks)
docker swarm leave --force

# Stop all running containers
docker ps --quiet | ForEach-Object {docker stop $_}
```

Também é recomendável remover todos os contêineres, imagens de contêineres, redes e volumes do sistema antes de remover o Docker. Faça isso executando este cmdlet:

PowerShell

```powershell
docker system prune --volumes --all
```



### Desinstalar o Docker

Em seguida, você precisará de fato desinstalar o Docker.

Para desinstalar o Docker no Windows 10

- Vá para **Configurações**>**Aplicativos** em seu computador Windows 10
- Em **Aplicativos e Recursos**, encontre **Docker for Windows**
- Vá para **Docker for Windows**>**Desinstalar**

Para desinstalar o Docker no Windows Server 2016:

Em uma sessão do PowerShell com privilégios elevados, use os cmdlets **Uninstall-Package** e **Uninstall-Module** para remover o módulo do Docker e o respectivo Provedor de Gerenciamento de Pacotes do seu sistema, conforme mostrado no exemplo a seguir:

PowerShell

```powershell
Uninstall-Package -Name docker -ProviderName DockerMsftProvider
Uninstall-Module -Name DockerMsftProvider
```

 Dica

Você pode encontrar o Provedor do Pacote que usou para instalar o Docker com `PS C:\> Get-PackageProvider -Name *Docker*`



### Limpar dados do Docker e componentes do sistema

Depois de desinstalar o Docker, você precisará remover as redes padrão do Docker para que sua configuração não permaneça em seu sistema após a remoção do Docker. Faça isso executando este cmdlet:

PowerShell

```powershell
Get-HNSNetwork | Remove-HNSNetwork
```

Para remover as redes padrão do Docker no Windows Server 2016.

PowerShell

```powershell
Get-ContainerNetwork | Remove-ContainerNetwork
```

Execute o seguinte cmdlet para remover os dados de programa do Docker do sistema:

PowerShell

```powershell
Remove-Item "C:\ProgramData\Docker" -Recurse
```

Talvez você queira remover os recursos opcionais do Windows associados ao Docker ou a contêineres no Windows.

Isso inclui o recurso "Contêineres", que é habilitado automaticamente em qualquer Windows 10 ou Windows Server 2016 quando o Docker é instalado. Ele também pode incluir o recurso "Hyper-V", que é habilitado automaticamente no Windows 10 quando o Docker é instalado, mas deve ser habilitado explicitamente no Windows Server 2016.

 Importante

[O recurso Hyper-V](https://learn.microsoft.com/pt-br/virtualization/hyper-v-on-windows/about/) é um recurso de virtualização geral que habilita muito mais do que apenas contêineres. Antes de desabilitar o recurso Hyper-V, certifique-se de que não haja nenhum outro componente virtualizado no seu sistema que o exija.

Para remover recursos do Windows no Windows 10:

- Vá até **Painel de Controle**>**Programas**>**Programas e Recursos**>**Ativar ou desativar recursos do Windows**.
- Localize o nome do recurso, ou recursos, que deseja desabilitar; neste caso, **Contêineres** e (opcionalmente) **Hyper-V**.
- Desmarque a caixa ao lado do nome do recurso que deseja desabilitar.
- Selecione **"OK"**

Para remover recursos do Windows no Windows Server 2016:

Em uma sessão do PowerShell com privilégios elevados, execute os seguintes cmdlets para desabilitar os recursos **Contêineres** e (opcionalmente) **Hyper-V** do sistema:

PowerShell

```powershell
Remove-WindowsFeature Containers
Remove-WindowsFeature Hyper-V
```



### Reinicializar o sistema

Para concluir a desinstalação e a limpeza, execute o seguinte cmdlet em uma sessão do PowerShell com privilégios elevados para reinicializar o sistema:

PowerShell

```powershell
Restart-Computer -Force
```

------

## Comentários

Esta página foi útil?

YesNo