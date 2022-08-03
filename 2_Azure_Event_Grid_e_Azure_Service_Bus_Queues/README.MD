# Lab 2 - Publicar_e_assinar_eventos_do_Event_Grid_e processar_mensagens_de_forma_assíncrona_usando_o_Azure_Service_Bus_Queues


## Diagrama de Arquitetura

![image](../../Arquiteturas/images/Lab2imersao.png)


## Tarefa 1

#### Passo 1: Abra o portal do Azure

1. Abra seu navegador.

2. Na janela do navegador aberta, navegue até o portal do Azure (<https://portal.azure.com>), e entre com a conta que você usará para este laboratório.

    > **Observação**: se esta for a primeira vez que você entra no portal do Azure, será oferecido um tour pelo portal. Selecione **Começar** para pular o tour e começar a usar o portal.

#### Passo 2: Abra o Azure Cloud Shell

 1. No portal do Azure, selecione o ícone do **Cloud Shell** ![Cloud Shell icon](./../images/az204_lab_CloudShell.png) para abrir uma nova sessão do Bash. Se o padrão do Cloud Shell for uma sessão do PowerShell, selecione **PowerShell** e, no menu suspenso, selecione **Bash** .

    > **Observação**: se esta for a primeira vez que você está iniciando o **Cloud Shell** , quando solicitado a selecionar **Bash** ou **PowerShell** , selecione **Bash** . Quando você receber a mensagem Você não tem armazenamento montado , selecione a assinatura que você está usando neste laboratório e, em seguida, selecione Criar armazenamento .

 2. No portal do Azure, no prompt de comando do **Cloud Shell** , execute o seguinte comando para obter a versão da ferramenta Azure Command-Line Interface (Azure CLI):

    ```bash
    az --version
    ```

#### Passo 3: Revise o registro do provedor Microsoft.EventGrid

1. No **painel CLoud Shell**, execute o seguinte comando para obter uma lista de subgrupos e comandos no nível raiz da CLI do Azure:

    ```bash
    az --help
    ```

2. No **painel CLoud Shell**, execute o seguinte comando para obter uma lista dos comandos disponíveis para provedores de recursos:

    ```bash
    az provider --help
    ```

3. No **painel CLoud Shell**, execute o seguinte comando para listar todos os provedores registrados atualmente:

    ```bash
    az provider list
    ```

4. No **painel CLoud Shell**, execute o seguinte comando para listar apenas os namespaces dos provedores registrados no momento:

    ```bash
    az provider list --query "[].namespace"
    ```

5. No **painel CLoud Shell**, revise a lista de provedores registrados no momento. Observe que o provedor **Microsoft.EventGrid** está atualmente incluído na lista de provedores.

6. Feche o **painel Cloud Shell**.

#### Passo 4: Criar um tópico EventGrid personalizado
1. Criar Grupo de Recursos


    **Cloud Shell**

    ```bash
    az group create --name "PubSubEvents" --location "eastus"
    ```
2. Criar tópico do EventGrid

    **Cloud Shell**
    ```bash
    az eventgrid topic create -g "PubSubEvents" --name "hrttopic<yourname>" -l "eastus" --input-schema eventgridschema
    ```

    Capture o valor do id do recurso como mostra a figura abaixo. Você usará esse valor posteriormente no laboratório.


    ![image](../../Arquiteturas/images/idtopicoeventgridlab2.png)

3. Criar Plano de Serviço de Aplicativo

    **Cloud Shell**
    ```bash
    az appservice plan create -g "PubSubEvents" -n EventPlan --is-linux --sku P1v2
    ```
3. Criar Aplicativo Web

    **Cloud Shell**
    ```bash
    az webapp create -g "PubSubEvents" -p EventPlan -n  "eventviewer<yourname>" -i microsoftlearning/azure-event-grid-viewer:latest
    ```
    > **Observação**: Aguarde o Azure concluir a criação do aplicativo Web antes de continuar com o laboratório.

    Capture o valor do link da URL como mostra a figura abaixo. Você usará esse valor posteriormente no laboratório.

    ![image](../../Arquiteturas/images/urlwebservicelab2.png)

    #### Passo 5: Criar uma assinatura do Event Grid

    **Cloud Shell**
    ```bash
    az eventgrid event-subscription create --name "basicsub" --source-resource-id "/subscriptions/<subscriptionID>/resourceGroups/PubSubEvents/providers/Microsoft.EventGrid/topics/<topicname>" --endpoint "https://eventviewer<yourname>.azurewebsites.net/api/updates" --delivery-identity-endpoint-type "webhook" --event-delivery-schema eventgridschema --azure-active-directory-tenant-id "tenantID"
    ```
    + No parâmetro **--source-resource-id** , insira o valor do ID do tópico do EventGrid que você guardou anteriormente.
    
    + No parâmetro **endpoint** , insira o valor de URL do aplicativo Web que você guardou anteriormente, certifique-se de que ele usa um prefixo https:// , e adicione o sufixo /api/updates. Por exemplo, se o valor da URL do seu aplicativo Web for http://eventviewerstudent.azurewebsites.net/, seu endpoint será https://eventviewerstudent.azurewebsites.net/api/updates.
    
  
    
    