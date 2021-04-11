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
ms.openlocfilehash: a63c6f074178794db38b47950e176dd729344a54
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492729"
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

È necessario accedere all'account con il comando [az login](/cli/azure/reference-index#az-login). Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account#az-account-set). Annotare il valore **id** dall'output **az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](/cli/azure/account#az-account-list).

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
Eseguire il comando seguente per creare un database, **newDatabase** se non ne è già stato creato uno.

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

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Connettersi e testare la connessione usando l'interfaccia della riga di comando di Azure

Il server flessibile database di Azure per MySQL consente di connettersi al server MySQL con il comando dell'interfaccia della riga di comando di Azure ```az mysql flexible-server connect``` . Questo comando consente di testare la connettività al server di database, creare un database di avvio rapido ed eseguire query direttamente sul server senza dover installare mysql.exe o MySQL Workbench.  È anche possibile usare Esegui il comando in modalità interattiva per l'esecuzione di più query.

Eseguire lo script seguente per verificare e convalidare la connessione al database dall'ambiente di sviluppo.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Esempio:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
Per la corretta connessione dovrebbe essere visualizzato l'output seguente:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Se la connessione non è riuscita, provare le soluzioni seguenti:
- Controllare se la porta 3306 è aperta nel computer client.
- Se il nome utente e la password dell'amministratore del server sono corretti
- Se è stata configurata la regola del firewall per il computer client
- Se il server è stato configurato con accesso privato in rete virtuale, assicurarsi che il computer client si trovi nella stessa rete virtuale.

Eseguire il comando seguente per eseguire una singola query usando l' ```--querytext``` argomento ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Esempio:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Per ulteriori informazioni sull'utilizzo ```az mysql flexible-server connect``` del comando, vedere la documentazione relativa a [connessione ed esecuzione di query](connect-azure-cli.md) .

## <a name="connect-using-mysql-command-line-client"></a>Connettersi tramite il client della riga di comando mysql

Se il server flessibile è stato creato con l'opzione per l'accesso privato (integrazione rete virtuale), sarà necessario connettersi da una risorsa all'interno della stessa rete virtuale del server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata con il server flessibile. Per ulteriori informazioni, vedere la documentazione sulla configurazione dell' [accesso privato](how-to-manage-virtual-network-portal.md) .

Se il server flessibile è stato creato con l'opzione per l'accesso pubblico (indirizzi IP consentiti), è possibile aggiungere il proprio indirizzo IP locale all'elenco di regole del firewall nel server. Per istruzioni dettagliate, fare riferimento alla [documentazione relativa alla creazione o alla gestione delle regole del firewall](how-to-manage-firewall-portal.md) .

È possibile usare [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) o [MySQL Workbench](./connect-workbench.md) per connettersi al server dall'ambiente locale. Il server flessibile database di Azure per MySQL supporta la connessione delle applicazioni client al servizio MySQL usando Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL). TLS è un protocollo standard del settore che garantisce connessioni di rete crittografate tra il server di database e le applicazioni client, consentendo di rispettare i requisiti di conformità. Per connettersi al server MySQL flessibile, sarà necessario scaricare il [certificato SSL pubblico](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) per la verifica dell'autorità di certificazione. Per altre informazioni sulla connessione con connessioni crittografate o sulla disabilitazione di SSL, vedere la documentazione [connettersi a database di Azure per MySQL-server flessibili con connessioni crittografate](how-to-connect-tls-ssl.md) .

L'esempio seguente illustra come connettersi al server flessibile usando l'interfaccia della riga di comando di MySQL. La riga di comando di MySQL verrà prima installata se non è già installata. Il certificato DigiCertGlobalRootCA richiesto per le connessioni SSL sarà scaricato. Usare l'impostazione--SSL-mode = stringa di connessione necessaria per applicare la verifica del certificato TLS/SSL. Passare il percorso del file del certificato locale al parametro--SSL-CA. Sostituire i valori con il nome server e la password effettivi.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Se è stato effettuato il provisioning del server flessibile usando **l'accesso pubblico**, è anche possibile usare [Azure cloud Shell](https://shell.azure.com/bash) per connettersi al server flessibile usando il client MySQL preinstallato, come illustrato di seguito:

Per usare Azure Cloud Shell per connettersi al server flessibile, sarà necessario consentire l'accesso alla rete da Azure Cloud Shell al server flessibile. A tale scopo, è possibile passare al pannello **rete** in portale di Azure per il server MySQL flessibile e selezionare la casella nella sezione **Firewall** "Consenti accesso pubblico da qualsiasi servizio di Azure in Azure a questo server", come illustrato nella schermata seguente e fare clic su Salva per salvare in modo permanente l'impostazione.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Screenshot che illustra come consentire l'accesso Azure Cloud Shell alla configurazione di rete di MySQL flessibile server per l'accesso pubblico.":::
 
 
> [!NOTE]
> Per lo sviluppo o il testing, è consigliabile usare il controllo **Consenti l'accesso pubblico da qualsiasi servizio di Azure in Azure a questo server** . Configura il firewall in modo da consentire le connessioni dagli indirizzi IP allocati a qualsiasi servizio o asset di Azure, incluse le connessioni dalle sottoscrizioni di altri clienti.

Fare clic su **prova** per avviare il Azure cloud Shell e usare i comandi seguenti per connettersi al server flessibile. Nel comando usare i propri valori per nome server, nome utente e password. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Quando ci si connette al server flessibile usando Azure Cloud Shell, è necessario usare il parametro--SSL = true e non--SSL-mode = REQUIRED.
> Il motivo principale è Azure Cloud Shell è preinstallato mysql.exe client dalla distribuzione MariaDB che richiede il parametro--SSL mentre il client MySQL dalla distribuzione di Oracle richiede il parametro--SSL-mode.

Se viene visualizzato il messaggio di errore seguente durante la connessione al server flessibile dopo il comando precedente, non è stato possibile impostare la regola del firewall usando l'opzione "Consenti l'accesso pubblico da qualsiasi servizio di Azure in Azure a questo server" indicata in precedenza o l'opzione non è stata salvata. Ripetere l'impostazione del firewall e riprovare.

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
> [Connettersi ed eseguire query usando l'interfaccia](connect-azure-cli.md) 
>  della riga di comando [Connettersi a database di Azure per MySQL-server flessibile con connessioni](how-to-connect-tls-ssl.md) 
>  crittografate [Creare un'app web php (Laravel) con MySQL](tutorial-php-database-app.md)
