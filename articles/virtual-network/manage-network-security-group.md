---
title: Creare, modificare o eliminare un gruppo di sicurezza di rete di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su dove trovare informazioni sulle regole di sicurezza e su come creare, modificare o eliminare un gruppo di sicurezza di rete.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 82f23c1fea29e2a88dd2a67ec9c89c7bf05bfff7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783504"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Creare, modificare o eliminare un gruppo di sicurezza di rete

Le regole di sicurezza dei gruppi di sicurezza di rete consentono di filtrare il tipo di traffico di rete consentito in ingresso e in uscita dalle subnet della rete virtuale e dalle interfacce di rete. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Panoramica dei gruppi di sicurezza di rete](./network-security-groups-overview.md). Completare quindi l'esercitazione [Filtrare il traffico di](tutorial-filter-network-traffic.md) rete per acquisire esperienza con i gruppi di sicurezza di rete.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se non se ne ha uno, configurare un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Completare una di queste attività prima di iniziare la parte restante di questo articolo:

- **Utenti del portale:** accedere al [portale](https://portal.azure.com) portale di Azure con l'account Azure.

- **Utenti di PowerShell:** eseguire i comandi nel [Azure Cloud Shell](https://shell.azure.com/powershell)oppure eseguire PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Nella scheda Azure Cloud Shell browser selezionare l'elenco a discesa **Selezionare** l'ambiente e quindi selezionare **PowerShell** se non è già selezionato.

    Se si esegue PowerShell in locale, usare Azure PowerShell versione 1.0.0 o successiva. Eseguire `Get-Module -ListAvailable Az.Network` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Eseguire `Connect-AzAccount` per creare una connessione con Azure.

- **Utenti dell'interfaccia della riga di** comando di Azure: eseguire i comandi nel [Azure Cloud Shell](https://shell.azure.com/bash)o eseguire l'interfaccia della riga di comando dal computer. Usare l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva se si esegue l'interfaccia della riga di comando di Azure in locale. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Eseguire `az login` per creare una connessione con Azure.

L'account con cui si accede o con [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cui ci si connette [ad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure deve essere assegnato al ruolo Collaboratore Rete o a un ruolo personalizzato a cui sono assegnate le azioni appropriate elencate in [Autorizzazioni](#permissions).

## <a name="work-with-network-security-groups"></a>Usare i gruppi di sicurezza di rete

È possibile creare un gruppo, [visualizzare tutti i gruppi di sicurezza di rete](#view-all-network-security-groups), [visualizzare i dettagli di un gruppo](#view-details-of-a-network-security-group), [modificare](#change-a-network-security-group) ed [eliminare](#delete-a-network-security-group) un gruppo di sicurezza di rete. È anche possibile [associare o annullare l'associazione](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) di un gruppo di sicurezza di rete da un'interfaccia di rete o da una subnet.

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

È previsto un limite al numero di gruppi di sicurezza di rete che è possibile creare per ogni località e sottoscrizione di Azure. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Nel [menu del portale di Azure](https://portal.azure.com) o nella **home page** selezionare **Crea una risorsa**.

2. Selezionare **Rete** e quindi gruppo **di sicurezza di rete.**

3. Nella pagina **Crea gruppo di sicurezza di** rete, nella scheda **Informazioni** di base, impostare i valori per le impostazioni seguenti:

    | Impostazione | Azione |
    | --- | --- |
    | **Sottoscrizione** | Scegliere la propria sottoscrizione. |
    | **Gruppo di risorse** | Scegliere un gruppo di risorse esistente oppure selezionare **Crea nuovo** per creare un nuovo gruppo di risorse. |
    | **Nome** | Immettere una stringa di testo univoca all'interno di un gruppo di risorse. |
    | **Area** | Scegliere la località desiderata. |

4. Selezionare **Rivedi e crea**.

5. Dopo aver visualizzato il **messaggio Convalida superata,** selezionare **Crea.**

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Visualizzare tutti i gruppi di sicurezza di rete

Passare al [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza di rete. Cercare e selezionare Gruppi **di sicurezza di rete**. Viene visualizzato l'elenco dei gruppi di sicurezza di rete per la sottoscrizione.

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg list](/cli/azure/network/nsg#az_network_nsg_list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Visualizzare i dettagli di un gruppo di sicurezza di rete

1. Passare al [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza di rete. Cercare e selezionare Gruppi **di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete.

Nella barra dei menu del gruppo di sicurezza di rete, in Impostazioni **,** è possibile visualizzare le regole di sicurezza **in** **ingresso,** le regole di sicurezza in **uscita,** le interfacce di rete e **le subnet** a cui è associato il gruppo di sicurezza di rete.

In **Monitoraggio** è possibile abilitare o disabilitare **Le impostazioni di diagnostica**. In **Supporto e risoluzione dei problemi** è possibile visualizzare Regole di sicurezza **effettive.** Per altre informazioni, vedere Registrazione diagnostica per un gruppo [di sicurezza di rete](virtual-network-nsg-manage-log.md) e Diagnosticare un problema del filtro del traffico di rete di una macchina [virtuale.](diagnose-network-traffic-filter-problem.md)

Per altre informazioni sulle impostazioni comuni di Azure elencate, vedere gli articoli seguenti:

- [Log attività](../azure-monitor/essentials/platform-logs-overview.md)
- [Controllo di accesso (IAM)](../role-based-access-control/overview.md)
- [Tag](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Locks](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script di automazione](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Modificare un gruppo di sicurezza di rete

1. Passare al [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza di rete. Cercare e selezionare Gruppi **di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete da modificare.

Le modifiche più comuni sono l'aggiunta di una regola di [sicurezza,](#delete-a-security-rule)la rimozione di una regola e l'associazione o l'associazione di un gruppo di sicurezza di rete [a](#create-a-security-rule)o da una subnet o un'interfaccia [di rete.](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg update](/cli/azure/network/nsg#az_network_nsg_update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associare o annullare l'associazione di un gruppo di sicurezza di rete da una subnet o un'interfaccia di rete

Per associare o annullare l'associazione di un gruppo di sicurezza di rete da un'interfaccia di rete, vedere [Associare o annullare l'associazione di un gruppo di sicurezza di rete da un'interfaccia di rete](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Per associare o annullare l'associazione di un gruppo di sicurezza di rete da una subnet, vedere [Cambiare le impostazioni della subnet](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Eliminare un gruppo di sicurezza di rete

Se un gruppo di sicurezza di rete è associato a subnet o interfacce di rete, non può essere eliminato. Annullare l'associazione di un gruppo di sicurezza di rete da tutte le subnet e le interfacce di rete prima di tentare di eliminarlo.

1. Passare al [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza di rete. Cercare e selezionare Gruppi **di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete da eliminare.

3. Nella barra degli strumenti del gruppo di sicurezza di rete selezionare **Elimina.** Quindi, selezionare **Sì** nella finestra di dialogo di conferma.

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg delete](/cli/azure/network/nsg#az_network_nsg_delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Usare le regole di sicurezza

Un gruppo di sicurezza di rete contiene zero o più regole di sicurezza. È possibile creare una regola, [visualizzare tutte le regole di sicurezza](#view-all-security-rules), [visualizzare i dettagli di una regola](#view-details-of-a-security-rule), [modificare](#change-a-security-rule) ed [eliminare](#delete-a-security-rule) una regola di sicurezza.

### <a name="create-a-security-rule"></a>Creare una regola di sicurezza

È previsto un limite al numero di regole per ogni gruppo di sicurezza di rete che è possibile creare per ogni località e sottoscrizione di Azure. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Passare al [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza di rete. Cercare e selezionare Gruppi **di sicurezza di rete.**

2. Selezionare il nome del gruppo di sicurezza di rete a cui si vuole aggiungere una regola di sicurezza.

3. Nella barra dei menu del gruppo di sicurezza di rete scegliere **Regole di sicurezza in ingresso** o Regole di sicurezza in **uscita.**

    Sono elencate diverse regole esistenti, tra cui alcune che potrebbero non essere state aggiunte. Quando si crea un gruppo di sicurezza di rete, vengono create diverse regole di sicurezza predefinite. Per altre informazioni, vedere [Regole di sicurezza predefinite](./network-security-groups-overview.md#default-security-rules).  Non è possibile eliminare le regole di sicurezza predefinite, ma è possibile eseguirne l'override con regole con una priorità più alta.

4. <a name="security-rule-settings"></a>Selezionare **Aggiungi**. Selezionare o aggiungere valori per le impostazioni seguenti e quindi selezionare **OK:**

    | Impostazione | Valore | Dettagli |
    | ------- | ----- | ------- |
    | **Origine** | Uno dei valori possibili:<ul><li>**qualsiasi**</li><li>**Indirizzi IP**</li><li>**Tag di servizio** (regola di sicurezza in ingresso) o **VirtualNetwork** (regola di sicurezza in uscita)</li><li>**Gruppo di &nbsp; sicurezza delle &nbsp; applicazioni**</li></ul> | <p>Se si sceglie **Indirizzi IP**, è necessario specificare anche Indirizzi IP **di origine/Intervalli CIDR**.</p><p>Se si sceglie **Tag di servizio**, è anche possibile selezionare un tag del servizio di **origine**.</p><p>Se si sceglie **Gruppo di sicurezza delle applicazioni**, è necessario selezionare anche un gruppo di sicurezza delle applicazioni esistente. Se si sceglie Gruppo  **di sicurezza delle** applicazioni sia per Origine che per Destinazione **,** le interfacce di rete all'interno di entrambi i gruppi di sicurezza delle applicazioni devono essere nella stessa rete virtuale.</p> |
    | **Indirizzi IP di origine/intervalli CIDR** | Elenco delimitato da virgole di indirizzi IP e intervalli Classless Interdomain Routing (CIDR) | <p>Questa impostazione viene visualizzata se si imposta **Origine** su **Indirizzi IP**. È necessario specificare un singolo valore o un elenco delimitato da virgole di più valori. Un esempio di più valori è `10.0.0.0/16, 192.188.1.1` . È previsto un limite al numero di valori che è possibile specificare. Per altre informazioni, vedere [Limiti di Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Se l'indirizzo IP specificato viene assegnato a una macchina virtuale di Azure, specificarne l'indirizzo IP privato, non l'indirizzo IP pubblico. Azure elabora le regole di sicurezza dopo la conversione dell'indirizzo IP pubblico in un indirizzo IP privato per le regole di sicurezza in ingresso, ma prima di convertire un indirizzo IP privato in un indirizzo IP pubblico per le regole in uscita. Per altre informazioni sugli indirizzi IP pubblici e privati in Azure, vedere [Tipi di indirizzo IP](./public-ip-addresses.md).</p> |
    | **Tag del servizio di origine** | Un tag di servizio dall'elenco a discesa | Questa impostazione facoltativa viene visualizzata se si imposta **Origine** su **Tag di servizio** per una regola di sicurezza in ingresso. Un tag di servizio è un identificatore predefinito per una categoria di indirizzi IP. Per altre informazioni sui tag di servizio disponibili e sui relativi elementi, vedere [Tag di servizio](./network-security-groups-overview.md#service-tags). |
    | **Gruppo di sicurezza dell'applicazione di origine** | Un gruppo di sicurezza delle applicazioni esistente | Questa impostazione viene visualizzata se si imposta **Origine** su **Gruppo di sicurezza dell'applicazione.** Selezionare un gruppo di sicurezza delle applicazioni presente nella stessa area dell'interfaccia di rete. Come [creare un gruppo di sicurezza delle applicazioni](#create-an-application-security-group). |
    | **Intervalli di porte di origine** | Uno dei valori possibili:<ul><li>Una singola porta, ad esempio `80`</li><li>Intervallo di porte, ad esempio `1024-65535`</li><li>Elenco delimitato da virgole di singole porte e/o intervalli di porte, ad esempio `80, 1024-65535`</li><li>Asterisco ( `*` ) per consentire il traffico su qualsiasi porta</li></ul> | Questa impostazione specifica le porte su cui la regola consente o nega il traffico. È previsto un limite al numero di porte che è possibile specificare. Per altri dettagli, vedere Limiti [di Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Destinazione** | Uno dei valori possibili:<ul><li>**qualsiasi**</li><li>**Indirizzi IP**</li><li>**Tag di servizio** (regola di sicurezza in uscita) **o VirtualNetwork** (regola di sicurezza in ingresso)</li><li>**Gruppo di &nbsp; sicurezza delle &nbsp; applicazioni**</li></ul> | <p>Se si sceglie **Indirizzi IP,** specificare anche Indirizzi **IP di destinazione/Intervalli CIDR**.</p><p>Se si sceglie **VirtualNetwork,** il traffico è consentito a tutti gli indirizzi IP all'interno dello spazio indirizzi della rete virtuale. **VirtualNetwork** è un tag di servizio.</p><p>Se si seleziona **Gruppo di sicurezza delle applicazioni**, è necessario selezionare un gruppo di sicurezza delle applicazioni esistente. Come [creare un gruppo di sicurezza delle applicazioni](#create-an-application-security-group).</p> |
    | **Indirizzi IP/intervalli CIDR di destinazione** | Elenco delimitato da virgole di indirizzi IP e intervalli CIDR | <p>Questa impostazione viene visualizzata se si imposta **Destinazione** su **Indirizzi IP.** Analogamente agli **intervalli** **CIDR/indirizzi IP di** origine e di origine, è possibile specificare uno o più indirizzi o intervalli. Esistono limiti al numero che è possibile specificare. Per altri dettagli, vedere Limiti [di Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Se l'indirizzo IP specificato viene assegnato a una macchina virtuale di Azure, assicurarsi di specificare il relativo indirizzo IP privato, non il relativo indirizzo IP pubblico. Azure elabora le regole di sicurezza dopo la conversione dell'indirizzo IP pubblico in un indirizzo IP privato per le regole di sicurezza in ingresso, ma prima che Azure converta un indirizzo IP privato in un indirizzo IP pubblico per le regole in uscita. Per altre informazioni sugli indirizzi IP pubblici e privati in Azure, vedere [Tipi di indirizzo IP](./public-ip-addresses.md).</p> |
    | **Tag del servizio di destinazione** | Un tag di servizio dall'elenco a discesa | Questa impostazione facoltativa viene visualizzata se si imposta **Destinazione** su **Tag di servizio** per una regola di sicurezza in uscita. Un tag di servizio è un identificatore predefinito per una categoria di indirizzi IP. Per altre informazioni sui tag di servizio disponibili e su ciò che ogni tag rappresenta, vedere [Tag di servizio.](./network-security-groups-overview.md#service-tags) |
    | **Gruppo di sicurezza delle applicazioni di destinazione** | Un gruppo di sicurezza delle applicazioni esistente | Questa impostazione viene visualizzata se si imposta **Destinazione su** Gruppo di **sicurezza dell'applicazione.** Selezionare un gruppo di sicurezza delle applicazioni presente nella stessa area dell'interfaccia di rete. Come [creare un gruppo di sicurezza delle applicazioni](#create-an-application-security-group). |
    | **Intervalli di porte di destinazione** | Uno dei valori possibili:<ul><li>Una singola porta, ad esempio `80`</li><li>Un intervallo di porte, ad esempio `1024-65535`</li><li>Elenco delimitato da virgole di singole porte e/o intervalli di porte, ad esempio `80, 1024-65535`</li><li>Asterisco ( `*` ) per consentire il traffico su qualsiasi porta</li></ul> | Come per **gli intervalli di porte di origine,** è possibile specificare una o più porte e intervalli. Esistono limiti al numero che è possibile specificare. Per altre informazioni, vedere [Limiti di Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Protocollo** | **Qualsiasi**, **TCP,** **UDP** o **ICMP** | È possibile limitare la regola a Transmission Control Protocol (TCP), UDP (User Datagram Protocol) o Internet Control Message Protocol (ICMP). Il valore predefinito è per la regola da applicare a tutti i protocolli. |
    | **Azione** | **Consenti** o **Nega** | Questa impostazione specifica se questa regola consente o nega l'accesso per la configurazione di origine e destinazione fornita. |
    | **Priorità** | Valore compreso tra 100 e 4096 univoco per tutte le regole di sicurezza all'interno del gruppo di sicurezza di rete | Azure elabora le regole di sicurezza in ordine di priorità. Più è basso il numero, maggiore sarà la priorità. È consigliabile lasciare un gap tra i numeri di priorità quando si creano regole, ad esempio 100, 200 e 300. Lasciare lacune semplifica l'aggiunta di regole in futuro, in modo che sia possibile assegnare loro una priorità più alta o inferiore rispetto alle regole esistenti. |
    | **Nome** | Nome univoco per la regola all'interno del gruppo di sicurezza di rete | Il nome può contenere fino a 80 caratteri. Deve iniziare con una lettera o un numero e terminare con una lettera, un numero o un carattere di sottolineatura. Il nome può contenere solo lettere, numeri, caratteri di sottolineatura, punti o trattini. |
    | **Descrizione** | Descrizione di testo | Facoltativamente, è possibile specificare una descrizione di testo per la regola di sicurezza. La descrizione non può contenere più di 140 caratteri. |

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Visualizzare tutte le regole di sicurezza

Un gruppo di sicurezza di rete contiene zero o più regole. Per saperne di più sulle informazioni elencate quando vengono visualizzate le regole, vedere [Panoramica dei gruppi di sicurezza di rete](./network-security-groups-overview.md).

1. Passare al [portale di Azure](https://portal.azure.com) per visualizzare le regole di un gruppo di sicurezza di rete. Cercare e selezionare Gruppi **di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete per il quale si desidera visualizzare le regole.

3. Nella barra dei menu del gruppo di sicurezza di rete scegliere **Regole di sicurezza in ingresso** o Regole di sicurezza in **uscita.**

L'elenco contiene tutte le regole create e le regole di sicurezza predefinite del gruppo [di sicurezza di rete.](./network-security-groups-overview.md#default-security-rules)

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Visualizzare i dettagli di una regola di sicurezza

1. Passare al [portale di Azure](https://portal.azure.com) per visualizzare le regole di un gruppo di sicurezza di rete. Cercare e selezionare Gruppi **di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete per il quale si desidera visualizzare i dettagli di una regola.

3. Nella barra dei menu del gruppo di sicurezza di rete scegliere **Regole di sicurezza in ingresso** o Regole di sicurezza in **uscita.**

4. Selezionare la regola di cui si vogliono visualizzare i dettagli. Per una spiegazione di tutte le impostazioni, vedere [Impostazioni delle regole di sicurezza.](#security-rule-settings)

    > [!NOTE]
    > Questa procedura si applica solo a una regola di sicurezza personalizzata. Non funziona se si sceglie una regola di sicurezza predefinita.

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg rule show](/cli/azure/network/nsg/rule#az_network_nsg_rule_show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Modificare una regola di sicurezza

1. Completare i passaggi descritti in [Visualizzare i dettagli di una regola di sicurezza](#view-details-of-a-security-rule).

2. Modificare le impostazioni in base alle esigenze e quindi selezionare **Salva**. Per una spiegazione di tutte le impostazioni, vedere [Impostazioni delle regole di sicurezza](#security-rule-settings).

    > [!NOTE]
    > Questa procedura si applica solo a una regola di sicurezza personalizzata. Non è consentito modificare una regola di sicurezza predefinita.

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Eliminare una regola di sicurezza

1. Completare i passaggi descritti in [Visualizzare i dettagli di una regola di sicurezza](#view-details-of-a-security-rule).

2. Selezionare **Elimina** e quindi scegliere **Sì**.

    > [!NOTE]
    > Questa procedura si applica solo a una regola di sicurezza personalizzata. Non è consentito eliminare una regola di sicurezza predefinita.

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg rule delete](/cli/azure/network/nsg/rule#az_network_nsg_rule_delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Usare i gruppi di sicurezza delle applicazioni

Un gruppo di sicurezza delle applicazioni contiene zero o più interfacce di rete. Per altre informazioni, vedere [Gruppi di sicurezza delle applicazioni](./network-security-groups-overview.md#application-security-groups). Tutte le interfacce di rete in un gruppo di sicurezza delle applicazioni devono trovarsi nella stessa rete virtuale. Per informazioni su come aggiungere un'interfaccia di rete a un gruppo di sicurezza delle applicazioni, vedere [Aggiungere un'interfaccia di rete a un gruppo di sicurezza delle applicazioni](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Creare un gruppo di sicurezza delle applicazioni

1. Nel [menu del portale di Azure](https://portal.azure.com) o nella **home page** selezionare **Crea una risorsa**.

2. Nella casella di ricerca immettere *Gruppo di sicurezza dell'applicazione*.

3. Nella pagina **Gruppo di sicurezza dell'applicazione** selezionare **Crea**.

4. Nella pagina **Crea un gruppo di sicurezza dell'applicazione,** nella scheda **Informazioni** di base, impostare i valori per le impostazioni seguenti:

    | Impostazione | Azione |
    | --- | --- |
    | **Sottoscrizione** | Scegliere la propria sottoscrizione. |
    | **Gruppo di risorse** | Scegliere un gruppo di risorse esistente oppure selezionare **Crea nuovo** per creare un nuovo gruppo di risorse. |
    | **Nome** | Immettere una stringa di testo univoca all'interno di un gruppo di risorse. |
    | **Area** | Scegliere il percorso desiderato. |

5. Selezionare **Rivedi e crea**.

6. Nella scheda **Rivedi e crea** selezionare Crea dopo aver visualizzato il messaggio **Convalida** **superata.**

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network asg create](/cli/azure/network/asg#az_network_asg_create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Visualizzare tutti i gruppi di sicurezza delle applicazioni

Passare al [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza delle applicazioni. Cercare e selezionare Gruppi **di sicurezza dell'applicazione**. Il portale di Azure visualizza un elenco dei gruppi di sicurezza delle applicazioni.

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network asg list](/cli/azure/network/asg#az_network_asg_list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Visualizzare i dettagli di un gruppo di sicurezza delle applicazioni specifico

1. Passare al [portale di Azure](https://portal.azure.com) per visualizzare un gruppo di sicurezza delle applicazioni. Cercare e selezionare Gruppi **di sicurezza delle applicazioni.**

2. Selezionare il nome del gruppo di sicurezza delle applicazioni di cui si vogliono visualizzare i dettagli.

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network asg show](/cli/azure/network/asg#az_network_asg_show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Modificare un gruppo di sicurezza delle applicazioni

1. Passare [all'portale di Azure](https://portal.azure.com) per visualizzare un gruppo di sicurezza delle applicazioni. Cercare e selezionare Gruppi **di sicurezza delle applicazioni.**

2. Selezionare il nome del gruppo di sicurezza delle applicazioni da modificare.

3. Selezionare **Cambia** accanto all'impostazione che si vuole modificare. Ad esempio, è possibile aggiungere o rimuovere **tag** oppure modificare il gruppo **di risorse o** la **sottoscrizione**.

    > [!NOTE]
    > Non è possibile modificare la posizione.

    Nella barra dei menu è anche possibile selezionare **Controllo di accesso (IAM).** Nella pagina **Controllo di accesso (IAM)** è possibile assegnare o rimuovere autorizzazioni per il gruppo di sicurezza delle applicazioni.

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network asg update](/cli/azure/network/asg#az_network_asg_update) |
| PowerShell | Nessun cmdlet di PowerShell |

### <a name="delete-an-application-security-group"></a>Eliminare un gruppo di sicurezza delle applicazioni

Non è possibile eliminare un gruppo di sicurezza delle applicazioni se contiene interfacce di rete. Per rimuovere tutte le interfacce di rete dal gruppo di sicurezza delle applicazioni, modificare le impostazioni dell'interfaccia di rete o eliminare le interfacce di rete. Per altre informazioni, vedere Aggiungere o [rimuovere da](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) gruppi di sicurezza delle applicazioni o Eliminare [un'interfaccia di rete.](virtual-network-network-interface.md#delete-a-network-interface)

1. Passare al [portale di Azure](https://portal.azure.com) per gestire i gruppi di sicurezza delle applicazioni. Cercare e selezionare Gruppi **di sicurezza dell'applicazione**.

2. Selezionare il nome del gruppo di sicurezza dell'applicazione da eliminare.

3. Selezionare **Elimina**, quindi selezionare **Sì** per eliminare il gruppo di sicurezza dell'applicazione.

#### <a name="commands"></a>Comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network asg delete](/cli/azure/network/asg#az_network_asg_delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività sui gruppi di sicurezza di rete, sulle regole [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) di sicurezza e sui gruppi di sicurezza delle applicazioni, l'account deve essere assegnato al ruolo Collaboratore rete o [a](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) un ruolo Personalizzato a cui sono assegnate le autorizzazioni appropriate, come elencato nelle tabelle seguenti:

### <a name="network-security-group"></a>Gruppo di sicurezza di rete

| Azione                                                        |   Nome                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Ottenere un gruppo di sicurezza di rete                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Creare o aggiornare un gruppo di sicurezza di rete                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Eliminare un gruppo di sicurezza di rete                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Assegnare un gruppo di sicurezza di rete a una subnet o un'interfaccia di rete 


>[!NOTE]
> Per eseguire `write` operazioni su un gruppo di sicurezza di rete, l'account di sottoscrizione deve avere almeno le autorizzazioni per il gruppo di risorse insieme `read` `Microsoft.Network/networkSecurityGroups/write` all'autorizzazione .


### <a name="network-security-group-rule"></a>Regola del gruppo di sicurezza di rete

| Azione                                                        |   Nome                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   Ottenere una regola                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   Creare o aggiornare una regola                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   Eliminare una regola                                                         |

### <a name="application-security-group"></a>Gruppo di sicurezza dell'applicazione

| Azione                                                                     | Nome                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Aggiungere una configurazione IP a gruppi di sicurezza dell'applicazione|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Aggiungere una regola di sicurezza a un gruppo di sicurezza dell'applicazione    |
| Microsoft.Network/applicationSecurityGroups/read                           | Ottenere un gruppo di sicurezza dell'applicazione                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Creare o aggiornare un gruppo di sicurezza delle applicazioni           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Eliminare un gruppo di sicurezza delle applicazioni                     |

## <a name="next-steps"></a>Passaggi successivi

- Creare un gruppo di sicurezza di rete o di applicazioni usando [script di esempio di PowerShell](powershell-samples.md) o dell'interfaccia della riga di comando di [Azure](cli-samples.md) o modelli di sicurezza [Resource Manager Azure](template-samples.md)
- Creare e assegnare [Criteri di Azure per](./policy-reference.md) le reti virtuali
