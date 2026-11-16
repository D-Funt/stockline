# Progreso de Stockline

Única fuente del temario. Claude la actualiza solo cuando Dylan confirma que un tema cumple la definición de terminado (ver `CLAUDE.md`).

Leyenda: `[ ]` pendiente · `[~]` a medias · `[x]` terminado

## Resumen

| Nivel | Hechos | Total | % |
|---|---|---|---|
| Principal | 0 | 38 | 0 % |
| Complementario | 0 | 42 | 0 % |
| Extensión | 0 | 15 | 0 % |
| **Total** | **0** | **95** | **0 %** |

**Tema actual:** P0. Preparación

---

## PRINCIPALES (núcleo secuencial)

### P0. Preparación
- [ ] Repo stockline en GitHub, README, .gitignore, convención de commits pequeños y de ramas
- [ ] Herramientas: Docker (y Compose), kind, kubectl, git, gh
- [ ] Fichero docs/PROGRESO.md y plantilla de notas por etapa

### P1. App y contenedor (Docker, Django en producción, 12-factor)
- [ ] App Django con los endpoints y las reglas de producción (config por entorno, health live y ready, logs JSON, migraciones aparte)
- [ ] Dockerfile multi-stage, usuario sin privilegios, base fijada, .dockerignore, forma exec y gestión de SIGTERM
- [ ] Docker Compose con la app y PostgreSQL, con healthcheck
- [ ] Análisis de la imagen: tamaño, capas (docker history), caché; secretos de construcción (BuildKit)
- [ ] Tags por SHA de commit; referencia por digest; buildx y multi-arquitectura
- [ ] Comparar con Podman y Buildah

### P2. CI con GitHub Actions y seguridad del pipeline
- [ ] Workflow de CI: tests, matriz, caché, artifacts, concurrency, timeouts, permisos mínimos del token
- [ ] Construcción y publicación en GHCR (buildx, login, metadata, caché de capas)
- [ ] Actions fijadas por SHA, Dependabot (github-actions, docker y pip), actionlint
- [ ] Escaneos: CodeQL, Trivy (imagen, dependencias y configuración), SBOM, secret scanning y push protection
- [ ] Environments con aprobación, restricción de ramas, variables y secrets bien usados
- [ ] Protección de rama o ruleset con checks obligatorios y CODEOWNERS sobre .github/workflows/
- [ ] Prueba de seguridad: inyección en un step a propósito y su arreglo; diferencia pull_request y pull_request_target
- [ ] OIDC con la nube a nivel de concepto práctico (se completa en M4)

### P3. Kubernetes a mano (kind)
- [ ] Clúster kind (varios nodos), namespaces, contextos de kubectl
- [ ] Deployment, Service, ConfigMap y Secret; configuración inyectada por entorno y por ficheros
- [ ] PostgreSQL con almacenamiento persistente (StatefulSet y PVC); comparar con un servicio gestionado
- [ ] Probes (liveness, readiness y startup), requests y limits, QoS
- [ ] Job de migraciones (o initContainer) y su orden respecto a la app
- [ ] Rolling update sin caída (maxSurge, maxUnavailable, terminationGracePeriodSeconds, preStop), rollout status, history y undo
- [ ] PodDisruptionBudget, HPA, Ingress (controlador en kind) y RBAC mínimo con ServiceAccount propia
- [ ] Ejercicio de diagnóstico: romper 6 cosas (imagen, probe, selector, memoria, secreto, permisos) y resolverlas solo con kubectl

### P4a. Despliegue desde el pipeline
- [ ] Decidir cómo llega el pipeline al clúster (runner self-hosted o clúster accesible) y justificarlo
- [ ] Paso de despliegue con la imagen por SHA, environment con aprobación y verificación del rollout
- [ ] Rollback probado, y análisis de pros y contras del modelo push

### P4b. GitOps con Argo CD y Kustomize
- [ ] Repo de manifiestos separado; Kustomize con base y overlays (dev y prod); generadores con hash
- [ ] Argo CD en kind: Application, AppProject, sincronización automática con prune y selfHeal
- [ ] Sync waves y hook PreSync para la migración
- [ ] Prueba de deriva: cambio manual en el clúster y reacción de Argo CD
- [ ] El pipeline actualiza la versión de la imagen en el repo de manifiestos (commit o PR); rollback con git revert
- [ ] App of Apps o ApplicationSet; ignoreDifferences (caso HPA); notificaciones básicas

### P5. Golden path
- [ ] Reusable workflow en un repo central con inputs, secrets y outputs; versionado por SHA o tag
- [ ] Composite action para pasos repetidos
- [ ] Segunda app mínima que lo consuma con una línea; documentación de uso
- [ ] Reflexión: required workflows en rulesets, plantillas de organización y cómo ofrecerlo a 200 repos

---

## COMPLEMENTARIOS (módulos, en orden)

### M1. Migración desde Jenkins (y Azure DevOps a nivel de concepto)
- [ ] Jenkins en Docker y un Jenkinsfile de la app (declarativo, parámetros, post, credenciales)
- [ ] GitHub Actions Importer: configure, audit, forecast, dry-run y migrate
- [ ] Comparar con mi workflow y anotar lo no convertible (shared libraries, plugins)
- [ ] Tabla de equivalencias Jenkins, Azure DevOps y Actions; plan de migración por olas y paridad

### M2. Runners self-hosted y Actions Runner Controller
- [ ] ARC con runner scale sets en kind, autenticación con GitHub App o token, runners efímeros
- [ ] Usarlo para desplegar al clúster (cierra P4a) y limitarlo con grupos de runners
- [ ] Análisis de riesgos y de cómo construir imágenes dentro del runner sin privilegios

### M3. Secretos
- [ ] Demostrar por qué un Secret de Kubernetes no es cifrado
- [ ] Sealed Secrets o External Secrets en el repo de manifiestos; comparar con SOPS
- [ ] Estrategia de rotación y plan para una filtración; detección con gitleaks o secret scanning
- [ ] Conceptos: identidad de carga de trabajo (IRSA o equivalentes), Vault y cert-manager

### M4. IaC y OIDC (Terraform, y Ansible a nivel básico)
- [ ] Terraform: recursos pequeños, variables, outputs, módulo propio, estado remoto con bloqueo, plan en cada PR
- [ ] Rol IAM con proveedor OIDC de GitHub y política de confianza restringida; el workflow asume el rol sin claves
- [ ] Escaneo de configuración (Trivy config o Checkov), tflint, y terraform destroy de todo lo creado
- [ ] Ansible: playbook idempotente con inventario, handlers y --check --diff
- [ ] Atención a costes en la nube: pedir confirmación antes de crear nada que cobre

### M5. Métricas y DORA (Prometheus y Grafana)
- [ ] Prometheus y Grafana en el clúster; métricas de la API (peticiones, latencia, errores)
- [ ] ServiceMonitor, reglas de alerta y Alertmanager; consultas PromQL (rate, percentiles)
- [ ] Definir SLIs y un SLO con su presupuesto de error
- [ ] Panel DORA con las cinco métricas actuales, con datos de la API de GitHub en una base de datos; definiciones de despliegue y fallo acordadas

### M6. Logs con Elastic Stack (y OpenSearch)
- [ ] Antes de empezar: comprobar requisitos de memoria y recursos de mi máquina
- [ ] Elasticsearch y Kibana en el clúster (operador ECK u otra opción confirmada en la documentación)
- [ ] Recolector de logs como DaemonSet (Fluent Bit o Filebeat) con metadatos de Kubernetes
- [ ] Logs JSON de Django con campos útiles (esquema ECS); consultas en Kibana, dashboard y alerta
- [ ] Ciclo de vida de los datos (data streams e ILM) y comprobación de salud del clúster
- [ ] Comparar con OpenSearch y con Loki

### M7. Seguridad del clúster y de las imágenes
- [ ] Pod Security (estándares y etiquetas por namespace, en warn y audit antes de enforce)
- [ ] securityContext completo en mi Deployment (cumplir el nivel restricted)
- [ ] NetworkPolicies con denegación por defecto y permisos mínimos (comprobar que el CNI las aplica)
- [ ] Kyverno o Gatekeeper con políticas útiles (requests y limits, sin latest, registries permitidos)
- [ ] Firma de la imagen con Cosign (keyless) y verificación en admisión; proveniencia con attestations
- [ ] RBAC revisado; conceptos: auditoría, Falco, kube-bench

### M8. Entrega progresiva
- [ ] Argo Rollouts con canary y con blue-green; pasos y pausas
- [ ] Análisis automático con métricas de Prometheus; abortar y promover
- [ ] Compatibilidad de la base de datos (expand y contract) para un cambio de esquema real
- [ ] Feature flags a nivel de concepto (OpenFeature) y comparación con Flagger

### M9. Helm
- [ ] Chart de la API con values por entorno, plantillas, helm template, lint, hooks de migración
- [ ] Argo CD desplegando el chart; comparar con Kustomize y razonar cuándo usar cada uno
- [ ] Instalar un software de terceros con un chart (por ejemplo, una dependencia del proyecto)

### M10. Para el final
- [ ] Flux (bootstrap, GitRepository, Kustomization, HelmRelease) para comparar con Argo CD
- [ ] Tekton y Backstage: leer, ver una demo y poder explicarlos y cuándo elegirlos
- [ ] Ansible más allá de lo básico (roles, colecciones, Vault) si quedó pendiente

---

## EXTENSIONES (conceptual o mini-demo)

Para cada una: Claude la explica, dice si merece una mini-demo en el proyecto y ayuda a decidir.

- [ ] cert-manager y TLS (certificados en el Ingress)
- [ ] Backups y recuperación con Velero (simular una pérdida y restaurar)
- [ ] Costes: OpenCost o Kubecost y FinOps
- [ ] Crossplane y Cluster API (infraestructura desde Kubernetes)
- [ ] Service mesh (mTLS, división de tráfico) y Gateway API
- [ ] Policy as code con OPA y Conftest; pruebas de infraestructura (Terratest, kuttl)
- [ ] SRE: SLOs avanzados, gestión de incidentes y postmortem (escribir uno sobre un fallo real del proyecto)
- [ ] Platform engineering: golden paths, plataforma como producto e IDP; qué métricas de adopción usaría
- [ ] GitHub a escala: EMU, rulesets de organización, registro de auditoría, GitHub Apps, API REST y GraphQL (script que audite repos)
- [ ] Gestión de artefactos: GitHub Packages, Artifactory y Nexus
- [ ] Kubernetes gestionado (EKS, AKS y GKE) y qué cambia respecto a kind
- [ ] Linux tipo RHEL (systemd, dnf, SELinux) y Podman en profundidad
- [ ] IA en el ciclo de desarrollo: gobierno, revisión humana, permisos de agentes y un ejemplo propio
- [ ] Marco SPACE y límites de las métricas de productividad
- [ ] Itinerario de certificaciones (CKS, Terraform, GitHub, AWS DevOps) con fechas realistas
