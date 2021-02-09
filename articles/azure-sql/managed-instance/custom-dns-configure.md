---
title: DNS personalizzato
titleSuffix: Azure SQL Managed Instance
description: Questo argomento descrive le opzioni di configurazione per un DNS personalizzato con Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: a54907dd3f7b3fbc06033624f14b12de14d9afb9
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831502"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Configurare un DNS personalizzato per Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Istanza gestita SQL di Azure deve essere distribuito in una [rete virtuale di Azure (VNet)](../../virtual-network/virtual-networks-overview.md). In alcuni scenari, ad esempio la posta elettronica del database e i server collegati ad altre istanze di SQL Server nell'ambiente cloud o ibrido, sono richiesti nomi host privati che devono essere risolti dall'Istanza gestita di SQL. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. 

Poiché SQL Istanza gestita usa lo stesso DNS per i propri interventi interni, configurare il server DNS personalizzato in modo che possa risolvere i nomi di dominio pubblici.

> [!IMPORTANT]
> Usare sempre un nome di dominio completo (FQDN) per il server di posta elettronica, per l'istanza di SQL Server e per altri servizi, anche se si trovano all'interno della zona DNS privata. Ad esempio, utilizzare `smtp.contoso.com` per il server di posta elettronica perché `smtp` non risolverà correttamente il problema. La creazione di un server collegato o di una replica che fa riferimento a SQL Server VM all'interno della stessa rete virtuale richiede anche un nome di dominio completo e un suffisso DNS predefinito. Ad esempio: `SQLVM.internal.cloudapp.net`. Per altre informazioni, vedere [Risoluzione dei nomi con l'uso del proprio server DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> L'aggiornamento di server DNS della rete virtuale non influirà immediatamente su SQL Istanza gestita. Per altri dettagli [, vedere How to Synchronize Virtual Network DNS Servers setting on SQL istanza gestita Virtual cluster](synchronize-vnet-dns-servers-setting-on-virtual-cluster.md) .

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere informazioni su [Azure SQL istanza gestita](sql-managed-instance-paas-overview.md).
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [creare un'istanza gestita](instance-create-quickstart.md).
- Per informazioni sulla configurazione di un VNet per un'istanza gestita, vedere la pagina relativa alla [configurazione di VNet per le istanze gestite](connectivity-architecture-overview.md).
