# Chatbot de RRHH para Gestión Organizacional con n8n e IA

Este proyecto consiste en un agente de inteligencia artificial integrado en un flujo de automatización con **n8n** y **Telegram**. El bot actúa como un asistente virtual completo de Recursos Humanos ("Vicky") capaz de interactuar con los usuarios en lenguaje natural, validar sus identidades contra una base de datos corporativa y gestionar de forma centralizada consultas críticas como saldos de vacaciones, liquidación de sueldos, banco de horas, entre otros indicadores, resolviendo de forma inteligente las interrupciones o saludos repetidos.

## 🚀 Funcionalidades

* **Detección de Intenciones:** Clasifica inteligentemente si el usuario está saludando o si está proporcionando datos para un trámite o consulta específica.
* **Gestión Integral de RRHH:** Capacidad para consultar y procesar datos sobre vacaciones, recibos de sueldo, horas extras, saldos de horas y datos generales del empleado.
* **Validación en Base de Datos:** Conexión directa a la base de datos relacional para verificar la existencia del empleado por Nombre y validar su identidad mediante DNI.
* **Gestión de Contexto Compleja:** Si el usuario saluda en medio del proceso de validación, el flujo redirige al saludo corporativo sin perder la memoria de los datos que ya se venían procesando en la sesión de IA.
* **Respuestas Dinámicas:** Recupera el puesto, área, días de vacaciones correspondientes y el estado del banco de horas directamente desde las tablas de la organización.

---

## 🛠️ Stack Tecnológico

* **n8n:** Orquestador del flujo y lógica de nodos.
* **Telegram Bot API:** Interfaz de mensajería para el usuario final.
* **Cohere Chat Model:** Cerebro de IA (LLM) encargado del procesamiento de lenguaje natural y el manejo de la memoria de la sesión.
* **PostgreSQL (Neon):** Base de datos relacional serverless en la nube donde se almacena la información estructurada de la organización (sueldos, horas, vacaciones).
* **Nodos Utilizados:** Telegram Trigger, AI Agent, Switch (Mode: Rules), Simple Memory, Postgres Tool.

---

## 📐 Estructura del Flujo (Workflow)

El diseño del flujo se estructuró en línea recta para el procesamiento cognitivo y utiliza un nodo Switch posterior para la distribución de respuestas, evitando la duplicación de mensajes y la pérdida de memoria:

1. **Telegram Trigger:** Recibe el mensaje de texto del usuario.
2. **AI Agent (Cohere):** Analiza el mensaje con lenguaje natural. Si detecta un saludo en cualquier momento de la charla, devuelve la palabra clave `SALUDO`. Si detecta datos o intenciones de negocio (sueldos, vacaciones, horas), interactúa con la base de datos mediante su herramienta SQL.
3. **Switch Node:** Actúa como un cartero/enrutador evaluando el output del agente:
   * **Salida 0 (Matches Regex / Equal 'SALUDO'):** Redirige al nodo de saludo corporativo fijo.
   * **Salida Fallback (Extra Output):** Envía la respuesta procesada con los datos solicitados (vacaciones, sueldos o banco de horas) al nodo de mensajería final.
4. **Send a text message / Send a text message1:** Nodos de salida de Telegram que envían la respuesta final al usuario de forma limpia.

---

## 📝 Configuración del Agente de IA (System Message)

Para asegurar el correcto funcionamiento del ruteo, el agente tiene integradas las siguientes directivas de comportamiento:

* Identificarse como "Vicky" de Recursos Humanos de ChocolaTech.
* Buscar empleados por Nombre mediante la herramienta de base de datos y solicitar el DNI solo si existe el registro.
* Validar el DNI y retornar de forma clara los datos solicitados, ya sea sobre saldos de sueldo, horas acumuladas o vacaciones de la persona.
* Responder estrictamente con la palabra `SALUDO` ante cualquier saludo del usuario para permitir el correcto desvío en el nodo Switch.

---

## 💻 Cómo replicar este proyecto

1. Descarga el archivo JSON del workflow de n8n.
2. Importa el flujo en tu instancia de n8n.
3. Configura tus credenciales para el **Telegram Bot**, **Cohere API** y tu base de datos **PostgreSQL hospedada en Neon**.
4. Asegúrate de configurar la opción `Determine Output By` en `First match` dentro del nodo Switch.
5. Ejecuta el workflow y testea la persistencia de datos en tu chat de Telegram.
