---
title: Benchmark di sicurezza di Azure V2-risposta agli eventi imprevisti
description: Risposta agli eventi imprevisti di benchmark di sicurezza di Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 002/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 40a88349040482b3325e63d7035dea35df444cdb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698587"
---
# <a name="security-control-v2-incident-response"></a>Controllo di sicurezza V2: risposta agli eventi imprevisti

La risposta agli eventi imprevisti riguarda i controlli nel ciclo di vita della risposta agli eventi imprevisti, ovvero preparazione, rilevamento e analisi, contenimento e attività post-evento imprevisto. Questo include l'uso di servizi di Azure, come il Centro sicurezza di Azure e Sentinel, per automatizzare il processo di risposta agli eventi imprevisti.

Per visualizzare i criteri di Azure predefiniti applicabili, vedere la pagina relativa ai [Dettagli del benchmark di sicurezza di Azure conformità normativa: risposta agli eventi imprevisti](../../governance/policy/samples/azure-security-benchmark#incident-response)

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Assicurarsi che l'organizzazione disponga di processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato questi processi per Azure e li eserciti regolarmente per garantire la conformità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparazione agli eventi imprevisti](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparazione: configurare la notifica dell'evento imprevisto

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| IR-2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel centro sicurezza di Azure. Le informazioni di contatto consentono a Microsoft di contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparazione agli eventi imprevisti](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Rilevamento e analisi: creare eventi imprevisti basati su avvisi di alta qualità

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Assicurarsi di disporre di un processo per creare avvisi di alta qualità e misurare la qualità degli avvisi. Questo consente di apprendere le lezioni dagli eventi imprevisti passati e di assegnare la priorità agli avvisi per gli analisti, in modo da non sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, alle origini di community convalidate e a strumenti progettati per generare e pulire gli avvisi fondendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità per molte risorse di Azure. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione per contribuire a individuare i rischi per le risorse di Azure. È possibile esportare avvisi e raccomandazioni manualmente o in modo continuativo.

- [Come configurare l'esportazione](../../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparazione agli eventi imprevisti](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Rilevamento e analisi: esaminare un evento imprevisto

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Assicurarsi che gli analisti possano eseguire query e utilizzare origini dati diverse durante l'analisi di potenziali eventi imprevisti, per creare una panoramica completa di ciò che è successo. È necessario raccogliere vari log per tenere traccia delle attività di un possibile utente malintenzionato attraverso la kill chain per evitare punti ciechi. Assicurarsi anche che le informazioni dettagliate e le nozioni apprese vengano acquisite per poter essere sfruttate da altri analisti e per riferimenti cronologici futuri.

Le origini dati per l'analisi includono le origini di registrazione centralizzate che sono già state raccolte dai servizi inclusi nell'ambito e dai sistemi in esecuzione, ma possono includere anche:

- Dati di rete: usare i log dei flussi dei gruppi di sicurezza di rete, Azure Network Watcher e Monitoraggio di Azure per acquisire i log dei flussi di rete e altre informazioni di analisi. 

- Snapshot dei sistemi in esecuzione: 

    - Usare la funzionalità snapshot macchina virtuale di Azure per creare uno snapshot del disco del sistema in esecuzione. 

    - Usare la funzionalità di dump della memoria nativa del sistema operativo per creare uno snapshot della memoria del sistema in esecuzione.

    - Usare la funzionalità snapshot dei servizi di Azure o la funzionalità del software in uso per creare snapshot dei sistemi in esecuzione.

Azure Sentinel fornisce analisi approfondite dei dati in qualsiasi origine di log e un portale di gestione dei casi per gestire l'intero ciclo di vita degli eventi imprevisti. Le informazioni di intelligence durante un'analisi possono essere associate a un evento imprevisto a scopo di rilevamento e creazione di report. 

- [Creare uno snapshot del disco di un computer Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Creare uno snapshot del disco di un computer Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Raccolta delle informazioni di diagnostica e del dump della memoria da parte del supporto tecnico di Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Esaminare gli eventi imprevisti con Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparazione agli eventi imprevisti](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Rilevamento e analisi: assegnare la priorità agli eventi imprevisti

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| IR-5 | 19.8 | CA-2, IR-4 |

Fornire il contesto agli analisti su cui si concentrano prima gli eventi imprevisti in base alla gravità dell'avviso e alla sensibilità degli asset. 

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità del Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di attendibilità con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha generato l'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparazione agli eventi imprevisti](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenimento, eliminazione e ripristino: automatizzare la gestione degli eventi imprevisti

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatizzare le attività ripetitive manuali per velocizzare il tempo di risposta e ridurre il carico sugli analisti. L'esecuzione delle attività manuali richiede più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che un analista può gestire. Le attività manuali rendono inoltre il lavoro degli analisti più faticoso, aumentando il rischio di errori umani che causano ritardi e compromettendo la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. Usare le funzionalità di automazione dei flussi di lavoro nel Centro sicurezza di Azure e in Azure Sentinel per attivare automaticamente le azioni o eseguire un playbook per rispondere agli avvisi di sicurezza in ingresso. Il playbook esegue azioni come l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione dei flussi di lavoro nel Centro sicurezza](../../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel Centro sicurezza di Azure](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparazione agli eventi imprevisti](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)