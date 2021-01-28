---
title: Opzioni di sicurezza per hive in Azure HDInsight
description: Opzioni di sicurezza per hive nei cluster standard ed ESP.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 1189a320d0dc700756c9f7664d0a6303be5dab51
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940893"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Opzioni di sicurezza per hive in Azure HDInsight

Questo documento descrive le opzioni di sicurezza consigliate per hive in HDInsight. Queste opzioni possono essere configurate tramite Ambari.

![' Opzioni di sicurezza per hive '](./media/hdinsight-security-options-for-hive/security-options-hive.png "Opzioni di sicurezza per hive")

## <a name="hiveserver2-authentication"></a>Autenticazione HiveServer2

Per i cluster standard, l'impostazione consigliata per l'autenticazione HiveServer2 è quella predefinita, ovvero None. Per abilitare l'autenticazione, è consigliabile eseguire l'aggiornamento a un cluster [ESP](../domain-joined/hdinsight-security-overview.md) (Enterprise Security Package). 

Per i cluster ESP, l'autenticazione [Kerberos](https://web.mit.edu/Kerberos/) è abilitata per impostazione predefinita. I moduli di autenticazione plug-in (PAM) e gli schemi di autenticazione personalizzati non sono supportati.

## <a name="hiveserver2-authorization"></a>Autorizzazione HiveServer2

Per i cluster standard, l'impostazione predefinita è None. È possibile abilitare [SqlStdAuth (autorizzazione basata su standard SQL)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) . L'autorizzazione tramite [Apache Ranger](https://ranger.apache.org/) non è supportata per i cluster standard. Si consiglia di eseguire l'aggiornamento a un cluster ESP per l'autorizzazione Ranger. 

Per i cluster ESP, l'autorizzazione tramite Ranger è abilitata per impostazione predefinita. 


## <a name="ssl-encryption-for-hiveserver2"></a>Crittografia SSL per HiveServer2

L'abilitazione di Hiveserver2 SSL non è consigliata per i cluster standard o ESP. Il protocollo SSL è invece abilitato sul gateway. La [crittografia in transito](../domain-joined/encryption-in-transit.md) può essere abilitata per crittografare le comunicazioni tra i nodi del cluster mediante [IPSec (Internet Protocol Security)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Passaggi successivi
* [Panoramica dell'autenticazione HiveServer2](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Panoramica dell'autorizzazione di HiveServer2](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [Abilitazione dell'autorizzazione hive basata su standard SQL](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger con hive](../domain-joined/apache-domain-joined-run-hive.md)
