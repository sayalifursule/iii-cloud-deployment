# iii Cloud Deployment Assignment

<div align="center">

# Multi-VM Distributed Worker Deployment using iii Framework on AWS

### DevOps Internship Assignment Submission

</div>

---

# Project Overview

This project demonstrates deployment of the iii quickstart distributed worker system across AWS cloud infrastructure using multiple virtual machines inside a secure private network.

The architecture separates workers across isolated VMs and enables internal RPC communication while exposing only the API layer to the public internet.

The implementation focuses on:

- Distributed worker deployment
- Private subnet communication
- Secure cloud networking
- Infrastructure reproducibility
- Cloud-native DevOps practices
- Internal RPC communication between workers

---

# Architecture Diagram

```text
                           Internet
                               |
                        Public API VM
                     (Public Subnet EC2)
                               |
                    ---------------------
                    |                   |
                    |   AWS VPC         |
                    |   10.0.0.0/16     |
                    |                   |
                    |  Private Subnet   |
                    |                   |
                    |  Math Worker VM   |
                    |  Caller Worker VM |
                    |                   |
                    ---------------------
```

---

# Tech Stack

| Category | Technology |
|---|---|
| Cloud Provider | AWS |
| Compute | EC2 |
| Networking | VPC, Subnets, Security Groups |
| Languages | Python, TypeScript |
| Framework | iii SDK |
| OS | Ubuntu 26.04 |
| Version Control | Git & GitHub |

---

# AWS Infrastructure Setup

## VPC Configuration

- Custom AWS VPC created
- CIDR block: `10.0.0.0/16`

## Subnets

### Public Subnet
Used for:
- API VM
- SSH access
- Internet communication

### Private Subnet
Used for:
- Worker VMs
- Internal RPC communication only
- No direct public internet exposure

---

# EC2 Instance Architecture

| VM | Purpose | Visibility |
|---|---|---|
| API VM | Public API Entry Point | Public |
| Caller Worker VM | RPC Dispatcher | Private |
| Math Worker VM | Mathematical Operations | Private |

---

# Security Design

## Security Groups

Implemented inbound/outbound firewall rules using AWS Security Groups.

### Public API VM
Allowed:
- SSH (Port 22)
- API Traffic

### Worker VMs
Allowed:
- Internal VPC communication only
- SSH from internal subnet

Workers are NOT directly accessible from the public internet.

---

# Worker Architecture

## caller-worker (TypeScript)

Responsibilities:
- Accepts requests
- Dispatches RPC calls
- Coordinates worker communication

Function:
```ts
math::add_two_numbers
```

---

## math-worker (Python)

Responsibilities:
- Handles mathematical processing
- Returns calculation results

Function:
```python
math::add
```

Example Response:
```json
{
  "c": 10
}
```

---

# JSON API Example

## Sample Request

```bash
curl -X POST http://<PUBLIC_API_IP>:3000/add \
-H "Content-Type: application/json" \
-d '{
  "a": 4,
  "b": 6
}'
```

## Sample Response

```json
{
  "c": 10
}
```

---

# RPC Flow

```text
HTTP Request
   ↓
Public API VM
   ↓
caller-worker (TypeScript)
   ↓ RPC
math-worker (Python)
   ↓
JSON Response Returned
```

---

# Repository Structure

```text
.
├── workers/
│   ├── caller-worker/
│   │   ├── src/
│   │   ├── package.json
│   │   └── iii.worker.yaml
│   │
│   └── math-worker/
│       ├── math_worker.py
│       ├── requirements.txt
│       └── iii.worker.yaml
│
├── config.yaml
├── iii.worker.yaml
├── README.md
└── .gitignore
```

---

# Deployment Steps

## Clone Repository

```bash
git clone https://github.com/sayalifursule/iii-cloud-deployment.git

cd iii-cloud-deployment
```

---

## Create Python Virtual Environment

```bash
python3 -m venv venv

source venv/bin/activate
```

---

## Install Dependencies

```bash
pip install iii-sdk

pip install -r workers/math-worker/requirements.txt
```

---

## Run Math Worker

```bash
python workers/math-worker/math_worker.py
```

---

# RPC Communication

The workers communicate through iii’s distributed RPC architecture over the private AWS subnet using websocket-based inter-worker messaging.

The caller-worker invokes the Python math-worker through internal RPC calls without exposing worker services to the public internet.

---

# Infrastructure Reproducibility

Infrastructure configuration and deployment steps are fully documented to allow recreation in a fresh AWS account.

Future improvements include full Terraform automation for reproducible cloud provisioning.

The project can be extended using:
- Terraform
- Pulumi
- AWS CLI automation
- Ansible

---

# Challenges Faced

During deployment, the iii runtime required KVM-backed sandbox support.

AWS free-tier EC2 instances do not expose:

```text
/dev/kvm
```

This caused the managed iii sandbox runtime to fail.

Error encountered:

```text
KVM not available -- /dev/kvm does not exist
```

---

# Troubleshooting & Workarounds

The following approaches were attempted successfully:

- Manual Python worker execution
- Virtual environment setup
- Direct iii SDK installation
- Internal VM communication testing
- SSH tunneling between VMs
- Private subnet networking verification

Although managed KVM-based execution was unavailable on free-tier infrastructure, the distributed architecture and deployment workflow were implemented successfully.

---

# Production Improvements

If deploying this system in production:

- Use KVM-enabled compute infrastructure
- Containerize workers using Docker
- Use Kubernetes orchestration
- Add centralized monitoring/logging
- Configure CI/CD pipelines
- Add authentication and rate limiting
- Use Secrets Manager
- Enable autoscaling

---

# Scaling Strategy for 100x Larger Models

For significantly larger AI models:

- GPU-enabled infrastructure would be required
- Model sharding/distributed inference
- Queue-based asynchronous processing
- Kubernetes-based orchestration
- Dedicated inference clusters
- Horizontal worker scaling
- Artifact storage systems

---

# Screenshots

## AWS VPC Configuration
<img width="1920" height="1020" alt="Screenshot 2026-05-23 131047" src="https://github.com/user-attachments/assets/2fe6cb49-d8e3-47ee-9feb-639ab42a3f46" />

---

## EC2 Instances
<img width="1920" height="1020" alt="Screenshot 2026-05-23 131142" src="https://github.com/user-attachments/assets/f0ae0ac0-0fe7-42a9-9fa9-574c99f8e8cc" />

---

## Private Subnet Configuration
<img width="1920" height="1020" alt="Screenshot 2026-05-23 131257" src="https://github.com/user-attachments/assets/a3488ca6-fc6c-4ea7-b4b5-247407275ffb" />

---

## Security Groups
<img width="1920" height="1020" alt="Screenshot 2026-05-23 131220" src="https://github.com/user-attachments/assets/c572fd4b-f398-402a-89d5-3e885ac268ff" />

---

## SSH Into Private VM
<img width="1920" height="1020" alt="Screenshot 2026-05-23 131841" src="https://github.com/user-attachments/assets/5cb67e4a-1f59-441d-8c95-587b15321747" />

---

## iii Worker Status
<img width="1600" height="850" alt="WhatsApp Image 2026-05-20 at 11 43 24 PM" src="https://github.com/user-attachments/assets/95eae78a-280f-46b2-a780-3c095d7d9328" />

---

## PowerShell / Terminal Output
<img width="1600" height="850" alt="WhatsApp Image 2026-05-20 at 11 43 55 PM" src="https://github.com/user-attachments/assets/c99fdaf1-5698-4c8f-8808-4654c5692f99" />

---

# Repository URL

https://github.com/sayalifursule/iii-cloud-deployment

---

# Author

## Sayali Fursule

BE Information Technology Graduate  
DevOps & Cloud Enthusiast
