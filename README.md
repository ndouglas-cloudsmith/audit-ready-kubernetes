# Audit-ready Kubernetes
*How to leverage policy-as-code for continuous compliance*
<br/><br/>
This was created for **Workshop #3** of PlatformCon: <br/>
[Link to the PlatformCon session](https://platformcon.com/sessions/audit-ready-kubernetes-how-to-leverage-policy-as-code-for-continuous-compliance)

<img width="50%" height="551" alt="audit-ready" src="https://github.com/user-attachments/assets/9402c784-6e5e-4229-bf40-df094b40b431" />


### Learning Outcomes
- ```OPA``` (**Edge**) is needed when we are enforcing controls outside Kubernetes
- ```OPA Gatekeeper``` offers a dedicated admission control technology in Kubernetes
- ```Kyverno``` provides a strong admission control alternative for Kubernetes clusters

OPA policies are expressed in a high-level declarative language called Rego. Rego (pronounced "ray-go") is purpose-built for expressing policies over complex hierarchical data structures.


## Part 1: OPA on the Edge

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

- Firstly, we start by defining a ```violation``` in OPA ```Rego``` policy against software packages, specifically.
- Then we query to ```OSV.dev``` API (used for OSSF Malicious Packages) to see if a packages is known to be malicious in any of the support open-source upstreams.
- If there is one or more responses that contain a known (```is_malicious```) package, it provides the output message that a specific dependency in your Backstage JSON data is known to be malicious, according to ```OSV.dev```.

<img width="1884" height="644" alt="violation" src="https://github.com/user-attachments/assets/8370c4a4-d34a-4211-844c-37f14cf8563c" />

### Using Standalone OPA
Standalone OPA operates in **three main modes** depending on what you're trying to do.

#### 1. The Interactive Shell (REPL)
This is great for learning ```Rego``` or testing a quick snippet, ultimately fine before we automate the golden path:

```
opa run
```
Type ```help``` inside the shell to see what you can do.

#### 2. The Evaluation Mode
This is perfect for ```CI/CD``` pipelines, or any process similar to spinning-up self-service templates. <br/>
You pass it a policy file and a data file, and it spits out the result immediately.

```
opa eval -d policy.rego -i input.json "data.example.allow"
```

Since our policy likely expects package data, I created a file named ```input.json``` and put the package info inside it: <br/>
This file could be the template for our Golden Path initiative inside our self-service, Internal Development Platform.
```
cat <<EOF > input.json
{
    "packages": [
        {
            "name": "jinja2",
            "version": "2.4.1",
            "ecosystem": "PyPI"
        }
    ]
}
EOF
```

Simple ```policy.rego``` example:
```
cat <<EOF > policy.rego
package example

default allow = false

allow if {
    input.packages[_].name != "malicious-pkg"
}
EOF
```

#### Test Violation
```
rm policy.rego
rm input.json
wget https://raw.githubusercontent.com/ndouglas-cloudsmith/malicious-package-policy/refs/heads/main/test-violation/policy.rego
wget https://raw.githubusercontent.com/ndouglas-cloudsmith/malicious-package-policy/refs/heads/main/test-violation/input.json
```

When you run ```opa eval```, you now want to target ```data.example.violation```.
```
opa eval -d policy.rego -i input.json "data.example.violation" --format values
```

#### Understanding the OPA CLI Workflow
When you run ```opa eval```, the engine performs a "three-way merge" to get your answer:

- ```-d policy.rego``` loads your logic (the rules).
- ```-i input.json``` loads the specific object you want to test (called the **Subject**).
- ```"data.example.allow"``` tells OPA exactly which rule inside the policy you want to see the result of.

<br/><br/>

### Querying the OSV API to check if template is safe

As always, clear out the old manifests and download the correct ```rego``` and ```input``` data for the policy control
```
rm policy.rego
rm input.json
wget https://raw.githubusercontent.com/ndouglas-cloudsmith/malicious-package-policy/refs/heads/main/osv-api/policy.rego
wget https://raw.githubusercontent.com/ndouglas-cloudsmith/malicious-package-policy/refs/heads/main/osv-api/input.json
```

Test the policy:
```
opa eval -d policy.rego -i input.json "data.example.violation"
```


## Part 2: OPA Gatekeeper

Let's start off by installing **[OPA Gatekeeper](https://kubernetes.io/blog/2019/08/06/opa-gatekeeper-policy-and-governance-for-kubernetes/)** - specifically for Kubernetes, using a **[Prebuilt Image](https://open-policy-agent.github.io/gatekeeper/website/docs/install#deploying-a-release-using-prebuilt-image)**
```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/v3.22.2/deploy/gatekeeper.yaml
```

## Part 3: Kyverno




## PlatformCon 2026 Workshops

1. [Hunting compromised software dependencies inside Kubernetes workloads](https://github.com/ndouglas-cloudsmith/compromised-dependencies-kubernetes/tree/main)
2. [AI agents & platform engineering: Efficiency boost or new source of trouble?](https://github.com/ndouglas-cloudsmith/AI-agents-platform-engineering)
3. **[Audit-ready Kubernetes: How to leverage policy-as-code for continuous compliance](https://github.com/ndouglas-cloudsmith/audit-ready-kubernetes/tree/main)**
4. [The ghost in the machine: Securing AI agent skills](https://github.com/ndouglas-cloudsmith/ghost-in-the-machine/tree/main)
