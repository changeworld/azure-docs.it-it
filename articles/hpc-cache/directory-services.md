---
title: Usare i gruppi estesi nella cache HPC di Azure
description: Come configurare i servizi di directory per l'accesso client a destinazioni di archiviazione nella cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/22/2020
ms.author: v-erkel
ms.openlocfilehash: 28265861c98cceaedf7d2662f6526a9f62fe68de
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803766"
---
# <a name="configure-directory-services"></a>Configurare i servizi di directory

Le impostazioni dei **servizi directory** consentono alla cache HPC di Azure di usare un'origine esterna per autenticare gli utenti per l'accesso all'archiviazione back-end.

Potrebbe essere necessario abilitare **gruppi estesi** se il flusso di lavoro include destinazioni di archiviazione NFS e client che sono membri di più di 16 gruppi.

Dopo aver fatto clic sul pulsante per abilitare i gruppi estesi, è necessario scegliere l'origine che la cache HPC di Azure utilizzerà per ottenere le credenziali utente e di gruppo.

* [Active Directory](#configure-active-directory) ottenere le credenziali da un server di Active Directory esterno. Non è possibile usare Azure Active Directory per questa attività.
* [File flat](#configure-file-download) : scaricare `/etc/group` e `/etc/passwd` file da un percorso di rete.
* [LDAP](#configure-ldap) -ottenere le credenziali da un'origine compatibile con LDAP (Lightweight Directory Access Protocol).

> [!NOTE]
> Assicurarsi che la cache possa accedere alla relativa origine informazioni di gruppo dall'interno della subnet protetta.<!-- + details/examples -->

Il campo **nome utente scaricato** Mostra lo stato del download più recente delle informazioni sul gruppo.

![screenshot della pagina delle impostazioni della pagina dei servizi directory nel portale, con l'opzione Sì selezionata per i gruppi estesi e il menu a discesa con etichetta Scarica origine aperta](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Configurare Active Directory

In questa sezione viene illustrato come configurare la cache per ottenere le credenziali di utenti e gruppi da un server di Active Directory esterno (AD).

In **Dettagli Active Directory** specificare i valori seguenti:

* **DNS primario** : specificare l'indirizzo IP di un Domain Name Server che la cache può usare per risolvere il nome di dominio ad.

* **DNS secondario** (facoltativo): immettere l'indirizzo di un server dei nomi da usare se il server primario non è disponibile.

* **Nome di dominio DNS ad** : specificare il nome di dominio completo del server di Active Directory a cui si aggiungerà la cache per ottenere le credenziali.

* **Nome server cache (account computer)** : impostare il nome che verrà assegnato a questa cache HPC quando viene aggiunto al dominio ad. Specificare un nome facilmente riconoscibile come questa cache. Il nome può contenere un massimo di 15 caratteri e può includere lettere maiuscole, minuscole, numeri e trattini (-).

Nella sezione **Credentials (credenziali** ) specificare un nome utente e una password di amministratore di Active Directory che possono essere usati dalla cache HPC di Azure per accedere al server ad. Queste informazioni vengono crittografate quando vengono archiviate e non possono essere sottoposte a query.

Salvare le impostazioni facendo clic sul pulsante nella parte superiore della pagina.

![screenshot della sezione dei dettagli del download con Active Directory valori inseriti](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Configurare il download del file

Questi valori sono necessari se si desidera scaricare file con le informazioni relative a utenti e gruppi. I file devono essere nel formato Linux/UNIX standard `/etc/group` `/etc/passwrd` .

* **URI file utente** : immettere l'URI completo per il `/etc/passwrd` file.
* **URI del file di gruppo** : immettere l'URI completo per il `/etc/group` file.

![screenshot della sezione Dettagli download per un download di file flat](media/group-download-details-file.png)

## <a name="configure-ldap"></a>Configurare LDAP

Specificare questi valori se si vuole usare un'origine LDAP non AD per ottenere le credenziali di utenti e gruppi. Se è necessario assistenza con questi valori, rivolgersi all'amministratore LDAP.

* **Server LDAP** : immettere il nome di dominio completo o l'indirizzo IP del server LDAP da usare. <!-- only one, not up to 3 -->

* **DN di base LDAP** : specificare il nome distinto di base per il dominio LDAP, in formato DN. Se non si conosce il DN di base, rivolgersi all'amministratore LDAP.

Il server e il DN di base sono le uniche impostazioni necessarie per eseguire il lavoro LDAP, ma le opzioni aggiuntive rendono più sicura la connessione.

![screenshot dell'area di configurazione LDAP della pagina Impostazioni pagina servizi directory](media/group-download-details-ldap.png)

Nella sezione **accesso sicuro** è possibile abilitare la crittografia e la convalida dei certificati per la connessione LDAP. Dopo aver fatto clic su **Sì** per abilitare la crittografia, sono disponibili le opzioni seguenti:

* **Richiedi certificato valido** : quando è impostato, il certificato del server LDAP viene verificato rispetto all'autorità di certificazione nel campo URI sottostante.

* **URI del certificato CA** : specificare il percorso del certificato autorevole. Può trattarsi di un collegamento a un certificato convalidato dall'autorità di certificazione o a un certificato autofirmato. Questo campo è necessario per usare l'impostazione certificati convalidati esternamente.

* **Scaricare automaticamente il certificato** : scegliere **Sì** se si vuole provare a scaricare un certificato non appena si inviano queste impostazioni.

Compilare la sezione **Credentials (credenziali** ) se si desidera utilizzare credenziali statiche per la sicurezza LDAP.

* **Binding DN** : immettere il nome distinto di binding da usare per l'autenticazione al server LDAP. (Usare il formato DN).
* **Password di binding** : specificare la password per il DN di binding.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'accesso client, vedere [montare la cache HPC di Azure](hpc-cache-mount.md)
* Se le credenziali non vengono scaricate correttamente, rivolgersi all'amministratore per l'origine delle credenziali. Se necessario, aprire un [ticket di supporto](hpc-cache-support-ticket.md) .
