---
author: baanders
description: file di inclusione che descrive la limitazione tra tenant con Gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589394"
---
Di conseguenza, le richieste alle API Gemelli digitali di Azure richiedono un utente o un'entità servizio che fa parte dello stesso tenant in cui si trova l'istanza Gemelli digitali di Azure. Per impedire l'analisi dannosa degli endpoint Gemelli digitali di Azure, alle richieste con token di accesso dall'esterno del tenant di origine verrà restituito un messaggio di errore "404 Sub-Domain non trovato". Questo errore verrà *restituito* anche se all'utente o all'entità servizio è stato assegnato un ruolo Gemelli digitali di Azure Data Owner o Gemelli digitali di Azure Data [Reader](../articles/digital-twins/concepts-security.md) Azure AD collaborazione [B2B.](../articles/active-directory/external-identities/what-is-b2b.md) 