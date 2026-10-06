# Structure

```
├── services/            # приложения (см. services/CONTEXT)
├── shared/              # библиотеки @shared/* (git-submodules; см. shared/CONTEXT)
├── proto/               # gRPC/protobuf-контракты (git-submodule)
├── context/             # репозитория контекста (git-submodule; сама CONTEXT-дерева не держит)
├── k8s/                 # манифесты Kubernetes: base/{infrastructure,services} + overlays/{production,local}
├── make/                # фрагменты Makefile: parameters, context, git, kafka
├── .github/workflows/   # build-and-push, deploy-gke (активен), deploy-docker-compose,
│                        # deploy-ssh-k8s (только manual), cleanup-registry, prune
├── Makefile             # install, up, reset, migrate, proto-generate, test, git-*, context-*
├── docker-compose.yml   # dev-топология (рабочая копия; не в git)
├── docker-compose.yml.dist / docker-compose.yml.deploy.dist   # шаблоны dev и deploy-compose
├── pnpm-workspace.yaml, turbo.json, tsconfig.base.json
├── .env.dist            # теги GHCR-образов для deploy-compose
└── README.md            # план развёртывания (K3s/VPS) — план, не факт
```
