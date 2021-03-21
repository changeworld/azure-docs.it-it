---
title: Distribuire il contenuto tramite FTP/S
description: Informazioni su come distribuire l'app nel servizio app di Azure usando FTP o FTPS. Migliorare la sicurezza del sito Web disabilitando l'FTP non crittografato.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045803"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuire l'app nel servizio app di Azure usando FTP/S

Questo articolo illustra come usare FTP o FTPS per distribuire l'app Web, il back-end dell'app per dispositivi mobili o l'app per le API nel [servizio app di Azure](./overview.md).

L'endpoint FTP/S per l'app è già attivo. Non è necessaria alcuna configurazione per abilitare la distribuzione FTP/S.

> [!NOTE]
> La pagina **centro di sviluppo (classica)** nell'portale di Azure, che è l'esperienza di distribuzione precedente, sarà deprecata a partire da marzo 2021. Questa modifica non avrà alcun effetto sulle impostazioni di distribuzione esistenti nell'app ed è possibile continuare a gestire la distribuzione delle app nella pagina **centro distribuzione** .

## <a name="get-deployment-credentials"></a>Ottenere le credenziali di distribuzione

1. Seguire le istruzioni in [configurare le credenziali di distribuzione per il servizio app Azure](deploy-configure-credentials.md) per copiare le credenziali dell'ambito dell'applicazione o per impostare le credenziali dell'ambito dell'utente. È possibile connettersi all'endpoint FTP/S dell'app usando entrambe le credenziali.

1. Creare il nome utente FTP nel formato seguente, a seconda dell'ambito delle credenziali scelto:

    | Ambito applicazione | Ambito utente |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    Nel servizio app l'endpoint FTP/S è condiviso tra le app. Poiché le credenziali dell'ambito utente non sono collegate a una risorsa specifica, è necessario anteporre il nome utente dell'ambito utente con il nome dell'app, come illustrato in precedenza.

## <a name="get-ftps-endpoint"></a>Ottenere l'endpoint FTP/S
    
# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Nella stessa pagina di gestione dell'app in cui sono state copiate le credenziali di distribuzione (credenziali FTP del **centro distribuzione**  >  ), copiare l' **endpoint FTPS**.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Eseguire il comando [AZ webapp Deployment list-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) . Nell'esempio seguente viene usato un [percorso JMES](https://jmespath.org/) per estrarre gli endpoint FTP/S dall'output.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Ogni app ha due endpoint FTP/S, uno è di lettura/scrittura, mentre l'altro è di sola lettura ( `profileName` contiene `ReadOnly` ) ed è per gli scenari di ripristino dei dati. Per distribuire i file con FTP, copiare l'URL dell'endpoint di lettura/scrittura.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Eseguire il comando [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) . Nell'esempio seguente viene estratto l'endpoint FTP/S dall'output XML.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Distribuire file in Azure

1. Nel client FTP, ad esempio [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/) o [WinSCP](https://winscp.net/index.php), usare le specifiche informazioni raccolte per connettersi all'app.
2. Copiare i file e la struttura di directory corrispondente nella directory [**/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) in Azure o nella directory **/site/wwwroot/App_Data/Jobs/** per i processi Web.
3. Passare all'URL dell'app per verificare che l'applicazione venga eseguita correttamente. 

> [!NOTE] 
> Diversamente dalle [distribuzioni basate su git](deploy-local-git.md) e dalla [distribuzione zip](deploy-zip.md), la distribuzione FTP non supporta l'automazione della compilazione, ad esempio: 
>
> - Ripristino delle dipendenze (ad esempio, automazioni NuGet, NPM, PIP e Composer)
> - Compilazione di file binari .NET
> - Generazione di web.config ([esempio di Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Generare questi file necessari manualmente nel computer locale e quindi distribuirli insieme all'app.
>

## <a name="enforce-ftps"></a>Applicare FTPS

Per una maggiore sicurezza, è consigliabile consentire il protocollo FTP solo su TLS/SSL. È anche possibile disabilitare FTP e FTPS se non si usa la distribuzione FTP.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Nella pagina delle risorse dell'app, in [portale di Azure](https://portal.azure.com), selezionare **configurazione**  >  **Impostazioni generali** dal dispositivo di spostamento a sinistra.

2. Per disabilitare l'FTP non crittografato, selezionare **FTPS solo** nello **stato FTP**. Per disabilitare completamente FTP e FTPS, selezionare **disattivato**. Al termine fare clic su **Salva**. Se si usa **solo FTPS**, è necessario applicare TLS 1,2 o versione successiva passando al pannello delle **Impostazioni TLS/SSL** dell'app Web. TLS 1.0 e 1.1 non sono supportati con **Solo FTPS**.

    ![Disabilitare FTP/FTPS](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Eseguire il comando [AZ webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) con l' `--ftps-state` argomento.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

I valori possibili per `--ftps-state` sono `AllAllowed` (FTP e FTPS abilitato), `Disabled` (FTP e FTPS disabilitati) e `FtpsOnly` (solo FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Eseguire il comando [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) con il `-FtpsState` parametro.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

I valori possibili per `--ftps-state` sono `AllAllowed` (FTP e FTPS abilitato), `Disabled` (FTP e FTPS disabilitati) e `FtpsOnly` (solo FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Risolvere i problemi di distribuzione FTP

- [Come è possibile risolvere i problemi di distribuzione FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Non è possibile eseguire l'FTP e pubblicare il codice. Come è possibile risolvere il problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Come è possibile connettersi a FTP nel Servizio app di Azure tramite la modalità passiva?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Come è possibile risolvere i problemi di distribuzione FTP?

Il primo passo per la risoluzione dei problemi di distribuzione FTP consiste nell'isolare un problema di distribuzione da un problema di runtime dell'applicazione.

Un problema di distribuzione comporta in genere che non venga distribuito alcun file o che vengano distribuiti file non corretti all'app. È possibile risolverlo analizzando la distribuzione FTP o selezionando un percorso di distribuzione alternativo (ad esempio il controllo del codice sorgente).

Un problema di runtime dell'applicazione determina in genere la distribuzione del set corretto di file all'app, ma un comportamento dell'app non corretto. È possibile risolverlo esaminando il comportamento di runtime del codice e analizzando i percorsi di errore specifici.

Per determinare se un problema è di distribuzione o di runtime, vedere [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues) (Problemi di distribuzione e di runtime).

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Non sono in grado di eseguire il protocollo FTP e di pubblicare il codice. Come è possibile risolvere il problema?
Verificare di aver immesso il [nome host](#get-ftps-endpoint) e le [credenziali](#get-deployment-credentials)corretti. Verificare anche che le porte FTP seguenti nel computer non siano bloccate da un firewall:

- Porta di connessione di controllo FTP: 21, 990
- Porta di connessione dati FTP: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Come è possibile connettersi a FTP nel Servizio app di Azure tramite la modalità passiva?
Servizio app di Azure supporta la connessione tramite la modalità attiva e passiva. La modalità passiva è preferibile perché le macchine di distribuzione sono in genere protette da un firewall (nel sistema operativo o nell'ambito di una rete domestica o aziendale). Vedere un'[esempio della documentazione WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="more-resources"></a>Altre risorse

* [Distribuzione dell'archivio Git locale nel servizio app di Azure](deploy-local-git.md)
* [Credenziali per la distribuzione del Servizio app di Azure](deploy-configure-credentials.md)
* [Esempio: creare un'app Web e distribuire i file con FTP (interfaccia della riga di comando di Azure)](./scripts/cli-deploy-ftp.md).
* [Esempio: caricare i file in un'app Web tramite FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).
