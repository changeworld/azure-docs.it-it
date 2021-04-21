---
title: 'Avvio rapido: Creare un server - Interfaccia della riga di comando di Azure - Database di Azure per MySQL - Server flessibile'
description: Questo argomento di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare un server flessibile di Database di Azure per MySQL in un gruppo di risorse di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7addfc3a0d91b85c4d63afa4ee6a55b5202c3855
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770238"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Avvio rapido: Creare un server flessibile di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure

Questo argomento di avvio rapido illustra come usare i comandi dell'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) per creare un server flessibile di Database di Azure per MySQL in cinque minuti. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **INVIO** per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisiti

È necessario accedere all'account con il comando [az login](/cli/azure/reference-index#az_login). Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account#az_account_set). Annotare il valore **id** dall'output **az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](/cli/azure/account#az_account_list).

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Creare un server flessibile

Creare un [gruppo di risorse di Azure](../../azure-resource-manager/management/overview.md) usando il comando `az group create` e quindi creare il server flessibile MySQL in questo gruppo di risorse. È necessario specificare un nome univoco. L'esempio seguente consente di creare un gruppo di risorse denominato `myresourcegroup` nell'area `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Creare un server flessibile con il comando `az mysql flexible-server create`. Un server può contenere più database. Il comando seguente crea un server usando le impostazioni predefinite del servizio e i valori del [contesto locale](/cli/azure/local-context) dell'interfaccia della riga di comando di Azure: 

```azurecli-interactive
az mysql flexible-server create
```

Il server creato ha gli attributi seguenti: 
- Nome server generato automaticamente, nome utente amministratore, password amministratore, nome del gruppo di risorse (se non è già specificato nel contesto locale) e nello stesso percorso del gruppo di risorse 
- Impostazioni predefinite del servizio per le configurazioni server rimanenti: livello di calcolo (con possibilità di burst), dimensioni/SKU di calcolo (B1MS), periodo di conservazione dei backup (7 giorni) e versione di MySQL (5.7)
- Il metodo di connettività predefinito è l'accesso privato (integrazione rete virtuale) con una rete virtuale e una subnet generate automaticamente

> [!NOTE] 
> Il metodo di connettività non può essere modificato dopo la creazione del server. Ad esempio, se è stata selezionata l'opzione per l'*accesso privato (integrazione rete virtuale)* durante la creazione, non è possibile passare all'*accesso pubblico (indirizzi IP consentiti)* dopo la creazione. È consigliabile creare un server con accesso privato per accedere in modo sicuro al server tramite l'integrazione rete virtuale. Altre informazioni sull'accesso privato sono disponibili nell'[articolo sui concetti](./concepts-networking.md).

Per modificare le impostazioni predefinite, vedere la [documentazione di riferimento](/cli/azure/mysql/flexible-server) dell'interfaccia della riga di comando di Azure per l'elenco completo dei parametri dell'interfaccia della riga di comando configurabili. 

Di seguito è riportato un output di esempio: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Per modificare le impostazioni predefinite, vedere la [documentazione di riferimento](/cli/azure/mysql/flexible-server) dell'interfaccia della riga di comando di Azure per l'elenco completo dei parametri dell'interfaccia della riga di comando configurabili. 

## <a name="create-a-database"></a>Creazione di un database
Eseguire il comando seguente per creare un **database, newdatabase,** se non è già stato creato.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> Le connessioni al database di Azure per MySQL comunicano sulla porta 3306. Se si tenta di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In questo caso, non è possibile connettersi al server a meno che il reparto IT non apra la porta 3306.

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Il risultato è in formato JSON. Annotare il **fullyQualifiedDomainName** e l'**administratorLogin**. Di seguito è riportato un esempio di output JSON: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Connettersi e testare la connessione tramite l'interfaccia della riga di comando di Azure

Il server flessibile di Database di Azure per MySQL consente di connettersi al server mysql con il comando dell'interfaccia della riga di comando di ```az mysql flexible-server connect``` Azure. Questo comando consente di testare la connettività al server di database, creare un database iniziale rapido ed eseguire query direttamente sul server senza dover installare mysql.exe o MySQL Workbench.  È anche possibile usare il comando in modalità interattiva per l'esecuzione di più query.

Eseguire lo script seguente per testare e convalidare la connessione al database dall'ambiente di sviluppo.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Esempio:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
Per una connessione riuscita, verrà visualizzato l'output seguente:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Se la connessione non è riuscita, provare queste soluzioni:
- Controllare se la porta 3306 è aperta nel computer client.
- se il nome utente e la password dell'amministratore del server sono corretti
- se è stata configurata la regola del firewall per il computer client
- Se il server è stato configurato con l'accesso privato nella rete virtuale, assicurarsi che il computer client si trova nella stessa rete virtuale.

Eseguire il comando seguente per eseguire una singola query usando ```--querytext``` l'argomento ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Esempio:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Per altre informazioni sull'uso ```az mysql flexible-server connect``` del comando , vedere la documentazione relativa alla connessione e alle [query.](connect-azure-cli.md)

## <a name="connect-using-mysql-command-line-client"></a>Connettersi tramite il client della riga di comando mysql

Se il server flessibile è stato creato con l'opzione per l'accesso privato (integrazione rete virtuale), sarà necessario connettersi da una risorsa all'interno della stessa rete virtuale del server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata con il server flessibile. Per altre [informazioni, vedere la documentazione sulla configurazione](how-to-manage-virtual-network-portal.md) dell'accesso privato.

Se il server flessibile è stato creato con l'opzione per l'accesso pubblico (indirizzi IP consentiti), è possibile aggiungere il proprio indirizzo IP locale all'elenco di regole del firewall nel server. Per [istruzioni dettagliate, vedere](how-to-manage-firewall-portal.md) la documentazione relativa alla creazione o alla gestione delle regole del firewall.

È possibile usare [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) o [MySQL Workbench](./connect-workbench.md) per connettersi al server dall'ambiente locale. Il server flessibile di Database di Azure per MySQL supporta la connessione delle applicazioni client al servizio MySQL tramite Transport Layer Security (TLS), noto in precedenza come Secure Sockets Layer (SSL). TLS è un protocollo standard di settore che garantisce connessioni di rete crittografate tra il server di database e le applicazioni client, consentendo di rispettare i requisiti di conformità. Per connettersi al server flessibile MySQL, è necessario scaricare il certificato [SSL pubblico](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) per la verifica dell'autorità di certificazione. Per altre informazioni sulla connessione con connessioni crittografate o sulla disabilitazione di SSL, vedere la documentazione Connettersi a Database di Azure per [MySQL - Server](how-to-connect-tls-ssl.md) flessibile con connessioni crittografate.

L'esempio seguente illustra come connettersi al server flessibile usando l'interfaccia della riga di comando mysql. Si installerà innanzitutto la riga di comando mysql se non è già installata. Si scaricherà il certificato DigiCertGlobalRootCA necessario per le connessioni SSL. Usare l'impostazione --ssl-mode=REQUIRED per applicare la verifica del certificato TLS/SSL. Passare il percorso del file del certificato locale al parametro --ssl-ca. Sostituire i valori con il nome server e la password effettivi.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Se è stato effettuato il provisioning del server flessibile usando l'accesso [pubblico,](https://shell.azure.com/bash) è anche possibile usare Azure Cloud Shell per connettersi al server flessibile usando il client mysql preinstallato, come illustrato di seguito:

Per usare Azure Cloud Shell per connettersi al server flessibile, è necessario consentire l'accesso alla rete Azure Cloud Shell al server flessibile. A tale scopo, è  possibile passare al pannello Rete in portale di Azure per il server flessibile MySQL e selezionare la casella nella sezione **Firewall** che indica "Consenti l'accesso pubblico da qualsiasi servizio di Azure in Azure a questo server" come illustrato nello screenshot seguente e fare clic su Salva per rendere persistente l'impostazione.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Screenshot che mostra come consentire Azure Cloud Shell l'accesso al server flessibile MySQL per la configurazione della rete di accesso pubblico.":::
 
 
> [!NOTE]
> Selezionare Consenti **l'accesso pubblico da qualsiasi servizio di Azure in Azure a** questo server da usare solo per lo sviluppo o il test. Configura il firewall per consentire le connessioni da indirizzi IP allocati a qualsiasi servizio o asset di Azure, incluse le connessioni dalle sottoscrizioni di altri clienti.

Fare clic **su Prova per** avviare il Azure Cloud Shell e usare i comandi seguenti per connettersi al server flessibile. Nel comando usare i propri valori per nome server, nome utente e password. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Durante la connessione al server flessibile Azure Cloud Shell, è necessario usare il parametro --ssl=true e non --ssl-mode=REQUIRED.
> Il motivo principale è che Azure Cloud Shell viene fornito con un client mysql.exe preinstallato dalla distribuzione MariaDB che richiede il parametro --ssl mentre il client mysql dalla distribuzione di Oracle richiede il parametro --ssl-mode.

Se viene visualizzato il messaggio di errore seguente durante la connessione al server flessibile seguendo il comando precedente, non è stata impostata la regola del firewall usando l'opzione "Consenti l'accesso pubblico da qualsiasi servizio di Azure in Azure a questo server" citata in precedenza o l'opzione non viene salvata. Riprovare a impostare il firewall e riprovare.

ERRORE 2002 (HY000): Impossibile connettersi al server MySQL in <servername> (115)

## <a name="clean-up-resources"></a>Pulire le risorse

Se queste risorse non sono necessarie per un'altra guida introduttiva/esercitazione, è possibile eliminarle eseguendo il comando seguente:

```azurecli-interactive
az group delete --name myresourcegroup
```

Se si vuole eliminare solo il server appena creato, è possibile eseguire il comando `az mysql server delete`.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi

>[!div class="nextstepaction"]
> [Connettersi ed eseguire query con l'interfaccia della riga di comando di](connect-azure-cli.md) 
>  Azure [Connettersi al server flessibile di Database di Azure per MySQL con connessioni crittografate](how-to-connect-tls-ssl.md) 
>  [Creare un'app Web PHP (Laravel) con MySQL](tutorial-php-database-app.md)
