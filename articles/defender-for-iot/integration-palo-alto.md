---
title: Integrazione di Palo Alto
description: Defender for Internet ha integrato la piattaforma di monitoraggio delle minacce per ICS continua con i firewall di nuova generazione di palo alto per consentire il blocco di minacce critiche, in modo più rapido ed efficiente.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783932"
---
# <a name="about-the-palo-alto-integration"></a>Informazioni sull'integrazione di palo alto

Defender for Internet ha integrato la piattaforma di monitoraggio delle minacce per ICS continua con i firewall di nuova generazione di palo alto per consentire il blocco di minacce critiche, in modo più rapido ed efficiente.

Sono disponibili i seguenti tipi di integrazione:

- Opzione di blocco automatico: Defender diretto per l'integrazione di IoT-Palo alto

- Invio di raccomandazioni per il blocco al sistema di gestione centrale: Defender for IoT-Panorama Integration

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>Configura blocco immediato per palo alto firewall specificato

In casi critici, ad esempio gli avvisi correlati al malware, è possibile abilitare il blocco automatico. Questa operazione viene eseguita configurando una regola di invio in Defender per tutto ciò che invia il comando di blocco direttamente a una specifica pagina di palo alto firewall.

Quando Defender for Internet rileva una minaccia critica, invia un avviso che include la possibilità di bloccare l'origine infetta. Selezionando **blocca origine** nei dettagli dell'avviso viene attivata la regola di inoltramento, che invia il comando di blocco al palo alto firewall specificato.

Per configurare:

1. Nel riquadro sinistro selezionare **inoltri**.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="Schermata di avviso di invio.":::

1. Selezionare **Crea regola di invio**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Crea regola di invio":::

1. Per configurare la regola di invio di palo alto nuova generazione:  
 
   - Definire i parametri della regola standard e nella casella di riepilogo a discesa **azioni** selezionare **Invia a palo alto nuova generazione**.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="Modificare la regola di invio.":::

1. Nel riquadro azioni impostare i parametri seguenti:

   - **Host**: immettere l'indirizzo IP del server nuova generazione.
   - **Porta**: immettere la porta del server nuova generazione.
   - **Nome utente**: immettere il nome utente del server nuova generazione.
   - **Password**: immettere la password del server nuova generazione.
   - **Configure**: configurare le opzioni seguenti per consentire il blocco delle origini sospette da palo alto firewall:
     - **Blocca i codici di funzione non validi**: violazioni del protocollo-valore di campo non valido che viola la specifica del protocollo ICS (potenziale exploit).
     - **Blocca la programmazione o gli aggiornamenti del firmware del PLC non autorizzati**: modifiche al PLC non autorizzate.
     - **Blocca arresto del PLC non autorizzato**: arresto del PLC (tempo di inattività).
     - **Blocca gli avvisi correlati al malware**: blocco di tentativi di malware industriali (Triton, NotPetya e così via). È possibile selezionare l'opzione di **blocco automatico**. In tal caso, il blocco viene eseguito automaticamente e immediatamente.
     - **Blocca l'analisi non autorizzata**: analisi non autorizzata (potenziale esplorazione).
     
1. Selezionare **Submit** (Invia).

Per bloccare l'origine sospetta: 

1. Nel riquadro **avvisi** selezionare l'avviso relativo all'integrazione di palo alto. Verrà visualizzata la finestra di dialogo **Dettagli avviso** .
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Dettagli avviso":::

1. Per bloccare automaticamente l'origine sospetta, selezionare **blocca origine**. Verrà visualizzata la finestra di dialogo **conferma** .

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confermare il blocco nella schermata conferma.":::

1. Nella finestra di dialogo **conferma** , fare clic su **OK**. L'origine sospetta è bloccata da palo alto firewall.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Invio di criteri di blocco a palo alto panorama

Defender per l'IT e Palo Alto Networks hanno un'integrazione offline che crea automaticamente nuovi criteri in Palo Alto Networks SMN, panorama. Questa integrazione richiede la conferma da parte dell'amministratore di panorama e non consente il blocco automatico.

L'integrazione è destinata agli eventi imprevisti seguenti:

- **Modifiche al PLC non autorizzate:** Aggiornamento della logica di scala o del firmware di un dispositivo. Questo può rappresentare un'attività legittima o un tentativo di compromissione del dispositivo. Il compromesso potrebbe verificarsi inserendo codice dannoso, ad esempio un Trojan di accesso remoto (RAT) o parametri che causano il funzionamento del processo fisico, ad esempio una turbina rotante, in modo non sicuro.

- **Violazione del protocollo:** Una struttura di pacchetti o un valore di campo che viola la specifica del protocollo. Questo può rappresentare un'applicazione non configurata correttamente o un tentativo dannoso di compromettere il dispositivo. Ad esempio, causando una condizione di overflow del buffer nel dispositivo di destinazione.

- **Arresto del PLC:** Comando che impedisce il funzionamento del dispositivo, rischiando così il processo fisico controllato dal PLC.

- **Malware industriale trovato nella rete ICS:** Malware che manipola i dispositivi ICS usando i protocolli nativi, ad esempio TRITON e Industroyer. Defender for Internet rileva anche il malware che è stato spostato lateralmente nell'ambiente ICS e SCADA, ad esempio Conficker, WannaCry e NotPetya.

- **Analisi malware:** Strumenti di esplorazione che raccolgono dati sulla configurazione di sistema in una fase di pre-attacco. Ad esempio, il Trojan Havex analizza le reti industriali per i dispositivi che usano OPC, un protocollo standard usato dai sistemi SCADA basati su Windows per comunicare con i dispositivi ICS.

## <a name="the-process"></a>Il processo

Quando Defender for Internet rileva un caso d'uso preconfigurato, il pulsante **origine blocco** viene aggiunto all'avviso. Quindi, quando l'utente **CyberX** seleziona il pulsante **origine blocco** , Defender for Internet crea i criteri per il panorama inviando la regola di inoltro predefinita.

Il criterio viene applicato solo quando l'amministratore panorama lo inserisce nel nuova generazione pertinente nella rete.

Nelle reti IT possono essere presenti indirizzi IP dinamici. Per tali subnet, pertanto, i criteri devono essere basati su FQDN (nome DNS) e non sull'indirizzo IP. Defender for Internet esegue la ricerca inversa e associa i dispositivi con indirizzo IP dinamico al relativo FQDN (nome DNS) ogni numero di ore configurate.

Defender for Internet viene inoltre inviato un messaggio di posta elettronica all'utente panorama pertinente per notificare che un nuovo criterio creato da Defender for Internet è in attesa dell'approvazione. Nella figura seguente viene presentato il Defender per IoT-Panorama architettura di integrazione.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="Architettura di integrazione CyberX-panorama":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Creare criteri di blocco panoramico in Defender per la configurazione di Internet delle cose

### <a name="to-configure-dns-lookup"></a>Per configurare la ricerca DNS

1. Nel riquadro sinistro selezionare impostazioni di **sistema**.

1. Nel riquadro **Impostazioni sistema** selezionare **impostazioni DNS** :::image type="icon" source="media/integration-paloalto/settings.png"::: .

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="Configurare le impostazioni DNS.":::

1. Nella finestra di dialogo **Modifica impostazioni DNS** impostare i parametri seguenti:

   - **Stato**: stato del resolver DNS.

   - **Indirizzo del server DNS**: immettere l'indirizzo IP o il nome di dominio completo del server DNS di rete.
   - **Porta server DNS**: immettere la porta utilizzata per eseguire una query sul server DNS.
   - **Subnet**: impostare l'intervallo di subnet dell'indirizzo IP dinamico. L'intervallo che Defender for Internet è invertito per cercare il proprio indirizzo IP nel server DNS in modo che corrisponda al nome FQDN corrente.
   - **Pianifica ricerca inversa**: definire le opzioni di pianificazione come segue:
     - Per orari specifici: specificare quando eseguire la ricerca inversa giornaliera.
     - Per intervalli fissi (in ore): impostare la frequenza di esecuzione della ricerca inversa.
   - **Numero di etichette**: indicare a Defender for Internet per la risoluzione automatica degli indirizzi IP di rete in FQDN del dispositivo. <br />Per configurare la risoluzione FQDN DNS, aggiungere il numero di etichette di dominio da visualizzare. Da sinistra a destra vengono visualizzati fino a 30 caratteri.
1. Selezionare **SAVE** (SALVA).
1. Per assicurarsi che le impostazioni DNS siano corrette, selezionare **Lookup test**. Il test garantisce che l'indirizzo IP del server DNS e la porta del server DNS siano impostati correttamente.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Per configurare una regola di invio per bloccare il traffico sospetto con palo alto firewall

1. Nel riquadro sinistro selezionare **inoltri**. Viene visualizzato il riquadro di invio.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="Schermata di avanzamento.":::

1. Nel riquadro di **avanzamento** selezionare **Crea regola di invio**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Crea regola di invio":::

1. Per configurare la regola di invio di palo alto panorama:

   Definire i parametri della regola standard e nella casella di riepilogo a discesa **azioni** selezionare **Invia a palo alto panorama**. Viene visualizzato il riquadro dettagli azione.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Seleziona azione":::

1. Nel riquadro azioni impostare i parametri seguenti:

   - **Host**: immettere l'indirizzo IP del server panorama.

   - **Porta**: immettere la porta del server panorama.
   - **Nome utente**: immettere il nome utente del server panorama.
   - **Password**: immettere la password del server panorama.
   - **Indirizzo report**: definire la modalità di esecuzione del blocco, come indicato di seguito:
   
     - **Per indirizzo IP**: crea sempre i criteri di blocco su panorama in base all'indirizzo IP.
     
     - **Per nome di dominio completo o indirizzo IP**: crea i criteri di blocco su panorama in base al nome di dominio completo, se esistente, in caso contrario tramite l'indirizzo IP.
     
   - **Posta elettronica**: impostare l'indirizzo di posta elettronica per il messaggio di notifica del criterio
     > [!NOTE]
     > Assicurarsi di aver configurato un server di posta in Defender per tutto. Se non viene immesso alcun indirizzo di posta elettronica, Defender for Internet non invia un messaggio di posta elettronica di notifica.
   - **Eseguire una ricerca DNS al rilevamento dell'avviso (casella** di controllo): quando l'opzione per FQDN o indirizzo IP è impostata nell'indirizzo del report. Questa casella di controllo è selezionata per impostazione predefinita. Se è impostato solo l'indirizzo IP, questa opzione è disabilitata.
   - **Configure**: configurare le opzioni seguenti per consentire il blocco delle origini sospette da palo alto panorama:
   
     - **Blocca i codici di funzione non validi**: violazioni del protocollo-valore di campo non valido violando ICS, specifica del protocollo (potenziale exploit).
     
     - **Blocca la programmazione o gli aggiornamenti del firmware del PLC non autorizzati**: modifiche al PLC non autorizzate.
     
     - **Blocca arresto del PLC non autorizzato**: arresto del PLC (tempo di inattività).
     
     - **Blocca gli avvisi correlati al malware**: blocco di tentativi di malware industriali (Triton, NotPetya e così via). È possibile selezionare l'opzione di **blocco automatico**. In tal caso, il blocco viene eseguito automaticamente e immediatamente.
     
     - **Blocca l'analisi non autorizzata**: analisi non autorizzata (potenziale esplorazione).
     
1. Selezionare **Submit** (Invia).

### <a name="to-block-the-suspicious-source"></a>Per bloccare l'origine sospetta

1. Nel riquadro **avvisi** selezionare l'avviso relativo all'integrazione di palo alto. Verrà visualizzata la finestra **di dialogo Dettagli avviso** .

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Dettagli avviso":::        

1. Per bloccare automaticamente l'origine sospetta, selezionare **blocca origine**.

1. Nella finestra di dialogo **conferma** , fare clic su **OK.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confermare":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [inviare le informazioni sugli avvisi](how-to-forward-alert-information-to-partners.md).
