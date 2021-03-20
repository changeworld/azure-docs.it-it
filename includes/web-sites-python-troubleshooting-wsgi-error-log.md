---
title: includere file
description: includere file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67180751"
---
Se Python rileva un errore durante l'avvio dell'applicazione, verrà restituita solo una semplice pagina di errore, ad esempio "Impossibile visualizzare la pagina perché si è verificato un errore interno del server.").

Per acquisire gli errori delle applicazioni Python:

1. Nel portale di Azure selezionare **Impostazioni** nell'app Web.
2. Nella scheda **Impostazioni** selezionare **Impostazioni dell'applicazione**.
3. In **Impostazioni dell'applicazione** immettere la coppia chiave/valore seguente:
    * Chiave : WSGI_LOG
    * Valore : D:\home\site\wwwroot\logs.txt (immettere il nome file scelto)

Gli errori dovrebbero essere ora visualizzati nel file logs.txt nella cartella wwwroot.
