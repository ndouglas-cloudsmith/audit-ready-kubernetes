# Audit-ready Kubernetes
*How to leverage policy-as-code for continuous compliance*
<br/><br/>
This was created for **Workshop #3** of PlatformCon: <br/>
[Link to the PlatformCon session](https://platformcon.com/sessions/audit-ready-kubernetes-how-to-leverage-policy-as-code-for-continuous-compliance)

<img width="50%" height="551" alt="Screenshot 2026-06-06 at 22 14 59" src="https://github.com/user-attachments/assets/53632a73-9549-4505-85aa-808107e39b46" />

### Learning Outcomes
- ```OPA Gatekeeper``` as as admission control technology in Kubernetes
- ```OPA``` (**Edge**) is needed when we are enforcing controls outside of Kubernetes
- ```Kyverno``` is proving to be a strong admission control alternative in Kubernetes

## Part 1: OPA Gatekeeper

Let's start off by installing **[OPA Gatekeeper](https://kubernetes.io/blog/2019/08/06/opa-gatekeeper-policy-and-governance-for-kubernetes/)** - specifically for Kubernetes, using a **[Prebuilt Image](https://open-policy-agent.github.io/gatekeeper/website/docs/install#deploying-a-release-using-prebuilt-image)**
```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/v3.22.2/deploy/gatekeeper.yaml
```

## Part 2: OPA on the Edge

Let's start off by installing **[OPA](https://github.com/ndouglas-cloudsmith/malicious-package-policy)** on a standalone endpoint. Since OPA is written in ```Go```, it's just a single executable file.

On my local ```macOS``` workstation, I will be using ```Homebrew``` to install OPA:
```
brew install opa
```

However, for ```Linux``` endpoints, we need to download the binary, make it executable, and move it to your path:
```
curl -L -o opa https://openpolicyagent.org/downloads/latest/opa_linux_amd64_static
chmod 755 opa
sudo mv opa /usr/local/bin/
```


## PlatformCon 2026 Workshops

1. [Hunting compromised software dependencies inside Kubernetes workloads](https://github.com/ndouglas-cloudsmith/compromised-dependencies-kubernetes/tree/main)
2. [AI agents & platform engineering: Efficiency boost or new source of trouble?](https://github.com/ndouglas-cloudsmith/AI-agents-platform-engineering)
3. **[Audit-ready Kubernetes: How to leverage policy-as-code for continuous compliance](https://github.com/ndouglas-cloudsmith/audit-ready-kubernetes/tree/main)**
4. [The ghost in the machine: Securing AI agent skills](https://github.com/ndouglas-cloudsmith/ghost-in-the-machine/tree/main)
