---
title: includere file
description: includere file
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: db13884e3deed8e990d21d82e215a1e837371275
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98052960"
---
## <a name="lifecycle"></a>Ciclo di vita

Nella scheda **ciclo** di vita specificare quando scade l'assegnazione di un utente al pacchetto di accesso. È inoltre possibile specificare se gli utenti possono estenderne le assegnazioni.

1. Nella sezione relativa alla **scadenza** impostare le **assegnazioni dei pacchetti di accesso scadono** a **Data**, **numero di giorni** o **mai**.

    Per la **Data** di scadenza, selezionare una data di scadenza in futuro.

    Per **numero di giorni**, specificare un numero compreso tra 0 e 3660 giorni.

    In base alla selezione, l'assegnazione di un utente al pacchetto di accesso scade in una determinata data, un determinato numero di giorni dopo l'approvazione o mai.

1. Fare clic su **Mostra impostazioni di scadenza avanzate** per visualizzare le impostazioni aggiuntive.

    ![Accedi a impostazioni di scadenza del ciclo di vita del pacchetto](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Per consentire all'utente di estendere le assegnazioni, impostare **Consenti agli utenti di estendere l'accesso** a **Sì**.

    Se nel criterio sono consentite le estensioni, l'utente riceverà un messaggio di posta elettronica di 14 giorni e anche un giorno prima che l'assegnazione del pacchetto di accesso sia impostata come scaduta, richiedendo loro di estendere l'assegnazione. L'utente deve essere ancora nell'ambito dei criteri al momento della richiesta di un'estensione. Inoltre, se i criteri hanno una data di fine esplicita per le assegnazioni e un utente invia una richiesta per estendere l'accesso, la data di estensione nella richiesta deve essere in corrispondenza o prima della scadenza delle assegnazioni, come definito nei criteri usati per concedere all'utente l'accesso al pacchetto di accesso. Se, ad esempio, il criterio indica che le assegnazioni sono impostate per scadere il 30 giugno, l'estensione massima che un utente può richiedere è il 30 giugno.

    Se l'accesso di un utente viene esteso, non sarà in grado di richiedere il pacchetto di accesso dopo la data di estensione specificata (data impostata nel fuso orario dell'utente che ha creato il criterio).

1. Per richiedere l'approvazione per concedere un'estensione, impostare **Richiedi approvazione per concedere l'estensione** a **Sì**.

    Verranno utilizzate le stesse impostazioni di approvazione specificate nella scheda **richieste** .

1. Fare clic su **Avanti** o su **Aggiorna**.
