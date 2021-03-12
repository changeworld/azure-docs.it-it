---
title: 'Guida introduttiva: distribuire il connettore Azure per FHIR (anteprima) usando un modello ARM'
description: Questa Guida introduttiva illustra come distribuire il connettore Azure per FHIR (anteprima) usando un modello di Azure Resource Manager (modello ARM).
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 01/21/2021
ms.openlocfilehash: b241484b8e8d981036fff4998d475b8b80fae42d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019342"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>Guida introduttiva: usare un modello di Azure Resource Manager (ARM) per distribuire il connettore Azure per FHIR (anteprima)

In questa Guida introduttiva si apprenderà come usare un modello di Azure Resource Manager (modello ARM) per distribuire il connettore Azure Internet per le risorse di interoperabilità sanitaria veloci (FHIR&#174;) *, una funzionalità dell'API di Azure per FHIR. Per distribuire un'istanza di lavoro del connettore Azure per FHIR, questo modello distribuisce anche un'API padre di Azure per il servizio FHIR e un'applicazione IoT Central di Azure che esporta i dati di telemetria da un simulatore di dispositivo al connettore Azure Internet per FHIR. È possibile eseguire il modello ARM per distribuire il connettore Azure per FHIR tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure dopo l'accesso.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuire in Azure un connettore Azure per FHIR usando un modello ARM nel portale di Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)

Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/).
* Per eseguire il codice in locale, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/).
* Se si vuole eseguire il codice in locale:
    * Una shell Bash (ad esempio Git Bash, inclusa in [Git per Windows](https://gitforwindows.org)).
    * [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Rivedere il modello

Il [modello](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) definisce le risorse di Azure seguenti:

* **Microsoft.HealthcareApis/services**
* **Microsoft. HealthcareApis/Services/iomtconnectors**
* **Microsoft. IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>Distribuire il modello

# <a name="portal"></a>[Portale](#tab/azure-portal)

Selezionare il collegamento seguente per distribuire il connettore Azure per FHIR usando il modello ARM nel portale di Azure:

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuire in Azure un connettore Azure per il servizio FHIR usando il modello ARM nel portale di Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

Nella pagina **Deploy Azure API for FHIR** (Distribuisci API di Azure per FHIR):

1. Se si vuole, in **Sottoscrizione** sostituire l'impostazione predefinita con un'altra sottoscrizione.

2. In **Gruppo di risorse** selezionare **Crea nuovo**, immettere un nome per il nuovo gruppo di risorse e quindi fare clic su **OK**.

3. Se è stato creato un nuovo gruppo di risorse, in **Area** selezionare un valore per il gruppo di risorse.

4. Immettere un nome per la nuova istanza di API di Azure per FHIR nel **nome del servizio FHIR**.

5. Scegliere il **percorso** per l'API di Azure per FHIR. La località può corrispondere o meno all'area del gruppo di risorse.

6. Specificare un nome per il connettore Azure per l'istanza di FHIR nel **nome del connettore** Internet.

7. Consente di specificare un nome per una connessione creata nel connettore Azure per FHIR in **nome connessione**. Questa connessione viene usata dall'applicazione IoT Central di Azure per eseguire il push dei dati di telemetria del dispositivo simulato nel connettore Azure per FHIR.

8. Immettere un nome per la nuova applicazione IoT Central di Azure nel **nome Central**. Questa applicazione userà il modello di *monitoraggio paziente continuo* per simulare un dispositivo.

9. Scegliere il percorso dell'applicazione IoT Central dall'elenco a discesa **percorso IOT Central** . 

10. Selezionare **Rivedi e crea**.

11. Leggere le condizioni e quindi selezionare **Crea**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Per eseguire gli script di PowerShell in locale, immettere prima di tutto `Connect-AzAccount` per configurare le credenziali di Azure.

Se il provider di risorse `Microsoft.HealthcareApis` non è ancora stato registrato per la sottoscrizione, è possibile registrarlo con il codice interattivo seguente. Per eseguire il codice in Azure Cloud Shell, selezionare **Prova** nell'angolo superiore di qualsiasi blocco di codice.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Se il provider di risorse `Microsoft.IoTCentral` non è ancora stato registrato per la sottoscrizione, è possibile registrarlo con il codice interattivo seguente. Per eseguire il codice in Azure Cloud Shell, selezionare **Prova** nell'angolo superiore di qualsiasi blocco di codice.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

Usare il codice seguente per distribuire il connettore Azure per il servizio FHIR usando il modello ARM.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Se il provider di risorse `Microsoft.HealthcareApis` non è ancora stato registrato per la sottoscrizione, è possibile registrarlo con il codice interattivo seguente. Per eseguire il codice in Azure Cloud Shell, selezionare **Prova** nell'angolo superiore di qualsiasi blocco di codice.

```azurecli-interactive
az extension add --name healthcareapis
```

Se il provider di risorse `Microsoft.IoTCentral` non è ancora stato registrato per la sottoscrizione, è possibile registrarlo con il codice interattivo seguente.

```azurecli-interactive
az extension add --name azure-iot
```

Usare il codice seguente per distribuire il connettore Azure per il servizio FHIR usando il modello ARM.

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Il completamento della distribuzione richiede alcuni minuti. Prendere nota dei nomi per l'API di Azure per il servizio FHIR, per l'applicazione IoT Central di Azure e per il gruppo di risorse usato per esaminare le risorse distribuite in un secondo momento.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

# <a name="portal"></a>[Portale](#tab/azure-portal)

Seguire questa procedura per visualizzare una panoramica del nuovo servizio API di Azure per FHIR:

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **API di Azure per FHIR**.

2. Nell'elenco dei servizi FHIR selezionare il nuovo servizio. Verrà visualizzata la pagina **Panoramica** del nuovo servizio API di Azure per FHIR.

3. Per verificare che sia stato effettuato il provisioning del nuovo account API FHIR, selezionare il collegamento accanto a **endpoint di metadati FHIR** per recuperare la dichiarazione di funzionalità dell'API FHIR. Il collegamento è nel formato `https://<service-name>.azurehealthcareapis.com/metadata`. Se è stato effettuato il provisioning dell'account, viene visualizzato un file JSON di grandi dimensioni.

4. Per verificare che sia stato effettuato il provisioning del nuovo connettore Azure Internet per FHIR, selezionare il **connettore Internet (anteprima)** dal menu di spostamento a sinistra per aprire la pagina dei **connettori** Internet. La pagina deve mostrare il connettore Azure per il provisioning per FHIR con il valore di *stato* *online*, il valore *Connections* come *1* e il mapping dei *dispositivi* e il *mapping di FHIR* mostrano l'icona *operazione riuscita* .

5. Nella [portale di Azure](https://portal.azure.com)cercare e selezionare **IOT Central applicazioni**.

6. Nell'elenco di applicazioni di IoT Central selezionare il nuovo servizio. Verrà visualizzata la pagina **Panoramica** per la nuova applicazione IoT Central.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Eseguire il codice interattivo seguente per visualizzare i dettagli del servizio API di Azure per FHIR. È necessario immettere il nome del nuovo servizio FHIR e del gruppo di risorse.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> Il connettore Azure per FHIR non fornisce i comandi di PowerShell in questo momento. Per convalidare il provisioning corretto del connettore Azure per FHIR, usare il processo di convalida fornito nella scheda **portale** .

Eseguire il codice interattivo seguente per visualizzare i dettagli relativi all'applicazione IoT Central di Azure. È necessario immettere il nome della nuova applicazione IoT Central e del gruppo di risorse.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[CLI](#tab/CLI)

Eseguire il codice interattivo seguente per visualizzare i dettagli del servizio API di Azure per FHIR. È necessario immettere il nome del nuovo servizio FHIR e del gruppo di risorse.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> Il connettore Azure per FHIR non fornisce al momento i comandi dell'interfaccia della riga di comando. Per convalidare il provisioning corretto del connettore Azure per FHIR, usare il processo di convalida fornito nella scheda **portale** .

Eseguire il codice interattivo seguente per visualizzare i dettagli relativi all'applicazione IoT Central di Azure. È necessario immettere il nome della nuova applicazione IoT Central e del gruppo di risorse.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Connettere i dati IoT al connettore Azure IoT per FHIR (anteprima)
> [!IMPORTANT]
> Il modello Mapping dispositivo fornito in questa guida è destinato all'uso con Esportazione dati (legacy) all'interno di IoT Central.

IoT Central applicazione non fornisce attualmente il modello ARM o i comandi PowerShell e CLI per impostare l'esportazione dei dati. Quindi, seguire le istruzioni riportate di seguito con portale di Azure.  

Una volta distribuita l'applicazione IoT Central, i due dispositivi simulati predefiniti inizieranno a generare dati di telemetria. Per questa esercitazione, verranno inseriti i dati di telemetria dal simulatore *Benda intelligente per i segni vitali* in FHIR tramite il connettore Azure IoT per FHIR. Per esportare i dati di Internet delle cose nel connettore Azure per FHIR, è consigliabile [configurare un'esportazione dei dati (legacy) in IOT Central](../../iot-central/core/howto-export-data-legacy.md). Nella pagina Esportazione dati (legacy):
- Selezionare *Hub eventi di Azure* come destinazione dell'esportazione.
- Selezionare il valore *Usa stringa di connessione* per il campo **Spazio dei nomi di Hub eventi**.
- Nel campo **Stringa di connessione** specificare la stringa di connessione del connettore Azure IoT per FHIR ottenuta in un passaggio precedente.
- Lasciare l'opzione **Telemetria** impostata su *Sì* per il campo **Dati da esportare**.

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Visualizzare i dati del dispositivo nell'API di Azure per FHIR

È possibile visualizzare le risorse di osservazione basate su FHIR create da connettore Azure per FHIR nel server FHIR usando il post. Configurare [Postman per accedere all'API di Azure per FHIR](access-fhir-postman-tutorial.md) e inviare una richiesta `GET` a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` per visualizzare le risorse di osservazione FHIR con il valore di frequenza cardiaca.

> [!TIP]
> Verificare che l'utente disponga dell'accesso appropriato al piano dati dell'API di Azure per FHIR. Usare il [controllo degli accessi in base al ruolo di Azure](configure-azure-rbac.md) per assegnare i ruoli del piano dati necessari.

---

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse per eliminare tutte le risorse contenute al suo interno.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Gruppi di risorse**.

2. Nell'elenco dei gruppi di risorse scegliere il nome del gruppo di risorse.

3. Nella pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.

4. Nella finestra di dialogo di conferma, digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

Per un'esercitazione dettagliata che illustra il processo di creazione di un modello di Resource Manager, vedere [Esercitazione: Creare e distribuire il primo modello di Azure Resource Manager](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata distribuita la funzionalità del connettore Azure IoT per FHIR nella risorsa API di Azure per FHIR. Per altre informazioni sul connettore Azure IoT per FHIR, selezionare uno dei passaggi successivi seguenti:

Comprendere le diverse fasi del flusso di dati nel connettore Azure IoT per FHIR.

>[!div class="nextstepaction"]
>[Flusso di dati del connettore Azure IoT per FHIR](iot-data-flow.md)

Informazioni su come configurare il connettore IoT usando un dispositivo e i modelli di mapping FHIR.

>[!div class="nextstepaction"]
>[Modelli di mapping del connettore Azure IoT per FHIR](iot-mapping-templates.md)

*Nel portale di Azure si fa riferimento al connettore Azure IoT per FHIR come Connettore IoT (anteprima). FHIR è un marchio registrato di HL7, usato con l'autorizzazione di HL7.
