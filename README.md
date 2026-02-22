# Cloud-Honeypot-WireGuard-SIEM  
![Topología de red](./Topología%20de%20red.png)

---

## 🛡️ Proyecto ASIR – Laboratorio de Ciberseguridad Cloud (DMV)

Infraestructura real desplegada en **Oracle Cloud (ARM64 – Always Free Tier)** orientada a la **captura, análisis y correlación de ataques reales** mediante honeypots de alta interactividad y un SIEM centralizado.

Este proyecto no es una simulación teórica: es un **Despliegue Mínimo Viable (DMV)** completamente funcional, segmentado y validado extremo a extremo.

---

## 🎯 Objetivo del proyecto

- Capturar actividad maliciosa real en Internet.
- Separar claramente **engaño, gestión y análisis**.
- Centralizar eventos en un **SIEM aislado**.
- Implementar un modelo de seguridad basado en:
  - Segmentación de red.
  - Principio de mínimo privilegio.
  - Zero Trust.
  - Infraestructura reproducible y sostenible (0€).

---

## 🏗️ Arquitectura final (Implementación real)

El entorno está desplegado en **3 instancias ARM64 dentro de una VCN privada (10.0.1.0/24)**:

### 1️⃣ Sensor – Honeypots (Nodo expuesto)

- **SO:** Oracle Linux 9 (ARM)
- **IP Pública:** Sí
- **Servicios expuestos:**
  - `22/TCP` → Cowrie (SSH honeypot)
  - `80/TCP` → DVWA (honeypot web)
- **SSH real movido a 2222**
- Wazuh Agent instalado
- Contenedores gestionados con Docker Compose

🔐 El puerto 22 no es el SSH real, sino el honeypot.  
La gestión legítima se realiza en el puerto **2222** con autenticación por clave pública.

---

### 2️⃣ SIEM – wazuh-honeypot (Nodo aislado)

- **SO:** Ubuntu 24.04 LTS (ARM)
- **IP Pública:** ❌ No
- **Acceso:** Solo por red privada o VPN
- **Función:** Wazuh Manager + Dashboard

📌 El SIEM nunca está expuesto a Internet.

---

### 3️⃣ VPN – vpn-wireguard (Gateway seguro)

- **SO:** Ubuntu 24.04 LTS (ARM)
- **Puerto expuesto:** `51820/UDP`
- **Función:** Punto único de administración segura

Permite:
- Acceso privado a toda la VCN.
- Gestión del SIEM sin exponerlo.
- Separación total entre Internet y análisis.

---

## 🔁 Flujo completo validado

Atacante (Internet)
↓
Sensor (Cowrie / DVWA)
↓
Wazuh Agent
↓ (Red privada 10.0.1.0/24)
Wazuh Manager (SIEM)
↓
Dashboard / Correlación


✔ Evento generado  
✔ Registrado en honeypot  
✔ Leído por agente  
✔ Enviado por red privada  
✔ Visualizado en el SIEM  

El ciclo completo fue validado en producción.

---

## 🔧 Tecnologías utilizadas

### ☁️ Cloud
- Oracle Cloud Infrastructure (VCN, Security Lists)
- VM.Standard.A1.Flex (Ampere ARM64 – Always Free Tier)

### 🖥️ Sistemas
- Oracle Linux 9
- Ubuntu 24.04 LTS

### 🎯 Honeypots
- **Cowrie (SSH)**
- **DVWA (Web vulnerable)**

### 📊 Monitorización
- **Wazuh (SIEM)**
- Ingesta directa de logs Docker

### 🔐 Seguridad
- WireGuard (VPN privada)
- Hardening SSH (22 → 2222)
- Separación gestión vs engaño
- Segmentación de red
- Modelo egress-control planificado

### ⚙️ DevOps
- Docker
- Docker Compose
- Script personalizado `start-dvwa.sh`
- Backend DVWA migrado a SQLite

---

## ⚙️ Retos técnicos reales superados

### 🔹 Incompatibilidad ARM64 (`exec format error`)
Muchas imágenes Docker x86_64 no funcionaban en Ampere A1.  
Se utilizaron imágenes compatibles o adaptadas a ARM.

### 🔹 Conflicto 22 vs 2222
Se movió el SSH real a 2222 para liberar el 22 al honeypot.

### 🔹 Dependencias MySQL en DVWA
Se migró a **SQLite** para:
- Reducir complejidad
- Eliminar dependencias externas
- Facilitar restauraciones limpias

### 🔹 Monitorización de contenedores con Wazuh
Se optó por ingesta directa de logs desde el host para garantizar trazabilidad completa.

---

## 🧱 Modelo de seguridad aplicado

- Mínimo privilegio en Security Lists.
- SIEM sin IP pública.
- Gestión solo por VPN.
- Autenticación SSH por clave pública.
- Separación total entre:
  - Servicio real.
  - Servicio de engaño.
  - Capa de análisis.

Arquitectura alineada con principios Zero Trust.

---

## 💰 Coste del proyecto

- **Infraestructura Cloud:** 0€
- **Licencias:** 0€
- **Software:** 100% Open Source

El proyecto se mantiene dentro del **Always Free Tier** de Oracle Cloud.

El coste real fue en:
- Ingeniería.
- Resolución de incompatibilidades ARM64.
- Hardening.
- Validación de flujo completo.

Se priorizó resolver técnicamente los problemas en ARM en lugar de migrar a instancias x86 de pago.

---

## 🚀 Cómo reproducir el laboratorio (Resumen)

1. Crear VCN privada en OCI (10.0.1.0/24).
2. Desplegar 3 instancias ARM64.
3. Configurar reglas:
   - 22 (Cowrie)
   - 80 (DVWA)
   - 51820 (WireGuard)
   - 1514 interno (Wazuh)
4. Instalar Docker en el sensor.
5. Desplegar Cowrie + DVWA.
6. Configurar Wazuh Manager.
7. Instalar Wazuh Agent en el sensor.
8. Configurar WireGuard.
9. Validar flujo Sensor → SIEM.

---

## 📊 Valor técnico del proyecto

Este laboratorio demuestra capacidad real en:

- Segmentación avanzada en cloud pública.
- Gestión multi-nodo.
- Adaptación ARM64.
- Hardening SSH profesional.
- Integración honeypot + SIEM.
- Infraestructura sostenible sin coste.

No es un laboratorio local:  
es una **infraestructura cloud funcional, monitorizada y segmentada**.

## 🔁 Flujo completo validado
