---
title: Misurazioni utente reale con le pagine Web-gestione traffico di Azure
description: Questo articolo illustra come configurare le pagine Web per inviare misurazioni utente reale a gestione traffico di Azure.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580394"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Come inviare le Misurazioni utente reale a Gestione traffico di Azure usando le pagine Web

È possibile configurare le pagine Web per inviare le Misurazioni utente reale a Gestione traffico ottenendo una chiave delle Misurazioni utente reale (RUM) e incorporando il codice generato nella pagina Web.

## <a name="obtain-a-real-user-measurements-key"></a>Ottenere una chiave delle Misurazioni utente reale

Le misurazioni prese e inviate a gestione traffico dall'applicazione client vengono identificate dal servizio usando una stringa univoca, denominata **chiave misurazioni utente reale (rum)**. È possibile ottenere una chiave RUM usando il portale di Azure, un'API REST, PowerShell o l'interfaccia della riga di comando di Azure.

Per ottenere la chiave RUM tramite il portale di Azure:
1. Da un browser accedere al portale di Azure. Se non si dispone già di un account, è possibile iscriversi per ottenere una versione di valutazione gratuita della durata di un mese.

1. Nella barra di ricerca del portale cercare il nome del profilo di gestione traffico che si vuole modificare e quindi selezionare il profilo di gestione traffico nei risultati visualizzati.

1. Nella pagina profilo di gestione traffico selezionare **misurazioni utente reale** in **Impostazioni**.

1. Selezionare **Genera chiave** per creare una nuova chiave rum.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="Screenshot della creazione della chiave."::: 

   **Figura 1: Generazione della chiave per le Misurazioni utente reale**

1. La pagina Visualizza ora la chiave RUM generata e un frammento di codice JavaScript che deve essere incorporato nella pagina HTML.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="Screenshot del codice JavaScript generato."::: 

    **Figura 2: Chiave delle Misurazioni utente reale e JavaScript per le misurazioni**
 
1. Selezionare il pulsante **copia** per copiare il codice JavaScript. 

> [!IMPORTANT]
> Usare il codice JavaScript generato per eseguire correttamente la funzionalità Misurazioni utente reale. Le modifiche a questo script o agli script usati da Misurazioni utente reale possono causare un comportamento imprevedibile.

## <a name="embed-the-code-to-an-html-web-page"></a>Incorporare il codice in una pagina Web HTML

Una volta ottenuta la chiave RUM, il passaggio successivo consiste nell'incorporare il codice JavaScript copiato in una pagina HTML visitata dagli utenti finali. La modifica dell'HTML può essere eseguita in molti modi e con diversi strumenti e flussi di lavoro. In questo esempio viene illustrato come aggiornare una pagina HTML per aggiungere lo script. È possibile usare queste linee guida per adattarlo al flusso di lavoro della gestione origine HTML.

1. Aprire la pagina HTML in un editor di testo.

1. Incollare il codice JavaScript copiato nell'ultima sezione nella sezione BODY del codice HTML. Il codice copiato è alla riga 8 & 9, vedere la figura 3.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Screenshot del codice JavaScript generato incorporato in una pagina Web."::: 

    **Figura 3: HTML semplice con codice JavaScript delle Misurazioni utente reale incorporato**

1. Salvare il file HTML e ospitarlo in un server Web connesso a Internet.

1. Al successivo rendering di questa pagina in un Web browser, verrà scaricato il codice JavaScript a cui viene fatto riferimento e lo script eseguirà le operazioni di misurazione e creazione di report.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Misurazioni utente reale](traffic-manager-rum-overview.md)
- Informazioni [sul funzionamento di gestione traffico](traffic-manager-overview.md)
- Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico
- Informazioni su come [creare un profilo di Gestione traffico](./quickstart-create-traffic-manager-profile.md)
