---
title: Protezione avanzata degli host di macchine virtuali AKS
description: Informazioni sulla protezione avanzata nel sistema operativo host della macchina virtuale AKS
services: container-service
author: mlearned
ms.topic: article
ms.date: 03/29/2021
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b0866905d0228d2304ebf5c8ef930a629979d2da
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012080"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Protezione avanzata per il sistema operativo host del nodo AKS Agent

Come servizio protetto, Azure Kubernetes Service (AKS) è conforme agli standard SOC, ISO, PCI DSS e HIPAA. Questo articolo illustra la protezione avanzata applicata agli host delle macchine virtuali (VM) AKS. Per altre informazioni sulla sicurezza di AKS, vedere Concetti relativi alla [sicurezza per le applicazioni e i cluster in Azure Kubernetes Service (AKS)](./concepts-security.md).

> [!Note]
> Questo documento è limitato agli agenti Linux in AKS.

I cluster AKS vengono distribuiti nelle VM host, che eseguono un sistema operativo ottimizzato per la sicurezza usato per i contenitori in esecuzione su AKS. Questo sistema operativo host si basa su un'immagine **Ubuntu 16.04. LTS** con una maggiore [protezione avanzata](#security-hardening-features) e ottimizzazioni applicate.

L'obiettivo del sistema operativo host con protezione avanzata consiste nel ridurre la superficie di attacco e l'ottimizzazione per la distribuzione dei contenitori in modo sicuro.

> [!Important]
> Il sistema operativo con protezione avanzata **non** è un benchmark di CIS. Sebbene si sovrappongano ai benchmark CIS, l'obiettivo non è conforme a CIS. L'obiettivo per la protezione avanzata del sistema operativo host è quello di convergere su un livello di protezione coerente con gli standard di sicurezza degli host interni di Microsoft.

## <a name="security-hardening-features"></a>Funzionalità di protezione avanzata

* AKS fornisce un sistema operativo host ottimizzato per la sicurezza per impostazione predefinita, ma non è possibile selezionare un sistema operativo alternativo.

* Azure applica patch giornaliere (incluse le patch di sicurezza) agli host di macchine virtuali AKS. 
    * Alcune di queste patch richiedono un riavvio, mentre altre non lo sono. 
    * L'utente è responsabile della pianificazione dei riavvii dell'host della macchina virtuale AKS in base alle esigenze. 
    * Per istruzioni su come automatizzare l'applicazione di patch di AKS, vedere applicazione di [patch ai nodi AKS](./node-updates-kured.md).

## <a name="what-is-configured"></a>Cosa è configurato

| CIS  | Descrizione controllo|
|---|---|
| 1.1.1.1 |Verificare che il montaggio dei filesystem CRAMFS sia disabilitato|
| 1.1.1.2 |Verificare che il montaggio dei filesystem freevxfs sia disabilitato|
| 1.1.1.3 |Verificare che il montaggio dei filesystem JFFS2 sia disabilitato|
| 1.1.1.4 |Verificare che il montaggio dei filesystem HFS sia disabilitato|
| 1.1.1.5 |Verificare che il montaggio dei filesystem HFS Plus sia disabilitato|
|1.4.3 |Assicurarsi che l'autenticazione sia necessaria per la modalità utente singolo |
|1.7.1.2 |Verificare che il banner di avviso di accesso locale sia configurato correttamente |
|1.7.1.3 |Verificare che il banner di avviso di accesso remoto sia configurato correttamente |
|1.7.1.5 |Verificare che siano configurate le autorizzazioni per/etc/issue |
|1.7.1.6 |Verificare che siano configurate le autorizzazioni per/etc/issue.NET |
|2.1.5 |Verificare che--streaming-Connection-Idle-timeout non sia impostato su 0 |
|3.1.2 |Verifica dell'invio del reindirizzamento pacchetti disabilitato |
|3.2.1 |Verificare che i pacchetti indirizzati di origine non siano accettati |
|3.2.2 |Assicurarsi che i reindirizzamenti ICMP non siano accettati |
|3.2.3 |Assicurarsi che i reindirizzamenti ICMP sicuri non siano accettati |
|3.2.4 |Verificare la registrazione di pacchetti sospetti |
|3.3.1 |Verificare che gli annunci router IPv6 non siano accettati |
|3.5.1 |Verificare che DCCP sia disabilitato |
|3.5.2 |Verificare che SCTP sia disabilitato |
|3.5.3 |Verificare che RDS sia disabilitato |
|3.5.4 |Verificare che TIPC sia disabilitato |
|4.2.1.2 |Verificare che la registrazione sia configurata |
|5.1.2 |Verificare che siano configurate le autorizzazioni per/etc/crontab |
|5.2.4 |Assicurarsi che l'invio SSH X11 sia disabilitato |
|5.2.5 |Verificare che MaxAuthTries SSH sia impostato su un massimo di 4 |
|5.2.8 |Assicurarsi che l'accesso alla radice SSH sia disabilitato |
|5.2.10 |Verificare che PermitUserEnvironment SSH sia disabilitato |
|5.2.11 |Assicurarsi che vengano usati solo gli algoritmi MAX approvati |
|5.2.12 |Verificare che l'intervallo di timeout di inattività SSH sia configurato |
|5.2.13 |Verificare che LoginGraceTime SSH sia impostato su un massimo di un minuto |
|5.2.15 |Verificare che il banner di avviso SSH sia configurato |
|5.3.1 |Verificare che siano configurati i requisiti di creazione della password |
|5.4.1.1 |Verificare che la scadenza della password sia pari o inferiore a 90 giorni |
|5.4.1.4 |Verificare che il blocco di password inattivo sia pari o inferiore a 30 giorni |
|5.4.4 |Verificare che l'utente predefinito umask sia 027 o più restrittivo |
|5.6 |Verificare che l'accesso al comando su sia limitato|

## <a name="additional-notes"></a>Note aggiuntive
 
* Per ridurre ulteriormente la superficie di attacco, alcuni driver del modulo kernel non necessari sono stati disabilitati nel sistema operativo.

* Il sistema operativo con protezione avanzata viene compilato e gestito in modo specifico per AKS e **non** è supportato al di fuori della piattaforma AKS.

## <a name="next-steps"></a>Passaggi successivi  

Per ulteriori informazioni sulla sicurezza di AKS, vedere gli articoli seguenti: 

* [Servizio Azure Kubernetes](./intro-kubernetes.md)
* [Considerazioni sulla sicurezza di AKS](./concepts-security.md)
* [Procedure consigliate di AKS](./best-practices.md)
