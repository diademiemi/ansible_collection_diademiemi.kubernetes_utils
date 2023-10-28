Ansible Role Template
=========

This is an Ansible role to install and configure selfsigned_ssl.

Include more information about selfsigned_ssl in this section.

Requirements
------------
Run this against a Kubernetes cluster. This will install cert-manager and create self-signed SSL certificates.
<!--
- List hardware requirements here  
-->

Role Variables
--------------
Variable | Default | Description
---|---|---
`kubernetes_utils_kubeconfig_path` | `""` | Path to the kubeconfig file, omitted by default.
`kubenetes_utils_selfsigned_ssl_kubeconfig_path` | `"{{ kubeconfig_path }}"` | Path to the kubeconfig for SSL, defaults to the value of `kubernetes_utils_kubeconfig_path`.
`kubernetes_utils_selfsigned_ssl_helm_add_repo` | `true` | Whether to add the Helm repository for self-signed SSL.
`kubernetes_utils_selfsigned_ssl_helm_url` | `"https://charts.jetstack.io"` | URL of the Helm repository for self-signed SSL.
`kubernetes_utils_selfsigned_ssl_helm_repo_name` | `jetstack` | Name of the Helm repository for self-signed SSL.
`kubernetes_utils_selfsigned_ssl_helm_install` | `true` | Whether to install the Helm chart for self-signed SSL.
`kubernetes_utils_selfsigned_ssl_helm_chart_ref` | `"{{ kubernetes_utils_selfsigned_ssl_helm_repo_name }}/cert-manager"` | Helm chart reference for self-signed SSL.
`kubernetes_utils_selfsigned_ssl_helm_release_name` | `cert-manager` | Release name for the Helm chart.
`kubernetes_utils_selfsigned_ssl_helm_chart_version` | `""` | Version of the Helm chart, omitted by default.
`kubernetes_utils_selfsigned_ssl_helm_chart_namespace` | `cert-manager` | Namespace for the Helm chart.
`kubernetes_utils_selfsigned_ssl_helm_chart_values` | `{ installCRDs: true }` | Values to be used in the Helm chart.
`kubernetes_utils_selfsigned_ssl_root_create_issuer` | `true` | Whether to create a root issuer for self-signed SSL.
`kubernetes_utils_selfsigned_ssl_root_issuer_name` | `selfsigned-issuer` | Name of the root issuer.
`kubernetes_utils_selfsigned_ssl_root_issuer_kind` | `ClusterIssuer` | Kind of the root issuer.
`kubernetes_utils_selfsigned_ssl_root_issuer_namespace` | `cert-manager` | Namespace for the root issuer.
`kubernetes_utils_selfsigned_ssl_root_issuer_spec` | `{ selfSigned: {} }` | Specification for the root issuer.
`kubernetes_utils_selfsigned_ssl_ca_create_ca` | `true` | Whether to create a Certificate Authority.
`kubernetes_utils_selfsigned_ssl_ca_name` | `selfsigned-ca` | Name of the Certificate Authority.
`kubernetes_utils_selfsigned_ssl_ca_namespace` | `cert-manager` | Namespace for the Certificate Authority.
`kubernetes_utils_selfsigned_ssl_ca_common_name` | `"{{ kubernetes_utils_selfsigned_ssl_ca_name }}"` | Common name for the Certificate Authority.
`kubernetes_utils_selfsigned_ssl_ca_secret_name` | `"{{ kubernetes_utils_selfsigned_ssl_ca_name }}"` | Secret name for the Certificate Authority.
`kubernetes_utils_selfsigned_ssl_ca_algorithm` | `ECDSA` | Algorithm for the Certificate Authority.
`kubernetes_utils_selfsigned_ssl_ca_key_size` | `256` | Key size for the Certificate Authority.
`kubernetes_utils_selfsigned_ssl_ca_issuer_name` | `"{{ kubernetes_utils_selfsigned_ssl_root_issuer_name }}"` | Issuer name for the Certificate Authority.
`kubernetes_utils_selfsigned_ssl_ca_issuer_kind` | `"{{ kubernetes_utils_selfsigned_ssl_root_issuer_kind }}"` | Issuer kind for the Certificate Authority.
`kubernetes_utils_selfsigned_ssl_ca_issuer_group` | `cert-manager.io` | Issuer group for the Certificate Authority.
`kubernetes_utils_selfsigned_ssl_signed_ca_create_issuer` | `true` | Whether to create an issuer for the signed CA.
`kubernetes_utils_selfsigned_ssl_signed_ca_issuer_name` | `selfsigned-signed-issuer` | Name of the issuer for the signed CA.
`kubernetes_utils_selfsigned_ssl_signed_ca_issuer_kind` | `ClusterIssuer` | Kind of the issuer for the signed CA.
`kubernetes_utils_selfsigned_ssl_signed_ca_issuer_namespace` | `cert-manager` | Namespace for the issuer of the signed CA.
`kubernetes_utils_selfsigned_ssl_signed_ca_issuer_spec` | `{ ca: { secretName: "{{ kubernetes_utils_selfsigned_ssl_ca_secret_name }}" }}` | Specification for the issuer of the signed CA.
`kubernetes_utils_selfsigned_ssl_signed_certs` | `[]` | List of signed certificates to create.

`kubernetes_utils_selfsigned_ssl_signed_certs` is a list of dictionaries with the following keys:

Required:
- `name`: Name of the certificate
- `namespace`: Namespace of the certificate

Derived from name:
- `common_name`: Common name of the certificate
- `secret_name`: Secret name of the certificate

Assumed from signed CA:
- `issuer_name`: Name of the issuer for the certificate
- `issuer_kind`: Kind of the issuer for the certificate

Defaults:
- `issuer_group`: cert-manager.io
- `dnsnames`: `[ "{{ ansible_fqdn }}" ]`

Example:
```yaml
kubernetes_utils_selfsigned_ssl_signed_certs:
  - name: selfsigned-signed-cert  # Required
    namespace: default  # Required

    # The following are derived from name, not required
    common_name: selfsigned-signed-cert
    secret_name: selfsigned-signed-cert
    # The following are assumed and not required
    issuer_name: "{{ kubernetes_utils_selfsigned_ssl_signed_ca_issuer_name }}"
    issuer_kind: "{{ kubernetes_utils_selfsigned_ssl_signed_ca_issuer_kind }}"
    issuer_group: cert-manager.io
    # This will default to FQDN if not specified
    dnsnames:
      - "{{ ansible_fqdn }}"
```



See [defaults/main.yml](./defaults/main.yml) for more information.

Dependencies
------------
<!-- List dependencies on other roles or criteria -->
None

Example Playbook
----------------

```yaml
- name: Use diademiemi.selfsigned_ssl role
  hosts: "{{ target | default('selfsigned_ssl') }}"
  vars:
    kubernetes_utils_selfsigned_ssl_signed_certs:
      - name: awx-cert
        namespace: awx
  roles:
    - role: "diademiemi.selfsigned_ssl"
      tags: ['diademiemi', 'selfsigned_ssl', 'setup']    ```

```

License
-------

MIT

Author Information
------------------

- diademiemi (@diademiemi)
