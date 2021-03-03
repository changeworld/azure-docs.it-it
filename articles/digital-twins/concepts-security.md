---
title: Sicurezza per le soluzioni di Gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Informazioni sulle procedure di sicurezza consigliate con i dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 81a44d4d0025c841cf56e19d6afee5e95bd44a55
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730508"
---
# <a name="secure-azure-digital-twins"></a>Proteggere i dispositivi gemelli digitali di Azure

Per la sicurezza, i dispositivi gemelli digitali di Azure consentono un controllo di accesso preciso su dati, risorse e azioni specifici nella distribuzione. Questa operazione viene eseguita tramite un ruolo granulare e una strategia di gestione delle autorizzazioni denominata **controllo degli accessi in base al ruolo di Azure (RBAC di Azure)**. [Qui](../role-based-access-control/overview.md)è possibile leggere i principi generali di controllo degli accessi in base al ruolo di Azure.

I dispositivi gemelli digitali di Azure supportano anche la crittografia dei dati inattivi.

## <a name="roles-and-permissions-with-azure-rbac"></a>Ruoli e autorizzazioni con RBAC di Azure

Il controllo degli accessi in base al ruolo di Azure viene fornito ai gemelli digitali di Azure tramite l'integrazione con Azure AD [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)

È possibile usare il controllo degli accessi in base al ruolo di Azure per concedere le autorizzazioni a un' *entità di sicurezza*, che può essere un utente, un gruppo o un'entità servizio dell'applicazione. L'entità di sicurezza viene autenticata da Azure AD e riceve un token OAuth 2,0 in return. Questo token può essere usato per autorizzare una richiesta di accesso a un'istanza di Azure Digital gemelli.

### <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Con Azure AD, l'accesso è un processo in due passaggi. Quando un'entità di sicurezza (un utente, un gruppo o un'applicazione) tenta di accedere ai dispositivi gemelli digitali di Azure, è necessario *autenticare* e *autorizzare* la richiesta. 

1. In primo luogo, l'identità dell'entità di sicurezza viene *autenticata* e viene restituito un token OAuth 2,0.
2. Successivamente, il token viene passato come parte di una richiesta al servizio Azure Digital Twins, per *autorizzare* l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che qualsiasi richiesta dell'applicazione contenga un token di accesso OAuth 2,0 in fase di esecuzione. Se un'applicazione è in esecuzione all'interno di un'entità di Azure, ad esempio un'app [funzioni di Azure](../azure-functions/functions-overview.md) , può usare un' **identità gestita** per accedere alle risorse. Per altre informazioni sulle identità gestite, vedere la sezione successiva.

Per il passaggio di autorizzazione è necessario assegnare un ruolo di Azure all'entità di sicurezza. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni che saranno disponibili nell'entità. I dispositivi gemelli digitali di Azure forniscono i ruoli di Azure che includono i set di autorizzazioni per le risorse di Azure Digital Twins. Questi ruoli sono descritti più avanti in questo articolo.

Per altre informazioni sui ruoli e sulle assegnazioni di ruolo supportati in Azure, vedere informazioni sui [*diversi ruoli*](../role-based-access-control/rbac-and-directory-admin-roles.md) nella documentazione relativa a RBAC di Azure.

#### <a name="authentication-with-managed-identities"></a>Autenticazione con identità gestite

[Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) è una funzionalità di Azure che consente di creare un'identità sicura associata alla distribuzione in cui viene eseguito il codice dell'applicazione. È quindi possibile associare tale identità ai ruoli di controllo di accesso per concedere autorizzazioni personalizzate per l'accesso a risorse di Azure specifiche necessarie per l'applicazione.

Con le identità gestite, la piattaforma Azure gestisce questa identità di runtime. Non è necessario archiviare e proteggere le chiavi di accesso nel codice o nella configurazione dell'applicazione, né per l'identità stessa, né per le risorse a cui è necessario accedere. Un'app client dei dispositivi gemelli digitali di Azure in esecuzione all'interno di un'applicazione di servizio app Azure non deve gestire le regole e le chiavi di firma di accesso condiviso o altri token di accesso. L'app client necessita solo dell'indirizzo endpoint dello spazio dei nomi di Azure Digital gemelli. Quando l'app si connette, i dispositivi gemelli digitali di Azure associano il contesto dell'entità gestita al client. Una volta associato a un'identità gestita, il client di Azure Digital Twins può eseguire tutte le operazioni autorizzate. L'autorizzazione verrà quindi concessa associando un'entità gestita a un ruolo Azure Digital Twins di Azure (descritto di seguito).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autorizzazione: ruoli di Azure per i dispositivi gemelli digitali di Azure

Azure fornisce **due ruoli predefiniti di Azure** per autorizzare l'accesso alle [API del piano dati](how-to-use-apis-sdks.md#overview-data-plane-apis)di Azure Digital gemelli. È possibile fare riferimento ai ruoli in base al nome o all'ID:

| Ruolo predefinito | Descrizione | ID | 
| --- | --- | --- |
| Proprietario di dati dei dispositivi gemelli digitali di Azure | Consente l'accesso completo alle risorse dei dispositivi gemelli digitali di Azure | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Lettore dati di dispositivi gemelli digitali di Azure | Consente l'accesso in sola lettura alle risorse di Azure Digital Twins | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

È possibile assegnare i ruoli in due modi:
* tramite il riquadro controllo di accesso (IAM) per i dispositivi gemelli digitali di Azure nella portale di Azure (vedere [*assegnare i ruoli di Azure usando il portale di Azure*](../role-based-access-control/role-assignments-portal.md))
* tramite i comandi dell'interfaccia della riga di comando per aggiungere o rimuovere un ruolo

Per istruzioni più dettagliate su come eseguire questa operazione, vedere l'esercitazione sui dispositivi gemelli di Azure per la [*connessione di una soluzione end-to-end*](tutorial-end-to-end.md).

Per altre informazioni sul modo in cui vengono definiti i ruoli predefiniti, vedere informazioni sulle [*definizioni di ruolo*](../role-based-access-control/role-definitions.md) nella documentazione relativa a RBAC di Azure. Per informazioni sulla creazione di ruoli personalizzati di Azure, vedere [*ruoli personalizzati di Azure*](../role-based-access-control/custom-roles.md).

##### <a name="automating-roles"></a>Automazione dei ruoli

Quando si fa riferimento ai ruoli negli scenari automatici, è consigliabile fare riferimento a essi in base ai relativi **ID** anziché ai rispettivi nomi. I nomi possono variare tra le versioni, ma non gli ID, rendendoli un riferimento più stabile in automazione.

> [!TIP]
> Se si assiging ruoli con un cmdlet, ad esempio `New-AzRoleAssignment` ([riferimento](/powershell/module/az.resources/new-azroleassignment)), è possibile usare il `-RoleDefinitionId` parametro anziché `-RoleDefinitionName` per passare un ID anziché un nome per il ruolo.

### <a name="permission-scopes"></a>Ambiti delle autorizzazioni

Prima di assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che dovrà avere. Le procedure consigliate stabiliscono che è preferibile concedere solo l'ambito più ristretto possibile.

L'elenco seguente descrive i livelli in cui è possibile definire l'ambito di accesso alle risorse di Azure Digital gemelli.
* Modelli: le azioni per questa risorsa determinano il controllo sui [modelli](concepts-models.md) caricati nei dispositivi gemelli digitali di Azure.
* Grafico gemelli digitali delle query: le azioni per questa risorsa determinano la possibilità di eseguire [operazioni di query](concepts-query-language.md) sui gemelli digitali all'interno del grafo dei gemelli digitali di Azure.
* Digital Twin: le azioni per questa risorsa forniscono il controllo sulle operazioni CRUD sui [gemelli digitali](concepts-twins-graph.md) nel grafico gemello.
* Relazione digitale gemello: le azioni per questa risorsa definiscono il controllo sulle operazioni CRUD sulle [relazioni](concepts-twins-graph.md) tra i dispositivi gemelli digitali nel grafico gemello.
* Route dell'evento: le azioni per questa risorsa determinano le autorizzazioni per [indirizzare gli eventi](concepts-route-events.md) da dispositivi gemelli digitali di Azure a un servizio endpoint, ad esempio [Hub eventi](../event-hubs/event-hubs-about.md), [griglia di eventi](../event-grid/overview.md)o [bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Risoluzione dei problemi relativi alle autorizzazioni

Se un utente tenta di eseguire un'azione non consentita dal ruolo, può ricevere un errore dalla lettura della richiesta di servizio `403 (Forbidden)` . Per ulteriori informazioni e procedure per la risoluzione dei problemi, vedere [*risoluzione dei problemi: richiesta di Azure Digital Twins non riuscita con stato: 403 (accesso negato)*](troubleshoot-error-403.md).

## <a name="managed-identity-for-accessing-other-resources-preview"></a>Identità gestita per l'accesso ad altre risorse (anteprima)

La configurazione di un' **identità gestita** di [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) per un'istanza di Azure Digital gemelli può consentire all'istanza di accedere facilmente ad altre risorse protette da Azure AD, ad esempio [Azure Key Vault](../key-vault/general/overview.md). L'identità viene gestita dalla piattaforma Azure e non richiede il provisioning o la rotazione dei segreti. Per altre informazioni sulle identità gestite in Azure AD, vedere  [*identità gestite per le risorse di Azure*](../active-directory/managed-identities-azure-resources/overview.md). 

Azure supporta due tipi di identità gestite: assegnato dal sistema e assegnato dall'utente. Attualmente, i dispositivi gemelli digitali di Azure supportano solo le **identità assegnate dal sistema**. 

Per eseguire l'autenticazione in un [endpoint personalizzato](concepts-route-events.md#create-an-endpoint), è possibile usare un'identità gestita assegnata dal sistema per l'istanza digitale di Azure. I dispositivi gemelli digitali di Azure supportano l'autenticazione basata sulle identità assegnata dal sistema agli endpoint per le destinazioni dell' [Hub eventi](../event-hubs/event-hubs-about.md) e del [bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)   e a un endpoint del [contenitore di archiviazione di Azure](../storage/blobs/storage-blobs-introduction.md)   per [gli eventi non recapitabili](concepts-route-events.md#dead-letter-events). [Griglia](../event-grid/overview.md)   di eventi gli endpoint non sono attualmente supportati per le identità gestite.

Per istruzioni su come abilitare un'identità gestita dal sistema per i dispositivi gemelli digitali di Azure e usarla per indirizzare gli eventi, vedere [*procedura: abilitare un'identità gestita per il routing degli eventi (anteprima)*](./how-to-enable-managed-identities-portal.md).

## <a name="private-network-access-with-azure-private-link-preview"></a>Accesso alla rete privata con collegamento privato di Azure (anteprima)

Il [collegamento privato di Azure](../private-link/private-link-overview.md) è un servizio che consente di accedere alle risorse di Azure (ad esempio [Hub eventi di Azure](../event-hubs/event-hubs-about.md), archiviazione di [Azure](../storage/common/storage-introduction.md)e [Azure Cosmos DB](../cosmos-db/introduction.md)) e ai servizi clienti e partner ospitati da Azure tramite un endpoint privato nella [rete virtuale di Azure (VNet)](../virtual-network/virtual-networks-overview.md). 

Analogamente, è possibile usare endpoint privati per l'istanza di Azure Digital Twin per consentire ai client che si trovano nella rete virtuale di accedere in modo sicuro all'istanza tramite un collegamento privato. 

L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della VNet di Azure. Il traffico di rete tra un client nella rete privata e l'istanza di Azure Digital Twins attraversa la VNet e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione alla rete Internet pubblica. Ecco una rappresentazione visiva del sistema:

:::image type="content" source="media/concepts-security/private-link.png" alt-text="Un diagramma che mostra una rete per una società PowerGrid che è un VNET protetto senza accesso al cloud Internet/pubblico, che si connette tramite un collegamento privato a un'istanza di dispositivi gemelli digitali di Azure denominata CityOfTwins.":::

La configurazione di un endpoint privato per l'istanza di Azure Digital Twins consente di proteggere l'istanza di Azure Digital Twins ed eliminare l'esposizione pubblica, nonché di evitare i dati exfiltration dalla VNet.

Per istruzioni su come configurare il collegamento privato per i dispositivi gemelli digitali di Azure, vedere [*procedura: abilitare l'accesso privato con collegamento privato (anteprima)*](./how-to-enable-private-link-portal.md).

### <a name="design-considerations"></a>Considerazioni sulla progettazione 

Quando si lavora con un collegamento privato per i dispositivi gemelli digitali di Azure, di seguito sono riportati alcuni fattori che è opportuno considerare:
* **Prezzi**: per informazioni dettagliate sui prezzi, vedere  [prezzi dei collegamenti privati di Azure](https://azure.microsoft.com/pricing/details/private-link). 
* **Disponibilità a livello** di area: per i dispositivi gemelli digitali di Azure, questa funzionalità è disponibile in tutte le aree di Azure in cui è disponibile Azure Digital gemelli. 
* **Numero massimo di endpoint privati per istanza di Azure Digital Twins**: 10

Per informazioni sui limiti del collegamento privato, vedere la [documentazione del collegamento privato di Azure: limitazioni](../private-link/private-link-service-overview.md#limitations).

## <a name="service-tags"></a>Tag di servizio

Un **tag di servizio** rappresenta un gruppo di prefissi di indirizzi IP da un determinato servizio di Azure. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag in base alla modifica degli indirizzi, riducendo la complessità degli aggiornamenti frequenti alle regole di sicurezza di rete. Per ulteriori informazioni sui tag di servizio, vedere la pagina relativa ai  [*tag della rete virtuale*](../virtual-network/service-tags-overview.md). 

È possibile usare i tag di servizio per definire i controlli di accesso alla rete nei [gruppi di sicurezza](../virtual-network/network-security-groups-overview.md#security-rules)   di rete o nel [firewall di Azure](../firewall/service-tags.md), usando i tag del servizio al posto di indirizzi IP specifici quando si creano le regole di sicurezza. Specificando il nome del tag di servizio (in questo caso, **AzureDigitalTwins**) nel **   campo di origine o *destinazione* appropriato   di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. 

Di seguito sono riportati i dettagli del tag del servizio **AzureDigitalTwins** .

| Tag | Scopo | È possibile usarlo in ingresso o in uscita? | Può essere regionale? | È possibile usarlo con Firewall di Azure? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Gemelli digitali di Azure<br>Nota: questo tag o gli indirizzi IP trattati da questo tag possono essere usati per limitare l'accesso agli endpoint configurati per le [route degli eventi](concepts-route-events.md). | In ingresso | No | Sì |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Uso dei tag del servizio per l'accesso agli endpoint della route di eventi 

Ecco i passaggi per accedere agli endpoint della [route degli eventi](concepts-route-events.md) usando i tag di servizio con i dispositivi gemelli digitali di Azure.

1. Per prima cosa, scaricare questo riferimento al file JSON che Mostra gli intervalli IP e i tag di servizio di Azure: [*intervalli IP di Azure e tag del servizio*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. Cercare gli intervalli IP "AzureDigitalTwins" nel file JSON.  

3. Per informazioni su come impostare i filtri IP per tale risorsa, vedere la documentazione della risorsa esterna connessa all'endpoint, ad esempio [griglia di eventi](../event-grid/overview.md), [Hub eventi](../event-hubs/event-hubs-about.md), [bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)o archiviazione di [Azure](../storage/blobs/storage-blobs-overview.md) per gli eventi di messaggi non [recapitabili](concepts-route-events.md#dead-letter-events).

4. Impostare i filtri IP sulle risorse esterne usando gli intervalli IP del *passaggio 2*.  

5. Aggiornare periodicamente gli intervalli IP come richiesto. È possibile che gli intervalli cambino nel tempo, quindi è consigliabile verificarli regolarmente e aggiornarli quando necessario. La frequenza di questi aggiornamenti può variare, ma è consigliabile controllarli una volta alla settimana.

## <a name="encryption-of-data-at-rest"></a>Crittografia dei dati inattivi

I dispositivi gemelli digitali di Azure forniscono la crittografia dei dati inattivi e in transito come sono scritti nei data center e li decrittografa per l'accesso. Questa crittografia viene eseguita utilizzando una chiave di crittografia gestita da Microsoft.

## <a name="cross-origin-resource-sharing-cors"></a>Condivisione risorse tra le origini (CORS)

I dispositivi gemelli digitali di Azure attualmente non supportano la **condivisione di risorse tra le origini (CORS)**. Di conseguenza, se si chiama un'API REST da un'app browser, un'interfaccia di [gestione API (gestione API)](../api-management/api-management-key-concepts.md) o un connettore [Power Apps](/powerapps/powerapps-overview) , è possibile che venga visualizzato un errore del criterio.

Per correggere l'errore, è possibile eseguire una delle operazioni seguenti:
* Rimuovere l'intestazione CORS `Access-Control-Allow-Origin` dal messaggio. Questa intestazione indica se la risposta può essere condivisa. 
* In alternativa, creare un proxy CORS ed eseguire la richiesta dell'API REST di Azure Digital gemelli. 

## <a name="next-steps"></a>Passaggi successivi

* Vedere questi concetti in azione in [*procedura: configurare un'istanza di e l'autenticazione*](how-to-set-up-instance-portal.md)di.

* Vedere come interagire con questi concetti dal codice dell'applicazione client in [*procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md).

* Altre informazioni su [RBAC di Azure](../role-based-access-control/overview.md).