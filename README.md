# Mikrotik Queue Sync PRO

UISP (UCRM) plugin that keeps your Mikrotik routers in sync with your service plans: speeds, suspensions and address-lists.
Complemento de UISP (UCRM) que mantiene sus routers Mikrotik sincronizados con sus planes de servicio: velocidades, suspensiones y address-lists.

**Download / Descarga:** [latest release / última versión](https://github.com/Bonabrux/mkt-queue-sync-pro/releases/latest) — a license is required / se requiere una licencia: **plugins@franco.ar**

## Free vs PRO / Gratuita vs PRO

| Feature / Función | [Free](https://github.com/Bonabrux/mkt-queue-sync) | PRO |
| --- | :---: | :---: |
| Queue sync: max-limit, limit-at, burst, priority / Sincronización de colas: max-limit, limit-at, ráfaga, prioridad | ✔ | ✔ |
| Traffic shaping overrides / Overrides de velocidad | ✔ | ✔ |
| Parent and dynamic queues protection / Protección de colas padre y dinámicas | ✔ | ✔ |
| Queue names updated when the service ID changes / Nombres actualizados cuando cambia el ID del servicio | ✔ | ✔ |
| Selective sync (`sync_with_ucrm`) / Sincronización selectiva | ✔ | ✔ |
| Router charset (Cyrillic and others) / Codificación del router (cirílico y otros) | ✔ | ✔ |
| Routers | 1 | Unlimited / Ilimitados |
| Services / Servicios | 100 | Unlimited / Ilimitados |
| Automatic sync on service edit (webhook) / Sincronización al editar un servicio (webhook) | | ✔ |
| Suspension: blocked users address-list + redirect/block rules / Suspensión: address-list de bloqueados + reglas de redirección/bloqueo | | ✔ |
| Address-list by service plan / Address-list por plan | | ✔ |
| Active services address-list / Address-list de servicios activos | | ✔ |
| Remove queues of ended services / Borrar colas de servicios terminados | | ✔ |
| Duplicated queues cleanup / Limpieza de colas duplicadas | | ✔ |

## How it works / Cómo funciona

* For each UISP service, the plugin finds the Mikrotik simple queue whose target is the service IP and updates max-limit, limit-at, burst-limit, burst-threshold, burst-time and priority. When a service has no queue, it is created (option *Add queues?*).
  Para cada servicio de UISP, el plugin busca la cola simple del Mikrotik cuyo target es la IP del servicio y actualiza max-limit, limit-at, burst-limit, burst-threshold, burst-time y prioridad. Si un servicio no tiene cola, se crea (opción *¿Agregar colas?*).
* Service IPs come from the UISP devices linked to the service (UCRM without UNMS: the service IP range). A service with several devices gets one queue with all its IPs.
  Las IPs del servicio salen de los dispositivos de UISP vinculados al servicio (UCRM sin UNMS: el rango de IP del servicio). Un servicio con varios dispositivos tiene una cola con todas sus IPs.
* Services synced: Prepared, Active, Suspended, Prepared blocked and Deferred.
  Servicios sincronizados: Preparado, Activo, Suspendido, Preparado bloqueado y Diferido.
* Speeds: the service plan speed, or the traffic shaping override of the service when enabled (an empty override speed uses the plan speed). An empty speed means unlimited.
  Velocidades: la del plan, o el override de velocidad del servicio cuando está activado (un override vacío usa la velocidad del plan). Una velocidad vacía es ilimitada.
* Priority: the service plan **Data usage limit** (1-8) is used as queue priority, 8 when empty. Burst-threshold: the service plan burst values; when higher than the burst-limit, 15% under it (logged).
  Prioridad: el **Data usage limit** del plan (1-8) se usa como prioridad de la cola, 8 si está vacío. Burst-threshold: los valores de ráfaga del plan; si superan el burst-limit, 15% por debajo (se informa en el log).
* Queue names: `<first name> <last name> - Service ID:<id>` or `<company> - Service ID: <id>`. UISP changes the service ID when a service is edited: the queue is matched by IP and renamed, no duplicated queue is created. A queue is never renamed to a name already used on the router (the speeds are still updated and it is logged).
  Nombres de cola: `<nombre> <apellido> - Service ID:<id>` o `<empresa> - Service ID: <id>`. UISP cambia el ID del servicio al editarlo: la cola se encuentra por IP y se renombra, no se crea una cola duplicada. Nunca se renombra una cola a un nombre que ya existe en el router (las velocidades se actualizan igual y se informa en el log).
* **Parent queues (used as parent by another queue) and dynamic queues (PPPoE, hotspot) are never modified nor removed**, even if their target is a service IP or range.
  **Las colas padre (usadas como parent por otra cola) y las dinámicas (PPPoE, hotspot) nunca se modifican ni se borran**, aunque su target sea la IP o el rango de un servicio.
* Each run writes a detailed log in the plugin page (UISP > System > Plugins). The log is cleared at the start of each run.
  Cada ejecución escribe un log detallado en la página del complemento (UISP > Sistema > Complementos). El log se borra al comenzar cada ejecución.

## Installation / Instalación

1. Download `mkt-queue-sync-pro.zip` from the [latest release](https://github.com/Bonabrux/mkt-queue-sync-pro/releases/latest) and upload it in UISP: System > Plugins.
   Descargue `mkt-queue-sync-pro.zip` desde la [última versión](https://github.com/Bonabrux/mkt-queue-sync-pro/releases/latest) y súbalo en UISP: Sistema > Complementos.
2. Configure the plugin (see [Settings](#settings--configuración)) and enable it.
   Configure el complemento (ver [Configuración](#settings--configuración)) y habilítelo.
3. Activate it (see [License](#license--licencia)). / Actívelo (ver [Licencia](#license--licencia)).
4. Choose how it runs / Elija cómo se ejecuta:
   * **Automatic execution / Ejecución automática:** enable it in the plugin settings (UISP runs it periodically). / actívela en la configuración del complemento (UISP lo ejecuta periódicamente).
   * **Manual / Manual:** menu **Mikrotik Sync Pro > Sync / Sincronizar**.
   * **On service edit / Al editar un servicio:** create a webhook in UISP (System > Webhooks) with the plugin **public URL** (shown in the plugin page, ends with `/public.php`) and the event `service.edit`. The webhook test is accepted too.
     cree un webhook en UISP (Sistema > Webhooks) con la **URL pública** del complemento (se ve en la página del complemento, termina en `/public.php`) y el evento `service.edit`. La prueba del webhook también se acepta.

The Mikrotik user needs API access: `/ip service` api enabled (default port 8728) and a user with write permissions.
El usuario del Mikrotik necesita acceso a la API: `/ip service` api habilitado (puerto por defecto 8728) y un usuario con permisos de escritura.

## License / Licencia

1. Open **Mikrotik Sync Pro > Activate plugin / Activar complemento**: it shows the **Installation ID** (`XXXX-XXXX-XXXX-XXXX`).
   Abra **Mikrotik Sync Pro > Activate plugin / Activar complemento**: muestra el **ID de instalación** (`XXXX-XXXX-XXXX-XXXX`).
2. Send the Installation ID to **plugins@franco.ar**. You receive a license for that installation (perpetual or with an expiration date).
   Envíe el ID de instalación a **plugins@franco.ar**. Recibirá una licencia para esa instalación (perpetua o con fecha de vencimiento).
3. Paste the license and save: the page shows *Licensed to: …*. / Pegue la licencia y guarde: la página muestra *Licensed to: …*.

* The Installation ID never exposes your UISP credentials. / El ID de instalación nunca expone sus credenciales de UISP.
* **Updating** the plugin (uploading a new zip over the installed one) keeps the license and the settings. **Deleting** the plugin removes its activation: after installing it again, open *Activate plugin*; if the Installation ID changed, a new license is needed.
  **Actualizar** el complemento (subir un zip nuevo sobre el instalado) conserva la licencia y la configuración. **Borrar** el complemento elimina su activación: después de instalarlo de nuevo, abra *Activar complemento*; si el ID de instalación cambió, se necesita una licencia nueva.
* Activation keys of previous versions keep working. / Las claves de activación de versiones anteriores siguen funcionando.
* Without a valid license (or after its expiration date) the plugin does not sync and says so in the log.
  Sin una licencia válida (o después de su vencimiento) el complemento no sincroniza y lo informa en el log.

## Settings / Configuración

| Setting | Description | Descripción |
| --- | --- | --- |
| Mikrotik IP Address | One IP per line, one line per router | Una IP por línea, una línea por router |
| Mikrotik API port | One port per line, same order as the IPs (default 8728) | Un puerto por línea, en el mismo orden que las IPs (por defecto 8728) |
| Mikrotik username / password | One per line, same order as the IPs | Uno por línea, en el mismo orden que las IPs |
| Burst limit % | Over max-limit, `UU/DD` (upload/download), `0/0` = no burst | Sobre el max-limit, `UU/DD` (subida/bajada), `0/0` = sin ráfaga |
| Burst time | Seconds, `UU/DD`, values 1-99, `1/1` if not used | Segundos, `UU/DD`, valores 1-99, `1/1` si no se usa |
| Limit-at % | Over max-limit, `UU/DD`, values 0-99 | Sobre el max-limit, `UU/DD`, valores 0-99 |
| Add queues? | Create a queue for services without one | Crear una cola para los servicios que no tienen |
| Add address-list? | [Address-list by service plan](#address-lists) | [Address-list por plan](#address-lists) |
| Remove ended? | [Remove queues of ended services](#remove-ended-services--borrar-servicios-terminados) | [Borrar colas de servicios terminados](#remove-ended-services--borrar-servicios-terminados) |
| Duplicated queues | [Update all of them or keep the first one](#duplicated-queues--colas-duplicadas) | [Actualizar todas o conservar la primera](#duplicated-queues--colas-duplicadas) |
| Active services address-list? | [Address-list of active services](#address-lists) | [Address-list de servicios activos](#address-lists) |
| Suspend feature? | [Suspension](#suspension--suspensión) | [Suspensión](#suspension--suspensión) |
| Suspension page IP / port | Server that shows the suspension page | Servidor que muestra la página de suspensión |
| UNMS API token | Needed with UISP / UNMS v1+ (UNMS > Settings > Users) | Necesario con UISP / UNMS v1+ (UNMS > Settings > Users) |
| Mikrotik charset | Must match Winbox (e.g. Cyrillic = Windows-1251) | Debe coincidir con Winbox (ej. cirílico = Windows-1251) |
| Debug mode | More detailed log | Log más detallado |

* Example: limit-at 20% upload and 50% download = `20/50`. / Ejemplo: limit-at 20% de subida y 50% de bajada = `20/50`.
* **Selective sync / Sincronización selectiva:** create on a router an address-list `sync_with_ucrm` with the IPs or ranges that router handles (`10.0.0.0/24`, `10.0.0.1-10.0.0.50`); only those services are synced on it. Routers without that list sync every service.
  cree en un router un address-list `sync_with_ucrm` con las IPs o rangos que maneja ese router (`10.0.0.0/24`, `10.0.0.1-10.0.0.50`); solo esos servicios se sincronizan en él. Los routers sin esa lista sincronizan todos los servicios.

### Suspension / Suspensión

* Services in status Suspended, Prepared blocked, Ended, Obsolete, Prepared and Quoted are added to the address-list **`BLOCKED_USERS_Synced`**, and removed from it when they are reactivated. An IP used by an Active service is never blocked.
  Los servicios en estado Suspendido, Preparado bloqueado, Terminado, Obsoleto, Preparado y Cotizado se agregan al address-list **`BLOCKED_USERS_Synced`**, y se quitan cuando se reactivan. Nunca se bloquea una IP usada por un servicio Activo.
* With the suspension page IP and port set, the plugin creates and keeps these rules (comments starting with `ucrm_suspend_`):
  Con la IP y el puerto de la página de suspensión configurados, el plugin crea y mantiene estas reglas (comentarios que empiezan con `ucrm_suspend_`):
  * NAT: HTTP (port 80) of blocked users redirected to the suspension page. / NAT: HTTP (puerto 80) de los bloqueados redirigido a la página de suspensión.
  * Filter: traffic from the suspension server accepted, DNS allowed for blocked users, everything else from blocked users dropped. / Filter: tráfico desde el servidor de suspensión aceptado, DNS permitido para los bloqueados, el resto del tráfico de los bloqueados descartado.
* Without suspension page IP/port only the address-list is synced, so you can use it with your own rules.
  Sin IP/puerto de la página de suspensión solo se sincroniza el address-list, para usarlo con sus propias reglas.
* The rules are added at the end of the chains: check that no previous rule accepts the clients traffic before them.
  Las reglas se agregan al final de las cadenas: verifique que ninguna regla anterior acepte el tráfico de los clientes antes que ellas.

### Address-lists

* **By service plan / Por plan:** each service IP in an address-list named as its service plan (comment `ucrm_mktsync_<queue name>`). Useful for firewall rules or routing by plan.
  cada IP de servicio en un address-list con el nombre de su plan (comentario `ucrm_mktsync_<nombre de cola>`). Útil para reglas de firewall o ruteo por plan.
* **Active services / Servicios activos:** address-list **`ACTIVE_USERS_Synced`** with every synced service IP (comment `ucrm_ActiveServices_<queue name>`); ended services disappear from it.
  address-list **`ACTIVE_USERS_Synced`** con la IP de cada servicio sincronizado (comentario `ucrm_ActiveServices_<nombre de cola>`); los servicios terminados desaparecen de ella.
* Only entries created by the plugin (by their comment) are added or removed. / Solo se agregan o quitan las entradas creadas por el plugin (según su comentario).

### Remove ended services / Borrar servicios terminados

Queues of ended services are removed, unless that IP is already used by another synced service.
Se borran las colas de los servicios terminados, salvo que esa IP ya la use otro servicio sincronizado.

### Duplicated queues / Colas duplicadas

Several queues with the same target as a service (from older versions or manual changes) are always reported in the log. Option *Update all of them*: every one gets the new speeds and keeps its name. Option *Keep the first one, remove the rest*: the first one (the one Mikrotik uses) is kept.
Varias colas con el mismo target que un servicio (de versiones anteriores o cambios manuales) siempre se informan en el log. Opción *Actualizar todas*: todas reciben las velocidades nuevas y conservan su nombre. Opción *Conservar la primera y borrar el resto*: se conserva la primera (la que usa el Mikrotik).

## Requirements / Requisitos

* UISP / UCRM 3.3.11+ (UNMS 2.2.0+). / UISP / UCRM 3.3.11+ (UNMS 2.2.0+).
* Mikrotik RouterOS with API enabled, reachable from the UISP server. / Mikrotik RouterOS con la API habilitada, accesible desde el servidor de UISP.
* UNMS API token (UNMS > Settings > Users) when using UISP / UNMS. / Token API de UNMS (UNMS > Settings > Users) al usar UISP / UNMS.

## Contact / Contacto

Licenses, support and questions / Licencias, soporte y consultas: **plugins@franco.ar**

Free version / Versión gratuita: https://github.com/Bonabrux/mkt-queue-sync
