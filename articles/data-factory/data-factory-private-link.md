---
title: Collegamento privato di Azure per Azure Data Factory
description: Informazioni sul funzionamento del collegamento privato di Azure in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 6fb460c65d37723b03c1dfd4fba2c353c19455bd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944574"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Collegamento privato di Azure per Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Con il collegamento privato di Azure è possibile connettersi a varie distribuzioni di piattaforme distribuite come servizio (PaaS) in Azure tramite un endpoint privato. Un endpoint privato è un indirizzo IP privato all'interno di una rete virtuale e una subnet specifiche. Per un elenco delle distribuzioni PaaS che supportano la funzionalità di collegamento privato, vedere la [documentazione del collegamento privato](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Comunicazione sicura tra le reti dei clienti e Azure Data Factory 
È possibile configurare una rete virtuale di Azure come rappresentazione logica della rete nel cloud. Questa operazione offre i vantaggi seguenti:
* Proteggi le tue risorse di Azure da attacchi nelle reti pubbliche.
* Si consente alle reti e Data Factory di comunicare in modo sicuro tra loro. 

È anche possibile connettere una rete locale alla rete virtuale configurando una connessione VPN Internet Protocol Security (IPsec) (da sito a sito) o una connessione di Azure ExpressRoute (peering privato). 

È anche possibile installare un runtime di integrazione self-hosted in un computer locale o in una macchina virtuale nella rete virtuale. In questo modo è possibile:
* Eseguire attività di copia tra un archivio dati cloud e un archivio dati in una rete privata.
* Inviare le attività di trasformazione sulle risorse di calcolo in una rete locale o in una rete virtuale di Azure. 

Tra Azure Data Factory e la rete virtuale del cliente sono necessari diversi canali di comunicazione, come illustrato nella tabella seguente:

| Dominio | Porta | Descrizione |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Un piano di controllo, necessario per la creazione e il monitoraggio di Data Factory. |
| `*.{region}.datafactory.azure.net` | 443 | Richiesta dal runtime di integrazione self-hosted per connettersi al servizio Data Factory. |
| `*.servicebus.windows.net` | 443 | Richiesta dal runtime di integrazione self-hosted per la creazione interattiva. |
| `download.microsoft.com` | 443 | Richiesta dal runtime di integrazione self-hosted per il download degli aggiornamenti. |

Con il supporto del collegamento privato per Azure Data Factory, è possibile:
* Creare un endpoint privato nella rete virtuale.
* Abilitare la connessione privata a un'istanza di data factory specifica. 

Le comunicazioni al servizio Azure Data Factory passano attraverso un collegamento privato e forniscono una connettività privata sicura. Non è necessario configurare il dominio e la porta precedenti in una rete virtuale o nel firewall aziendale per fornire un modo più sicuro per proteggere le risorse.  

![Diagramma del collegamento privato per l'architettura Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

L'abilitazione del servizio di collegamento privato per ognuno dei canali di comunicazione precedenti offre le funzionalità seguenti:
- **Supportato**:
   - È possibile creare e monitorare i data factory nella rete virtuale, anche se si bloccano tutte le comunicazioni in uscita.
   - Le comunicazioni del comando tra il runtime di integrazione self-hosted e il servizio Azure Data Factory possono essere eseguite in modo sicuro in un ambiente di rete privata. Il traffico tra il runtime di integrazione self-hosted e il servizio di Azure Data Factory passa attraverso il collegamento privato. 
- **Attualmente non supportata**:
   - La creazione interattiva che usa un runtime di integrazione self-hosted, ad esempio test connection, browse Folder List e Table list, Get schema e Preview data, passa attraverso un collegamento privato.
   - La nuova versione del runtime di integrazione self-hosted può essere scaricata automaticamente dall'area download Microsoft se si Abilita l'aggiornamento automatico.

   > [!NOTE]
   > Per le funzionalità che non sono attualmente supportate, è comunque necessario configurare il dominio e la porta indicati in precedenza nella rete virtuale o nel firewall aziendale. 

> [!WARNING]
> Quando si crea un servizio collegato, assicurarsi che le credenziali siano archiviate in Azure Key Vault. In caso contrario, le credenziali non funzioneranno quando si Abilita il collegamento privato in Azure Data Factory.

## <a name="dns-changes-for-private-endpoints"></a>Modifiche DNS per gli endpoint privati
Quando si crea un endpoint privato, il record di risorse DNS CNAME per il Data Factory viene aggiornato a un alias in un sottodominio con il prefisso ' privatelink '. Per impostazione predefinita, si crea anche una [zona DNS privata](../dns/private-dns-overview.md), che corrisponde al sottodominio "privatelink", con i record di risorse DNS a per gli endpoint privati.

Quando si risolve l'URL dell'endpoint data factory dall'esterno del VNet con l'endpoint privato, questo viene risolto nell'endpoint pubblico del servizio di data factory. Quando viene risolto da VNet che ospita l'endpoint privato, l'URL dell'endpoint di archiviazione viene risolto nell'indirizzo IP dell'endpoint privato.

Per l'esempio illustrato in precedenza, i record di risorse DNS per il Data Factory ' datafactorya ', in caso di risoluzione dall'esterno del VNet che ospita l'endpoint privato, saranno:

| Nome | Type | valore |
| ---------- | -------- | --------------- |
| Datafactorya. {Region}. DataFactory. Azure. NET | CNAME   | Datafactorya. {Region}. privatelink. DataFactory. Azure. NET |
| Datafactorya. {Region}. privatelink. DataFactory. Azure. NET | CNAME   | < endpoint pubblico del servizio data factory > |
| < endpoint pubblico del servizio data factory >  | A | Indirizzo IP pubblico del servizio < data factory > |

I record di risorse DNS per datafactorya, quando risolti in VNet che ospita l'endpoint privato, saranno:

| Nome | Type | valore |
| ---------- | -------- | --------------- |
| Datafactorya. {Region}. DataFactory. Azure. NET | CNAME   | Datafactorya. {Region}. privatelink. DataFactory. Azure. NET |
| Datafactorya. {Region}. privatelink. DataFactory. Azure. NET   | A | < indirizzo IP dell'endpoint privato > |

Se si usa un server DNS personalizzato nella rete, i client devono essere in grado di risolvere il nome di dominio completo per il Data Factory endpoint nell'indirizzo IP dell'endpoint privato. È necessario configurare il server DNS per delegare il sottodominio di collegamento privato alla zona DNS privata per la VNet o configurare i record A per ' datafactorya. {Region}. privatelink. DataFactory. Azure. NET ' con l'indirizzo IP dell'endpoint privato.

Per ulteriori informazioni sulla configurazione del server DNS per supportare endpoint privati, fare riferimento agli articoli seguenti:
- [Risoluzione dei nomi per le risorse in reti virtuali di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Configurazione DNS per endpoint privati](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Configurare il collegamento privato per Azure Data Factory
È possibile creare endpoint privati usando [il portale di Azure](../private-link/create-private-endpoint-portal.md).

È possibile scegliere se connettere il runtime di integrazione self-hosted per Azure Data Factory tramite endpoint pubblico o privato. 

![Screenshot del blocco dell'accesso pubblico del Integration Runtime self-hosted.](./media/data-factory-private-link/disable-public-access-shir.png)


È anche possibile passare al data factory di Azure nella portale di Azure e creare un endpoint privato, come illustrato di seguito:

![Screenshot del riquadro "connessioni a endpoint privati" per la creazione di un endpoint privato.](./media/data-factory-private-link/create-private-endpoint.png)

Nel passaggio della **risorsa** selezionare **Microsoft. DataFactory/factorys** come **tipo di risorsa**. Se si vuole creare un endpoint privato per le comunicazioni tra il runtime di integrazione self-hosted e il servizio Azure Data Factory, selezionare **DataFactory** come **risorsa secondaria di destinazione**.

![Screenshot del riquadro "connessioni a endpoint privati" per la selezione della risorsa.](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> La disabilitazione dell'accesso alla rete pubblica è applicabile solo al runtime di integrazione self-hosted, non a Azure Integration Runtime e SQL Server Integration Services (SSIS) Integration Runtime.

Se si vuole creare un endpoint privato per la creazione e il monitoraggio del data factory nella rete virtuale, selezionare **portale** come **risorsa secondaria di destinazione**.

> [!NOTE]
> È comunque possibile accedere al portale di Azure Data Factory tramite una rete pubblica dopo aver creato un endpoint privato per il portale.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una data factory usando l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md)
- [Introduzione al servizio Azure Data Factory](introduction.md)
- [Creazione di oggetti visivi in Azure Data Factory](author-visually.md)