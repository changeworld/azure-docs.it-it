---
title: Sviluppo di applicazioni-database di Azure per MySQL
description: Introduce aspetti di progettazione che lo sviluppatore deve considerare quando scrive il codice dell'applicazione per la connessione al database di Azure per MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 45447a26c0455fc5945af8b8e9f7442af7facfbe
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99830686"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Panoramica dello sviluppo di applicazioni per il database di Azure per MySQL 
Questo articolo tratta alcuni aspetti di progettazione che lo sviluppatore deve considerare quando scrive il codice dell'applicazione per la connessione a Database di Azure per MySQL. 

> [!TIP]
> Per un'esercitazione che mostra come creare un server, creare un firewall basato su server, visualizzare le proprietà del server, creare il database, connettersi ed eseguire query usando Workbench e mysql.exe, vedere [Progettare il primo database di Azure per il database MySQL](tutorial-design-database-using-portal.md).

## <a name="language-and-platform"></a>Linguaggio e piattaforma
Sono disponibili esempi di codice per svariati linguaggi di programmazione e piattaforme. È possibile trovare collegamenti a esempi di codice in: [Connectivity libraries used to connect to Azure Database for MySQL](concepts-connection-libraries.md) (Raccolte connessioni utilizzate per connettersi al database di Azure per MySQL)

## <a name="tools"></a>Strumenti
Database di Azure per MySQL usa la versione community di MySQL, compatibile con gli strumenti di gestione comuni di MySQL, ad esempio Workbench o utilità MySQL, ad esempio mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql), [dbForge Studio per MySQL](https://www.devart.com/dbforge/mysql/studio/) e altri. È possibile anche usare il portale di Azure, l'interfaccia della riga di comando di Azure e le API REST per interagire con il servizio di database.

## <a name="resource-limitations"></a>Limiti delle risorse
Database di Azure per MySQL gestisce le risorse disponibili per un server usando due diversi meccanismi: 
- Governance delle risorse.
- Imposizione di limiti.

## <a name="security"></a>Sicurezza
Database di Azure per MySQL fornisce risorse per limitare l'accesso, proteggere i dati, configurare gli utenti e i ruoli e monitorare le attività in un database MySQL.

## <a name="authentication"></a>Authentication
Database di Azure per MySQL supporta l'autenticazione server degli utenti e degli account di accesso.

## <a name="resiliency"></a>Resilienza
Quando si verifica un errore temporaneo durante la connessione a un database MySQL, il codice deve ritentare la chiamata. Per la ripetizione dei tentativi si consiglia di usare una logica backoff, in modo da non sovraccaricare il database SQL con più client che ripetono i tentativi contemporaneamente.

- Esempi di codice: per alcuni esempi di codice che illustrano la logica di ripetizione dei tentativi, vedere gli esempi del linguaggio in uso in [Connectivity libraries used to connect to Azure Database for MySQL](concepts-connection-libraries.md) (Raccolte connessioni utilizzate per connettersi al database di Azure per MySQL)

## <a name="managing-connections"></a>Gestione delle connessioni
Le connessioni di database sono una risorsa limitata, pertanto si consiglia di usare in modo ragionevole le connessioni quando si accede al database MySQL per ottenere prestazioni migliori.
- Accedere al database usando il pool di connessioni o connessioni permanenti.
- Accedere al database tramite una connessione di breve durata. 
- Usare la logica di ripetizione tentativi nell'applicazione al momento del tentativo di connessione, per rilevare gli errori causati dal raggiungimento del massimo livello di connessioni simultanee consentito. Nella logica di ripetizione impostare un breve ritardo e quindi attendere un tempo casuale prima di eseguire altri tentativi di connessione.
