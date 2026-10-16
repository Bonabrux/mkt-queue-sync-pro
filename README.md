# Mikrotik Queue Sync PRO

UISP (UCRM) plugin that keeps Mikrotik routers in sync with your service plans: speeds, suspensions and address-lists.
Complemento de UISP (UCRM) que mantiene los routers Mikrotik sincronizados con sus planes de servicio: velocidades, suspensiones y address-lists.

**Download / Descarga:** [latest release / última versión](https://github.com/Bonabrux/mkt-queue-sync-pro/releases/latest) — a license is required / se requiere una licencia: **plugins@franco.ar**

## Features / Funciones

* **Simple queues** with max-limit, limit-at, burst and priority from each service plan, respecting traffic shaping overrides.
  **Colas simples** con max-limit, limit-at, ráfaga y prioridad de cada plan, respetando los overrides de velocidad.
* **Multiple routers**, each one optionally limited to its own IP ranges (`sync_with_ucrm` address-list).
  **Varios routers**, cada uno opcionalmente limitado a sus rangos de IP (address-list `sync_with_ucrm`).
* **Unlimited services.** / **Servicios ilimitados.**
* **Suspension:** suspended services go to the `BLOCKED_USERS_Synced` address-list, with the NAT/filter rules that redirect them to your suspension page.
  **Suspensión:** los servicios suspendidos van al address-list `BLOCKED_USERS_Synced`, con las reglas NAT/filter que los redirigen a su página de suspensión.
* **Address-lists** by service plan and of active services. / **Address-lists** por plan y de servicios activos.
* **Remove ended services** queues. / **Borrado de colas** de servicios terminados.
* **Automatic sync on service edit** (UISP webhook). / **Sincronización automática al editar un servicio** (webhook de UISP).
* **Duplicated queues** report and cleanup. / **Colas duplicadas:** informe y limpieza.
* **Parent and dynamic queues (PPPoE, hotspot) are never modified.** Queue names stay updated when UISP changes the service ID.
  **Las colas padre y dinámicas (PPPoE, hotspot) nunca se modifican.** Los nombres de las colas se actualizan cuando UISP cambia el ID del servicio.
* Router charset support (Cyrillic and other alphabets). / Soporte de codificación del router (cirílico y otros alfabetos).

| | [Free](https://github.com/Bonabrux/mkt-queue-sync) | PRO |
| --- | :---: | :---: |
| Queue sync / Sincronización de colas | ✔ | ✔ |
| Routers | 1 | Unlimited / Ilimitados |
| Services / Servicios | 100 | Unlimited / Ilimitados |
| Webhook, suspension, address-lists, remove ended, duplicates cleanup / Webhook, suspensión, address-lists, borrado de terminados, limpieza de duplicados | | ✔ |

## Installation / Instalación

1. Download `mkt-queue-sync-pro.zip` from the [latest release](https://github.com/Bonabrux/mkt-queue-sync-pro/releases/latest) and upload it in UISP: System > Plugins.
   Descargue `mkt-queue-sync-pro.zip` desde la [última versión](https://github.com/Bonabrux/mkt-queue-sync-pro/releases/latest) y súbalo en UISP: Sistema > Complementos.
2. Configure and enable the plugin. / Configure y habilite el complemento.
3. Open **Mikrotik Sync Pro > Activate plugin / Activar complemento**, send the **Installation ID** to plugins@franco.ar and paste the license you receive.
   Abra **Mikrotik Sync Pro > Activate plugin / Activar complemento**, envíe el **ID de instalación** a plugins@franco.ar y pegue la licencia que reciba.
4. Enable automatic execution or run **Sync / Sincronizar** from the menu. Optional: create a UISP webhook for `service.edit` events with the plugin public URL.
   Active la ejecución automática o ejecute **Sync / Sincronizar** desde el menú. Opcional: cree un webhook de UISP para eventos `service.edit` con la URL pública del complemento.

Updating: upload the new zip over the installed plugin, the license and settings are kept.
Actualización: suba el nuevo zip sobre el complemento instalado, la licencia y la configuración se conservan.

## Requirements / Requisitos

* UISP / UCRM 3.3.11+ (UNMS 2.2.0+), Mikrotik RouterOS with API enabled (`/ip service`, default port 8728).
  UISP / UCRM 3.3.11+ (UNMS 2.2.0+), Mikrotik RouterOS con la API habilitada (`/ip service`, puerto por defecto 8728).
* UNMS API token (UNMS > Settings > Users) when using UISP / UNMS. / Token API de UNMS (UNMS > Settings > Users) al usar UISP / UNMS.

## Contact / Contacto

Licenses, support and questions / Licencias, soporte y consultas: **plugins@franco.ar**
