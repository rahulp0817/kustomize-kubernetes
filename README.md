# Kustomize: Mastery 🚀

Welcome to Kustomize learning! This guide will help you learn Kustomize step by step, using simple examples and clear explanations.

## What is Kustomize?
Kustomize is a template-free, Kubernetes-native configuration management tool that allows you to customize raw YAML files for multiple environments without altering the original files
Kustomize helps you change Kubernetes files without editing them directly. Think of it like cooking:
- You have a basic recipe (we call this the base)
- You want to make small changes (we call these overlays)
- You don't need to change the original recipe!

## Topics
1. [Tranformers](./transformers/README.md) - Learn what Kustomize is and how to use it
2. [Patches](./patches/README.md) - Make specific changes to your files
<!-- 3. [ConfigMaps and Secrets](./03-configmaps-secrets/README.md) - Work with configuration files -->
<!-- 4. [Generators](./04-generators/README.md) - Create configs automatically -->
<!-- 5. [Real World Examples](./05-real-world/README.md) - See how to use Kustomize in real projects -->

## Requirements
- kubectl installed on your computer
- Basic understanding of Kubernetes (pods, deployments)
- A text editor 

## Project structure
```
.
├── README.md
├── patches
│   ├── README.md
│   ├── base
│   │   ├── deploy.yml
│   │   └── kustomization.yml
│   └── overlays
│       ├── dev
│       │   ├── kustomization.yml
│       │   └── replicas.yml
│       └── stage
│           ├── kustomization.yml
│           └── replicas.yml
└── transforms
    ├── README.md
    ├── base
    │   ├── deploy.yml
    │   └── kustomization.yml
    └── overlays
        ├── dev
        │   └── kustomization.yml
        └── stage
            └── kustomization.yml
```
## 📚 References

- [Kustomize Official Docs](https://kustomize.io/)
- [kubectl kustomize reference](https://kubectl.docs.kubernetes.io/references/kustomize/)


Need help? Check the README in each folder for detailed explanations and examples. 
Happy Kustomizing! 🎉