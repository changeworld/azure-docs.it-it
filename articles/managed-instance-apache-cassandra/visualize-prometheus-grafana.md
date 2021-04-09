---
title: Configurare Grafana per visualizzare le metriche emesse da Azure Istanza gestita per Apache Cassandra
description: Informazioni su come installare e configurare Grafana in una macchina virtuale per visualizzare le metriche emesse da un cluster Istanza gestita di Azure per Apache Cassandra.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745590"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Configurare Grafana per visualizzare le metriche emesse dal cluster di istanze gestite

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando si distribuisce un cluster Azure Istanza gestita per Apache Cassandra, il servizio effettua il provisioning di un server che ospita [Prometheus](https://prometheus.io/) che può essere utilizzato da vari strumenti client. Prometeo è una soluzione di monitoraggio Open Source. L'istanza gestita emetterà le metriche e manterrà 10 minuti o 10 GB di dati (a seconda della soglia raggiunta per primo). Questo articolo descrive come configurare Grafana per visualizzare le metriche emesse dal cluster di istanze gestite. Per visualizzare le metriche, è necessario eseguire le attività seguenti:

* Distribuire una macchina virtuale Ubuntu all'interno della rete virtuale di Azure in cui è presente l'istanza gestita.
* Installare lo [strumento Grafana](https://grafana.com/grafana/) open source per creare dashboard e visualizzare le metriche emesse da Prometeo.

## <a name="deploy-a-ubuntu-server"></a>Distribuire un server Ubuntu

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Passare al gruppo di risorse in cui si trova il cluster dell'istanza gestita. Selezionare **Aggiungi** e cercare l'immagine di **Ubuntu server 18,04 LTS** :

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Trovare un'immagine di Ubuntu Server dalla portale di Azure." border="true":::

1. Selezionare l'immagine e selezionare **Crea**.

1. Nel pannello **Crea una macchina virtuale** immettere i valori per i campi seguenti. è possibile lasciare i valori predefiniti per gli altri campi:

   * **Nome macchina virtuale** : immettere un nome per la VM.
   * **Region** : selezionare la stessa area in cui è stata distribuita la rete virtuale.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Compilare il modulo per creare una macchina virtuale con un'immagine di Ubuntu Server." border="true":::

1. Nella scheda **rete** selezionare il rete in ingresso virtuale in cui è distribuita l'istanza gestita:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Configurare le impostazioni di rete del server Ubuntu." border="true":::

1. Infine selezionare **Verifica + crea** per creare il server Grafana.

## <a name="install-grafana"></a>Installare Grafana

1. Dalla portale di Azure aprire la rete virtuale in cui sono state distribuite l'istanza gestita e il server Grafana. Verrà visualizzata un'istanza del set di scalabilità di macchine virtuali denominata **Cassandra-Jump (istanza 0)**. Questa metrica Prometeo è ospitata in questo set di scalabilità di macchine virtuali. Prendere nota dell'indirizzo IP dell'istanza:

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Ottiene l'indirizzo IP dell'istanza di Prometeo." border="true":::

1. Connettersi al server Ubuntu appena creato usando l'interfaccia della riga di comando di [Azure](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) o lo strumento client preferito per connettersi tramite SSH.

1. Dopo la connessione alla VM, è necessario installare e configurare Grafana per la connessione al set di scalabilità di macchine virtuali in cui sono ospitate le metriche. Aprire un prompt dei comandi e immettere il `nano` comando per aprire un editor di testo nano. Incollare lo script seguente nell'editor di testo, assicurarsi di sostituire `<prometheus IP address>` con l'indirizzo IP registrato nel passaggio precedente:

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. Digitare `ctrl + X` per salvare il file. È possibile assegnare un nome al file `grafana.sh` .

1. Immettere il `./grafana.sh` comando nel prompt dei comandi per installare Grafana.

1. Al termine dell'installazione, Grafana sarà disponibile sulla **porta 3000** nell'indirizzo IP del server, come illustrato nello screenshot seguente:

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="Eseguire Grafana sulla porta 3000." border="true":::

1. È possibile scegliere tra i dashboard open source creati per Apache Cassandra in Grafana, ad esempio il file JSON di [Panoramica cluster](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) . Scaricare e importare la definizione JSON del dashboard in Grafana:

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Importare la definizione JSON Grafana." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Caricare la definizione JSON Grafana." border="true":::

1. È quindi possibile monitorare il cluster dell'istanza gestita di Cassandra con il dashboard scelto:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Visualizzare le metriche dell'istanza gestita di Cassandra nel dashboard." border="true":::

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come configurare i dashboard per visualizzare le metriche in Prometheus usando Grafana. Scopri di più su Azure Istanza gestita per Apache Cassandra con gli articoli seguenti:

* [Panoramica di Azure Istanza gestita per Apache Cassandra](introduction.md)
* [Distribuire un cluster di Apache Spark gestiti con Azure Databricks (anteprima)](deploy-cluster-databricks.md)
