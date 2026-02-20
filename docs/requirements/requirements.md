# DOCUMENTO DE ANÁLISIS DE REQUERIMIENTOS – RF-01
## Bankify – Autenticación de Usuarios mediante Usuario y Contraseña

---

## INFORMACIÓN GENERAL

| Campo | Detalle                                                                                                                                                                                                                                                                                                                            |
|---|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Funcionalidad** | Autenticación de Usuarios                                                                                                                                                                                                                                                                                                          |
| **Código** | RF-01                                                                                                                                                                                                                                                                                                                              |
| **Nombre** | Autenticación mediante usuario y contraseña en la plataforma Bankify                                                                                                                                                                                                                                                               |
| **Tipo** | Funcional                                                                                                                                                                                                                                                                                                                          |
| **Descripción** | El sistema debe permitir la autenticación mediante usuario y contraseña para clientes, asesores, supervisores y gerente financiero. Cada rol tendrá acceso únicamente a las funcionalidades habilitadas según sus permisos dentro de la plataforma Bankify.                                                                        |
| **Cómo se ejecutará** | El usuario ingresa a la plataforma Bankify, introduce su nombre de usuario (correo electrónico) y contraseña en el formulario de inicio de sesión y hace clic en "Iniciar sesión". El sistema valida las credenciales contra la base de datos, determina el rol del usuario y redirige a la vista correspondiente según dicho rol. |
| **Actor principal** | Cliente, Asesor, Supervisor, Gerente Financiero                                                                                                                                                                                                                                                                                    |
| **Precondiciones** | 1. El usuario debe estar registrado y activo en el sistema. <br>2. El usuario debe conocer sus credenciales (correo y contraseña). <br>3. La cuenta del usuario no debe estar inactiva o bloqueada. <br>4. El servicio de autenticación del sistema debe estar operativo.                                                          |

---

## DATOS DE ENTRADA

| Nombre | Descripción | Tipo de campo | Reglas / Validación | Obligatorio |
|---|---|---|---|---|
| Correo electrónico | Identificador único del usuario en el sistema | Email | Formato válido (usuario@dominio.ext). Debe existir en la base de datos. | Sí |
| Contraseña | Contraseña de acceso asociada a la cuenta | Contraseña | Mínimo 8 caracteres. Al menos 1 mayúscula, 1 número y 1 carácter especial. No se muestra en pantalla (campo enmascarado). | Sí |

---

## DATOS DE SALIDA

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Token de sesión | Identificador de sesión activa generado por el sistema | Alfanumérico | Generado automáticamente. Con expiración configurada (ej. 30 min inactividad). | Sí |
| Rol del usuario | Rol asignado al usuario autenticado | Texto | Valores posibles: `CLIENTE`, `ASESOR`, `SUPERVISOR`, `GERENTE_FINANCIERO`. | Sí |
| Redirección al dashboard | El sistema redirige al usuario al panel correspondiente a su rol | URL | Cliente → `/dashboard/cliente`. Asesor → `/dashboard/asesor`. Supervisor → `/dashboard/supervisor`. Gerente Financiero → `/dashboard/gerente`. | Sí |
| Mensaje de bienvenida | Mensaje personalizado mostrado al iniciar sesión | Texto | Se muestra en pantalla después del inicio de sesión exitoso. | Sí |

---

## FLUJO BÁSICO

| Paso | Actor | Descripción | Excepción |
|---|---|---|---|
| 1 | Usuario | Ingresa a la plataforma Bankify y navega a la pantalla de inicio de sesión. | — |
| 2 | Usuario | Introduce su correo electrónico y contraseña en el formulario. | — |
| 3 | Usuario | Hace clic en el botón "Iniciar sesión". | — |
| 4 | Sistema | Valida que los campos no estén vacíos y que tengan el formato correcto. | Flujo Alterno FA-01 si los campos están vacíos o tienen formato inválido. |
| 5 | Sistema | Verifica que el correo electrónico exista en la base de datos. | Flujo Alterno FA-02 si el usuario no existe. |
| 6 | Sistema | Compara la contraseña ingresada (hash) con la almacenada en la base de datos. | Flujo Alterno FA-03 si la contraseña es incorrecta. |
| 7 | Sistema | Verifica que la cuenta del usuario esté activa. | Flujo Alterno FA-04 si la cuenta está inactiva o bloqueada. |
| 8 | Sistema | Genera un token de sesión y registra el inicio de sesión. | — |
| 9 | Sistema | Determina el rol del usuario autenticado. | — |
| 10 | Sistema | Redirige al usuario al dashboard correspondiente a su rol y muestra mensaje de bienvenida. | — |

---

## FLUJO ALTERNO (MANEJO DE ERRORES)

| Código | Actor | Descripción del error | Acción del sistema |
|---|---|---|---|
| FA-01 | Sistema | Uno o más campos están vacíos o tienen formato inválido (ej. correo sin @). | El sistema resalta en rojo los campos con error y muestra un mensaje descriptivo. No procesa el inicio de sesión. |
| FA-02 | Sistema | El correo electrónico ingresado no está registrado en el sistema. | Muestra mensaje genérico: *"Correo o contraseña incorrectos"* (sin revelar cuál de los dos falló, por seguridad). |
| FA-03 | Sistema | La contraseña ingresada no coincide con la almacenada. | Muestra mensaje genérico: *"Correo o contraseña incorrectos"*. Incrementa el contador de intentos fallidos. Bloquea la cuenta tras 5 intentos consecutivos fallidos. |
| FA-04 | Sistema | La cuenta del usuario está inactiva o bloqueada. | Muestra mensaje: *"Tu cuenta se encuentra inactiva o bloqueada. Comunícate con tu asesor o administrador."* |
| FA-05 | Sistema | El servicio de autenticación no está disponible. | Muestra mensaje: *"El servicio no está disponible en este momento. Intenta de nuevo más tarde."* |

---

## REGLAS DE NEGOCIO

| No. | Descripción |
|---|---|
| 1 | El sistema debe soportar cuatro roles diferenciados: **Cliente**, **Asesor**, **Supervisor** y **Gerente Financiero**. Cada rol tendrá acceso exclusivo a las funcionalidades autorizadas. |
| 2 | Las contraseñas deben almacenarse cifradas en la base de datos utilizando un algoritmo seguro de hashing (ej. bcrypt). Nunca en texto plano. |
| 3 | Tras **5 intentos fallidos consecutivos** de inicio de sesión, la cuenta del usuario se bloqueará automáticamente como medida de seguridad. |
| 4 | El mensaje de error ante credenciales incorrectas debe ser genérico (*"Correo o contraseña incorrectos"*) y nunca debe indicar cuál de los dos campos falló, para evitar ataques de enumeración de usuarios. |
| 5 | El token de sesión generado debe expirar tras un período de inactividad configurable (mínimo recomendado: 30 minutos). |
| 6 | Un usuario no puede tener más de una sesión activa simultánea en la plataforma (sesión única por usuario). |
| 7 | Solo los usuarios con estado **Activo** en el sistema pueden autenticarse. Los usuarios con estado Inactivo no pueden iniciar sesión. |

---

## NOTAS Y COMENTARIOS

- Se recomienda implementar autenticación en dos factores (2FA) como mejora futura, especialmente para los roles de **Supervisor** y **Gerente Financiero**, dado el nivel de privilegio que poseen.
- Considerar el uso de HTTPS obligatorio para el envío de credenciales y el intercambio de tokens de sesión.
- El formulario de login debe ser responsivo y funcionar correctamente en dispositivos móviles.
- Registrar en logs los intentos de inicio de sesión (exitosos y fallidos) incluyendo IP, fecha y hora, como parte de las buenas prácticas de seguridad y trazabilidad.
- Aplicar heurística de Nielsen #5 (Prevención de errores): deshabilitar el botón "Iniciar sesión" si los campos están vacíos, para evitar envíos vacíos.

---

## ABREVIATURAS Y GLOSARIO

| Abreviatura | Significado |
|---|---|
| RF | Requerimiento Funcional |
| BD | Base de Datos |
| MVP | Minimum Viable Product (Producto Mínimo Viable) |
| 2FA | Autenticación en Dos Factores |
| HTTPS | Hypertext Transfer Protocol Secure |
| Token | Identificador único de sesión generado tras autenticación exitosa |
| Hash | Resultado del proceso de cifrado unidireccional de una contraseña |
| DIAN | Dirección de Impuestos y Aduanas Nacionales |
| Rol | Perfil de usuario que define los permisos y accesos dentro del sistema |
| Sesión | Período activo de interacción de un usuario autenticado con el sistema |

---

## ANEXOS

| Tipo                  | Descripción                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Prototipo             | Mockup del formulario de inicio de sesión (pantallas: login → dashboard por rol).                                                                                                                                                                                                                                                                              |
| Diagrama CU           | Diagrama de Caso de Uso: Actores `Cliente`, `Asesor`, `Supervisor`, `Gerente Financiero` - Caso de Uso `Loging`. Incluye `<<include>>` hacia `Validar credenciales` y `<<extend>>` hacia `Bloquear por intentos fallidos`, `<<include>>` hacia `Redirigir por rol`, `<<include>>` hacia `Validar campos` y `<<extend>>` hacia `Bloquear por intentos fallidos` |
| Diagrama de secuencia | Diagrama de secuencia del flujo de autenticación: Usuario → Frontend → Backend → Base de Datos → Respuesta con token y rol.                                                                                                                                                                                                                                    |

---

## CONTROL DE VERSIONES

| Elaborado por | Aprobado por | Fecha | Descripción y justificación de cambios |
|---|---|---|---|
| Squad DOSW – Grupo 2 | Docente DOSW | 20/02/2026 | Versión 1.0 – Creación inicial del análisis de requerimientos RF-02. |

## Bankify – Generación de Reporte Tributario Individual en Formato PDF

---

## INFORMACIÓN GENERAL

### Funcionalidad

| Campo | Detalle                                                                                                                                                                                                                                                                                                                  |
|---|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Código** | RF-02                                                                                                                                                                                                                                                                                                                    |
| **Nombre** | Generación de reporte tributario individual en formato PDF para el cliente                                                                                                                                                                                                                                               |
| **Tipo** | Funcional                                                                                                                                                                                                                                                                                                                |
| **Descripción** | El sistema debe permitir que un cliente autenticado genere su reporte tributario de declaración de renta en formato PDF, consolidando la información de todas sus cuentas bancarias activas registradas en Bankify (saldos, movimientos y rendimientos del período fiscal correspondiente).                              |
| **Cómo se ejecutará** | El cliente ingresa a la plataforma Bankify con sus credenciales, navega a la sección de reportes tributarios, selecciona el período fiscal y solicita la generación del reporte. El sistema consolida la información de todas sus cuentas, genera el documento PDF y lo pone a disposición del cliente para su descarga. |
| **Actor principal** | Cliente (propietario de las cuentas)                                                                                                                                                                                                                                                                                     |
| **Precondiciones** | 1. El cliente debe estar autenticado en el sistema. <br>2. El cliente debe tener al menos una cuenta bancaria activa registrada en Bankify. <br>3. El período fiscal seleccionado debe ser válido y estar disponible en el sistema. <br>4. El servicio de generación de PDFs debe estar operativo.                       |

---

### DATOS DE ENTRADA

| Nombre | Descripción | Tipo de campo | Reglas / Validación | Obligatorio |
|---|---|---|---|---|
| ID de cliente | Identificador único del cliente autenticado | Numérico | Obtenido automáticamente de la sesión activa. No editable por el usuario. | Sí |
| Período fiscal | Año gravable para el cual se genera el reporte | Lista / Numérico | Debe ser un año válido entre el año de apertura de la cuenta y el año fiscal inmediatamente anterior al actual. No se permite el año en curso si aún no ha cerrado. | Sí |


---

### DATOS DE SALIDA

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Documento PDF | Reporte tributario generado en formato PDF | Archivo PDF | Debe incluir: datos del cliente, número(s) de cuenta, saldo inicial del período, saldo final del período, total de depósitos, total de retiros y rendimientos generados. Nombre del archivo: `reporte_tributario_<ID_cliente>_<año_fiscal>.pdf`. | Sí |
| Fecha de generación | Fecha y hora en que fue generado el reporte | Fecha / Hora | Formato: `DD/MM/AAAA HH:MM:SS`. Se incluye dentro del documento PDF y en el historial del sistema. | Sí |
| Mensaje de confirmación | Mensaje en pantalla que confirma la generación exitosa | Texto | Se muestra en pantalla inmediatamente después de generar el PDF. | Sí |
| Enlace de descarga | URL temporal para descargar el PDF generado | URL | Válido por 24 horas desde su generación. | Sí |

---

### FLUJO BÁSICO

| Paso | Actor | Descripción | Excepción |
|---|---|---|---|
| 1 | Cliente | Inicia sesión en la plataforma Bankify con sus credenciales. | — |
| 2 | Cliente | Navega a la sección "Reportes Tributarios" desde su panel principal. | — |
| 3 | Sistema | Muestra la pantalla de generación de reporte con el selector de período fiscal. | — |
| 4 | Cliente | Selecciona el período fiscal (año gravable) para el cual desea generar el reporte. | — |
| 5 | Cliente | Hace clic en el botón "Generar Reporte PDF". | — |
| 6 | Sistema | Valida que el período fiscal seleccionado sea válido y esté disponible. | Flujo Alterno FA-01 si el período no es válido. |
| 7 | Sistema | Consulta todas las cuentas activas del cliente para el período indicado. | Flujo Alterno FA-02 si el cliente no tiene cuentas activas. |
| 8 | Sistema | Consolida la información tributaria: saldos, movimientos, depósitos, retiros y rendimientos del período. | Flujo Alterno FA-03 si no hay movimientos en el período. |
| 9 | Sistema | Genera el documento PDF con el formato oficial de declaración de renta de Bankify. | Flujo Alterno FA-04 si falla la generación del PDF. |
| 10 | Sistema | Almacena el PDF generado y registra el evento en el historial del cliente. | — |
| 11 | Sistema | Muestra mensaje de confirmación y habilita el enlace de descarga del PDF. | — |
| 12 | Cliente | Descarga el documento PDF desde el enlace proporcionado. | — |

---

### FLUJO ALTERNO (MANEJO DE ERRORES)

| Código | Actor | Descripción del error | Acción del sistema |
|---|---|---|---|
| FA-01 | Sistema | El período fiscal seleccionado no es válido o no está disponible (ej. año en curso sin cerrar). | Muestra mensaje: *"El período fiscal seleccionado no está disponible. Por favor selecciona un año gravable cerrado."* |
| FA-02 | Sistema | El cliente no tiene cuentas bancarias activas registradas en el período indicado. | Muestra mensaje: *"No se encontraron cuentas activas para el período seleccionado. No es posible generar el reporte."* |
| FA-03 | Sistema | El cliente no registra movimientos en sus cuentas durante el período fiscal seleccionado. | Genera el reporte indicando saldo en cero y ausencia de movimientos. Incluye nota aclaratoria en el documento. |
| FA-04 | Sistema | Falla en el servicio de generación del PDF (error técnico). | Muestra mensaje: *"Ocurrió un error al generar el reporte. Por favor intenta de nuevo más tarde."* Registra el error en los logs del sistema. |
| FA-05 | Sistema | La sesión del cliente expiró durante el proceso de generación. | Redirige al cliente al formulario de inicio de sesión con mensaje: *"Tu sesión ha expirado. Por favor inicia sesión nuevamente."* |

---

### REGLAS DE NEGOCIO

| No. | Descripción |
|---|---|
| 1 | El reporte tributario solo puede ser generado por el cliente propietario de las cuentas. Ningún otro rol puede generar el reporte individual de un cliente específico (el Gerente Financiero tiene su propio flujo para reportes masivos hacia la DIAN). |
| 2 | El reporte debe consolidar **todas** las cuentas bancarias activas que el cliente posea en Bankify para el período fiscal indicado. |
| 3 | Solo se pueden generar reportes de **períodos fiscales cerrados** (años anteriores al año en curso). No se permite generar reportes del año en curso. |
| 4 | El documento PDF generado debe contener como mínimo: nombre completo del cliente, número de identificación, número(s) de cuenta, banco asociado, saldo inicial, saldo final, total de depósitos, total de retiros y rendimientos generados en el período. |
| 5 | El enlace de descarga del PDF tendrá una vigencia máxima de **24 horas** desde su generación. Pasado este tiempo, el cliente deberá generar el reporte nuevamente. |
| 6 | Cada generación de reporte debe quedar registrada en el historial de actividad del cliente, incluyendo fecha, hora y período fiscal consultado. |
| 7 | Los números de cuenta incluidos en el reporte deben mostrarse con enmascaramiento parcial (ej. `01****7890`) para proteger la información sensible dentro del documento. |

---

### NOTAS Y COMENTARIOS

- Se recomienda que el PDF generado cumpla con los lineamientos visuales e institucionales de Bankify (logo, colores corporativos, pie de página con aviso legal).
- Considerar la posibilidad de enviar automáticamente el PDF al correo electrónico del cliente como canal adicional de entrega, además del enlace de descarga en plataforma.
- El sistema debe garantizar que los PDFs generados sean documentos no editables para preservar la integridad de la información tributaria.
- Para versiones futuras, evaluar la posibilidad de incluir firma digital en el documento para dotarlo de validez legal ante la DIAN.
- Aplicar heurística de Nielsen #1 (Visibilidad del estado del sistema): mostrar un indicador de progreso (spinner o barra) mientras se genera el PDF, dado que el proceso puede tardar algunos segundos.

---

### ABREVIATURAS Y GLOSARIO

| Abreviatura | Significado |
|---|---|
| RF | Requerimiento Funcional |
| PDF | Portable Document Format |
| BD | Base de Datos |
| DIAN | Dirección de Impuestos y Aduanas Nacionales |
| MVP | Minimum Viable Product (Producto Mínimo Viable) |
| Período fiscal | Año gravable sobre el cual se declaran ingresos y patrimonio ante la DIAN |
| Declaración de renta | Obligación tributaria que reporta ingresos, patrimonio y retenciones de un período fiscal |
| Rendimientos | Intereses o beneficios generados por los saldos de las cuentas bancarias durante el período |

---

### ANEXOS

| Tipo | Descripción |
|---|---|
| Prototipo | Mockup de la pantalla de generación de reporte (pantallas: panel cliente → reportes tributarios → selección período → confirmación → descarga). |
| Diagrama CU | Diagrama de Caso de Uso: Actor `Cliente` – Caso de Uso `Generar reporte tributario PDF`. Incluye `<<include>>` hacia `Consultar cuentas activas` y `Consolidar información fiscal`. |
| Diagrama de secuencia | Diagrama de secuencia del flujo: Cliente → Frontend → Backend → BD (consulta cuentas y movimientos) → Servicio PDF → Respuesta con enlace de descarga. |
| Plantilla PDF | Plantilla visual del documento PDF de declaración de renta con los campos requeridos por Bankify. |

---

### CONTROL DE VERSIONES

| Elaborado por | Aprobado por | Fecha | Descripción y justificación de cambios                               |
|---|---|---|----------------------------------------------------------------------|
| Squad DOSW – Grupo 2 | Docente DOSW | 20/02/2026 | Versión 1.0 – Creación inicial del análisis de requerimientos RF-02. |
