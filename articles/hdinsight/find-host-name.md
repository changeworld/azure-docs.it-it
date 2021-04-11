---
title: Come ottenere i nomi host dei nodi del cluster di Azure HDInsight
description: Informazioni su come ottenere i nomi host e il nome FQDN dei nodi del cluster di Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111464"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>Trovare i nomi host dei nodi del cluster

Il cluster HDInsight viene creato con il clustername.azurehdinsight.net DNS pubblico. Quando si esegue la connessione SSH a singoli nodi o si imposta la connessione ai nodi del cluster con nella stessa rete virtuale personalizzata, è necessario usare il nome host o i nomi di dominio completi (FQDN) dei nodi del cluster.

In questo articolo si apprenderà come ottenere i nomi host dei nodi del cluster. È possibile ottenerlo manualmente tramite l'interfaccia utente Web di Ambariri o automaticamente tramite l'API REST di Ambari.

> [!WARNING]
> Usare gli approcci consigliati seguenti per recuperare i nomi host dei nodi del cluster. I numeri nel nome host non sono garantiti in sequenza e HDInsight può modificare il formato del nome host in modo che sia allineato alle macchine virtuali con aggiornamento versione. Non prendere la dipendenza da una determinata convenzione di denominazione attualmente esistente. 
>

È possibile ottenere i nomi host tramite l'interfaccia utente di Ambariri o l'API REST Ambari. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Ottenere i nomi host dall'interfaccia utente Web di Ambariri
È possibile usare l'interfaccia utente Web di Ambariri per ottenere i nomi host quando si esegue la connessione SSH al nodo. La visualizzazione host dell'interfaccia utente Web di Ambari è disponibile nel cluster HDInsight all'indirizzo `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts` , dove `CLUSTERNAME` è il nome del cluster.

![Get-host-names-in-Ambari-UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Ottenere i nomi host dall'API REST di Ambari
Quando si compilano script di automazione, è possibile usare l'API REST Ambari per ottenere i nomi host prima di stabilire connessioni agli host. I numeri nel nome host non sono garantiti in sequenza e HDInsight può modificare il formato del nome host in modo che sia allineato alle macchine virtuali con aggiornamento versione. Non prendere la dipendenza da una determinata convenzione di denominazione attualmente esistente. 

Di seguito sono riportati alcuni esempi di come recuperare il nome di dominio completo per i nodi del cluster. Per altre informazioni sull'API REST di Ambari, vedere [gestire i cluster HDInsight tramite l'API REST di Apache Ambari](.\hdinsight-hadoop-manage-ambari-rest-api.md)

Nell'esempio seguente viene usato [JQ](https://stedolan.github.io/jq/) o [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) per analizzare il documento di risposta JSON e visualizzare solo i nomi host.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```