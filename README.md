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
2. Follow a guide at Probot on [how to create and configure a GitHubApp](https://probot.github.io/docs/development/#manually-configuring-a-github-app)
3. Create a Quay.io repository to host the controller container image
   - Create a new Quay.io registry as an empty registry (go to [quay.io/new](https://quay.io/new/), select your namespace and mark the repository as _Public_)
   - Create a robot account (go to Quay.io, in top right corner select _Account Settings_, then second tab from the top _Robot Accounts -> Create Robot Account_)
   - Grant this bot account __Write__ access to your new container repository
   - Save robot credentials as `QUAY_USERNAME` and `QUAY_PASSWORD` in the repository secrets (on GitHub repository page open _Settings -> Secrets -> Actions -> New repository secret_)
   - In order to [properly expire container images](./.github/actions/set-expiration/action.yaml) we also need `QUAY_OAUTH_TOKEN`. You can either use your own account token or (better) create new Quay Application in your organization. To do so, go to `https://quay.io/organization/<org_name>`, then _Applications -> Create New Application_. Copy the OAuth Token and save it as `QUAY_OAUTH_TOKEN` in the repository secrets
4. Template all references

    ```sh
    cat <<EOM > /tmp/data.yaml
    name: application-name
    description: Some text
    prod-namespace: namespaceA
    stage-namespace: namespaceB
    image: quay_image_name
    quay_org: quay_org
    org: github_org
    team: team-name
    repo: repo
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
    mustache /tmp/data.yaml .github/workflows/pr.yaml > .github/workflows/pr.yaml
    mustache /tmp/data.yaml .github/workflows/push.yaml > .github/workflows/push.yaml
    mustache /tmp/data.yaml .github/workflows/release.yaml > .github/workflows/release.yaml
    ```


5. Create credentials secrets for deployment based on your GitHub app data

    ```sh
    # Copy secret from base
    cp manifests/base/controller/secret.yaml manifests/overlays/stage/secret.enc.yaml
    cp manifests/base/controller/secret.yaml manifests/overlays/prod/secret.enc.yaml

    # edit manifests/overlays/*/secret.enc.yaml filling in all data
    vim manifests/overlays/*/secret.enc.yaml

    # Encrypt them via sops
    sops -e -i --pgp="0508677DD04952D06A943D5B4DC4116D360E3276" manifests/overlays/stage/secret.enc.yaml
    sops -e -i --pgp="0508677DD04952D06A943D5B4DC4116D360E3276" manifests/overlays/prod/secret.enc.yaml
    ```

6. Hack on `src/app.ts`.

## Resources

- [Probot documentation](https://probot.github.io/docs/)
- [Open Services Group extensions](https://github.com/open-services-group/probot-extensions)
- Example: [Peribolos as a service](https://github.com/open-services-group/peribolos-as-a-service)

## Contributions

See [`CONTRIBUTING.md`](CONTRIBUTING.md) on how to contribute.

---

## Credit

See [`ACKNOWLEDGMENTS.md`](ACKNOWLEDGMENTS.md).
