---
title: Configurare i firewall e le reti virtuali di Archiviazione di Azure | Microsoft Docs
description: Configurare la sicurezza di rete su più livelli per l'account di archiviazione usando i firewall di archiviazione di Azure e la rete virtuale di Azure.
services: storage
author: santoshc
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 9e395ce996bf7e6889a27fcb04b0e643cf63c58b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430888"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurare i firewall e le reti virtuali di Archiviazione di Azure

Archiviazione di Azure offre un modello di sicurezza su più livelli, Questo modello consente di proteggere e controllare il livello di accesso agli account di archiviazione richiesti dalle applicazioni e dagli ambienti aziendali, in base al tipo e al subset di reti o risorse usate. Quando vengono configurate le regole di rete, solo le applicazioni che richiedono dati tramite il set di reti specificato o tramite il set specificato di risorse di Azure possono accedere a un account di archiviazione. È possibile limitare l'accesso all'account di archiviazione alle richieste provenienti da indirizzi IP, intervalli IP, subnet in una rete virtuale di Azure (VNet) o istanze di risorse di alcuni servizi di Azure specificati.

Gli account di archiviazione hanno un endpoint pubblico accessibile tramite Internet. È anche possibile creare [endpoint privati per l'account di archiviazione](storage-private-endpoints.md), assegnando così un indirizzo IP privato della rete virtuale all'account di archiviazione e proteggendo tutto il traffico tra la rete virtuale e l'account di archiviazione tramite un collegamento privato. Il firewall di archiviazione di Azure fornisce il controllo di accesso per l'endpoint pubblico dell'account di archiviazione. Si può usare il firewall anche per bloccare tutti gli accessi tramite l'endpoint pubblico quando si usano endpoint privati. La configurazione del firewall di archiviazione consente inoltre di selezionare servizi della piattaforma Azure attendibili per accedere in modo sicuro all'account di archiviazione.

Per accedere a un account di archiviazione quando le regole di rete sono applicate, un'applicazione deve ancora inviare una richiesta che deve essere correttamente autorizzata. L'autorizzazione è supportata con le credenziali di Azure Active Directory (Azure AD) per BLOB e code, con una chiave di accesso dell'account valida o con un token di firma di accesso condiviso.

> [!IMPORTANT]
> L'attivazione delle regole firewall per l'account di archiviazione blocca le richieste in ingresso per i dati per impostazione predefinita, a meno che le richieste non provengano da un servizio in esecuzione all'interno di una rete virtuale di Azure o da indirizzi IP pubblici consentiti. Le richieste che vengono bloccate sono quelle che provengono da altri servizi di Azure, dal portale di Azure, dai servizi di registrazione e metriche e così via.
>
> È possibile concedere l'accesso ai servizi di Azure eseguiti all'interno di una rete virtuale consentendo il traffico dalla subnet che ospita l'istanza del servizio. È anche possibile abilitare un numero limitato di scenari tramite il meccanismo di eccezioni descritto di seguito. Per accedere ai dati dall'account di archiviazione tramite il portale di Azure, è necessario usare un computer all'interno del limite attendibile (IP o rete virtuale) configurato.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenari

Per proteggere l'account di archiviazione, occorre innanzitutto configurare una regola per negare l'accesso al traffico da tutte le reti (incluso il traffico Internet) nell'endpoint pubblico, per impostazione predefinita. Quindi, è necessario configurare regole che concedano l'accesso al traffico da reti virtuali specifiche. È anche possibile configurare regole per concedere l'accesso al traffico da intervalli di indirizzi IP Internet pubblici selezionati, abilitando le connessioni da client Internet o locali specifici. Questa configurazione consente di creare un limite di rete protetto per le applicazioni.

È possibile combinare regole del firewall che consentono l'accesso da reti virtuali specifiche e da intervalli di indirizzi IP pubblici nello stesso account di archiviazione. Le regole del firewall di archiviazione possono essere applicate ad account di archiviazione esistenti oppure durante la creazione di nuovi account di archiviazione.

Le regole del firewall di archiviazione si applicano all'endpoint pubblico di un account di archiviazione. Non sono necessarie regole di accesso al firewall per consentire il traffico per gli endpoint privati di un account di archiviazione. Il processo di approvazione della creazione di un endpoint privato concede l'accesso implicito al traffico dalla subnet che ospita l'endpoint privato.

Le regole di rete vengono applicate a tutti i protocolli di rete per archiviazione di Azure, inclusi REST e SMB. Per accedere ai dati usando strumenti come il portale di Azure, Storage Explorer e AZCopy, è necessario configurare regole di rete esplicite.

Una volta configurate, le regole di rete vengono applicate a tutte le richieste. I token di firma di accesso condiviso che concedono l'accesso a un indirizzo IP specifico hanno lo scopo di limitare l'accesso del titolare del token, ma non concedono nuovi accessi oltre le regole di rete configurate.

Le regole di rete non influiscono sul traffico del disco della macchina virtuale (incluse le operazioni di montaggio e smontaggio e l'I/O del disco). L'accesso REST ai BLOB di pagine è protetto dalle regole di rete.

Gli account di archiviazione classici non supportano i firewall e le reti virtuali.

È possibile usare dischi non gestiti negli account di archiviazione con regole di rete applicate per eseguire il backup e il ripristino di VM creando un'eccezione. Questo processo è documentato nella sezione [Manage Exceptions](#manage-exceptions) di questo articolo. Le eccezioni firewall non sono applicabili ai dischi gestiti perché sono già gestiti da Azure.

## <a name="change-the-default-network-access-rule"></a>Modificare la regola predefinita di accesso alla rete

Per impostazione predefinita, gli account di archiviazione accettano connessioni da client di qualsiasi rete. Per poter limitare l'accesso alle sole reti selezionate è necessario modificare l'azione predefinita.

> [!WARNING]
> La modifica delle regole di rete può influire sulla capacità di connessione delle applicazioni al servizio Archiviazione di Azure. L'impostazione della regola di rete predefinita **Nega** blocca qualsiasi accesso ai dati, a meno che non vengano applicate anche regole di rete specifiche che **concedono** l'accesso. Prima di modificare la regola predefinita per negare l'accesso, verificare di concedere l'accesso alle reti autorizzate mediante le regole di rete.

### <a name="managing-default-network-access-rules"></a>Gestione delle regole predefinite di accesso alla rete

Le regole predefinite di accesso alla rete per gli account di archiviazione possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando v2.

#### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare all'account di archiviazione che si vuole proteggere.

2. Selezionare dal menu impostazioni denominato **rete**.

3. Per negare l'accesso per impostazione predefinita, scegliere di consentire l'accesso da **Reti selezionate**. Per consentire il traffico da tutte le reti, scegliere di consentire l'accesso da **Tutte le reti**.

4. Selezionare **Salva** per applicare le modifiche.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installare [Azure PowerShell](/powershell/azure/install-Az-ps) e [accedere](/powershell/azure/authenticate-azureps).

2. Visualizzare lo stato della regola predefinita per l'account di archiviazione.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Impostare la regola predefinita per negare l'accesso alla rete per impostazione predefinita.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Impostare la regola predefinita per consentire l'accesso alla rete per impostazione predefinita.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [accedere](/cli/azure/authenticate-azure-cli).

2. Visualizzare lo stato della regola predefinita per l'account di archiviazione.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Impostare la regola predefinita per negare l'accesso alla rete per impostazione predefinita.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Impostare la regola predefinita per consentire l'accesso alla rete per impostazione predefinita.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Concedere l'accesso da una rete virtuale

È possibile configurare gli account di archiviazione in modo da consentire l'accesso solo da subnet specifiche. Le subnet consentite possono appartenere a una rete virtuale nella stessa sottoscrizione o a quelle in una sottoscrizione diversa, incluse le sottoscrizioni appartenenti a un tenant di Azure Active Directory diverso.

Abilitare un [endpoint di servizio](../../virtual-network/virtual-network-service-endpoints-overview.md) per Archiviazione di Azure all'interno della rete virtuale. L'endpoint di servizio instrada il traffico dalla rete virtuale tramite un percorso ottimale al servizio Archiviazione di Azure. Con ogni richiesta vengono anche trasmesse le identità della subnet e della rete virtuale. Gli amministratori possono quindi configurare regole di rete per l'account di archiviazione che consentano la ricezione di richieste da subnet specifiche in una rete virtuale. Per accedere ai dati, i client ai quali viene garantito l'accesso con queste regole di rete devono continuare a soddisfare i requisiti di autorizzazione dell'account di archiviazione.

Ogni account di archiviazione supporta fino a 200 regole della rete virtuale, che possono essere combinate con [le regole di rete IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Aree della rete virtuale disponibili

In generale gli endpoint di servizio funzionano tra reti virtuali e istanze del servizio incluse nella stessa area di Azure. Quando si usano endpoint di servizio con Archiviazione di Azure, questo ambito viene espanso per includere le [aree abbinate](../../best-practices-availability-paired-regions.md). Gli endpoint di servizio garantiscono la continuità durante un failover a livello di area, nonché l'accesso alle istanze di archiviazione con ridondanza geografica e accesso in lettura. Le regole di rete che concedono l'accesso da una rete virtuale a un account di archiviazione concedono anche l'accesso a qualsiasi istanza RA-GRS.

Quando si pianifica il ripristino di emergenza durante un'interruzione a livello di area, è consigliabile creare in anticipo le reti virtuali nell'area abbinata. Abilitare gli endpoint di servizio per Archiviazione di Azure, con regole di rete che concedono l'accesso da queste reti virtuali alternative. Applicare quindi le regole agli account di archiviazione con ridondanza geografica.

> [!NOTE]
> Gli endpoint di servizio non si applicano al traffico esterno all'area della rete virtuale di Microsoft Azure e alla coppia di aree designata. Le regole di rete che concedono l'accesso dalle reti virtuali agli account di archiviazione possono essere applicate solo nell'area primaria di un account di archiviazione o nell'area abbinata designata.

### <a name="required-permissions"></a>Autorizzazioni necessarie

Per applicare una regola di rete virtuale a un account di archiviazione, l'utente deve avere le autorizzazioni appropriate per le subnet aggiunte. L'applicazione di una regola può essere eseguita da un [collaboratore account di archiviazione](../../role-based-access-control/built-in-roles.md#storage-account-contributor) o da un utente a cui è stata assegnata l'autorizzazione per l' `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` [operazione del provider di risorse di Azure](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork) tramite un ruolo personalizzato di Azure.

Gli account di archiviazione e le reti virtuali alle quali è stato concesso l'accesso possono trovarsi in sottoscrizioni diverse, incluse quelle che appartengono a un tenant di Azure AD diverso.

> [!NOTE]
> La configurazione di regole che concedano l'accesso alle subnet nelle reti virtuali che appartengono a un tenant di Azure Active Directory diverso è attualmente supportata solo tramite PowerShell, l'interfaccia della riga di comando e le API REST. Queste regole non possono essere configurate tramite il portale di Azure, anche se possono essere visualizzate nel portale.

### <a name="managing-virtual-network-rules"></a>Gestione delle regole di rete virtuale

Le regole di rete virtuale per gli account di archiviazione possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando v2.

#### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare all'account di archiviazione che si vuole proteggere.

2. Selezionare dal menu impostazioni denominato **rete**.

3. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

4. Per concedere l'accesso a una rete virtuale con una nuova regola di rete, in **reti virtuali** selezionare **Aggiungi rete virtuale esistente**, selezionare opzioni **reti virtuali** e **subnet** , quindi selezionare **Aggiungi**. Per creare una nuova rete virtuale e concedergli l'accesso, selezionare **Aggiungi nuova rete virtuale**. Fornire le informazioni necessarie per creare la nuova rete virtuale e quindi selezionare **Crea**.

    > [!NOTE]
    > Se un endpoint di servizio per Archiviazione di Azure non è stato configurato in precedenza per la rete virtuale e le subnet selezionate, è possibile configurarlo in questa operazione.
    >
    > Attualmente solo le reti virtuali che appartengono allo stesso tenant di Azure Active Directory sono disponibili per la selezione durante la creazione delle regole. Per concedere l'accesso a una subnet in una rete virtuale che appartiene a un altro tenant, usare PowerShell, l'interfaccia della riga di comando o le API REST.

5. Per rimuovere una regola della rete virtuale o della subnet, selezionare **...** per aprire il menu di scelta rapida per la rete virtuale o la subnet e selezionare **Rimuovi**.

6. Selezionare **Save (Salva** ) per applicare le modifiche.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installare [Azure PowerShell](/powershell/azure/install-Az-ps) e [accedere](/powershell/azure/authenticate-azureps).

2. Visualizzare l'elenco delle regole di rete virtuale.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Abilitare l'endpoint di servizio di Archiviazione di Azure in una rete virtuale e una subnet esistenti.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Aggiungere una regola di rete per una rete virtuale e una subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Per aggiungere una regola di rete per una subnet in una rete virtuale che appartiene a un altro tenant di Azure AD, usare un parametro **VirtualNetworkResourceId** completo nel formato "/subscriptions/ID-sottoscrizione/resourceGroups/Nome-gruppo-risorse/providers/Microsoft.Network/virtualNetworks/nome-rete-virtuale/subnets/nome-subnet".

5. Rimuovere una regola di rete per una rete virtuale e una subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [accedere](/cli/azure/authenticate-azure-cli).

2. Visualizzare l'elenco delle regole di rete virtuale.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Abilitare l'endpoint di servizio di Archiviazione di Azure in una rete virtuale e una subnet esistenti.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Aggiungere una regola di rete per una rete virtuale e una subnet.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Per aggiungere una regola per una subnet in una VNet che appartiene a un altro tenant Azure AD, usare un ID di subnet completo nel formato "/subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /Subnets/ \<subnet-name\> ".
    >
    > È possibile usare il parametro **subscription** per recuperare l'ID subnet di una rete virtuale che appartiene a un altro tenant di Azure AD.

5. Rimuovere una regola di rete per una rete virtuale e una subnet.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

---

## <a name="grant-access-from-an-internet-ip-range"></a>Concedere l'accesso da un intervallo IP di Internet

È possibile configurare gli account di archiviazione in modo che consentano l'accesso da intervalli specifici di indirizzi IP Internet pubblici. Questa configurazione concede l'accesso ai servizi specifici basati su Internet e alle reti locali e blocca il traffico Internet generale.

Specificare gli intervalli di indirizzi Internet consentiti con la [notazione CIDR](https://tools.ietf.org/html/rfc4632) nel formato *16.17.18.0/24* o come indirizzi IP singoli, ad esempio *16.17.18.19*.

   > [!NOTE]
   > Gli intervalli di indirizzi di piccole dimensioni con dimensioni di prefisso "/31" o "/32" non sono supportati. Questi intervalli vanno configurati con le regole usate per gli indirizzi IP singoli.

Le regole di rete per gli IP sono consentite solo per gli indirizzi IP della **rete Internet pubblica**. Gli intervalli di indirizzi IP riservati per le reti private (come definito nell'[RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) non sono consentiti nelle regole IP. Le reti private includono gli indirizzi che iniziano con _10.*_ , _172.16.*_  - _172.31.*_ e _192.168.*_ .

   > [!NOTE]
   > Le regole della rete IP non hanno alcun effetto sulle richieste provenienti dalla stessa area di Azure dell'account di archiviazione. Usare le [regole di rete virtuale](#grant-access-from-a-virtual-network) per consentire richieste della stessa area.

  > [!NOTE]
  > I servizi distribuiti nella stessa area dell'account di archiviazione usano indirizzi IP privati di Azure per la comunicazione. Di conseguenza, non è possibile limitare l'accesso a servizi di Azure specifici in base all'intervallo di indirizzi IP in uscita pubblici.

Per la configurazione delle regole del firewall di archiviazione sono supportati solo gli indirizzi IPV4.

Ogni account di archiviazione supporta fino a 200 regole di rete IP.

### <a name="configuring-access-from-on-premises-networks"></a>Configurazione dell'accesso da reti locali

Per garantire l'accesso all'account di archiviazione dalle reti locali con una regola di rete IP è necessario identificare gli indirizzi IP esposti a Internet usati dalla rete. Per assistenza contattare l'amministratore di rete.

Se si usa [ExpressRoute](../../expressroute/expressroute-introduction.md) dall'ambiente locale, per il peering pubblico o per il peering Microsoft, sarà necessario identificare gli indirizzi IP NAT usati. Per il peering pubblico, ogni circuito ExpressRoute usa per impostazione predefinita due indirizzi IP NAT applicati al traffico del servizio di Azure quando il traffico entra nel backbone della rete di Microsoft Azure. Per il peering Microsoft, gli indirizzi IP NAT usati vengono forniti dal cliente o dal provider di servizi. Per consentire l'accesso alle risorse del servizio è necessario autorizzare questi indirizzi IP pubblici nell'impostazione del firewall IP per le risorse. Per trovare gli indirizzi IP del circuito ExpressRoute per il peering pubblico, [aprire un ticket di supporto in ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure. Vedere altre informazioni su [NAT per il peering pubblico e il peering Microsoft ExpressRoute.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gestione delle regole di rete IP

Le regole di rete IP per gli account di archiviazione possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando v2.

#### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare all'account di archiviazione che si vuole proteggere.

2. Selezionare dal menu impostazioni denominato **rete**.

3. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

4. Per concedere l'accesso a un intervallo IP di Internet, immettere l'indirizzo IP o l'intervallo di indirizzi (in formato CIDR) in **Firewall** > **Intervallo di indirizzi**.

5. Per rimuovere una regola di rete IP, selezionare l'icona del cestino accanto all'intervallo di indirizzi.

6. Selezionare **Salva** per applicare le modifiche.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installare [Azure PowerShell](/powershell/azure/install-Az-ps) e [accedere](/powershell/azure/authenticate-azureps).

2. Elencare le regole di rete IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Aggiungere una regola di rete per un singolo indirizzo IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Aggiungere una regola di rete per un intervallo di indirizzi IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Rimuovere una regola di rete per un singolo indirizzo IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Rimuovere una regola di rete per un intervallo di indirizzi IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [accedere](/cli/azure/authenticate-azure-cli).

1. Elencare le regole di rete IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Aggiungere una regola di rete per un singolo indirizzo IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Aggiungere una regola di rete per un intervallo di indirizzi IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Rimuovere una regola di rete per un singolo indirizzo IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Rimuovere una regola di rete per un intervallo di indirizzi IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Concedi l'accesso da istanze di risorse di Azure (anteprima)

In alcuni casi, un'applicazione potrebbe dipendere da risorse di Azure che non possono essere isolate tramite una rete virtuale o una regola di indirizzi IP. Tuttavia, si vuole proteggere e limitare l'accesso all'account di archiviazione solo alle risorse di Azure dell'applicazione. È possibile configurare gli account di archiviazione per consentire l'accesso a istanze di risorse specifiche di alcuni servizi di Azure creando una regola dell'istanza di risorsa. 

I tipi di operazioni che un'istanza di risorsa può eseguire sui dati dell'account di archiviazione sono determinati dalle [assegnazioni di ruolo di Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) dell'istanza di risorsa. Le istanze di risorse devono provenire dallo stesso tenant dell'account di archiviazione, ma possono appartenere a qualsiasi sottoscrizione nel tenant.

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica ed è disponibile in tutte le aree del cloud pubblico.

> [!NOTE]
> Le regole dell'istanza di risorsa sono attualmente supportate solo per le sinapsi di Azure. Il supporto per altri servizi di Azure elencati nella sezione relativa all' [identità gestita assegnata dal sistema](#trusted-access-system-assigned-managed-identity) di questo articolo sarà disponibile nelle prossime settimane.


### <a name="portal"></a>[Portale](#tab/azure-portal)

È possibile aggiungere o rimuovere le regole di rete delle risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/) per iniziare.

2. Individuare l'account di archiviazione e visualizzare la sezione della panoramica dell'account.

3. Selezionare **rete** per visualizzare la pagina di configurazione per la rete.

4. Nell'elenco a discesa **tipo di risorsa** scegliere il tipo di risorsa dell'istanza di risorsa. 

5. Nell'elenco a discesa **nome istanza** selezionare l'istanza della risorsa. È anche possibile scegliere di includere tutte le istanze di risorse nel tenant attivo, nella sottoscrizione o nel gruppo di risorse.

6. Selezionare **Salva** per applicare le modifiche. L'istanza della risorsa viene visualizzata nella sezione **istanze risorse** della pagina impostazioni di rete. 

Per rimuovere l'istanza di risorsa, selezionare l'icona di eliminazione ( :::image type="icon" source="media/storage-network-security/delete-icon.png"::: ) accanto all'istanza di risorsa.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

È possibile usare i comandi di PowerShell per aggiungere o rimuovere le regole di rete delle risorse.

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

#### <a name="install-the-preview-module"></a>Installare il modulo di anteprima

Installare la versione più recente del modulo PowershellGet. Chiudere e riaprire la console di PowerShell.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Installare il modulo **AZ. storage** Preview.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

Per altre informazioni su come installare i moduli di PowerShell, vedere [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>Concedere l'accesso

Aggiungere una regola di rete per concedere l'accesso da un'istanza di risorsa.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Specificare più istanze di risorse contemporaneamente modificando il set di regole di rete.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Rimuovere l'accesso

Rimuovere una regola di rete che concede l'accesso da un'istanza di risorsa.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Rimuovere tutte le regole di rete che concedono l'accesso dalle istanze di risorse.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Visualizzare un elenco di istanze di risorse consentite

Consente di visualizzare un elenco completo delle istanze di risorse a cui è stato concesso l'accesso all'account di archiviazione.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile usare i comandi dell'interfaccia della riga di comando di Azure per aggiungere o rimuovere regole di rete delle risorse

#### <a name="install-the-preview-extension"></a>Installare l'estensione di anteprima

1. Aprire [Azure Cloud Shell](../../cloud-shell/overview.md)o aprire un'applicazione console comando come Windows PowerShell, se è stata [installata](/cli/azure/install-azure-cli) l'interfaccia della riga di comando di Azure in locale.

2. Quindi, verificare che la versione dell'interfaccia della riga di comando di Azure installata sia `2.13.0` o superiore usando il comando seguente.

   ```azurecli
   az --version
   ```

   Se la versione dell'interfaccia della riga di comando di Azure fosse inferiore a `2.13.0`, installare una versione successiva. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

3. Per installare l'estensione di anteprima, digitare il comando seguente.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Concedere l'accesso

Aggiungere una regola di rete per concedere l'accesso da un'istanza di risorsa.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Rimuovere l'accesso

Rimuovere una regola di rete che concede l'accesso da un'istanza di risorsa.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Visualizzare un elenco di istanze di risorse consentite

Consente di visualizzare un elenco completo delle istanze di risorse a cui è stato concesso l'accesso all'account di archiviazione.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>Concessione dell'accesso ai servizi di Azure attendibili 

Alcuni servizi di Azure operano da reti che non possono essere incluse nelle regole di rete. È possibile concedere a un sottoinsieme di tali servizi di Azure attendibili l'accesso all'account di archiviazione, mantenendo al tempo stesso le regole di rete per altre app. Questi servizi attendibili utilizzeranno quindi l'autenticazione avanzata per connettersi in modo sicuro all'account di archiviazione.

È possibile concedere l'accesso ai servizi di Azure attendibili creando un'eccezione della regola di rete. Per istruzioni dettagliate, vedere la sezione [gestire le eccezioni](#manage-exceptions) di questo articolo.

Quando si concede l'accesso ai servizi di Azure attendibili, vengono concessi i tipi di accesso seguenti:

- Accesso attendibile per le operazioni di selezione alle risorse registrate nella sottoscrizione.
- Accesso attendibile alle risorse in base all'identità gestita assegnata dal sistema.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Accesso attendibile per le risorse registrate nella sottoscrizione

Le risorse di alcuni servizi, **se registrate nella sottoscrizione**, possono accedere all'account di archiviazione **nella stessa sottoscrizione** per determinate operazioni, come la scrittura di log o il backup.  La tabella seguente descrive ogni servizio e le operazioni consentite. 

| Service                  | Nome provider di risorse     | Operazioni consentite                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Backup di Azure             | Microsoft.RecoveryServices | Eseguire il backup e il ripristino di dischi non gestiti nelle macchine virtuali IAAS (non obbligatorio per i dischi gestiti). [Altre informazioni](../../backup/backup-overview.md) |
| Azure Data Box           | Microsoft.DataBox          | Consente l'importazione di dati in Azure tramite Data Box. [Altre informazioni](../../databox/data-box-overview.md) |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Creazione di immagini personalizzate e installazione di artefatti. [Altre informazioni](../../devtest-labs/devtest-lab-overview.md) |
| Griglia di eventi di Azure         | Microsoft.EventGrid        | Abilitare la pubblicazione di eventi di archiviazione BLOB e consentire a Griglia di eventi la pubblicazione nelle code di archiviazione. Informazioni sugli [eventi di archiviazione BLOB](../../event-grid/overview.md#event-sources) e sulla [pubblicazione nelle code](../../event-grid/event-handlers.md). |
| Hub eventi di Azure         | Microsoft.EventHub         | Archiviare dati con Acquisizione di Hub eventi. [Altre informazioni](../../event-hubs/event-hubs-capture-overview.md). |
| Sincronizzazione file di Azure          | Microsoft.StorageSync      | Consente di trasformare il file server locale in una cache per le condivisioni file di Azure. Consente la sincronizzazione multisito, il ripristino di emergenza rapido e il backup sul cloud. [Altre informazioni](../files/storage-sync-files-planning.md) |
| HDInsight di Azure          | Microsoft.HDInsight        | Consente di effettuare il provisioning del contenuto iniziale del file system predefinito per un nuovo cluster HDInsight. [Altre informazioni](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) |
| Importazione/Esportazione di Azure      | Microsoft.ImportExport     | Consente l'importazione di dati in archiviazione di Azure o l'esportazione di dati da archiviazione di Azure tramite il servizio di importazione/esportazione di archiviazione di Azure. [Altre informazioni](../../import-export/storage-import-export-service.md)  |
| Monitoraggio di Azure            | Microsoft.Insights         | Consente la scrittura di dati di monitoraggio in un account di archiviazione protetto, inclusi i log delle risorse, i log di accesso e di controllo di Azure Active Directory e i log di Microsoft Intune. [Altre informazioni](../../azure-monitor/roles-permissions-security.md) |
| Rete di Azure         | Microsoft.Network          | Archiviare e analizzare i log del traffico di rete, inclusi i servizi Network Watcher e Analisi del traffico. [Altre informazioni](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Abilitare la replica per il ripristino di emergenza di macchine virtuali IaaS di Azure quando si usa un account di archiviazione di origine, di destinazione o della cache abilitato per il firewall.  [Altre informazioni](../../site-recovery/azure-to-azure-tutorial-enable-replication.md) |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Accesso attendibile basato sull'identità gestita assegnata dal sistema

La tabella seguente elenca i servizi che possono avere accesso ai dati dell'account di archiviazione se alle istanze delle risorse di tali servizi viene assegnata l'autorizzazione appropriata. Per concedere l'autorizzazione, è necessario assegnare in modo esplicito [un ruolo di Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) all' [identità gestita assegnata dal sistema](../../active-directory/managed-identities-azure-resources/overview.md) per ogni istanza di risorsa. In questo caso l'ambito di accesso dell'istanza corrisponde al ruolo di Azure assegnato all'identità gestita. 

> [!TIP]
> Il modo consigliato per concedere l'accesso a risorse specifiche consiste nell'usare le regole dell'istanza di risorsa. Per concedere l'accesso a istanze di risorse specifiche, vedere la sezione [concedere l'accesso da istanze di risorse di Azure (anteprima)](#grant-access-specific-instances) di questo articolo.


| Service                        | Nome provider di risorse                 | Scopo            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Gestione API di Azure           | Microsoft.ApiManagement/service        | Consente al servizio gestione API di accedere agli account di archiviazione dietro il firewall usando i criteri. [Altre informazioni](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy) |
| Ricerca cognitiva di Azure         | Microsoft.Search/searchServices        | Consente ai servizi Ricerca cognitiva di accedere agli account di archiviazione per l'indicizzazione, l'elaborazione e l'esecuzione di query. |
| Servizi cognitivi di Azure       | Microsoft. CognitiveService/accounts    | Consente ai servizi cognitivi di accedere agli account di archiviazione. |
| Attività di Registro Azure Container | Microsoft.ContainerRegistry/registries | Le attività del Registro Azure Container possono accedere agli account di archiviazione durante la compilazione di immagini del contenitore. |
| Data factory di Azure             | Microsoft.DataFactory/factories        | Consente l'accesso agli account di archiviazione tramite il runtime di Azure Data Factory. |
| Condivisione dati di Azure               | Microsoft.DataShare/accounts           | Consente l'accesso agli account di archiviazione tramite Condivisione dati. |
| Azure DevTest Labs             | Microsoft.DevTestLab/labs              | Consente l'accesso agli account di archiviazione tramite DevTest Labs. |
| Hub IoT Azure                  | Microsoft.Devices/IotHubs              | Consente la scrittura dei dati di un hub IoT nell'archivio BLOB. [Altre informazioni](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| App per la logica di Azure               | Microsoft.Logic/workflows              | Consente alle app per la logica di accedere agli account di archiviazione. [Altre informazioni](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) |
| Servizio Azure Machine Learning | Microsoft.MachineLearningServices      | Le aree di lavoro autorizzate di Azure Machine Learning scrivono l'output di esperimenti, i modelli e i log nell'archivio BLOB e leggono i dati. [Altre informazioni](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources) |
| Servizi multimediali di Azure           | Microsoft.Media/mediaservices          | Consente l'accesso agli account di archiviazione tramite servizi multimediali. |
| Azure Migrate                  | Microsoft. migrate/migrateprojects      | Consente l'accesso agli account di archiviazione tramite Azure Migrate. |
| Azure Purview                  | Microsoft. competenza/account             | Consente alle competenze di accedere agli account di archiviazione. |
| Rendering remoto di Azure         | Microsoft. MixedReality/remoteRenderingAccounts | Consente l'accesso agli account di archiviazione tramite il rendering remoto. |
| Azure Site Recovery            | Microsoft.RecoveryServices/vaults      | Consente l'accesso agli account di archiviazione tramite Site Recovery. |
| Database SQL di Azure             | Microsoft.Sql                          | Consente di [scrivere](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) i dati di controllo negli account di archiviazione dietro il firewall. |
| Azure Synapse Analytics        | Microsoft.Sql                          | Consente l'importazione e l'esportazione di dati da database SQL specifici usando l'istruzione COPY o la polibase (nel pool dedicato) o la `openrowset` funzione e le tabelle esterne in un pool senza server. [Altre informazioni](../../azure-sql/database/vnet-service-endpoint-rule-overview.md) |
| Analisi di flusso di Azure         | Microsoft.StreamAnalytics              | Consente la scrittura dei dati di un processo di streaming nell'archivio BLOB. [Altre informazioni](../../stream-analytics/blob-output-managed-identity.md) |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces           | Consente l'accesso ai dati in archiviazione di Azure da Azure sinapsi Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Concedi l'accesso a analisi archiviazione

In alcuni casi l'accesso per la lettura di log delle risorse e metriche viene richiesto dall'esterno dei limiti di rete. Quando si configura l'accesso ai servizi attendibili per l'account di archiviazione, è possibile consentire l'accesso in lettura per i file di log, le tabelle di metriche o entrambi creando un'eccezione della regola di rete. Per istruzioni dettagliate, vedere la sezione **gestire le eccezioni** riportata di seguito. Per altre informazioni sull'uso di analisi archiviazione, vedere [usare analisi archiviazione di Azure per raccogliere i dati dei log e delle metriche](./storage-analytics.md). 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Gestire eccezioni

Le eccezioni alle regole di rete possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure v2.

#### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare all'account di archiviazione che si vuole proteggere.

2. Selezionare dal menu impostazioni denominato **rete**.

3. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

4. In **Eccezioni** selezionare le eccezioni da autorizzare.

5. Selezionare **Salva** per applicare le modifiche.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installare [Azure PowerShell](/powershell/azure/install-Az-ps) e [accedere](/powershell/azure/authenticate-azureps).

2. Visualizzare le eccezioni alle regole di rete dell'account di archiviazione.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Configurare le eccezioni alle regole di rete dell'account di archiviazione.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Rimuovere le eccezioni alle regole di rete dell'account di archiviazione.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario la rimozione delle eccezioni non ha alcun effetto.

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [accedere](/cli/azure/authenticate-azure-cli).

2. Visualizzare le eccezioni alle regole di rete dell'account di archiviazione.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Configurare le eccezioni alle regole di rete dell'account di archiviazione.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Rimuovere le eccezioni alle regole di rete dell'account di archiviazione.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario la rimozione delle eccezioni non ha alcun effetto.

---

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni sugli endpoint del servizio Rete di Azure in [Endpoint di servizio](../../virtual-network/virtual-network-service-endpoints-overview.md).

Approfondire gli argomenti relativi alla sicurezza di Archiviazione di Azure in [Guida alla sicurezza di Archiviazione di Azure](../blobs/security-recommendations.md).