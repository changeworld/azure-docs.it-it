---
title: Integrazione del gateway applicazione con gli endpoint di servizio - Servizio app di Azure | Microsoft Docs
description: Descrive come il gateway applicazione si integra con Servizio app di Azure con gli endpoint di servizio.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: f1d517ba37bbef95d1863485c8c3b6313f196c11
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374914"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integrazione del gateway applicazione con gli endpoint di servizio
Esistono tre varianti del servizio app che richiedono una configurazione leggermente diversa dell'integrazione con gateway applicazione di Azure. Le variazioni includono il normale servizio app, noto anche come multi-tenant, internal Load Balancer (ILB) ambiente del servizio app (ASE) e ambiente del servizio app esterno. Questo articolo illustra come configurarlo con il servizio app (multi-tenant) e illustra le considerazioni relative al servizio app esterno e al servizio app esterno.

## <a name="integration-with-app-service-multi-tenant"></a>Integrazione con il servizio app (multi-tenant)
Il servizio app (multi-tenant) ha un endpoint pubblico con connessione Internet. Usando [gli endpoint di servizio](../../virtual-network/virtual-network-service-endpoints-overview.md) è possibile consentire il traffico solo da una subnet specifica all'interno di una rete virtuale di Azure e bloccare tutto il resto. Nello scenario seguente questa funzionalità verrà utilizzata per garantire che un'istanza del servizio app possa ricevere il traffico solo da una specifica istanza del gateway applicazione.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="Il diagramma mostra il flusso di Internet verso un gateway applicazione in una rete virtuale di Azure e il flusso da qui tramite un'icona del firewall alle istanze di app nel servizio app.":::

Questa configurazione è in due parti oltre alla creazione del servizio app e del gateway applicazione. La prima parte è l'abilitazione degli endpoint di servizio nella subnet della rete virtuale in cui viene distribuito il gateway applicazione. Gli endpoint di servizio garantiranno che tutto il traffico di rete che lascia la subnet verso il servizio app sia contrassegnato con l'ID subnet specifico. La seconda parte è impostare una restrizione di accesso dell'app Web specifica per garantire che sia consentito solo il traffico contrassegnato con questo ID subnet specifico. È possibile configurarlo usando strumenti diversi a seconda delle preferenze.

## <a name="using-azure-portal"></a>Uso del portale di Azure
Con portale di Azure, seguire quattro passaggi per effettuare il provisioning e configurare l'installazione. Se sono disponibili risorse esistenti, è possibile ignorare i primi passaggi.
1. Creare un servizio app usando una delle guide introduttive nella documentazione del servizio app, ad esempio Guida introduttiva [a .NET Core](../quickstart-dotnetcore.md)
2. Creare un gateway applicazione usando la guida [introduttiva del portale,](../../application-gateway/quick-create-portal.md)ma ignorare la sezione Aggiungere destinazioni back-end.
3. Configurare [il servizio app come back-end nel gateway applicazione,](../../application-gateway/configure-web-app-portal.md)ma ignorare la sezione Limitare l'accesso.
4. Creare infine la [restrizione di accesso usando gli endpoint di servizio](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule).

È ora possibile accedere al servizio app tramite il gateway applicazione, ma se si prova ad accedere direttamente al servizio app, si dovrebbe ricevere un errore HTTP 403 che indica che il sito Web è stato arrestato.

![Screenshot che mostra il testo di un errore 403 - Accesso negato.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
Il [modello Resource Manager di distribuzione eseguirà][template-app-gateway-app-service-complete] il provisioning di uno scenario completo. Lo scenario è costituito da un'istanza del servizio app bloccata con endpoint di servizio e restrizioni di accesso per ricevere solo il traffico dal gateway applicazione. Il modello include molte impostazioni predefinite intelligenti e suffissi univoci aggiunti ai nomi delle risorse per essere semplice. Per eseguirne l'override, è necessario clonare il repo o scaricare il modello e modificarlo. 

Per applicare il modello, è possibile usare il pulsante Distribuisci in Azure disponibile nella descrizione del modello oppure usare PowerShell o l'interfaccia della riga di comando appropriata.

## <a name="using-azure-command-line-interface"></a>Uso dell'interfaccia della riga di comando di Azure
[L'esempio dell'interfaccia della](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) riga di comando di Azure eseguirà il provisioning di un servizio app bloccato con endpoint di servizio e restrizioni di accesso per ricevere solo il traffico dal gateway applicazione. Se è sufficiente isolare il traffico verso un servizio app esistente da un gateway applicazione esistente, è sufficiente il comando seguente.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Nella configurazione predefinita, il comando garantisce sia la configurazione dell'endpoint di servizio nella subnet che la restrizione di accesso nel servizio app.

## <a name="considerations-for-ilb-ase"></a>Considerazioni per l'ase del server del database ilb
L'ambiente del servizio app ilb non è esposto a Internet e il traffico tra l'istanza e un gateway applicazione è quindi già isolato nella rete virtuale. La procedura [seguente consente di configurare](../environment/integrate-with-application-gateway.md) un'istanza del servizio di gestione del servizio di gestione del traffico ilb e di integrarla con un gateway applicazione usando portale di Azure. 

Per assicurarsi che solo il traffico proveniente dalla subnet del gateway applicazione raggiunga l'interfaccia del servizio app, è possibile configurare un gruppo di sicurezza di rete (NSG) che influisce su tutte le app Web nell'interfaccia del servizio app. Per il gruppo di protezione di rete è possibile specificare l'intervallo IP della subnet e facoltativamente le porte (80/443). Assicurarsi di non eseguire l'override delle regole del gruppo di sicurezza di rete [necessarie](../environment/network-info.md#network-security-groups) per il corretto funzionamento dell'ase.

Per isolare il traffico verso una singola app Web, è necessario usare restrizioni di accesso basate su IP perché gli endpoint di servizio non funzioneranno per l'app app. L'indirizzo IP deve essere l'INDIRIZZO IP privato dell'istanza del gateway applicazione.

## <a name="considerations-for-external-ase"></a>Considerazioni per l'asser utente esterno
L'ambiente del servizio app esterno ha un servizio di bilanciamento del carico pubblico come il servizio app multi-tenant. Gli endpoint di servizio non funzionano per l'istanza del gateway applicazione ed è per questo motivo che è necessario usare restrizioni di accesso basate su IP usando l'indirizzo IP pubblico dell'istanza del gateway applicazione. Per creare un'istanza dell'interfaccia del servizio portale di Azure esterna, è possibile seguire questa [guida introduttiva](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager modello per uno scenario completo"

## <a name="considerations-for-kuduscm-site"></a>Considerazioni sul sito kudu/scm
Il sito scm, noto anche come kudu, è un sito amministratore, che esiste per ogni app Web. Non è possibile invertire il proxy del sito scm e molto probabilmente si vuole bloccarlo anche a singoli indirizzi IP o a una subnet specifica.

Se si vogliono usare le stesse restrizioni di accesso del sito principale, è possibile ereditare le impostazioni usando il comando seguente.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Se si desidera impostare singole restrizioni di accesso per il sito scm, è possibile aggiungere restrizioni di accesso usando il flag --scm-site come illustrato di seguito.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul ambiente del servizio app, vedere la [documentazione ambiente del servizio app .](/azure/app-service/environment)

Per proteggere ulteriormente l'app Web, le informazioni su Web Application Firewall nel gateway applicazione sono disponibili nella documentazione Web application firewall di Azure [.](../../web-application-firewall/ag/ag-overview.md)
