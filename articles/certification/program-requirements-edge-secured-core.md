---
title: Sicurezza perimetrale-requisiti di certificazione Core
description: Edge protetto-requisiti del programma di certificazione principale
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 5bb02f939bb63fd1c6365fd4570996f09119e958
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166905"
---
# <a name="azure-certified-device---edge-secured-core-preview"></a>Azure Certified Device-Edge protetto-Core (anteprima) #

## <a name="edge-secured-core-certification-requirements"></a>Requisiti di certificazione Secured-Core Edge ##

Questo documento descrive le funzionalità e i requisiti specifici del dispositivo che verranno soddisfatti per completare la certificazione ed elencare un dispositivo nel catalogo dei dispositivi Azure Internet con l'etichetta Edge Secured-core.

### <a name="program-purpose"></a>Scopo del programma ###
Edge protetto-core è una certificazione incrementale nel programma Azure Certified Device per i dispositivi Internet per le cose che eseguono un sistema operativo completo, ad esempio Linux o Windows 10. questo programma consente ai partner di dispositivi di distinguere i loro dispositivi soddisfacendo un set aggiuntivo di criteri di sicurezza. I dispositivi che soddisfano questo criterio abilitano le promesse seguenti:
1. Identità dispositivo basata su hardware 
2. In grado di applicare l'integrità del sistema 
3. Rimane aggiornato ed è gestibile in remoto
4. Fornisce protezione dati inattivi
5. Fornisce protezione dati in transito
6. Agente sicurezza integrato e protezione avanzata
### <a name="requirements"></a>Requisiti ###

---
|Nome|SecuredCore. built-in. sicurezza|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è assicurarsi che i dispositivi possano segnalare informazioni di sicurezza ed eventi inviando i dati ad Azure Defender per tutto.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida |Il dispositivo deve generare avvisi e registri di sicurezza. Messaggi di avviso e di log del dispositivo nel centro sicurezza di Azure.<ol><li>Scaricare e distribuire l'agente di sicurezza da GitHub</li><li>Convalidare il messaggio di avviso da Azure Defender per l'it.</li></ol>|
|Risorse|[Azure docs per tutto il Defender](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|Nome|SecuredCore. Encryption. storage|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Scopo del test per verificare che i dati sensibili possano essere crittografati in un archivio non volatile.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per assicurarsi che la crittografia di archiviazione sia abilitata e che l'algoritmo predefinito sia XTS-AES, con lunghezza della chiave 128 bit o superiore.|
|Risorse||

---
|Nome|SecuredCore. hardware. SecureEnclave|
|:---|:---|
|Stato|Facoltativo|
|Descrizione|Scopo del test per convalidare l'esistenza di un enclave sicuro e che l'enclave è accessibile da un agente protetto.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per assicurarsi che l'agente sicurezza di Azure possa comunicare con l'enclave sicura|
|Risorse|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Nome|SecuredCore. hardware. Identity|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è verificare che l'identificazione del dispositivo sia radicata nell'hardware.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per assicurarsi che il dispositivo disponga di un TPM e che sia possibile eseguirne il provisioning tramite l'hub di Internet.|
|Risorse|[Configurare il provisioning automatico con DPS](../iot-dps/quick-setup-auto-provision.md)|

---
|Nome|SecuredCore. Update|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è la convalida del dispositivo in grado di ricevere e aggiornare il firmware e il software.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Conferma del partner che è stato in grado di inviare un aggiornamento al dispositivo tramite Microsoft Update, aggiornamento del dispositivo di Azure o altri servizi approvati.|
|Risorse|[Aggiornamento del dispositivo per l'hub Internet](../iot-hub-device-update/index.yml)|

---
|Nome|SecuredCore.Manageability.Configuration|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è di convalidare i dispositivi che supportano la gestione della sicurezza remota.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per garantire che il dispositivo supporti la possibilità di essere gestibile in remoto e in particolare configurazioni di sicurezza. E lo stato viene segnalato nuovamente a hub Internet/Azure Defender per l'it.|
|Risorse||

---
|Nome|SecuredCore. gestibilità. Reset|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo di questo test è di convalidare il dispositivo rispetto a due casi d'uso: a) la possibilità di eseguire una reimpostazione (rimuovere i dati utente, rimuovere le configurazioni utente), b) ripristinare il dispositivo in modo che sia noto il caso di un aggiornamento che causa problemi.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite una combinazione di set di strumenti e la documentazione inviata che il dispositivo supporta questa funzionalità. Il produttore del dispositivo può determinare se implementare queste funzionalità per supportare la reimpostazione remota o solo la reimpostazione locale.|
|Risorse||

---
|Nome|SecuredCore. Updates. Duration|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo di questo criterio è garantire che il dispositivo rimanga protetto.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale|
|Convalida|Dall'invio dei dispositivi certificati verrà richiesto di tenere aggiornati i dispositivi per 60 mesi dalla data di invio. Le specifiche disponibili per gli acquirenti e i dispositivi in qualche modo dovrebbero indicare la durata per cui il software verrà aggiornato.|
|Risorse||

---
|Nome|SecuredCore. Policy. VULN. Divulgation|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo di questo criterio è garantire che esista un meccanismo per la raccolta e la distribuzione di report di vulnerabilità nel prodotto.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale|
|Convalida|La documentazione relativa al processo di invio e ricezione dei report sulle vulnerabilità per i dispositivi certificati verrà rivista.|
|Risorse||

---
|Nome|SecuredCore. Policy. VULN. Fix|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo di questo criterio è garantire che le vulnerabilità di livello elevato/critico (con CVSS 3,0) vengano risolte entro 180 giorni dalla correzione disponibile.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale|
|Convalida|La documentazione relativa al processo di invio e ricezione dei report sulle vulnerabilità per i dispositivi certificati verrà rivista.|
|Risorse||


---
|Nome|SecuredCore. Encryption. TLS|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è di convalidare il supporto per le versioni di TLS e i pacchetti di crittografia richiesti.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per assicurarsi che il dispositivo supporti una versione minima di TLS 1,2 e supporti i seguenti pacchetti di crittografia TLS richiesti.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Risorse| [Supporto TLS nell'hub IoT](../iot-hub/iot-hub-tls-support.md) <br /> [Pacchetti di crittografia TLS in Windows 10](https://docs.microsoft.com/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|Nome|SecuredCore. Protection. SignedUpdates|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è verificare che gli aggiornamenti debbano essere firmati.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per assicurarsi che gli aggiornamenti al sistema operativo, ai driver, al software dell'applicazione, alle librerie, ai pacchetti e al firmware non vengano applicati a meno che non sia firmato e convalidato correttamente.
|Risorse||

---
|Nome|SecuredCore. firmware. SecureBoot|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test consiste nel convalidare l'integrità di avvio del dispositivo.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per garantire la convalida delle firme del firmware e del kernel a ogni avvio del dispositivo. <ul><li>UEFI: avvio protetto abilitato</li><li>Uboot: l'avvio verificato è abilitato</li></ul>|
|Risorse||

---
|Nome|SecuredCore. Protection. CodeIntegrity|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo di questo test è verificare che l'integrità del codice sia disponibile in questo dispositivo.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per assicurarsi che l'integrità del codice sia abilitata. </br> Windows: HVCI obbligatoria </br> Linux: DM-verità e IMA|
|Risorse||

---
|Nome|SecuredCore. Protection. NetworkServices|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è verificare che le applicazioni che accettano input dalla rete non siano in esecuzione con privilegi elevati.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per assicurarsi che i servizi che accettano connessioni di rete non siano in esecuzione con privilegi di sistema o radice.|
|Risorse||

---
|Nome|SecuredCore. Protection. Baselines|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è verificare che il sistema sia conforme a una configurazione di sicurezza di base.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per assicurarsi che siano stati eseguiti i benchmark delle configurazioni di sistema di Defender.|
|Risorse| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Nome|SecuredCore. firmware. Protection|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è garantire che il dispositivo abbia mitigazioni adeguate dalle minacce alla sicurezza del firmware.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per verificare che sia protetto da minacce alla sicurezza del firmware tramite uno degli approcci seguenti: <ul><li>DRTM + mitigazioni della modalità di gestione UEFI</li><li>DRTM + protezione avanzata modalità di gestione UEFI</li><li>FW approvato che esegue la protezione avanzata del firmware SRTM + Runtime</li></ul> |
|Risorse| https://trustedcomputinggroup.org/ |

---
|Nome|SecuredCore. firmware. attestazione|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è garantire che il dispositivo possa attestare in remoto il servizio di attestazione Microsoft Azure.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per assicurarsi che i log di avvio della piattaforma e le misurazioni delle attività di avvio possano essere raccolti e attestati in remoto per il servizio di attestazione Microsoft Azure.|
|Risorse| [Attestazione di Microsoft Azure](../attestation/index.yml) |

---
|Nome|SecuredCore. hardware. MemoryProtection|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è verificare che DMA non sia abilitato nelle porte accessibili esternamente.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Se nel dispositivo sono presenti porte esterne compatibili con DMA, per verificare che IOMMU o SMMU sia abilitata e configurata per tali porte.|
|Risorse||

---
|Nome|SecuredCore. Protection. debug|
|:---|:---|
|Stato|Obbligatoria|
|Descrizione|Lo scopo del test è verificare che la funzionalità di debug nel dispositivo sia disabilitata.|
|Disponibilità di destinazione|2021|
|Si applica a|Qualsiasi dispositivo|
|OS|Agnostico|
|Tipo di convalida|Manuale/strumenti|
|Convalida|Il dispositivo deve essere convalidato tramite il set di strumenti per assicurarsi che la funzionalità di debug richieda l'autorizzazione per abilitare.|
|Risorse||
