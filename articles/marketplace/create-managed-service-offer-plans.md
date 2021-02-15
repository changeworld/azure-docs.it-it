---
title: Come creare piani per l'offerta di servizio gestito in Azure Marketplace
description: Informazioni su come creare piani per l'offerta di servizi gestiti in Azure Marketplace tramite il centro per i partner Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 9b5526af03bdbefeb54633c49bbd43743555f60b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383229"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Come creare piani per l'offerta di servizio gestito

Le offerte del servizio gestito vendute attraverso Microsoft Commercial Marketplace devono avere almeno un piano. È possibile creare un'ampia gamma di piani con diverse opzioni all'interno della stessa offerta. Questi piani, detti anche SKU, possono differire in termini di versione, monetizzazione o livelli di servizio. Per istruzioni dettagliate sui piani, vedere [piani e prezzi per le offerte per Marketplace commerciali](./plans-pricing.md).

## <a name="create-a-plan"></a>Creare un piano

1. Nella scheda **panoramica piano** dell'offerta in centro per i partner selezionare **+ Crea nuovo piano**.
2. Nella finestra di dialogo visualizzata, in **ID piano**, immettere un ID piano univoco. Usare un massimo di 50 caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. Non è possibile modificare l'ID del piano dopo aver selezionato **Crea**. Questo ID sarà visibile ai clienti.
3. Nella casella **nome piano** immettere un nome univoco per il piano. Usare un massimo di 50 caratteri. Questo nome sarà visibile ai clienti.
4. Selezionare **Crea**.

## <a name="define-the-plan-listing"></a>Definire l'elenco dei piani

Nella scheda **elenco piano** definire il nome e la descrizione del piano come si desidera che vengano visualizzati nel Marketplace commerciale.

1. Nella casella **nome piano** viene visualizzato il nome specificato in precedenza per il piano. È possibile modificarlo in qualsiasi momento. Questo nome verrà visualizzato nel Marketplace commerciale come titolo del piano dell'offerta.
2. Nella casella **Riepilogo piano** specificare una breve descrizione del piano, che può essere usato nei risultati della ricerca nel Marketplace.
3. Nella casella **Descrizione piano** spiegare cosa rende univoco questo piano e diverso da altri piani all'interno dell'offerta.
4. Selezionare **Salva bozza** prima di continuare con la scheda successiva.

## <a name="define-pricing-and-availability"></a>Definire prezzi e disponibilità

L'unico modello di determinazione prezzi disponibile per le offerte del servizio gestito è **Bring your own License (BYOL)**. Ciò significa che i clienti vengono fatturati direttamente per i costi correlati a questa offerta e Microsoft non addebita alcun costo.

È possibile configurare ogni piano in modo che sia visibile a tutti (pubblico) o solo a un gruppo di destinatari specifico (privato).

> [!NOTE]
> I piani privati non sono supportati con le sottoscrizioni stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

> [!IMPORTANT]
> Quando un piano viene pubblicato come pubblico non è possibile modificarlo in un piano privato. Per controllare i clienti che possono accettare l'offerta e delegare le risorse, usare un piano privato. Con un piano pubblico non è possibile limitare la disponibilità a determinati clienti o a un determinato numero di clienti, ma è possibile interrompere completamente la vendita del piano, se necessario. È possibile rimuovere l'accesso a una delega dopo che un cliente accetta un'offerta solo se è stata inclusa un'Autorizzazione con Definizione ruolo impostata su Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti quando è stata pubblicata l'offerta. È anche possibile contattare il cliente e chiedere di rimuovere l'accesso.

## <a name="make-your-plan-public"></a>Rendere pubblico il piano

1. In **visibilità piano** selezionare **pubblico**.
2. Selezionare **Salva bozza**. Per tornare alla scheda Panoramica piano, selezionare **panoramica piano** in alto a sinistra.
3. Per creare un altro piano per questa offerta, selezionare **+ Crea nuovo piano** nella scheda **panoramica piano** .

## <a name="make-your-plan-private"></a>Rendere privato il piano

Si concede l'accesso a un piano privato usando gli ID sottoscrizione di Azure. È possibile aggiungere un massimo di 10 ID sottoscrizione manualmente o fino a 10.000 ID sottoscrizione usando. File CSV.

Per aggiungere manualmente fino a 10 ID sottoscrizione:

1. In **visibilità piano** selezionare **privato**.
2. Immettere l'ID sottoscrizione di Azure del gruppo di destinatari a cui si vuole concedere l'accesso.
3. Facoltativamente, immettere una descrizione di questo gruppo di destinatari nella casella **Descrizione** .
4. Per aggiungere un altro ID, selezionare **Aggiungi ID (max 10)**.
5. Al termine dell'aggiunta degli ID, selezionare **Salva bozza**.

Per aggiungere fino a 10.000 ID sottoscrizione con un. File CSV:

1. In **visibilità piano** selezionare **privato**.
2. Selezionare il collegamento **Esporta destinatari (CSV)** . Verrà scaricato un. File CSV.
3. Aprire. File CSV. Nella colonna **ID** immettere gli ID sottoscrizione di Azure a cui si vuole concedere l'accesso.
4. Nella colonna **Descrizione**   è possibile aggiungere una descrizione per ogni voce.
5. Nella colonna **tipo**   aggiungere **SubscriptionId**   a ogni riga con un ID.
6. Salvare il file come. File CSV.
7. In centro per i partner selezionare il collegamento **Importa destinatari (CSV)** .
8. Nella finestra di dialogo **conferma**   selezionare **Sì**, quindi caricare il. File CSV.
9. Selezionare **Salva bozza**.

## <a name="technical-configuration"></a>Configurazione tecnica

In questa sezione viene creato un manifesto con le informazioni di autorizzazione per la gestione delle risorse del cliente. Queste informazioni sono necessarie per abilitare la [gestione delle risorse delegata di Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

Esaminare i [tenant, i ruoli e gli utenti negli scenari di Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) per comprendere quali ruoli sono supportati e le procedure consigliate per la definizione delle autorizzazioni.

> [!NOTE]
> Gli utenti e i ruoli nelle voci di autorizzazione verranno applicati a tutti i clienti che attivano il piano. Se si vuole limitare l'accesso a un cliente specifico, è necessario pubblicare un piano privato per l'uso esclusivo.

### <a name="manifest"></a>Manifesto

1. In **manifesto** specificare una **versione** per il manifesto. Usare il formato n.n.n (ad esempio, 1.2.5).
2. Immettere l' **ID tenant**. Si tratta di un GUID associato all'ID tenant di Azure Active Directory (Azure AD) dell'organizzazione, ovvero il tenant di gestione da cui verrà eseguito l'accesso alle risorse dei clienti. Se non lo si ha disposizione, è possibile trovarlo passando il puntatore sul nome dell'account nell'angolo in alto a destra del portale di Azure o selezionando **Cambia directory**.

Se si pubblica una nuova versione dell'offerta ed è necessario creare un manifesto aggiornato, selezionare **+ Nuovo manifesto**. Assicurarsi di incrementare il numero di versione rispetto al numero di versione precedente del manifesto.

### <a name="authorizations"></a>Authorizations

Le autorizzazioni definiscono le entità nel tenant di gestione che possono accedere alle risorse e alle sottoscrizioni per i clienti che acquistano il piano. A ognuna di queste entità viene assegnato un ruolo predefinito che concede livelli di accesso specifici.

È possibile creare fino a 20 autorizzazioni per ogni piano.

> [!TIP]
> Nella maggior parte dei casi, è consigliabile assegnare i ruoli a un gruppo utenti o a un'entità servizio di Azure AD anziché a una serie di singoli account utente. In questo modo è possibile aggiungere o rimuovere l'accesso per i singoli utenti senza dover aggiornare e ripubblicare il piano quando cambiano i requisiti di accesso. Quando si assegnano ruoli a gruppi di Azure AD, [il tipo di gruppo deve essere sicurezza e non Office 365](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Per altri consigli, vedere [Tenant, ruoli e utenti negli scenari di Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md).

Per ogni autorizzazione, è necessario fornire le informazioni seguenti. È possibile selezionare **+ Aggiungi autorizzazione** ogni volta che è necessario per aggiungere altri utenti o definizioni del ruolo.

* **ID oggetto AAD**: identificatore Azure ad di un utente, di un gruppo di utenti o di un'applicazione a cui verranno concesse determinate autorizzazioni (definite dalla definizione di ruolo) per le risorse dei clienti.
* **Nome visualizzato dell'oggetto AAD**: un nome descrittivo per aiutare il cliente a comprendere lo scopo di questa autorizzazione. Questo nome verrà visualizzato dal cliente durante la delega delle risorse.
* **Definizione del ruolo**: selezionare uno dei ruoli predefiniti Azure ad disponibili nell'elenco. Questo ruolo determinerà le autorizzazioni che l'utente nel campo **ID entità** avrà sulle risorse dei clienti. Per le descrizioni di questi ruoli, vedere [ruoli predefiniti](../role-based-access-control/built-in-roles.md) e supporto dei ruoli [per il faro di Azure](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).

> [!NOTE]
> Poiché i nuovi ruoli predefiniti applicabili vengono aggiunti ad Azure, diventeranno disponibili qui. Potrebbe verificarsi un certo ritardo prima che vengano visualizzati.

* **Ruoli assegnabili**: questa opzione viene visualizzata solo se è stato selezionato amministratore accesso utenti nella **definizione del ruolo** per l'autorizzazione. In tal caso, è necessario aggiungere qui uno o più ruoli assegnabili. L'utente nel campo **Azure ad ID oggetto** sarà in grado di assegnare questi ruoli alle identità gestite, operazione necessaria per [distribuire i criteri che possono essere corretti](../lighthouse/how-to/deploy-policy-remediation.md). Nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà applicata a questo utente.

> [!TIP]
> Per assicurarsi che sia possibile [rimuovere l'accesso a una delega](../lighthouse/how-to/remove-delegation.md) se necessario, includere un'**Autorizzazione** con **Definizione ruolo** impostata su [Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Se questo ruolo non viene assegnato, le risorse delegate possono essere rimosse solo da un utente che si trova nel tenant del cliente.

Una volta completate tutte le sezioni del piano, è possibile selezionare **+ Crea nuovo piano** per creare piani aggiuntivi. Al termine, selezionare **Salva bozza** prima di continuare.

## <a name="next-steps"></a>Passaggi successivi

* [Rivedi e pubblica](review-publish-offer.md)
