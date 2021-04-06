---
title: Domande comuni sui cluster Service Fabric gestiti
description: Domande frequenti su Service Fabric cluster gestiti, incluse funzionalità, casi d'uso e scenari comuni.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 02/15/2021
ms.custom: references_regions
ms.openlocfilehash: aa77896ba88d0ffd0a6f94a84603b5f4a1803357
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633088"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Domande frequenti sui cluster gestiti di Service Fabric

Di seguito sono riportate alcune domande frequenti e le risposte per i cluster gestiti Service Fabric (anteprima).

## <a name="general"></a>Generale

### <a name="what-are-service-fabric-managed-clusters"></a>Che cosa sono Service Fabric cluster gestiti?

Service Fabric cluster gestiti sono un'evoluzione del modello di risorse cluster Service Fabric progettato per semplificare la distribuzione e la gestione dei cluster. Un cluster gestito Service Fabric usa il modello di incapsulamento Azure Resource Manager in modo che un utente debba solo definire e distribuire una singola risorsa cluster rispetto alle numerose risorse indipendenti che devono distribuire oggi (set di scalabilità di macchine virtuali, Load Balancer, IP e altro ancora).

### <a name="what-regions-are-supported-in-the-preview"></a>Quali aree sono supportate nell'anteprima?

Le aree supportate per l'anteprima dei cluster gestiti Service fabric includono,,, `centraluseuap` `eastus2euap` `eastasia` `northeurope` , `westcentralus` e `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>È possibile eseguire una migrazione sul posto del cluster di Service Fabric esistente a una risorsa cluster gestita?

No. A questo punto è necessario creare una nuova risorsa cluster Service Fabric per usare il nuovo tipo di risorsa cluster Service Fabric gestito.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Sono previsti costi aggiuntivi per i cluster Service Fabric gestiti?

No. Nessun costo aggiuntivo è associato a un cluster gestito Service Fabric oltre il costo delle risorse di calcolo, archiviazione e rete sottostanti richieste per il cluster.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>C'è un nuovo contratto di servizio introdotto dalla risorsa cluster Service Fabric gestita?

Il contratto di contratto non cambia rispetto al modello di risorsa Service Fabric corrente.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Qual è la differenza tra un cluster SKU Basic e standard?

Un cluster SKU Basic significa che la maggior parte delle configurazioni viene fornita dal provider di risorse Service Fabric. I cluster SKU Basic sono destinati a essere usati per gli ambienti di test e pre-produzione. Un cluster SKU standard consente agli utenti di configurare il cluster in modo specifico per soddisfare le proprie esigenze. Per ulteriori informazioni, vedere [Service Fabric SKU del cluster gestito](./overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="cluster-deployment-and-management"></a>Distribuzione e gestione di cluster

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Quando si eseguono estensioni di script personalizzate nel set di scalabilità di macchine virtuali, è possibile continuare a eseguire questa operazione con una risorsa Service Fabric gestita?

Sì, è possibile specificare le estensioni di macchina virtuale nei tipi di nodo del cluster gestito. Per altre informazioni, vedere [aggiungere un'estensione del set di scalabilità a un tipo di nodo del cluster gestito Service Fabric](how-to-managed-cluster-vmss-extension.md).

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Si vuole avere un servizio di bilanciamento del carico solo interno. è possibile?

No. Non è attualmente possibile avere un servizio di bilanciamento del carico solo interno. È consigliabile bloccare le regole del gruppo di sicurezza di rete (NSG) per bloccare il traffico in ingresso/in uscita indesiderato.

### <a name="can-i-autoscale-my-cluster"></a>È possibile scalare automaticamente il cluster?

La scalabilità automatica non è attualmente disponibile nell'anteprima.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>È possibile distribuire il cluster tra le zone di disponibilità?

I cluster di zone a disponibilità incrociata non sono attualmente disponibili nell'anteprima.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>È possibile scegliere tra aggiornamenti automatici e manuali per il runtime del cluster?

Nell'anteprima tutti gli aggiornamenti del runtime verranno completati automaticamente.

## <a name="applications"></a>Applicazioni

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Esiste un'esperienza di sviluppo locale per i cluster Service Fabric gestiti?

L'esperienza di sviluppo locale rimane invariata rispetto ai cluster di Service Fabric esistenti. Per altre informazioni, vedere [configurare l'ambiente di sviluppo](./service-fabric-get-started.md) per altri dettagli sull'esperienza di sviluppo locale.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>È possibile distribuire le applicazioni come una risorsa Azure Resource Manager?

Sì. È stato aggiunto il supporto per distribuire le applicazioni come risorsa Azure Resource Manager (oltre alla distribuzione tramite PowerShell e l'interfaccia della riga di comando). Per iniziare, vedere [distribuire un'applicazione cluster gestita Service Fabric usando il modello ARM](how-to-managed-cluster-app-deployment-template.md).
