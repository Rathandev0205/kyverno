# kyverno
- Kyverno is a policy-as-code automation engine in kubernetes, where the policies are defined, managed and enforced in YAML format to keep the clusters accoring to the company compliance, governance,monitoring and security etc.
- Kyverno operates as webhook admission controller in kubernetes, it evaluates API requests against defined policies and ensures that only compliant configurations are applied.
  
  **Admission controller in kubernetes**
  <img width="800" height="332" alt="image" src="https://github.com/user-attachments/assets/3f5fc838-ed6f-4782-ab66-9de99974b5c2" />
  
  **This is where kyverno operates inside the cluster**
  <img width="800" height="241" alt="image" src="https://github.com/user-attachments/assets/02a9bcc1-e78d-4375-bfaf-6356ac14abea" />

# Custom Resource Definitions (CRDs) of kyverno
- Kyverno uses 2 CRDs of kubernetes to define the actual rules and conditions for validating, mutating, generating, or cleaning up Kubernetes resources.
  1. ClusterPolicy: Used to define and maintain policies at **namespace** level.
  2. Policy: Used to define and maintain policies at **cluster** level.
