---
title: Distribuire l'esempio di progetto Azure Security benchmark Foundation
description: Procedura di distribuzione per l'esempio di progetto di benchmark Foundation di Azure, inclusi i dettagli del parametro dell'artefatto Blueprint.
ms.date: 02/18/2020
ms.topic: sample
ms.openlocfilehash: e48f3da383bdb6d5c9960595f3c0fdcabc27dc75
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740682"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Distribuire l'esempio di progetto Azure Security benchmark Foundation

Per distribuire l'esempio di progetto Azure Security benchmark Foundation, è necessario eseguire i passaggi seguenti:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio di progetto di **Azure Security benchmark Foundation** in _altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto Azure Security benchmark Foundation.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma tale modifica potrebbe allontanarsi dal progetto di Azure Security benchmark Foundation.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. Fornire **Note sulle modifiche** , ad esempio "prima versione pubblicata dall'esempio di progetto di Azure Security benchmark Foundation". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

## <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base
       - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ognuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome del progetto.
       Cambiarlo, se necessario, o lasciarlo inalterato.
     - **Località**: selezionare un'area in cui creare l'identità gestita.
     - Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato.
       Per altre informazioni, vedere [Managed identities for Azure resources](../../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Scegliere una versione **pubblicata** della copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare l'impostazione di blocco del progetto per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Scegliere l'opzione predefinita _Assegnata dal sistema_ per l'identità gestita oppure l'opzione _Assegnata dall'utente_ per l'identità.

   - Parametri di progetto

     I parametri definiti in questa sezione vengono usati da molti artefatti nella definizione del progetto per assicurare coerenza.
    
     - **Prefisso per le risorse e i gruppi di risorse**: questa stringa viene usata come prefisso per tutti i nomi di risorse e gruppi di risorse
     - **Nome Hub**: nome dell'hub
     - **Conservazione log (giorni)**: numero di giorni per cui vengono conservati i log. Se si immette ' 0', i log vengono conservati per un periodo illimitato
     - **Deploy Hub**: immettere ' true ' o ' false ' per specificare se l'assegnazione distribuisce i componenti dell'hub dell'architettura
     - **Posizione Hub**: percorso del gruppo di risorse Hub
     - **Indirizzi IP di destinazione**: indirizzi IP di destinazione per la connettività in uscita; elenco delimitato da virgole di indirizzi IP o prefissi di intervallo IP
     - **Nome Network Watcher**: nome della risorsa Network Watcher
     - **Network Watcher nome del gruppo di risorse**: nome del gruppo di risorse Network Watcher
     - **Abilita protezione DDoS**: immettere ' true ' o ' false ' per specificare se la protezione DDoS è abilitata nella rete virtuale
     
    > [!NOTE] 
    > Se Network Watcher è già abilitato, è consigliabile usare il gruppo di risorse Network Watcher esistente. È anche necessario specificare il percorso per il gruppo di risorse Network Watcher esistente per il parametro dell'artefatto **Network Watcher percorso del gruppo di risorse**.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

## <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente fornisce un elenco dei parametri del progetto:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|Descrizione|
|-|-|-|-|
|Gruppo di risorse Hub|Resource group|Nome del gruppo di risorse|Locked: concatena il prefisso con il nome dell'hub|
|Gruppo di risorse Hub|Resource group|Località del gruppo di risorse|Bloccato: usa la posizione dell'hub|
|Modello Firewall di Azure|Modello di Resource Manager|Indirizzo IP privato del firewall di Azure||
|Modello di diagnostica e Log Analytics di Azure|Modello di Resource Manager|Percorso dell'area di lavoro Log Analytics|Percorso in cui viene creata l'area di lavoro Log Analytics; eseguire `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` in Azure PowersShell per visualizzare le aree disponibili|
|Modello di diagnostica e Log Analytics di Azure|Modello di Resource Manager|ID dell'account di automazione di Azure (facoltativo)|ID risorsa dell'account di automazione. usato per creare un servizio collegato tra Log Analytics e un account di automazione|
|Modello del gruppo di sicurezza di rete di Azure|Modello di Resource Manager|Abilitare i log di flusso NSG|Immettere ' true ' o ' false ' per abilitare o disabilitare i log di flusso NSG|
|Modello di hub rete virtuale di Azure|Modello di Resource Manager|Prefisso indirizzo rete virtuale|Prefisso dell'indirizzo di rete virtuale per la rete virtuale Hub|
|Modello di hub rete virtuale di Azure|Modello di Resource Manager|Prefisso dell'indirizzo della subnet del firewall|Prefisso dell'indirizzo della subnet del firewall per la rete virtuale Hub|
|Modello di hub rete virtuale di Azure|Modello di Resource Manager|Prefisso dell'indirizzo della subnet Bastion|Prefisso dell'indirizzo della subnet Bastion per la rete virtuale Hub|
|Modello di hub rete virtuale di Azure|Modello di Resource Manager|Prefisso dell'indirizzo della subnet del gateway|Prefisso dell'indirizzo della subnet del gateway per la rete virtuale Hub|
|Modello di hub rete virtuale di Azure|Modello di Resource Manager|Prefisso dell'indirizzo della subnet di gestione|Prefisso dell'indirizzo della subnet di gestione per la rete virtuale Hub|
|Modello di hub rete virtuale di Azure|Modello di Resource Manager|Prefisso dell'indirizzo della subnet della casella di salto|Prefisso dell'indirizzo della subnet della casella di salto per la rete virtuale Hub|
|Modello di hub rete virtuale di Azure|Modello di Resource Manager|Nomi degli indirizzi subnet (facoltativo)|Matrice di nomi di subnet da distribuire nella rete virtuale dell'hub; ad esempio, "Subnet1", "Subnet2"|
|Modello di hub rete virtuale di Azure|Modello di Resource Manager|Prefissi di indirizzo della subnet (facoltativo)|Matrice di prefissi di indirizzo IP per subnet facoltative per la rete virtuale dell'hub; ad esempio, "10.0.7.0/24", "10.0.8.0/24"|
|Gruppo di risorse spoke|Resource group|Nome del gruppo di risorse|Bloccato: concatena il prefisso con il nome spoke|
|Gruppo di risorse spoke|Resource group|Località del gruppo di risorse|Bloccato: usa la posizione dell'hub|
|Modello spoke della rete virtuale di Azure|Modello di Resource Manager|Distribuzione spoke|Immettere ' true ' o ' false ' per specificare se l'assegnazione distribuisce i componenti spoke dell'architettura|
|Modello spoke della rete virtuale di Azure|Modello di Resource Manager|ID sottoscrizione Hub|ID sottoscrizione in cui è distribuito l'hub; il valore predefinito è la sottoscrizione in cui si trova la definizione del progetto|
|Modello spoke della rete virtuale di Azure|Modello di Resource Manager|Nome spoke|Nome della spoke|
|Modello spoke della rete virtuale di Azure|Modello di Resource Manager|Prefisso dell'indirizzo della rete virtuale|Prefisso dell'indirizzo di rete virtuale per la rete virtuale spoke|
|Modello spoke della rete virtuale di Azure|Modello di Resource Manager|Prefisso indirizzo subnet|Prefisso dell'indirizzo della subnet per la rete virtuale spoke|
|Modello spoke della rete virtuale di Azure|Modello di Resource Manager|Nomi degli indirizzi subnet (facoltativo)|Matrice di nomi di subnet da distribuire nella rete virtuale spoke; ad esempio, "Subnet1", "Subnet2"|
|Modello spoke della rete virtuale di Azure|Modello di Resource Manager|Prefissi di indirizzo della subnet (facoltativo)|Matrice di prefissi di indirizzo IP per subnet facoltative per la rete virtuale spoke; ad esempio, "10.0.7.0/24", "10.0.8.0/24"|
|Modello spoke della rete virtuale di Azure|Modello di Resource Manager|Distribuzione spoke|Immettere ' true ' o ' false ' per specificare se l'assegnazione distribuisce i componenti spoke dell'architettura|
|Modello di Network Watcher di Azure|Modello di Resource Manager|Località Network Watcher|Percorso per la risorsa Network Watcher|
|Modello di Network Watcher di Azure|Modello di Resource Manager|Località del gruppo di risorse Network Watcher|Se Network Watcher è già abilitato, il valore di questo parametro **deve** corrispondere al percorso del gruppo di risorse Network Watcher esistente.|

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica l'errore `The resource group 'NetworkWatcherRG' failed to deploy due to the
following error: Invalid resource group location '{location}'. The Resource group already exists in
location '{location}'.` , verificare che il parametro blueprint **Network Watcher nome del gruppo di risorse** specifichi il nome del gruppo di risorse Network Watcher esistente e che il parametro dell'artefatto **Network Watcher percorso del gruppo di risorse** specifichi il percorso del gruppo di risorse Network Watcher esistente.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati esaminati i passaggi per distribuire l'esempio di progetto Azure Security benchmark Foundation, vedere l'articolo seguente per informazioni sull'architettura:

> [!div class="nextstepaction"]
> [Progetto di Azure Security benchmark Foundation-Panoramica](./index.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
