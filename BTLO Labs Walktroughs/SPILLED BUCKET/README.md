# ☁️ Cloud Attack Investigation – Scenario & Q&A

## Scenario
Due to a sudden boom in Cloud services, a recently formed Australian-based company shifted from On-Prem to Cloud infrastructure. While moving to the Cloud, they unknowingly had a few misconfigurations, which a group of hackers leveraged to attack them. The attackers were successful in attacking and gaining access to their infrastructure. The company does not have an IR team ready to perform cloud-based investigations. They also said they manage their Cloud from Browser only, no CLI was configured.  

The investigation starts using Splunk server. We need to make sure what are our source types that are going to provide us with the data that we are going to analyze. Once we confirmed the sourcetype, we can start searching.  

---
# 🛡️ Executive Summary – Cloud Attack Investigation

## 📌 Context
Una empresa australiana recién migrada a infraestructura Cloud sufrió un ataque debido a **misconfiguraciones críticas** en sus servicios.  
Los atacantes aprovecharon accesos inseguros y lograron comprometer múltiples recursos en AWS.  
La investigación se realizó utilizando **Splunk** como plataforma de análisis, apoyándose en **CloudTrail** y **VPC Flow Logs**.

---

## 🔎 Key Findings

### Initial Access
- El atacante accedió al bucket **developers-configuration**.  
- IP maliciosa identificada: **18[.]216[.]138[.]52**.  

### Credential Exposure
- Objeto descargado: **VPN-Profiles/DevelopersProfile_wg0.conf**.  
- Software asociado: **WireGuard**.  
- El atacante filtró su propia IP al conectarse: **122[.]161[.]49[.]105**.  

### Compromised EC2 Instances
- Conexión inicial a instancia privada: **10.0.1.125**.  
- Enumeración de roles IAM → **arn:aws:iam::764581110688:role/ec2-role**.  
- Persistencia lograda mediante APIs: **CreateUser → CreateAccessKey → AttachUserPolicy**.  
- Identidad creada: **web_engg_2**.  
- Política asociada: *(Policy ARN identificado en logs)*.  
- Acceso adicional vía SSH a instancia privada: **10.0.2.32**.  
- Reverse shell detectado hacia atacante en puerto sospechoso.  

---

## ⚠️ Impact
- **Data Exfiltration**: Descarga de objetos sensibles desde S3.  
- **Persistence**: Creación de usuarios y llaves IAM con políticas adjuntas.  
- **Privilege Escalation**: Uso de roles IAM para ampliar acceso.  
- **Infrastructure Compromise**: Conexión a múltiples instancias EC2 y establecimiento de reverse shell.  

---

## 🛠️ Recommendations
- Implementar **Cloud Security Posture Management (CSPM)** para detectar misconfiguraciones.  
- Activar **MFA** y rotación periódica de llaves IAM.  
- Configurar **AWS Config + GuardDuty** para alertas en tiempo real.  
- Limitar accesos a buckets S3 mediante políticas de **least privilege**.  
- Establecer un **Incident Response Playbook** para entornos Cloud.  








# IOCs (Indicators of Compromise)

| Category        | Indicator                                      | Notes                                      |
|-----------------|-----------------------------------------------|--------------------------------------------|
| S3 Bucket       | developers-configuration                      | Bucket accedido por el atacante             |
| Attacker IP     | 18[.]216[.]138[.]52                           | IP asociada al acceso S3                    |
| Object/File     | VPN-Profiles/DevelopersProfile_wg0.conf       | Archivo descargado desde S3                 |
| Software        | WireGuard                                     | Software asociado al archivo de configuración|
| Attacker IP     | 122[.]161[.]49[.]105                          | IP filtrada al conectarse vía WireGuard     |
| EC2 Instance    | 10.0.1.125                                    | Instancia privada comprometida              |
| IAM Role ARN    | arn:aws:iam::764581110688:role/ec2-role       | Rol enumerado por el atacante               |
| IAM APIs        | CreateUser, CreateAccessKey, AttachUserPolicy | APIs usadas para persistencia               |
| IAM Identity    | web_engg_2                                    | Usuario IAM creado                          |
| Policy ARN      | (Identificado en logs)                        | Política adjunta al usuario                 |
| EC2 Instance    | 10.0.2.32                                     | Instancia accedida vía SSH                  |
| Reverse Shell   | (Attacker IP, Port)                           | Conexión reverse shell detectada            |


## Q&A

### Question #1  
**Which S3 bucket's object was accessed by the attacker?**  
**Answer:** developers-configuration  

---

### Question #2  
**What was the attacker IP associated in S3 access? [Defanged IP]**  
**Answer:** 18[.]216[.]138[.]52  

---

### Question #3  
**What object/file did the attacker then access/download that would allow access to their environment?**  
**Answer:** VPN-Profiles/DevelopersProfile_wg0.conf  

---

### Question #4  
**Based on the previous question, what is the software associated with the file?**  
**Answer:** wireguard  

---

### Question #5  
**Using the previously mentioned file, one of the attackers accidentally connected via main system leading to his IP address getting leaked. What is the IP address of the Attacker? [Defanged IP]**  
**Answer:** 122[.]161[.]49[.]105  

---

### Question #6  
**What was the Private IP of the EC2 instance to which the attacker connected?**  
**Answer:** 10.0.1.125  

---

### Question #7  
**The attacker performed further enumeration while being inside the EC2 instance and found a role that could be used further by assuming it. What was the ARN of the role?**  
**Answer:** arn:aws:iam::764581110688:role/ec2-role  

---

### Question #8  
**Using the role Attacker targeted IAM to achieve persistence in the environment. Provide the APIs used for it in order of its usage.**  
**Answer:** CreateUser, CreateAccessKey, AttachUserPolicy  

---

### Question #9  
**Provide the name of the IAM Identity created during Persistence.**  
**Answer:** web_engg_2  

---

### Question #10  
**What policy was attached to the Identity later? Provide the policy ARN.**  
**Answer:** (Identificado en logs)  

---

### Question #11  
**Another EC2 instance was found to be accessed by the Attacker using SSH. Find its Private IP Address.**  
**Answer:** 10.0.2.32  

---

### Question #12  
**From the above EC2 instance, attacker then created a reverse shell. Find the Reverse Shell IP & port. [Defanged IP]**  
**Answer:** (Attacker IP, Port)  

---
