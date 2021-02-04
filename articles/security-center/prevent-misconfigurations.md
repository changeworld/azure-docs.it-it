---
title: Come impedire configurazioni non configurate con il Centro sicurezza di Azure
description: Informazioni su come usare le opzioni ' enforce ' è Deny ' del Centro sicurezza nelle pagine dei dettagli delle raccomandazioni
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558192"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Impedire gli errori di configurazione con i consigli di tipo Imponi/Nega

Gli errori di configurazione della sicurezza sono una delle cause principali degli eventi imprevisti di sicurezza. Il Centro sicurezza consente ora di contribuire a *evitare* gli errori di configurazione nelle nuove risorse per quanto riguarda raccomandazioni specifiche. 

Questa funzionalità può contribuire alla protezione dei carichi di lavoro e alla stabilizzazione del punteggio di sicurezza.

È possibile imporre una configurazione di sicurezza, basata su una raccomandazione specifica, in due modi:

- L'effetto **Deny** di Criteri di Azure consente di interrompere la creazione di risorse non integre
- L'opzione **Imponi** consente di sfruttare i vantaggi dell'effetto **DeployIfNotExist** di Criteri di Azure e di correggere automaticamente eventuali risorse non conformi al momento della creazione

Si trova nella parte superiore della pagina dei dettagli della risorsa per le raccomandazioni di sicurezza selezionate (vedere le [raccomandazioni con le opzioni di negazione/applicazione](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Impedisci la creazione di risorse

1. Aprire la raccomandazione che le nuove risorse devono soddisfare e selezionare il pulsante **Nega** nella parte superiore della pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Pagina di raccomandazione con pulsante nega evidenziato":::

    Viene visualizzato il riquadro configurazione che elenca le opzioni di ambito. 

1. Impostare l'ambito selezionando la sottoscrizione o il gruppo di gestione pertinente.

    > [!TIP]
    > È possibile utilizzare i tre puntini alla fine della riga per modificare una singola sottoscrizione oppure utilizzare le caselle di controllo per selezionare più sottoscrizioni o gruppi, quindi selezionare **modifica per nega**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Impostazione dell'ambito per Deny di criteri di Azure":::


## <a name="enforce-a-secure-configuration"></a>Applicare una configurazione sicura

1. Aprire la raccomandazione per la distribuzione di un modello se le nuove risorse non soddisfano le esigenze e selezionare il pulsante **applica** nella parte superiore della pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Pagina di raccomandazione con il pulsante Applica evidenziato":::

    Viene visualizzato il riquadro Configurazione con tutte le opzioni di configurazione dei criteri. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Imponi opzioni di configurazione":::

1. Impostare l'ambito, il nome dell'assegnazione e altre opzioni rilevanti.

1. Selezionare **Rivedi e crea**.

## <a name="recommendations-with-denyenforce-options"></a>Raccomandazioni con opzioni Deny/enforce

Questi consigli possono essere usati con l'opzione **Deny** :

- L'accesso agli account di archiviazione con configurazioni del firewall e di rete virtuale deve essere limitato
- La cache di Azure per Redis deve risiedere all'interno di una rete virtuale
- Gli account Azure Cosmos DB devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi
- Le aree di lavoro di Azure Machine Learning devono essere crittografate con una chiave gestita dal cliente
- Azure Spring Cloud deve usare l'aggiunta alla rete
- Gli account di servizi cognitivi devono abilitare la crittografia dei dati con una chiave gestita dal cliente (CMK)
- È consigliabile applicare limiti per la CPU e la memoria dei contenitori
- Le immagini del contenitore devono essere distribuite solo da registri attendibili
- I registri contenitori devono essere crittografati con una chiave gestita dal cliente
- È consigliabile evitare i contenitori con escalation dei privilegi
- I contenitori che condividono spazi dei nomi host sensibili devono essere evitati
- I contenitori devono essere in ascolto solo sulle porte consentite
- Per i contenitori deve essere imposto il file system radice non modificabile (di sola lettura)
- Le chiavi degli insiemi di credenziali delle chiavi devono avere una data di scadenza
- I segreti degli insiemi di credenziali delle chiavi devono avere una data di scadenza
- Negli insiemi di credenziali delle chiavi deve essere abilitata la protezione dalla rimozione definitiva
- Negli insiemi di credenziali delle chiavi deve essere abilitata la funzionalità di eliminazione temporanea
- Per i contenitori devono essere imposte le funzionalità Linux con privilegi minimi
- Devono essere abilitate solo connessioni sicure alla Cache Redis
- La sovrascrittura o la disabilitazione del profilo AppArmor dei contenitori deve essere limitata
- I contenitori con privilegi devono essere evitati
- È consigliabile evitare l'esecuzione di contenitori come utente radice
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign
- I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client
- I servizi devono essere in ascolto solo sulle porte consentite
- È consigliabile eseguire la migrazione degli account di archiviazione alle nuove risorse di Azure Resource Manager
- Gli account di archiviazione devono limitare l'accesso alla rete usando regole di rete virtuale
- L'utilizzo della rete host e delle porte deve essere limitato
- L'utilizzo dei montaggi dei volumi HostPath dei pod deve essere limitato a un elenco noto per limitare l'accesso ai nodi da contenitori compromessi
- È consigliabile che il periodo di validità dei certificati archiviati in Azure Key Vault non sia superiore a 12 mesi
- È consigliabile eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager
- Web Application Firewall (WAF) deve essere abilitato per il gateway applicazione
- Il Web Application Firewall (WAF) deve essere abilitato per il servizio Azure front door Service

Questi consigli possono essere usati con l'opzione **Imponi** :

- È consigliabile abilitare il controllo in SQL Server
- La soluzione Backup di Azure deve essere abilitata per le macchine virtuali
- Azure Defender per SQL deve essere abilitato nei server SQL
- I log di diagnostica devono essere abilitati in Analisi di flusso di Azure
- È consigliabile abilitare i log di diagnostica negli account Batch
- È consigliabile abilitare i log di diagnostica Data Lake Analytics
- È consigliabile abilitare i log di diagnostica in Hub eventi
- I log di diagnostica in Key Vault devono essere abilitati
- È consigliabile abilitare i log di diagnostica in App per la logica
- È consigliabile abilitare i log di diagnostica nei servizi di ricerca
- È consigliabile abilitare i log di diagnostica nel bus di servizio