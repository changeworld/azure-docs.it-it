---
title: Configurare la disponibilità elevata
description: Aumentare la resilienza del Defender per la distribuzione di Internet delle cose installando un'appliance di disponibilità elevata della console di gestione locale. Le distribuzioni a disponibilità elevata garantiscono che i sensori gestiti segnalino continuamente una console di gestione locale attiva.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: eb5f2c6293042f44de5e3c061c6d379bee6e5b06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523890"
---
# <a name="about-high-availability"></a>Informazioni sulla disponibilità elevata

Aumentare la resilienza del Defender per la distribuzione di Internet delle cose installando un'appliance di disponibilità elevata della console di gestione locale. Le distribuzioni a disponibilità elevata garantiscono che i sensori gestiti segnalino continuamente una console di gestione locale attiva.

Questa distribuzione viene implementata con una coppia di console di gestione locale che include un'appliance primaria e una secondaria.

## <a name="about-primary-and-secondary-communication"></a>Informazioni sulle comunicazioni primarie e secondarie

Quando viene abbinata una console di gestione locale primaria e secondaria:

- Viene applicato un certificato SLL della console di gestione locale per creare una connessione sicura tra le appliance primarie e secondarie. SLL può essere il certificato autofirmato installato per impostazione predefinita o un certificato installato dal cliente.

- I dati della console di gestione locale primaria vengono automaticamente sottoposti a backup nella console di gestione locale secondaria ogni 10 minuti. Viene eseguito il backup delle configurazioni della console di gestione locale e dei dati del dispositivo. I file e i log PCAP non sono inclusi nel backup. È possibile eseguire il backup e il ripristino manuale di PCAPs e log.

- La configurazione principale nella console di gestione è duplicata nel database secondario; ad esempio, le impostazioni di sistema. Se queste impostazioni vengono aggiornate nel database primario, vengono aggiornate anche sul database secondario.

- Prima della scadenza della licenza secondaria, è necessario definirla come primaria per poter aggiornare la licenza.

## <a name="about-failover-and-failback"></a>Informazioni su failover e failback

Se un sensore non è in grado di connettersi alla console di gestione locale primaria, si connette automaticamente al database secondario. Il sistema sarà supportato contemporaneamente sia nel database primario che in quello secondario, se meno della metà dei sensori comunicano con il database secondario. Il secondario assume il sopravvento quando più della metà dei sensori comunicano con esso. Il failover dal database primario al database secondario richiede circa tre minuti. Quando si verifica il failover, la console di gestione locale primaria si blocca. Quando si verifica questo problema, è possibile accedere al database secondario usando le stesse credenziali di accesso.

Durante il failover, i sensori continuano a provare a comunicare con il dispositivo principale. Quando più della metà dei sensori gestiti riesce a comunicare con il database primario, il database primario viene ripristinato. Quando viene ripristinato il database primario, viene visualizzato il messaggio seguente nella console secondaria.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Screenshot di un messaggio che viene visualizzato nella console secondaria quando viene ripristinato il database primario.":::

Eseguire di nuovo l'accesso all'appliance principale dopo il reindirizzamento.

## <a name="high-availability-setup-overview"></a>Panoramica della configurazione della disponibilità elevata

Le procedure di installazione e configurazione vengono eseguite in quattro fasi principali:

1. Installare un'appliance principale della console di gestione locale. 

2. Configurare l'appliance principale della console di gestione locale. Ad esempio le impostazioni di backup pianificate, le impostazioni VLAN. Per informazioni dettagliate, vedere la guida dell'utente della console di gestione locale. Tutte le impostazioni vengono applicate automaticamente al dispositivo secondario dopo l'associazione.

3. Installare un'appliance secondaria della console di gestione locale. Per ulteriori informazioni, vedere [informazioni sul Defender per l'installazione](how-to-install-software.md)di Internet.

4. Associare le appliance della console di gestione locale primaria e secondaria, come descritto [qui](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console). La console di gestione locale primaria deve gestire almeno due sensori per eseguire la configurazione.

## <a name="high-availability-requirements"></a>Requisiti di disponibilità elevata

Verificare di aver soddisfatto i requisiti di disponibilità elevata seguenti:

- Requisiti per i certificati

- Requisiti software e hardware

- Requisiti di accesso alla rete

### <a name="software-and-hardware-requirements"></a>Requisiti software e hardware

- Entrambe le appliance della console di gestione locale primaria e secondaria devono eseguire modelli hardware e versioni software identiche.

- Il sistema a disponibilità elevata può essere configurato da Defender solo per gli utenti del tutto, usando gli strumenti dell'interfaccia della riga di comando.

### <a name="network-access-requirements"></a>Requisiti di accesso alla rete

È necessario verificare che i criteri di sicurezza dell'organizzazione consentano di accedere ai servizi seguenti nella console di gestione locale primaria e secondaria. Questi servizi consentono inoltre la connessione tra i sensori e la console di gestione locale secondaria:

|Porta|Servizio|Descrizione|
|----|-------|-----------|
|**443 o TCP**|HTTPS|Concede l'accesso alla console Web della console di gestione locale.|
|**22 o TCP**|SSH|Sincronizza i dati tra le appliance della console di gestione locale primaria e secondaria|
|**123 o UDP**|NTP| Sincronizzazione dell'ora NTP della console di gestione locale. Verificare che le appliance attive e passive siano definite con lo stesso fuso orario.|

## <a name="create-the-primary-and-secondary-pair"></a>Creare la coppia primaria e secondaria

Verificare che le appliance della console di gestione locale primaria e secondaria siano accese prima di avviare la procedura.  

### <a name="on-the-primary"></a>Nel database primario

1. Accedere all'interfaccia della riga di comando come difensore per l'utente.

2. Eseguire il comando seguente nel database primario:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>In questo documento, la console di gestione locale principale viene definita come primaria e l'agente viene indicato come secondario.

3. Immettere l'indirizzo IP del dispositivo secondario nel ```<Secondary ip>``` campo e premere INVIO. L'indirizzo IP viene quindi convalidato e il certificato SSL viene scaricato nel database primario. L'immissione dell'indirizzo IP associa anche i sensori al dispositivo secondario.

4. Eseguire il comando seguente nel database primario per verificare che il certificato sia installato correttamente:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Eseguire il comando seguente nel database primario. **Non eseguire con sudo.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

Ciò consente la connessione tra le appliance primarie e secondarie per scopi di backup e ripristino.

6. Immettere l'indirizzo IP del database secondario e premere INVIO.

### <a name="on-the-secondary"></a>Nel database secondario

1. Accedere all'interfaccia della riga di comando come difensore per l'utente.

2. Eseguire il comando seguente sul database secondario. **Non eseguire con sudo**:

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

Ciò consente la connessione tra le appliance primarie e secondarie per scopi di backup e ripristino.

3. Immettere l'indirizzo IP del database primario e premere INVIO.

### <a name="track-high-availability-activity"></a>Tenere traccia dell'attività di disponibilità elevata

I log principali dell'applicazione possono essere esportati al team di supporto di Defender for Internet per gestire eventuali problemi di disponibilità elevata.  

Per accedere ai log principali:

1. Selezionare **Esporta** dalla finestra **impostazioni di sistema** .

## <a name="update-the-on-premises-management-console-with-high-availability"></a>Aggiornare la console di gestione locale con disponibilità elevata

Eseguire l'aggiornamento a disponibilità elevata nell'ordine seguente. Verificare che ogni passaggio sia completato prima di iniziare un nuovo passaggio.

Per eseguire l'aggiornamento con la disponibilità elevata:

1. Aggiornare la console di gestione locale primaria.

2. Aggiornare la console di gestione locale secondaria.

3. Aggiornare i sensori.

## <a name="see-also"></a>Vedi anche

[Attivare e configurare la console di gestione locale](how-to-activate-and-set-up-your-on-premises-management-console.md)