---
title: Creare l'ase del traffico del traffico ilb v1
description: Creare un ambiente del servizio app con un servizio di bilanciamento del carico interno (AMBIENTE DEL SERVIZIO APP). Questo documento è rivolto solo ai clienti che usano l'ambiente del servizio app v1 legacy.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: a6cc1cae640b97ecb3d95ee1e4f8ec34750e32d2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833005"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Come creare un ambiente del servizio app con servizio di bilanciamento del carico interno usando modelli di Azure Resource Manager

> [!NOTE] 
> Questo articolo riguarda l'ambiente del servizio app v1. È disponibile una versione più recente dell'ambiente del servizio app più facile da usare ed eseguita in un'infrastruttura più potente. Per altre informazioni sulla nuova versione, iniziare con [Introduzione all'ambiente del servizio app](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica
È possibile creare ambienti del servizio app con un indirizzo interno di rete virtuale anziché un indirizzo VIP pubblico.  L'indirizzo interno viene messo a disposizione da un componente di Azure denominato servizio di bilanciamento del carico interno (ILB).  È possibile creare un ambiente del servizio app con servizio di bilanciamento del carico interno usando il portale di Azure.  L'ambiente può anche essere creato con l'automazione, usando i modelli di Azure Resource Manager.  Questo articolo illustra i passaggi e la sintassi necessari per creare un ambiente del servizio app con servizio di bilanciamento del carico interno con i modelli di Azure Resource Manager.

L'automazione della creazione di un ambiente del servizio app con servizio di bilanciamento del carico interno prevede tre passaggi:

1. Viene prima creato l'ambiente del servizio app di base in una rete virtuale usando un indirizzo di servizio di bilanciamento del carico interno anziché un indirizzo VIP pubblico.  Nell'ambito di questo passaggio viene assegnato un nome di dominio radice all'ambiente del servizio app con servizio di bilanciamento del carico interno.
2. Dopo aver creato l'ase del servizio di gestione del traffico ilb, viene caricato un certificato TLS/SSL.  
3. Il certificato TLS/SSL caricato viene assegnato in modo esplicito all'ase del servizio di gestione del traffico ilb come certificato TLS/SSL "predefinito".  Questo certificato TLS/SSL verrà usato per il traffico TLS verso le app nell'ase del servizio app ilb quando le app vengono indirizzate usando il dominio radice comune assegnato all'app del servizio app (ad esempio `https://someapp.mycustomrootcomain.com` )

## <a name="creating-the-base-ilb-ase"></a>Creazione dell'ambiente del servizio app con servizio di bilanciamento del carico interno
Un modello di Azure Resource Manager di esempio e il file dei parametri associati sono disponibili in GitHub [qui][quickstartilbasecreate].

La maggior parte dei parametri del file *azuredeploy.parameters.json* è comune alla creazione sia dell'ambiente del servizio app con servizio di bilanciamento del carico interno che dell'ambiente del servizio app associato a un indirizzo VIP pubblico.  L'elenco seguente indica parametri importanti o specifici per la creazione di un ambiente del servizio app con servizio di bilanciamento del carico interno:

* *internalLoadBalancingMode:* nella maggior parte dei casi impostare questo valore su 3, ovvero il traffico HTTP/HTTPS sulle porte 80/443 e le porte del canale di controllo/dati in ascolto del servizio FTP nell'ambiente del servizio di bilanciamento del carico interno verranno associate a un indirizzo interno della rete virtuale allocata con bilanciamento del carico interno.  Se questa proprietà viene invece impostata su 2, solo le porte correlate al servizio FTP, ovvero i canali di controllo e i canali dei dati, saranno associate a un indirizzo del servizio di bilanciamento del carico interno, mentre il traffico HTTP/HTTPS rimarrà associato all'indirizzo VIP pubblico.
* *dnsSuffix*: questo parametro indica il dominio radice predefinito che verrà assegnato all'ambiente del servizio app.  Nella variante pubblica del servizio app di Azure, il dominio radice predefinito per tutte le app Web è *azurewebsites.net*.  Poiché tuttavia un ambiente del servizio app con servizio di bilanciamento del carico interno è interno alla rete virtuale di un cliente, non ha senso usare il dominio radice predefinito del servizio pubblico.  Un ambiente del servizio app con servizio di bilanciamento del carico interno deve invece avere un dominio radice predefinito appropriato per la rete virtuale interna dell'azienda.  Un'ipotetica azienda Contoso Corporation potrebbe ad esempio usare un dominio radice predefinito *internal-contoso.com* per le app che devono essere risolvibili e accessibili solo nella rete virtuale di Contoso. 
* *ipSslAddressCount*: questo parametro viene automaticamente impostato sul valore predefinito di 0 nel file *azuredeploy.json* perché gli ambienti del servizio app con servizio di bilanciamento del carico interno hanno un solo indirizzo del servizio di bilanciamento del carico interno.  Non sono disponibili indirizzi IP SSL espliciti per un ambiente del servizio app con servizio di bilanciamento del carico interno, quindi il pool di indirizzi IP SSL per un ambiente del servizio app con servizio di bilanciamento del carico interno deve essere impostato su zero, in caso contrario si verificherà un errore di provisioning. 

Dopo aver *compilatoazuredeploy.parameters.js* file per un'istanza del servizio di gestione del servizio di gestione del traffico ilb, è possibile creare l'ase del servizio di gestione del traffico ilb usando il frammento di codice di PowerShell seguente.  Modificare i percorsi dei file in modo che corrispondano Azure Resource Manager file modello nel computer.  Indicare anche i valori per il nome della distribuzione di Azure Resource Manager e il nome del gruppo di risorse.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Dopo aver inviato il modello di Azure Resource Manager, la creazione dell'ambiente del servizio app con servizio di bilanciamento del carico interno richiederà alcune ore.  Al termine della creazione, l'ambiente del servizio app con servizio di bilanciamento del carico interno verrà visualizzato nel portale nell'elenco di ambienti del servizio app per la sottoscrizione che ha attivato la distribuzione.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Caricamento e configurazione del certificato TLS/SSL "predefinito"
Dopo aver creato l'app del servizio app ILB, un certificato TLS/SSL deve essere associato all'app come uso "predefinito" del certificato TLS/SSL per stabilire connessioni TLS/SSL alle app.  Continuando con l'ipotetico esempio di Contoso Corporation, se il suffisso DNS predefinito dell'ambiente del servizio internal-contoso.com è *,* una connessione a richiede un certificato TLS/SSL valido per *`https://some-random-app.internal-contoso.com`* **.internal-contoso.com*. 

Esistono diversi modi per ottenere un certificato TLS/SSL valido, incluse le CA interne, l'acquisto di un certificato da un'autorità di certificazione esterna e l'uso di un certificato autofirmato.  Indipendentemente dall'origine del certificato TLS/SSL, gli attributi del certificato seguenti devono essere configurati correttamente:

* *Subject*: questo attributo deve essere impostato su **.your-root-domain-here.com*.
* *Nome alternativo soggetto*: questo attributo deve includere sia **.your-root-domain-here.com* che **.scm.your-root-domain-here.com*.  Il motivo della seconda voce è che le connessioni TLS al sito SCM/Kudu associato a ogni app verranno effettuate usando un indirizzo nel *formato your-app-name.scm.your-root-domain-here.com*.

Con un certificato TLS/SSL valido, sono necessari due passaggi preliminari aggiuntivi.  Il certificato TLS/SSL deve essere convertito/salvato come file con estensione pfx.  Il file con estensione pfx deve includere tutti i certificati intermedi e radice ed essere protetto con una password.

Il file con estensione pfx risultante deve quindi essere convertito in una stringa base64 perché il certificato TLS/SSL verrà caricato usando un Azure Resource Manager modello.  Poiché i modelli di Azure Resource Manager sono file di testo, il file con estensione pfx deve essere convertito in una stringa con codifica Base64 per essere incluso come parametro del modello.

Il frammento di codice di PowerShell seguente illustra un esempio di generazione di un certificato autofirmato, esportazione del certificato come file con estensione pfx, conversione del file con estensione pfx in una stringa con codifica Base64 e salvataggio della stringa con codifica Base64 in un file separato.  Il codice di PowerShell per la codifica Base64 è stato adattato dal [blog degli script di PowerShell][examplebase64encoding].

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Dopo che il certificato TLS/SSL è stato generato correttamente e convertito in una stringa con codifica Base64, è possibile usare il modello di Azure Resource Manager di esempio in GitHub per la configurazione del certificato [TLS/SSL][configuringDefaultSSLCertificate] predefinito.

I parametri del file *azuredeploy.parameters.json* sono elencati di seguito:

* *appServiceEnvironmentName:* nome dell'ase del servizio app con servizio di ilb in fase di configurazione.
* *existingAseLocation:* stringa di testo contenente l'area di Azure in cui è stato distribuito l'ambiente del servizio app ilb.  Ad esempio "Stati Uniti centro-meridionali".
* *pfxBlobString:* rappresentazione di stringa con codifica based64 del file con estensione pfx.  Usando il frammento di codice indicato in precedenza, la stringa contenuta in "exportedcert.pfx.b64" dovrà essere copiata e incollata come valore dell'attributo *pfxBlobString* .
* *password:* password usata per proteggere il file con estensione pfx.
* *certificateThumbprint:* identificazione personale del certificato.  Se si recupera questo valore da PowerShell(ad esempio, *$certificate. Identificazione personale* del frammento di codice precedente). È possibile usare il valore così come è.  Se tuttavia si copia il valore dalla finestra di dialogo del certificato di Windows, rimuovere gli spazi estranei.  *CertificateThumbprint* dovrebbe essere simile a: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName:* identificatore di stringa descrittivo di propria scelta usato per l'identità del certificato.  Il nome viene usato come parte dell'identificatore Azure Resource Manager univoco per l'entità *Microsoft.Web/certificates* che rappresenta il certificato TLS/SSL.  Il nome **deve** terminare con il suffisso seguente: \_yourASENameHere_InternalLoadBalancingASE.  Questo suffisso viene usato dal portale per indicare che il certificato è usato per la protezione di un ambiente del servizio app abilitato al bilanciamento del carico interno.

Un esempio abbreviato di *azuredeploy.parameters.json* è illustrato di seguito:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appServiceEnvironmentName": {
            "value": "yourASENameHere"
        },
        "existingAseLocation": {
            "value": "East US 2"
        },
        "pfxBlobString": {
            "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
        },
        "password": {
            "value": "PASSWORDGOESHERE"
        },
        "certificateThumbprint": {
            "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
        },
        "certificateName": {
            "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
        }
    }
}
```

Dopo aver *compilatoazuredeploy.parameters.js* file, è possibile configurare il certificato TLS/SSL predefinito usando il frammento di codice di PowerShell seguente.  Modificare i percorsi dei file in modo che corrispondano Azure Resource Manager file modello nel computer.  Indicare anche i valori per il nome della distribuzione di Azure Resource Manager e il nome del gruppo di risorse.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Dopo l'invio del modello di Azure Resource Manager, ci vorranno circa 40 minuti per ogni front-end dell'ambiente del servizio app per apportare la modifica.  Per un ambiente del servizio app di dimensioni predefinite che usa due front-end, ad esempio, l'operazione richiederà all'incirca un'ora e venti minuti.  Durante l'esecuzione del modello, l'ambiente del servizio app non potrà essere ridimensionato.  

Al termine del modello, è possibile accedere alle app nell'ambiente del servizio app ilb tramite HTTPS e le connessioni verranno protette usando il certificato TLS/SSL predefinito.  Il certificato TLS/SSL predefinito verrà usato quando le app nell'ambiente del servizio app ilb vengono indirizzate usando una combinazione del nome dell'applicazione e del nome host predefinito.  Ad *`https://mycustomapp.internal-contoso.com`* esempio, usare il certificato TLS/SSL predefinito per **.internal-contoso.com*.

Tuttavia, proprio come le app in esecuzione nel servizio multi-tenant pubblico, gli sviluppatori possono anche configurare nomi host personalizzati per le singole app e quindi configurare associazioni di certificato TLS/SSL SNI univoche per le singole app.  

## <a name="getting-started"></a>Guida introduttiva
Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

