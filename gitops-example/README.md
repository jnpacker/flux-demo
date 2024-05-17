# Examples
## Example 1 is using FLUXCD to manage the FLUXCD install, this can also be accomplished with "flux bootstrap..."
1. Complete [../fluxcd-install/README.md](../fluxcd-install/README.md)
2. Create a secret with the GitDeploy token if using a private repository
   1. ```
      flux create secret git flux-git-deploy --url=ssh://git@github.com/jnpacker/fluxcd-demo.git --private-key-file=../id_flux_rsa --export > secret-flux-git-deploy.yaml
      ```
   2. `kubectl apply -f ./secret-flux-git-deploy.yaml`
3. If you want FLUXCD to self-manage using this repositories manifests
  ```
  kubectl apply -f ./gitrepo-fluxcd-manifest.yaml
  ```