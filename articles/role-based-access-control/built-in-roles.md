---
title: Ruoli predefiniti di Azure - Controllo degli accessi in base al ruolo di Azure
description: Questo articolo descrive i ruoli predefiniti di Azure per il controllo degli accessi in base al ruolo di Azure. Elenca Actions, NotActions, DataActions e NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 04/09/2021
ms.custom: generated
ms.openlocfilehash: c89e6ed98e0a71f530cefda4cc1f42a27996d805
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518508"
---
# <a name="azure-built-in-roles"></a>Ruoli predefiniti di Azure

Il [controllo degli accessi in base al ruolo di Azure](overview.md) ha diversi ruoli predefiniti di Azure che è possibile assegnare a utenti, gruppi, entità servizio e identità gestite. Le assegnazioni di ruolo sono il modo in cui si controlla l'accesso alle risorse di Azure. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati di Azure](custom-roles.md). Per informazioni su come assegnare ruoli, vedere [Passaggi per assegnare un ruolo di Azure.](role-assignments-steps.md)

Questo articolo elenca i ruoli predefiniti di Azure. Per i ruoli di amministratore per Azure Active Directory (Azure AD), vedere Azure AD [ruoli predefiniti](../active-directory/roles/permissions-reference.md).

La tabella seguente fornisce una breve descrizione di ogni ruolo predefinito. Fare clic sul nome del ruolo per vedere l'elenco di `Actions`, `NotActions`, `DataActions` e `NotDataActions` per ogni ruolo. Per informazioni sul significato di queste azioni e su come si applicano ai piani di gestione e ai piani dati, vedere [Informazioni sulle definizioni dei ruoli di Azure](role-definitions.md).

## <a name="all"></a>Tutti

> [!div class="mx-tableFixed"]
> | Ruolo predefinito | Descrizione | ID |
> | --- | --- | --- |
> | **Generale** |  |  |
> | [Collaboratore](#contributor) | Concede l'accesso completo per gestire tutte le risorse, ma non consente di assegnare ruoli nel controllo degli accessi in base al ruolo di Azure, gestire le assegnazioni in Azure Blueprints o condividere raccolte di immagini. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Proprietario](#owner) | Concede l'accesso completo per gestire tutte le risorse, inclusa la possibilità di assegnare ruoli nel controllo degli accessi in base al ruolo di Azure. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Lettore](#reader) | Visualizza tutto risorse, ma non consente di apportare modifiche. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Amministratore accessi utente](#user-access-administrator) | Consente di gestire gli accessi utente alle risorse di Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Calcolo** |  |  |
> | [Collaboratore macchine virtuali classiche](#classic-virtual-machine-contributor) | Consente di gestire le macchine virtuali classiche, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Virtual Machine Administrator Login](#virtual-machine-administrator-login) (Accesso amministratore macchina virtuale) | Consente di visualizzare le macchine virtuali nel portale e di accedere come amministratore | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Collaboratore macchine virtuali](#virtual-machine-contributor) | Consente di gestire le macchine virtuali, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Virtual Machine User Login](#virtual-machine-user-login) (Accesso utente macchina virtuale) | Consente di visualizzare le macchine virtuali nel portale e di accedere come utente normale. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Rete** |  |  |
> | [Collaboratore endpoint rete CDN](#cdn-endpoint-contributor) | Può gestire gli endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Lettore endpoint rete CDN](#cdn-endpoint-reader) | Può visualizzare gli endpoint della rete CDN, ma non può apportare modifiche. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Collaboratore profilo rete CDN](#cdn-profile-contributor) | Può gestire i profili e i rispettivi endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Lettore profilo rete CDN](#cdn-profile-reader) | Può visualizzare i profili e i rispettivi endpoint della rete CDN, ma non può apportare modifiche. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Collaboratore reti virtuali classiche](#classic-network-contributor) | Consente di gestire le reti classiche, ma non di accedervi. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Collaboratore zona DNS](#dns-zone-contributor) | Consente di gestire le zone DNS e i set di record in DNS di Azure, ma non di controllare chi è autorizzato ad accedervi. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Collaboratore di rete](#network-contributor) | Consente di gestire le reti, ma non di accedervi. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [DNS privato Collaboratore zona](#private-dns-zone-contributor) | Consente di gestire le risorse di zona DNS private, ma non le reti virtuali a cui sono collegate. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Collaboratore Gestione traffico](#traffic-manager-contributor) | Consente di gestire i profili di Gestione traffico, ma non di controllare chi è autorizzato ad accedervi. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Collaboratore per Avere](#avere-contributor) | Può creare e gestire un cluster Avere vFXT. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Operatore di Avere](#avere-operator) | Usato dal cluster Avere vFXT per gestire il cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Collaboratore di backup](#backup-contributor) | Consente di gestire il servizio di backup, ma non di creare insiemi di credenziali e concedere l'accesso ad altri utenti | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operatore di backup](#backup-operator) | Consente di gestire i servizi di backup, ma non di rimuovere il backup, creare insiemi di credenziali e concedere l'accesso ad altri utenti. | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Lettore di backup](#backup-reader) | Può visualizzare i servizi di backup, ma non può apportare modifiche. | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Collaboratore account di archiviazione classico](#classic-storage-account-contributor) | Consente di gestire gli account di archiviazione classici, ma non di accedervi. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico](#classic-storage-account-key-operator-service-role) | Gli operatori della chiave dell'account di archiviazione classico sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione classici | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Collaboratore Data Box](#data-box-contributor) | Consente di gestire tutto il servizio Data Box, ad eccezione della concessione dell'accesso ad altri utenti. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Lettore Data Box](#data-box-reader) | Consente di gestire il servizio Data Box, ad eccezione della creazione di ordini, della modifica dei dettagli dell'ordine e della concessione dell'accesso ad altri utenti. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Sviluppatore di Data Lake Analytics](#data-lake-analytics-developer) | Consente di inviare, monitorare e gestire i propri processi, ma non di creare o eliminare account Data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Lettore e accesso ai dati](#reader-and-data-access) | Consente di visualizzare tutti gli elementi ma non consente di eliminare o creare un account di archiviazione o una risorsa contenuta. Consente anche l'accesso in lettura/scrittura a tutti i dati contenuti in un account di archiviazione tramite l'accesso alle chiavi dell'account di archiviazione. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Collaboratore account di archiviazione](#storage-account-contributor) | Consente di gestire gli account di archiviazione. Consente di accedere alla chiave dell'account, che può essere usata per accedere ai dati usando l'autorizzazione con chiave condivisa. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione](#storage-account-key-operator-service-role) | Consente di elencare e rigenerare le chiavi di accesso dell'account di archiviazione. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Collaboratore ai dati del BLOB di archiviazione](#storage-blob-data-contributor) | Consente di leggere, scrivere ed eliminare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Proprietario dei dati del BLOB di archiviazione](#storage-blob-data-owner) | Concede l'accesso completo ai contenitori e ai dati dei BLOB di Archiviazione di Azure, inclusa l'assegnazione del controllo di accesso POSIX. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Lettore dei dati del BLOB di archiviazione](#storage-blob-data-reader) | Consente di leggere ed elencare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegante di BLOB di archiviazione](#storage-blob-delegator) | Ottiene una chiave di delega utente, che può quindi essere usata per creare una firma di accesso condiviso per un contenitore o un BLOB firmato con credenziali di Azure AD. Per altre informazioni, vedere [Creare una firma di accesso condiviso di delega utente](/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Collaboratore per la condivisione SMB di dati per file di archiviazione](#storage-file-data-smb-share-contributor) | Consente l'accesso in lettura, scrittura ed eliminazione a file e directory nelle condivisioni file di Azure. Questo ruolo non ha un equivalente predefinito nei file server Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Collaboratore con privilegi elevati per la condivisione SMB di dati per file di archiviazione](#storage-file-data-smb-share-elevated-contributor) | Consente la lettura, scrittura, eliminazione e modifica degli ACL nei file e nelle directory delle condivisioni file di Azure. Questo ruolo è equivalente a un ACL di modifica della condivisione file nei file server Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Ruolo con autorizzazioni di lettura per la condivisione SMB di dati per file di archiviazione](#storage-file-data-smb-share-reader) | Consente l'accesso in lettura a file e directory nelle condivisioni file di Azure. Questo ruolo è equivalente a un ACL di lettura della condivisione file nei file server Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Collaboratore ai dati della coda di archiviazione](#storage-queue-data-contributor) | Lettura, scrittura ed eliminazione delle code e dei messaggi delle code di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Ruolo con autorizzazioni di elaborazione per i messaggi sui dati della coda di archiviazione](#storage-queue-data-message-processor) | Visualizzazione in anteprima, recupero ed eliminazione di un messaggio da una coda di Archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Mittente dei messaggi sui dati della coda di archiviazione](#storage-queue-data-message-sender) | Consente di aggiungere messaggi a una coda di Archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Ruolo con autorizzazioni di lettura per i dati della coda di archiviazione](#storage-queue-data-reader) | Consente di leggere ed elencare le code e i messaggi delle code di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Mappe di Azure Collaboratore dati](#azure-maps-data-contributor) | Concede l'accesso per l'accesso in lettura, scrittura ed eliminazione ai dati correlati al mapping da un account mappe di Azure. | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Lettore di dati per Mappe di Azure](#azure-maps-data-reader) | Concede l'accesso per la lettura dei dati correlati alle mappe da un account Mappe di Azure. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Azure Spring Cloud lettore dati](#azure-spring-cloud-data-reader) | Consentire l'accesso in lettura Azure Spring Cloud dati | b5537268-8956-4941-a8f0-646150406f0c |
> | [Collaboratore servizi di ricerca](#search-service-contributor) | Consente di gestire i servizi di Ricerca, ma non di accedervi. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Lettore AccessKey di SignalR](#signalr-accesskey-reader) | Leggere le chiavi di accesso al servizio SignalR | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [Server app SignalR (anteprima)](#signalr-app-server-preview) | Consente al server app di accedere al servizio SignalR con le opzioni di autenticazione di AAD. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [Collaboratore SignalR](#signalr-contributor) | Creare, leggere, aggiornare ed eliminare risorse del servizio SignalR | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [Collaboratore serverless SignalR (anteprima)](#signalr-serverless-contributor-preview) | Consente all'app di accedere al servizio in modalità serverless con le opzioni di autenticazione di AAD. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [Proprietario del servizio SignalR (anteprima)](#signalr-service-owner-preview) | Accesso completo alle SERVIZIO AZURE SIGNALR REST | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [Lettore del servizio SignalR (anteprima)](#signalr-service-reader-preview) | Accesso in sola lettura Servizio Azure SignalR API REST | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Collaboratore piani Web](#web-plan-contributor) | Consente di gestire i piani Web per i siti Web, ma non di accedervi. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Collaboratore siti Web](#website-contributor) | Consente di gestire i siti Web (non i piani Web), ma non di accedervi. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Contenitori** |  |  |
> | [AcrDelete](#acrdelete) | acr delete | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | firmatario immagine acr | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | lettore di dati di quarantena acr | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | writer di dati di quarantena acr | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Ruolo di amministratore del cluster del servizio Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Elencare l'azione delle credenziali di amministratore del cluster. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Ruolo di utente del cluster del servizio Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Elencare l'azione delle credenziali di utente del cluster. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [servizio Azure Kubernetes collaboratore](#azure-kubernetes-service-contributor-role) | Concede l'accesso ai cluster di lettura e servizio Azure Kubernetes scrittura | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [servizio Azure Kubernetes controllo degli accessi in base al ruolo](#azure-kubernetes-service-rbac-admin) | Consente di gestire tutte le risorse nel cluster/spazio dei nomi, ad eccezione dell'aggiornamento o dell'eliminazione delle quote e degli spazi dei nomi delle risorse. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [servizio Azure Kubernetes amministratore del cluster controllo degli accessi in base al ruolo](#azure-kubernetes-service-rbac-cluster-admin) | Consente di gestire tutte le risorse nel cluster. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [servizio Azure Kubernetes lettore controllo degli accessi in base al ruolo](#azure-kubernetes-service-rbac-reader) | Consente l'accesso in sola lettura per visualizzare la maggior parte degli oggetti in uno spazio dei nomi. Non consente la visualizzazione di ruoli o associazioni di ruoli. Questo ruolo non consente la visualizzazione dei segreti, poiché la lettura del contenuto di Segreti consente l'accesso alle credenziali serviceAccount nello spazio dei nomi, che consentirebbe l'accesso api come qualsiasi ServiceAccount nello spazio dei nomi (una forma di escalation dei privilegi). L'applicazione di questo ruolo nell'ambito del cluster consente l'accesso a tutti gli spazi dei nomi. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [servizio Azure Kubernetes RBAC Writer](#azure-kubernetes-service-rbac-writer) | Consente l'accesso in lettura/scrittura alla maggior parte degli oggetti in uno spazio dei nomi. Questo ruolo non consente la visualizzazione o la modifica di ruoli o associazioni di ruolo. Tuttavia, questo ruolo consente l'accesso ai segreti e l'esecuzione di pod come qualsiasi ServiceAccount nello spazio dei nomi, quindi può essere usato per ottenere i livelli di accesso API di qualsiasi ServiceAccount nello spazio dei nomi. L'applicazione di questo ruolo nell'ambito del cluster consente l'accesso a tutti gli spazi dei nomi. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Database** |  |  |
> | [Ruolo Lettore dell'account Cosmos DB](#cosmos-db-account-reader-role) | Può leggere i dati degli account Azure Cosmos DB. Vedere [Collaboratore account DocumentDB](#documentdb-account-contributor) per la gestione degli account Azure Cosmos DB. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operatore di Cosmos DB](#cosmos-db-operator) | Consente di gestire gli account Azure Cosmos DB, ma non di accedere ai dati contenuti negli stessi. Impedisce l'accesso a chiavi dell'account e stringhe di connessione. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Può inviare una richiesta di ripristino per un database di Cosmos DB o un contenitore per un account | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | Può eseguire l'azione di ripristino per Cosmos DB account di database con modalità di backup continuo | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [Collaboratore account DocumentDB](#documentdb-account-contributor) | È in grado di gestire account Azure Cosmos DB. Azure Cosmos DB era precedentemente noto come DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Collaboratore cache Redis](#redis-cache-contributor) | Consente di gestire le cache Redis, ma non di accedervi. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Collaboratore database SQL](#sql-db-contributor) | Consente di gestire i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza o i rispettivi server SQL padre. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Collaboratore per Istanza gestita di SQL](#sql-managed-instance-contributor) | Consente di gestire le istanze gestite di SQL e la configurazione di rete necessaria, ma non consente l'accesso ad altri utenti. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Gestione della sicurezza SQL](#sql-security-manager) | Consente di gestire i criteri relativi alla sicurezza di server e database SQL, ma non di accedervi. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Collaboratore SQL Server](#sql-server-contributor) | Consente di gestire i server e i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analisi** |  |  |
> | [Proprietario dei dati di Hub eventi di Azure](#azure-event-hubs-data-owner) | Consente l'accesso completo alle risorse di Hub eventi di Azure. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Ricevitore dei dati di Hub eventi di Azure](#azure-event-hubs-data-receiver) | Consente l'accesso per la ricezione alle risorse dell'Hub eventi di Azure. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Mittente dei dati di Hub eventi di Azure](#azure-event-hubs-data-sender) | Consente l'accesso per l'invio alle risorse dell'Hub eventi di Azure. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Collaboratore Data Factory](#data-factory-contributor) | Consente di creare e gestire data factory, oltre alle risorse figlio in esse contenute. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Pulizia dati](#data-purger) | Eliminare dati privati da un'area di lavoro Log Analytics. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operatore di cluster HDInsight](#hdinsight-cluster-operator) | Consente di leggere e modificare le configurazioni dei cluster HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Collaboratore Servizi di dominio HDInsight](#hdinsight-domain-services-contributor) | Può leggere, creare, modificare ed eliminare operazioni correlate ai Servizi di dominio necessarie per HDInsight Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Collaboratore di Log Analytics](#log-analytics-contributor) | Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Lettore di Log Analytics](#log-analytics-reader) | Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Purview Data Curatrice](#purview-data-curator) | Il creatore di dati Microsoft.Purview può creare, leggere, modificare ed eliminare oggetti dati del catalogo e stabilire relazioni tra gli oggetti. Questo ruolo è in anteprima e soggetto a modifiche. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Lettore dati purview](#purview-data-reader) | Il lettore di dati Microsoft.Purview può leggere gli oggetti dati del catalogo. Questo ruolo è in anteprima e soggetto a modifiche. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Amministratore dell'origine dati purview](#purview-data-source-administrator) | L'amministratore dell'origine dati Microsoft.Purview può gestire le origini dati e le analisi dei dati. Questo ruolo è in anteprima e soggetto a modifiche. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Collaboratore del Registro schemi (anteprima)](#schema-registry-contributor-preview) | Lettura, scrittura ed eliminazione di gruppi e schemi del Registro schemi. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Lettore del Registro schemi (anteprima)](#schema-registry-reader-preview) | Lettura ed elenco di gruppi e schemi del Registro schemi. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockchain** |  |  |
> | [Accesso ai nodi di tipo membro della blockchain (anteprima)](#blockchain-member-node-access-preview) | Consente l'accesso ai nodi di tipo membro della blockchain | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Intelligenza artificiale e Machine Learning** |  |  |
> | [Collaboratore Servizi cognitivi](#cognitive-services-contributor) | Consente di creare, leggere, aggiornare, eliminare e gestire le chiavi di Servizi cognitivi. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Collaboratore Visione personalizzata cognitivi](#cognitive-services-custom-vision-contributor) | Accesso completo al progetto, inclusa la possibilità di visualizzare, creare, modificare o eliminare progetti. | c1ff6cc2-c111-46fe-8896-e0ef812ad9f3 |
> | [Distribuzione Visione personalizzata servizi cognitivi](#cognitive-services-custom-vision-deployment) | Pubblicare, annullare la pubblicazione o esportare modelli. La distribuzione può visualizzare il progetto ma non può eseguire l'aggiornamento. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Etichettatore Visione personalizzata Servizi cognitivi](#cognitive-services-custom-vision-labeler) | Visualizzare, modificare le immagini di training e creare, aggiungere, rimuovere o eliminare i tag immagine. Gli etichettatori possono visualizzare il progetto, ma non possono aggiornare altro che le immagini e i tag di training. | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Lettore di servizi cognitivi Visione personalizzata](#cognitive-services-custom-vision-reader) | Azioni di sola lettura nel progetto. I lettori non possono creare o aggiornare il progetto. | 93586559-c37d-4a6b-ba08-b9f0940c2d73 |
> | [Cognitive Services Visione personalizzata Trainer](#cognitive-services-custom-vision-trainer) | Visualizzare, modificare i progetti ed eseguire il training dei modelli, inclusa la possibilità di pubblicare, annullare la pubblicazione, esportare i modelli. Gli istruttori non possono creare o eliminare il progetto. | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Ruolo con autorizzazioni di lettura per i dati di Servizi cognitivi (anteprima)](#cognitive-services-data-reader-preview) | Consente di leggere i dati di Servizi cognitivi. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Amministratore di servizi cognitivi Advisor metriche](#cognitive-services-metrics-advisor-administrator) | Accesso completo al progetto, inclusa la configurazione a livello di sistema. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Editor servizi cognitivi QnA Maker](#cognitive-services-qna-maker-editor) | È possibile creare, modificare, importare ed esportare una knowledge base. Non è possibile pubblicare o eliminare una KNOWLEDGE BASE. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [Lettore di servizi cognitivi QnA Maker](#cognitive-services-qna-maker-reader) | È possibile leggere e testare solo una knowledge base. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Utente Servizi cognitivi](#cognitive-services-user) | Consente di leggere ed elencare le chiavi di Servizi cognitivi. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Internet delle cose** |  |  |
> | [Amministratore dell'aggiornamento dei dispositivi](#device-update-administrator) | Consente l'accesso completo alle operazioni di gestione e contenuto | 02ca0879-e8e4-47a5-a61e-5c618b76e64a |
> | [Amministratore del contenuto dell'aggiornamento del dispositivo](#device-update-content-administrator) | Consente l'accesso completo alle operazioni sui contenuti | 0378884a-3af5-44ab-8323-f5b22f9f3c98 |
> | [Lettore di contenuto per l'aggiornamento del dispositivo](#device-update-content-reader) | Consente l'accesso in lettura alle operazioni sul contenuto, ma non consente di apportare modifiche | d1ee9a80-8b14-47f0-bdc2-f4a351625a7b |
> | [Amministratore delle distribuzioni degli aggiornamenti dei dispositivi](#device-update-deployments-administrator) | Consente l'accesso completo alle operazioni di gestione | e4237640-0e3d-4a46-8fda-70bc94856432 |
> | [Lettore distribuzioni aggiornamenti dispositivi](#device-update-deployments-reader) | Consente l'accesso in lettura alle operazioni di gestione, ma non consente di apportare modifiche | 49e2f5d2-7741-4835-8efa-19e1fe35e47f |
> | [Lettore di aggiornamenti del dispositivo](#device-update-reader) | Consente l'accesso in lettura alle operazioni di gestione e contenuto, ma non consente di apportare modifiche | e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f |
> | **Realtà mista** |  |  |
> | [Amministratore rendering remoto](#remote-rendering-administrator) | Fornisce all'utente funzionalità di conversione, gestione della sessione, rendering e diagnostica per Rendering remoto di Azure | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Client di rendering remoto](#remote-rendering-client) | Fornisce all'utente funzionalità di gestione sessione, rendering e diagnostica per Rendering remoto di Azure. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Collaboratore per l'account per gli ancoraggi spaziali](#spatial-anchors-account-contributor) | Consente di gestire gli ancoraggi nello spazio nell'account, ma non di eliminarli | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Proprietario dell'account per gli ancoraggi spaziali](#spatial-anchors-account-owner) | Consente di gestire gli ancoraggi nello spazio nell'account, incluse le operazioni di eliminazione | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Ruolo Lettore dell'account per gli ancoraggi spaziali](#spatial-anchors-account-reader) | Consente di individuare e leggere le proprietà degli ancoraggi nello spazio nell'account | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integrazione** |  |  |
> | [Collaboratore servizio Gestione API](#api-management-service-contributor) | Può gestire il servizio e le API. | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Ruolo operatore del servizio Gestione API](#api-management-service-operator-role) | Può gestire il servizio ma non le API. | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Ruolo lettura del servizio Gestione API](#api-management-service-reader-role) | Consente l'accesso di sola lettura al servizio e alle API. | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Proprietario dei dati di Configurazione dell'app](#app-configuration-data-owner) | Consente l'accesso completo ai dati di Configurazione dell'app. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Ruolo con autorizzazioni di lettura per i dati di Configurazione dell'app](#app-configuration-data-reader) | Consente l'accesso in lettura ai dati di Configurazione dell'app. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Proprietario dei dati del bus di servizio di Azure](#azure-service-bus-data-owner) | Consente l'accesso completo alle risorse del bus di servizio di Azure. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Ricevitore dei dati del bus di servizio di Azure](#azure-service-bus-data-receiver) | Consente l'accesso per la ricezione alle risorse del bus di servizio di Azure. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Mittente dei dati del bus di servizio di Azure](#azure-service-bus-data-sender) | Consente l'accesso per l'invio alle risorse del bus di servizio di Azure. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Proprietario della registrazione di Azure Stack](#azure-stack-registration-owner) | Consente di gestire le registrazioni di Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Collaboratore EventGrid](#eventgrid-contributor) | Consente di gestire le operazioni di EventGrid. | 1e241071-0855-49ea-94dc-649edcd759de |
> | [Collaboratore per sottoscrizioni di eventi di Griglia di eventi](#eventgrid-eventsubscription-contributor) | Consente di gestire le operazioni di sottoscrizione di eventi EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Ruolo con autorizzazioni di lettura per sottoscrizioni di eventi di Griglia di eventi](#eventgrid-eventsubscription-reader) | Consente di leggere le sottoscrizioni di eventi EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Collaboratore dati FHIR](#fhir-data-contributor) | Il ruolo consente all'utente o all'entità l'accesso completo ai dati FHIR | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR Data Exporter](#fhir-data-exporter) | Il ruolo consente all'utente o all'entità di leggere ed esportare i dati FHIR | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [Lettore dati FHIR](#fhir-data-reader) | Il ruolo consente all'utente o all'entità di leggere i dati FHIR | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR Data Writer](#fhir-data-writer) | Il ruolo consente all'utente o all'entità di leggere e scrivere dati FHIR | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [ambiente del servizio di integrazione collaboratore](#integration-service-environment-contributor) | Consente di gestire gli ambienti del servizio di integrazione, ma non di accedervi. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [ambiente del servizio di integrazione Developer](#integration-service-environment-developer) | Consente agli sviluppatori di creare e aggiornare flussi di lavoro, account di integrazione e connessioni API negli ambienti del servizio di integrazione. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Collaboratore account Intelligent Systems](#intelligent-systems-account-contributor) | Consente di gestire gli account Sistemi intelligenti, ma non di accedervi. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Collaboratore per app per la logica](#logic-app-contributor) | Consente di gestire le app per la logica, ma non di modificarne l'accesso. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operatore per app per la logica](#logic-app-operator) | Consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identità** |  |  |
> | [Managed Identity Contributor](#managed-identity-contributor) (Collaboratore per identità gestita) | Crea, legge, aggiorna ed elimina l'identità assegnata all'utente | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Managed Identity Operator](#managed-identity-operator) (Operatore per identità gestita) | Legge e assegna l'identità assegnata all'utente | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Sicurezza** |  |  |
> | [Collaboratore per l'attestazione](#attestation-contributor) | Può leggere in scrittura o eliminare l'istanza del provider di attestazione | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Lettore di attestazione](#attestation-reader) | Può leggere le proprietà del provider di attestazione | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Azure Sentinel Di Automazione](#azure-sentinel-automation-contributor) | Azure Sentinel Di Automazione | f4c81013-99ee-4d62-a7ee-b3f1f648599a |
> | [Collaboratore di Azure Sentinel](#azure-sentinel-contributor) | Collaboratore di Azure Sentinel | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Ruolo con autorizzazioni di lettura di Azure Sentinel](#azure-sentinel-reader) | Ruolo con autorizzazioni di lettura di Azure Sentinel | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Risponditore di Azure Sentinel](#azure-sentinel-responder) | Risponditore di Azure Sentinel | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault amministratore](#key-vault-administrator) | Eseguire tutte le operazioni del piano dati in un insieme di credenziali delle chiavi e in tutti gli oggetti in esso contenuti, inclusi certificati, chiavi e segreti. Non è possibile gestire le risorse dell'insieme di credenziali delle chiavi o le assegnazioni di ruolo. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault Certificates Officer](#key-vault-certificates-officer) | Eseguire qualsiasi azione sui certificati di un insieme di credenziali delle chiavi, ad eccezione delle autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Collaboratore di Key Vault](#key-vault-contributor) | Gestire gli insiemi di credenziali delle chiavi, ma non consente di assegnare ruoli nel controllo degli accessi in base al ruolo di Azure e non consente di accedere a segreti, chiavi o certificati. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault Crypto Officer](#key-vault-crypto-officer) | Eseguire qualsiasi azione sulle chiavi di un insieme di credenziali delle chiavi, ad eccezione delle autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault crittografia del servizio di crittografia](#key-vault-crypto-service-encryption-user) | Legge i metadati delle chiavi ed esegue operazioni di wrapping/annullamento del wrapping. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault Crypto User](#key-vault-crypto-user) | Eseguire operazioni di crittografia usando le chiavi. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault lettore](#key-vault-reader) | Leggere i metadati degli insiemi di credenziali delle chiavi e dei relativi certificati, chiavi e segreti. Impossibile leggere i valori sensibili, ad esempio il contenuto del segreto o il materiale della chiave. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault Secrets Officer](#key-vault-secrets-officer) | Eseguire qualsiasi azione sui segreti di un insieme di credenziali delle chiavi, ad eccezione delle autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault Secrets User](#key-vault-secrets-user) | Leggere il contenuto del segreto. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Collaboratore HSM gestito](#managed-hsm-contributor) | Consente di gestire i pool di HSM gestiti, ma non di accedervi. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Amministrazione della protezione](#security-admin) | Visualizzazione e aggiornamento delle autorizzazioni per il Centro sicurezza. Ha le stesse autorizzazioni del Ruolo con autorizzazioni di lettura per la sicurezza e può anche aggiornare i criteri di sicurezza e rimuovere gli avvisi e le raccomandazioni. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Collaboratore per valutazioni della sicurezza](#security-assessment-contributor) | Consente di eseguire il push delle valutazioni nel Centro sicurezza | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Gestore sicurezza (legacy)](#security-manager-legacy) | Questo è un ruolo legacy. Usare invece Amministratore della sicurezza. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader) | Visualizzazione delle autorizzazioni per il Centro sicurezza. Può visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non può apportare modifiche. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Utente DevTest Labs](#devtest-labs-user) | Consente di connettere, avviare, riavviare e arrestare le macchine virtuali in Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Lab Creator](#lab-creator) (Creatore di lab) | Consente di creare nuovi lab con gli account Azure Lab. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitoraggio** |  |  |
> | [Collaboratore componente di Application Insights](#application-insights-component-contributor) | È in grado di gestire i componenti di Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Debugger di snapshot di Application Insights](#application-insights-snapshot-debugger) | Concede l'autorizzazione utente per visualizzare e scaricare gli snapshot di debug raccolti con Application Insights Snapshot Debugger. Si noti che queste autorizzazioni non sono incluse nei ruoli [Proprietario](#owner) e [Collaboratore](#contributor). Quando si assegna agli utenti il ruolo Snapshot Debugger di Application Insights, è necessario concedere il ruolo direttamente all'utente. Il ruolo non viene riconosciuto quando viene aggiunto a un ruolo personalizzato. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Collaboratore al monitoraggio](#monitoring-contributor) | Può leggere tutti i dati del monitoraggio e modificare le impostazioni di monitoraggio. Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Autore delle metriche di monitoraggio](#monitoring-metrics-publisher) | Abilitare la pubblicazione di metriche nelle risorse di Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Lettore di monitoraggio](#monitoring-reader) | Può leggere tutti i dati del monitoraggio (metriche, log e così via). Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Collaboratore per le cartelle di lavoro](#workbook-contributor) | Può salvare le cartelle di lavoro condivise. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Ruolo con autorizzazioni di lettura per le cartelle di lavoro](#workbook-reader) | Può leggere le cartelle di lavoro. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Gestione e governance** |  |  |
> | [Operatore processo di automazione](#automation-job-operator) | Consente di creare e gestire i processi tramite i runbook di Automazione. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operatore di automazione](#automation-operator) | Gli operatori di automazione possono avviare, arrestare, sospendere e riprendere processi. | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Operatore runbook di automazione](#automation-runbook-operator) | Consente di leggere le proprietà del runbook per permettere di creare processi del runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Arc utente del cluster Kubernetes abilitato](#azure-arc-enabled-kubernetes-cluster-user-role) | Elencare le credenziali utente del cluster. | 00493d72-78f6-4148-b6c5-d3ce8e4799dd |
> | [Azure Arc amministratore di Kubernetes](#azure-arc-kubernetes-admin) | Consente di gestire tutte le risorse nel cluster/spazio dei nomi, ad eccezione dell'aggiornamento o dell'eliminazione di quote di risorse e spazi dei nomi. | dffb1e0c-446f-4dde-a09f-99eb5cc68b96 |
> | [Azure Arc amministratore del cluster Kubernetes](#azure-arc-kubernetes-cluster-admin) | Consente di gestire tutte le risorse nel cluster. | 8393591c-06b9-48a2-a542-1bd6b377f6a2 |
> | [Azure Arc Visualizzatore Kubernetes](#azure-arc-kubernetes-viewer) | Consente di visualizzare tutte le risorse nel cluster/spazio dei nomi, ad eccezione dei segreti. | 63f0a09d-1495-4db4-a681-037d84835eb4 |
> | [Azure Arc Kubernetes Writer](#azure-arc-kubernetes-writer) | Consente di aggiornare tutti gli elementi nel cluster/spazio dei nomi, ad eccezione dei ruoli (cluster) e delle associazioni di ruoli (cluster). | 5b999177-9696-4545-85c7-50de3797e5a1 |
> | [Onboarding di Azure Connected Machine](#azure-connected-machine-onboarding) | Può eseguire l'onboarding di Azure Connected Machine. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Amministratore delle risorse di Azure Connected Machine](#azure-connected-machine-resource-administrator) | Può leggere, scrivere, eliminare e ripetere l'onboarding di Azure Connected Machine. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Lettore per la fatturazione](#billing-reader) | Consente l'accesso in lettura ai dati di fatturazione. | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Collaboratore di progetto](#blueprint-contributor) | Può gestire le definizioni di progetto, ma non assegnarle. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Operatore di progetto](#blueprint-operator) | Può assegnare i progetti pubblicati esistenti, ma non creare nuovi progetti. Si noti che funziona solo se l'assegnazione viene eseguita con un'identità gestita assegnata dall'utente. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Collaboratore Gestione costi](#cost-management-contributor) | Può visualizzare i costi e gestire la configurazione dei costi, ad esempio budget ed esportazioni | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Lettore Gestione costi](#cost-management-reader) | Può visualizzare la configurazione e i dati dei costi, ad esempio budget ed esportazioni | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Amministratore impostazioni gerarchia](#hierarchy-settings-administrator) | Consente agli utenti di modificare ed eliminare le impostazioni della gerarchia | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes Cluster - Azure Arc Onboarding](#kubernetes-cluster---azure-arc-onboarding) | Definizione del ruolo per autorizzare qualsiasi utente/servizio a creare una risorsa connectedClusters | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Ruolo collaboratore per applicazioni gestite](#managed-application-contributor-role) | Consente la creazione di risorse di applicazioni gestite. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Ruolo di Operatore applicazione gestita](#managed-application-operator-role) | Consente di leggere ed eseguire azioni sulle risorse dell'applicazione gestita | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Lettore applicazioni gestite](#managed-applications-reader) | Consente di leggere le risorse in un accesso di app gestita e JIT richiesta. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti](#managed-services-registration-assignment-delete-role) | Il Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti consente agli utenti del tenant di gestione di eliminare l'assegnazione della registrazione assegnata al proprio tenant. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Collaboratore gruppo di gestione](#management-group-contributor) | Ruolo Collaboratore gruppo di gestione | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Lettore gruppo di gestione](#management-group-reader) | Ruolo Lettore gruppo di gestione | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Collaboratore account New Relic APM](#new-relic-apm-account-contributor) | Consente di gestire gli account e le applicazioni di APR New Relic, ma non di accedervi. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Ruolo con autorizzazioni di scrittura per i dati di Policy Insights (anteprima)](#policy-insights-data-writer-preview) | Consente l'accesso in lettura ai criteri delle risorse e l'accesso in scrittura agli eventi dei criteri dei componenti delle risorse. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Operatore di richiesta quota](#quota-request-operator) | Leggere e creare richieste di quota, ottenere lo stato della richiesta di quota e creare ticket di supporto. | 0e5f05e5-9ab9-446b-b98d-1e2157c94125 |
> | [Acquirente della prenotazione](#reservation-purchaser) | Consente di acquistare prenotazioni | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [Collaboratore per i criteri delle risorse](#resource-policy-contributor) | Utenti con diritti di creazione/modifica di criteri delle risorse, creazione di ticket di supporto e lettura di risorse/gerarchia. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Collaboratore al ripristino sito](#site-recovery-contributor) | Consente di gestire il servizio Site Recovery ad eccezione della creazione dell'insieme di credenziali e dell'assegnazione di ruolo. | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operatore del ripristino sito](#site-recovery-operator) | Consente di eseguire il failover e il failback ma non di eseguire altre operazioni di gestione di Site Recovery. | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Reader di ripristino sito](#site-recovery-reader) | Consente di visualizzare lo stato di Site Recovery ma non di eseguire altre operazioni di gestione. | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Collaboratore alla richiesta di supporto](#support-request-contributor) | Consente di creare e gestire le richieste di supporto. | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Collaboratore per tag](#tag-contributor) | Consente di gestire i tag sulle entità senza concedere l'accesso alle entità stesse. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Altri** |  |  |
> | [Gemelli digitali di Azure proprietario dei dati](#azure-digital-twins-data-owner) | Ruolo di accesso completo per il piano dati di Gemelli digitali | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Gemelli digitali di Azure lettore dati](#azure-digital-twins-data-reader) | Ruolo di sola lettura per le proprietà del piano dati di Gemelli digitali | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [Collaboratore BizTalk](#biztalk-contributor) | Consente di gestire i servizi BizTalk, ma non di accedervi. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Collaboratore gruppo di applicazioni di Desktop Virtualization](#desktop-virtualization-application-group-contributor) | Collaboratore del gruppo di applicazioni di virtualizzazione desktop. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Lettore del gruppo di applicazioni desktop virtualization](#desktop-virtualization-application-group-reader) | Lettore del gruppo di applicazioni di virtualizzazione desktop. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [Collaboratore Virtualizzazione desktop](#desktop-virtualization-contributor) | Collaboratore di Virtualizzazione desktop. | 082f0a83-3be5-4ba1-904c-961cca79b387 |
> | [Collaboratore pool di host di virtualizzazione desktop](#desktop-virtualization-host-pool-contributor) | Collaboratore del pool di host di virtualizzazione desktop. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Lettore del pool di host di virtualizzazione desktop](#desktop-virtualization-host-pool-reader) | Lettore del pool di host di virtualizzazione desktop. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [Lettore di virtualizzazione desktop](#desktop-virtualization-reader) | Lettore di Desktop Virtualization. | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Operatore host sessione di virtualizzazione desktop](#desktop-virtualization-session-host-operator) | Operatore dell'host di sessione di virtualizzazione desktop. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [Utente di Desktop Virtualization](#desktop-virtualization-user) | Consente all'utente di usare le applicazioni in un gruppo di applicazioni. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Operatore sessione utente di Desktop Virtualization](#desktop-virtualization-user-session-operator) | Operatore della sessione Uesr di Desktop Virtualization. | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [Collaboratore dell'area di lavoro Desktop Virtualization](#desktop-virtualization-workspace-contributor) | Collaboratore dell'area di lavoro Virtualizzazione desktop. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Lettore dell'area di lavoro Virtualizzazione desktop](#desktop-virtualization-workspace-reader) | Lettore dell'area di lavoro Desktop Virtualization. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [Lettore backup su disco](#disk-backup-reader) | Fornisce l'autorizzazione per il backup dell'insieme di credenziali per eseguire il backup su disco. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Operatore ripristino disco](#disk-restore-operator) | Fornisce l'autorizzazione per eseguire il backup dell'insieme di credenziali per eseguire il ripristino del disco. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Collaboratore snapshot disco](#disk-snapshot-contributor) | Fornisce l'autorizzazione per eseguire il backup dell'insieme di credenziali per gestire gli snapshot del disco. | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Collaboratore raccolte di processi dell'unità di pianificazione](#scheduler-job-collections-contributor) | Consente di gestire le raccolte di processi dell'utilità di pianificazione, ma non di accedervi. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Operatore hub servizi](#services-hub-operator) | Operatore hub servizi consente di eseguire tutte le operazioni di lettura, scrittura ed eliminazione correlate ai connettori dell'hub dei servizi. | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>Generale


### <a name="contributor"></a>Collaboratore

Concede l'accesso completo per gestire tutte le risorse, ma non consente di assegnare ruoli nel controllo degli accessi in base al ruolo di Azure, gestire le assegnazioni in Azure Blueprints o condividere raccolte di immagini. [Scopri di più](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | * | È in grado di creare e gestire ogni tipo di risorsa |
> | **NotActions** |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Eliminazione di ruoli, assegnazioni di criteri, definizioni dei criteri e definizioni dei set di criteri |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Creazione di ruoli, assegnazioni di ruoli, assegnazioni di criteri, definizioni dei criteri e definizioni dei set di criteri |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Concede al chiamante l'accesso di tipo Amministratore Accesso utenti a livello dell'ambito del tenant |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | Crea o aggiorna eventuali assegnazioni del progetto |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | Elimina eventuali assegnazioni del progetto |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/galleries/share/action | Condivide una raccolta in ambiti diversi |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Proprietario

Concede l'accesso completo per gestire tutte le risorse, inclusa la possibilità di assegnare ruoli nel controllo degli accessi in base al ruolo di Azure. [Scopri di più](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | * | È in grado di creare e gestire ogni tipo di risorsa |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Reader

Visualizza tutto risorse, ma non consente di apportare modifiche. [Scopri di più](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Amministratore accessi utente

Consente di gestire gli accessi utente alle risorse di Azure. [Scopri di più](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/* | Gestire l'autorizzazione |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Calcolo


### <a name="classic-virtual-machine-contributor"></a>Collaboratore macchine virtuali classiche

Consente di gestire le macchine virtuali classiche, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Creare e gestire nomi di dominio di calcolo classici |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Creare e gestire macchine virtuali |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | Collega un IP riservato |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | Ottiene gli IP riservati |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | Unisce la rete virtuale. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | Ottiene la rete virtuale. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | Restituisce il disco dell'account di archiviazione. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | Restituisce l'immagine dell'account di archiviazione. Operazione deprecata: usare 'Microsoft.ClassicStorage/storageAccounts/vmImages'. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Restituisce l'account di archiviazione con l'account specificato. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Virtual Machine Administrator Login (Accesso amministratore macchina virtuale)

Visualizzare le macchine virtuali nel portale e accedere come amministratore [Altre informazioni](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Ottiene una definizione del servizio di bilanciamento del carico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Consente di accedere a una macchina virtuale come utente normale |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | Consente di accedere a una macchina virtuale con privilegi di amministratore di Windows o di utente root di Linux |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Collaboratore macchine virtuali

Consente di gestire le macchine virtuali, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. [Scopri di più](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Creare e gestire set di disponibilità di calcolo |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Creare e gestire percorsi di calcolo |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Eseguire tutte le azioni delle macchine virtuali, tra cui creazione, aggiornamento, eliminazione, avvio, riavvio e accensione delle macchine virtuali. Eseguire script predefiniti nelle macchine virtuali. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Creare e gestire i set di scalabilità delle macchine virtuali |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Crea un nuovo disco o ne aggiorna uno esistente |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Ottiene le proprietà di un disco |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | Elimina il disco |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | Aggiunge un pool di indirizzi back-end del gateway applicazione. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Aggiunge un pool di indirizzi di back-end del servizio di bilanciamento del carico. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | Aggiunge un pool NAT in entrata del servizio di bilanciamento del carico. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Aggiunge una regola NAT in entrata del servizio di bilanciamento del carico. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | Consente l'uso di probe di un servizio di bilanciamento del carico. Con questa autorizzazione, ad esempio, la proprietà healthProbe di un set di scalabilità di macchine virtuali può fare riferimento al probe. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Ottiene una definizione del servizio di bilanciamento del carico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | Creare e gestire percorsi di rete |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Creare e gestire interfacce di rete |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Aggiunge un gruppo di sicurezza di rete. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | Ottiene una definizione del gruppo di sicurezza di rete |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Aggiunge un indirizzo IP pubblico. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Aggiunge una rete virtuale. Senza avvisi. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Crea un programma di protezione del backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Crea un elemento protetto di backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Restituisce tutti i criteri di protezione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | Crea i criteri di protezione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault' |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Virtual Machine User Login (Accesso utente macchina virtuale)

Consente di visualizzare le macchine virtuali nel portale e di accedere come utente normale. [Scopri di più](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Ottiene una definizione del servizio di bilanciamento del carico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Consente di accedere a una macchina virtuale come utente normale |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Rete


### <a name="cdn-endpoint-contributor"></a>Collaboratore endpoint rete CDN

Può gestire gli endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>Lettore endpoint rete CDN

Può visualizzare gli endpoint della rete CDN, ma non può apportare modifiche.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Collaboratore profilo rete CDN

Può gestire i profili e i rispettivi endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. [Scopri di più](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>Lettore profilo rete CDN

Può visualizzare i profili e i rispettivi endpoint della rete CDN, ma non può apportare modifiche.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Collaboratore reti virtuali classiche

Consente di gestire le reti classiche, ma non di accedervi. [Scopri di più](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Creare e gestire reti classiche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Collaboratore zona DNS

Consente di gestire le zone DNS e i set di record in DNS di Azure, ma non di controllare chi è autorizzato ad accedervi. [Scopri di più](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | Creazione e gestione di zone e record DNS |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Collaboratore di rete

Consente di gestire le reti, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | Creare e gestire reti |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>DNS privato Collaboratore zona

Consente di gestire le risorse di zona DNS private, ma non le reti virtuali a cui sono collegate. [Scopri di più](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | Aggiunge una rete virtuale. Senza avvisi. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Collaboratore Gestione traffico

Consente di gestire i profili di Gestione traffico, ma non di controllare chi è autorizzato ad accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Archiviazione


### <a name="avere-contributor"></a>Collaboratore per Avere

Può creare e gestire un cluster Avere vFXT. [Scopri di più](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Ottiene una definizione di subnet della rete virtuale |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Aggiunge una rete virtuale. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Aggiunge un gruppo di sicurezza di rete. Senza avvisi. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Creare e gestire account di archiviazione |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Ottiene le risorse del gruppo di risorse. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Restituisce il risultato dell'eliminazione di un BLOB |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Restituisce un BLOB o un elenco di BLOB |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Restituisce il risultato della scrittura su un BLOB |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Operatore di Avere

Usato dal cluster Avere vFXT per gestire il cluster [Altre informazioni](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Ottiene le proprietà di una macchina virtuale |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Crea un'interfaccia di rete o ne aggiorna una esistente.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Ottiene una definizione di subnet della rete virtuale |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Aggiunge una rete virtuale. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Aggiunge un gruppo di sicurezza di rete. Senza avvisi. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Restituisce il risultato dell'eliminazione di un contenitore |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Restituisce l'elenco dei contenitori |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Restituisce il risultato dell'operazione PUT sul contenitore BLOB |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Restituisce il risultato dell'eliminazione di un BLOB |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Restituisce un BLOB o un elenco di BLOB |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Restituisce il risultato della scrittura su un BLOB |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Collaboratore di backup

Consente di gestire il servizio di backup, ma non di creare insiemi di credenziali e concedere l'accesso ad altri utenti [Altre informazioni](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Consente di gestire i risultati dell'operazione sulla gestione del backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Consente di creare e gestire i contenitori di backup all'interno delle infrastrutture di backup dell'insieme di credenziali dei Servizi di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Aggiorna l'elenco di contenitori |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Consente di creare e gestire i processi di backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Esporta processi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Consente di creare e gestire i risultati delle operazioni di gestione di backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Consente di creare e gestire i criteri di backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Consente di creare e gestire gli elementi su cui è possibile eseguire il backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Consente di creare e gestire gli elementi su cui è stato eseguito il backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Consente di creare e gestire i contenitori che contengono gli elementi di backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Consente di creare e gestire i certificati relativi al backup nell'insieme di credenziali dei Servizi di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Consente di creare e gestire informazioni estese relative all'insieme di credenziali |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Consente di creare e gestire le identità registrate |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Consente di creare e gestire l'uso dell'insieme di credenziali dei Servizi di ripristino |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Convalida l'operazione sull'elemento protetto |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Ottiene tutti i contenitori che si possono proteggere |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Convalida le funzioni |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Risolve l'avviso. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | L'operazione restituisce l'elenco delle operazioni per un provider di risorse |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Ottiene lo stato dell'operazione per una determinata operazione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Elenca tutte le finalità di protezione del backup |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Operatore di backup

Consente di gestire i servizi di backup, ad eccezione della rimozione del backup, della creazione dell'insieme di credenziali e dell'accesso ad altri utenti [Altre informazioni](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Restituisce lo stato dell'operazione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Ottiene il risultato dell'operazione eseguita sul contenitore di protezione. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Esegue il backup dell'elemento protetto. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Ottiene il risultato dell'operazione eseguita sugli elementi protetti. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Restituisce lo stato dell'operazione eseguita sugli elementi protetti. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Effettua il provisioning del ripristino elementi immediato per l'elemento protetto |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action | Ottiene AccessToken per il ripristino tra aree. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Ottiene i punti di ripristino degli elementi protetti. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Ripristina i punti di ripristino degli elementi protetti. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoca il ripristino elementi immediato per l'elemento protetto |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Crea un elemento protetto di backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Restituisce tutti i contenitori registrati |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Aggiorna l'elenco di contenitori |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Consente di creare e gestire i processi di backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Esporta processi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Consente di creare e gestire i risultati delle operazioni di gestione di backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Ottiene i risultati dell'operazione sui criteri. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Restituisce tutti i criteri di protezione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Consente di creare e gestire gli elementi su cui è possibile eseguire il backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Restituisce l'elenco di tutti gli elementi protetti. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Restituisce tutti i contenitori che appartengono alla sottoscrizione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali della risorsa o dell'insieme di credenziali. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | L'operazione Registra contenitore di servizi può essere usata per registrare un contenitore con il servizio di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Convalida l'operazione sull'elemento protetto |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Ottiene lo stato dell'operazione sui criteri. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | Crea un contenitore registrato |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | Esegue operazioni di richiesta di informazioni per i carichi di lavoro all'interno di un contenitore |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Crea un programma di protezione del backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Ottiene una finalità di protezione del backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Ottiene tutti i contenitori che si possono proteggere |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Ottiene tutti gli elementi in un contenitore |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Convalida le funzioni |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupAadProperties/read | Ottenere le proprietà di AAD per l'autenticazione nella terza area per il ripristino tra aree. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/action | Elencare i processi di ripristino tra aree nell'area secondaria per l'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/action | Ottenere i dettagli del processo di ripristino tra aree nell'area secondaria per l'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrossRegionRestore/action | Attivare il ripristino tra aree. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/read | Restituisce il risultato dell'operazione CRR per l'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/read | Restituisce lo stato dell'operazione CRR per l'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Risolve l'avviso. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | L'operazione restituisce l'elenco delle operazioni per un provider di risorse |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Ottiene lo stato dell'operazione per una determinata operazione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Elenca tutte le finalità di protezione del backup |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/locations/backupAadProperties/read",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Lettore di backup

Può visualizzare i servizi di backup, ma non può apportare modifiche [Altre informazioni](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Restituisce lo stato dell'operazione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Ottiene il risultato dell'operazione eseguita sul contenitore di protezione. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Ottiene il risultato dell'operazione eseguita sugli elementi protetti. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Restituisce lo stato dell'operazione eseguita sugli elementi protetti. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Ottiene i punti di ripristino degli elementi protetti. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Restituisce tutti i contenitori registrati |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | Restituisce il risultato dell'operazione di processo. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | Restituisce tutti gli oggetti processo |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Esporta processi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | Restituisce il risultato dell'operazione di backup di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Ottiene i risultati dell'operazione sui criteri. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Restituisce tutti i criteri di protezione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Restituisce l'elenco di tutti gli elementi protetti. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Restituisce tutti i contenitori che appartengono alla sottoscrizione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | Restituisce la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | Restituisce la configurazione dell'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Ottiene lo stato dell'operazione sui criteri. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Ottiene una finalità di protezione del backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Ottiene tutti gli elementi in un contenitore |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Risolve l'avviso. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | L'operazione restituisce l'elenco delle operazioni per un provider di risorse |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Ottiene lo stato dell'operazione per una determinata operazione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Elenca tutte le finalità di protezione del backup |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Convalida le funzioni |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Collaboratore account di archiviazione classico

Consente di gestire gli account di archiviazione classici, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Creare e gestire account di archiviazione |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico

Gli operatori delle chiavi dell'account di archiviazione classico possono elencare e rigenerare le chiavi per gli account di archiviazione classici [Altre informazioni](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | Rigenera le chiavi di accesso esistenti per l'account di archiviazione. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Collaboratore Data Box

Consente di gestire tutto il servizio Data Box, ad eccezione della concessione dell'accesso ad altri utenti. [Scopri di più](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Lettore Data Box

Consente di gestire il servizio Data Box, ad eccezione della creazione di ordini, della modifica dei dettagli dell'ordine e della concessione dell'accesso ad altri utenti. [Scopri di più](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | Elenca le credenziali non crittografate correlate all'ordine. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | Restituisce l'elenco degli SKU disponibili. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | Questo metodo esegue tutti i tipi di convalida. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | Questo metodo restituisce le configurazioni per l'area. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | Convalida l'indirizzo di spedizione e fornisce indirizzi alternativi, se disponibili. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Sviluppatore di Data Lake Analytics

Consente di inviare, monitorare e gestire i propri processi, ma non di creare o eliminare account Data Lake Analytics. [Scopri di più](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Elimina un account Analisi Data Lake. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | Concede le autorizzazioni per annullare i processi inviati da altri utenti. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Crea o aggiorna un account Analisi Data Lake. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Crea o aggiorna un account Archivio Data Lake collegato a un account Analisi Data Lake. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Scollega un account Archivio Data Lake da un account Analisi Data Lake. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Crea o aggiorna un account di archiviazione collegato a un account Analisi Data Lake. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Scollega un account di archiviazione da un account Analisi Data Lake. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Crea o aggiorna una regola del firewall. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Elimina una regola del firewall. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Crea o aggiorna i criteri di calcolo. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Elimina criteri di calcolo. |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Lettore e accesso ai dati

Consente di visualizzare tutti gli elementi ma non consente di eliminare o creare un account di archiviazione o una risorsa contenuta. Consente anche l'accesso in lettura/scrittura a tutti i dati contenuti in un account di archiviazione tramite l'accesso alle chiavi dell'account di archiviazione.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | Restituisce il token SAS dell’account per l’account di archiviazione specificato. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Collaboratore account di archiviazione

Consente di gestire gli account di archiviazione. Consente di accedere alla chiave dell'account, che può essere usata per accedere ai dati usando l'autorizzazione con chiave condivisa. [Scopri di più](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Senza avvisi. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Creare e gestire account di archiviazione |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Ruolo del servizio dell'operatore della chiave dell'account di archiviazione

Consente di elencare e rigenerare le chiavi di accesso dell'account di archiviazione. [Scopri di più](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | Rigenera le chiavi di accesso per l'account di archiviazione specificato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Collaboratore ai dati del BLOB di archiviazione

Consente di leggere, scrivere ed eliminare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Elimina un contenitore. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Restituisce un contenitore o un elenco di contenitori. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Modifica i metadati o le proprietà di un contenitore. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Eliminare un BLOB. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Restituisce un BLOB o un elenco di BLOB. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Consente di scrivere in un BLOB. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | Sposta il BLOB da un percorso a un altro |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/add/action | Restituisce il risultato dell'aggiunta di contenuto BLOB |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Proprietario dei dati del BLOB di archiviazione

Concede l'accesso completo ai contenitori e ai dati dei BLOB di Archiviazione di Azure, inclusa l'assegnazione del controllo di accesso POSIX. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | Autorizzazioni complete per i contenitori. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | Autorizzazioni complete per i BLOB. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Lettore dei dati del BLOB di archiviazione

Consente di leggere ed elencare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Restituisce un contenitore o un elenco di contenitori. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Restituisce un BLOB o un elenco di BLOB. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Delegante di BLOB di archiviazione

Ottiene una chiave di delega utente, che può quindi essere usata per creare una firma di accesso condiviso per un contenitore o un BLOB firmato con credenziali di Azure AD. Per altre informazioni, vedere [Creare una firma di accesso condiviso di delega utente](/rest/api/storageservices/create-user-delegation-sas). [Scopri di più](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Collaboratore per la condivisione SMB di dati per file di archiviazione

Consente l'accesso in lettura, scrittura ed eliminazione a file e directory nelle condivisioni file di Azure. Questo ruolo non ha un equivalente predefinito nei file server Windows. [Scopri di più](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Restituisce un file, una cartella o un elenco di file/cartelle. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Restituisce il risultato della scrittura di un file o della creazione di una cartella. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Restituisce il risultato dell'eliminazione di un file o una cartella. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Collaboratore con privilegi elevati per la condivisione SMB di dati per file di archiviazione

Consente la lettura, scrittura, eliminazione e modifica degli ACL nei file e nelle directory delle condivisioni file di Azure. Questo ruolo è equivalente a un ACL di modifica della condivisione file nei file server Windows. [Scopri di più](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Restituisce un file, una cartella o un elenco di file/cartelle. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Restituisce il risultato della scrittura di un file o della creazione di una cartella. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Restituisce il risultato dell'eliminazione di un file o una cartella. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Restituisce il risultato della modifica delle autorizzazioni per un file o una cartella. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Ruolo con autorizzazioni di lettura per la condivisione SMB di dati per file di archiviazione

Consente l'accesso in lettura a file e directory nelle condivisioni file di Azure. Questo ruolo è equivalente a un ACL di lettura della condivisione file nei file server Windows. [Scopri di più](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Restituisce un file, una cartella o un elenco di file/cartelle. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Collaboratore ai dati della coda di archiviazione

Lettura, scrittura ed eliminazione delle code e dei messaggi delle code di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | Elimina una coda. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Restituisce una coda o un elenco di code. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | Modifica le proprietà o i metadati della coda. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | Elimina uno o più messaggi da una coda. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Visualizza in anteprima o recupera uno o più messaggi da una coda. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | Aggiunge un messaggio a una coda. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Restituisce il risultato dell'elaborazione di un messaggio |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Ruolo con autorizzazioni di elaborazione per i messaggi sui dati della coda di archiviazione

Visualizzazione in anteprima, recupero ed eliminazione di un messaggio da una coda di Archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Visualizza in anteprima un messaggio. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Recupera ed elimina un messaggio. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Mittente dei messaggi sui dati della coda di archiviazione

Consente di aggiungere messaggi a una coda di Archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | Aggiunge un messaggio a una coda. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Ruolo con autorizzazioni di lettura per i dati della coda di archiviazione

Consente di leggere ed elencare le code e i messaggi delle code di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Restituisce una coda o un elenco di code. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Visualizza in anteprima o recupera uno o più messaggi da una coda. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-contributor"></a>Mappe di Azure collaboratore ai dati

Concede l'accesso in lettura, scrittura ed eliminazione ai dati correlati alla mappa da un account mappe di Azure. [Scopri di più](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/write |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/delete |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read, write, and delete access to map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "name": "8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read",
        "Microsoft.Maps/accounts/*/write",
        "Microsoft.Maps/accounts/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-maps-data-reader"></a>Lettore di dati per Mappe di Azure

Concede l'accesso per la lettura dei dati correlati alle mappe da un account Mappe di Azure. [Scopri di più](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-data-reader"></a>Azure Spring Cloud lettore dati

Consenti l'accesso in lettura Azure Spring Cloud dati [Altre informazioni](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/*/read |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b5537268-8956-4941-a8f0-646150406f0c",
  "name": "b5537268-8956-4941-a8f0-646150406f0c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Collaboratore servizi di ricerca

Consente di gestire i servizi di Ricerca, ma non di accedervi. [Scopri di più](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | È in grado di creare e gestire servizi di ricerca |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>Lettore AccessKey di SignalR

Leggere le chiavi di accesso al servizio SignalR

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/read |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/action | Visualizza il valore delle chiavi di accesso di SignalR nel portale di gestione o tramite API |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>Server app SignalR (anteprima)

Consente al server app di accedere al servizio SignalR con le opzioni di autenticazione di AAD.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Generare un elemento AccessKey temporaneo per la firma di ClientTokens. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | Avviare una connessione server. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>Collaboratore SignalR

Creare, leggere, aggiornare ed eliminare risorse del servizio SignalR

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>Collaboratore serverless SignalR (anteprima)

Consente all'app di accedere al servizio in modalità serverless con le opzioni di autenticazione di AAD.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Generare un ClientToken per avviare una connessione client. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>Proprietario del servizio SignalR (anteprima)

Accesso completo alle SERVIZIO AZURE SIGNALR REST

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Generare un accessKey temporaneo per la firma di ClientToken. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Generare un ClientToken per avviare una connessione client. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | Trasmettere messaggi a tutte le connessioni client nell'hub. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | Trasmettere un messaggio al gruppo. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Controllare l'esistenza del gruppo o l'esistenza dell'utente nel gruppo. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | Partecipa/Lascia il gruppo. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | Inviare messaggi direttamente a una connessione client. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Controllare l'esistenza della connessione client. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | Chiudere la connessione client. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | Inviare messaggi all'utente, che può essere costituito da più connessioni client. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Controllare l'esistenza dell'utente. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write | Modificare un utente. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/auth/clientToken/action",
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>Lettore del servizio SignalR (anteprima)

Accesso in sola lettura alle SERVIZIO AZURE SIGNALR REST

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Verificare l'esistenza del gruppo o dell'utente nel gruppo. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Verificare l'esistenza della connessione client. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Verificare l'esistenza dell'utente. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Collaboratore piani Web

Consente di gestire i piani Web per i siti Web, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Creare e gestire server farm |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | Aggiunge un ambiente del servizio app |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Collaboratore siti Web

Consente di gestire i siti Web (non i piani Web), ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | È in grado di creare e gestire i componenti di Insights |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificates/* | Creare e gestire certificati dei siti Web |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | Ottiene i nomi dei siti assegnati al nome host. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Aggiunge un piano di servizio app |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Ottiene le proprietà per un piano di servizio app |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | Creare e gestire siti Web. Per creare un sito sono anche necessarie le autorizzazione di scrittura associate al piano di servizio app |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Contenitori


### <a name="acrdelete"></a>AcrDelete

acr delete [Altre informazioni](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | Elimina l'artefatto in un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

acr image signer [Altre informazioni](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | Eseguire il push/pull di metadati considerati attendibili per un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

acr pull [Altre informazioni](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Eseguire il pull o ottenere immagini da un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

acr push [Altre informazioni](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Eseguire il pull o ottenere immagini da un registro contenitori. |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | Eseguire il push o scrivere immagini in un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

lettore di dati di quarantena acr

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Eseguire il pull o ottenere immagini in quarantena da un registro contenitori |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

writer di dati di quarantena acr

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Eseguire il pull o ottenere immagini in quarantena da un registro contenitori |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | Scrivere/modificare lo stato di quarantena di immagini in quarantena |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Ruolo di amministratore del cluster del servizio Azure Kubernetes

Elencare l'azione delle credenziali di amministratore del cluster. [Scopri di più](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | Elenca la credenziale clusterAdmin di un cluster gestito |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | Recupera il profilo di accesso per un cluster gestito in base al nome del ruolo con un'operazione di elenco delle credenziali |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Recupera un cluster gestito |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Ruolo di utente del cluster del servizio Azure Kubernetes

Elencare l'azione delle credenziali di utente del cluster. [Scopri di più](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Elenca la credenziale clusterUser di un cluster gestito |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Recupera un cluster gestito |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>servizio Azure Kubernetes collaboratore

Concede l'accesso ai cluster di lettura e servizio Azure Kubernetes scrittura per [altre informazioni](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Recupera un cluster gestito |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | Crea un nuovo cluster gestito o ne aggiorna uno esistente |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>servizio Azure Kubernetes controllo degli accessi in base al ruolo

Consente di gestire tutte le risorse nel cluster/spazio dei nomi, ad eccezione dell'aggiornamento o dell'eliminazione delle quote e degli spazi dei nomi delle risorse. [Scopri di più](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crea o aggiorna una distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Elenca la credenziale clusterUser di un cluster gestito |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | Scrive resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | Elimina resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | Scrive spazi dei nomi |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | Elimina gli spazi dei nomi |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>servizio Azure Kubernetes amministratore del cluster controllo degli accessi in base al ruolo

Consente di gestire tutte le risorse nel cluster. [Scopri di più](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crea o aggiorna una distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Elenca la credenziale clusterUser di un cluster gestito |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>servizio Azure Kubernetes lettore controllo degli accessi in base al ruolo

Consente l'accesso in sola lettura per visualizzare la maggior parte degli oggetti in uno spazio dei nomi. Non consente la visualizzazione di ruoli o associazioni di ruoli. Questo ruolo non consente la visualizzazione dei segreti, perché la lettura del contenuto dei segreti consente l'accesso alle credenziali ServiceAccount nello spazio dei nomi , che consentirebbe l'accesso all'API come qualsiasi ServiceAccount nello spazio dei nomi (una forma di escalation dei privilegi). L'applicazione di questo ruolo nell'ambito del cluster consente l'accesso a tutti gli spazi dei nomi. [Scopri di più](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crea o aggiorna una distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Legge i controllerrevision |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/read | Legge i daemonset |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/read | Legge le distribuzioni |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/read | Legge i set di repliche |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/read | Legge i set con stato |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/read | Legge horizontalpodautoscalers |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/read | Legge cronjobs |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/read | Legge i processi |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/read | Legge le mappe di configurazione |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/read | Legge gli endpoint |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Legge gli eventi |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Legge gli eventi |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/read | Legge i daemonset |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/read | Legge le distribuzioni |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/read | Legge i dati in ingresso |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/read | Legge networkpolicies |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/read | Legge i set di repliche |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Numero massimo di operazioni di lettura |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Legge gli spazi dei nomi |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/read | Legge i dati in ingresso |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/read | Legge networkpolicies |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/read | Legge persistentvolumeclaims |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/read | Legge i pod |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/read | Legge poddisruptionbudgets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Legge i controller di replica |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Legge i controller di replica |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Legge resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/read | Legge gli account di servizio |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/read | Legge i servizi |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>servizio Azure Kubernetes RBAC Writer

Consente l'accesso in lettura/scrittura alla maggior parte degli oggetti in uno spazio dei nomi. Questo ruolo non consente la visualizzazione o la modifica di ruoli o associazioni di ruolo. Tuttavia, questo ruolo consente l'accesso ai segreti e l'esecuzione di pod come qualsiasi ServiceAccount nello spazio dei nomi, quindi può essere usato per ottenere i livelli di accesso api di qualsiasi ServiceAccount nello spazio dei nomi. L'applicazione di questo ruolo nell'ambito del cluster consente l'accesso a tutti gli spazi dei nomi. [Scopri di più](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crea o aggiorna una distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Legge i controllerrevision |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Legge gli eventi |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Legge gli eventi |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Legge gli intervallo di limiti |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Legge gli spazi dei nomi |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Legge resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Database


### <a name="cosmos-db-account-reader-role"></a>Ruolo Lettore dell'account Cosmos DB

Può leggere i dati degli account Azure Cosmos DB. Vedere [Collaboratore account DocumentDB](#documentdb-account-contributor) per la gestione degli account Azure Cosmos DB. [Scopri di più](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | Leggere tutte le raccolte |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | Legge le chiavi di sola lettura degli account di database. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | Consente di leggere le definizioni della metrica |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | Esegue la lettura delle metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Operatore di Cosmos DB

Consente di gestire gli account Azure Cosmos DB, ma non di accedere ai dati contenuti negli stessi. Impedisce l'accesso a chiavi dell'account e stringhe di connessione. [Scopri di più](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Senza avvisi. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/write | Creare o aggiornare una definizione di ruolo SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/delete | Eliminare una definizione di ruolo SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/write | Creare o aggiornare un'assegnazione di ruolo SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/delete | Eliminare un'assegnazione di ruolo SQL |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Può inviare una richiesta di ripristino per un database Cosmos DB o un contenitore per un account [Altre informazioni](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | Invia una richiesta per configurare il backup |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | Invia una richiesta di ripristino |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosrestoreoperator"></a>CosmosRestoreOperator

Può eseguire l'azione di ripristino per Cosmos DB account di database con modalità di backup continuo

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/restore/action | Invia una richiesta di ripristino |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/*/read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/read | Leggere un account di database ripristinabile o elencare tutti gli account di database ripristinabili |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform restore action for Cosmos DB database account with continuous backup mode",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "name": "5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosRestoreOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Collaboratore account DocumentDB

È in grado di gestire account Azure Cosmos DB. Azure Cosmos DB era precedentemente noto come DocumentDB. [Scopri di più](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Creare e gestire account Azure Cosmos DB |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Senza avvisi. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Collaboratore cache Redis

Consente di gestire le cache Redis, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | Registra il provider di risorse 'Microsoft.Cache' con una sottoscrizione |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | Creare e gestire cache Redis |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Collaboratore database SQL

Consente di gestire i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza o i rispettivi server SQL padre. [Scopri di più](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | Creare e gestire database SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Esegue la lettura delle metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Consente di leggere le definizioni della metrica |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Modificare le impostazioni di controllo |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Recupera i record di controllo BLOB del database |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Modificare i criteri di mascheratura dei dati |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Modificare i criteri di avviso di sicurezza |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Modificare i criteri di protezione |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Collaboratore per Istanza gestita di SQL

Consente di gestire le istanze gestite di SQL e la configurazione di rete necessaria, ma non consente l'accesso ad altri utenti.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Esegue la lettura delle metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Consente di leggere le definizioni della metrica |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | Elimina un server gestito specifico Azure Active Directory solo l'oggetto di autenticazione |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | Aggiunge o aggiorna un server gestito specifico Azure Active Directory solo l'oggetto di autenticazione |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>Gestione della sicurezza SQL

Consente di gestire i criteri relativi alla sicurezza di server e database SQL, ma non di accedervi. [Scopri di più](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Senza avvisi. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/read | Ottiene il risultato delle operazioni dell'amministratore asincrono di Azure dell'istanza gestita. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Creare e gestire le impostazioni di controllo di SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | Recupera i dettagli dei criteri di controllo BLOB del server esteso configurati in uno specifico server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Creare e gestire le impostazioni di controllo dei database di SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Recupera i record di controllo BLOB del database |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Creare e gestire i criteri della maschera dei dati dei database dei server SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | Recupera i dettagli dei criteri di controllo BLOB esteso configurati in uno specifico database |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | Restituisce l'elenco dei database o ottiene le proprietà per il database specificato |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | Recupera uno schema del database. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | Recupera una colonna del database. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | Recupera una tabella del database. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Creare e gestire i criteri degli avvisi di sicurezza dei database di SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Creare e gestire le metriche di sicurezza dei database di server SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Creare e gestire i criteri degli avvisi di sicurezza di SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/read | Restituisce l'elenco delle istanze gestite o ottiene le proprietà per l'istanza gestita specificata. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/administrators/read | Ottiene un elenco degli amministratori di istanza gestita. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/administrators/read | Ottiene un oggetto Azure Active Directory amministratore specifico |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/administrators/read",
        "Microsoft.Sql/servers/administrators/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Collaboratore SQL Server

Consente di gestire i server e i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza. [Scopri di più](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/* | Creare e gestire server SQL |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Esegue la lettura delle metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Consente di leggere le definizioni della metrica |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Modificare le impostazioni di controllo di SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Modificare le impostazioni di controllo dei database di SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Recupera i record di controllo BLOB del database |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Modificare i criteri di mascheratura dei dati dei database di SQL server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Modificare i criteri degli avvisi di sicurezza dei database di SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Modificare le metriche di protezione dei database di SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Modificare i criteri degli avvisi di sicurezza di SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/delete | Elimina un server specifico Azure Active Directory solo l'oggetto di autenticazione |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | Aggiunge o aggiorna un server specifico Azure Active Directory solo l'oggetto di autenticazione |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analytics


### <a name="azure-event-hubs-data-owner"></a>Proprietario dei dati di Hub eventi di Azure

Consente l'accesso completo alle risorse di Hub eventi di Azure. [Scopri di più](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Ricevitore dei dati di Hub eventi di Azure

Consente l'accesso per la ricezione alle risorse dell'Hub eventi di Azure. [Scopri di più](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Mittente dei dati di Hub eventi di Azure

Consente l'accesso per l'invio alle risorse dell'Hub eventi di Azure. [Scopri di più](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Collaboratore Data Factory

Consente di creare e gestire data factory, oltre alle risorse figlio in esse contenute. [Scopri di più](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Creare e gestire data factory e le relative risorse figlio. |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/factoryies/* | Creare e gestire data factory e le relative risorse figlio. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Creare o aggiornare un oggetto eventSubscription |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Pulizia dati

Eliminare dati privati da un'area di lavoro Log Analytics. [Scopri di più](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | Ripulitura dei dati da Application Insights |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visualizzare i dati di analisi dei log |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | Elimina i dati specificati dall'area di lavoro |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>Operatore di cluster HDInsight

Consente di leggere e modificare le configurazioni dei cluster HDInsight. [Scopri di più](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | Recuperare le impostazioni del gateway per il cluster HDInsight |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | Aggiornare le impostazioni del gateway per il cluster HDInsight |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Collaboratore Servizi di dominio HDInsight

È possibile leggere, creare, modificare ed eliminare le operazioni correlate a Servizi di dominio necessarie per HDInsight Enterprise Security Package [altre informazioni](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Collaboratore di Log Analytics

Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure. [Scopri di più](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Installa o aggiorna le estensioni di Azure Arc |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Lettore di Log Analytics

Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. [Scopri di più](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Esegue la ricerca usando il nuovo motore. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Esegue una query di ricerca |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-curator"></a>Purview Data Esere

Il creatore di dati Microsoft.Purview può creare, leggere, modificare ed eliminare oggetti dati del catalogo e stabilire relazioni tra gli oggetti. Questo ruolo è in anteprima ed è soggetto a modifiche.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Leggere la risorsa account per il provider Microsoft Purview. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Legge gli oggetti dati. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/write | Creare, aggiornare ed eliminare oggetti dati. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data curator can create, read, modify and delete catalog data objects and establish relationships between objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "name": "8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read",
        "Microsoft.Purview/accounts/data/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Curator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-reader"></a>Lettore di dati di purview

Il lettore di dati Microsoft.Purview può leggere gli oggetti dati del catalogo. Questo ruolo è in anteprima ed è soggetto a modifiche.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Leggere la risorsa account per il provider Microsoft Purview. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Legge gli oggetti dati. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data reader can read catalog data objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ff100721-1b9d-43d8-af52-42b69c1272db",
  "name": "ff100721-1b9d-43d8-af52-42b69c1272db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-source-administrator"></a>Purview Data Source Administrator

L'amministratore dell'origine dati Microsoft.Purview può gestire le origini dati e le analisi dei dati. Questo ruolo è in anteprima ed è soggetto a modifiche.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Leggere la risorsa account per il provider Microsoft Purview. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/read | Leggere le origini dati e le analisi. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/write | Creare, aggiornare ed eliminare origini dati e gestire le analisi. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data source administrator can manage data sources and data scans. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/200bba9e-f0c8-430f-892b-6f0794863803",
  "name": "200bba9e-f0c8-430f-892b-6f0794863803",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/scan/read",
        "Microsoft.Purview/accounts/scan/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Source Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Collaboratore del Registro schemi (anteprima)

Lettura, scrittura ed eliminazione di gruppi e schemi del Registro schemi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Lettore del Registro schemi (anteprima)

Lettura ed elenco di gruppi e schemi del Registro schemi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/read | Ottenere l'elenco delle descrizioni delle risorse schemagroup |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/read | Recuperare gli schemi |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Accesso ai nodi di tipo membro della blockchain (anteprima)

Consente l'accesso ai nodi membro blockchain [Altre informazioni](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | Recupera o elenca i nodi della transazione di tipo membro della blockchain esistenti. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | Stabilisce la connessione a un nodo della transazione di tipo membro della blockchain. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>Intelligenza artificiale e Machine Learning


### <a name="cognitive-services-contributor"></a>Collaboratore Servizi cognitivi

Consente di creare, leggere, aggiornare, eliminare e gestire le chiavi di Servizi cognitivi. [Scopri di più](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | Ottiene le funzionalità di una sottoscrizione. |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/read | Ottiene la funzionalità di una sottoscrizione in un provider di risorse specificato. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Consente di leggere le definizioni del log |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Consente di leggere le definizioni della metrica |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Esegue la lettura delle metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-contributor"></a>Collaboratore servizi cognitivi Visione personalizzata

Accesso completo al progetto, inclusa la possibilità di visualizzare, creare, modificare o eliminare progetti. [Scopri di più](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the ability to view, create, edit, or delete projects.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "name": "c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Custom Vision Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-deployment"></a>Distribuzione di servizi Visione personalizzata cognitivi

Pubblicare, annullare la pubblicazione o esportare modelli. La distribuzione può visualizzare il progetto, ma non può aggiornarsi. [Scopri di più](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/publish/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/export/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/quicktest/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/detect/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Esporta un progetto. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Publish, unpublish or export models. Deployment can view the project but can't update.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "name": "5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/classify/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/detect/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Deployment",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-labeler"></a>Etichettatore Visione personalizzata Servizi cognitivi

Visualizzare, modificare le immagini di training e creare, aggiungere, rimuovere o eliminare i tag immagine. Gli etichettatori possono visualizzare il progetto, ma non possono aggiornare altro che le immagini e i tag di training. [Scopri di più](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Ottenere le immagini inviate all'endpoint di stima. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tags/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/suggested/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tagsandregions/suggestions/action | Questa API otterrà tag e aree suggeriti per una matrice/batch di immagini senza tag insieme alle confidenze per i tag. Restituisce una matrice vuota se non vengono trovati tag. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Esporta un progetto. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit training images and create, add, remove, or delete the image tags. Labelers can view the project but can't update anything other than training images and tags.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/88424f51-ebe7-446f-bc41-7fa16989e96c",
  "name": "88424f51-ebe7-446f-bc41-7fa16989e96c",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Labeler",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-reader"></a>Lettore di servizi cognitivi Visione personalizzata

Azioni di sola lettura nel progetto. I lettori non possono creare o aggiornare il progetto. [Scopri di più](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Ottenere le immagini inviate all'endpoint di stima. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Esporta un progetto. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only actions in the project. Readers can't create or update the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "name": "93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-trainer"></a>Cognitive Services Visione personalizzata Trainer

Visualizzare, modificare i progetti ed eseguire il training dei modelli, inclusa la possibilità di pubblicare, annullare la pubblicazione, esportare i modelli. Gli istruttori non possono creare o eliminare il progetto. [Scopri di più](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/action | Creare un progetto. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/delete | Eliminare un progetto specifico. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/import/action | Importa un progetto. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Esporta un progetto. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit projects and train the models, including the ability to publish, unpublish, export the models. Trainers can't create or delete the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "name": "0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/delete",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Trainer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Ruolo con autorizzazioni di lettura per i dati di Servizi cognitivi (anteprima)

Consente di leggere i dati di Servizi cognitivi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-metrics-advisor-administrator"></a>Amministratore Advisor metriche cognitivi

Accesso completo al progetto, inclusa la configurazione a livello di sistema. [Scopri di più](../cognitive-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/MetricsAdvisor/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the system level configuration.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cb43c632-a144-4ec5-977c-e80c4affc34a",
  "name": "cb43c632-a144-4ec5-977c-e80c4affc34a",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/MetricsAdvisor/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Metrics Advisor Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-editor"></a>Editor QnA Maker cognitivi

È ora possibile creare, modificare, importare ed esportare una KB. Non è possibile pubblicare o eliminare una KB. [Scopri di più](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Ottiene informazioni su un'assegnazione di ruolo. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Ottiene informazioni su una definizione di ruolo. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Ottiene l'elenco di Knowledge Base o i dettagli di una knowledge base specifica. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Scaricare la Knowledge Base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/create/write | Operazione asincrona per creare una nuova knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/write | Operazione asincrona per modificare una knowledge base o sostituire il contenuto della knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | Chiamata a GenerateAnswer per eseguire una query sulla knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/train/action | Eseguire il training di una chiamata per aggiungere suggerimenti alla knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | Scaricare le modifiche dal runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/write | Sostituire i dati delle modifiche. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Ottiene le chiavi dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/action | Genera nuovamente una chiave endpoint. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Ottiene le impostazioni dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/write | Aggiornare i seetting dell'endpoint per un endpoint. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/operations/read | Recupera i dettagli di una specifica operazione a esecuzione prolungata. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Ottiene l'elenco di Knowledge Base o i dettagli di una knowledge base specifica. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Scaricare la Knowledge Base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/create/write | Operazione asincrona per creare una nuova knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/write | Operazione asincrona per modificare una Knowledge Base o sostituire il contenuto della knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | Chiamata di GenerateAnswer per eseguire query sulla knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/train/action | Eseguire il training della chiamata per aggiungere suggerimenti alla Knowledge Base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Scaricare le modifiche dal runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/write | Sostituire i dati delle modifiche. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Ottiene le chiavi dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action | Genera nuovamente una chiave dell'endpoint. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Ottiene le impostazioni dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/write | Aggiornare i seetting dell'endpoint per un endpoint. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/operations/read | Recupera i dettagli di una specifica operazione a esecuzione prolungata. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Ottiene l'elenco di Knowledge Base o i dettagli di una knowledge base specifica. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Scaricare la knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write | Operazione asincrona per creare una nuova knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/write | Operazione asincrona per modificare una knowledge base o sostituire il contenuto della knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | Chiamata a GenerateAnswer per eseguire una query sulla knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action | Eseguire il training di una chiamata per aggiungere suggerimenti alla knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | Scaricare le modifiche dal runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/write | Sostituire i dati delle modifiche. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Ottiene le chiavi dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action | Genera nuovamente una chiave dell'endpoint. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Ottiene le impostazioni dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/write | Aggiornare i seetting dell'endpoint per un endpoint. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/operations/read | Recupera i dettagli di una specifica operazione a esecuzione prolungata. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you create, edit, import and export a KB. You cannot publish or delete a KB.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "name": "f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/operations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>Lettore di QnA Maker cognitivi

Leggere e testare solo una KB. [Scopri di più](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Ottiene informazioni su un'assegnazione di ruolo. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Ottiene informazioni su una definizione di ruolo. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Ottiene l'elenco di Knowledge Base o i dettagli di una knowledge base specifica. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Scaricare la Knowledge Base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | Chiamata a GenerateAnswer per eseguire una query sulla knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | Scaricare le modifiche dal runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Ottiene le chiavi dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Ottiene le impostazioni dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Ottiene l'elenco di Knowledge Base o i dettagli di una knowledge base specifica. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Scaricare la knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | Chiamata a GenerateAnswer per eseguire una query sulla knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Scaricare le modifiche dal runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Ottiene le chiavi dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Ottiene le impostazioni dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Ottiene l'elenco di Knowledge Base o i dettagli di una knowledge base specifica. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Scaricare la Knowledge Base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | Chiamata di GenerateAnswer per eseguire query sulla knowledge base. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | Scaricare le modifiche dal runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Ottiene le chiavi dell'endpoint per un endpoint |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Ottiene le impostazioni dell'endpoint per un endpoint |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you read and test a KB only.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/466ccd10-b268-4a11-b098-b4849f024126",
  "name": "466ccd10-b268-4a11-b098-b4849f024126",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Utente Servizi cognitivi

Consente di leggere ed elencare le chiavi di Servizi cognitivi. [Scopri di più](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | Elenco delle chiavi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Legge un avviso della metrica (versione classica) |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | Legge un'impostazione di diagnostica della risorsa |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Consente di leggere le definizioni del log |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Consente di leggere le definizioni della metrica |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Esegue la lettura delle metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="internet-of-things"></a>Internet delle cose


### <a name="device-update-administrator"></a>Amministratore dell'aggiornamento del dispositivo

Offre l'accesso completo alle operazioni di gestione e contenuto [Altre informazioni](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Esegue un'operazione di lettura correlata agli aggiornamenti |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Esegue un'operazione di scrittura correlata agli aggiornamenti |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Esegue un'operazione di eliminazione correlata agli aggiornamenti |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Esegue un'operazione di lettura correlata alla gestione |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | Esegue un'operazione di scrittura correlata alla gestione |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | Esegue un'operazione di eliminazione correlata alla gestione |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management and content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "name": "02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete",
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-administrator"></a>Amministratore del contenuto dell'aggiornamento del dispositivo

Offre l'accesso completo alle operazioni sui contenuti [Altre informazioni](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Esegue un'operazione di lettura correlata agli aggiornamenti |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Esegue un'operazione di scrittura correlata agli aggiornamenti |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Esegue un'operazione di eliminazione correlata agli aggiornamenti |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "name": "0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-reader"></a>Lettore di contenuto per l'aggiornamento del dispositivo

Consente l'accesso in lettura alle operazioni sul contenuto, ma non consente di apportare modifiche [Altre informazioni](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Esegue un'operazione di lettura correlata agli aggiornamenti |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "name": "d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-administrator"></a>Amministratore delle distribuzioni di aggiornamenti del dispositivo

Offre l'accesso completo alle operazioni di gestione [Altre informazioni](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Esegue un'operazione di lettura correlata alla gestione |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | Esegue un'operazione di scrittura correlata alla gestione |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | Esegue un'operazione di eliminazione correlata alla gestione |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e4237640-0e3d-4a46-8fda-70bc94856432",
  "name": "e4237640-0e3d-4a46-8fda-70bc94856432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-reader"></a>Lettore delle distribuzioni di aggiornamenti del dispositivo

Consente l'accesso in lettura alle operazioni di gestione, ma non consente di apportare modifiche [Altre informazioni](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Esegue un'operazione di lettura correlata alla gestione |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "name": "49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-reader"></a>Lettore di aggiornamenti del dispositivo

Consente l'accesso in lettura alle operazioni di gestione e contenuto, ma non consente di apportare modifiche [Altre informazioni](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Esegue un'operazione di lettura correlata agli aggiornamenti |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Esegue un'operazione di lettura correlata alla gestione |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management and content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "name": "e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Realtà mista


### <a name="remote-rendering-administrator"></a>Amministratore rendering remoto

Offre all'utente funzionalità di conversione, gestione della sessione, rendering e diagnostica per Rendering remoto di Azure [Altre informazioni](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | Avviare la conversione asset |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | Ottenere le proprietà di conversione degli asset |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | Arrestare la conversione di asset |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Ottenere le proprietà della sessione |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Avviare le sessioni |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Arrestare le sessioni |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Connettersi a una sessione |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Connettersi al controllo rendering remoto |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Client di rendering remoto

Fornisce all'utente funzionalità di gestione della sessione, del rendering e della diagnostica per Rendering remoto di Azure. [Scopri di più](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Ottenere le proprietà della sessione |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Avviare le sessioni |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Arrestare le sessioni |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Connettersi a una sessione |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Connettersi al controllo rendering remoto |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Collaboratore per l'account per gli ancoraggi spaziali

Consente di gestire gli ancoraggi nello spazio nell'account, ma non di eliminarli [Altre informazioni](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Creare ancoraggi nello spazio |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Individuare gli ancoraggi nello spazio vicini |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Recuperare le proprietà degli ancoraggi nello spazio |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Individuare gli ancoraggi nello spazio |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio di ancoraggi nello spazio di Azure |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Aggiornare le proprietà degli ancoraggi nello spazio |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Proprietario dell'account per gli ancoraggi spaziali

Consente di gestire gli ancoraggi nello spazio nell'account, inclusa l'eliminazione. [Altre informazioni](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Creare ancoraggi nello spazio |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | Eliminare gli ancoraggi nello spazio |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Individuare gli ancoraggi nello spazio vicini |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Recuperare le proprietà degli ancoraggi nello spazio |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Individuare gli ancoraggi nello spazio |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio di ancoraggi nello spazio di Azure |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Aggiornare le proprietà degli ancoraggi nello spazio |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Ruolo Lettore dell'account per gli ancoraggi spaziali

Consente di individuare e leggere le proprietà degli ancoraggi nello spazio nell'account [Altre informazioni](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Individuare gli ancoraggi nello spazio vicini |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Recuperare le proprietà degli ancoraggi nello spazio |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Individuare gli ancoraggi nello spazio |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio di ancoraggi nello spazio di Azure |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integrazione


### <a name="api-management-service-contributor"></a>Collaboratore servizio Gestione API

Può gestire il servizio e le API Altre [informazioni](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | È in grado di creare e gestire il servizio Gestione API |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Ruolo operatore del servizio Gestione API

Può gestire il servizio ma non le API Altre [informazioni](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Leggere le istanze del servizio Gestione API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | Esegue il backup del servizio Gestione API nel contenitore specificato in un account di archiviazione fornito dall’utente |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | Elimina l’istanza del servizio Gestione API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | Modifica SKU/unità, aggiunge/rimuove distribuzioni regionali del servizio Gestione API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Leggere i metadati per un'istanza del servizio Gestione API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | Ripristinare il servizio Gestione API dal contenitore specificato in un account di archiviazione fornito dall'utente |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | Carica il certificato TLS/SSL per un servizio Gestione API. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | Configura, aggiorna o rimuove i nomi di dominio personalizzati per un servizio Gestione API. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | Creare o aggiornare un'istanza del servizio Gestione API di Azure |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Recuperare le chiavi associate all'utente |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Ruolo lettura del servizio Gestione API

Accesso in sola lettura al servizio e alle API [Altre informazioni](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Leggere le istanze del servizio Gestione API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Leggere i metadati per un'istanza del servizio Gestione API |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Recuperare le chiavi associate all'utente |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Proprietario dei dati di Configurazione dell'app

Consente l'accesso completo ai dati di Configurazione dell'app. [Scopri di più](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Ruolo con autorizzazioni di lettura per i dati di Configurazione dell'app

Consente l'accesso in lettura ai dati di Configurazione dell'app. [Scopri di più](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Proprietario dei dati del bus di servizio di Azure

Consente l'accesso completo alle risorse del bus di servizio di Azure. [Scopri di più](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Ricevitore dei dati del bus di servizio di Azure

Consente l'accesso per la ricezione alle risorse del bus di servizio di Azure. [Scopri di più](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Mittente dei dati del bus di servizio di Azure

Consente l'accesso per l'invio alle risorse del bus di servizio di Azure. [Scopri di più](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Proprietario della registrazione di Azure Stack

Consente di gestire le registrazioni di Azure Stack.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | Ottenere le proprietà di una Azure Stack Edge sottoscrizione |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | Ottiene le proprietà di un prodotto del marketplace Azure Stack |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Ottiene le proprietà di una registrazione di Azure Stack |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-contributor"></a>Collaboratore EventGrid

Consente di gestire le operazioni di EventGrid.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/* | Creare e gestire risorse di Griglia di eventi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1e241071-0855-49ea-94dc-649edcd759de",
  "name": "1e241071-0855-49ea-94dc-649edcd759de",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>Collaboratore per sottoscrizioni di eventi di Griglia di eventi

Consente di gestire le operazioni di sottoscrizione di eventi EventGrid. [Scopri di più](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* | Creare e gestire sottoscrizioni di eventi a livello di regione |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Elenca le sottoscrizioni di eventi globali per tipo di argomento |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Elenca sottoscrizioni di eventi per area |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Elenca sottoscrizioni di eventi per area per tipo di argomento |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>Ruolo con autorizzazioni di lettura per sottoscrizioni di eventi di Griglia di eventi

Consente di leggere le sottoscrizioni di eventi EventGrid. [Scopri di più](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Leggere un oggetto eventSubscription |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Elenca le sottoscrizioni di eventi globali per tipo di argomento |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Elenca sottoscrizioni di eventi per area |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Elenca sottoscrizioni di eventi per area per tipo di argomento |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>Collaboratore ai dati FHIR

Ruolo consente all'utente o all'entità l'accesso completo ai dati FHIR [Altre informazioni](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR Data Exporter

Ruolo consente all'utente o all'entità di leggere ed esportare dati FHIR [Altre informazioni](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Leggere le risorse FHIR (include la ricerca e la cronologia con controllo delle versioni).  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | Operazione di esportazione ($export). |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>Lettore dati FHIR

Ruolo consente all'utente o all'entità di leggere i dati FHIR [Altre informazioni](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Leggere le risorse FHIR (include la ricerca e la cronologia con controllo delle versioni).  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHIR Data Writer

Ruolo consente all'utente o all'entità di leggere e scrivere dati FHIR [Altre informazioni](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | Eliminazione completa (inclusa la cronologia delle versioni). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>ambiente del servizio di integrazione Collaboratore

Consente di gestire gli ambienti del servizio di integrazione, ma non di accedervi. [Scopri di più](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>ambiente del servizio di integrazione Developer

Consente agli sviluppatori di creare e aggiornare flussi di lavoro, account di integrazione e connessioni API negli ambienti del servizio di integrazione. [Scopri di più](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | Legge l'ambiente del servizio di integrazione. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/join/action |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/*/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Collaboratore account Intelligent Systems

Consente di gestire gli account Sistemi intelligenti, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | Microsoft.IntelligentSystems/accounts/* | Creare e gestire account di Intelligent Systems |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Collaboratore alle app per la logica

Consente di gestire le app per la logica, ma non di modificarne l'accesso. [Scopri di più](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Restituisce l'account di archiviazione con l'account specificato. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai log attività tramite il portale. Elencare categorie di log nel log attività. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | Gestisce le risorse di App per la logica. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Crea e gestisce un gateway di connessione. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/* | Crea e gestisce una connessione. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | Crea e gestisce un'API personalizzata. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Aggiunge un piano di servizio app |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Ottiene le proprietà per un piano di servizio app |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | Elenca i segreti delle funzioni. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Operatore delle app per la logica

Consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle. [Scopri di più](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | Legge le regole di avviso di Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | Ottiene le impostazioni di diagnostica di App per la logica |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | Ottiene le metriche disponibili per App per la logica. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | Legge le risorse di App per la logica. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | Disabilita il flusso di lavoro. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | Abilita il flusso di lavoro. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | Convalida il flusso di lavoro. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | Legge i gateway di connessione. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | Legge le connessioni. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | Legge l'API personalizzata. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Ottiene le proprietà per un piano di servizio app |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identità


### <a name="managed-identity-contributor"></a>Managed Identity Contributor (Collaboratore per identità gestita)

Creare, leggere, aggiornare ed eliminare un'identità assegnata dall'utente [Altre informazioni](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | Ottiene l'identità assegnata a un utente esistente |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | Crea una nuova identità assegnata a un utente esistente o aggiorna i tag associati a un'identità assegnata a un utente esistente |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | Elimina l'identità assegnata a un utente esistente |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Managed Identity Operator (Operatore per identità gestita)

Leggere e assegnare un'identità assegnata [dall'utente Altre informazioni](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Sicurezza


### <a name="attestation-contributor"></a>Collaboratore per l'attestazione

Può leggere la scrittura o eliminare l'istanza del provider di attestazione [Altre informazioni](../attestation/quickstart-powershell.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | Microsoft.Attestation/attestationProviders/attestation/write |  |
> | Microsoft.Attestation/attestationProviders/attestation/delete |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read write or delete the attestation provider instance",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "name": "bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read",
        "Microsoft.Attestation/attestationProviders/attestation/write",
        "Microsoft.Attestation/attestationProviders/attestation/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="attestation-reader"></a>Lettore di attestazione

Può leggere le proprietà del provider di attestazione [Altre informazioni](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read the attestation provider properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "name": "fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-automation-contributor"></a>Azure Sentinel Collaboratore di Automazione

Azure Sentinel Collaboratore di [Automazione Altre informazioni](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/read | Esegue la lettura del trigger. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/listCallbackUrl/action | Ottiene l'URL di callback per il trigger. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/runs/read | Esegue la lettura dell'esecuzione del flusso di lavoro. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Automation Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "name": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Logic/workflows/triggers/read",
        "Microsoft.Logic/workflows/triggers/listCallbackUrl/action",
        "Microsoft.Logic/workflows/runs/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Automation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-contributor"></a>Collaboratore di Azure Sentinel

Azure Sentinel Collaboratore [Altre informazioni](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Esegue la ricerca usando il nuovo motore. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visualizzare i dati di analisi dei log |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Ottiene soluzione OMS esistente |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Esegue query sui dati nell'area di lavoro |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Ottiene le origini dati in un'area di lavoro. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Leggere una cartella di lavoro privata |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Ruolo con autorizzazioni di lettura di Azure Sentinel

Azure Sentinel Lettore [Altre informazioni](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Controllare l'autorizzazione utente e la licenza |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Eseguire query su indicatori di intelligence per le minacce |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Eseguire query su indicatori di intelligence per le minacce |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Esegue la ricerca usando il nuovo motore. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visualizzare i dati di analisi dei log |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | Ottiene i servizi collegati nell'area di lavoro specificata. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Ottiene una query di ricerca salvata |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Ottiene soluzione OMS esistente |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Esegue query sui dati nell'area di lavoro |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Ottiene le origini dati in un'area di lavoro. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Leggere una cartella di lavoro |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Leggere una cartella di lavoro privata |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Risponditore di Azure Sentinel

Azure Sentinel Responder [Altre informazioni](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Controllare l'autorizzazione utente e la licenza |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Aggiungere tag all'indicatore di Intelligence per le minacce |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Indicatori di Intelligence per le minacce di query |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/action | Intelligence per le minacce per i tag bulk |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Aggiungere tag all'indicatore di intelligence per le minacce |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/action | Sostituire i tag dell'indicatore di intelligence per le minacce |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Eseguire query su indicatori di intelligence per le minacce |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Esegue la ricerca usando il nuovo motore. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visualizzare i dati di analisi dei log |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Ottiene le origini dati in un'area di lavoro. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Ottiene una query di ricerca salvata |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Ottiene soluzione OMS esistente |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Esegue query sui dati nell'area di lavoro |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Ottiene le origini dati in un'area di lavoro. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Leggere una cartella di lavoro |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Leggere una cartella di lavoro privata |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/automationRules/*",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator"></a>Key Vault amministratore

Eseguire tutte le operazioni del piano dati su un insieme di credenziali delle chiavi e su tutti gli oggetti in esso contenuti, inclusi certificati, chiavi e segreti. Impossibile gestire le risorse dell'insieme di credenziali delle chiavi o le assegnazioni di ruolo. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer"></a>Key Vault Certificates Officer

Eseguire qualsiasi azione sui certificati di un insieme di credenziali delle chiavi, ad eccezione delle autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Collaboratore di Key Vault

Gestire gli insiemi di credenziali delle chiavi, ma non consente di assegnare ruoli nel controllo degli accessi in base al ruolo di Azure e non consente di accedere a segreti, chiavi o certificati. [Scopri di più](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | Ripulisce un Key Vault eliminato temporaneamente |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer"></a>Key Vault Crypto Officer

Eseguire qualsiasi azione sulle chiavi di un insieme di credenziali delle chiavi, ad eccezione delle autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user"></a>Key Vault utente crittografia del servizio Crypto

Leggere i metadati delle chiavi ed eseguire operazioni di wrapping/annullamento del wrapping. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Creare o aggiornare un oggetto eventSubscription |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Leggere un oggetto eventSubscription |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/delete | Eliminare un eventoSottoscrizione |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Elencare le chiavi nell'insieme di credenziali specificato oppure leggere le proprietà e il materiale pubblico di una chiave. Per le chiavi asimmetriche, questa operazione espone la chiave pubblica e include la possibilità di eseguire algoritmi a chiave pubblica, ad esempio crittografare e verificare la firma. Le chiavi private e le chiavi simmetriche non vengono mai esposte. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Esegue il wrapping di una chiave simmetrica con Key Vault chiave. Si noti che se la chiave Key Vault è asimmetrica, questa operazione può essere eseguita da entità con accesso in lettura. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Annulla il wrapping di una chiave simmetrica con Key Vault chiave. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventGrid/eventSubscriptions/write",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/eventSubscriptions/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user"></a>Key Vault Crypto User

Eseguire operazioni di crittografia usando le chiavi. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Elencare le chiavi nell'insieme di credenziali specificato oppure leggere le proprietà e il materiale pubblico di una chiave. Per le chiavi asimmetriche, questa operazione espone la chiave pubblica e include la possibilità di eseguire algoritmi a chiave pubblica, ad esempio crittografare e verificare la firma. Le chiavi private e simmetriche non vengono mai esposte. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | Aggiorna gli attributi specificati associati alla chiave specificata. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | Crea il file di backup di una chiave. Il file può essere usato per ripristinare la chiave in un Key Vault della stessa sottoscrizione. Possono essere applicate restrizioni. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | Crittografa testo non crittografato con una chiave. Si noti che se la chiave è asimmetrica, questa operazione può essere eseguita da entità con accesso in lettura. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | Decrittografa il testo crittografato con una chiave. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Esegue il wrapping di una chiave simmetrica con una Key Vault simmetrica. Si noti che se la Key Vault chiave è asimmetrica, questa operazione può essere eseguita dalle entità con accesso in lettura. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Annulla il wrapping di una chiave simmetrica con una Key Vault simmetrica. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | Firma un digest del messaggio (hash) con una chiave. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | Verifica la firma di un digest del messaggio (hash) con una chiave. Si noti che se la chiave è asimmetrica, questa operazione può essere eseguita da entità con accesso in lettura. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader"></a>Key Vault lettore

Leggere i metadati degli insiemi di credenziali delle chiavi e dei relativi certificati, chiavi e segreti. Impossibile leggere i valori sensibili, ad esempio il contenuto del segreto o il materiale della chiave. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Elencare o visualizzare le proprietà di un segreto, ma non il relativo valore. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer"></a>Key Vault Secrets Officer

Eseguire qualsiasi azione sui segreti di un insieme di credenziali delle chiavi, ad eccezione delle autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user"></a>Key Vault Secrets User

Leggere il contenuto del segreto. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | Ottiene il valore di un segreto. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Elencare o visualizzare le proprietà di un segreto, ma non il relativo valore. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>Collaboratore HSM gestito

Consente di gestire i pool di HSM gestiti, ma non di accedervi. [Scopri di più](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHSMs/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage managed HSM pools, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18500a29-7fe2-46b2-a342-b16a415e101d",
  "name": "18500a29-7fe2-46b2-a342-b16a415e101d",
  "permissions": [
    {
      "actions": [
        "Microsoft.KeyVault/managedHSMs/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed HSM contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Amministrazione della protezione

Visualizzazione e aggiornamento delle autorizzazioni per il Centro sicurezza. Ha le stesse autorizzazioni del Ruolo con autorizzazioni di lettura per la sicurezza e può anche aggiornare i criteri di sicurezza e rimuovere gli avvisi e le raccomandazioni. [Scopri di più](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Creare e gestire assegnazioni di criteri |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Creare e gestire definizioni di criteri |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | Creare e gestire le esenzioni dei criteri |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Creare e gestire set di criteri |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visualizzare i dati di analisi dei log |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Creare e gestire criteri e componenti di protezione |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Collaboratore per valutazioni della sicurezza

Consente di eseguire il push delle valutazioni nel Centro sicurezza

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | Creare o aggiornare valutazioni della sicurezza per la sottoscrizione |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Gestore sicurezza (legacy)

Questo è un ruolo legacy. Usare invece Amministratore della sicurezza.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | Leggere le informazioni di configurazione delle macchine virtuali classiche |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | Scrivere la configurazione delle macchine virtuali classiche |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | Leggere le informazioni della configurazione sulla rete classica |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Creare e gestire criteri e componenti di protezione |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Ruolo con autorizzazioni di lettura per la sicurezza

Visualizzazione delle autorizzazioni per il Centro sicurezza. Può visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non può apportare modifiche. [Scopri di più](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Legge un avviso della metrica (versione classica) |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visualizzare i dati di analisi dei log |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | Leggere criteri e componenti di sicurezza |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/action | Ottiene informazioni sui pacchetti IoT Defender scaricabili |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/action | Scaricare il file di attivazione del manager con i dati della quota di sottoscrizione |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/action | Scarica il file della password di reimpostazione per i sensori IoT |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Security/iotDefenderSettings/packageDownloads/action",
        "Microsoft.Security/iotDefenderSettings/downloadManagerActivation/action",
        "Microsoft.Security/iotSensors/downloadResetPassword/action",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Utente DevTest Labs

Consente di connettere, avviare, riavviare e arrestare le macchine virtuali in Azure DevTest Labs. [Scopri di più](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | Ottiene le proprietà di un set di disponibilità |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | Leggere le proprietà di una macchina virtuale (dimensioni della VM, stato di runtime, estensioni della VM e così via) |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | Disabilita la macchina virtuale e rilascia le risorse di calcolo |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Ottiene le proprietà di una macchina virtuale |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | Riavvia la macchina virtuale |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | Avvia la macchina virtuale |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | Leggere le proprietà di un lab |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | Attesta una macchina virtuale attestabile casualmente nel lab. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | Crea macchine virtuali in un lab. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | Consente di verificare che nel lab sia presente un profilo valido per l'utente corrente. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | Elimina le formule. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | Esegue la lettura di formule. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | Aggiunge o modifica le formule. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | Valuta i criteri del lab. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | Consente di assumere la proprietà di una macchina virtuale esistente |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | Elenca le pianificazioni di avvio/arresto applicabili, se presenti. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | Ottiene una stringa che rappresenta il contenuto del file RDP per la macchina virtuale |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Aggiunge un pool di indirizzi di back-end del servizio di bilanciamento del carico. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Aggiunge una regola NAT in entrata del servizio di bilanciamento del carico. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | Legge le proprietà di un'interfaccia di rete, ad esempio tutti i servizi di bilanciamento del carico di cui fa parte l'interfaccia di rete |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | Aggiunge una macchina virtuale a un'interfaccia di rete. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Crea un'interfaccia di rete o ne aggiorna una esistente.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | Leggere le proprietà di un indirizzo IP pubblico |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Aggiunge un indirizzo IP pubblico. Senza avvisi. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Aggiunge una rete virtuale. Senza avvisi. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Ottiene o elenca le distribuzioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | **NotActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | Elenca le dimensioni disponibili a cui è possibile aggiornare la macchina virtuale |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Lab Creator (Creatore di lab)

Consente di creare nuovi lab con gli account lab di Azure. [Scopri di più](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | Crea un lab in un account del lab. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | Consente di ottenere i prezzi e la disponibilità di combinazioni di dimensioni, aree geografiche e sistemi operativi per l'account lab. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | Ottiene informazioni sulle restrizioni di base e l'utilizzo per questa sottoscrizione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Monitorare


### <a name="application-insights-component-contributor"></a>Collaboratore componente di Application Insights

Può gestire i Application Insights altre [informazioni](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire le regole di avviso classiche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/read | Ottenere il token per le metriche in tempo reale |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Creare e gestire nuove regole di avviso |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | È in grado di creare e gestire i componenti di Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/topology/read | Topologia di lettura |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/transactions/read | Lettura di transazioni |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Creare e gestire i test Web di Insights |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/generateLiveToken/read",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/topology/read",
        "Microsoft.Insights/transactions/read",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Debugger di snapshot di Application Insights

Concede l'autorizzazione utente per visualizzare e scaricare gli snapshot di debug raccolti con Application Insights Snapshot Debugger. Si noti che queste autorizzazioni non sono incluse nei ruoli [Proprietario](#owner) e [Collaboratore](#contributor). Quando si assegna agli utenti il ruolo Snapshot Debugger di Application Insights, è necessario concedere il ruolo direttamente all'utente. Il ruolo non viene riconosciuto quando viene aggiunto a un ruolo personalizzato. [Scopri di più](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Collaboratore al monitoraggio

Può leggere tutti i dati del monitoraggio e modificare le impostazioni di monitoraggio. Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [Scopri di più](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | È in grado di creare e gestire i componenti di Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Elenco degli eventi dei registri attività (eventi di gestione) in una sottoscrizione. Questa autorizzazione è applicabile sia all'accesso programmatico che all'accesso al portale per il registro attività. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai log attività tramite il portale. Elencare categorie di log nel log attività. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Metriche per una risorsa. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registra il provider Microsoft Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Creare e gestire i test Web di Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | Crea una nuova area di lavoro o collega a un'area di lavoro esistente fornendo l'ID cliente dall’area di lavoro esistente. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Consente di leggere, scrivere ed eliminare pacchetti di soluzioni dell'analisi dei log. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | Consente di leggere, scrivere ed eliminare ricerche salvate dell'analisi dei log. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Esegue una query di ricerca |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | Consente di leggere, scrivere ed eliminare le configurazioni di dati di archiviazione dell'analisi dei log. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | Ottenere informazioni sui monitoraggi dell'integrità delle macchine virtuali guest. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Autore delle metriche di monitoraggio

Abilita la pubblicazione di metriche sulle risorse di Azure [Altre informazioni](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registra il provider Microsoft Insights |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Scrive metrica |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Lettore di monitoraggio

Può leggere tutti i dati del monitoraggio (metriche, log e così via). Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [Scopri di più](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Esegue una query di ricerca |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Collaboratore per le cartelle di lavoro

Può salvare le cartelle di lavoro condivise. [Scopri di più](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | Creare o aggiornare una cartella di lavoro |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | Eliminare una cartella di lavoro |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Leggere una cartella di lavoro |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Ruolo con autorizzazioni di lettura per le cartelle di lavoro

Può leggere le cartelle di lavoro. [Scopri di più](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Leggere una cartella di lavoro |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Gestione e governance


### <a name="automation-job-operator"></a>Operatore processo di automazione

Consente di creare e gestire i processi tramite i runbook di Automazione. [Scopri di più](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Legge le risorse del ruolo di lavoro ibrido per runbook |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Ottiene un processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Riprende un processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Arresta un processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Ottiene un flusso del processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Sospende un processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Crea un processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Ottiene l'output di un processo |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Operatore di automazione

Gli operatori di automazione possono avviare, arrestare, sospendere e riprendere i processi [Altre informazioni](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Legge le risorse del ruolo di lavoro ibrido per runbook |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Ottiene un processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Riprende un processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Arresta un processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Ottiene un flusso del processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Sospende un processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Crea un processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | Ottiene una pianificazione del processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | Crea una pianificazione del processo di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | Ottiene l'area di lavoro collegata all'account di automazione |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | Ottiene un account di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Ottiene un runbook di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | Ottiene un asset della pianificazione di automazione di Azure |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | Crea o aggiorna un asset della pianificazione di automazione di Azure |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Ottiene l'output di un processo |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Operatore runbook di automazione

Consente di leggere le proprietà del runbook per permettere di creare processi del runbook. [Scopri di più](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Ottiene un runbook di automazione di Azure |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-enabled-kubernetes-cluster-user-role"></a>Azure Arc ruolo utente del cluster Kubernetes abilitato

Elencare le credenziali utente del cluster.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crea o aggiorna una distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/listClusterUserCredentials/action | Elencare le credenziali dell'utente cluster |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credentials action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "name": "00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/listClusterUserCredentials/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Enabled Kubernetes Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-admin"></a>Azure Arc amministratore di Kubernetes

Consente di gestire tutte le risorse nel cluster/spazio dei nomi, ad eccezione dell'aggiornamento o dell'eliminazione delle quote e degli spazi dei nomi delle risorse. [Scopri di più](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crea o aggiorna una distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Legge i controllerrevision |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write | Scrive localsubjectaccessreviews |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Legge gli eventi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Legge gli eventi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Legge gli intervallo di limiti |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Legge gli spazi dei nomi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/rolebindings/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/roles/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Legge resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "name": "dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-cluster-admin"></a>Azure Arc amministratore del cluster Kubernetes

Consente di gestire tutte le risorse nel cluster. [Scopri di più](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crea o aggiorna una distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "name": "8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-viewer"></a>Azure Arc visualizzatore Kubernetes

Consente di visualizzare tutte le risorse nel cluster/spazio dei nomi, ad eccezione dei segreti. [Scopri di più](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crea o aggiorna una distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Legge i controllerrevision |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/read | Legge i daemonset |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/read | Legge le distribuzioni |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/read | Legge i set di repliche |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/read | Legge i set con stato |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/read | Legge horizontalpodautoscalers |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/read | Legge cronjobs |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/read | Legge i processi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/read | Legge le mappe di configurazione |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/read | Legge gli endpoint |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Legge gli eventi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Legge gli eventi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/read | Legge i daemonset |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/read | Legge le distribuzioni |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/read | Legge i dati in ingresso |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/read | Legge networkpolicies |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/read | Legge i set di repliche |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Legge gli intervallo di limiti |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Legge gli spazi dei nomi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/read | Legge i dati in ingresso |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/read | Legge lepolizie di rete |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/read | Legge persistentvolumeclaims |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/read | Legge i pod |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/read | Legge poddisruptionbudgets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Legge i controller di replica |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Legge i controller di replica |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Legge resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/read | Legge gli account del servizio |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/read | Legge i servizi |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/63f0a09d-1495-4db4-a681-037d84835eb4",
  "name": "63f0a09d-1495-4db4-a681-037d84835eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/read",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/read",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/read",
        "Microsoft.Kubernetes/connectedClusters/configmaps/read",
        "Microsoft.Kubernetes/connectedClusters/endpoints/read",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/read",
        "Microsoft.Kubernetes/connectedClusters/pods/read",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/read",
        "Microsoft.Kubernetes/connectedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Viewer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-writer"></a>Azure Arc Kubernetes Writer

Consente di aggiornare tutti gli elementi nel cluster/spazio dei nomi, ad eccezione dei ruoli (cluster) e delle associazioni di ruolo (cluster). [Scopri di più](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crea o aggiorna una distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Legge i controllerrevision |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Legge gli eventi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Legge gli eventi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Legge gli intervallo di limiti |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Legge gli spazi dei nomi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Legge resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5b999177-9696-4545-85c7-50de3797e5a1",
  "name": "5b999177-9696-4545-85c7-50de3797e5a1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Onboarding di Azure Connected Machine

Può eseguire l'onboarding di Azure Connected Machine. [Scopri di più](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Legge qualsiasi computer Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Scrive sui computer Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/read | Legge tutti Azure Arc privateLinkScopes |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | Assegnazione di configurazione guest Get. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/privateLinkScopes/read",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Amministratore delle risorse di Azure Connected Machine

Può leggere, scrivere, eliminare e ripetere l'onboarding di Azure Connected Machine.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Legge qualsiasi computer Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Scrive sui computer Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Elimina un computer Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Installa o aggiorna le estensioni di Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/privateLinkScopes/*",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Lettore per la fatturazione

Consente l'accesso in lettura ai dati di fatturazione [Altre informazioni](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | Lettura delle informazioni di fatturazione |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Collaboratore di progetto

Può gestire le definizioni di progetto, ma non assegnarle. [Scopri di più](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | Crea e gestisce definizioni di progetto o artefatti del progetto. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Operatore di progetto

Può assegnare i progetti pubblicati esistenti, ma non creare nuovi progetti. Si noti che funziona solo se l'assegnazione viene eseguita con un'identità gestita assegnata dall'utente. [Scopri di più](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Crea e gestisce le assegnazioni del progetto. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Collaboratore Gestione costi

Può visualizzare i costi e gestire la configurazione dei costi (ad esempio budget, esportazioni) [Altre informazioni](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Ottiene le configurazioni |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Legge i suggerimenti |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Lettore Gestione costi

Può visualizzare i dati sui costi e la configurazione (ad esempio budget, esportazioni) [Altre informazioni](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Ottiene le configurazioni |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Legge i suggerimenti |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Amministratore impostazioni gerarchia

Consente agli utenti di modificare ed eliminare le impostazioni della gerarchia

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | Crea o aggiorna le impostazioni gerarchia del gruppo di gestione. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | Elimina le impostazioni gerarchia del gruppo di gestione. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes Cluster - Azure Arc Onboarding

Definizione del ruolo per autorizzare qualsiasi utente/servizio a creare una risorsa cluster connessa [Altre informazioni](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crea o aggiorna una distribuzione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Scrive i cluster connessi |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | Leggere i cluster connessi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Ruolo collaboratore per applicazioni gestite

Consente la creazione di risorse di applicazioni gestite.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | Esegue la registrazione in Microsoft Solutions. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Ruolo di Operatore applicazione gestita

Consente di leggere ed eseguire azioni sulle risorse dell'applicazione gestita

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/read | Recupera un elenco di applicazioni. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Lettore applicazioni gestite

Consente di leggere le risorse in un accesso di app gestita e JIT richiesta.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti

Il Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti consente agli utenti del tenant di gestione di eliminare l'assegnazione della registrazione assegnata al proprio tenant. [Scopri di più](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | Recupera un elenco di assegnazioni della registrazione di servizi gestiti. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | Rimuove l'assegnazione della registrazione di servizi gestiti. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | Legge lo stato dell'operazione per la risorsa. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Collaboratore gruppo di gestione

Ruolo Collaboratore Gruppo di gestione [Altre informazioni](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | Elimina un gruppo di gestione. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | Annulla l'associazione della sottoscrizione dal gruppo di gestione. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | Associa una sottoscrizione esistente al gruppo di gestione. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | Crea o aggiorna un gruppo di gestione. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Elenca la sottoscrizione nel gruppo di gestione specificato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Lettore gruppo di gestione

Ruolo Lettore gruppo di gestione

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Elenca la sottoscrizione nel gruppo di gestione specificato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Collaboratore account New Relic APM

Consente di gestire gli account e le applicazioni di APR New Relic, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Ruolo con autorizzazioni di scrittura per i dati di Policy Insights (anteprima)

Consente l'accesso in lettura ai criteri delle risorse e l'accesso in scrittura agli eventi dei criteri dei componenti delle risorse. [Scopri di più](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | Ottiene informazioni su un'assegnazione di criteri. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | Ottiene informazioni su una definizione di criteri. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/read | Ottenere informazioni su un'esenzione dei criteri. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | Ottiene informazioni su una definizione di set di criteri. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | Controlla lo stato di conformità di un componente specifico in base ai criteri dei dati. |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | Registra gli eventi dei criteri per il componente della risorsa. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="quota-request-operator"></a>Operatore di richiesta quota

Leggere e creare richieste di quota, ottenere lo stato della richiesta di quota e creare ticket di supporto. [Scopri di più](/rest/api/reserved-vm-instances/quotaapi)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/read | Ottenere il limite o la quota di servizio corrente della risorsa e del percorso specificati |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/write | Creare un limite o una quota del servizio per la risorsa e il percorso specificati |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimitsRequests/read | Ottenere qualsiasi richiesta di limite di servizio per la risorsa e il percorso specificati |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Registra il provider di risorse Capacity e consente la creazione di risorse Capacity. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and create quota requests, get quota request status, and create support tickets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "name": "0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "permissions": [
    {
      "actions": [
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/read",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/write",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimitsRequests/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Quota Request Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>Acquirente di prenotazioni

Consente di acquistare prenotazioni Altre [informazioni](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Registra il provider di risorse Capacity e consente la creazione di risorse Capacity. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/register/action | Registra la sottoscrizione con il provider di risorse Microsoft.Compute |
> | [Microsoft.SQL](resource-provider-operations.md#microsoftsql)/register/action | Registra la sottoscrizione per il provider di risorse del database Microsoft SQL e consente la creazione di database Microsoft SQL. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/register/action | Esegue la registrazione nel provider di risorse Consumption |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/catalogs/read | Legge il catalogo delle prenotazioni |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Ottiene informazioni su un'assegnazione di ruolo. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/reservationRecommendations/read | Elenca le raccomandazioni singole o condivise relative alle istanze riservate per una sottoscrizione. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/supporttickets/write | Consente la creazione e l'aggiornamento di un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you purchase reservations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "name": "f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Compute/register/action",
        "Microsoft.SQL/register/action",
        "Microsoft.Consumption/register/action",
        "Microsoft.Capacity/catalogs/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Consumption/reservationRecommendations/read",
        "Microsoft.Support/supporttickets/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reservation Purchaser",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Collaboratore per i criteri delle risorse

Utenti con diritti di creazione/modifica di criteri delle risorse, creazione di ticket di supporto e lettura di risorse/gerarchia. [Scopri di più](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Creare e gestire assegnazioni di criteri |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Creare e gestire definizioni di criteri |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | Creare e gestire le esenzioni dei criteri |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Creare e gestire set di criteri |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Collaboratore al ripristino sito

Consente di gestire il servizio Site Recovery ad eccezione della creazione dell'insieme di credenziali e dell'assegnazione di [ruolo Altre informazioni](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp è un'operazione interna usata dal servizio |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali della risorsa o dell'insieme di credenziali. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Consente di creare e gestire informazioni estese relative all'insieme di credenziali |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Consente di creare e gestire le identità registrate |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | Consente di creare o aggiornare le impostazioni degli avvisi di replica |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Legge gli eventi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | Consente di creare e gestire le infrastrutture di replica |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Consente di creare e gestire i processi di replica |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | Consente di creare e gestire i criteri di replica |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | Consente di creare e gestire i piani di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Consente di creare e gestire la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Consente di leggere gli avvisi per l'insieme di credenziali dei servizi di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | Legge qualsiasi stato dell'operazione di replica dell'insieme di credenziali |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Operatore del ripristino sito

Consente di eseguire il failover e il failback, ma non di eseguire altre Site Recovery operazioni di [gestione. Altre informazioni](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp è un'operazione interna usata dal servizio |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Legge le impostazioni degli avvisi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Legge gli eventi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | Verifica la coerenza dell'infrastruttura |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Legge le infrastrutture |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | Riassocia gateway |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | Rinnova il certificato per l'infrastruttura |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Legge le reti |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Legge i mapping di rete |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Legge i contenitori di protezione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Legge gli elementi da proteggere |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Applica punto di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Commit del failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover pianificato |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Legge gli elementi protetti |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Legge i punti di ripristino di replica |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Ripristina replica |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Riprotegge l'elemento protetto |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Cambia il contenitore di protezione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover di test |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Pulizia del failover di test |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aggiorna servizio Mobility |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Legge i mapping dei contenitori di protezione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Legge i provider dei servizi di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Aggiorna i provider |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Legge le classificazioni di archiviazione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Legge i mapping delle classificazioni di archiviazione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Legge i vCenter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Consente di creare e gestire i processi di replica |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Legge i criteri |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | Piano di ripristino del commit del failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | Piano di ripristino del failover pianificato |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Legge i piani di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | Piano di ripristino di riprotezione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | Piano di ripristino del failover di test |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Piano di ripristino della pulizia del failover di test |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | Piano di ripristino del failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Consente di leggere gli avvisi per l'insieme di credenziali dei servizi di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Reader di ripristino sito

Consente di visualizzare lo stato Site Recovery ma non di eseguire altre operazioni di gestione [Altre informazioni](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Legge le impostazioni degli avvisi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Legge gli eventi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Legge le infrastrutture |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Legge le reti |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Legge i mapping di rete |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Legge i contenitori di protezione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Legge gli elementi da proteggere |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Legge gli elementi protetti |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Legge i punti di ripristino di replica |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Legge i mapping dei contenitori di protezione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Legge i provider dei servizi di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Legge le classificazioni di archiviazione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Legge i mapping delle classificazioni di archiviazione |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Legge i vCenter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | Legge i processi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Legge i criteri |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Legge i piani di ripristino |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Collaboratore alla richiesta di supporto

Consente di creare e gestire le richieste di supporto [Altre informazioni](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Collaboratore per tag

Consente di gestire i tag sulle entità senza concedere l'accesso alle entità stesse. [Scopri di più](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Ottiene le risorse del gruppo di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | Ottiene le risorse di una sottoscrizione. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Altri


### <a name="azure-digital-twins-data-owner"></a>Gemelli digitali di Azure proprietario dei dati

Ruolo di accesso completo per il piano dati di Gemelli digitali [Altre informazioni](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Legge, elimina, crea o aggiorna qualsiasi route di eventi |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Legge, crea, aggiorna o elimina qualsiasi gemello digitale |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | Richiamare qualsiasi comando in un gemello digitale |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | Legge, crea, aggiorna o elimina qualsiasi relazione di Gemelli digitali |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Legge, crea, aggiorna o elimina qualsiasi modello |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | Eseguire query su qualsiasi grafo di Gemelli digitali |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Gemelli digitali di Azure lettore dati

Ruolo di sola lettura per le proprietà del piano dati di Gemelli digitali [Altre informazioni](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/read | Legge tutti i gemelli digitali |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/read | Legge tutte le relazioni tra gemelli digitali |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/read | Legge qualsiasi route di eventi |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/read | Legge qualsiasi modello |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/action | Eseguire query su qualsiasi grafo di gemelli digitali |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>Collaboratore BizTalk

Consente di gestire i servizi BizTalk, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.BizTalkServices/BizTalk/* | È in grado di creare e gestire i servizi BizTalk |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-contributor"></a>Collaboratore del gruppo di applicazioni desktop virtualization

Collaboratore del gruppo di applicazioni Desktop Virtualization. [Scopri di più](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Leggere i pool di host |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Lettura di pool di host/host di sessione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86240b0e-9422-4c43-887b-b61143f32ba8",
  "name": "86240b0e-9422-4c43-887b-b61143f32ba8",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-reader"></a>Lettore del gruppo di applicazioni desktop virtualization

Lettore del gruppo di applicazioni di virtualizzazione desktop. [Scopri di più](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Leggere i gruppi di applicazioni |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Leggere i pool di host |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Read hostpools/sessionhosts |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Ottiene o elenca le distribuzioni. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Legge un avviso della metrica (versione classica) |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "name": "aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-contributor"></a>Collaboratore Virtualizzazione desktop

Collaboratore di Virtualizzazione desktop. [Scopri di più](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/082f0a83-3be5-4ba1-904c-961cca79b387",
  "name": "082f0a83-3be5-4ba1-904c-961cca79b387",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-contributor"></a>Collaboratore al pool di host di virtualizzazione desktop

Collaboratore del pool di host di virtualizzazione desktop. [Scopri di più](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "name": "e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-reader"></a>Lettore pool di host di virtualizzazione desktop

Lettore del pool di host di virtualizzazione desktop. [Scopri di più](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Leggere i pool di host |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Ottiene o elenca le distribuzioni. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Legge un avviso della metrica (versione classica) |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ceadfde2-b300-400a-ab7b-6143895aa822",
  "name": "ceadfde2-b300-400a-ab7b-6143895aa822",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-reader"></a>Lettore di virtualizzazione desktop

Lettore di Virtualizzazione desktop. [Scopri di più](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Ottiene o elenca le distribuzioni. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Legge un avviso della metrica (versione classica) |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49a72310-ab8d-41df-bbb0-79b649203868",
  "name": "49a72310-ab8d-41df-bbb0-79b649203868",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-session-host-operator"></a>Operatore host sessione di virtualizzazione desktop

Operatore dell'host sessione di virtualizzazione desktop. [Scopri di più](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Leggere i pool di host |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Session Host.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "name": "2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Session Host Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Utente di Desktop Virtualization

Consente all'utente di usare le applicazioni in un gruppo di applicazioni. [Scopri di più](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/action | Usare ApplicationGroup |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user-session-operator"></a>Operatore sessione utente di Desktop Virtualization

Operatore della sessione Uesr di Desktop Virtualization. [Scopri di più](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Leggere i pool di host |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Leggere pool di host/host di sessione |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Uesr Session.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "name": "ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User Session Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-contributor"></a>Collaboratore dell'area di lavoro Virtualizzazione desktop

Collaboratore dell'area di lavoro Desktop Virtualization. [Scopri di più](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Leggere i gruppi di applicazioni |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "name": "21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/*",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-reader"></a>Lettore dell'area di lavoro di virtualizzazione desktop

Lettore dell'area di lavoro Virtualizzazione desktop. [Scopri di più](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/read | Leggere le aree di lavoro |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Leggere i gruppi di applicazioni |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Ottiene o elenca le distribuzioni. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Legge un avviso della metrica (versione classica) |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "name": "0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-backup-reader"></a>Lettore backup su disco

Fornisce l'autorizzazione per il backup dell'insieme di credenziali per eseguire il backup su disco. [Scopri di più](../backup/disk-backup-faq.yml)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Ottiene le proprietà di un disco |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | Ottiene gli URI SAS del disco per l’accesso al BLOB |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk backup.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "name": "3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/beginGetAccess/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-restore-operator"></a>Operatore di ripristino del disco

Fornisce l'autorizzazione per il backup dell'insieme di credenziali per eseguire il ripristino del disco. [Scopri di più](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Crea un nuovo disco o ne aggiorna uno esistente |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Ottiene le proprietà di un disco |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk restore.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b50d9833-a0cb-478e-945f-707fcc997c13",
  "name": "b50d9833-a0cb-478e-945f-707fcc997c13",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Restore Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-snapshot-contributor"></a>Collaboratore snapshot disco

Fornisce l'autorizzazione per eseguire il backup dell'insieme di credenziali per gestire gli snapshot del disco. [Scopri di più](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/delete | Elimina una snapshot |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/write | Crea una nuova snapshot o ne aggiorna una esistente |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/read | Ottiene le proprietà di una snapshot |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/beginGetAccess/action | Ottiene l'URI SAS dello snapshot per l'accesso al BLOB |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/endGetAccess/action | Revoca l'URI SAS dello snapshot |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | Ottiene gli URI SAS del disco per l’accesso al BLOB |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/write | Crea un account di archiviazione con i parametri specificati o aggiorna le proprietà o i tag o aggiunge un dominio personalizzato per l’account di archiviazione specificato. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/delete | Elimina un account di archiviazione esistente. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to manage disk snapshots.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "name": "7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/snapshots/delete",
        "Microsoft.Compute/snapshots/write",
        "Microsoft.Compute/snapshots/read",
        "Microsoft.Compute/snapshots/beginGetAccess/action",
        "Microsoft.Compute/snapshots/endGetAccess/action",
        "Microsoft.Compute/disks/beginGetAccess/action",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/write",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Snapshot Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Collaboratore raccolte di processi dell'unità di pianificazione

Consente di gestire le raccolte di processi dell'utilità di pianificazione, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Creare e gestire raccolte di processi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="services-hub-operator"></a>Operatore hub servizi

Operatore hub servizi consente di eseguire tutte le operazioni di lettura, scrittura ed eliminazione correlate ai connettori dell'hub dei servizi. [Scopri di più](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Creare e gestire una distribuzione |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/write | Creare o aggiornare un connettore dell'hub dei servizi |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/read | Visualizzare o elencare i connettori dell'hub dei servizi |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/delete | Eliminare connettori dell'hub di servizi |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/checkAssessmentEntitlement/action | Elenca i diritti di valutazione per una determinata area di lavoro dell'hub dei servizi |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/read | Visualizzare gli entitlement dell'offerta di supporto per una determinata area di lavoro dell'hub dei servizi |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/workspaces/read | Elencare le aree di lavoro dell'hub dei servizi per un determinato utente |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Services Hub Operator allows you to perform all read, write, and deletion operations related to Services Hub Connectors.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/82200a5b-e217-47a5-b665-6d8765ee745b",
  "name": "82200a5b-e217-47a5-b665-6d8765ee745b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.ServicesHub/connectors/write",
        "Microsoft.ServicesHub/connectors/read",
        "Microsoft.ServicesHub/connectors/delete",
        "Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action",
        "Microsoft.ServicesHub/supportOfferingEntitlement/read",
        "Microsoft.ServicesHub/workspaces/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Services Hub Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure usando il portale di Azure](role-assignments-portal.md)
- [Ruoli personalizzati di Azure](custom-roles.md)
- [Autorizzazioni nel Centro sicurezza di Azure](../security-center/security-center-permissions.md)
