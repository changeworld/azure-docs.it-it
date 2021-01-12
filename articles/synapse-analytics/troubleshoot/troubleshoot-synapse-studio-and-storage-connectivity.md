---
title: Risolvere i problemi di connettività tra Synapse Studio e archiviazione
description: Risolvere i problemi di connettività tra Synapse Studio e archiviazione
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d570b4a8df5d59cf8828985bee20852d6bc79b1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117062"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Risolvere i problemi di connettività tra Azure Synapse Analytics Synapse Studio e la risorsa di archiviazione

In sinapsi studio è possibile esplorare le risorse dei dati presenti nella risorsa di archiviazione collegata. Questa guida consente di risolvere i problemi di connettività quando si tenta di accedere alle risorse di dati. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Caso #1: l'account di archiviazione non dispone delle autorizzazioni appropriate

Se l'account di archiviazione non dispone delle autorizzazioni appropriate, non è possibile espandere la struttura di archiviazione tramite "data", > "Linked" in sinapsi Studio. Vedere la schermata del sintomo del problema riportata di seguito. 

Il messaggio di errore dettagliato può variare, ma il significato generale del messaggio di errore è: "questa richiesta non è autorizzata per eseguire questa operazione".

Nel nodo archiviazione collegata:  
![Problema di connettività di archiviazione 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

Nel nodo del contenitore di archiviazione:  
![Problema di connettività di archiviazione 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**Soluzione**: per assegnare l'account al ruolo appropriato, vedere [usare la portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Case #2: non è stato possibile inviare la richiesta al server di archiviazione

Quando si seleziona la freccia per espandere la struttura di archiviazione in "dati"-> "collegato" in sinapsi studio, è possibile che venga visualizzato il problema "REQUEST_SEND_ERROR" nel pannello di sinistra. Vedere la schermata del sintomo del problema seguente:

Nel nodo archiviazione collegata:  
![Problema di connettività dell'archiviazione 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

Nel nodo del contenitore di archiviazione:  
![Problema di connettività di archiviazione 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Questo problema può essere dovuto a diversi motivi:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>La risorsa di archiviazione si trova dietro un vNet e un endpoint privato di archiviazione deve configurare

**Soluzione**: in questo caso, è necessario configurare l'endpoint privato di archiviazione per l'account di archiviazione. Per informazioni su come configurare l'endpoint privato di archiviazione per vNet, vedere [usare la portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../security/how-to-connect-to-workspace-from-restricted-network.md).

È possibile usare il comando "nslookup \<storage-account-name\> . DFS.Core.Windows.NET" per verificare la connettività dopo la configurazione dell'endpoint privato di archiviazione. Deve restituire una stringa simile a: " \<storage-account-name\> . privatelink.DFS.Core.Windows.NET".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>La risorsa di archiviazione non si trova dietro un vNet ma l'endpoint del servizio BLOB (Azure AD) non è accessibile a causa del firewall configurato

**Soluzione**: in questo caso, è necessario aprire l'account di archiviazione nell'portale di Azure. Nella barra di spostamento a sinistra scorrere verso il basso fino a **supporto e risoluzione dei problemi** e selezionare **Verifica connettività** per verificare lo stato di connettività del **servizio BLOB (Azure ad)** . Se non è accessibile, seguire la guida promossa per verificare la configurazione dei **firewall e delle reti virtuali** nella pagina dell'account di archiviazione. Per altre informazioni sui firewall di archiviazione, vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](../../storage/common/storage-network-security.md).

### <a name="other-issues-to-check"></a>Altri problemi da controllare 

* La risorsa di archiviazione a cui si accede è Azure Data Lake Storage Gen2 ed è dietro un firewall e vNet (con l'endpoint privato di archiviazione configurato) allo stesso tempo.
* La risorsa contenitore a cui si accede è stata eliminata o non esiste.
* Cross-tenant: il tenant dell'area di lavoro usato dall'utente per l'accesso non è uguale al tenant dell'account di archiviazione. 


## <a name="next-steps"></a>Passaggi successivi
Se la procedura precedente non consente di risolvere il problema, [creare un ticket di supporto](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).