---
title: Risolvere gli errori comuni - Database di Azure per MySQL
description: Informazioni su come risolvere gli errori di migrazione comuni rilevati dai nuovi utenti del servizio Database di Azure per MySQL
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ca75416a66bcf2c90028c7f1dc11fbe23a9a9bd9
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631368"
---
# <a name="common-errors"></a>Errori comuni

Database di Azure per MySQL è un servizio di database completamente gestito basato sulla versione Community di MySQL. L'esperienza MySQL in un ambiente di servizio gestito può risultare diversa rispetto all'esecuzione di MySQL in un ambiente personale. In questo articolo verranno illustrati alcuni degli errori comuni che gli utenti possono rilevare quando eseguono per la prima volta la migrazione o lo sviluppo nel servizio Database di Azure per MySQL.

## <a name="common-connection-errors"></a>Errori di connessione comuni

#### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>ERRORE 1184 (pari 08S01): la connessione 22 al database è stata interrotta:' nome-DB ' utente:' utente ' host:' hostIP ' (comando init_connect non riuscito)
L'errore precedente si verifica dopo l'accesso riuscito, ma prima di eseguire qualsiasi comando quando viene stabilita la sessione. Il messaggio precedente indica che è stato impostato un valore errato di init_connect parametro del server che causa l'esito negativo dell'inizializzazione della sessione.

Alcuni parametri del server come require_secure_transport non sono supportati a livello di sessione e quindi il tentativo di modificare i valori di questi parametri usando init_connect può generare l'errore 1184 durante la connessione al server MySQL, come illustrato di seguito.

MySQL> mostrare i database; ERRORE 2006 (HY000): il server MySQL non è più connesso. Tentativo di riconnessione in corso... ID connessione: 64897 database corrente: * * * nessuna * * _ errore 1184 (pari 08S01): connessione interrotta 22 al database:' nome-DB ' utente:' utente ' host:' hostIP ' (comando init_connect non riuscito)

_ *Risoluzione**: è necessario reimpostare init_connect valore nella scheda parametri server portale di Azure e impostare solo i parametri del server supportati usando init_connect parametro. 


## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Errori dovuti a mancanza del privilegio SUPER e del ruolo DBA

Il privilegio SUPER e il ruolo DBA non sono supportati nel servizio. È quindi possibile che si verifichino alcuni degli errori comuni elencati di seguito:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERRORE 1419: You do not have the SUPER privilege and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable) (Non è disponibile il privilegio SUPER e la registrazione binaria è abilitata. È consigliabile usare la variabile log_bin_trust_function_creators meno sicura)

È possibile che l'errore indicato si verifichi durante la creazione di una funzione, di un trigger come indicato di seguito o dell'importazione di uno schema. Le dichiarazioni DDL come CREATE FUNCTION o CREATE TRIGGER vengono scritte nel log binario, per consentire alla replica secondaria di eseguirle. Il thread SQL della replica ha privilegi completi, che possono essere sfruttati per elevare i privilegi. Per evitare questo rischio per i server in cui è abilitata la registrazione binaria, il motore di MySQL richiede agli autori di funzioni archiviate di avere il privilegio SUPER, oltre al privilegio CREATE ROUTINE consueto obbligatorio. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Soluzione**:  Per risolvere l'errore, impostare log_bin_trust_function_creators su 1 dal pannello [Parametri del server](howto-server-parameters.md) nel portale, eseguire le istruzioni DDL o importare lo schema per creare gli oggetti specifici e ripristinare il valore precedente del parametro log_bin_trust_function_creators dopo la creazione.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ERRORE 1227 (42000) alla riga 101: Access denied; you need (at least one of) the SUPER privilege(s) for this operation. Operation failed with exitcode 1 (Accesso negato. L'operazione richiede almeno uno dei privilegi SUPER. L'operazione non è riuscita con codice 1)

È possibile che l'errore precedente si verifichi durante l'importazione di un file di dump o durante la creazione di una procedura che contiene [definer](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Soluzione**:  per risolvere questo errore, l'utente amministratore può concedere privilegi per la creazione o l'esecuzione di procedure mediante l'esecuzione del comando GRANT, come indicato negli esempi seguenti:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
In alternativa è possibile sostituire i definer con il nome dell'utente amministratore che esegue il processo di importazione, come illustrato di seguito.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>ERRORE 1227 (42000) alla riga 295: Access denied; you need (at least one of) the SUPER or SET_USER_ID privilege(s) for this operation (Accesso negato. L'operazione richiede almeno uno dei privilegi SUPER o SET_USER_ID)

Questo errore può verificarsi durante l'esecuzione di script CREATE VIEW con istruzioni DEFINER come parte dell'importazione di un file dump o dell'esecuzione di uno script. Database di Azure per MySQL non concede ad alcun utente i privilegi SUPER o SET_USER_ID. 

**Risoluzione**: 
* Usare il definer utente per eseguire l'istruzione CREATE VIEW, se possibile. Probabilmente esistono numerose viste con definer diversi che hanno autorizzazioni diverse, quindi questa soluzione potrebbe non essere applicabile.  OR
* Modificare il file dump o lo script CREATE VIEW e rimuovere l'istruzione DEFINER= dal file dump OPPURE 
* Modificare il file dump o lo script CREATE VIEW e sostituire i valori dell'istruzione definer con l'utente con autorizzazioni di amministratore che sta eseguendo l'importazione o il file di script.

> [!Tip] 
> Usare sed o perl per modificare un file dump o uno script SQL per sostituire l'istruzione DEFINER=

## <a name="common-connection-errors-for-server-admin-login"></a>Errori di connessione comuni per l'accesso amministratore server

Durante la creazione di un server di Database di Azure per MySQL, l'utente finale fornisce un accesso amministratore server. Questo tipo di accesso consente di creare nuovi database, aggiungere nuovi utenti e concedere autorizzazioni. Se l'accesso amministratore server viene eliminato o se le relative autorizzazioni vengono revocate o la password viene cambiata, potrebbero iniziare a verificarsi errori di connessione nell'applicazione. Di seguito sono elencati alcuni degli errori comuni:

#### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>ERRORE 1045 (28000): Access denied for user 'username'@'IP address' (using password: YES) (Accesso negato per l'utente 'nomeutente'@'indirizzo IP' (uso di una password: SÌ))

Questo errore si verifica se:

* Il nome utente non esiste
* Il nome utente dell'utente è stato eliminato
* La password è stata cambiata o reimpostata.

**Risoluzione**: 
* Verificare se il nome utente esiste come utente valido nel server o se è stato eliminato accidentalmente. È possibile eseguire la query seguente accedendo all'account dell'utente di Database di Azure per MySQL:
  ```sql
  select user from mysql.user;
  ```
* Se non è possibile accedere a MySQL per eseguire la query, è consigliabile [reimpostare la password di amministratore usando il portale di Azure](howto-create-manage-server-portal.md). L'opzione di reimpostazione della password dal portale di Azure consentirà di ricreare l'utente, reimpostare la password e ripristinare le autorizzazioni di amministratore, in modo da eseguire l'accesso con le credenziali di amministratore del server ed eseguire ulteriori operazioni.

## <a name="next-steps"></a>Passaggi successivi
Se la risposta cercata non è stata trovata, prendere in considerazione quanto segue:

- Pubblicare la domanda nella [pagina di domande di Microsoft Q&A](/answers/topics/azure-database-mysql.html) o in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Inviare un messaggio di posta elettronica al team di Database di Azure per MySQL [@Ask Database di Azure per MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Questo indirizzo di posta elettronica non è un alias del supporto tecnico.
- Contattare il supporto di Azure, [creando un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Per risolvere un problema relativo all'account, inviare una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.
- Per fornire commenti e suggerimenti o richiedere nuove funzionalità, creare una richiesta tramite [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
