# Flujo de Trabajo (Workflow) en Jira: Subtareas de Arquitectura TI

Este documento define el flujo de trabajo estándar en **Jira** para la gestión, diseño, revisión y entrega de artefactos de **Arquitectura TI** a nivel de subtareas. El modelo garantiza la separación de responsabilidades entre el gobierno técnico, la ejecución, la validación con el cliente y la aprobación del Comité de Arquitectura (ARB).

---

## 1. Roles y Campos Clave en Jira

Para mantener la trazabilidad completa durante el ciclo de vida de la subtarea, se deben configurar los siguientes campos en la pantalla de la incidencia:

* **Responsable (`Assignee`):** Campo dinámico que cambia según el estado actual de la subtarea para reflejar quién tiene la acción pendiente.
* **Arquitecto Líder / Aprobador:** Campo personalizado de usuario (fijo). Responsable de aprobar el alcance inicial, asignar la tarea, realizar el filtro técnico interno y dar el cierre formal.
* **Arquitecto Ejecutor:** Campo personalizado de usuario (fijo). Responsable del diseño de la solución, la presentación ante el cliente/comité y la corrección de observaciones.
* **Enlace a Artefactos:** Campo tipo URL para vincular los documentos de trabajo (Confluence, SharePoint, Git, Miro, etc.).
* **Acta / Evidencia de Comité:** Campo tipo URL o texto para registrar la conformidad del cliente y el dictamen del Comité de Arquitectura.

---

## 2. Estados del Flujo de Trabajo

| Estado | Categoría Jira | Responsable (`Assignee`) | Descripción y Criterio de Salida |
| :--- | :--- | :--- | :--- |
| **1. Por Asignar** | Por hacer (Gris) | Arquitecto Líder | Estado inicial. El líder valida el alcance de la solicitud, aprueba su ejecución y asigna al Arquitecto Ejecutor. |
| **2. En Diseño** | En curso (Azul) | Arquitecto Ejecutor | El ejecutor analiza los requerimientos y elabora los borradores de los artefactos (diagramas, SAD, ADRs). |
| **3. Revisión Técnica Interna** | En curso (Azul) | Arquitecto Líder | El líder revisa que los artefactos cumplan con los estándares técnicos internos antes de exponerlos al cliente. |
| **4. Revisión con Cliente** | En curso (Azul) | Arquitecto Ejecutor | Se presenta la solución al cliente o negocio para validar la alineación funcional y obtener conformidad. |
| **5. En Comité de Arquitectura** | En curso (Azul) | Arquitecto Líder / Ejecutor | La solución pasa por el foro de gobierno (ARB) para validar seguridad, escalabilidad, costos y estándares corporativos. |
| **6. Ajustes Requeridos** | En curso (Azul) | Arquitecto Ejecutor | *(Estado de retorno)* Si existen observaciones en la revisión interna, del cliente o del comité, la tarea vuelve aquí para ajustes. |
| **7. Aprobado / Listo para Entrega** | En curso (Azul) | Arquitecto Ejecutor | Cuenta con visto bueno técnico, del cliente y del comité. Se preparan las versiones finales de los artefactos. |
| **8. Entregado / Cerrado** | Listo (Verde) | Arquitecto Líder | Artefactos publicados en el repositorio oficial y comunicados al proyecto. Subtarea finalizada. |

---

## 3. Diagrama de Transiciones

```text
[Por Asignar]
      │  (Aprobar y asignar)
      ▼
 [En Diseño] ◄──────────────────────────────────────────┐
      │  (Enviar a revisión interna)                    │
      ▼                                                 │
[Revisión Técnica Interna] ─────(Solicitar ajustes)─────┤
      │  (Visto bueno técnico)                          │
      ▼                                                 │
[Revisión con Cliente] ─────────(Cambios de negocio)────┤
      │  (Conformidad cliente)                          │
      ▼                                                 │
[En Comité de Arquitectura] ────(Observaciones ARB)─────┘
      │  (Aprobado por Comité)
      ▼
[Aprobado / Listo para Entrega]
      │  (Confirmar publicación)
      ▼
[Entregado / Cerrado]
```

---

## 4. Reglas de Automatización y Validadores (Jira Rules)

### Validadores en Transiciones (`Validators`)
* **De `Por Asignar` a `En Diseño`:** Obligar a que los campos *Arquitecto Ejecutor* y *Fecha de vencimiento* estén definidos.
* **De `En Diseño` a `Revisión Técnica Interna`:** Exigir que el campo *Enlace a Artefactos* no esté vacío.
* **De `Revisión con Cliente` a `En Comité de Arquitectura`:** Exigir evidencia de conformidad del cliente (adjunto en PDF, enlace a minuta o comentario de aprobación).
* **De `En Comité de Arquitectura` a `Aprobado / Listo para Entrega`:** Exigir el registro en el campo *Acta / Evidencia de Comité* y restringir la transición mediante condición (`Condition`) para que solo el *Arquitecto Líder* pueda ejecutarla.

### Automatizaciones (`Jira Automation`)
* **Asignación a Revisión Interna:** Al pasar a `Revisión Técnica Interna`, asignar automáticamente la subtarea al usuario definido en *Arquitecto Líder*.
* **Retorno por Observaciones:** Al pasar a `Ajustes Requeridos`, reasignar automáticamente al *Arquitecto Ejecutor* y enviar notificación con el último comentario registrado.
* **Medición de Tiempos (SLAs):** Utilizar los estados `Revisión con Cliente` y `En Comité de Arquitectura` para medir los tiempos de espera externos frente al tiempo neto de diseño.

---

## 5. Checklist de Cierre (Definition of Done)

Antes de transicionar la subtarea a **Entregado / Cerrado**, se debe verificar el cumplimiento de los siguientes puntos:

- [ ] Artefactos técnicos (Diagramas C4/UML, SAD, ADRs) actualizados tras las observaciones recibidas.
- [ ] Conformidad formal del Cliente / Product Owner registrada en Jira.
- [ ] Aprobación oficial del Comité de Arquitectura (ARB) documentada con número de sesión o acta.
- [ ] Versiones finales publicadas en el repositorio oficial (sin marcas de agua de "Borrador").
- [ ] Enlaces finales vinculados en la historia de usuario o épica padre en Jira.
