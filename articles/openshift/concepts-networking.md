---
title: Concetti - Diagramma di rete per Azure Red Hat su OpenShift 4
description: Diagramma di rete e panoramica delle funzionalità di rete di Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: 5d69aacb6e3f25e3414aa446c4c5ae7852cabdfc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720903"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Concetti di rete per Azure Red Hat OpenShift (ARO)

Questa guida offre una panoramica delle funzionalità di rete di Azure Red Hat OpenShift sui cluster OpenShift 4, oltre a un diagramma e a un elenco di endpoint importanti. Per altre informazioni sui principali concetti di rete di OpenShift, vedere la [documentazione relativa alle funzionalità di rete di Azure Red Hat OpenShift 4](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

![Diagramma di rete di Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

Quando si distribuisce Azure Red Hat in OpenShift 4, l'intero cluster è contenuto in una rete virtuale. All'interno di questa rete virtuale, i nodi master e i nodi di lavoro si trovano ognuno nella propria subnet. Ogni subnet usa un servizio di bilanciamento del carico interno e uno pubblico.

## <a name="networking-components"></a>Componenti di rete

Di seguito sono elencati i componenti di rete importanti di un cluster di Azure Red Hat OpenShift.

* **aro-pls**
    * È un endpoint di collegamento privato di Azure, usato dai tecnici responsabili dell'affidabilità dei siti Microsoft e Red Hat per gestire il cluster.
* **aro-internal-lb**
    * Questo endpoint bilancia il carico del traffico verso il server API. Per questo servizio di bilanciamento del carico i nodi master si trovano nel pool back-end.
* **aro-public-lb**
    * Se l'API è pubblica, questo endpoint instrada e bilancia il carico del traffico verso il server API. L'endpoint assegna un IP in uscita pubblico che consente ai master di accedere ad Azure Resource Manager e restituire report sull'integrità del cluster.
* **aro-internal**
    * Questo endpoint bilancia il carico del traffico dei servizi interni. Per questo servizio di bilanciamento del carico i nodi di lavoro si trovano nel pool back-end.
    * Questo servizio di bilanciamento del carico non viene creato per impostazione predefinita. Viene creato quando si crea un servizio di tipo LoadBalancer con le annotazioni corrette. Ad esempio: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **aro-internal-lb**
    * Questo endpoint viene usato per il traffico pubblico. È il percorso del traffico in ingresso quando si crea un'applicazione e una route.
    * Questo servizio di bilanciamento del carico copre anche la connettività Internet in uscita da qualsiasi pod in esecuzione nei nodi di lavoro tramite le regole in uscita di Azure Load Balancer.
        * Attualmente le regole in uscita non sono configurabili. Allocano le porte TCP 1.024 a ogni nodo.
        * DisableOutboundSnat non è configurato nelle regole di bilanciamento del carico, quindi i pod potrebbero ottenere come IP in uscita qualsiasi IP pubblico configurato in questo servizio Azure Load Balancer.
        * Come conseguenza dei due punti precedenti, l'unico modo per aggiungere porte SNAT temporanee consiste nell'aggiungere servizi LoadBalancer pubblici ad ARO.
* **aro-outbound-pip**
    * Questo endpoint funge da IP pubblico (PIP) per i nodi di lavoro.
    * Questo endpoint consente ai servizi di aggiungere a un elenco di indirizzi consentiti un indirizzo IP specifico proveniente da un cluster di Azure Red Hat OpenShift.
* **aro-nsg**
    * Quando si espone un servizio, l'API crea una regola in questo gruppo di sicurezza di rete in modo che il traffico attraversi e raggiunga il piano di controllo e i nodi.
    * Per impostazione predefinita, questo gruppo di sicurezza di rete accetta tutto il traffico in uscita. Attualmente il traffico in uscita può essere limitato solo al piano di controllo di Azure Red Hat OpenShift.
* **aro-controlplane-nsg**
  * Questo endpoint consente al traffico solo di entrare attraverso la porta 6443 per raggiungere i nodi master.
* **Registro Azure Container**
    * È un registro contenitori fornito e usato da Microsoft internamente. Questo registro è di sola lettura e non è destinato all'uso da parte degli utenti di Azure Red Hat OpenShift.
        * Questo registro fornisce le immagini della piattaforma host e i componenti cluster. Un esempio sono i contenitori di monitoraggio o di registrazione.
        * Le connessioni a questo registro si verificano sull'endpoint di servizio (connettività interna tra i servizi di Azure).
        * Per impostazione predefinita, questo registro interno non è disponibile all'esterno del cluster.
* **Collegamento privato**
    * Consente la connettività di rete dal piano di gestione in un cluster per consentire ai tecnici responsabili dell'affidabilità dei siti Microsoft e Red Hat di gestire il cluster.

## <a name="networking-policies"></a>Criteri di rete

* **In ingresso**: i criteri di rete in ingresso sono supportati come parte di [OpenShift SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html). Questi criteri di rete sono abilitati per impostazione predefinita e vengono applicati dagli utenti. Anche se i criteri di rete in ingresso sono conformi a NetworkPolicy v1, i tipi Egress e IPBlock non sono supportati.

* **In uscita**: i criteri di rete in uscita sono supportati tramite la funzionalità del [firewall in uscita](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) di OpenShift. Esiste un solo criterio in ingresso per ogni spazio dei nomi/progetto. I criteri in uscita non sono supportati nello spazio dei nomi "predefinito" e vengono valutati in ordine (dal primo all'ultimo).

## <a name="networking-basics-in-openshift"></a>Nozioni fondamentali sulla rete in OpenShift

OpenShift Software Defined Networking [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) si usa per configurare una rete di overlay tramite Open vSwitch [(OVS)](https://www.openvswitch.org/), un'implementazione di OpenFlow basata sulla specifica CNI (Container Network Interface). SDN supporta diversi plug-in e Criteri di rete è quello usato in Azure Red Hat su OpenShift 4. Tutte le comunicazioni di rete sono gestite da SDN, quindi non sono necessarie route aggiuntive sulle reti virtuali per ottenere la comunicazione da pod a pod.

## <a name="networking--for-azure-red-hat-openshift"></a>Rete per Azure Red Hat OpenShift

Le funzionalità di rete seguenti sono specifiche di Azure Red Hat OpenShift:  
* Gli utenti possono creare il loro cluster ARO in una rete virtuale esistente oppure creare una rete virtuale durante la creazione del cluster ARO.
* I CIDR di rete per pod e servizi sono configurabili.
* I nodi e i master si trovano in subnet diverse.
* Le subnet di rete virtuale di nodi e master devono avere dimensioni minime di /27.
* CIDR Pod predefinito è 10.128.0.0/14.
* CIDR servizio predefinito è 172.30.0.0/16.
* CIDRs di rete di Pod e servizi non devono sovrapporsi ad altri intervalli di indirizzi in uso nella rete e non devono trovarsi all'interno dell'intervallo di indirizzi IP della rete virtuale del cluster.
* La dimensione CIDR del Pod deve essere minima/18. (La rete Pod è indirizzi IP non instradabili e viene usata solo all'interno di OpenShift SDN).
* A ogni nodo è allocata una subnet di dimensioni /23 (512 IP) per i pod. Questo valore non può essere modificato.
* Non è possibile collegare un pod a più reti.
* Non è possibile configurare un indirizzo IP statico in uscita (questa è una funzionalità OpenShift. Per altre informazioni, vedere [Configurazione di indirizzi IP in uscita](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html)).

## <a name="network-settings"></a>Impostazioni di rete

Per i cluster Azure Red Hat OpenShift 4 sono disponibili le impostazioni di rete seguenti:

* **API Visibility** (Visibilità API): imposta la visibilità dell'API quando si esegue il [comando az aro create](tutorial-create-cluster.md#create-the-cluster).
    * "Public" (Pubblica): il server API è accessibile da reti esterne.
    * "Private" (Privata): al server API è assegnato un IP privato dalla subnet master, accessibile solo tramite reti connesse (reti virtuali con peering, altre subnet nel cluster). Verrà creata una zona DNS privata per conto del cliente.
* **Ingress Visibility** (Visibilità in ingresso): imposta la visibilità dell'API quando si esegue il [comando az aro create](tutorial-create-cluster.md#create-the-cluster).
    * Per impostazione predefinita, le route "pubbliche" vengono indirizzate al servizio Load Balancer Standard di Azure (questa impostazione può essere cambiata).
    * Per impostazione predefinita, le route private vengono indirizzate al servizio di bilanciamento del carico interno (questa impostazione può essere modificata).

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete
I gruppi di sicurezza di rete vengono creati nel gruppo di risorse del nodo, che è bloccato per gli utenti. I gruppi di sicurezza di rete vengono assegnati direttamente alle subnet, non nelle schede di interfaccia di rete del nodo. I gruppi di sicurezza di rete non sono modificabili e gli utenti non hanno le autorizzazioni per cambiarli.

Con un server API visibile pubblicamente non è possibile creare gruppi di sicurezza di rete e assegnarli alle schede di interfaccia di rete.

## <a name="domain-forwarding"></a>Inoltro del dominio
Azure Red Hat OpenShift usa CoreDNS. L'inoltro del dominio può essere configurato. Non è possibile usare il DNS personale nelle reti virtuali. Per altre informazioni, vedere la documentazione sull'[uso dell'inoltro DNS](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator).

## <a name="whats-new-in-openshift-45"></a>Novità di OpenShift 4.5

Con il supporto di OpenShift 4.5, in Azure Red Hat OpenShift sono state introdotte alcune modifiche architetturali significative. Queste modifiche si applicano solo ai nuovi cluster creati che eseguono OpenShift 4.5. L'architettura dei cluster esistenti aggiornati a OpenShift 4.5 non verrà cambiata dal processo di aggiornamento. Gli utenti dovranno ricreare i cluster per usare questa nuova architettura.

![Diagramma di rete di Azure Red Hat OpenShift 4.5](./media/concepts-networking/aro-4-5-networking-diagram.png)

Nel diagramma precedente si noteranno alcune modifiche:
* In precedenza, ARO usava due servizi di bilanciamento del carico pubblici, uno per il server API e l'altro per il pool di nodi di lavoro. Con questo aggiornamento dell'architettura, è stato effettuato il consolidamento in un singolo servizio di bilanciamento del carico. 
* Per ridurre la complessità, le risorse con indirizzi IP esterni dedicati sono state rimosse.
* Il piano di controllo di ARO ora condivide lo stesso gruppo di sicurezza di rete dei nodi di lavoro di ARO.

Per altre informazioni su OpenShift 4.5, vedere le [note sulla versione di OpenShift 4.5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul traffico in uscita e sulle funzionalità supportate da Azure Red Hat OpenShift per il traffico in uscita, vedere la documentazione sui [criteri di supporto](support-policies-v4.md).
