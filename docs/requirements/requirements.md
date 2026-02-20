




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
