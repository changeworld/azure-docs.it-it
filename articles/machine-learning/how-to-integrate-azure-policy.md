---
title: Controllare e gestire la conformità dei criteri
titleSuffix: Azure Machine Learning
description: Informazioni su come usare i criteri di Azure per usare i criteri predefiniti per Azure Machine Learning per assicurarsi che le aree di lavoro siano conformi ai propri requisiti.
author: aashishb
ms.author: aashishb
ms.date: 03/25/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: f708e2181511da97ecffcd6f1636a2b232b4fbc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544367"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Controllare e gestire Azure Machine Learning usando criteri di Azure

[Criteri di Azure](../governance/policy/index.yml) è uno strumento di governance che consente di garantire che le risorse di Azure siano conformi ai criteri. Con Azure Machine Learning è possibile assegnare i seguenti criteri:

| Criteri | Descrizione |
| ----- | ----- |
| **Chiave gestita dal cliente** | Controllare o imporre se le aree di lavoro devono usare una chiave gestita dal cliente. |
| **Collegamento privato** | Controllare o imporre se le aree di lavoro utilizzano un endpoint privato per comunicare con una rete virtuale. |
| **Endpoint privato** | Configurare la subnet della rete virtuale di Azure in cui deve essere creato l'endpoint privato. |
| **Area DNS privato** | Configurare la zona DNS privata da usare per il collegamento privato. |
| **Identità gestita assegnata dall'utente** | Controllare o imporre se le aree di lavoro utilizzano un'identità gestita assegnata dall'utente. |

I criteri possono essere impostati in ambiti diversi, ad esempio a livello di sottoscrizione o di gruppo di risorse. Per ulteriori informazioni, vedere la [documentazione di criteri di Azure](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Criteri predefiniti

Azure Machine Learning fornisce un set di criteri che è possibile utilizzare per gli scenari comuni con Azure Machine Learning. È possibile assegnare queste definizioni dei criteri alla sottoscrizione esistente o utilizzarle come base per creare definizioni personalizzate. Per un elenco completo dei criteri predefiniti per Azure Machine Learning, vedere [criteri predefiniti per Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Per visualizzare le definizioni dei criteri predefinite correlate ai Azure Machine Learning, attenersi alla procedura seguente:

1. Passare a __criteri di Azure__ nella [portale di Azure](https://portal.azure.com).
1. Selezionare __Definizioni__.
1. Per __tipo__ selezionare _predefinito_ e per __categoria__ selezionare __Machine Learning__.

Da qui è possibile selezionare le definizioni dei criteri per visualizzarle. Durante la visualizzazione di una definizione, è possibile usare il collegamento __assign__ per assegnare il criterio a un ambito specifico e configurare i parametri per il criterio. Per altre informazioni, vedere [assegnare un criterio-portale](../governance/policy/assign-policy-portal.md).

È anche possibile assegnare i criteri tramite [Azure PowerShell](../governance/policy/assign-policy-powershell.md), l'interfaccia della riga di comando di [Azure](../governance/policy/assign-policy-azurecli.md)e i [modelli](../governance/policy/assign-policy-template.md).

## <a name="workspace-encryption-with-customer-managed-key"></a>Crittografia dell'area di lavoro con chiave gestita dal cliente

Controlla se un'area di lavoro deve essere crittografata con una chiave gestita dal cliente o usando una chiave gestita da Microsoft per crittografare le metriche e i metadati. Per altre informazioni sull'uso della chiave gestita dal cliente, vedere la sezione [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) dell'articolo sulla crittografia dei dati.

Per configurare questo criterio, impostare il parametro Effect su __Audit__ o __Deny__. Se è impostato su __Audit__, è possibile creare un'area di lavoro senza una chiave gestita dal cliente e nel log attività viene creato un evento di avviso.

Se il criterio è impostato su __Nega__, non è possibile creare un'area di lavoro a meno che non specifichi una chiave gestita dal cliente. Il tentativo di creare un'area di lavoro senza una chiave gestita dal cliente genera un errore simile a `Resource 'clustername' was disallowed by policy` e crea un errore nel log attività. L'identificatore dei criteri viene restituito anche come parte di questo errore.

## <a name="workspace-should-use-private-link"></a>L'area di lavoro deve usare un collegamento privato

Controlla se un'area di lavoro deve usare il collegamento privato di Azure per comunicare con la rete virtuale di Azure. Per altre informazioni sull'uso del collegamento privato, vedere [configurare un collegamento privato per un'area di lavoro](how-to-configure-private-link.md).

Per configurare questo criterio, impostare il parametro Effect su __Audit__ o __Deny__. Se è impostato su __Audit__, è possibile creare un'area di lavoro senza usare un collegamento privato e nel log attività viene creato un evento di avviso.

Se il criterio è impostato su __Nega__, non è possibile creare un'area di lavoro a meno che non utilizzi un collegamento privato. Il tentativo di creare un'area di lavoro senza un collegamento privato genera un errore. Anche l'errore viene registrato nel log attività. L'identificatore dei criteri viene restituito come parte di questo errore.

## <a name="workspace-should-use-private-endpoint"></a>L'area di lavoro deve usare un endpoint privato

Configura un'area di lavoro per creare un endpoint privato all'interno della subnet specificata di una rete virtuale di Azure.

Per configurare questo criterio, impostare il parametro Effect su __DeployIfNotExists__. Impostare __privateEndpointSubnetID__ sull'ID Azure Resource Manager della subnet.
## <a name="workspace-should-use-private-dns-zones"></a>L'area di lavoro deve usare le zone DNS private

Configura un'area di lavoro per l'uso di una zona DNS privata, sostituendo la risoluzione DNS predefinita per un endpoint privato.

Per configurare questo criterio, impostare il parametro Effect su __DeployIfNotExists__. Impostare __privateDnsZoneId__ sull'ID Azure Resource Manager della zona DNS privata da usare. 

## <a name="workspace-should-use-user-assigned-managed-identity"></a>L'area di lavoro deve usare l'identità gestita assegnata dall'utente

Controlla se un'area di lavoro viene creata utilizzando un'identità gestita assegnata dal sistema (impostazione predefinita) o un'identità gestita assegnata dall'utente. L'identità gestita per l'area di lavoro viene usata per accedere alle risorse associate, ad esempio archiviazione di Azure, Azure Container Registry, Azure Key Vault e applicazione Azure Insights. Per altre informazioni, vedere [usare identità gestite con Azure Machine Learning](how-to-use-managed-identities.md).

Per configurare questo criterio, impostare il parametro Effect su __Audit__, __Deny__ o __disabled__. Se è impostato su __Controlla__, è possibile creare un'area di lavoro senza specificare un'identità gestita assegnata dall'utente. Viene utilizzata un'identità assegnata dal sistema e viene creato un evento di avviso nel log attività.

Se il criterio è impostato su __Nega__, non è possibile creare un'area di lavoro a meno che non si fornisca un'identità assegnata dall'utente durante il processo di creazione. Il tentativo di creare un'area di lavoro senza fornire un'identità assegnata dall'utente genera un errore. Anche l'errore viene registrato nel log attività. L'identificatore dei criteri viene restituito come parte di questo errore.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di Criteri di Azure](../governance/policy/overview.md)
* [Criteri predefiniti per Azure Machine Learning](policy-reference.md)
* [Uso dei criteri di sicurezza con il Centro sicurezza di Azure](../security-center/tutorial-security-policy.md)