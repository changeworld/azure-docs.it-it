---
title: Risolvere i problemi di connettività tra Synapse Studio e archiviazione
description: Risolvere i problemi di connettività tra Synapse Studio e archiviazione
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d5f79131608315f7e1c05cbfc0117300eea6c511
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566274"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Risolvere i problemi di connettività tra Azure Synapse Analytics Synapse Studio e la risorsa di archiviazione

In Synapse Studio è possibile esplorare le risorse di dati che si trovano nella risorsa di archiviazione collegata. Questa guida consente di risolvere i problemi di connettività quando si tenta di accedere alle risorse dati. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Caso #1: L'account di archiviazione non dispone delle autorizzazioni appropriate

Se l'account di archiviazione non dispone delle autorizzazioni appropriate, non è possibile espandere la struttura di archiviazione tramite "Data" --> "Linked" in Synapse Studio. Vedere la schermata del sintomo del problema riportata di seguito. 

Il messaggio di errore dettagliato può variare, ma il significato generale del messaggio di errore è: "Questa richiesta non è autorizzata a eseguire questa operazione".

Nel nodo di archiviazione collegato:  
![Problema di connettività di archiviazione 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

Nel nodo contenitore di archiviazione:  
![Problema di connettività di archiviazione 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**SOLUZIONE:** per assegnare l'account al ruolo appropriato, vedere Usare il portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati di BLOB [e code](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Caso #2: Non è stato possibile inviare la richiesta al server di archiviazione

Quando si seleziona la freccia per espandere la struttura di archiviazione in "Dati" --> "Collegato" in Synapse Studio, è possibile che venga visualizzato il problema "REQUEST_SEND_ERROR" nel pannello sinistro. Vedere lo screenshot del sintomo del problema riportato di seguito:

Nel nodo di archiviazione collegato:  
![Problema di connettività di archiviazione 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

Nel nodo contenitore di archiviazione:  
![Problema di connettività di archiviazione 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Questo problema può essere stato risolto per diversi motivi:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>La risorsa di archiviazione si trova dietro una rete virtuale ed è necessario configurare un endpoint privato di archiviazione

**SOLUZIONE:** in questo caso, è necessario configurare l'endpoint privato di archiviazione per l'account di archiviazione. Per informazioni su come configurare l'endpoint privato di archiviazione per la rete virtuale, vedere Usare il portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB [e coda.](../security/how-to-connect-to-workspace-from-restricted-network.md)

È possibile usare il comando "nslookup .dfs.core.windows.net" per controllare la connettività dopo la configurazione \<storage-account-name\> dell'endpoint privato di archiviazione. Deve restituire una stringa simile a: \<storage-account-name\> ".privatelink.dfs.core.windows.net".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>La risorsa di archiviazione non si trova dietro una rete virtuale, ma l'endpoint del servizio BLOB (Azure AD) non è accessibile a causa del firewall configurato

**SOLUZIONE:** in questo caso, è necessario aprire l'account di archiviazione nel portale di Azure. Nel riquadro di spostamento a sinistra scorrere verso il basso fino a **Supporto** e risoluzione dei problemi e selezionare **Verifica** connettività per controllare lo stato di connettività del servizio BLOB **(Azure AD).** Se non è accessibile, seguire la guida promossa per controllare la configurazione di **Firewall** e reti virtuali nella pagina dell'account di archiviazione. Per altre informazioni sui firewall di archiviazione, vedere [Configurare Archiviazione di Azure firewall e reti virtuali.](../../storage/common/storage-network-security.md)

### <a name="other-issues-to-check"></a>Altri problemi da verificare 

* La risorsa di archiviazione a cui si sta accedendo è Azure Data Lake Storage Gen2 e si trova dietro un firewall e una rete virtuale (con l'endpoint privato di archiviazione configurato) contemporaneamente.
* La risorsa contenitore a cui si accede è stata eliminata o non esiste.
* Cross-tenant: il tenant dell'area di lavoro usato dall'utente per l'accesso non corrisponde al tenant dell'account di archiviazione. 


## <a name="next-steps"></a>Passaggi successivi
Se i passaggi precedenti non consentono di risolvere il problema, [creare un ticket di supporto](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).