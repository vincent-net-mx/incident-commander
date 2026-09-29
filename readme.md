# Guía de Estudio: Bases de Datos y Arquitectura de Seguridad

En este repositorio encontrarás tres cosas:
1. **Guía de estudio teórica** (`readme.md`, este archivo que estás leyendo). Incluye los contenidos y conceptos vistos en la materia de Base de Datos.
2. **Guía de estudio práctica** ([`index.html`](https://vincent-net-mx.github.io/incident-commander/)). Incluye una serie de ejercicios en una terminal, son tres misiones donde deberás ejecutar comandos de SQL para robustecer la seguridad de un sistema.
3. **Guía de soluciones** ([`soluciones.html`](https://vincent-net-mx.github.io/incident-commander/soluciones.html)). Incluye la solución a cada fase de los retos prácticos de la terminal, así como recursos para resolver cada problema y preguntas guía si deseas tratar de encontrar la solución por ti mismo antes de revelarla.

---
# Guía Teórica

## MÓDULO 1: Fundamentos y Diseño de Bases de Datos

### 1. Conceptos Básicos
* **Dato vs. Información:**
  * **Dato:** Valor o representación simbólica cruda sin contexto.
  * **Información:** Dato contextualizado y procesado que adquiere significado y utilidad para la toma de decisiones.
* **DBMS (Database Management System):** Conjunto de programas y herramientas de software diseñados para definir, crear, gestionar, consultar y administrar bases de datos de forma eficiente y segura.
* **Herramientas de Administración (Clientes GUI):**
  * *DBeaver* (Multiplataforma, compatible con casi cualquier motor SQL/NoSQL).
  * *TablePlus* (Cliente nativo ligero y moderno para SQL y NoSQL).

---

### 2. SQL y Manipulación de Datos

#### 2.1 Lenguaje de Definición de Datos (DDL) y Tipos
El subconjunto **DDL** (*Data Definition Language*) se encarga de crear, modificar y eliminar las estructuras de la base de datos (tablas, restricciones, esquemas).

* **Tipos de datos comunes:**
  * `Integer` / `BigInt`: Números enteros.
  * `Float`: Números con punto flotante (decimales).
  * `Text` / `Varchar`: Cadenas de caracteres alfanuméricas.
  * `Bool` / `Boolean`: Valores booleanos (`TRUE` / `FALSE`).
  * `Datetime`: Fecha y hora combinadas.
  * `Timestamp`: Marca temporal precisa (suele incluir zona horaria o registrar el instante exacto de una transacción).

#### 2.2 Creación de Tablas y Restricciones (*Constraints*)
* `CREATE TABLE`: Sentencia base para crear una entidad estructurada.
* Restricciones principales:
  * `NOT NULL`: Impide almacenar valores nulos o vacíos en esa columna.
  * `UNIQUE`: Garantiza que todos los valores de la columna sean distintos entre sí.
  * `DEFAULT`: Asigna un valor predeterminado si no se especifica uno en la inserción.
  * `CONSTRAINT`: Cláusula formal para definir reglas de validación personalizadas o relaciones (`CHECK`, `PRIMARY KEY`, `FOREIGN KEY`).

#### 2.3 Operaciones Básicas (CRUD / DML)
* **SELECT:** Consulta y recuperación de datos.
* **INSERT:** Creación de nuevos registros.
  ```sql
  INSERT INTO tabla (columna1, columna2, columna3) 
  VALUES ('valor1', 'valor2', 'valor3');
  ```
* **UPDATE:** Modificación de registros existentes.
* **DELETE:** Eliminación de registros.
* *Nota de sintaxis:* En SQL, los operadores `!=` y `<>` son equivalentes para indicar desigualdad.

---

### 3. Modelado, Claves e Integridad Referencial

#### 3.1 Claves (Keys)
* **Llave Primaria (Primary Key - PK):** Identificador exclusivo de cada fila dentro de una tabla. No admite valores nulos ni duplicados.
* **Llave Foránea (Foreign Key - FK):** Columna o conjunto de columnas que hace referencia a la llave primaria de otra tabla, estableciendo un vínculo relacional entre ambas.
* **Tabla Pivote (Intermedia / Puente):** Tabla utilizada en relaciones *Muchos a Muchos* ($N:M$) que combina dos o más llaves foráneas para asociar registros de tablas independientes.

**Ejemplo de Relación PK / FK:**

*Tabla: `Usuarios`*
| ID_Usuario (PK) | Nombre | Email |
| :--- | :--- | :--- |
| 1 | Ana | ana@gmail.com |
| 2 | Luis | luis@gmail.com |

*Tabla: `Pedidos`*
| ID_Pedido (PK) | Fecha | ID_Usuario (FK) |
| :--- | :--- | :--- |
| 101 | 09/09/2026 | 1 |
| 102 | 10/09/2026 | 2 |

#### 3.2 Buenas Prácticas para Llaves Primarias
1. Utilizar claves primarias numéricas y secuenciales simples (`INT`, `BIGINT`) o identificadores únicos estables (`UUID`).
2. Evitar el uso de datos del negocio que puedan cambiar con el tiempo (por ejemplo, correos electrónicos o números telefónicos) como llave primaria.
3. Indexar siempre las claves foráneas para optimizar las uniones (`JOIN`).
4. Definir explícitamente las reglas de integridad referencial (`ON UPDATE` y `ON DELETE`).

#### 3.3 Cardinalidad de Relaciones
* **Uno a Uno ($1:1$):** Cada registro de la Tabla A se asocia exactamente con un registro de la Tabla B.
* **Uno a Muchos ($1:N$):** Un registro de la Tabla A se relaciona con múltiples registros de la Tabla B, pero cada registro de B pertenece únicamente a uno de A.
* **Muchos a Muchos ($N:M$):** Registros de la Tabla A pueden tener múltiples correspondencias en B y viceversa. Requiere obligatoriamente una **tabla pivote**.

#### 3.4 Integridad Referencial y Reglas de Cascada
Asegura que las referencias entre tablas siempre apunten a datos existentes y válidos.

| Evento | Acción | Comportamiento |
| :--- | :--- | :--- |
| `ON UPDATE` / `ON DELETE` | **RESTRICT / NO ACTION** | Impide actualizar o eliminar el registro padre si existen registros hijos asociados. |
| `ON UPDATE` / `ON DELETE` | **CASCADE** | Propaga la modificación o eliminación a todos los registros hijos relacionados. |
| `ON UPDATE` / `ON DELETE` | **SET NULL** | Asigna el valor `NULL` a la clave foránea de los registros hijos si el padre se altera o elimina. |

---

### 4. Normalización de Bases de Datos

La normalización es la técnica de descomposición estructural de tablas que previene redundancias, minimiza anomalías de inserción/actualización/borrado y garantiza la consistencia del modelo.

* **1NF (Primera Forma Normal):**
  * Los valores deben ser atómicos (indivisibles en subpartes con significado propio).
  * No deben existir grupos repetitivos ni colecciones de datos en una sola celda.
  * Cada columna debe poseer un tipo de dato consistente.
* **2NF (Segunda Forma Normal):**
  * Cumplir con la 1NF.
  * Todos los atributos que no forman parte de la clave deben tener **dependencia funcional completa** de la clave primaria (elimina dependencias parciales en claves compuestas).
* **3NF (Tercera Forma Normal):**
  * Cumplir con la 2NF.
  * Eliminar **dependencias transitivas** (ningún atributo no clave puede depender de otro atributo no clave; cada campo debe depender *única y exclusivamente* de la clave primaria).
  * No almacenar valores calculados (ej. totales, promedios) que puedan derivarse en tiempo de consulta.
* **BCNF (Forma Normal de Boyce-Codd):**
  * Versión más estricta de la 3NF.
  * Todo determinante (atributo del cual depende funcionalmente otro atributo) debe ser necesariamente una clave candidata.

---

### 5. Optimización e Indexación

* **¿Qué es un Índice?:** Es una estructura de datos accesoria (comúnmente árboles B/B+ o tablas hash) generada por el DBMS que ordena lógicamente las referencias a los registros, funcionando de forma idéntica al índice temático de un libro.
* **Mecanismo:** Evita el escaneo completo de la tabla (*Full Table Scan*). En lugar de revisar fila por fila, el motor navega el índice y salta directamente a la posición física del dato en disco o memoria.
* **Ventajas:**
  * Reducción drástica del tiempo de respuesta en consultas de lectura (`SELECT`).
  * Optimización de cláusulas `WHERE`, `ORDER BY` y operaciones de combinación (`JOIN`).

---

### 6. Bases de Datos NoSQL (*Not Only SQL*)

Diseñadas para esquemas flexibles, alto volumen y requerimientos de latencia mínima. Almacenan información en estructuras diversas (como colecciones de documentos) en lugar del esquema tabular rígido.

* **Ventajas Principales:**
  * **Escalabilidad horizontal:** Facilidad para distribuir datos a través de clusters de servidores (*sharding*).
  * **Flexibilidad de esquema:** Permite modificar la estructura de los datos sin alterar todo el repositorio.
  * **Rendimiento:** Alta velocidad en lecturas/escrituras intensivas según el modelo seleccionado.
* **Tipos de motores NoSQL:**
  1. **Documentales:** Almacenan datos semiestructurados (generalmente JSON / BSON). Ej. MongoDB.
  2. **Grafos:** Modelan relaciones complejas mediante nodos y aristas. Ej. Neo4j.
  3. **Clave-Valor / Multivalor:** Acceso ultra-rápido por identificador clave. Ej. Redis.
  4. **En Memoria (RAM):** Máxima velocidad con persistencia secundaria.
  5. **Tabulares / Columnares:** Almacenan por columnas en vez de filas, óptimos para analítica masiva. Ej. Cassandra.
  6. **Orientadas a Objetos:** Integran conceptos de POO (como herencia y polimorfismo) directo al almacenamiento.

---

## MÓDULO 2: Arquitectura de Seguridad y Gestión de Riesgos

### 1. Principios Fundamentales de Seguridad

> *"La arquitectura de seguridad no es un gasto que evita que algo malo pase; es la inversión que permite que el negocio siga funcionando cuando lo malo inevitablemente suceda."*

* **Diferenciación de Conceptos:**
  * **Vulnerabilidad:** Debilidad intrínseca en el diseño, código, configuración o proceso que puede ser explotada.
  * **Amenaza:** Suceso, agente o circunstancia externa o interna con el potencial de explotar una vulnerabilidad y causar daño.

#### 1.1 La Tríada CIA
Pilar central de la seguridad de la información:

| Elemento | Ideal / Objetivo | Amenaza Principal |
| :--- | :--- | :--- |
| **Confidencialidad** | Privacidad; acceso restringido únicamente a entidades autorizadas. | Filtración, divulgación o espionaje de datos. |
| **Integridad** | Fidelidad, exactitud y completitud de la información sin alteraciones no autorizadas. | Manipulación de datos, fraude, corrupción maliciosa. |
| **Disponibilidad (Accesibilidad)** | Acceso oportuno y confiable a los sistemas y datos cuando se requieran. | Caídas de servicio, ataques DoS/DDoS, fallas de hardware. |

#### 1.2 Zero Trust (*Nunca confiar, siempre verificar*)
* **Premisa:** Desecha el concepto tradicional de "perímetro o red interna segura".
* **Validación continua:** Cada solicitud, usuario, aplicación y dispositivo debe ser autenticado y autorizado explícitamente, sin importar su procedencia física o lógica.
* **Objetivo clave:** Impedir el **movimiento lateral** de un atacante dentro de la red corporativa si una terminal es vulnerada.

#### 1.3 Principio de Menor Privilegio (PoLP - *Principle of Least Privilege*)
* Otorga a cada usuario, proceso o servicio únicamente los permisos indispensables para cumplir su labor específica.
* Reduce drásticamente el **radio de explosión** (*blast radius*): si una credencial se ve comprometida, el impacto queda confinado a las atribuciones mínimas de esa cuenta.

---

### 2. Ciclo de Vida de la Arquitectura de Seguridad

#### 2.1 Fase de Análisis y Planificación
* **Identificación de Activos y Riesgos:**
  1. Catálogo e inventario de activos críticos para el negocio.
  2. Definición de requisitos de cumplimiento normativo y legal.
  3. Establecimiento de metas de seguridad alineadas a los objetivos de rentabilidad y operación.
* **Evaluación de Amenazas:**
  1. Análisis de vectores de ataque y superficies de exposición.
  2. Clasificación de datos según criticidad y confidencialidad (Público, Interno, Confidencial, Restringido).
  3. Modelado de amenazas sistemático para anticipar escenarios de compromiso.

#### 2.2 Fase de Diseño de Estrategias y Estándares
* **Defensa en Profundidad:** Superposición de múltiples capas defensivas (física, red, endpoint, aplicación, datos).
* **Segmentación:** Separación lógica y física de redes y cargas de trabajo.
* **Gestión de Identidades y Accesos (IAM):** Control centralizado de credenciales, roles (RBAC) y autenticación multifactor (MFA).
* **Seguridad por Diseño (*Security by Design*):**
  * La seguridad considerada como un requisito funcional desde el día cero, no como un parche tardío.
  * Análisis riguroso de librerías y componentes de terceros (*Supply Chain Security*).

---

### 3. Continuidad del Negocio, Gobernanza y Almacenamiento

#### 3.1 DRP (*Disaster Recovery Plan*) y Resiliencia
* **Resiliencia Operativa:** Capacidad de una arquitectura para absorber impactos, operar en modo degradado y recuperarse ágilmente.
* **DRP:** Procedimientos documentados y probados para restaurar sistemas de misión crítica tras un incidente mayor o desastre (catástrofe natural, ciberataque de *Ransomware*). Sin DRP, las pérdidas de datos prolongadas suelen traducirse en la quiebra de la organización.

#### 3.2 Gobernanza, Cumplimiento y el Costo de Brechas
* **Marcos Normativos:** Alineación con leyes de privacidad (GDPR, regulaciones locales) y certificaciones internacionales (ISO 27001). Previene sanciones económicas y bloqueos operativos.
* **El Costo Real de un Incidente:** El impacto no se limita al rescate o al robo de información; abarca:
  * Honorarios legales y peritaje forense.
  * Multas regulatorias por incumplimiento.
  * Costos de notificación y compensación a usuarios afectados.
  * Pérdida irreversible de confianza y daño a la reputación de marca.

#### 3.3 Tolerancia a Fallos en Almacenamiento: RAID
* **RAID (*Redundant Array of Independent Disks*):**
  * Arquitectura que agrupa múltiples unidades de almacenamiento físico en una sola entidad lógica.
  * **Técnicas fundamentales:**
    * *Striping* (Distribución de datos entre discos): Aumenta el rendimiento de lectura y escritura.
    * *Mirroring* (Duplicación en espejo): Proporciona redundancia idéntica e inmediata.
    * *Paridad:* Cálculo matemático que permite reconstruir la información perdida de un disco dañado sin duplicar el volumen completo.

---

## RESUMEN RÁPIDO: Conceptos Clave para Examen

| Concepto | Definición en una frase |
| :--- | :--- |
| **PK (Primary Key)** | Campo único e irrepetible que identifica una fila. |
| **FK (Foreign Key)** | Campo que apunta a una PK externa para mantener relaciones. |
| **Integridad Referencial** | Garantía de que ninguna FK referencie un dato inexistente. |
| **Normalización (1NF a 3NF)** | Proceso para eliminar duplicados y dependencias transitivas/parciales. |
| **Índice** | Estructura que agiliza búsquedas evitando recorrer toda la tabla. |
| **NoSQL** | Motores no relacionales, escalables horizontalmente y con esquemas flexibles. |
| **Zero Trust** | Modelo de seguridad donde nadie es de confianza por defecto, ni siquiera dentro de la red. |
| **PoLP** | Dar únicamente los accesos mínimos estrictamente necesarios. |
| **Tríada CIA** | Confidencialidad, Integridad y Disponibilidad. |
| **DRP** | Plan formal para recuperar los sistemas críticos tras una catástrofe. |
| **RAID** | Combinación de discos para balancear velocidad, paridad y redundancia física. |
