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

gitops-repo/
└── charts/
    └── scan-bootstrap/
        ├── Chart.yaml
        ├── values.yaml       # proxy, registry, namespace, tools, schedule, etc.
        └── templates/
            ├── flux-imagerepositories.yaml
            ├── flux-imagereflectors.yaml
            ├── flux-imagepolicies.yaml
            ├── flux-imageupdateautomation.yaml
            ├── flux-helmrelease.yaml
            └── cronworkflow-scans.yaml

```
