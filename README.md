# Red Hat Advanced Cluster Management for Kubernetes, Configuration Policies with Flux

# Instructions
  1. Generate an SSH key pair `ssh-keygen -o -t rsa -C "my-name@email.com"
  2. Fork this repository
  3. In the newly forked repository, go to <b>Settings</b> > <b>Deploy Keys</b> > <b>Add deploy key</b>
  4. Add the id_rsa.pub key that was generated in step 1
  5. bootstrap flux (TBD - kubectl and YAML)
```
    flux bootstrap git \
      --url=ssh://git@<host>/<org>/<repository> \
      --branch=main \
      --private-key-file=<path/to/private.key> \
      --password=<key-passphrase> \
      --path=clusters/<my-cluster>
```
  6. While connected to the same cluster where `flux bootstrap` was run, execute the `flux-controler-nonroot.sh` script to set the clusterrole rbac's
  7. In `clusters/<my-cluster>/flux-system/kustomization.yaml` file, add the following patch:
    ```
    patches:
        - patch: |
            apiVersion: apps/v1
            kind: Deployment
            metadata:
                name: all
            spec:
                template:
                spec:
                    containers:
                    - name: manager
                        securityContext:
                        runAsUser: 65534
                        seccompProfile:
                            $patch: delete      
            target:
            kind: Deployment
            labelSelector: app.kubernetes.io/part-of=flux
        - patch: |-
            - op: remove
                path: /metadata/labels/pod-security.kubernetes.io~1warn
            - op: remove
                path: /metadata/labels/pod-security.kubernetes.io~1warn-version      
            target:
            kind: Namespace
            labelSelector: app.kubernetes.io/part-of=flux
    ```
    8. Re-run the `flux bootstrap...` command after each of the changes or after making all the changes





  # Fixes
  * If the bootstrapping fails and you change the `--path` you will want to run `flux uninstall`
