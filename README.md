
# Lab_02: MFA en Microsoft Entra ID — Operación Moroso

## 1. Descripción del proyecto

Tras desplegar la infraestructura de identidad híbrida de Mirador de Montepinar SL, Antonio Recio ha decidido que ya no se puede fiar ni de sus propios vecinos.

Con el moroso de la comunidad todavía en paradero desconocido, Antonio quiere asegurarse de que nadie pueda hacerse pasar por otro vecino para acceder a los recursos de la empresa. Para ello, encarga la implantación de la autenticación multifactor (MFA) en Microsoft Entra ID.

El objetivo de este laboratorio es configurar y comprobar mecanismos de autenticación multifactor para las cuentas de prueba del tenant, estudiar los métodos de autenticación disponibles y documentar cómo MFA contribuye a reducir el riesgo de accesos no autorizados.

**Objetivos técnicos:**
- Revisar la configuración de seguridad y autenticación del tenant de Microsoft Entra ID.
- Configurar y probar MFA con las opciones disponibles en el entorno de laboratorio.
- Verificar el comportamiento del inicio de sesión y documentar los resultados.
- Analizar las limitaciones de la configuración aplicada y las posibles mejoras mediante políticas de acceso condicional.

**Escenario ficticio:** Mirador de Montepinar SL, donde Antonio Recio quiere controlar quién entra en el sistema casi tanto como quién debe las cuotas de la comunidad.

## Fases de Implementación

Fase 1: Identidad Híbrida y SincronizaciónDirectorio local: Creación automatizada mediante PowerShell de las identidades de la junta directiva (arecio, mfigueroa) bajo la Unidad Organizativa Usuarios y miembros del grupo Directiva-Montepinar.Sincronización: Vinculación con Microsoft Entra ID mediante Microsoft Entra Connect utilizando sincronización de hash de contraseñas (Password Hash Synchronization - PHS).Consistencia de atributos: Normalización del Nombre Principal de Usuario (UPN) hacia el dominio malastro.onmicrosoft.com y enriquecimiento de metadatos corporativos (cargo, departamento, contacto).

Fase 2: Resiliencia IAM y Cuentas Break-GlassSiguiendo las directrices del NIST y Microsoft para la mitigación del riesgo de pérdida total de acceso (tenant lockout):Se crearon dos cuentas nativas en la nube (bg-conserje1 y bg-conserje2) excluidas de cualquier federación o dependencia de la infraestructura local.Se les asignó de forma directa y permanente el rol de Administrador Global.Quedaron agrupadas bajo BreakGlass-Montepinar, garantizando que puedan ser excluidas de directivas que impongan autenticaciones de terceros o dependientes de dispositivos móviles.

Fase 3: Endurecimiento contra Ataques de MFA FatiguePara neutralizar vectores de compromiso basados en inundación de solicitudes push (Push Bombing / MFA Fatigue):Coincidencia de números (Number Matching): Se exige ingresar en la aplicación móvil el valor numérico aleatorio bidígito mostrado en la pantalla de inicio de sesión.Contexto de aplicación: La notificación emergente detalla explícitamente qué servicio o recurso solicita la autenticación (evitando inicios de sesión cruzados inadvertidos).Contexto geográfico: Se informa de la ciudad, región y dirección IP de origen, permitiendo al usuario identificar accesos desde ubicaciones anómalas.

Fase 4: Directiva de Acceso Condicional (Zero Trust)Implementación de la política CA-Montepinar-MFA:Asignaciones (Scope):Incluidos: Grupo Directiva-Montepinar.Excluidos: Grupo BreakGlass-Montepinar (Prevención de lockout).Recursos de destino: Todos los recursos (Todas las aplicaciones en la nube).Controles de concesión (Grant Controls): Conceder acceso > Requerir autenticación multifactor.Estrategia de despliegue: Configuración inicial en Solo informe (Report-only) para auditar el impacto sin degradar la disponibilidad del servicio.Vista de ExclusionesControl de Concesión y Despliegue Seguro

🔍 Validación, Simulación y Auditoría Forense

1. Simulación Lógica con la Herramienta What IfAntes del paso a producción, se sometieron las identidades al motor de inferencia de Entra ID:Caso 1: Antonio Recio (arecio)Resultado: Directiva CA-Montepinar-MFA aplicada. El acceso requiere obligatoriamente MFA.Caso 2: Conserje Break-Glass (bg-conserje1)Resultado: Directiva CA-Montepinar-MFA omitida por pertenencia a regla de exclusión explícita.Evaluación Antonio Recio (MFA Requerido)Evaluación Break-Glass (Exclusión Validada)

2. Auditoría en Registros de Inicio de Sesión (Sign-in Logs)Con la directiva en modo Solo informe, se forzó un inicio de sesión interactivo con las credenciales de Antonio Recio en [https://myapplications.microsoft.com](https://myapplications.microsoft.com).La inspección forense del registro confirmó que las condiciones de usuario y aplicación hicieron coincidencia (Match), y el control de concesión evaluó el estado como «Acción del usuario requerida», validando la directiva sin riesgo operativo.

3. Ejecución Bloqueante en VivoTras conmutar la directiva al estado Activado, se abrió una nueva sesión de navegación limpia como Antonio Recio. El motor de Acceso Condicional interceptó la autenticación en tiempo real, bloqueando el acceso a los recursos corporativos hasta completar el registro del segundo factor de autenticación.

🛡️ Competencias Clave DemostradasIdentidad Híbrida & Sincronización: Dominio de Microsoft Entra Connect, atributos de enlace e integración local-nube.Mitigación de Lockout Crítico: Arquitectura de cuentas de emergencia nativas aplicando el principio de alta disponibilidad IAM.Técnicas Anti-Push Bombing: Configuración avanzada de políticas de métodos de autenticación (NIST SP 800-63B).Ciclo de Vida de Acceso Condicional: Despliegue metodológico corporativo (Report-only ➔ What If ➔ Sign-in Analysis ➔ Enforcement).Auditoría y Triage en SOC: Capacidad de lectura, rastreo e interpretación de eventos interactivos en Entra Sign-in Logs.
