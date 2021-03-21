---
title: Domande frequenti sugli acceleratori di soluzioni IoT - Azure | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti relative agli acceleratori di soluzioni Internet. Include i collegamenti ai repository GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 1fd2b8461bd66c826dc4890c331b740c4703f896
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903990"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Domande frequenti sugli acceleratori di soluzioni IoT

Vedere anche le [domande frequenti specifiche della fabbrica connesse](iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Dove è possibile visualizzare il codice sorgente degli acceleratori di soluzioni?

Il codice sorgente è memorizzato nei repository di GitHub seguenti:

* [Acceleratore di soluzioni di connected factory](https://github.com/Azure/azure-iot-connected-factory)
* [Acceleratore soluzione simulazione dispositivi](https://github.com/Azure/device-simulation-dotnet)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Dove è possibile trovare gli acceleratori della soluzione per il monitoraggio remoto e la manutenzione predittiva?

A partire dal 10 dicembre 2020, i tasti di scelta rapida per il monitoraggio remoto e la manutenzione predittiva sono stati rimossi dal sito [acceleratori della soluzione Azure](https://www.azureiotsolutions.com/Accelerators) Internet e non sono più disponibili per le nuove distribuzioni. I repository GitHub per entrambi gli acceleratori sono stati archiviati. Il codice è ancora disponibile per consentire a tutti gli utenti di accedere, ma i repository non accettano nuovi contributi.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Cosa accade alle distribuzioni di monitoraggio remoto e di manutenzione predittiva esistenti?

Le distribuzioni esistenti non sono interessate dalla rimozione degli acceleratori della soluzione di monitoraggio remoto e di manutenzione predittiva e continueranno a funzionare. Non sono interessati anche i repository con fork. I repository master in GitHub sono stati archiviati.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Ricerca per categorie distribuire l'acceleratore di soluzioni di simulazione del dispositivo?

Per distribuire l'acceleratore di soluzione per la simulazione del dispositivo, vedere il repository GitHub per la [simulazione del dispositivo](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) .

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Dove è possibile reperire informazioni sugli acceleratori di soluzioni rimossi?

Vedere le pagine seguenti nel sito versioni precedenti:

* [Monitoraggio remoto](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Manutenzione predittiva](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Quali SDK è possibile usare per sviluppare i client di dispositivi per gli acceleratori di soluzioni?

È possibile trovare collegamenti agli SDK dei dispositivi IoT per i vari linguaggi (C, .NET, Java, Node.js, Python) nel repository GitHub [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks).

Se si usa il dispositivo DevKit, è possibile trovare risorse ed esempi nel repository GitHub [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk).

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Un amministratore del servizio vuole modificare il mapping della directory tra la sottoscrizione e un tenant di Azure AD specifico. Come completare questa attività

Vedere [Come aggiungere una sottoscrizione esistente alla directory di Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Si vuole modificare un amministratore del servizio o coamministratore quando si accede con un account dell'organizzazione

Vedere l'articolo di supporto [Modifica dell'amministratore del servizio e del coamministratore quando si effettua l'accesso con un account dell'organizzazione](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Perché viene visualizzato un errore simile al seguente? "L'account non ha le autorizzazioni appropriate per creare una soluzione. Rivolgersi all'amministratore account o provare con un account diverso".

Per indicazioni vedere il diagramma seguente:

![Diagramma di flusso delle autorizzazioni](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Se si continua a visualizzare l'errore dopo aver confermato di essere un amministratore globale del tenant Azure AD e un coamministratore della sottoscrizione, l'amministratore dell'account dovrà rimuovere l'utente e assegnare nuovamente le autorizzazioni necessarie in questo ordine. Innanzitutto, dovrà aggiungere l'utente come amministratore globale e quindi aggiungere l'utente come coamministratore della sottoscrizione di Azure. Se i problemi persistono, contattare [Guida e supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Perché viene visualizzato questo errore quando si dispone di una sottoscrizione di Azure? "Per creare soluzioni preconfigurate è necessaria una sottoscrizione di Azure. È possibile creare un account di valutazione gratuito in pochi minuti."

Se si è certi di avere una sottoscrizione di Azure, convalidare il mapping del tenant per la sottoscrizione e verificare che sia selezionato il tenant corretto nell'elenco a discesa. Se si è verificato che il tenant è corretto, seguire il diagramma precedente e verificare il mapping della sottoscrizione e il tenant di Azure AD.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Qual è la differenza tra l'eliminazione di un gruppo di risorse nel portale di Azure e la selezione di Delete in un Solution Accelerator in azureiotsolutions.com?

* Se si elimina l'acceleratore di soluzione in [azureiotsolutions.com](https://www.azureiotsolutions.com/), si eliminano tutte le risorse distribuite al momento della creazione dell'acceleratore di soluzioni. Se sono state aggiunte altre risorse al gruppo, anche queste ultime vengono eliminate.
* Se si elimina il gruppo di risorse nel [portale di Azure](https://portal.azure.com), si eliminano solo le risorse presenti in tale gruppo. È anche necessario eliminare l'applicazione Azure Active Directory associata all'acceleratore di soluzioni.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>È possibile continuare a sfruttare gli investimenti negli acceleratori di soluzioni Azure IoT?

Sì. Qualsiasi soluzione esistente oggi continua a essere inclusa nella sottoscrizione di Azure e il codice sorgente rimane disponibile in GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Di quante istanze dell'hub IoT è possibile eseguire il provisioning in una sottoscrizione?

Per impostazione predefinita, è possibile eseguire il provisioning di [10 hub IoT per ogni sottoscrizione](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). È possibile creare un [ticket di supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare questo limite. Di conseguenza, poiché ogni acceleratore di soluzioni effettua il provisioning di un nuovo hub IoT, è possibile effettuare il provisioning solo di un massimo di 10 acceleratori di soluzioni in una determinata sottoscrizione.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Di quante istanze di Azure Cosmos DB è possibile effettuare il provisioning in una sottoscrizione?

Cinquanta. Anche se è possibile creare un [ticket di supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare questo limite, per impostazione predefinita è possibile effettuare il provisioning solo di 50 istanze di Cosmos DB per ogni sottoscrizione.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>È possibile creare un acceleratore di soluzioni se è disponibile Microsoft Azure per DreamSpark?

> [!NOTE]
> Microsoft Azure per DreamSpark è ora noto come Microsoft Imagine per studenti.

Attualmente non è possibile creare un acceleratore di soluzioni con un account [Microsoft Azure per DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/), ma in un paio di minuti è possibile creare un [account di valutazione gratuito per Azure](https://azure.microsoft.com/free/), che consente di creare un acceleratore di soluzioni.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Come si elimina un tenant di Azure AD?

Vedere il post del blog di Eric Golpe relativo alla [procedura dettagliata di eliminazione di un tenant di Azure AD](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant).

### <a name="next-steps"></a>Passaggi successivi

È anche possibile esplorare alcune altre funzionalità degli acceleratori di soluzioni IoT:

* [Distribuire l'acceleratore di soluzioni di connected factory](quickstart-connected-factory-deploy.md)
* [Sicurezza IoT sin dall'inizio](../iot-fundamentals/iot-security-ground-up.md)