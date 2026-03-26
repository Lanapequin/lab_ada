# 🧪 Laboratorio 2 — Especificaciones de la App
### App: Simulaciones Educativas sobre el Antiguo Egipto
### Público objetivo: Estudiantes de secundaria en Colombia (12–17 años)

---

## 📋 Historias de Usuario

| ID | Historia |
|---|---|
| HU-01 | Como estudiante de secundaria, quiero explorar simulaciones de la vida diaria en el Antiguo Egipto (alimentación, trabajo, religión), para comprender cómo vivía la gente común más allá de los faraones. |
| HU-02 | Como estudiante, quiero responder preguntas interactivas después de cada simulación, para verificar si entendí lo que aprendí y recibir retroalimentación inmediata. |
| HU-03 | Como estudiante, quiero ver un panel con mis avances y logros desbloqueados, para saber cuánto he aprendido y motivarme a continuar. |
| HU-04 | Como docente, quiero revisar el progreso de mis estudiantes y asignar módulos específicos, para integrar la app en mis clases de historia. |
| HU-05 | Como estudiante en Colombia con conexión limitada, quiero que la app funcione con contenido descargable o ligero, para poder usarla aunque no tenga buena señal. |

---

## 🗺️ Mapa de Componentes v1

### Módulos principales

- **M1:** Autenticación y Perfiles
- **M2:** Simulaciones Educativas
- **M3:** Evaluación y Retroalimentación
- **M4:** Progreso y Gamificación
- **M5:** Panel Docente
- **M6:** Contenido Offline

---

### Componentes por módulo

#### M1 — Autenticación y Perfiles

| ID | Nombre | Tipo |
|---|---|---|
| C1 | Formulario de registro/login | UI |
| C2 | Servicio de autenticación | Servicio |
| C3 | Base de datos de usuarios | Datos |

#### M2 — Simulaciones Educativas

| ID | Nombre | Tipo |
|---|---|---|
| C4 | Pantalla de simulación interactiva | UI |
| C5 | Motor de escenarios (lógica de simulación) | Servicio |
| C6 | Repositorio de contenidos históricos | Datos |

#### M3 — Evaluación y Retroalimentación

| ID | Nombre | Tipo |
|---|---|---|
| C7 | Componente de preguntas/retos | UI |
| C8 | Servicio de evaluación y calificación | Servicio |
| C9 | Base de datos de respuestas | Datos |

#### M4 — Progreso y Gamificación

| ID | Nombre | Tipo |
|---|---|---|
| C10 | Panel de progreso del estudiante | UI |
| C11 | Servicio de logros y puntos | Servicio |

#### M5 — Panel Docente

| ID | Nombre | Tipo |
|---|---|---|
| C12 | Dashboard docente | UI |
| C13 | Servicio de reportes y asignación | Servicio |

#### M6 — Contenido Offline

| ID | Nombre | Tipo |
|---|---|---|
| C14 | Gestor de descarga de contenido | Servicio |
| C15 | Almacenamiento local | Datos |

---

### Dependencias principales

```
C1  → C2   (formulario login → servicio auth)
C2  → C3   (servicio auth → base de datos usuarios)
C4  → C5   (pantalla simulación → motor de escenarios)
C5  → C6   (motor escenarios → repositorio de contenidos)
C7  → C8   (preguntas → servicio evaluación)
C8  → C9   (evaluación → base de datos respuestas)
C8  → C11  (evaluación → servicio de logros)
C10 → C11  (panel progreso → servicio logros)
C12 → C13  (dashboard docente → servicio reportes)
C14 → C15  (gestor descarga → almacenamiento local)
C4  → C14  (simulación → gestor offline)
```

---

### Notas

- ✅ **MVP incluye:** C1, C2, C3, C4, C5, C6, C7, C8, C10, C11
- 🔮 **Futuro:** C12, C13 (panel docente), C14, C15 (modo offline completo)

---

## 📦 Especificaciones de Componentes (2.2)

---

### Especificación 1: Formulario de Registro/Login (C1)

**Tipo:** UI  
**Propósito:** Permitir que estudiantes y docentes creen una cuenta o inicien sesión para acceder a la app de forma segura y personalizada.  
**Historias relacionadas:** HU-01, HU-04

**Entradas:**
- Correo electrónico
- Contraseña
- Rol (estudiante / docente)
- Nombre completo (solo en registro)

**Salidas:**
- Token de sesión activo
- Redirección al panel correspondiente (estudiante o docente)
- Mensaje de error si los datos son incorrectos

**Reglas de negocio:**
- La contraseña debe tener mínimo 8 caracteres
- El correo no puede estar duplicado en la base de datos
- El rol seleccionado define la vista que se carga al ingresar

**Flujo principal:**
1. El usuario abre la app y ve la pantalla de bienvenida
2. Elige "Registrarse" o "Iniciar sesión"
3. Completa el formulario con sus datos
4. El sistema valida los datos con el Servicio de Autenticación (C2)
5. Si es válido, redirige al panel del rol correspondiente

**Requisitos no funcionales (RNF):**
- **Rendimiento:** El login debe completarse en menos de 2 segundos
- **Seguridad:** Las contraseñas deben almacenarse cifradas (hash bcrypt)
- **Accesibilidad:** Etiquetas ARIA en todos los campos; contraste WCAG AA

**Criterios de aceptación:**
- Dado que un usuario nuevo completa todos los campos válidos, cuando hace clic en "Registrarse", entonces se crea su cuenta y es redirigido a su panel en menos de 2 segundos.
- Dado que un usuario ingresa una contraseña incorrecta, cuando hace clic en "Ingresar", entonces ve un mensaje de error claro sin revelar qué campo específico falló.

---

### Especificación 2: Pantalla de Simulación Interactiva (C4)

**Tipo:** UI  
**Propósito:** Mostrar escenarios visuales e interactivos de la vida cotidiana en el Antiguo Egipto, permitiendo al estudiante explorar roles, ambientes y situaciones históricas.  
**Historias relacionadas:** HU-01, HU-05

**Entradas:**
- Módulo/tema seleccionado (ej.: "Vida en el Nilo", "Mercado de Menfis")
- Decisiones del usuario durante la simulación
- Datos del repositorio de contenidos (C6)

**Salidas:**
- Escenario animado o ilustrado con narración
- Opciones de interacción (botones, rutas de decisión)
- Resultado de la escena según la elección del estudiante

**Reglas de negocio:**
- Cada simulación debe tener mínimo 3 puntos de decisión
- El contenido histórico debe estar validado por fuentes confiables (cargadas en C6)
- Si no hay conexión, carga la versión descargada (vía C14)

**Flujo principal:**
1. El estudiante selecciona un tema desde el menú principal
2. La pantalla carga el escenario con ilustración y texto introductorio
3. El estudiante toma decisiones en los puntos de interacción
4. El motor de escenarios (C5) procesa la decisión y avanza la narrativa
5. Al finalizar, se activa automáticamente el módulo de evaluación (C7)

**Requisitos no funcionales (RNF):**
- **Rendimiento:** La simulación debe cargar en menos de 3 segundos con conexión estándar
- **Accesibilidad:** Texto legible en pantallas de 5 pulgadas; fuente mínima 14px
- **Usabilidad:** Navegación con no más de 2 toques para cualquier acción principal

**Criterios de aceptación:**
- Dado que el estudiante selecciona un tema, cuando la simulación carga, entonces ve el escenario con al menos una imagen, texto contextual y una opción de interacción en menos de 3 segundos.
- Dado que el estudiante toma una decisión en un punto de interacción, cuando confirma su elección, entonces la narrativa avanza con una consecuencia histórica coherente.

---

### Especificación 3: Motor de Escenarios (C5)

**Tipo:** Servicio  
**Propósito:** Gestionar la lógica de las simulaciones: cargar escenarios, procesar decisiones del usuario y determinar qué contenido mostrar a continuación según las elecciones.  
**Historias relacionadas:** HU-01

**Entradas:**
- ID del escenario seleccionado
- Decisión del usuario (ID de opción elegida)
- Datos del repositorio de contenidos (C6)

**Salidas:**
- Siguiente escenario o pantalla según la decisión
- Indicadores de progreso para C11
- Señal de fin de simulación para activar C7

**Reglas de negocio:**
- Cada escenario tiene un árbol de decisiones predefinido (mínimo 2 ramificaciones)
- Las decisiones incorrectas históricamente muestran retroalimentación educativa, no solo error
- El motor registra el camino recorrido por el estudiante para reportes

**Flujo principal:**
1. Recibe el ID del escenario desde la pantalla de simulación (C4)
2. Consulta el repositorio (C6) y carga los datos del escenario
3. Devuelve a C4 el contenido y las opciones del primer nodo
4. Recibe la decisión del usuario y evalúa el árbol de decisiones
5. Devuelve el siguiente nodo o activa el fin de simulación

**Requisitos no funcionales (RNF):**
- **Rendimiento:** Procesamiento de decisión en menos de 500ms
- **Escalabilidad:** Debe soportar hasta 200 usuarios concurrentes en MVP
- **Mantenibilidad:** Los árboles de decisión deben ser editables sin cambiar código

**Criterios de aceptación:**
- Dado que el estudiante elige una opción, cuando el motor la procesa, entonces devuelve el siguiente nodo en menos de 500ms.
- Dado que el estudiante toma una decisión históricamente incorrecta, cuando el motor responde, entonces muestra un mensaje educativo explicando el contexto histórico correcto.

---

### Especificación 4: Componente de Preguntas/Retos (C7)

**Tipo:** UI  
**Propósito:** Presentar al estudiante preguntas de comprensión al finalizar cada simulación, para verificar el aprendizaje y ofrecer retroalimentación inmediata.  
**Historias relacionadas:** HU-02

**Entradas:**
- ID de la simulación completada
- Preguntas asociadas desde la base de datos (C9)
- Respuestas del estudiante

**Salidas:**
- Puntaje obtenido en la evaluación
- Retroalimentación por cada respuesta (correcta/incorrecta + explicación)
- Datos enviados al servicio de evaluación (C8) y logros (C11)

**Reglas de negocio:**
- Cada simulación tiene entre 3 y 5 preguntas asociadas
- El estudiante debe responder todas para avanzar al siguiente módulo
- Una puntuación ≥ 70% desbloquea el siguiente tema

**Flujo principal:**
1. Se activa automáticamente al finalizar la simulación
2. Carga las preguntas correspondientes desde C9
3. Muestra una pregunta a la vez con opciones de respuesta
4. Al responder, muestra retroalimentación inmediata
5. Al completar todas, envía resultados a C8 y muestra resumen final

**Requisitos no funcionales (RNF):**
- **Rendimiento:** Carga de preguntas en menos de 1 segundo
- **Accesibilidad:** Botones de respuesta con área de toque mínima de 44x44px
- **Usabilidad:** Retroalimentación visible por al menos 3 segundos antes de avanzar

**Criterios de aceptación:**
- Dado que el estudiante termina una simulación, cuando se activa el módulo de retos, entonces ve la primera pregunta cargada en menos de 1 segundo.
- Dado que el estudiante responde una pregunta incorrectamente, cuando confirma su respuesta, entonces ve una explicación histórica de por qué es incorrecta.
- Dado que el estudiante obtiene ≥ 70%, cuando termina la evaluación, entonces el siguiente módulo queda desbloqueado en su panel.

---

### Especificación 5: Panel de Progreso del Estudiante (C10)

**Tipo:** UI  
**Propósito:** Mostrar al estudiante un resumen visual de sus avances, módulos completados, puntajes obtenidos y logros desbloqueados para motivar la continuidad del aprendizaje.  
**Historias relacionadas:** HU-03

**Entradas:**
- Datos de simulaciones completadas (desde C8)
- Logros y puntos acumulados (desde C11)
- Perfil del usuario (desde C3)

**Salidas:**
- Visualización de progreso por módulo (completado / en curso / bloqueado)
- Lista de logros obtenidos
- Porcentaje general de avance en la app

**Reglas de negocio:**
- Un módulo aparece como "completado" solo si la evaluación superó el 70%
- Los logros se muestran con nombre, ícono y fecha de obtención
- El porcentaje general se calcula sobre el total de módulos disponibles

**Flujo principal:**
1. El estudiante accede al panel desde el menú principal
2. El sistema consulta C8 y C11 para obtener datos actualizados
3. Renderiza el mapa de módulos con estados visuales
4. Muestra sección de logros desbloqueados
5. Muestra porcentaje global de avance

**Requisitos no funcionales (RNF):**
- **Rendimiento:** El panel debe cargar en menos de 2 segundos
- **Usabilidad:** El progreso debe ser comprensible de un vistazo sin leer texto
- **Accesibilidad:** Usar íconos + colores + texto (no solo color para indicar estado)

**Criterios de aceptación:**
- Dado que el estudiante completa una simulación con ≥ 70%, cuando abre su panel, entonces el módulo aparece marcado como completado con su puntaje.
- Dado que el estudiante desbloquea un logro, cuando lo ve en el panel, entonces aparece con nombre, ícono y fecha.
- Dado que el estudiante tiene 0% de avance, cuando abre el panel por primera vez, entonces ve todos los módulos como "bloqueados" excepto el primero.

---

### Especificación 6: Servicio de Evaluación y Calificación (C8)

**Tipo:** Servicio  
**Propósito:** Procesar las respuestas del estudiante, calcular su puntaje, almacenarlo y comunicar los resultados al panel de progreso y al servicio de logros.  
**Historias relacionadas:** HU-02, HU-03

**Entradas:**
- ID del estudiante
- ID de la simulación evaluada
- Respuestas seleccionadas por el estudiante

**Salidas:**
- Puntaje calculado (0–100%)
- Indicador de aprobación (≥ 70% = aprobado)
- Datos guardados en C9
- Señal a C11 para actualizar logros

**Reglas de negocio:**
- Cada respuesta correcta suma puntos iguales (puntaje total / número de preguntas)
- Si el puntaje es < 70%, el módulo no se desbloquea pero el estudiante puede reintentar
- Se permite máximo 3 intentos por simulación; al tercero se muestran las respuestas correctas

**Flujo principal:**
1. Recibe las respuestas del componente C7
2. Compara con las respuestas correctas almacenadas en C9
3. Calcula el puntaje total
4. Guarda el resultado en C9 vinculado al estudiante
5. Notifica a C11 si se cumplen condiciones de logro y devuelve resultado a C7

**Requisitos no funcionales (RNF):**
- **Rendimiento:** Cálculo y respuesta en menos de 1 segundo
- **Seguridad:** Las respuestas correctas no deben ser accesibles desde el cliente
- **Confiabilidad:** El resultado debe guardarse aunque la conexión falle (reintentos automáticos)

**Criterios de aceptación:**
- Dado que el estudiante responde todas las preguntas, cuando el servicio procesa los resultados, entonces devuelve el puntaje correcto en menos de 1 segundo.
- Dado que el estudiante obtiene < 70% por tercera vez, cuando termina el intento, entonces ve las respuestas correctas con explicación.
- Dado que el estudiante aprueba, cuando el servicio guarda el resultado, entonces C11 recibe la señal y actualiza los logros correspondientes.

---

### Especificación 7: Repositorio de Contenidos Históricos (C6)

**Tipo:** Datos  
**Propósito:** Almacenar y servir todo el contenido educativo de la app: escenarios, textos históricos, imágenes, árboles de decisión y preguntas de evaluación, validados pedagógicamente.  
**Historias relacionadas:** HU-01, HU-02, HU-05

**Entradas:**
- Solicitudes del motor de escenarios (C5)
- Solicitudes del componente de preguntas (C7)
- Contenido cargado por administrador (futuro panel de administración)

**Salidas:**
- Datos del escenario solicitado (texto, imágenes, árbol de decisiones)
- Preguntas y respuestas correctas para cada simulación
- Versión comprimida del contenido para descarga offline (a C14)

**Reglas de negocio:**
- Todo contenido debe estar etiquetado con: tema, nivel de dificultad y fuente histórica
- Las imágenes deben tener licencia libre o ser ilustraciones propias
- El contenido debe estar disponible en español colombiano

**Flujo principal:**
1. C5 o C7 solicita contenido por ID de escenario o simulación
2. El repositorio busca el registro en la base de datos
3. Valida que el contenido esté activo y disponible
4. Devuelve los datos en formato JSON estructurado
5. Registra la solicitud para métricas de uso

**Requisitos no funcionales (RNF):**
- **Rendimiento:** Tiempo de respuesta menor a 1 segundo para cualquier consulta
- **Escalabilidad:** Debe soportar al menos 50 escenarios en MVP sin degradar rendimiento
- **Integridad:** Respaldos automáticos diarios; sin pérdida de contenido validado

**Criterios de aceptación:**
- Dado que C5 solicita un escenario por ID, cuando el repositorio responde, entonces entrega el objeto completo (texto + árbol de decisiones) en menos de 1 segundo.
- Dado que se agrega contenido nuevo, cuando es publicado, entonces está disponible para todos los usuarios sin necesidad de actualizar la app.
- Dado que un estudiante usa la app offline, cuando C14 solicita el contenido, entonces el repositorio entrega una versión comprimida descargable del módulo completo.

---

## ✅ Trazabilidad HU → Componentes

| Historia de Usuario | Componentes que la cubren |
|---|---|
| HU-01 | C1, C4, C5, C6 |
| HU-02 | C7, C8, C9 |
| HU-03 | C10, C11 |
| HU-04 | C12, C13 *(futuro)* |
| HU-05 | C4, C6, C14, C15 |