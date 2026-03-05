# Taller 1 — Fundamentos de DevSecOps

## ¿Qué es DevSecOps?

**DevSecOps** significa **Development (Desarrollo) + Security (Seguridad) + Operations (Operaciones)**.

Es una metodología que integra la seguridad durante **todo el ciclo de vida del software**, no solo al final. Su enfoque principal es que desarrollo, operaciones y seguridad trabajen de forma coordinada desde el inicio.

## Objetivo principal

Detectar y corregir vulnerabilidades **lo antes posible** para reducir:

- Riesgo de incidentes de seguridad.
- Costo de corrección en etapas tardías.
- Tiempo de entrega afectado por retrabajo.

## Seguridad en cada etapa del ciclo DevSecOps

### 1) Planificación

- Identificar riesgos técnicos y de negocio.
- Definir requisitos de seguridad (autenticación, cifrado, trazabilidad, cumplimiento).
- Establecer criterios de aceptación de seguridad.

### 2) Desarrollo

- Aplicar prácticas de código seguro.
- Realizar revisión de código (peer review).
- Usar análisis estático de código (SAST).
- Gestionar dependencias con control de vulnerabilidades.

### 3) Integración Continua (CI/CD)

- Ejecutar escaneo automático de vulnerabilidades.
- Automatizar pruebas de seguridad en pipelines.
- Bloquear despliegues si hay hallazgos críticos.

### 4) Despliegue

- Configuración segura por defecto (hardening).
- Control de acceso basado en roles (RBAC).
- Gestión segura de secretos (tokens, llaves, contraseñas).

### 5) Monitoreo y Respuesta

- Centralización de logs y alertas.
- Detección temprana de comportamientos anómalos.
- Plan de respuesta a incidentes y mejora continua.

---

## Preguntas orientadoras — Análisis DevSecOps

### 1) Trazabilidad

**Pregunta:** ¿Por qué es un riesgo de seguridad dejar la configuración de `user.name` y `user.email` vacía o utilizar datos genéricos en un entorno empresarial?

**Respuesta:**
En un entorno empresarial, cada commit debe poder asociarse a una identidad real y verificable. Si `user.name` y `user.email` están vacíos o son genéricos:

- Se pierde la trazabilidad de cambios.
- Se dificulta identificar al responsable de un error o vulnerabilidad.
- Se complica una auditoría o investigación forense.
- Se debilitan controles de cumplimiento y responsabilidad.

**Buena práctica:** configurar identidad corporativa correcta y exigir commits firmados cuando aplique.

---

### 2) Cifrado asimétrico (SSH)

**Pregunta:** ¿Cuál es la diferencia funcional entre la llave privada y la pública? ¿Qué pasaría si un tercero obtiene acceso a tu llave privada?

**Respuesta:**
En autenticación SSH se usan dos llaves:

- **Llave pública:** se comparte con el servidor (por ejemplo GitHub/GitLab) para reconocer al usuario.
- **Llave privada:** permanece solo en el equipo del usuario y prueba su identidad al firmar el reto criptográfico.

Si un tercero obtiene la llave privada, puede autenticarse como el usuario legítimo y:

- Acceder a repositorios.
- Subir o modificar código malicioso.
- Exfiltrar información sensible.

**Buena práctica:** proteger la llave privada con passphrase, no compartirla nunca y rotarla ante sospecha de compromiso.

---

### 3) Principio de mínimo privilegio (PAT)

**Pregunta:** ¿Qué riesgos conlleva asignar permisos de “Administrador” (Full Control) a un PAT en lugar de permisos de lectura/escritura?

**Respuesta:**
El principio de mínimo privilegio indica que un usuario o token debe tener únicamente los permisos necesarios para su función.

Un PAT con **Full Control** incrementa el impacto ante filtración o robo, permitiendo acciones críticas como:

- Eliminar repositorios.
- Modificar configuraciones sensibles.
- Gestionar colaboradores y permisos.
- Acceder a recursos innecesarios.

Con permisos limitados (lectura/escritura), el daño potencial se reduce significativamente.

**Buena práctica:** definir alcance mínimo, fecha de expiración y rotación periódica de tokens.

---

### 4) Higiene del repositorio (`.gitignore`)

**Pregunta:** ¿Para qué sirve `.gitignore` desde una perspectiva de seguridad?

**Respuesta:**
`.gitignore` evita que archivos sensibles o irrelevantes entren al repositorio remoto, reduciendo la exposición accidental de datos.

**Archivos que nunca deberían subirse (ejemplos):**

- Credenciales y secretos: `.env`, `*.pem`, `*.key`, `id_rsa`.
- Archivos de configuración local con datos sensibles: `config.local.*`.
- Artefactos temporales o del sistema: `.DS_Store`, `Thumbs.db`.
- Logs con información sensible: `*.log`.

---

### 5) Exposición de secretos en GitHub

**Pregunta:** Si accidentalmente subes una API key o contraseña, ¿basta con borrarla y hacer un nuevo commit?

**Respuesta:**
No. El secreto continúa en el historial de Git, aunque se elimine en commits posteriores.

**Acciones correctas inmediatas:**

1. Revocar o deshabilitar la credencial comprometida.
2. Generar una nueva credencial.
3. Eliminar el secreto del historial (`git filter-repo` o alternativa equivalente).
4. Forzar actualización de clones si aplica.
5. Implementar prevención: secretos en variables de entorno o gestor de secretos.

---

## Buenas prácticas profesionales recomendadas

- Activar MFA en cuentas de repositorio y nube.
- Habilitar escaneo de secretos (secret scanning).
- Aplicar protección de ramas (`main`/`master`) con pull requests obligatorios.
- Exigir revisiones de código y status checks antes de merge.
- Automatizar SAST, DAST y análisis de dependencias (SCA) en CI.
- Mantener inventario y actualización de librerías vulnerables.
- Definir política de respuesta a incidentes y postmortem.

## Herramientas comunes en DevSecOps

- **SAST:** SonarQube, Semgrep.
- **SCA (dependencias):** Dependabot, Snyk.
- **Secret scanning:** Gitleaks, GitHub Secret Scanning.
- **Contenedores e infraestructura:** Trivy, Checkov.
- **Monitoreo y detección:** ELK/Opensearch, Prometheus + Alertmanager, SIEM.

## Checklist mínimo para un repositorio seguro

- [ ] Identidad Git corporativa correctamente configurada.
- [ ] Autenticación fuerte (SSH con passphrase o token con MFA).
- [ ] `.gitignore` con exclusión de secretos y archivos locales.
- [ ] Pipeline CI/CD con escaneo automático de seguridad.
- [ ] Protección de ramas y revisión obligatoria de código.
- [ ] Rotación periódica de credenciales y tokens.

## Conclusión

DevSecOps no es una herramienta única, sino una cultura de trabajo que integra seguridad de forma continua. Cuanto antes se incorporen controles de seguridad en el ciclo de desarrollo, menor será el impacto de vulnerabilidades en producción y mayor la confiabilidad del software.
