---
title: Errori comuni e problemi noti relativi alla migrazione ai servizi cloud di Azure (supporto esteso)
description: Panoramica degli errori comuni durante la migrazione da servizi cloud (versione classica) a servizio cloud (supporto esteso)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 58203730793202649c401d96182469fa1eac6ef1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286942"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Errori comuni e problemi noti relativi alla migrazione ai servizi cloud di Azure (supporto esteso)

Questo articolo illustra i problemi noti e gli errori comuni che possono verificarsi quando si esegue la migrazione da servizi cloud (versione classica) a servizi cloud (supporto esteso). 

## <a name="known-issues"></a>Problemi noti
I problemi seguenti sono noti e vengono risolti.

| Problemi noti | Strategia di riduzione del rischio | 
|---|---|
| Le istanze del ruolo riavviano UD da UD dopo l'esito positivo del commit. | L'operazione di riavvio segue lo stesso metodo del sistema operativo guest mensile rollout. Non eseguire il commit della migrazione dei servizi cloud con una singola istanza del ruolo o con un riavvio.| 
| Portale di Azure Impossibile leggere lo stato di migrazione dopo l'aggiornamento del browser. | Eseguire di nuovo Validate e preparare l'operazione per tornare allo stato di migrazione originale. | 
| Certificato visualizzato come risorsa segreta in Key Vault. | Dopo la migrazione, ricaricare il certificato come risorsa di certificato per semplificare l'operazione di aggiornamento nei servizi cloud (supporto esteso). | 
| Le etichette di distribuzione non vengono salvate come tag come parte della migrazione. | Per gestire queste informazioni, creare manualmente i tag dopo la migrazione.
| Il nome del gruppo di risorse è in tutte le maiuscole. | Senza effetti. La soluzione non è ancora disponibile. |
| Il nome del blocco su servizi cloud (supporto esteso) non è corretto. | Senza effetti. La soluzione non è ancora disponibile. | 
| Il nome dell'indirizzo IP non è corretto nel pannello del portale di servizi cloud (supporto esteso). | Senza effetti. La soluzione non è ancora disponibile. | 
| Nome DNS non valido visualizzato per l'indirizzo IP virtuale dopo l'operazione di aggiornamento in un servizio cloud migrato. | Senza effetti. La soluzione non è ancora disponibile. | 
| Una volta completata la preparazione, il collegamento di una nuova distribuzione di servizi cloud (supporto esteso) come Swapable non è consentito. | Non collegare un nuovo servizio cloud come scambiabile a un servizio cloud preparato. | 
| È necessario aggiornare i messaggi di errore. | Senza effetti. | 

## <a name="common-migration-errors"></a>Errori comuni di migrazione
Errori comuni di migrazione e procedure di mitigazione. 

| Messaggio di errore | Dettagli | 
|---|---|
| Il tipo di risorsa non è stato trovato nello spazio dei nomi `Microsoft.Compute` per la versione API ' 2020-10-01-Preview '. | [Registrare la sottoscrizione per il](in-place-migration-overview.md#setup-access-for-migration) flag funzionalità cloudservices per accedere all'anteprima pubblica. | 
| errore interno del server. ripetere la richiesta. | Ripetere l'operazione, utilizzare [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) oppure contattare il supporto tecnico. | 
| Il server ha rilevato un errore imprevisto durante il tentativo di allocazione delle risorse di rete per il servizio cloud. ripetere la richiesta. | Ripetere l'operazione, utilizzare [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) oppure contattare il supporto tecnico. | 
| Deployment Deployment-Name in Cloud Service Cloud-Service-Name deve trovarsi all'interno di una rete virtuale da migrare. | La distribuzione non si trova in una rete virtuale. Per ulteriori informazioni, fare riferimento a [questo](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) documento. | 
| La migrazione del nome della distribuzione della distribuzione nel servizio Cloud Cloud-Service-Name non è supportata perché si trova in Region Region-Name. Aree consentite: [elenco delle aree disponibili]. | L'area non è ancora supportata per la migrazione. | 
| Non è possibile eseguire la migrazione del nome della distribuzione della distribuzione nel servizio Cloud Cloud-Service-Name perché non sono presenti subnet associate al ruolo o al nome del ruolo. Associare tutti i ruoli a una subnet, quindi riprovare a eseguire la migrazione del servizio cloud. | Aggiornare la distribuzione del servizio cloud (versione classica) inserendola in una subnet prima della migrazione. |  
| Non è possibile eseguire la migrazione del nome della distribuzione della distribuzione nel servizio Cloud Cloud-Service-Name perché la distribuzione richiede almeno una funzionalità che non è registrata nella sottoscrizione in Azure Resource Manager. Registrare tutte le funzionalità necessarie per eseguire la migrazione della distribuzione. Funzionalità mancanti: [elenco di funzionalità mancanti]. | Contattare il supporto tecnico per ottenere la registrazione dei flag funzionalità. | 
| Non è possibile eseguire la migrazione della distribuzione perché il servizio cloud della distribuzione ha due slot occupati. La migrazione dei servizi cloud è supportata solo per le distribuzioni che sono l'unica distribuzione nel servizio cloud. Eliminare l'altra distribuzione nel servizio cloud per procedere con la migrazione di questa distribuzione. | Per altri dettagli, vedere l'elenco di [scenari non supportati](in-place-migration-overview.md#unsupported-configurations--migration-scenarios) . | 
| Distribuzione distribuzione-il nome in servizio ospitato cloud-service-name è in stato intermedio: stato. La migrazione non è consentita. | È in corso la creazione, l'eliminazione o l'aggiornamento della distribuzione. Attendere il completamento dell'operazione e riprovare. | 
| Il nome della distribuzione della distribuzione nel servizio ospitato Cloud-Service-Name contiene IP riservati, ma nessun nome IP riservato. Per risolvere il problema, aggiornare il nome dell'indirizzo IP riservato o contattare il servizio Microsoft Azure Service Desk. | Aggiornare la distribuzione del servizio cloud. | 
| Il nome della distribuzione della distribuzione nel servizio ospitato Cloud-Service-Name contiene IP riservati riservati-IP-Name, ma nessun endpoint nell'IP riservato. Per risolvere questo problema, aggiungere almeno un endpoint all'indirizzo IP riservato. | Aggiungi endpoint a IP riservato. | 
| È in corso il commit della migrazione della distribuzione {0} in servizio ospitato {1} che non può essere modificata fino a quando non viene completata correttamente.  | Operazione di attesa o ripetizione dei tentativi. | 
| La migrazione della distribuzione {0} in servizio ospitato {1} è in corso di interruzione e non può essere modificata fino a quando non viene completata correttamente. | Operazione di attesa o ripetizione dei tentativi. |
| Una o più macchine virtuali nella distribuzione {0} in servizio ospitato {1} è in fase di aggiornamento. Non è possibile eseguire la migrazione fino a quando l'operazione precedente non viene completata correttamente. Riprovare tra qualche minuto. | Attendere il completamento dell'operazione. | 
| La migrazione non è supportata per la distribuzione {0} in servizio ospitato {1} perché usa le funzionalità seguenti non ancora supportate per la migrazione: distribuzione non vnet.| La distribuzione non si trova in una rete virtuale. Per ulteriori informazioni, fare riferimento a [questo](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) documento. | 
| Il nome della rete virtuale non può essere null o vuoto. | Specificare il nome della rete virtuale nel corpo della richiesta REST | 
| Il nome della subnet non può essere null o vuoto. | Specificare il nome della subnet nel corpo della richiesta REST. | 
| È deve essere impostato su uno dei valori seguenti: default, New o existing. | Specificare la proprietà è nel corpo della richiesta REST. | 
| Opzione di destinazione VNet predefinita non implementata. | Il valore "default" non è supportato per la proprietà è nel corpo della richiesta REST. | 
| Non {0} è possibile eseguire la migrazione della distribuzione perché CSPKG non è disponibile. | Aggiornare la distribuzione e riprovare. | 
| La subnet con ID ' {0} ' si trova in una posizione diversa rispetto alla distribuzione ' {1} ' nel servizio ospitato ' {2} '. Il percorso della subnet è' {3} ' e il percorso del servizio ospitato è' {4} '.  Specificare una subnet nella stessa posizione della distribuzione. | Aggiornare il servizio cloud in modo che sia la subnet che il servizio cloud si trovino nella stessa posizione prima della migrazione. | 
| La migrazione della distribuzione {0} in servizio ospitato {1} è in corso di interruzione e non può essere modificata fino a quando non viene completata correttamente. | Attendere il completamento dell'interruzione o riprovare a interrompere. Utilizzare [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) oppure contattare il supporto tecnico. | 
| {0}La distribuzione in servizio ospitato {1} non è stata preparata per la migrazione. | Eseguire preparare il servizio cloud prima di eseguire l'operazione di commit. | 
| UnknownExceptionInEndExecute: Contract. Assert non riuscito: rgName è null o vuoto: eccezione ricevuta in EndExecute che non è un RdfeException. |   Utilizzare [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) oppure contattare il supporto tecnico. | 
| UnknownExceptionInEndExecute: un'attività è stata annullata: eccezione ricevuta in EndExecute che non è un RdfeException. | Utilizzare [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) oppure contattare il supporto tecnico. | 
| XrpVirtualNetworkMigrationError: errore di migrazione della rete virtuale. | Utilizzare [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) oppure contattare il supporto tecnico. | 
| La distribuzione {0} in servizio ospitato {1}  appartiene alla rete virtuale {2} . Eseguire la migrazione della rete virtuale {2} per eseguire la migrazione di questo servizio ospitato {1} . | Vedere [migrazione della rete virtuale](in-place-migration-technical-details.md#virtual-network-migration). | 
| La quota corrente per il nome della risorsa in Azure Resource Manager non è sufficiente per completare la migrazione. La quota corrente è {0} , la richiesta aggiuntiva è {1} . Archiviare una richiesta di supporto per aumentare la quota e ripetere la migrazione dopo la generazione della quota.    | Per richiedere un aumento della quota, seguire i canali appropriati: <br>[Aumento della quota per le risorse di rete](../azure-portal/supportability/networking-quota-requests.md) <br>[Aumento della quota per le risorse di calcolo](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sui requisiti di migrazione, vedere la pagina relativa ai [Dettagli tecnici sulla migrazione a servizi cloud di Azure (supporto esteso)](in-place-migration-technical-details.md)
