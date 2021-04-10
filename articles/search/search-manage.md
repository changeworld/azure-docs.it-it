---
title: Amministrazione dei servizi nel portale
titleSuffix: Azure Cognitive Search
description: Gestire un servizio di ricerca cognitiva di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure, usando il portale di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 98e0137c8e48696737cd5d8d1fd4d3de925b9f7f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579799"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Amministrazione dei servizi per Azure ricerca cognitiva nel portale di Azure

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Interfaccia della riga di comando di Azure](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portale](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure ricerca cognitiva è un servizio di ricerca completamente gestito basato sul cloud, usato per creare un'esperienza di ricerca avanzata nelle app personalizzate. Questo articolo illustra le attività amministrative che è possibile eseguire nel [portale di Azure](https://portal.azure.com) per un servizio di ricerca che è già stato creato. Il portale consente di eseguire tutte le [attività di gestione](#management-tasks) correlate al servizio, nonché la gestione e l'esplorazione dei contenuti. Di conseguenza, il portale offre un ampio spettro di accesso a tutti gli aspetti delle operazioni del servizio di ricerca.

Ogni servizio di ricerca viene gestito come risorsa autonoma. La figura seguente mostra le pagine del portale per un singolo servizio di ricerca gratuita denominato "demo-Search-svc". Sebbene si possa essere abituati a usare Azure PowerShell o l'interfaccia della riga di comando di Azure per la gestione dei servizi, è opportuno acquisire familiarità con gli strumenti e le funzionalità disponibili nelle pagine del portale. Alcune attività sono semplicemente più semplici e veloci da eseguire nel portale rispetto al codice. 

## <a name="overview-home-page"></a>Pagina Panoramica (Home)

La pagina panoramica è la pagina "Home" di ogni servizio. Di seguito, le aree sullo schermo racchiuse tra le caselle rosse indicano le attività, gli strumenti e i riquadri che possono essere usati spesso, specialmente se non si ha familiarità con il servizio.

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="Pagine del portale per un servizio di ricerca" border="true":::

| Area | Descrizione |
|------|-------------|
| 1  | La sezione **Essentials** Mostra le proprietà del servizio che includono l'endpoint usato durante la configurazione delle connessioni. Mostra anche i conteggi dei livelli, delle repliche e delle partizioni a colpo d'occhio. |
| 2 | Nella parte superiore della pagina è presente una serie di comandi per richiamare gli strumenti interattivi o gestire il servizio. Sia [i dati di importazione](search-get-started-portal.md) che [Esplora ricerche](search-explorer.md) vengono comunemente usati per la prototipazione e l'esplorazione. |
| 3 | Sotto la **sezione** informazioni di base, sono disponibili una serie di sottopagine a schede che consentono di accedere rapidamente alle statistiche di utilizzo, alle metriche di integrità dei servizi e all'accesso a tutti gli indici, gli indicizzatori, le origini dati e skillsets del servizio disponibili. Se si seleziona un indice o un altro oggetto, saranno disponibili ulteriori pagine per la visualizzazione della composizione, delle impostazioni e dello stato dell'oggetto (se applicabile). |
| 4 | A sinistra è possibile accedere ai collegamenti che aprono ulteriori pagine per accedere alle chiavi API utilizzate nelle connessioni, configurare il servizio, configurare la sicurezza, monitorare le operazioni, automatizzare le attività e ottenere supporto. |

### <a name="read-only-service-properties"></a>Proprietà del servizio di sola lettura

Alcuni aspetti di un servizio di ricerca sono determinati quando viene eseguito il provisioning del servizio e non è possibile modificarli:

* Nome del servizio (non è possibile rinominare un servizio di ricerca)
* Posizione del servizio (non è possibile spostare facilmente un servizio di ricerca intatto in un'altra area. Sebbene sia presente un modello, lo stato di un processo manuale è quello di trasferire il contenuto.
* Livello di servizio (non è possibile passare da S1 a S2, ad esempio, senza creare un nuovo servizio)

## <a name="management-tasks"></a>Attività di gestione

L'amministrazione del servizio è leggera per progettazione ed è spesso definita dalle attività che è possibile eseguire in relazione al servizio stesso:

* [Regolare la capacità](search-capacity-planning.md) aggiungendo o rimuovendo le repliche e le partizioni
* [Gestire le chiavi API](search-security-api-keys.md) usate per le operazioni di amministrazione e query
* [Controllare l'accesso alle operazioni di amministrazione](search-security-rbac.md) tramite la sicurezza basata sui ruoli
* [Configurare le regole del firewall IP](service-configure-firewall.md) per limitare l'accesso in base all'indirizzo IP
* [Configurare un endpoint privato](service-create-private-endpoint.md) tramite il collegamento privato di Azure e una rete virtuale privata
* [Monitorare l'integrità del servizio](search-monitor-usage.md): archiviazione, volumi di query e latenza

È inoltre possibile enumerare tutti gli oggetti creati nel servizio, ovvero indici, indicizzatori, origini dati, skillsets e così via. La pagina Panoramica del portale Mostra tutto il contenuto esistente nel servizio. La maggior parte delle operazioni in un servizio di ricerca sono correlate al contenuto.

Le stesse attività di gestione eseguite nel portale possono essere gestite anche a livello di codice tramite le [API REST di gestione](/rest/api/searchmanagement/), [AZ. search PowerShell](search-manage-powershell.md), [AZ Search Azure CLI Module](search-manage-azure-cli.md)e Azure SDK per .NET, Python, Java e JavaScript. Le attività amministrative sono completamente rappresentate nel portale e in tutte le interfacce programmatiche. Nessuna attività amministrativa specifica disponibile in una sola modalità.

Ricerca cognitiva sfrutta altri servizi di Azure per un monitoraggio e una gestione più approfonditi. Di per sé, gli unici dati archiviati all'interno del servizio di ricerca sono il contenuto dell'oggetto, ovvero indici, definizioni dell'indicizzatore e delle origini dati e altri oggetti. Le metriche segnalate alle pagine del portale vengono estratte da log interni in un ciclo di 30 giorni in sequenza. Per la conservazione dei log controllata dall'utente e per gli eventi aggiuntivi, è necessario [monitoraggio di Azure](../azure-monitor/index.yml). Per altre informazioni sulla configurazione della registrazione diagnostica per un servizio di ricerca, vedere [raccogliere e analizzare i dati di log](search-monitor-logs.md).

## <a name="administrator-permissions"></a>Autorizzazioni degli amministratori

Quando si apre la pagina di panoramica del servizio di ricerca, le autorizzazioni assegnate all'account determinano quali pagine sono disponibili. La pagina panoramica all'inizio dell'articolo mostra le pagine del portale che un amministratore o un collaboratore visualizzerà.

In una risorsa di Azure, i diritti amministrativi vengono concessi tramite assegnazioni di ruolo. Nel contesto di Azure ricerca cognitiva, le [assegnazioni di ruolo](search-security-rbac.md) determinano chi può allocare repliche e partizioni o gestire chiavi API, indipendentemente dal fatto che stiano usando il portale, [PowerShell](search-manage-powershell.md), l'interfaccia della riga di comando di [Azure](search-manage-azure-cli.md)o le [API REST di gestione](/rest/api/searchmanagement/search-howto-management-rest-api):

> [!TIP]
> Il provisioning o il ritiro delle autorizzazioni per il servizio in sé può essere effettuato da un amministratore o da un coamministratore della sottoscrizione di Azure. Usando i meccanismi a livello di Azure, è possibile bloccare una sottoscrizione o una risorsa per evitare l'eliminazione accidentale o non autorizzata del servizio di ricerca da parte degli utenti con diritti di amministratore. Per altre informazioni, vedere [bloccare le risorse per impedire l'eliminazione imprevista](../azure-resource-manager/management/lock-resources.md).

## <a name="next-steps"></a>Passaggi successivi

* Esaminare le [funzionalità di monitoraggio](search-monitor-usage.md) disponibili nel portale
* Automazione con [PowerShell](search-manage-powershell.md) o l' [interfaccia](search-manage-azure-cli.md) della riga di comando di Azure
* Esaminare le [funzionalità di sicurezza](search-security-overview.md) per proteggere il contenuto e le operazioni
* Abilitare la [registrazione diagnostica](search-monitor-logs.md) per monitorare i carichi di lavoro di query e indicizzazione