---
title: Gestione degli account CloudSimple-Azure
description: Informazioni sulla gestione di un account CloudSimple, creato insieme al servizio CloudSimple ed è associato alla sottoscrizione di Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 68ddea6c58bfff5e82643acc947ac1e7de76d699
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88141041"
---
# <a name="account-management-overview"></a>Panoramica della gestione di account

Quando si crea il servizio CloudSimple, viene creato un account in CloudSimple.  L'account è associato alla sottoscrizione di Azure in cui si trova il servizio.  Tutti gli utenti con ruoli **proprietario** e **collaboratore** nella sottoscrizione possono accedere al portale di CloudSimple.  L'ID sottoscrizione e l'ID tenant di Azure associati al servizio CloudSimple sono disponibili nella [pagina account](account.md).

## <a name="additional-alert-emails"></a>Messaggi di posta elettronica di avviso aggiuntivi

È possibile configurare gli ID di posta elettronica in CloudSimple per ricevere gli avvisi:

* Correlato al servizio
* Per l'elaborazione automatica

## <a name="cloudsimple-operator-access"></a>Accesso dell'operatore CloudSimple

È possibile controllare l'accesso al portale di CloudSimple per il personale operativo del servizio.  Il personale operativo del servizio accede al portale quando si invia un ticket di supporto.  Le operazioni del servizio correggeranno eventuali problemi segnalati e le azioni intraprese saranno disponibili nei log di controllo.

## <a name="users"></a>Utenti

Tutti gli utenti che dispongono di un ruolo **proprietario** e **collaboratore** nella sottoscrizione possono accedere al portale di CloudSimple.  Quando si accede al portale, l'utente viene creato nell'account CloudSimple.  È possibile disabilitare l'accesso al portale di CloudSimple per utenti specifici dalla pagina accounts.

## <a name="next-steps"></a>Passaggi successivi

* [Visualizza riepilogo account](account.md)
* [Visualizza elenco utenti](users.md)
