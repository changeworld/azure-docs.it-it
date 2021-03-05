---
title: Isolamento rete
description: Gli utenti possono limitare l'accesso pubblico alle risorse QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 4e9b45c72e5fb4cbd9e548727faf8946e3e5ba8f
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125087"
---
# <a name="recommended-settings-for-network-isolation"></a>Impostazioni consigliate per l'isolamento rete

Attenersi alla procedura seguente per limitare l'accesso pubblico alle risorse QnA Maker. Proteggere una risorsa di servizi cognitivi dall'accesso pubblico [configurando la rete virtuale](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-cognitive-search-resource"></a>Limitare l'accesso alla risorsa ricerca cognitiva

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

Configurazione di ricerca cognitiva come endpoint privato all'interno di un VNET. Quando viene creata un'istanza di ricerca durante la creazione di una risorsa di QnA Maker, è possibile forzare ricerca cognitiva a supportare una configurazione di endpoint privato creata interamente all'interno di un VNet del cliente.

Per usare un endpoint privato, è necessario creare tutte le risorse nella stessa area.

* Risorsa QnA Maker
* nuova risorsa ricerca cognitiva
* nuova risorsa rete virtuale

Completare i passaggi seguenti nel [portale di Azure](https://portal.azure.com):

1. Creare una [risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
2. Creare una nuova risorsa ricerca cognitiva con la connettività endpoint (dati) impostata su _privata_. Creare la risorsa nella stessa area della risorsa QnA Maker creata nel passaggio 1. Altre informazioni sulla [creazione di una risorsa ricerca cognitiva](../../../search/search-create-service-portal.md), quindi usare questo collegamento per passare direttamente alla [pagina di creazione della risorsa](https://ms.portal.azure.com/#create/Microsoft.Search).
3. Creare una nuova [risorsa di rete virtuale](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
4. Configurare VNET nella risorsa del servizio app creata nel passaggio 1 di questa procedura. Creare una nuova voce DNS in VNET per la nuova risorsa ricerca cognitiva creata nel passaggio 2. all'indirizzo IP del ricerca cognitiva.
5. [Associare il servizio app alla nuova risorsa ricerca cognitiva](../how-to/set-up-qnamaker-service-azure.md) creata nel passaggio 2. Quindi, è possibile eliminare la risorsa ricerca cognitiva originale creata nel passaggio 1.
    
Nel [portale di QnA Maker](https://www.qnamaker.ai/)creare la prima Knowledge base.

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

[Creare endpoint privati](../reference-private-endpoint.md) nella risorsa ricerca di Azure.

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>Limitare l'accesso al servizio app (Runtime QnA)

È possibile aggiungere IP al servizio app allow per limitare l'accesso o configurare il servizio app ambiente per ospitare QnA Maker servizio app.

#### <a name="add-ips-to-app-service-allowlist"></a>Aggiungi indirizzi IP al servizio app consentiti

1. Consentire il traffico solo da indirizzi IP di servizi cognitivi. Questi sono già inclusi nel tag del servizio `CognitiveServicesManagement` . Questa operazione è necessaria per la creazione di API (Create/Update KB) per richiamare il servizio app e aggiornare di conseguenza il servizio ricerca di Azure. Vedere [altre informazioni sui tag del servizio.](../../../virtual-network/service-tags-overview.md)
2. Assicurarsi di consentire anche altri punti di ingresso come il servizio Azure bot, il portale QnA Maker e così via per l'accesso all'API di stima "GenerateAnswer".
3. Per aggiungere gli intervalli di indirizzi IP a un oggetto Allow, attenersi alla procedura seguente:

   1. Scaricare [gli intervalli IP per tutti i tag del servizio](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Selezionare gli indirizzi IP di "CognitiveServicesManagement".
   3. Passare alla sezione rete della risorsa del servizio app e fare clic sull'opzione "Configura restrizione di accesso" per aggiungere gli indirizzi IP a un oggetto allow.

È anche presente uno script automatizzato per eseguire la stessa operazione per il servizio app. È possibile trovare lo [script di PowerShell per configurare un oggetto Allow](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) su GitHub. È necessario immettere l'ID sottoscrizione, il gruppo di risorse e il nome effettivo del servizio app come parametri dello script. Eseguendo lo script, gli IP verranno aggiunti automaticamente al servizio app allow.

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
4.  Creare un'istanza del servizio cognitivo QnA Maker (Microsoft. CognitiveServices/accounts) usando Azure Resource Manager, in cui QnA Maker endpoint deve essere impostato sull'endpoint del servizio app creato in precedenza (https://mywebsite.myase.p.azurewebsite.net).
    
---
