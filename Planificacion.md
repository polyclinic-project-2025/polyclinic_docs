## PLANIFICACIÓN DEL PROYECTO

Proyecto: Sistema de Gestión de Procesos Prioritarios del Policlínico
Tecnologías: ASP.NET Core, EF Core, PostgreSQL, React
Equipo:

* Miembro 1: Melissa Maureen Sales Brito
* Miembro 2: Ramón Cherta González
* Miembro 3: John Mauris López Ramos
* Miembro 4: Ronal Prieto Vázquez

---

##  HITO 1: Arquitectura Base, Autenticación y CRUD de Administración

Duración: Semana 7 – 10
Objetivo: Implementar la base del sistema, autenticación y CRUDs iniciales del módulo administrativo.

| Nº | Tarea                                                                                                             | Responsables | Inicio   | Fin      | Dependencias |
| -- | ----------------------------------------------------------------------------------------------------------------- | ------------ | -------- | -------- | ------------ |
| 1  | Crear repositorio en GitHub y definir flujo de trabajo colaborativo (branches por feature, PRs, issues)           | 1        | Semana 7 | Semana 7 | -            |
| 2  | Definir estructura del proyecto con la arquitectura por capas (Domain, Application, Infrastructure, Presentation) | 1        | Semana 8 | Semana 8 | 1            |
| 3  | Configurar entorno de desarrollo (.NET, PostgreSQL, React, dependencias)                                          | 1        | Semana 8 | Semana 8 | 1            |
| 4  | Implementar entidades base ( Role, Department,Employee) y DTOs asociados                                              | 1 y 2        | Semana 9 | Semana 9 | 2            |
| 5  | Configurar DbContext, relaciones y migraciones iniciales (EF Core + PostgreSQL)                                   | 1,2,3 y 4       | Semana 9 | Semana 9 | 4            |
| 6  | Implementar repositorios genéricos y específicos (DepartmentRepository,EmployeeRepository)                           | 1,2 y 3       | Semana 9 | Semana 9 | 5            |
| 7  | Implementar autenticación (registro, login, roles, refresh tokens)                                            | 1        | Semana 9 | Semana 9 | 3,4          |
| 8  | Implementar servicios de aplicación ( DepartmentService, AuthService,EmployeeService)                                 | 1,2 y 3        | Semana 10 | Semana 10 | 6,7          |
| 9  | Crear controladores API (AuthController) con endpoints CRUD                                      | 1        | Semana 10 | Semana 10 | 8            |
| 10 | Diseñar e implementar interfaz React: Login, Dashboard, CRUD de usuarios/departamentos                            | 1,2,3 y 4     | Semana 10 | Semana 10 | 9            |
| 11 | Pruebas unitarias y de integración                        | 1, 2, 3 y 4  | Semana 4 | Semana 4 | 9,10         |

Resultado:
Arquitectura lista, autenticación funcional, CRUD de administración operativos (API + BD + UI).

---

## ⚙️ HITO 2: Módulo Clínico y Gestión de Consultas

Duración: Semana 11 – 14
Objetivo: Implementar los módulos de pacientes, doctores, consultas y medicamentos, integrando backend y frontend.

| Nº | Tarea                                                                                                             | Responsables | Inicio   | Fin      | Dependencias |
| -- | ----------------------------------------------------------------------------------------------------------------- | ------------ | -------- | -------- | ------------ |
| 12 | Diseñar entidades y relaciones (Patient, Doctor, Consultation, Medication)                  | 1 y 2        | Semana 11 | Semana 12 | 9            |
| 13 | Configurar modelos y relaciones en EF Core (1:N, N:N) y aplicar migraciones                             | 2 y 3        | Semana 11 | Semana 12 | 12           |
| 14 | Implementar servicios de negocio (PatientService, ConsultationService, MedicationService)               | 1 y 4        | Semana 11 | Semana 12 | 13           |
| 15 | Crear endpoints REST para pacientes, consultas y medicamentos                                           | 1 y 3        | Semana 12 | Semana 13 | 14           |
| 16 | Crear interfaz React: gestión de pacientes, consultas, medicamentos (formularios, validaciones, tablas) | 2 y 3        | Semana 12 | Semana 13 | 15           |
| 17 | Implementar listados, filtros y paginación de consultas por doctor, paciente o fecha                    | 1 y 2        | Semana 13 | Semana 14 | 15           |
| 18 | Integrar frontend con API REST                                             | 3 y 4        | Semana 13 | Semana 14 | 16,17        |
| 19 | Pruebas unitarias e integración (servicios, controladores, consultas SQL/LINQ)                          | 1, 2 y 4     | Semana 13 | Semana 14 | 18           |
| 20 | Documentación técnica y demo funcional interna                                                          | 3 y 4        | Semana 14 | Semana 14 | 19           |

 Resultado:
Módulo clínico completamente funcional, con consultas, pacientes y medicamentos integrados al sistema.

---

##  HITO 3: Módulo de Almacén, Reportes y Optimización

 Duración: Semana 14 – 16
 Objetivo: Implementar la gestión de inventario, solicitudes de medicamentos, reportes estadísticos y optimización final.

| Nº | Tarea                                                                         | Responsables | Inicio    | Fin       | Dependencias |
| -- | ----------------------------------------------------------------------------- | ------------ | --------- | --------- | ------------ |
| 21 | Crear entidades: Stock, MedicationLot, Request                 | 1 y 2        | Semana 14  | Semana 14  | 12           |
| 22 | Configurar modelos, relaciones y migraciones en EF Core                       | 2 y 3        | Semana 14  | Semana 15 | 21           |
| 23 | Implementar servicios y lógica de negocio del módulo de almacén               | 1 y 4        | Semana 14  | Semana 15 | 22           |
| 24 | Crear endpoints API: StockController, RequestController                       | 1 y 3        | Semana 14 | Semana 15 | 23           |
| 25 | Diseñar interfaz React: stock, solicitudes, aprobaciones y denegaciones       | 2 y 3        | Semana 14 | Semana 15 | 24           |
| 26 | Implementar consultas analíticas y reportes exportables (LINQ avanzado / PDF) | 1, 2 y 4     | Semana 14 | Semana 15 | 24           |
| 27 | Crear dashboard de reportes (React + Chart.js / Recharts)                     | 3 y 4        | Semana 11 | Semana 12 | 26           |
| 28 | Pruebas de rendimiento, seguridad y validación de módulos integrados          | 1, 2, 3 y 4  | Semana 15 | Semana 15 | 25–27        |
| 29 | Despliegue final, documentación técnica, manual de usuario      | 3 y 4        | Semana 15 | Semana 15 | 28           |

 Resultado:
Sistema completo, con todos los módulos integrados, consultas y reportes funcionales y versión final desplegada.

---

##  Precedencia general de los hitos

1️⃣ Hito 1 → Base técnica, autenticación, CRUD iniciales
2️⃣ Hito 2 → Módulo clínico (pacientes, consultas, medicamentos)
3️⃣ Hito 3 → Módulo almacén, reportes y optimización
