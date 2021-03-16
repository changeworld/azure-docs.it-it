---
title: Isolamento rete
description: Gli utenti possono limitare l'accesso pubblico alle risorse QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: afb396bc364a2fa2db923fbcbe6bfe1b7aedbc26
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467545"
---
# <a name="recommended-settings-for-network-isolation"></a>Impostazioni consigliate per l'isolamento rete

Attenersi alla procedura seguente per limitare l'accesso pubblico alle risorse QnA Maker. Proteggere una risorsa di servizi cognitivi dall'accesso pubblico [configurando la rete virtuale](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-app-service-qna-runtime"></a>Limitare l'accesso al servizio app (Runtime QnA)

È possibile aggiungere indirizzi IP all'elenco Consenti del servizio app per limitare l'accesso o configurare ambiente del servizio app per ospitare QnA Maker servizio app.

#### <a name="add-ips-to-app-service-allow-list"></a>Aggiungi indirizzi IP all'elenco Consenti del servizio app

1. Consentire il traffico solo da indirizzi IP di servizi cognitivi. Questi sono già inclusi nel tag del servizio `CognitiveServicesManagement` . Questa operazione è necessaria per la creazione di API (Create/Update KB) per richiamare il servizio app e aggiornare di conseguenza il servizio ricerca di Azure. Vedere [altre informazioni sui tag del servizio.](../../../virtual-network/service-tags-overview.md)
2. Assicurarsi di consentire anche altri punti di ingresso come il servizio Azure bot, il portale QnA Maker e così via per l'accesso all'API di stima "GenerateAnswer".
3. Attenersi alla procedura seguente per aggiungere gli intervalli di indirizzi IP a un elenco Consenti:

   1. Scaricare [gli intervalli IP per tutti i tag del servizio](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Selezionare gli indirizzi IP di "CognitiveServicesManagement".
   3. Passare alla sezione rete della risorsa del servizio app e fare clic sull'opzione "Configura restrizione di accesso" per aggiungere gli indirizzi IP a un elenco Consenti.

È anche presente uno script automatizzato per eseguire la stessa operazione per il servizio app. È possibile trovare lo [script di PowerShell per configurare un elenco Consenti](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) in GitHub. È necessario immettere l'ID sottoscrizione, il gruppo di risorse e il nome effettivo del servizio app come parametri dello script. Eseguendo lo script, gli IP verranno aggiunti automaticamente all'elenco Consenti del servizio app.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Configurare ambiente del servizio app per ospitare QnA Maker servizio app
    
Il ambiente del servizio app (ASE) può essere usato per ospitare QnA Maker servizio app. Eseguire i passaggi seguenti:

1. Creare una ambiente del servizio app e contrassegnarla come "External". Per istruzioni, seguire l' [esercitazione](../../../app-service/environment/create-external-ase.md) .
2.  Creare un servizio app all'interno del ambiente del servizio app.
    1. Controllare la configurazione del servizio app e aggiungere "PrimaryEndpointKey" come impostazione dell'applicazione. Il valore di ' PrimaryEndpointKey ' deve essere impostato su " \<app-name\> -PrimaryEndpointKey". Il nome dell'app è definito nell'URL del servizio app. Ad esempio, se l'URL del servizio app è "mywebsite.myase.p.azurewebsite.net", il nome dell'app è "sito Web". In questo caso, il valore di ' PrimaryEndpointKey ' deve essere impostato su "sito Web-PrimaryEndpointKey".
    2. Creare un servizio di ricerca di Azure.
    3. Verificare che le impostazioni di ricerca di Azure e app siano configurate in modo appropriato. 
          Seguire questa [esercitazione](../reference-app-service.md?tabs=v1#app-service).
3.  Aggiornare il gruppo di sicurezza di rete associato al ambiente del servizio app
    1. Aggiornare le regole di sicurezza in ingresso predefinite in base alle esigenze.
    2. Aggiungere una nuova regola di sicurezza in ingresso con origine come ' tag di servizio ' e tag del servizio di origine come ' CognitiveServicesManagement '.
       
    ![eccezioni per le porte in ingresso](../media/inbound-ports.png)

4.  Creare un'istanza del servizio cognitivo QnA Maker (Microsoft. CognitiveServices/accounts) usando Azure Resource Manager, in cui QnA Maker endpoint deve essere impostato sull'endpoint del servizio app creato in precedenza (https://mywebsite.myase.p.azurewebsite.net).
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>Limitare l'accesso alla risorsa ricerca cognitiva

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

Ricerca cognitiva istanza può essere isolata tramite un endpoint privato dopo la creazione delle risorse QnA Maker. Per le connessioni a endpoint privati è necessario un VNet tramite il quale è possibile accedere all'istanza di servizio di ricerca. 

Se il servizio app QnA Maker viene limitato utilizzando una ambiente del servizio app, utilizzare lo stesso VNet per creare una connessione dell'endpoint privato all'istanza di ricerca cognitiva. Creare una nuova voce DNS in VNet per eseguire il mapping dell'endpoint ricerca cognitiva all'indirizzo IP dell'endpoint privato ricerca cognitiva. 

Se non viene usato un ambiente del servizio app per il servizio app QnAMaker, creare prima di tutto una nuova risorsa VNet e quindi creare la connessione all'endpoint privato per l'istanza di ricerca cognitiva. In questo caso, il servizio app di QnA Maker deve [essere integrato con VNet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet) per connettersi all'istanza di ricerca cognitiva. 

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

[Creare endpoint privati](../reference-private-endpoint.md) nella risorsa ricerca di Azure.

---
