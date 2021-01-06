---
title: Come aggiungere un pubblico di anteprima per l'offerta di servizio gestito
description: Informazioni su come aggiungere un pubblico di anteprima per l'offerta del servizio gestito nel centro per i partner Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918276"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>Come aggiungere un pubblico di anteprima per l'offerta di servizio gestito

Questo articolo descrive come configurare un pubblico di anteprima per un'offerta di servizio gestito nel Marketplace commerciale usando il centro per i partner. I destinatari dell'anteprima possono esaminare l'offerta prima che diventi disponibile.

## <a name="define-a-preview-audience"></a>Definire un pubblico di anteprima

Nella pagina **Anteprima destinatari** è possibile definire un gruppo di destinatari limitato che può esaminare l'offerta del servizio gestito prima di pubblicarla in un gruppo più ampio di destinatari del Marketplace. È possibile definire i destinatari dell'anteprima usando gli ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi viene visualizzato dai clienti. È possibile trovare l'ID sottoscrizione di Azure nella pagina **sottoscrizioni** nella portale di Azure.

Aggiungere almeno un ID sottoscrizione di Azure, singolarmente (fino a 10) o caricando un file CSV (fino a 100) per definire chi può visualizzare l'anteprima dell'offerta prima che venga pubblicata in tempo reale. Se l'offerta è già attiva, è comunque possibile definire un pubblico di anteprima per il test degli aggiornamenti all'offerta.

> [!NOTE]
> Un pubblico di *Anteprima* è diverso da un pubblico *privato* . Un pubblico di anteprima è autorizzato ad accedere all'offerta prima che venga pubblicata Live negli archivi online. Possono visualizzare e convalidare tutti i piani, inclusi quelli che saranno disponibili solo per un pubblico privato dopo che l'offerta è stata completamente pubblicata nel Marketplace. È possibile rendere un piano disponibile solo per i destinatari privati. Un pubblico privato (definito nella scheda disponibilità di un piano) dispone di accesso esclusivo a un piano specifico.

## <a name="add-email-addresses-manually"></a>Aggiungere manualmente gli indirizzi di posta elettronica

1. Nella pagina **Anteprima destinatari** aggiungere un singolo ID sottoscrizione di Azure e una descrizione facoltativa nelle caselle fornite.
2. Per aggiungere un altro indirizzo di posta elettronica, selezionare il collegamento **Aggiungi ID (max 10)** .
3. Selezionare **Salva bozza** prima di continuare con la scheda successiva.

## <a name="add-email-addresses-using-a-csv-file"></a>Aggiungere indirizzi di posta elettronica usando un file CSV

1. Nella pagina anteprima destinatari **Anteprima** selezionare il collegamento **Esporta destinatari (CSV)** .
2. Aprire il file CSV. Nella colonna **ID** immettere gli ID sottoscrizione di Azure che si desidera aggiungere ai destinatari dell'anteprima.
3. Nella colonna **Descrizione** è possibile aggiungere una descrizione per ogni voce.
4. Nella colonna **tipo** aggiungere **SubscriptionId** a ogni riga con un ID.
5. Salvare il file come file CSV.
6. Nella pagina **Anteprima destinatari** selezionare il collegamento **Importa destinatari (CSV)** .
7. Nella finestra di dialogo **conferma** selezionare **Sì**, quindi caricare il file CSV.
8. Selezionare **Salva bozza** prima di continuare con la scheda successiva.

## <a name="next-steps"></a>Passaggi successivi

* [Creare piani](create-managed-service-offer-plans.md)
