# 🚀 Enterprise DevSecOps & Cloud Pipeline

![DevSecOps](https://img.shields.io/badge/DevSecOps-Automated-success?style=for-the-badge&logo=githubactions)
![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![Terraform](https://img.shields.io/badge/terraform-%235835CC.svg?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)

Ce projet est une démonstration complète d'une infrastructure "Zero-Touch" et "Production Ready", intégrant les meilleures pratiques d'industrialisation logicielle, de sécurité (DevSecOps) et de déploiement Cloud.

## 🎯 Compétences Démontrées

- **Qualité de code :** Tests unitaires automatisés via `pytest`.
- **Conteneurisation :** Application Python (FastAPI) packagée via `Docker`.
- **DevSecOps :** Scan de vulnérabilités automatisé (CVE) via `Trivy` directement dans le pipeline CI/CD.
- **Infrastructure as Code (IaC) :** Provisionnement complet du cloud AWS via `Terraform` (S3 Backend, VPC, NAT Gateway, EKS Cluster).
- **Orchestration :** Configuration de déploiement Kubernetes via des manifestes YAML (`Deployment`, `Service LoadBalancer`).

---

## 🏗️ Architecture Globale

### 1. Le Pipeline CI/CD (GitHub Actions)

À chaque `git push`, le pipeline exécute une série de vérifications strictes. Si une faille critique est détectée par Trivy, ou si un test échoue, le déploiement est immédiatement bloqué.

```mermaid
graph LR
    A[Développeur] -->|Git Push| B(GitHub Actions)
    B --> C{Tests Pytest}
    C -->|Succès| D[Build Docker]
    C -->|Échec| Z[Alerte / Arrêt]
    D --> E{Trivy Security Scan}
    E -->|Critique / High| Z
    E -->|Safe| F[Image Prête]
    F --> G((Déploiement K8s))
    
    style B fill:#2088FF,stroke:#333,stroke-width:2px,color:#fff
    style C fill:#238636,stroke:#333,stroke-width:2px,color:#fff
    style D fill:#2496ED,stroke:#333,stroke-width:2px,color:#fff
    style E fill:#FF9900,stroke:#333,stroke-width:2px,color:#fff
    style Z fill:#DA3633,stroke:#333,stroke-width:2px,color:#fff
```

### 2. L'Infrastructure Cloud AWS (Terraform)

Le code IaC provisionne un réseau sécurisé et un cluster Elastic Kubernetes Service (EKS).

```mermaid
flowchart TD
    User((Utilisateurs)) --> IGW[Internet Gateway]
    
    subgraph AWS_VPC [VPC - Réseau Privé géré par Terraform]
        
        subgraph Public_Subnets [Sous-réseaux Publics]
            ALB[Load Balancer]
            NAT[NAT Gateway]
        end
        
        subgraph Private_Subnets [Sous-réseaux Privés]
            EKS[Cluster Kubernetes EKS]
            Nodes[Node Group t3.medium]
        end
        
    end
    
    IGW --> ALB
    ALB --> Nodes
    EKS --- Nodes
    
    style AWS_VPC fill:#e6f3ff,stroke:#2496ED,stroke-width:2px
    style Public_Subnets fill:#d4ecd4,stroke:#238636,stroke-width:1px
    style Private_Subnets fill:#fce4e4,stroke:#DA3633,stroke-width:1px
```

---

## 📂 Structure du Répertoire

```text
.
├── app/                        # Code source de l'application (FastAPI)
│   ├── main.py                 # Point d'entrée de l'API
│   ├── test_main.py            # Tests unitaires Pytest
│   └── requirements.txt        # Dépendances Python
├── k8s/                        # Manifestes Kubernetes
│   ├── deployment.yaml         # Configuration des pods (replicas)
│   └── service.yaml            # Exposition LoadBalancer
├── terraform/                  # Scripts Infrastructure as Code
│   ├── main.tf                 # Définition VPC, EKS, KMS, et Backend S3
│   └── variables.tf            # Variables cloud
├── .github/workflows/          
│   └── deploy.yml              # Pipeline CI/CD DevSecOps
└── Dockerfile                  # Instructions de build du conteneur
```

---

## 🚀 Lancer le projet localement

Vous n'avez pas besoin d'AWS pour tester l'application, tout peut tourner localement avec Docker !

**1. Construire l'image Docker :**
```bash
docker build -t devsecops-demo .
```

**2. Simuler le DevSecOps (Scanner l'image avec Trivy) :**
```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --severity CRITICAL,HIGH devsecops-demo:latest
```

**3. Lancer l'application :**
```bash
docker run -p 8080:8000 devsecops-demo
```
Accédez ensuite à l'API via `http://localhost:8080/`.

---

## ☁️ Déploiement Cloud (Terraform)

Si vous disposez d'un compte AWS et des identifiants (Access Keys) configurés, vous pouvez déployer (ou simuler le déploiement) de l'architecture.

*(Attention, le cluster EKS génère des coûts sur AWS. Utilisez `terraform destroy` après vos tests !)*

```bash
cd terraform
# Initialisation des modules EKS/VPC
terraform init

# Simulation des 51+ ressources à créer
terraform plan

# (Optionnel) Création réelle de l'infrastructure
terraform apply
```
