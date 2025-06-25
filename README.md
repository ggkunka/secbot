# secbot
```
+-----------------+       +----------------------+       +---------------------+
| Public Registry |  -->  | Flux Image Automation |  -->  | Harbor (Private Reg)|
| (DockerHub, GH) |       | (Reflector + Pusher) |       |                     |
+-----------------+       +----------------------+       +---------------------+
                                                           |
                                                           v
                                +------------------------------+
                                | Argo CD                      |
                                | - Monitors infra-scans repo  |
                                | - Deploys Helm charts        |
                                +------------------------------+
                                                           |
                                                           v
                          +----------------------------------------+
                          | Kubernetes Cluster                     |
                          | - Pods with images from Harbor         |
                          | - Secrets for image pull               |
                          +----------------------------------------+

infra-scans/
├── README.md
├── .helmignore
├── Chart.yaml                     # (optional) Umbrella chart
├── values.yaml                    # (optional) Umbrella chart values
├── charts/                        # All sub-charts (your tools)
│   ├── kube-bench/
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   │       └── cronjob.yaml
│   ├── falco/
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   │       └── daemonset.yaml
│   ├── anchore-engine/
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   │       └── deployment.yaml
│   ├── nessus/
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   │       └── cronjob.yaml
│   ├── mde-linux/
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   │       └── daemonset.yaml
│   ├── invicti/
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   │       └── cronjob.yaml
│   └── pentest-scripts/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│           ├── nmap-cronjob.yaml
│           └── t50-cronjob.yaml
└── argocd-apps/                   # Argo CD Application & Workflow manifests
    ├── argo-workflows.yaml       # deploys the Argo Workflows controller via Helm
    ├── security-scans-cronwf.yaml# your monthly CronWorkflow
    ├── kube-bench-app.yaml       # optional: individual Argo App per chart
    ├── falco-app.yaml
    ├── anchore-app.yaml
    ├── nessus-app.yaml
    ├── mde-linux-app.yaml
    ├── invicti-app.yaml
    └── pentest-app.yaml
```
