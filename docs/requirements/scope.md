# 📄 Requerimientos del Sistema

## 1. Sistema

* **Nombre del sistema:** Bankify
* **Objetivo:** El sistema tiene como objetivo permitir la gestión básica y controlada de cuentas bancarias de los clientes de Bankify, garantizando el cumplimiento de reglas de negocio definidas, facilitando la consulta de información financiera, la realización de depósitos y la generación de reportes tributarios en formatos PDF y JSON para la DIAN.

## 2. Problema a resolver
Actualmente, Bankify no cuenta con un sistema centralizado que permita gestionar de manera validada y segura la creación y administración de cuentas bancarias.
No existe una plataforma que:

* Valide correctamente los números de cuenta según las reglas del negocio.
* Permita consultar el saldo de las cuentas.
* Controle la realización de depósitos.
* Genere reportes tributarios individuales para clientes.
* Envíe reportes tributarios consolidados a la DIAN en formato JSON.

Esta situación impide validar el modelo de negocio del MVP y limita la operación organizada de la fintech.

El sistema propuesto busca resolver esta problemática mediante una plataforma digital que centralice la gestión de clientes y cuentas bancarias.

## 3. Diagrama de Contexto

### 3.1 Diagrama

Se adjunta el diagrama de contexto elaborado para el sistema Bankify, en el cual se representan los actores internos, sistemas externos y los flujos principales de información.

![Captura](/docs/uml/DiagramaContextoBankify.png)

### 3.2 Actores

| Actor / Rol       |                                       Descripción                                       |
| ----------------- | :-------------------------------------------------------------------------------------: |
| Cliente           |            Persona que crea, consulta e interactúa con sus cuentas bancarias            |
| Asesor            | Usuario interno que gestiona cuentas bancarias (crear, activar, actualizar e inactivar) |
| Supervisor        |           Usuario interno encargado de la gestión y administración de clientes          |
| Gerente financiero |      Usuario responsable de generar reportes tributarios consolidados para la DIAN      |

### 3.3 Sistemas externos

| Sistema  |                                                Descripción                                                |
| -------- | :-------------------------------------------------------------------------------------------------------: |
| Bancos   | Entidades bancarias registradas en el sistema que validan el código de banco asociado al número de cuenta |
| DIAN     |                Entidad externa a la cual se envían los reportes tributarios en formato JSON               |

## 4. Alcance del sistema

### 4.1 Dentro del sistema

Funciones que el sistema sí realiza:

1. Autenticación de usuarios (clientes y operadores).
2. Creación, activación, actualización e inactivación de clientes.
3. Creación, activación, actualización e inactivación de cuentas bancarias.
4. Validación de número de cuenta (10 dígitos, solo números y código de banco válido).
5. Consulta de saldo de cuentas.
6. Registro de depósitos en cuentas.
7. Generación de reportes tributarios en PDF para clientes.
8. Generación y envío de reportes tributarios en formato JSON a la DIAN.
9. Eliminación de clientes y sus cuentas asociadas.

### 4.2 Fuera del sistema

Funciones que el sistema no realiza:

1. Transferencias interbancarias reales entre bancos.
2. Gestión de créditos, préstamos o productos financieros complejos.
3. Procesamiento real de transacciones bancarias externas.
4. Cálculo avanzado de impuestos fuera de los datos registrados en el sistema.
5. Integración con pasarelas de pago externas.
