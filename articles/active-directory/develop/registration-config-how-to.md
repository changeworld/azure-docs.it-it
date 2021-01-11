---
title: Ottenere gli endpoint per una registrazione dell'app Azure AD
titleSuffix: Microsoft identity platform
description: Come trovare gli endpoint di autenticazione per un'applicazione personalizzata che si sta sviluppando o registrando con Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 3afaf654228511a357461a9e762be0b04acc65c6
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064165"
---
# <a name="how-to-discover-endpoints"></a>Come individuare gli endpoint

È possibile trovare gli endpoint di autenticazione per l'applicazione nel [portale di Azure](https://portal.azure.com).

1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **registrazioni app** e quindi selezionare **endpoint** nel menu in alto.

    Viene visualizzata la pagina **endpoint** che Mostra gli endpoint di autenticazione per il tenant.
    
    Usare l'endpoint che corrisponde al protocollo di autenticazione usato in combinazione con l' **ID applicazione (client)** per creare la richiesta di autenticazione specifica per l'applicazione.

I **cloud nazionali** (ad esempio Azure ad Cina, Germania e governo degli Stati Uniti) hanno il proprio portale di registrazione delle app e Azure ad endpoint di autenticazione. Per altre informazioni, vedere [Panoramica dei cloud nazionali](authentication-national-cloud.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli endpoint nei diversi ambienti di Azure, vedere Panoramica dei [cloud nazionali](authentication-national-cloud.md).
