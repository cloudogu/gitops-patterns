cloudogu/gitops-patterns
===

## Intro
This repo collects some patterns (AKA strategies, models, approaches, best practices, etc.) about 
GitOps process design (AKA GitOps repository structures, etc.) as well as links to talks, articles and example repos.

It originated in [schnatterer's talk](https://cloudogu.github.io/gitops-talks/2023-03-mastering-gitops/#/) 
about the "perfect" GitOps process, our experience in [consulting](https://cloudogu.com/en/consulting/?mtm_campaign=gitops-playground&mtm_kwd=consulting&mtm_source=github&mtm_medium=link),
and our [GitOps trainings for both Flux and ArgoCD](https://platform.cloudogu.com/en/trainings/gitops-continuous-operations/?mtm_campaign=gitops-playground&mtm_kwd=training&mtm_source=github&mtm_medium=link).  

For questions or suggestions you are welcome to join us at our [community forum](https://community.cloudogu.com/c/gitops/23).

[![Discuss it on myCloudogu](https://static.cloudogu.com/static/images/discuss-it.png)](https://community.cloudogu.com/c/gitops/23)

PRs welcome!

## Pattern areas 

Let's categorize our GitOps patterns into separate ares to make them easier to grasp.

* **Operator deployment**: GitOps operators ↔ Clusters/Namespaces
* **Repository**: How many repos?
* **Promotion**: How to model environments/stages?
* **Wiring**: Bootstrapping operator, linking repos and folders

## GitOps Patterns

### Operator deployment

* **Hub and Spoke** [^1][^2] 1 Operator : n Clusters  
![Hube and spoke](https://github.com/cloudogu/gitops-talks/blob/1744c1d/images/deployment-hub-and-spoke.svg)
* **Standalone** [^1]: 1 Operator : 1 Cluster  
![Standalone](https://github.com/cloudogu/gitops-talks/blob/1744c1d/images/deployment-standalone.svg)
* **Namespaced** [^3]: n Operators : 1 Cluster  
![Namespaced](https://github.com/cloudogu/gitops-talks/blob/1744c1d/images/deployment-namespaced.svg)
* **Split-Instance** [^1]: 1 Operator : n Clusters; components split between management and target clusters


### Repository structure

* **Monorepo** [^2][^4]  
  Opposite: polyrepo [^2]
* **Repo per Team** [^4]  
  More generalized: Team ➡️ Tenant[^4]
* **Repo per Application** [^2][^4]  
  Implementations: 
    * Config replication [^3]
    * Repo pointer [^6][^3]
* **Repo per environment** [^4]  
  Synonym: Environment per repository[^5], Repo per Stage  


### Promotion

[Release](https://codefresh.io/blog/how-to-model-your-gitops-environments-and-promote-releases-between-them/)\/[Application](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro)/[Environment](https://github.com/kostis-codefresh/gitops-environment-promotion)/[Workload](https://www.weave.works/blog/kubernetes-workload-promotion-in-gitops-cd-pipelines) Promotion

* **Folder/Directory per environment**  
  Synonym: Environment per folder [^5]  
* **Repo per environment** [^4]  
  Synonym: Environment per repo [^5]  
* **Branch per environment**
  Synoym: Environment per branch [^5]  
  Often discouraged [^6] or declared an anti-pattern[^7][^14], but can work [^13].  
* **Preview environments** [^9] [^10] [^11]  
  Synoyms: ephemeral, dynamic, pull request[^8], test, temporary[^9]  
  Beyond the GitOps world also known as "Preview Deployments"[^15] and "Deploy Previews"[^16]


#### Implementing release promotion
* **Configuration Management**  
  Synonyms: Templating, Patching, Overlay, Rendering
  * plain kustomize (`kustomize.yaml`)
  * Helm 
    * via CRD such as `HelmRelease` (Flux) or `Application` (ArgoCD)
    * via Umbrella Chart[^12]
    * via `helm template` on CI server
* **Global Environments** vs **Environment per App**[^3]  
  ![Global Envs](https://github.com/cloudogu/gitops-talks/blob/1744c1d/images/global-environments.svg)
  ![Env per app](https://github.com/cloudogu/gitops-talks/blob/1744c1d/images/environment-per-app.svg)


### Wiring

* **Bootstrapping**,
  e.g. using `kubectl` or operator-specific CLI such as `flux` or `argocd-autopilot`
* **Linking**
  e.g. using Operator-specific CRDs such as `Kustomization` (Flux) or `Application` (ArgoCD)
* **Nesting**, e.g. App of Apps pattern (ArgoCD)
* **Templating**, e.g. implemented using `ApplicationSets`

## Public GitOps Repo Examples

See also [^3].


### GitOps Playground
[cloudogu/gitops-playground](https://github.com/cloudogu/gitops-playground)

* **Repo pattern**: Per team/monorepo mixed with per app
* **Operator pattern**: Hub and Spoke
* **Operator**: ArgoCD / Flux
* Boostrapping: `Helm`
* Linking: ArgoCD `Application`
* **Features**:
  * Automation via CI server
  * Mixed repo patterns
  * ArgoCD **and** Flux examples

![](https://github.com/cloudogu/gitops-talks/blob/1744c1d/docs/image-sources/repo-examples/1.svg)


### ArgoCD autopilot

[argoproj-labs/argocd-autopilot](https://github.com/argoproj-labs/argocd-autopilot)

* **Repo pattern**: Monorepo
* **Operator pattern**: Standalone / Hub and Spoke
* **Operator**: ArgoCD
* **Boostrapping**: `argocd-autopilot`
* Linking: `kustomize.yaml`, ArgoCD `Application`, `ApplicationSet`
* Features:
  * Operate ArgoCD with GitOps
  * In the future: a lot more automation
    and YAML creation

![](https://github.com/cloudogu/gitops-talks/blob/1744c1d/docs/image-sources/repo-examples/3.svg)


### Flux Monorepo

[fluxcd/flux2-kustomize-helm-example](https://github.com/fluxcd/flux2-kustomize-helm-example)

* **Repo pattern**: Monorepo
* **Operator pattern**: Standalone
* **Operator**: Flux 
* **Boostrapping**: `flux` CLI
* **Linking**: `kustomize.yaml`, Flux `Kustomization`
* **Features**: cross-cutting infra

![](https://github.com/cloudogu/gitops-talks/blob/1744c1d/docs/image-sources/repo-examples/4.svg)


### Flux repo per team/tenant

[fluxcd/flux2-multi-tenancy](https://github.com/fluxcd/flux2-multi-tenancy)

* **Repo pattern**: Repo per team/tenant
* **Operator pattern**: Standalone
* **Operator**: Flux 
* **Boostrapping**: `flux` CLI
* **Linking**: `kustomize.yaml`, Flux `Kustomization`
* **Features**: cross-cutting infra

![](https://github.com/cloudogu/gitops-talks/blob/1744c1d/docs/image-sources/repo-examples/5.svg)


### 📕 Path to GitOps examples

[christianh814/example-kubernetes-go-repo](https://github.com/christianh814/example-kubernetes-go-repo)

[christianh814/example-kubernetes-goflux-repo](https://github.com/christianh814/example-kubernetes-goflux-repo/)

[christianh814/example-openshift-go-repo](https://github.com/christianh814/example-openshift-go-repo)

* **Repo pattern**: Monorepo
* **Operator pattern**: Standalone
* **Operator**: [ArgoCD] [flux]
* **Boostrapping**: kubectl
* **Linking**: `kustomize.yaml`,
  * ArgoCD `Application`, `ApplicationSet` /
  * Flux  `Kustomization`
* **Features**:
  * Cross-cutting infra and app(s)
  * ArgoCD **and** Flux examples

![](https://github.com/cloudogu/gitops-talks/blob/1744c1d/docs/image-sources/repo-examples/6.svg)


### Environment variations

[kostis-codefresh/gitops-environment-promotion](https://github.com/kostis-codefresh/gitops-environment-promotion)

* Operator: ArgoCD  (Flux)
* Features:
  * Env variants for a single app
  * Promotion "via cp"

![](https://github.com/cloudogu/gitops-talks/blob/1744c1d/docs/image-sources/repo-examples/7.svg)

## Synonoyms

* Patterns ≈ strategies, models, approaches, best practices, standards
* GitOps process design ≈ GitOps repository structures,
* GitOps Operator ≈ GitOps controller
* Config Repo = GitOps repo, Infra repo, Payload repo  
  ![Config repo example](https://github.com/cloudogu/gitops-talks/blob/1744c1d/images/gitops-repo-example.svg) 
* App repo = Source code repo, Source repo  
  ![App repo example](https://github.com/cloudogu/gitops-talks/blob/1744c1d/images/app-repo-example.svg) 
* Environment = Stage
* Folder = Directory
* Templating ≈ Templating, Patching, Overlay, Rendering, Bundling, Packaging?


## References

[^1]: Article [A Comprehensive Overview of Argo CD Architectures – 2023](https://codefresh.io/blog/a-comprehensive-overview-of-argo-cd-architectures-2023/) by Dan Garfield  
[^2]: Article/Book [How to set up your GitOps directory structure](https://developers.redhat.com/articles/2022/09/07/how-set-your-gitops-directory-structure) by Christian Hernandez
[^3]: Slides [The perfect GitOps process: repos, folders, stages, patterns](https://cloudogu.github.io/gitops-talks/2023-03-mastering-gitops/#/) by Johannes Schnatterer
[^4]: Documentation [Flux | Ways of structuring your repositories](https://fluxcd.io/flux/guides/repository-structure/)
[^5]: Lesson [GitOps at Scale Lesson series - Git repository strategies](https://learning.codefresh.io/path-player?courseid=gitops-scale&unit=gitops-scale_63a08184b7f67Unit) by Codefresh (paywalled)
[^6]: Talk [GitOps: Core Concepts & Ways of Structuring Your Repos](https://www.youtube.com/watch?v=vLNZA_2Na_s) by Pinky Ravi and Scott Rigby
[^7]: Article [Stop Using Branches for Deploying to Different GitOps Environments](https://codefresh.io/blog/how-to-model-your-gitops-environments-and-promote-releases-between-them/) by Kostis Kapelonis
[^8]: Video [Environments Based On Pull Requests (PRs): Using Argo CD To Apply GitOps Principles On Previews](https://www.youtube.com/watch?v=cpAaI8p4R60) by Viktor Farcic 
[^9]: Article [Creating Temporary Preview Environments Based On Pull Requests With Argo CD And Codefresh](https://codefresh.io/blog/creating-temporary-preview-environments-based-pull-requests-argo-cd-codefresh/) by Codefresh
[^10]: Talk [GitOps Con Europe - Implementing Preview Environments with GitOps in Kubernetes ](https://www.youtube.com/watch?v=QNAiIJRIVWA&t=202s) by  François Le Pape, Remazing
[^11]: Talk [Preview Environments with ArgoCD](https://www.youtube.com/watch?v=7ahiwZuiCBM) by Brandon Phillips
[^12]: Documentation [Helm | Chart Development Tips and Tricks](https://v3-1-0.helm.sh/docs/howto/charts_tips_and_tricks/#complex-charts-with-many-dependencies)
[^13]: Article [Monitoring and Hardening the GitOps Delivery Pipeline with Flux](https://medium.com/mediamarktsaturn-tech-blog/monitoring-and-hardening-the-gitops-delivery-pipeline-with-flux-a226bdef0351) by Florian Heubeck
[^14]: Article [Git best practices: Workflows for GitOps deployments ](https://developers.redhat.com/articles/2022/07/20/git-workflows-best-practices-gitops-deployments) by Christian Hernandez
[^15]: Documentation [Vercel: Preview Deployments](https://vercel.com/docs/concepts/deployments/preview-deployments)
[^16]: Documentation [Netlify: Deploy Previews](https://docs.netlify.com/site-deploys/deploy-previews/)

