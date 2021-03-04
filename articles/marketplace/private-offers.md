---
title: Offerte private in Microsoft Commercial Marketplace
description: Offerte private nel Marketplace commerciale Microsoft per gli editori di app e servizi.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 02/22/2021
ms.openlocfilehash: 7c02c41379cfb886fd7e6f84486d815d3cdbfb5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097177"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Offerte private in Microsoft Commercial Marketplace

Le offerte private, denominate anche piani privati, consentono agli editori di creare piani visibili solo ai clienti di destinazione. Questo articolo illustra le opzioni e i vantaggi offerti dalle offerte private.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Sbloccare le offerte aziendali con offerte private

Grazie alla creazione di offerte private, gli editori possono offrire privatamente soluzioni personalizzate ai clienti mirati con le funzionalità richieste dalle organizzazioni:

- I *prezzi concordati* consentono agli editori di estendere gli sconti e i prezzi fuori listino delle offerte disponibili pubblicamente.
- I *termini e condizioni privati* consentono agli editori di personalizzare termini e condizioni per un cliente specifico.
- Le *configurazioni specializzate* consentono agli editori di adattare le proprie macchine virtuali, le applicazioni di Azure e software as a Service (SaaS) alle esigenze di un singolo cliente. Questa opzione consente inoltre agli editori di fornire l'accesso in anteprima alle nuove funzionalità del prodotto, prima di avviarle a tutti i clienti.

Le offerte private consentono agli editori di sfruttare la scalabilità e la disponibilità globale di un Marketplace pubblico, con la flessibilità e il controllo necessari per negoziare e distribuire le configurazioni e le offerte personalizzate. Le aziende possono ora acquistare e vendere in modi previsti.

## <a name="create-private-offers-using-plans"></a>Creazione di offerte private mediante piani

Per *le offerte nuove o esistenti con i piani*, gli editori possono creare con facilità nuove varianti private creando nuovi piani (noti in precedenza come SKU) e contrassegnando questi ultimi come privati. Ogni offerta può avere fino a 45 piani privati.

<!--- [Private SKUs]() --->

I piani privati sono disponibili per i tipi di offerta seguenti:

- Macchina virtuale di Azure
- Applicazione Azure (implementato come modelli di soluzione o applicazioni gestite)
- Servizio gestito
- Offerte SaaS

I piani privati sono componenti di un'offerta e sono visibili e acquistabili dai clienti di destinazione. I piani privati sono visibili e acquistabili dai clienti di destinazione. I piani privati possono essere resi disponibili per i clienti sia in Azure globale che in Azure per enti pubblici.

I piani privati possono riutilizzare le immagini di base e/o i metadati dell'offerta già pubblicati per un piano pubblico. Questa opzione consente agli editori di creare più varianti private di un'offerta pubblica senza dover pubblicare più versioni della stessa immagine di base e offrire metadati. Solo per la macchina virtuale di Azure e l'applicazione Azure, quando un piano privato condivide un'immagine di base con un piano pubblico, qualsiasi modifica apportata all'immagine di base dell'offerta viene propagata in tutti i piani pubblici e privati usando tale immagine di base.

Per le *nuove offerte che includono solo piani privati*, gli editori possono creare le proprie offerte come qualsiasi altra offerta e quindi contrassegnare i piani come privati. Le offerte che hanno solo piani privati non saranno individuabili o accessibili in [portale di Azure](https://azure.microsoft.com/features/azure-portal/) da parte dei clienti che non sono associati all'offerta.

>[!NOTE]
>Un'offerta che contiene solo piani privati non sarà visibile nel Marketplace pubblico di Azure o AppSource.

## <a name="target-customers-with-private-offers"></a>Clienti di destinazione con offerte private

Per le offerte private sia nuove che esistenti, gli editori possono rivolgersi a determinati clienti usando gli identificatori di sottoscrizioni. Per le offerte di macchine virtuali di Azure, applicazione Azure e servizi gestiti, gli editori possono vincolare la disponibilità di un piano privato a un singolo ID sottoscrizione di Azure o caricare un volume condiviso cluster con un massimo di 10.000 ID sottoscrizione di Azure. Per le offerte SaaS, gli editori possono associare un ID tenant Azure Active Directory per limitare la disponibilità di un piano privato, usando l'approccio di caricamento manuale o CSV.

Una volta che un'offerta è stata certificata e pubblicata, i clienti possono essere aggiornati o rimossi dal piano usando la funzionalità Sincronizza sottoscrizioni private. Questa funzionalità consente agli editori di aggiornare in modo rapido e semplice l'elenco dei clienti a cui viene presentato il piano privato senza certificare o pubblicare di nuovo l'offerta.

## <a name="deploying-private-offers"></a>Distribuzione di offerte private

Una volta effettuato l'accesso al portale di Azure, i clienti possono seguire questa procedura per selezionare le offerte private.

1. Accedere a [portale di Azure](https://ms.portal.azure.com/).
1. In **servizi di Azure** selezionare **Crea una risorsa**.
1. Nella **nuova** pagina, accanto ad **Azure Marketplace**, selezionare **Visualizza tutto**. Verrà visualizzata la pagina Marketplace.
1. Nel percorso di spostamento a sinistra selezionare **offerte private**.

> [!NOTE]
> Le offerte private sono individuabili solo in [portale di Azure](https://azure.microsoft.com/features/azure-portal/). Non vengono visualizzate in [Microsoft AppSource](https://appsource.microsoft.com/) o in [Azure Marketplace](https://azuremarketplace.microsoft.com). Per ulteriori informazioni sulla pubblicazione nei diversi negozi online del Marketplace commerciale, vedere [Introduzione alle opzioni di elenco](./determine-your-listing-type.md).

Anche le offerte private verranno visualizzate nei risultati della ricerca e potranno essere distribuite tramite la riga di comando e i modelli di Azure Resource Manager, come qualsiasi altra offerta.

[![[Offerte private visualizzate nei risultati della ricerca.]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

Le offerte private verranno visualizzate anche nei risultati della ricerca. È sufficiente cercare il badge **privato** .

>[!Note]
>Le offerte private non sono supportate con le sottoscrizioni stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
