---
title: Azure Defender per il servizio app - Vantaggi e funzionalità
description: Informazioni sulle funzionalità di Azure Defender per il servizio app e su come abilitarlo nella sottoscrizione
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ec4ac5d355266a46b33d89fd25c2665493773f5d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100815"
---
# <a name="protect-your-web-apps-and-apis"></a>Proteggi le tue app Web e le tue API

## <a name="prerequisites"></a>Prerequisiti

Il Centro sicurezza è integrato in modo nativo con il servizio app, eliminando la necessità di eseguire la distribuzione e l'onboarding; l'integrazione è trasparente.

Per proteggere il piano di servizio app Azure con Azure Defender per il servizio app, è necessario:

- Un piano di servizio app supportato associato a computer dedicati. I piani supportati sono elencati nella pagina relativa alla [disponibilità](#availability).

- Azure Defender è abilitato nella sottoscrizione, come descritto in [Guida introduttiva: abilitare Azure Defender](enable-azure-defender.md).

    > [!TIP]
    > Facoltativamente, è possibile abilitare singoli piani in Azure Defender, ad esempio Azure Defender per il servizio app.

## <a name="availability"></a>Disponibilità

| Aspetto                       | Dettagli                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stato della versione:               | Disponibilità generale (GA)                                                                                                                                                                      |
| Prezzi:                     | [Azure Defender per il servizio app](azure-defender.md) viene fatturato come indicato nei [prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/)<br>La fatturazione avviene in base alle istanze di calcolo totali in tutti i piani       |
| Piani di servizio app supportati: | [Tutti i piani di servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/) sono supportati tranne [funzioni di Azure nel piano a consumo](../azure-functions/functions-scale.md). |
| Cloud:                      | ![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Quali sono i vantaggi di Azure Defender per il servizio app?

App Azure servizio è una piattaforma completamente gestita per la creazione e l'hosting di API e app Web. Poiché la piattaforma è completamente gestita, non è necessario preoccuparsi dell'infrastruttura. Fornisce funzionalità di gestione e monitoraggio e informazioni operative dettagliate per soddisfare i requisiti di prestazioni, sicurezza e conformità di livello aziendale. Per altre informazioni, vedere [Servizio app di Azure](https://azure.microsoft.com/services/app-service/).

**Azure Defender per il servizio app** sfrutta la scalabilità del cloud per identificare gli attacchi alle applicazioni in esecuzione sul servizio app. Gli utenti malintenzionati mettono alla prova le applicazioni Web per individuare e sfruttare i punti deboli. Prima di essere instradate verso ambienti specifici, le richieste alle applicazioni in esecuzione in Azure passano attraverso diversi gateway, in cui vengono ispezionate e registrate. Questi dati vengono poi usati per identificare exploit e utenti malintenzionati, oltre che per apprendere nuovi modelli che verranno usati successivamente.

Quando si Abilita Azure Defender per il servizio app, si usufruisce immediatamente dei servizi seguenti offerti dal piano di Azure Defender:

- **Sicuro** : il Centro sicurezza valuta le risorse coperte dal piano di servizio app e genera raccomandazioni sulla sicurezza in base ai risultati ottenuti. Usare le istruzioni dettagliate in questi consigli per rafforzare le risorse del servizio app.

- **Rilevamento** : Azure Defender rileva una grande quantità di minacce per le risorse del servizio app monitorando:
    - l'istanza di macchina virtuale in cui è in esecuzione il servizio app e la relativa interfaccia di gestione
    - richieste e risposte inviate da e verso le app del servizio app
    - sandbox e VM sottostanti
    - Log interni del servizio app: disponibili grazie alla visibilità di Azure come provider di servizi cloud

Azure Defender, una soluzione nativa del cloud, può identificare le metodologie di attacco applicabili a più destinazioni. Da un singolo host, ad esempio, sarebbe difficile identificare un attacco distribuito da un piccolo subset di indirizzi IP, ovvero eseguire la ricerca per indicizzazione su endpoint simili in più host.

I dati di log e l'infrastruttura possono essere concatenati: da un nuovo attacco che circola in folle a compromessi nei computer dei clienti. Pertanto, anche se il Centro sicurezza viene distribuito dopo che le vulnerabilità di un'app Web sono già state sfruttate, potrebbe essere in grado di rilevare gli attacchi in atto.


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Quali minacce possono essere rilevate da Azure Defender per il servizio app?

### <a name="threats-by-mitre-attck-tactics"></a>Minacce per MITRE ATT&le tattiche CK

Azure Defender monitora numerose minacce per le risorse del servizio app. Gli avvisi coprono quasi l'elenco completo delle strategie di controllo dell'ATT&CK, dal pre-attacco al comando e al controllo. Azure Defender può rilevare:

- **Minacce di pre-attacco** : Defender può rilevare l'esecuzione di più tipi di scanner di vulnerabilità usati spesso da utenti malintenzionati per sondare le applicazioni per individuare eventuali debolezze.

- Minacce di accesso **iniziali**  -  [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) consente di eseguire questi avvisi che includono l'attivazione di un avviso quando un indirizzo IP dannoso noto si connette all'interfaccia FTP del servizio app Azure.

- **Minacce per l'esecuzione** : Defender può rilevare i tentativi di esecuzione di comandi con privilegi elevati, comandi di Linux in un servizio app di Windows, comportamenti di attacco senza file, strumenti di data mining digitali e molte altre attività di esecuzione di codice sospetto e dannoso.

### <a name="dangling-dns-detection"></a>Rilevamento DNS in sospeso

Azure Defender per il servizio app identifica anche eventuali voci DNS rimanenti nel registrar DNS quando un sito Web del servizio app viene ritirato. queste voci sono note come voci DNS in sospeso. Quando si rimuove un sito Web e non si rimuove il dominio personalizzato dal registrar DNS, la voce DNS punta a una risorsa inesistente e il sottodominio è vulnerabile a un'acquisizione. Azure Defender non esegue l'analisi del registrar DNS per le voci DNS in sospeso *esistenti* ; viene avvisato quando un sito Web del servizio app viene ritirato e il dominio personalizzato (voce DNS) non viene eliminato.

Le acquisizioni dei sottodomini rappresentano una minaccia comune e a gravità elevata per le organizzazioni. Quando un attore di minacce rileva una voce DNS in sospeso, crea il proprio sito all'indirizzo di destinazione. Il traffico destinato al dominio dell'organizzazione viene quindi indirizzato al sito dell'attore minaccia e può utilizzare tale traffico per un'ampia gamma di attività dannose.

La protezione DNS sospesa è disponibile se i domini vengono gestiti con DNS di Azure o un registrar esterno e si applicano al servizio app in Windows e Linux.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Esempio di avviso di Azure Defender relativo a una voce DNS in sospeso individuata. Abilitare Azure Defender per il servizio app per ricevere questo e altri avvisi per l'ambiente." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

Altre informazioni su DNS in sospeso e sul rischio di acquisizione di sottodomini, in [Impedisci le voci DNS in sospeso ed evitare l'acquisizione di sottodomini](../security/fundamentals/subdomain-takeover.md).

Per un elenco completo degli avvisi del servizio app Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureappserv).

> [!NOTE]
> Defender potrebbe non attivare avvisi DNS sospesi se il dominio personalizzato non punta direttamente a una risorsa del servizio app o se Defender non ha monitorato il traffico verso il sito Web perché la protezione DNS sospesa è stata abilitata (perché non ci saranno log per facilitare l'identificazione del dominio personalizzato).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per il servizio app. 

Per i materiali correlati, vedere gli articoli seguenti: 

- Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Trasmettere avvisi a una soluzione SIEM, SOAR o di gestione dei servizi IT](export-to-siem.md).
- Per un elenco degli avvisi di Azure Defender per il servizio app, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureappserv).
- Per altre informazioni sui piani di servizio app, vedere [Piani del servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).
> [!div class="nextstepaction"]
> [Abilitare Azure Defender](enable-azure-defender.md)