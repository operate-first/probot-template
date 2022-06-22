<p align="center">
  <a href="https://github.com/open-services-group/probot-template">
    <img src="https://raw.githubusercontent.com/open-services-group/probot-template/main/static/robot.svg" width="160" alt="Probot's logo, a cartoon robot" />
  </a>
</p>
<h3 align="center"><a href="https://github.com/open-services-group/probot-template">Probot on Kubernetes - template repository</a></h3>
<p align="center">
  <a href="https://github.com/open-services-group/probot-template">
    <img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/open-services-group/probot-template">
  </a>
  <a href="https://github.com/open-services-group/probot-template/blob/main/LICENSE">
    <img alt="License" src="https://img.shields.io/badge/license-MIT-blue.svg">
  </a>
  <a href="https://github.com/open-services-group/probot-template/issues?q=is%3Aissue+is%3Aopen+label%3Akind%2Fbug">
    <img alt="Reported bugs" src="https://img.shields.io/github/issues-search/open-services-group/probot-template?color=red&label=reported%20bugs&query=is%3Aopen%20label%3Akind%2Fbug">
  </a>
  <a href="https://github.com/open-services-group/probot-template/issues?q=is%3Aissue+is%3Aopen+label%3Akind%2Fbug">
    <img alt="Feature requests" src="https://img.shields.io/github/issues-search/open-services-group/probot-template?label=feature%20requests&query=is%3Aopen%20label%3Akind%2Ffeature">
  </a>
</p>

---
## How to use

1. Create a new repository from this template
2. Template all references

    ```sh
    cat <<EOM > /tmp/data.yaml
    name: application-name
    description: Some text
    prod-namespace: namespaceA
    stage-namespace: namespaceB
    image: quay.io/org/repo
    team: team-name
    org-repo: org/repo
    EOM

    mustache /tmp/data.yaml manifests/base/controller/kustomization.yaml > manifests/base/controller/kustomization.yaml
    mustache /tmp/data.yaml manifests/base/tasks/kustomization.yaml > manifests/base/tasks/kustomization.yaml
    mustache /tmp/data.yaml manifests/base/kustomization.yaml > manifests/base/kustomization.yaml
    mustache /tmp/data.yaml manifests/overlays/stage/kustomization.yaml > manifests/overlays/stage/kustomization.yaml
    mustache /tmp/data.yaml manifests/overlays/prod/kustomization.yaml > manifests/overlays/prod/kustomization.yaml
    mustache /tmp/data.yaml src/app.ts > src/app.ts
    mustache /tmp/data.yaml package.json > package.json
    mustache /tmp/data.yaml package-lock.json > package-lock.json
    mv README.md README.old.md
    mustache /tmp/data.yaml README.template.md > README.md
    ```

3. Follow a guide at Probot on [how to create and configure a GitHubApp](https://probot.github.io/docs/development/#manually-configuring-a-github-app)

4. Create credentials secrets for deployment based on your GitHub app data

    ```sh
    # Copy secret from base
    cp manifests/base/controller/secret.yaml manifests/overlays/stage/secret.enc.yaml
    cp manifests/base/controller/secret.yaml manifests/overlays/prod/secret.enc.yaml

    # edit manifests/overlays/*/secret.enc.yaml filling in all data

    # Encrypt them via sops
    sops -e -i --pgp="0508677DD04952D06A943D5B4DC4116D360E3276" manifests/overlays/stage/secret.enc.yaml
    sops -e -i --pgp="0508677DD04952D06A943D5B4DC4116D360E3276" manifests/overlays/prod/secret.enc.yaml
    ```

5. Hack on `src/app.ts`.

## Resources

- [Probot documentation](https://probot.github.io/docs/)
- [Open Services Group extensions](https://github.com/open-services-group/probot-extensions)
- Example: [Peribolos as a service](https://github.com/open-services-group/peribolos-as-a-service)

## Contributions

See [`CONTRIBUTING.md`](CONTRIBUTING.md) on how to contribute.

---

## Credit

See [`ACKNOWLEDGMENTS.md`](ACKNOWLEDGMENTS.md).
