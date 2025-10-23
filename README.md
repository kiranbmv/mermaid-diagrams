# mermaid-diagrams

## Certificate Lifecycle Management with HashiCorp Vault

This diagram illustrates the certificate lifecycle management process using HashiCorp Vault as a subordinate CA under AT&T's internal OAM Root CA, integrated with OpenShift via the Vault Secrets Operator.

### Mermaid Diagram

```mermaid
sequenceDiagram
  participant ATT_ROOT as AT&T Internal OAM Root CA (Owned by AT&T)
  participant ATT_INT as AT&T Intermediate CA (Signed by Root)
  participant VAULT as HashiCorp Vault (External to OCP) PKI Engine
  participant VSO as Vault Secrets Operator (Running in OCP 4.16)
  participant WORKLOAD as OpenShift Workload (Quay/Ingress)

  Note over ATT_ROOT, ATT_INT: Initial CA Setup
  ATT_ROOT ->> ATT_INT: Signs Intermediate CA Certificate
  ATT_INT ->> VAULT: Import Intermediate CA Cert & Key (Vault becomes Sub-CA)
  Note over VAULT, WORKLOAD: Certificate Lifecycle Management (LCM)
  rect rgb(220, 240, 255)
    Note over WORKLOAD, VSO: 1. Certificate Request (CSR Generation)
    WORKLOAD ->> VSO: Application needs TLS certificate
    VSO ->> VAULT: Generate CSR & Request Certificate
  end
  rect rgb(255, 245, 220)
    Note over VAULT: 2. Certificate Issuance
    VAULT ->> VAULT: Validate Request & Policy
    VAULT ->> VAULT: Sign CSR using Intermediate CA
    VAULT ->> VSO: Return Signed Certificate Chain
  end
  rect rgb(220, 255, 220)
    Note over VSO, WORKLOAD: 3. Certificate Distribution
    VSO ->> VSO: Create/Update Kubernetes Secret
    VSO ->> WORKLOAD: Inject Certificate as Secret
    
  end
  rect rgb(255, 220, 220)
    Note over VAULT, WORKLOAD: 4. Certificate Rotation & Renewal
    VAULT ->> VAULT: Certificate nearing expiry
    VAULT ->> VSO: Push updated certificate
    VSO ->> VSO: Update Kubernetes Secret
  end
```

### How to View Rendered Mermaid Diagrams

GitHub automatically renders Mermaid diagrams in Markdown files. To view the rendered diagram:

1. **In the GitHub UI**: Simply view this README.md file in GitHub's web interface. The diagram will be automatically rendered.
2. **Click on the diagram**: You can click on the rendered diagram to see it in a larger view.
3. **Edit mode preview**: When editing, click the "Preview" tab above the editor to see the rendered diagram.

Mermaid syntax is fully supported in GitHub-flavored Markdown, making it easy to create and maintain complex diagrams directly in your documentation.
