---
title: Intervalli di indirizzi IP privati SNAT del firewall di Azure
description: È possibile configurare gli intervalli di indirizzi IP per SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: 0df91680dadbc4ac19299a4df48a585a11f044e8
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072242"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Intervalli di indirizzi IP privati SNAT del firewall di Azure

Il firewall di Azure fornisce SNAT automatici per tutto il traffico in uscita agli indirizzi IP pubblici. Per impostazione predefinita, il firewall di Azure non SNAT con le regole di rete quando l'indirizzo IP di destinazione si trova in un intervallo di indirizzi IP privati per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Le regole dell'applicazione vengono sempre applicate usando un [proxy trasparente](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) qualunque sia l'indirizzo IP di destinazione.

Questa logica funziona correttamente quando si instrada il traffico direttamente a Internet. Tuttavia, se è stato abilitato il [tunneling forzato](forced-tunneling.md), il traffico associato a Internet viene inviato tramite SNAT a uno degli indirizzi IP privati del firewall in AzureFirewallSubnet, nascondendo l'origine dal firewall locale.

Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure invierà il traffico tramite SNAT a uno degli indirizzi IP privati firewall in AzureFirewallSubnet. Tuttavia, è possibile configurare il firewall di Azure in modo che **non** SNAT l'intervallo di indirizzi IP pubblici. Ad esempio, per specificare un singolo indirizzo IP, è possibile specificarlo come segue: `192.168.1.10` . Per specificare un intervallo di indirizzi IP, è possibile specificarlo come segue: `192.168.1.0/24` .

- Per configurare il firewall di Azure in modo che **non SNAT mai** indipendentemente dall'indirizzo IP di destinazione, usare **0.0.0.0/0** come intervallo di indirizzi IP privati. Con questa configurazione, il firewall di Azure non può mai instradare il traffico direttamente a Internet. 

- Per configurare il firewall in modo che sia **sempre** SNAT indipendentemente dall'indirizzo di destinazione, usare **255.255.255.255/32** come intervallo di indirizzi IP privati.

> [!IMPORTANT]
> L'intervallo di indirizzi privato specificato si applica solo alle regole di rete. Attualmente, le regole dell'applicazione SNAT sempre.

> [!IMPORTANT]
> Se si desidera specificare gli intervalli di indirizzi IP privati e mantenere gli intervalli di indirizzi IANA RFC 1918 predefiniti, assicurarsi che nell'elenco personalizzato sia ancora incluso l'intervallo IANA RFC 1918. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Configurare gli intervalli di indirizzi IP privati SNAT-Azure PowerShell

È possibile usare Azure PowerShell per specificare gli intervalli di indirizzi IP privati per il firewall.

### <a name="new-firewall"></a>Nuovo firewall

Per un nuovo firewall, il cmdlet Azure PowerShell è:

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> Per distribuire il firewall `New-AzFirewall` di Azure, è necessario un VNet esistente e un indirizzo IP pubblico. Per una guida alla distribuzione completa, vedere [distribuire e configurare il firewall di Azure con Azure PowerShell](deploy-ps.md) .

> [!NOTE]
> IANAPrivateRanges viene espansa alle impostazioni predefinite correnti nel firewall di Azure mentre gli altri intervalli vengono aggiunti. Per mantenere il valore predefinito di IANAPrivateRanges nella specifica dell'intervallo privato, è necessario che rimanga nella `PrivateRange` specifica, come illustrato negli esempi seguenti.

Per ulteriori informazioni, vedere [New-AzFirewall](/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Firewall esistente

Per configurare un firewall esistente, usare i cmdlet di Azure PowerShell seguenti:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Configurare gli intervalli di indirizzi IP privati SNAT-interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per specificare gli intervalli di indirizzi IP privati per il firewall.

### <a name="new-firewall"></a>Nuovo firewall

Per un nuovo firewall, il comando dell'interfaccia della riga di comando di Azure è:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> La distribuzione del firewall di Azure con il comando dell'interfaccia della riga `az network firewall create` di comando di Azure richiede passaggi di configurazione aggiuntivi per creare indirizzi IP pubblici e configurazione IP Vedere [distribuire e configurare il firewall di Azure con l'interfaccia](deploy-cli.md) della riga di comando di Azure per una distribuzione completa.

> [!NOTE]
> IANAPrivateRanges viene espansa alle impostazioni predefinite correnti nel firewall di Azure mentre gli altri intervalli vengono aggiunti. Per mantenere il valore predefinito di IANAPrivateRanges nella specifica dell'intervallo privato, è necessario che rimanga nella `PrivateRange` specifica, come illustrato negli esempi seguenti.

### <a name="existing-firewall"></a>Firewall esistente

Per configurare un firewall esistente, il comando dell'interfaccia della riga di comando di Azure è:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Configurare gli intervalli di indirizzi IP privati SNAT-modello ARM

Per configurare SNAT durante Distribuzione modelli ARM, è possibile aggiungere quanto segue alla `additionalProperties` proprietà:

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Configurare gli intervalli di indirizzi IP privati SNAT-portale di Azure

È possibile usare la portale di Azure per specificare gli intervalli di indirizzi IP privati per il firewall.

1. Selezionare il gruppo di risorse e quindi selezionare il firewall.
2. Nella pagina **Panoramica** , **intervalli di indirizzi IP privati**, selezionare il valore predefinito **IANA RFC 1918**.

   Verrà visualizzata la pagina **modifica prefissi IP privati** :

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Modificare i prefissi IP privati":::

1. Per impostazione predefinita, è configurato **IANAPrivateRanges** .
2. Modificare gli intervalli di indirizzi IP privati per l'ambiente in uso e quindi selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [tunneling forzato del firewall di Azure](forced-tunneling.md).