---
title: Introduzione allo strumento di snapshot coerente applicazione Azure per Azure NetApp Files | Microsoft Docs
description: Viene fornita una guida per l'installazione dello strumento applicazione Azure snapshot coerente che è possibile utilizzare con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736363"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Introduzione allo strumento di snapshot coerente con applicazione Azure (anteprima)

Questo articolo fornisce una guida per l'installazione dello strumento di snapshot coerente applicazione Azure che è possibile usare con Azure NetApp Files.

## <a name="getting-the-snapshot-tools"></a>Ottenere gli strumenti di snapshot

È consigliabile che i clienti ottengano la versione più recente del [programma di installazione di AzAcSnap](https://aka.ms/azacsnapdownload) da Microsoft.

Al file di installazione automatica è associato un [file di firma del programma](https://aka.ms/azacsnapdownloadsignature) di installazione di AzAcSnap, firmato con la chiave pubblica di Microsoft, per consentire la verifica GPG del programma di installazione scaricato.

Una volta completati i download, seguire i passaggi descritti in questa guida per installare.

### <a name="verifying-the-download"></a>Verifica del download

Il programma di installazione, che è scaricabile per le versioni precedenti, dispone di un file di firma PGP associato con un' `.asc` estensione del nome file. Questo file può essere usato per verificare che il programma di installazione scaricato sia un file fornito da Microsoft. La chiave pubblica PGP Microsoft usata per la firma dei pacchetti Linux è disponibile qui ( <https://packages.microsoft.com/keys/microsoft.asc> ) ed è stata usata per firmare il file di firma.

La chiave pubblica PGP Microsoft può essere importata nel locale di un utente, come indicato di seguito:

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

I seguenti comandi considerano attendibile la chiave pubblica PGP Microsoft:

1. Elencare le chiavi nell'archivio.
2. Modificare la chiave Microsoft.
3. Controllare l'impronta digitale con `fpr`
4. Firmare la chiave per considerarla attendibile.

```bash
gpg --list-keys
```

Chiavi elencate:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Output della `gpg` chiave pubblica di firma della sessione interattiva Microsoft:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

Il file della firma PGP per il programma di installazione può essere controllato come segue:

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

Per altri dettagli sull'uso di GPG, vedere [il manuale della privacy di GNU](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Scenari supportati

Gli strumenti di snapshot possono essere usati negli scenari seguenti.

- SID singolo
- SID multipli
- HSR
- Aumento delle istanze
- MDC (solo singolo tenant supportato)
- Contenitore singolo
- Sistema operativo SUSE
- Sistema operativo RHEL
- TIPO DI SKU I
- TIPO DI SKU II

Vedere gli [scenari supportati per le istanze large di Hana](../virtual-machines/workloads/sap/hana-supported-scenario.md)

## <a name="snapshot-support-matrix-from-sap"></a>Matrice di supporto snapshot da SAP

La matrice seguente viene fornita come linea guida per le versioni di SAP HANA supportate da SAP per i backup degli snapshot di archiviazione.

| Versioni del database       |1,0 SPS12|2,0 SPS0|2,0 SPS1|2,0 SPS2|2,0 SPS3|2,0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Database contenitore singolo| √       | √      | -      | -      | -      | -      |
|Tenant singolo di MDC        | -       | -      | √      | √      | √      | √      |
|Tenant multi-tenant     | -       | -      | -      | -      | -      | √      |
> √ = <small>supportato da SAP per gli snapshot di archiviazione</small>

## <a name="important-things-to-remember"></a>Aspetti importanti da ricordare

- Dopo la configurazione degli strumenti per gli snapshot, monitorare continuamente lo spazio di archiviazione disponibile e, se necessario, eliminare gli snapshot precedenti a intervalli regolari per evitare la compilazione dello spazio di archiviazione.
- Usare sempre gli strumenti di snapshot più recenti.
- Usare la stessa versione degli strumenti di snapshot nel panorama.
- Testare gli strumenti di snapshot e familiarizzare con i parametri necessari e l'output del comando prima di usare nel sistema di produzione.
- Quando si configura l'utente HANA per il backup (dettagli più avanti in questo documento), è necessario configurare l'utente per ogni istanza di HANA. Creare un account utente SAP HANA per accedere all'istanza di HANA in SYSTEMDB (e non nel database SID) per MDC. Nell'ambiente contenitore singolo può essere configurato nel database tenant.
- I clienti devono fornire la chiave pubblica SSH per l'accesso alla risorsa di archiviazione. Questa azione deve essere eseguita una volta per ogni nodo e per ogni utente in cui viene eseguito il comando.
- Il numero di snapshot per volume è limitato a 250.
- Se si modifica manualmente il file di configurazione, usare sempre un editor di testo di Linux, ad esempio "vi" e non gli editor di Windows come il blocco note. L'uso dell'editor di Windows può danneggiare il formato del file.
  - Configurare `hdbuserstore` per l'utente SAP HANA per la comunicazione con SAP Hana.
- Per il ripristino di emergenza: gli strumenti di snapshot devono essere testati nel nodo di ripristino di emergenza prima della configurazione di DR.
- Eseguire regolarmente il monitoraggio dello spazio su disco, l'eliminazione automatica dei log viene gestita con l' `--trim` opzione del   `azacsnap -c backup` per SAP Hana 2 e versioni successive.
- **Rischio di snapshot non eseguito** : gli strumenti di snapshot interagiscono solo con il nodo del sistema di SAP Hana specificato nel file di configurazione.  Se il nodo non è più disponibile, non esiste alcun meccanismo per iniziare a comunicare automaticamente con un altro nodo.  
  - Per un **SAP HANA Scale-Out con** uno scenario di standby, è tipico installare e configurare gli strumenti di snapshot nel nodo master. Tuttavia, se il nodo master diventa non disponibile, il nodo standby assumerà il ruolo di nodo master. In questo caso, il team di implementazione deve configurare gli strumenti di snapshot in entrambi i nodi (Master e stand-by) per evitare gli snapshot mancanti. Nello stato normale, il nodo master utilizzerà gli snapshot HANA avviati da crontab, ma dopo il failover del nodo master sarà necessario eseguire tali snapshot da un altro nodo, ad esempio il nuovo nodo master (primo standby). Per ottenere questo risultato, per il nodo standby è necessario che lo strumento snapshot sia installato, che la comunicazione di archiviazione sia abilitata, hdbuserstore configurata, `azacsnap.json` configurata e che i comandi crontab siano gestiti in anticipo del failover.
  - Per uno scenario a **disponibilità elevata SAP Hana HSR** , è consigliabile installare, configurare e pianificare gli strumenti di snapshot nei nodi (primari e secondari). Quindi, se il nodo primario non è più disponibile, il nodo secondario assumerà il sopravvento con gli snapshot presi sul database secondario. Nello stato normale, il nodo primario utilizzerà gli snapshot HANA avviati da crontab e il nodo secondario tenterà di creare gli snapshot, ma avrà esito negativo perché il database primario funziona correttamente.  Tuttavia, dopo il failover del nodo primario, tali snapshot verranno eseguiti dal nodo secondario. Per ottenere questo risultato, per il nodo secondario è necessario che lo strumento snapshot sia installato, che la comunicazione di archiviazione sia abilitata, `hdbuserstore` configurata, azacsnap.jsconfigurata e che il crontab sia abilitato prima del failover.

## <a name="guidance-provided-in-this-document"></a>Indicazioni fornite in questo documento

Per illustrare l'utilizzo degli strumenti di snapshot, vengono fornite le indicazioni seguenti.

### <a name="taking-snapshot-backups"></a>Esecuzione di backup di snapshot

- [Quali sono i prerequisiti per lo snapshot di archiviazione](azacsnap-installation.md#prerequisites-for-installation)
  - [Abilitare la comunicazione con l'archiviazione](azacsnap-installation.md#enable-communication-with-storage)
  - [Abilitare la comunicazione con SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana)
- [Come eseguire manualmente gli snapshot](azacsnap-tips.md#take-snapshots-manually)
- [Come configurare il backup automatico degli snapshot](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [Come monitorare gli snapshot](azacsnap-tips.md#monitor-the-snapshots)
- [Come eliminare uno snapshot](azacsnap-tips.md#delete-a-snapshot)
- [Come ripristinare uno snapshot](azacsnap-tips.md#restore-a-snapshot)
- [Come ripristinare uno `boot` snapshot](azacsnap-tips.md#restore-a-boot-snapshot)
- [Cosa sono i fatti chiave per conoscere gli snapshot](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> Gli snapshot vengono testati sia per SID singolo che per SID.

### <a name="performing-disaster-recovery"></a>Esecuzione del ripristino di emergenza

- [Quali sono i prerequisiti per l'installazione di ripristino di emergenza](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Come configurare un ripristino di emergenza](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [Come monitorare la replica dei dati dal sito primario al sito di ripristino di emergenza](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [Come eseguire un failover nel sito di ripristino di emergenza](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Passaggi successivi

- [Installare applicazione Azure strumento di snapshot coerente](azacsnap-installation.md)