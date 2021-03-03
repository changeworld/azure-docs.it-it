---
title: 'Anteprima: avvio attendibile per le macchine virtuali di Azure'
description: Informazioni sul lancio attendibile per macchine virtuali di Azure.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: bc1afa72a0eebd2bb467616237641222b790923c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679807"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Avvio attendibile per macchine virtuali di Azure (anteprima)

Azure offre un avvio attendibile come metodo semplice per migliorare la sicurezza delle macchine virtuali di [seconda generazione](generation-2.md) . Il lancio attendibile protegge da tecniche di attacco avanzate e permanenti. L'avvio attendibile è costituito da numerose tecnologie di infrastruttura coordinate che possono essere abilitate in modo indipendente. Ogni tecnologia fornisce un altro livello di difesa contro le minacce sofisticate.

> [!IMPORTANT]
> L'avvio attendibile richiede la creazione di nuove macchine virtuali. Non è possibile abilitare l'avvio attendibile sulle macchine virtuali esistenti create inizialmente senza di essa.
>
> L'avvio attendibile è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
>
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="benefits"></a>Vantaggi 

- Distribuisci in modo sicuro le macchine virtuali con i caricatori di avvio verificati, i kernel del sistema operativo e i driver.
- Proteggi in modo sicuro chiavi, certificati e segreti nelle macchine virtuali.
- Ottenere informazioni dettagliate e garantire l'integrità dell'intera catena di avvio.
- Verificare che i carichi di lavoro siano attendibili e verificabili.

## <a name="public-preview-limitations"></a>Limiti dell'anteprima pubblica

**Supporto delle dimensioni**: tutte le dimensioni delle macchine virtuali di [seconda generazione](generation-2.md) , eccetto:

- Modello HBV3 
- Serie Lsv2 
- Serie M 
- Serie Mv2 
- Serie NDv4 
- Serie NVv4

**Supporto del sistema operativo**:
- RedHat Enterprise Linux 8,3
- SUSE 15 SP2
- Ubuntu 20.04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise

**Regioni**: 
- Stati Uniti centro-meridionali
- Europa settentrionale

**Prezzi**: nessun costo aggiuntivo per i prezzi delle macchine virtuali esistenti.

**Le funzionalità seguenti non sono supportate in questa versione di anteprima**:
- Backup
- Azure Site Recovery
- Raccolta di immagini condivise
- Disco del sistema operativo temporaneo
- Disco condiviso
- Immagine gestita
- Host dedicato di Azure 

## <a name="secure-boot"></a>Avvio protetto

Alla radice dell'avvio attendibile è disponibile l'avvio protetto per la macchina virtuale. Questa modalità, implementata nel firmware della piattaforma, protegge dall'installazione di rootkit basati su malware e di boot Kit. L'avvio protetto funziona per assicurarsi che sia possibile avviare solo i driver e i sistemi operativi firmati. Stabilisce una "radice di attendibilità" per lo stack software nella macchina virtuale. Con l'avvio protetto abilitato, tutti i componenti di avvio del sistema operativo (caricatore di avvio, kernel, driver del kernel) devono essere firmati da autori attendibili. Entrambe le finestre e selezionare le distribuzioni Linux supportano l'avvio protetto. Se l'avvio protetto non è in grado di autenticare che l'immagine è stata firmata da un autore attendibile, non sarà possibile eseguire l'avvio della macchina virtuale. Per altre informazioni, vedere [Avvio protetto](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot).

## <a name="vtpm"></a>vTPM

Il lancio attendibile introduce anche vTPM per le macchine virtuali di Azure. Si tratta di una versione virtualizzata di un [Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview)hardware, conforme alla specifica TPM 2.0. Funge da insieme di credenziali protetto dedicato per le chiavi e le misurazioni. Il lancio attendibile offre alla VM una propria istanza di TPM dedicata, in esecuzione in un ambiente protetto al di fuori della portata di qualsiasi macchina virtuale. Il vTPM consente l' [attestazione](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) misurando l'intera catena di avvio della VM (UEFI, sistema operativo, sistema e driver). 

L'avvio attendibile USA vTPM per eseguire l'attestazione remota dal cloud. Viene usato per i controlli di integrità della piattaforma e per prendere decisioni basate sull'attendibilità. Come controllo di integrità, l'avvio attendibile può certificare crittograficamente che la macchina virtuale è stata avviata correttamente. Se il processo ha esito negativo, probabilmente perché la VM sta eseguendo un componente non autorizzato, il Centro sicurezza di Azure emetterà avvisi di integrità. Gli avvisi includono i dettagli sui componenti che non hanno superato i controlli di integrità.

## <a name="virtualization-based-security"></a>Sicurezza basata su virtualizzazione

La [sicurezza basata sulla virtualizzazione](/windows-hardware/design/device-experiences/oem-vbs) (VBS) utilizza l'hypervisor per creare un'area di memoria protetta e isolata. Windows usa queste aree per eseguire varie soluzioni di sicurezza con una maggiore protezione da vulnerabilità e attacchi dannosi. Il lancio attendibile consente di abilitare l'integrità del codice hypervisor (HVCI obbligatoria) e Windows Defender Credential Guard.

HVCI obbligatoria è una potente mitigazione del sistema che protegge i processi in modalità kernel di Windows dall'inserimento e dall'esecuzione di codice dannoso o non verificato. Controlla i driver e i file binari in modalità kernel prima di eseguirli, impedendo il caricamento in memoria dei file non firmati. Ciò garantisce che tale codice eseguibile non possa essere modificato dopo che è stato consentito il caricamento. Per ulteriori informazioni su VBS e HVCI obbligatoria, vedere la pagina relativa alla [sicurezza basata su virtualizzazione (VBS) e l'integrità del codice applicato da hypervisor (hvci obbligatoria)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571).

Con avvio attendibile e VBS è possibile abilitare Windows Defender Credential Guard. Questa funzionalità consente di isolare e proteggere i segreti in modo che solo il software di sistema con privilegi possa accedervi. Consente di impedire l'accesso non autorizzato a segreti e attacchi di furto di credenziali, ad esempio gli attacchi pass-the-hash (PtH). Per altre informazioni, vedere [Credential Guard](https://docs.microsoft.com/windows/security/identity-protection/credential-guard/credential-guard).


## <a name="security-center-integration"></a>Integrazione nel Centro sicurezza

L'avvio attendibile è integrato nel centro sicurezza di Azure per garantire che le macchine virtuali siano configurate correttamente. Il Centro sicurezza di Azure valuterà continuamente le macchine virtuali compatibili ed emetterà raccomandazioni pertinenti.

- **Raccomandazione per abilitare l'avvio protetto** : questa raccomandazione si applica solo alle macchine virtuali che supportano l'avvio attendibile. Il Centro sicurezza di Azure identificherà le VM che possono abilitare l'avvio protetto, ma è disabilitato. Verrà rilasciata una raccomandazione con gravità bassa per abilitarla.
- **Raccomandazione per abilitare vTPM** : se la macchina virtuale ha vTPM abilitata, il Centro sicurezza di Azure può usarlo per eseguire l'attestazione Guest e identificare modelli di minacce avanzati. Se il Centro sicurezza di Azure identifica le macchine virtuali che supportano l'avvio attendibile e vTPM disabilitate, viene rilasciata una raccomandazione con gravità basso per abilitarla. 
- **Valutazione dell'integrità di attestazione** : se la macchina virtuale è abilitata per vTPM, un'estensione del Centro sicurezza di Azure può convalidare in modalità remota la macchina virtuale avviata in modo integro. Questa operazione è nota come attestazione remota. Il Centro sicurezza di Azure rilascia una valutazione che indica lo stato dell'attestazione remota.

## <a name="azure-defender-integration"></a>Integrazione di Azure Defender

Se le macchine virtuali sono configurate correttamente con avvio attendibile, Azure Defender può rilevare e segnalare problemi di integrità delle macchine virtuali.

- **Avviso per errore di attestazione della macchina virtuale** : Azure Defender eseguirà periodicamente l'attestazione sulle VM. Questo problema si verifica anche dopo l'avvio della macchina virtuale. Se l'attestazione ha esito negativo, verrà attivato un avviso con livello di gravità medio.
    L'attestazione della macchina virtuale può non riuscire per i motivi seguenti:
    - Le informazioni attestate, che includono un log di avvio, differiscono da una baseline attendibile. Questo può indicare che i moduli non attendibili sono stati caricati e che il sistema operativo potrebbe essere compromesso.
    - Non è stato possibile verificare l'offerta di attestazione per avere origine dalla vTPM della VM attestata. Questo può indicare che il malware è presente e potrebbe intercettare il traffico verso la vTPM.
    - L'estensione di attestazione nella macchina virtuale non risponde. Questo può indicare un attacco Denial of Service da parte di malware o un amministratore del sistema operativo.

    > [!NOTE]
    >  Questo avviso è disponibile per le macchine virtuali con vTPM abilitato e l'estensione di attestazione installata. L'avvio protetto deve essere abilitato per il superamento dell'attestazione. L'attestazione avrà esito negativo se l'avvio protetto è disabilitato. Se è necessario disabilitare l'avvio protetto, è possibile eliminare questo avviso per evitare falsi positivi.

- **Avviso per un modulo kernel Linux non attendibile** : per l'avvio attendibile con avvio protetto abilitato, è possibile che una macchina virtuale venga avviata anche se un driver del kernel non riesce a eseguire la convalida ed è vietato il caricamento. In tal caso, Azure Defender emetterà un avviso con livello di gravità basso. Sebbene non esista una minaccia immediata, perché il driver non attendibile non è stato caricato, questi eventi devono essere esaminati. Considerare quanto segue:
    - Quale driver del kernel ha avuto esito negativo? Si ha familiarità con questo driver e si prevede che venga caricato?
    - Si tratta della versione esatta del driver previsto? I file binari del driver sono intatti? Se si tratta di un driver di terze parti, il fornitore ha superato i test di conformità del sistema operativo per ottenere la firma?


## <a name="faq"></a>Domande frequenti

Domande frequenti sul lancio attendibile.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Perché utilizzare l'avvio attendibile? Che cosa fa l'avvio di Guard attendibile?

Protezione da avvio attendibile contro i kit di avvio, i rootkit e il malware a livello di kernel. Questi tipi avanzati di malware vengono eseguiti in modalità kernel e rimangono nascosti agli utenti. Ad esempio:
- Rootkit del firmware: questi kit sovrascrivono il firmware del BIOS della macchina virtuale, in modo che il rootkit possa iniziare prima del sistema operativo. 
- Kit di avvio: questi kit sostituiscono il bootloader del sistema operativo in modo che la macchina virtuale carichi il kit di avvio prima del sistema operativo.
- Rootkit kernel: questi kit sostituiscono una parte del kernel del sistema operativo in modo che il rootkit possa avviarsi automaticamente quando il sistema operativo viene caricato.
- Rootkit driver: questi kit fingono di essere uno dei driver attendibili usati dal sistema operativo per comunicare con i componenti della macchina virtuale.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Quali sono le differenze tra avvio protetto e avvio con misurazione?

Nella catena di avvio protetta ogni passaggio del processo di avvio controlla una firma crittografica dei passaggi successivi. Ad esempio, il BIOS controllerà una firma sul caricatore e il caricatore controllerà le firme per tutti gli oggetti kernel caricati e così via. Se uno degli oggetti viene compromesso, la firma non corrisponde e la macchina virtuale non viene avviata. Per altre informazioni, vedere [Avvio protetto](/windows-hardware/design/device-experiences/oem-secure-boot). L'avvio con misurazioni non interrompe il processo di avvio, misura o calcola l'hash degli oggetti successivi nella catena e archivia gli hash nei registri di configurazione della piattaforma (PCRs) in vTPM. I record di avvio misurati vengono usati per il monitoraggio dell'integrità di avvio.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>Cosa accade quando viene rilevato un errore di integrità?

Il lancio attendibile per le macchine virtuali di Azure viene monitorato per le minacce avanzate. Se vengono rilevate tali minacce, verrà generato un avviso. Gli avvisi sono disponibili solo nel [livello standard](/azure/security-center/security-center-pricing) del Centro sicurezza di Azure.
Il Centro sicurezza di Azure esegue periodicamente l'attestazione. Se l'attestazione non riesce, verrà generato un avviso con livello di gravità medio. L'attestazione dell'avvio attendibile può non riuscire per i motivi seguenti: 
- Le informazioni attestate, che includono un log di Trusted computing base (TCB), differiscono da una baseline attendibile (ad esempio, quando è abilitato l'avvio protetto). Questo può indicare che i moduli non attendibili sono stati caricati e che il sistema operativo potrebbe essere compromesso.
- Non è stato possibile verificare l'offerta di attestazione per avere origine dalla vTPM della VM attestata. Questo può indicare che il malware è presente ed è possibile che stia intercettando il traffico al TPM. 
- L'estensione di attestazione nella macchina virtuale non risponde. Questo può indicare un attacco Denial of Service da parte di malware o un amministratore del sistema operativo.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>In che modo viene eseguito l'avvio attendibile rispetto alla VM schermata Hyper-V?
La VM schermata Hyper-V è attualmente disponibile solo in Hyper-V. La [VM schermata Hyper-V](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) viene in genere distribuita insieme all'infrastruttura sorvegliata. Un'infrastruttura sorvegliata è costituita da un servizio sorveglianza host (HGS), da uno o più host sorvegliati e da un set di macchine virtuali schermate. Le macchine virtuali schermate Hyper-V sono destinate all'uso in infrastrutture in cui i dati e lo stato della macchina virtuale devono essere protetti dagli amministratori dell'infrastruttura e da software non attendibile che potrebbero essere in esecuzione negli host Hyper-V. Il lancio attendibile d'altro canto può essere distribuito come macchina virtuale autonoma o set di scalabilità di macchine virtuali in Azure senza la distribuzione e la gestione di HGS aggiuntive. Tutte le funzionalità di avvio attendibili possono essere abilitate con una semplice modifica nel codice di distribuzione o in una casella di controllo nel portale di Azure.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Come è possibile convertire le macchine virtuali esistenti in un avvio attendibile?
Per la macchina virtuale di seconda generazione, il percorso di migrazione da convertire a avvio attendibile è destinato a disponibilità generale (GA).

## <a name="next-steps"></a>Passaggi successivi

Distribuire una [VM di avvio attendibile usando il portale](trusted-launch-portal.md).
