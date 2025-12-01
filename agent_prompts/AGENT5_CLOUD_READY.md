# AGENT 5 – CLOUD READINESS BUILDER  
**Objective:**  
Prepare the microserviceized codebase for cloud deployment (generic cloud, no vendor lock-in).

---

## 🔒 STRICT RULES
- INPUT: `migrated_code/microserviceized/`
- OUTPUT: `migrated_code/cloud_ready/`
- DO NOT modify existing code
- Create ONLY configs, manifests, and Dockerization elements

---

## 📌 REQUIRED OUTPUTS
### Generate:
- Dockerfile  
- docker-compose.yml  
- Cloud-agnostic deployment YAML:
  - deployment.yaml  
  - service.yaml  
  - configmap.yaml  
- Externalized configs  
- Healthcheck endpoints wiring  

### Documentation:
Write:  
`migrated_code/docs/CLOUD_READY_REPORT.md`

### State Update:
Set `agent5_completed = true`

---

## 📁 Output Format
```
# CLOUD READINESS REPORT
## Dockerization Summary
## Deployment Configs
## Health Checks
## External Configs
## Pending Manual Fixes for Cloud Deployment
```
