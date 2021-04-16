---
title: Firewall di Azure di indirizzi IP privati SNAT
description: È possibile configurare gli intervalli di indirizzi IP per SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: 91d4d631376c03b668128936f3840ce1119f9b6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482747"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Firewall di Azure di indirizzi IP privati SNAT

Firewall di Azure SNAT automatico per tutto il traffico in uscita verso indirizzi IP pubblici. Per impostazione predefinita, Firewall di Azure non esegue SNAT con le regole di rete quando l'indirizzo IP di destinazione è in un intervallo di indirizzi IP privati per [IANA RFC 1918.](https://tools.ietf.org/html/rfc1918) Le regole dell'applicazione vengono sempre applicate usando [un proxy trasparente](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) indipendentemente dall'indirizzo IP di destinazione.

Questa logica funziona correttamente quando si instradare il traffico direttamente a Internet. Tuttavia, se è stato abilitato il [tunneling](forced-tunneling.md)forzato, il traffico associato a Internet viene inviato a uno degli indirizzi IP privati del firewall in AzureFirewallSubnet, nascondendo l'origine dal firewall locale.

Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure invierà il traffico tramite SNAT a uno degli indirizzi IP privati firewall in AzureFirewallSubnet. Tuttavia, è possibile configurare Firewall di Azure **l'intervallo** di indirizzi IP pubblici non SNAT. Ad esempio, per specificare un singolo indirizzo IP, è possibile specificarlo come segue: `192.168.1.10` . Per specificare un intervallo di indirizzi IP, è possibile specificarlo nel modo seguente: `192.168.1.0/24` .

- Per configurare Firewall di Azure SNAT indipendentemente dall'indirizzo IP di destinazione, usare **0.0.0.0/0/0** come intervallo di indirizzi IP privati.  Con questa configurazione, Firewall di Azure instradare il traffico direttamente a Internet. 

- Per configurare il  firewall in modo che SNAT sia sempre indipendentemente dall'indirizzo di destinazione, usare **255.255.255.255/32** come intervallo di indirizzi IP privati.

> [!IMPORTANT]
> L'intervallo di indirizzi privati specificato si applica solo alle regole di rete. Attualmente, le regole dell'applicazione sono sempre SNAT.

> [!IMPORTANT]
> Se si vogliono specificare intervalli di indirizzi IP privati e mantenere gli intervalli di indirizzi IANA RFC 1918 predefiniti, assicurarsi che l'elenco personalizzato includa ancora l'intervallo IANA RFC 1918. 

È possibile configurare gli indirizzi IP privati SNAT usando i metodi seguenti. È necessario configurare gli indirizzi privati SNAT usando il metodo appropriato per la configurazione. I firewall associati a un criterio firewall devono specificare l'intervallo nei criteri e non usare `AdditionalProperties` .


|Metodo            |Uso delle regole classiche  |Uso dei criteri firewall  |
|---------|---------|---------|
|Portale di Azure     | [Supportati](#classic-rules-3)| [Supportati](#firewall-policy-1)|
|Azure PowerShell     |[Configurare `PrivateRange`](#classic-rules)|attualmente non supportato|
|Interfaccia della riga di comando di Azure|[Configurare `--private-ranges`](#classic-rules-1)|attualmente non supportato|
|Modello ARM     |[configurare `AdditionalProperties` nella proprietà del firewall](#classic-rules-2)|[configurare `snat/privateRanges` nei criteri del firewall](#firewall-policy)|


## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Configurare gli intervalli di indirizzi IP privati SNAT - Azure PowerShell
### <a name="classic-rules"></a>Regole classiche

È possibile usare Azure PowerShell per specificare intervalli di indirizzi IP privati per il firewall.

> [!NOTE]
> La proprietà del firewall `PrivateRange` viene ignorata per i firewall associati a un criterio firewall. È necessario usare la proprietà in come descritto in Configurare intervalli di `SNAT` indirizzi IP privati `firewallPolicies` [SNAT - Modello arm.](#firewall-policy)

#### <a name="new-firewall"></a>Nuovo firewall

Per un nuovo firewall che usa regole classiche, il cmdlet Azure PowerShell è:

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
> La distribuzione Firewall di Azure con `New-AzFirewall` richiede una rete virtuale e un indirizzo IP pubblico esistenti. Vedere [Distribuire e configurare Firewall di Azure usando Azure PowerShell](deploy-ps.md) per una guida alla distribuzione completa.

> [!NOTE]
> IANAPrivateRanges viene espanso alle impostazioni predefinite correnti Firewall di Azure mentre gli altri intervalli vengono aggiunti. Per mantenere l'impostazione predefinita IANAPrivateRanges nella specifica dell'intervallo privato, deve rimanere nella specifica, come `PrivateRange` illustrato negli esempi seguenti.

Per altre informazioni, vedere [New-AzFirewall](/powershell/module/az.network/new-azfirewall).

#### <a name="existing-firewall"></a>Firewall esistente

Per configurare un firewall esistente usando le regole classiche, usare i cmdlet Azure PowerShell seguenti:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Configurare intervalli di indirizzi IP privati SNAT - Interfaccia della riga di comando di Azure
### <a name="classic-rules"></a>Regole classiche

È possibile usare l'interfaccia della riga di comando di Azure per specificare intervalli di indirizzi IP privati per il firewall usando le regole classiche. 

#### <a name="new-firewall"></a>Nuovo firewall

Per un nuovo firewall che usa regole classiche, il comando dell'interfaccia della riga di comando di Azure è:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> La distribuzione Firewall di Azure comando dell'interfaccia della riga di comando di Azure richiede passaggi di configurazione `az network firewall create` aggiuntivi per creare indirizzi IP pubblici e configurazione IP. Per una guida alla distribuzione completa, vedere Distribuire e [configurare Firewall di Azure tramite l'interfaccia](deploy-cli.md) della riga di comando di Azure.

> [!NOTE]
> IANAPrivateRanges viene espanso alle impostazioni predefinite correnti Firewall di Azure mentre gli altri intervalli vengono aggiunti. Per mantenere il valore predefinito IANAPrivateRanges nella specifica dell'intervallo privato, deve rimanere nella specifica, come `private-ranges` illustrato negli esempi seguenti.

#### <a name="existing-firewall"></a>Firewall esistente

Per configurare un firewall esistente usando le regole classiche, il comando dell'interfaccia della riga di comando di Azure è:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Configurare gli intervalli di indirizzi IP privati SNAT - Modello di Arm
### <a name="classic-rules"></a>Regole classiche

Per configurare SNAT durante l'Distribuzione modelli ARM, è possibile aggiungere quanto segue alla `additionalProperties` proprietà :

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```
### <a name="firewall-policy"></a>Criterio firewall

Firewall di Azure associati a un criterio firewall supportano intervalli privati SNAT a partire dalla versione dell'API 2020-11-01. Attualmente, è possibile usare un modello per aggiornare l'intervallo privato SNAT nei criteri del firewall. L'esempio seguente configura il firewall per il **traffico** di rete SNAT sempre:

```json
{ 

            "type": "Microsoft.Network/firewallPolicies", 
            "apiVersion": "2020-11-01", 
            "name": "[parameters('firewallPolicies_DatabasePolicy_name')]", 
            "location": "eastus", 
            "properties": { 
                "sku": { 
                    "tier": "Standard" 
                }, 
                "snat": { 
                    "privateRanges": [255.255.255.255/32] 
                } 
            } 
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Configurare gli intervalli di indirizzi IP privati SNAT - portale di Azure
### <a name="classic-rules"></a>Regole classiche

È possibile usare il portale di Azure per specificare gli intervalli di indirizzi IP privati per il firewall.

1. Selezionare il gruppo di risorse e quindi selezionare il firewall.
2. Nella pagina **Panoramica,** **Intervalli IP privati**, selezionare il valore **predefinito IANA RFC 1918**.

   Verrà **visualizzata la pagina Modifica prefissi IP** privati:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Modificare i prefissi ip privati":::

1. Per impostazione predefinita, **IANAPrivateRanges** è configurato.
2. Modificare gli intervalli di indirizzi IP privati per l'ambiente e quindi selezionare **Salva.**

### <a name="firewall-policy"></a>Criterio firewall

1.  Selezionare il gruppo di risorse e quindi selezionare i criteri del firewall.
2.  Selezionare **Intervalli IP privati (SNAT)** nella **colonna** Impostazioni.

    Per impostazione predefinita, **l'opzione Usa Firewall di Azure comportamento SNAT** dei criteri è selezionata. 
3. Per personalizzare la configurazione SNAT, deselezionare la casella di controllo e in Esegui **SNAT** selezionare le condizioni per eseguire SNAT per l'ambiente.
      :::image type="content" source="media/snat-private-range/private-ip-ranges-snat.png" alt-text="Intervalli IP privati (SNAT)":::


4.   Selezionare **Applica**.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Firewall di Azure tunneling forzato.](forced-tunneling.md)