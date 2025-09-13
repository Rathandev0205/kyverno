# kyverno
- Kyverno is a policy-as-code automation engine in kubernetes, where the policies are defined, managed and enforced in YAML format to keep the clusters accoring to the company compliance, governance,monitoring and security etc.
- Kyverno operates as webhook admission controller in kubernetes, it evaluates API requests against defined policies and ensures that only compliant configurations are applied.
  
  **Admission controller in kubernetes**
  <img width="800" height="332" alt="image" src="https://github.com/user-attachments/assets/3f5fc838-ed6f-4782-ab66-9de99974b5c2" />
  
  **This is where kyverno operates inside the cluster**
  <img width="800" height="241" alt="image" src="https://github.com/user-attachments/assets/02a9bcc1-e78d-4375-bfaf-6356ac14abea" />

# Custom Resource Definitions (CRDs) of kyverno
- Kyverno uses 2 CRDs of kubernetes to define the actual rules and conditions for validating, mutating, generating, or cleaning up Kubernetes resources.
  1. **ClusterPolicy**: Used to define and maintain policies at **namespace** level.
  2. **Policy**: Used to define and maintain policies at **cluster** level.

 # Kyverno Policies and Rules 
 - Policies in Kyverno are defined as Kubernetes resources, which makes them highly integrable with existing Kubernetes workflows. These policies can perform a variety of functions:
    1.**Validation**: Ensures specific rules are followed by rejecting or reporting configurations that violate policies.
    2. **Mutation**: Automatically adjusts resources to meet specific requirements before they are admitted to the Kubernetes cluster.
    3. **Generation**: Creates additional resources based on existing ones according to predefined rules.
- Kyverno policies are applied to Kubernetes resources (e.g., Pods, Services, etc.) and are executed by the Kyverno controller when resources are created, updated, or deleted.
- kyverno acts according to the mentioned validationFailureAction in policy.
- There are two types of validationFailureAction
  
   **1. Enforce:** If a resource violates the policy, it'll be rejected. The API server will not allow the resource to be created or updated.
  
   **2. Audit:** If a resource violates the policy it is still created/updated, but Kyverno records or warns about the violation.
- A policy in Kyverno is a set of rule(s), where each rule consists of a match declaration and anyone of these (validate, mutate, generate)

# Sample kyverno Policies

## Validation policy, which restricts pod to run as root user

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy     # Policy
metadata:
  name: restrict-root
spec:
  validationFailureAction: Enforce      # Audit
  rules:
  - name: restrict-root
    match:
      any:
      - resources:
          kinds:
          - Pod     # Deployment
    validate:       # mutate
      pattern:
        spec:
          containers:
          - securityContext:
              runAsNonRoot: true      #or runAsUser: "!0"
```
## Mutation policy to enforce default resource limits

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: enforce-default-resource-limits
spec:
  validationFailureAction: Enforce
  rules:
    - name: enforce-default-resource-limits
      match:
        resources:
          kinds:
            - Pod
      mutate:
        patchStrategicMerge:
          spec:
            containers:
              - (name): "*"
                resources:
                  limits:
                    memory: "256Mi"
                    cpu: "500m"
                  requests:
                    memory: "128Mi"
                    cpu: "250m"
```
# Mutation Policy
- Mutating policies in Kubernetes are rules that automatically modify resource definitions before they are stored in etcd.
- By automatically applying necessary changes, they speed up deployment processes and reduce the need for manual intervention.
- Mutating policies can enforce security settings or compliance requirements automatically.
- Some of the basic sample mutation policies are:
1. Default Resource Limits
2. Automatically Add Labels
3. Enforce Annotations
4. Set Image Pull Policy as Always for the latest image tags etc.

In kyverno mutation policy, **patchStrategicMerge** is a key used to merge the changes specified in the policy.

# Kyverno CLI
- Kyverno CLI is used to test policies locally against Kubernetes resources without applying them to a live cluster. (similar to localstack for terraform).
- It supports both YAML and JSON for input manifests and policies.
- Kyverno CLI can be integrated into Continuous Integration/Continuous Deployment (CI/CD) pipelines, automating policy compliance checks as part of the deployment process.

## Testing Policies with Kyverno CLI
Here's how to validate, mutate, and generate configurations using Kyverno CLI.
### Validate Policies
To validate a resource against a policy, use the below command:

`kyverno apply /path/to/policy.yaml --resource /path/to/resource.yaml`

This command will output (refer below image) whether the resource passes the policy checks.

<img width="436" height="72" alt="image" src="https://github.com/user-attachments/assets/587d1424-7469-4107-8306-990c7af46cf7" />

### Mutate Policies
The below command will output the mutated resource, allowing you to review the changes.

`kyverno apply /path/to/policy.yaml --resource /path/to/resource.yaml --output-mutated`

<img width="561" height="418" alt="image" src="https://github.com/user-attachments/assets/112ff183-a05a-44d2-ad30-980cb4d46a58" />






