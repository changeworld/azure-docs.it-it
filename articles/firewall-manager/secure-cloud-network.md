---
title: "Esercitazione: Proteggere l'hub virtuale con Gestione firewall di Azure"
description: In questa esercitazione viene illustrato come proteggere l'hub virtuale con Gestione firewall di Azure usando il portale di Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: 14ec6fafbbadff2ecc37b229270c269f068a666f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104670461"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Esercitazione: Proteggere l'hub virtuale con Gestione firewall di Azure

Gestione firewall di Azure consente di creare hub virtuali protetti per proteggere il traffico di rete cloud destinato a indirizzi IP privati, a soluzioni PaaS di Azure e a Internet. Il routing del traffico verso il firewall è automatizzato, pertanto non è necessario creare route definite dall'utente.

![proteggere la rete cloud](media/secure-cloud-network/secure-cloud-network.png)

Gestione firewall supporta anche un'architettura di rete virtuale hub. Per un confronto delle architetture di tipo hub virtuale protetto e rete virtuale hub, vedere [Informazioni sulle opzioni disponibili per l'architettura di Gestione firewall di Azure](vhubs-and-vnets.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare la rete virtuale spoke
> * Creare un hub virtuale protetto
> * Connettere le reti virtuali hub-spoke
> * Instradare il traffico all'hub
> * Distribuire i server
> * Creare un criterio firewall e proteggere l'hub
> * Testare il firewall

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-hub-and-spoke-architecture"></a>Creare un'architettura hub-spoke

Prima di tutto, creare reti virtuali spoke in cui poter collocare i server.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Creare due reti virtuali spoke e le subnet

Le due reti virtuali includeranno ognuna un server del carico di lavoro e saranno protette tramite firewall.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Cercare **rete virtuale** e selezionare **Crea**.
2. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. Per **gruppo di risorse**, **selezionare Crea nuovo** e digitare **FW-Manager-RG** come nome e fare clic su **OK**.
2. Per **Nome**, digitare **Spoke-01**.
3. In **Area** selezionare **(Stati Uniti) Stati Uniti orientali**.
4. Selezionare **Avanti: Indirizzi IP**.
1. In **Spazio degli indirizzi** immettere **10.0.0.0/16**.
3. In **Nome subnet** selezionare **predefinito**.
4. Per **nome subnet** digitare  **workload-01-SN**.
5. In **Intervallo di indirizzi subnet** digitare **10.0.1.0/24**.
6. Selezionare **Salva**.
1. Selezionare **Rivedi e crea**.
2. Selezionare **Crea**.

Ripetere questa procedura per creare un'altra rete virtuale simile:

Nome: **Spoke-02**<br>
Spazio di indirizzi: **10.1.0.0/16**<br>
Nome subnet: **Workload-02-SN**<br>
Intervallo di indirizzi subnet: **10.1.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>Creare l'hub virtuale protetto

Creare l'hub virtuale protetto usando Gestione firewall.

1. Nella home page del portale di Azure selezionare **Tutti i servizi**.
2. Nella casella di ricerca digitare **Gestione firewall**, quindi selezionare **Gestione firewall**.
3. Nella pagina **Gestione firewall** selezionare **Visualizza hub virtuali protetti**.
4. Nella pagina **Gestione firewall | Hub virtuali protetti** selezionare **Crea nuovo hub virtuale protetto**.
5. Per **gruppo di risorse** selezionare **FW-Manager-RG**.
7. In **Area** selezionare **Stati Uniti orientali**.
1. Per **Nome hub virtuale protetto**, digitare **Hub-01**.
2. Per **spazio indirizzi Hub** Digitare **10.2.0.0/16**.
3. Come nome della nuova rete WAN virtuale digitare **Vwan-01**.
4. Lasciare deselezionata la casella di controllo **Includere il gateway VPN per abilitare i partner di sicurezza affidabili**.
5. Selezionare **Avanti: Firewall di Azure**.
6. Accettare l'impostazione predefinita **Firewall di Azure** **Abilitato** e quindi selezionare **Avanti: Partner di sicurezza affidabile**.
7. Accettare l'impostazione predefinita **Partner di sicurezza affidabile** **Disabilitato** e selezionare **Avanti: Rivedi e crea**.
8. Selezionare **Crea**. 

   Sono necessari circa 30 minuti per la distribuzione.

Al termine della distribuzione, è possibile ottenere l'indirizzo IP pubblico del firewall.

1. Aprire **gestione firewall**.
2. Selezionare **Hub virtuali**.
3. Selezionare **hub-01**.
7. Selezionare **Configurazione IP pubblico**.
8. Prendere nota dell'indirizzo IP pubblico, che verrà usato in seguito.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>Connettere le reti virtuali hub-spoke

Eseguire ora il peering tra le reti virtuali hub-spoke.

1. Selezionare il gruppo di risorse **FW-Manager-RG** , quindi selezionare la rete WAN virtuale **Vwan-01** .
2. In **Connettività** selezionare **Connessioni rete virtuale**.
3. Selezionare **Aggiungi connessione**.
4. Per **Nome connessione**, digitare **hub-spoke-01**.
5. Per **Hub**, selezionare **Hub-01**.
6. Per **gruppo di risorse** selezionare **FW-Manager-RG**.
7. Per **Rete virtuale**, selezionare **Spoke-01**.
8. Selezionare **Crea**.

Ripetere la connessione alla rete virtuale **Spoke-02**: nome connessione - **hub-spoke-02**

## <a name="deploy-the-servers"></a>Distribuire i server

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Selezionare **Windows Server 2016 Datacenter** nell'elenco **Più comuni**.
3. Immettere i valori seguenti per la macchina virtuale:

   |Impostazione  |Valore  |
   |---------|---------|
   |Resource group     |**FW-Manager-RG**|
   |Nome macchina virtuale     |**Srv-workload-01**|
   |Region     |**(USA) Stati Uniti orientali**|
   |Nome utente amministratore     |digitare un nome utente|
   |Password     |digitare una password|

4. In **Regole porta in ingresso**, per **Porte in ingresso pubbliche**, selezionare **Nessuna**.
6. Accettare tutte le altre impostazioni predefinite e selezionare **Avanti: Dischi**.
7. Accettare tutte le impostazioni predefinite del disco e selezionare **Avanti: Rete**.
8. Selezionare **Spoke-01** per la rete virtuale e **Workload-01-SN** per la subnet.
9. In **IP pubblico** selezionare **Nessuno**.
11. Accettare tutte le altre impostazioni predefinite e selezionare **Avanti: Gestione**.
12. Selezionare **Disabilita** per disabilitare la diagnostica di avvio. Accettare tutte le altre impostazioni predefinite e selezionare **Rivedi e crea**.
13. Verificare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**.

Usare le informazioni della tabella seguente per configurare un'altra macchina virtuale denominata **Srv-Workload-02**. Il resto della configurazione è uguale a quella della macchina virtuale **Srv-workload-01**.

|Impostazione  |valore  |
|---------|---------|
|Rete virtuale|**Spoke-02**|
|Subnet|**Workload-02-SN**|

Dopo la distribuzione dei server, selezionare una risorsa server e in **Rete** prendere nota dell'indirizzo IP privato di ogni server.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Creare un criterio firewall e proteggere l'hub

Un criterio firewall definisce raccolte di regole per indirizzare il traffico su uno o più hub virtuali protetti. Sarà perciò necessario creare il criterio firewall e quindi proteggere l'hub.

1. In Gestione firewall selezionare **Visualizza criteri firewall di Azure**.
2. Selezionare **Crea criterio firewall di Azure**.
1. Per **gruppo di risorse** selezionare **FW-Manager-RG**.
1. In **Dettagli criteri**, per **Nome** digitare **Policy-01** e per **Area** selezionare **Stati Uniti orientali**.
1. Selezionare **Avanti: impostazioni DNS**.
1. Selezionare **Avanti: controllo TLS (anteprima)**.
1. Selezionare **Next: Rules**.
1. Nella scheda **Regole** selezionare **Aggiungi una raccolta regole**.
1. Nella pagina **Aggiungi una raccolta regole** digitare **App-RC-01** per **Nome**.
1. Per **Tipo di raccolta regole**, selezionare **Applicazione**.
1. In **Priorità** digitare **100**.
1. Verificare che **Azione raccolta regole** sia **Consenti**.
1. Come **Nome** della regola, digitare **Allow-msft**.
1. Per **Tipo di origine**, selezionare **Indirizzo IP**.
1. Per **Origine**, digitare **\*** .
1. Per **Protocollo**, digitare **http,https**.
1. Verificare che **Tipo di destinazione** sia impostato su **FQDN**.
1. Per **Destinazione**, digitare **\*.microsoft.com**.
1. Selezionare **Aggiungi**.

Aggiungere una regola DNAT in modo da poter connettere un desktop remoto alla macchina virtuale **Srv-Workload-01**.

1. Selezionare **Aggiungi/regola raccolta**.
1. Per **nome** Digitare **DNAT-RDP**.
1. Per **Tipo di raccolta regole**, selezionare **DNAT**.
1. In **Priorità** digitare **100**.
1. Per **Nome** della regola digitare **Allow-rdp**.
1. Per **Tipo di origine**, selezionare **Indirizzo IP**.
1. Per **Origine**, digitare **\*** .
1. In **Protocollo** selezionare **TCP**.
1. In **Porte di destinazione** digitare **3389**.
1. In **Tipo di destinazione** selezionare **Indirizzo IP**.
1. Per **Destinazione**, digitare l'indirizzo IP pubblico del firewall annotato in precedenza.
1. Per **Indirizzo convertito**, digitare l'indirizzo IP privato per la macchina virtuale **Srv-Workload-01** annotato in precedenza.
1. Per **Porta tradotta** digitare **3389**.
1. Selezionare **Aggiungi**.

Aggiungere una regola di rete in modo da poter connettere un desktop remoto da **Srv-Workload-01** a **Srv-Workload-02**.

1. Selezionare **Aggiungi una raccolta regole**.
2. Per **Nome**, digitare **vnet-rdp**.
3. In **Tipo di raccolta regole** selezionare **Rete**.
4. In **Priorità** digitare **100**.
1. Per **azione raccolta regole** selezionare **Consenti**.
1. Per **Nome** della regola, digitare **Allow-vnet**.
1. Per **Tipo di origine**, selezionare **Indirizzo IP**.
1. Per **Origine**, digitare **\*** .
1. In **Protocollo** selezionare **TCP**.
1. In **Porte di destinazione** digitare **3389**.
1. In **Tipo di destinazione** selezionare **Indirizzo IP**.
1. Per **Destinazione**, digitare l'indirizzo IP pubblico della macchina virtuale **Srv-Workload-02** annotato in precedenza.
1. Selezionare **Aggiungi**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

## <a name="associate-policy"></a>Associa criteri

Associare i criteri del firewall all'hub.

1. In gestione firewall selezionare **criteri del firewall di Azure**.
1. Selezionare la casella di controllo per **policy-01**.
1. Selezionare **Gestisci associazioni/associa Hub**.
1. Selezionare **hub-01**.
1. Selezionare **Aggiungi**.

## <a name="route-traffic-to-your-hub"></a>Instradare il traffico all'hub

A questo punto è necessario assicurarsi che il traffico di rete venga instradato attraverso il firewall.

1. In gestione firewall selezionare **Hub virtuali**.
2. Selezionare **Hub-01**.
3. In **Impostazioni** selezionare **Configurazione della sicurezza**.
4. In **Traffico Internet** selezionare **Firewall di Azure**.
5. In **Traffico privato** selezionare **Send via Azure Firewall** (Invia tramite Firewall di Azure).
1. Selezionare **Salva**.

   L'aggiornamento delle tabelle di route richiede alcuni minuti.
1. Verificare che le due connessioni mostrino che il firewall di Azure protegga sia il traffico Internet che quello privato.

## <a name="test-the-firewall"></a>Testare il firewall

Per testare le regole del firewall, è necessario connettere un desktop remoto usando l'indirizzo IP pubblico del firewall, che è Nato a **SRV-workload-01**. Da qui verrà usato un browser per testare la regola dell'applicazione e connettere un desktop remoto a **Srv-Workload-02** per testare la regola di rete.

### <a name="test-the-application-rule"></a>Testare la regola dell'applicazione

A questo punto testare le regole del firewall per verificare che tutto funzioni come previsto.

1. Connettere un desktop remoto all'indirizzo IP pubblico del firewall, quindi accedere.

3. Aprire Internet Explorer e passare a `https://www.microsoft.com`.
4. Selezionare **OK** > **Close** negli avvisi di sicurezza di Internet Explorer.

   Verrà visualizzata la home page Microsoft.

5. Passare a `https://www.google.com`.

   Si verrà bloccati dal firewall.

A questo punto è stato verificato che la regola dell'applicazione del firewall funziona:

* È possibile passare al nome di dominio completo consentito ma non agli altri.

### <a name="test-the-network-rule"></a>Testare la regola di rete

Ora testare la regola di rete.

- Da SRV-workload-01 aprire un desktop remoto per l'indirizzo IP privato SRV-workload-02.

   Un desktop remoto dovrebbe connettersi a Srv-Workload-02.

A questo punto è stato verificato che la regola di rete del firewall funziona:
* È possibile connettere un desktop remoto a un server che si trova in un'altra rete virtuale.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine del test delle risorse del firewall, eliminare il gruppo di risorse **FW-Manager-RG** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere le informazioni sui partner di sicurezza affidabili](trusted-security-partners.md)
