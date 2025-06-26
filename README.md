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
infra-flux-installer/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── flux-crds.yaml            # Optional CRD manifest (airgap-safe)
│   ├── flux-install.yaml         # Deploys Flux controllers from OCI or Harbor
│   ├── image-repository.yaml     # Flux ImageRepository per tool
│   ├── image-policy.yaml         # Flux ImagePolicy per tool
│   ├── image-update.yaml         # Flux ImageUpdateAutomation
│   ├── git-repository.yaml       # Git source for HelmReleases
│   ├── helmrelease-scan.yaml     # HelmRelease templates for scanner tools
│   ├── proxy-secret.yaml         # Proxy ENV injection (optional)
│   └── harbor-pullsecret.yaml    # Harbor credentials

./gitops-repo/
├── .flux/                                # Flux source definitions
│   ├── imagerepositories/                # Track upstream scanner images
│   │   ├── kube-bench.yaml               # e.g. docker.io/aquasec/kube-bench:latest
│   │   ├── falco.yaml                    # e.g. quay.io/falcosecurity/falco:latest
│   │   └── ...                           # one file per tool
│   ├── imagereflectors/                  # Mirror images into Harbor
│   │   ├── kube-bench-mirror.yaml        # mirrors into harbor/tenant/kube-bench:latest
│   │   ├── falco-mirror.yaml             # manifests ImageReflector per tool
│   │   └── ...                           # one file per tool
│   ├── imagepolicies/                    # Define which tags to select
│   │   ├── kube-bench-policy.yaml        # choose semver latest or simply latest
│   │   ├── falco-policy.yaml             # policies for each tool
│   │   └── ...                           # one file per tool
│   ├── imageupdateautomation.yaml        # Flux CRD to patch root values.yaml
│   └── helmreleases/                     # Deploy scan-bootstrap umbrella chart
│       └── scan-bootstrap-hr.yaml        # HelmRelease for scan-bootstrap
├── charts/                               # Optional: embed scan-bootstrap chart here
│   └── scan-bootstrap/
│       ├── Chart.yaml                    # Umbrella chart for CronWorkflow + Argo Workflows
│       ├── values.yaml                   # Receives patched mirrorImage values
│       └── templates/                    # CRDs, mirroring CRDs, CronWorkflow template
└── values.yaml                           # Root values.yaml: mirrorImage placeholders for Flux to patch

```
