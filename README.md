cloudogu/gitops-patterns
===

## Intro
This repo collects some patterns (AKA strategies, models, approaches, best practices, etc.) about
GitOps process design (AKA GitOps repository structures, etc.) as well as links to talks, articles and example repos.

It originated in [schnatterer's talk](https://cloudogu.github.io/gitops-talks/2023-03-mastering-gitops/#/)
about the "perfect" GitOps process, our experience in [consulting](https://cloudogu.com/en/consulting/?mtm_campaign=gitops-playground&mtm_kwd=consulting&mtm_source=github&mtm_medium=link),
and our [GitOps trainings for both Flux and ArgoCD](https://platform.cloudogu.com/en/trainings/gitops-continuous-operations/?mtm_campaign=gitops-playground&mtm_kwd=training&mtm_source=github&mtm_medium=link).

You can read more about the patterns in [this series of articles](#articles).

[![Discuss it on myCloudogu](https://static.cloudogu.com/static/images/discuss-it.png)](https://community.cloudogu.com/c/gitops/23)

PRs welcome!

## Contents

- [Articles](#articles)
- [Pattern categories](#pattern-categories)
- [GitOps Patterns](#gitops-patterns)
  - [Operator deployment](#operator-deployment)
    - [Hub and Spoke](#hub-and-spoke)
    - [Instance per Cluster](#instance-per-cluster)
    - [Instance per Namspace](#instance-per-namespace)
    - [Split-Instance](#split-instance)
  - [Repository structure](#repository-structure)
    - [Monorepo](#monorepo)
    - [Repo per Team](#repo-per-team)
    - [Repo per Application](#repo-per-app) (can be implemented via [Repo Separation](#repo-separation), [Config Replication](#config-replication), [Repo Pointer](#repo-pointer) or [Config Split](#config-split))
    - [Repo per Environment](#repo-per-env)
  - [Promotion](#promotion)
    - [Environments](#environments)
      - [Folder/Directory per environment](#folder-per-env)
      - [Repo per environment](#repo-per-env)
      - [Branch per environment](#branch-per-env)
      - [Preview environments](#preview-env)
    - [Implementation](#implementation)
      - [Configuration Management](#config-management), includes [Rendered Manifests](#rendered-manifests) and [Umbrella Chart](#umbrella-chart)
      - [Global Environments vs Environment per App](#global-vs-env-per-app)
      - [Config update](#config-update)
  - [Wiring](#wiring)
    - [Bootstrapping](#bootstrapping)
    - [Linking](#linking), includes [Nesting](#nesting) (e.g. [App of Apps](#app-of-apps)), [Templating](#templating)
- [Public GitOps Repo Examples](#public-gitops-repo-examples)
  - [GitOps Playground](#gitops-playground)
  - [ArgoCD autopilot](#argocd-autopilot)
  - [Flux Monorepo](#flux-monorepo)
  - [Flux repo per team/tenant](#flux-repo-per-teamtenant)
  - [📕 Path to GitOps examples](#-path-to-gitops-examples)
  - [Environment variations](#environment-variations)
  - [Others](#others)
- [Synonyms](#synonyms)
- [References](#references)

## Articles

<table border="0">
   <tr>
     <td><img width="50px" src="https://cloudogu.com/assets/blog/2023/gitops-patterns-1_150x150-5d446b6ca993915cb215d62a735c10d9f11750d5ad38836005191ad863c39c019fe08f781841a17349b9d81e1f71f9199669acce02965e0d27904e06a289e23f.png" alt="article icon"/></td>
     <td><a href="https://cloudogu.com/en/blog/gitops-repository-patterns-part-1-introduction">Part 1️⃣: GitOps Chasm + categories</a></td>
  </tr>
  <tr>
     <td><img width="50px" src="https://cloudogu.com/assets/blog/2023/gitops-patterns-2_150x150-e75f7484d3ac721d49765dc4846909900ee1435a9134eaa900950ccd1bda41154acd087f39900377124e3915819388fe3abcf2fff549f42e050744cf3afc52d6.png" alt="article icon"/></td>
     <td><a href="https://cloudogu.com/en/blog/gitops-repository-patterns-part-2-operator-deployment-patterns">Part 2️⃣: Deployment patterns</a></td>
  </tr>
  <tr>
     <td><img width="50px" src="https://cloudogu.com/assets/blog/2023/gitops-patterns-3_150x150-51786b5ff0561b8ed439b437e3fbab7c9fe127447c87145e8899780c7f360bab333287ac3ed9e099df71a57ec7b1cfeb8dab8c38a6667986b9f3865adda0e945.png" alt="article icon"/></td>
     <td><a href="https://cloudogu.com/en/blog/gitops-repository-patterns-part-3-repository-patterns">Part 3️⃣: Repo patterns</a></td>
  </tr>
  <tr>
    <td><img width="50px" src="https://cloudogu.com/assets/blog/2023/gitops-patterns-4_150x150-f4c1b3e73b2b00f3da6b2219a6d73dc98a0d93e4b5d6e986be820ce1df70900e1446c28c8a77dbb22568ed77871f6eb659091841edb80117162be48c08bfcd7e.png" alt="article icon"/></td>
    <td><a href="https://cloudogu.com/en/blog/gitops-repository-patterns-part-4-promotion-patterns">Part 4️⃣: Promotion patterns</a></td>
  </tr>
  <tr>
    <td><img width="50px" src="https://cloudogu.com/assets/blog/2023/gitops-patterns-5_150x150-bf3aeb38358d09ba8b2f98bb721207e942bbf5e67b9af6f7cc4c70cda8cb9a27707adefe73a3dfe343ba744b91c0bc6e59ace749efaa8dace729be95b75d9a38.png" alt="article icon"/></td>
    <td><a href="https://cloudogu.com/en/blog/gitops-repository-patterns-part-5-wiring-patterns">Part 5️⃣: Wiring patterns + conclusion</a></td>
  </tr>
  <tr>
    <td><img width="50px" src="https://cloudogu.com/assets/blog/2023/gitops-patterns-6_150x150-fdb06341511e6699307a4f913b9d5635cbaefa432029c468c58476d5c6108485a8f50ce68202a5baf92743ae1e9388243bec81cd206de2bc40c647612b46318f.png" alt="article icon"/></td>
    <td><a href="https://cloudogu.com/en/blog/gitops-repository-patterns-part-6-examples">Part 6️⃣: Example Repositories</a></td>
  </tr>
</table>

## Pattern categories

Let's group GitOps patterns into separate categories to make them easier to grasp.  
See also [part 1️⃣ of the article series](https://cloudogu.com/en/blog/gitops-repository-patterns-part-1-introduction).

* **Operator deployment**: GitOps operators ↔ Clusters/Namespaces
* **Repository**: How many repos?
* **Promotion**: 
  * Environments: How to model environments/stages?
  * Implementation: How to implement the actual process of promotion?
* **Wiring**: Bootstrapping operator, linking repos and folders

## GitOps Patterns

### Operator deployment

See also [part 2️⃣ of the article series](https://cloudogu.com/en/blog/gitops-repository-patterns-part-2-operator-deployment-patterns).

* **Hub and Spoke** [^1][^2] 1 Operator : n Clusters  <span id="hub-and-spoke"/>    
  Synonyms: Management Cluster [^18] - sounds familiar, e.g. from Cluster API, but it requires a cluster. In SaaS/PaaS constellations, the central instance may not even be operated in a cluster. Therefore, the term "Hub and Spoke" seems more fitting to us, especially since it is also well known from computer networks or logistics.  
  ![Hube and spoke](src/svg/deployment-hub-and-spoke.svg)
* **Instance Per Cluster** [^18] : 1 Operator : 1 Cluster <span id="standalone"/> <span id="instance-per-cluster"/>  
  Synonyms: Standalone [^1] - could apply to both "Instance per Cluster" and "Instance per Namespace"; does not define the relationship to clusters and namespaces precisely enough, as not every operator supports both deployment patterns.  
  ![Instance Per Cluster](src/svg/deployment-instance-per-cluster.svg)
* **Instance per Namespace**: n Operators : 1 Cluster <span id="namespaced"/> <span id="instance-per-namespace"/>  
  Synonyms: Namespaced [^3]  
  ![Instance per Namespace](src/svg/deployment-instance-per-namespace.svg)

More Patterns:
* Split-Instance[^1]: 1 Operator : n Clusters; components split between management and target clusters
* Hybrids of Hub and Spoke and Instance per Cluster patterns: "Instance per Logical Group" and "Argo Managing more Argos".[^18]

### Repository structure

See also [part 3️⃣ of the article series](https://cloudogu.com/en/blog/gitops-repository-patterns-part-3-repository-patterns).

* **Monorepo** [^2][^4] <span id="monorepo"/>  
  Opposite: polyrepo [^2]
* **Repo per Team** [^4] <span id="repo-per-team"/>  
  More generalized: Team ➡️ Tenant[^4]
* **Repo per Application** [^2][^4] <span id="repo-per-app"/>    
  Implementations:
  * Repo separation [^19] <span id="repo-separation"/>  
    Keep code in app repo, config in config repo   
    <img width=50% src="https://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/cloudogu/gitops-patterns/main/src/puml/repo-separation.puml&fmt=svg">
  * Config replication [^3] <span id="config-replication"/>  
    Keep config in app repo and have CI server replicate it to the config repo  
    <img width=50% src="https://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/cloudogu/gitops-patterns/main/src/puml/config-replication.puml&fmt=svg">  
  * Repo pointer [^6][^3][^4] <span id="repo-pointer"/>  
    Keep the whole config in app repo and add a pointer from config repo (e.g. Argo CD `Application` or a Flux `GitRepository`+`Kustomization`), avoiding redundancy   
    <img width=50% src="https://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/cloudogu/gitops-patterns/main/src/puml/repo-pointer.puml&fmt=svg">
  * Config Split [^20] <span id="config-split"/>  
    Keep parts of the config in app repo (e.g. helm chart or kustomize base), and rest in config repo (e.g. `value.yaml`s or overlays for different envs).  
    Then have the config repo point to the app repo (e.g. via ArgoCD `Application` or Flux `Kustomization`).  
    <img width=50% src="https://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/cloudogu/gitops-patterns/main/src/puml/config-split-helm-git.puml&fmt=svg">  
    Alternative implementations: have CI server push chart to **helm registry**  
    <img width=50% src="https://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/cloudogu/gitops-patterns/main/src/puml/config-split-helm-repo.puml&fmt=svg">  
    Or use push **helm chart to OCI registry**  
    <img width=50% src="https://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/cloudogu/gitops-patterns/main/src/puml/config-split-helm-oci.puml&fmt=svg">  
    Or use any config management tool (e.g. `helm template`, `kustomize build`, `kubectl kustomize`, [jsonnet](https://jsonnet.org/), [cuelang](https://cuelang.org/), [timoni](https://timoni.sh/), etc. ) on the CI server for pushing the final manifests as **OCI artifacts[^22] to the registry** (Rendered Manifests Pattern[^23]).  
    Then have the config repo point to the OCI artifact (e.g. via Flux `Kustomization`).  
    This way, the OCI registry functions as a "GitOps Cache"[^21]:  
    The operator only needs to pull the artifacts instead of rendering/overlaying the config from different sources.   
    <img width=50% src="https://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/cloudogu/gitops-patterns/main/src/puml/config-split-oci-artifacts.puml&fmt=svg">
* **Repo per environment** [^4] <span id="repo-per-env"/>  
  Synonym: Environment per repository[^5], Repo per Stage


### Promotion

See also [part 4️⃣ of the article series](https://cloudogu.com/en/blog/gitops-repository-patterns-part-4-promotion-patterns).

We understand the term "promotion" as the process of deploying applications to different environments.  
Sometimes, the term "promotion" is prefixed with other words: [Release](https://codefresh.io/blog/how-to-model-your-gitops-environments-and-promote-releases-between-them/)/[Application](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro)/[Environment](https://github.com/kostis-codefresh/gitops-environment-promotion)/[Workload](https://www.weave.works/blog/kubernetes-workload-promotion-in-gitops-cd-pipelines)/[Change](https://twitter.com/argoproj/status/1642527308450873345).

For promotion, we see different sets of patterns: 
* one regarding the modelling of environments and
* one regarding the implementation of the actual process of promotion.

#### Environments

* **Folder/Directory per environment** <span id="folder-per-env"/>  
  Synonym: Environment per folder [^5]
* **Repo per environment** [^4]  <span id="repo-per-env"/>  
  Synonym: Environment per repo [^5]
* **Branch per environment**  <span id="branch-per-env"/>  
  Synonym: Environment per branch [^5]  
  Often discouraged [^6] or declared an anti-pattern[^7][^14], but can work [^13].
* **Preview environments** [^9] [^10] [^11] <span id="preview-env"/>  
  Synonyms: ephemeral, dynamic, pull request[^8], test, temporary[^9]  
  Beyond the GitOps world also known as "Preview Deployments"[^15] and "Deploy Previews"[^16]

#### Implementation

* **Configuration Management** <span id="config-management"/>  
  * Synonyms:
    * General: Templating, Patching, Overlay, Rendering, Hydrating
    * templates = dry; rendered = hydrated [^26]
  * Where to render the manifests?
    * On the CI Server (**Rendered Manifests Pattern**[^23]) <span id="rendered-manifests"/>  
      This pattern also fits nicely with Flux's OCI artifacts feature (see [Config split](#config-split)).
    * On the GitOps Operator (**GitOps Operator Rendering** or **GitOps Controller Rendering**[^25])
  * Tools: 
    * Kustomize
      * Plain`kustomization.yaml` - **operator-agnostic** (GitOps Operator Rendering on Argo CD and Flux)
      * ≠ Flux CRD `Kustomization` (GitOps Operator Rendering)
      * `kustomize build` / `kubectl kustomize` via CI server (Rendered Manifests)
    * Helm
      * CRD such as `HelmRelease` (GitOps Operator Rendering on Flux) or `Application` (GitOps Operator Rendering on ArgoCD)
      * Umbrella Chart[^12] <span id="umbrella-chart"/> (common on Argo CD, possible but unusual on Flux)
      * `helm template` on CI server (Rendered Manifests)
    * Others, e.g. Jsonnet, CueLang, Timoni
      * Rendered Manifests Pattern
      * Argo CD Config Management Plugins[^24] (built-in for JSonnet)
* **Global Environments** vs **Environment per App**[^3]  <span id="global-vs-env-per-app"/>  
  ![Global Envs](src/svg/global-environments.svg)
  ![Env per app](src/svg/environment-per-app.svg)
* **Config update** <span id="config-update"/>  
  Who updates image (version) in GitOps repo, creates branch and PR?
  * Manual: Human pushes branch and create PR 🥵
  * CI Server: Build job pushes branch, creates PR  
    <img width=50% src="https://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/cloudogu/gitops-patterns/main/src/puml/config-update-ci.puml&fmt=svg">  
  * Image Updater: Operator pushes branch, create PR manually  
    <img width=50% src="https://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/cloudogu/gitops-patterns/main/src/puml/config-update-image-updater.puml&fmt=svg">
  * Dependency Bot: Bot pushes branch, creates PR  
    <img width=50% src="https://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/cloudogu/gitops-patterns/main/src/puml/config-update-dependency-bot.puml&fmt=svg">

### Wiring

See also [part 5️⃣ of the article series](https://cloudogu.com/en/blog/gitops-repository-patterns-part-5-wiring-patterns).

* **Bootstrapping** <span id="bootstrapping"/>,
  e.g. using `kubectl` or operator-specific CLI such as `flux` or `argocd-autopilot`
* **Linking** <span id="linking"/>
  e.g. using Operator-specific CRDs such as `Kustomization` (Flux) or `Application` (ArgoCD)
  * **Nesting**, e.g. App of Apps pattern [^17] (ArgoCD) <span id="nesting"/> <span id="app-of-apps"/>
  * **Templating**, e.g. implemented using `ApplicationSets` <span id="templating"/>

## Public GitOps Repo Examples

See also [^3].

See also [part 6️⃣ of the article series](https://cloudogu.com/de/blog/gitops-repository-patterns-part-6-examples).

The diagrams have been created with [tree2svg](https://github.com/schnatterer/tree2svg).

### GitOps Playground
[cloudogu/gitops-playground](https://github.com/cloudogu/gitops-playground)

* **Repo pattern**: Per team mixed with per app (implemented via Config Replication)
* **Operator pattern**: Instance per Cluster (Hub and Spoke also possible)
* **Operator**: ArgoCD (Flux)
* Boostrapping: `Helm`, `kubectl`
* Linking: ArgoCD `Application`
* **Features**:
  * Env per app Pattern
  * Operate ArgoCD with GitOps
  * Config Update via CI server
  * Mixed repo patterns
  * ArgoCD **and** Flux examples

![](src/repo-examples/2-with-ci.svg)


### ArgoCD autopilot

[argoproj-labs/argocd-autopilot](https://github.com/argoproj-labs/argocd-autopilot)

* **Repo pattern**: Monorepo
* **Operator pattern**: Instance per Cluster / Hub and Spoke
* **Operator**: ArgoCD
* **Boostrapping**: `argocd-autopilot`
* Linking: `kustomization.yaml`, ArgoCD `Application`, `ApplicationSet`
* Features:
  * Operate ArgoCD with GitOps
  * In the future: a lot more automation
    and YAML creation

![](src/repo-examples/3.svg)


### Flux Monorepo

[fluxcd/flux2-kustomize-helm-example](https://github.com/fluxcd/flux2-kustomize-helm-example)

* **Repo pattern**: Monorepo
* **Operator pattern**: Instance per Cluster
* **Operator**: Flux
* **Boostrapping**: `flux` CLI
* **Linking**: `kustomization.yaml`, Flux `Kustomization`
* **Features**: cross-cutting infra

![](src/repo-examples/4.svg)


### Flux repo per team/tenant

[fluxcd/flux2-multi-tenancy](https://github.com/fluxcd/flux2-multi-tenancy)

* **Repo pattern**: Repo per team/tenant
* **Operator pattern**: Instance per Cluster
* **Operator**: Flux
* **Boostrapping**: `flux` CLI
* **Linking**: `kustomization.yaml`, Flux `Kustomization`
* **Features**: cross-cutting infra

![](src/repo-examples/5.svg)


### 📕 Path to GitOps examples

[christianh814/example-kubernetes-go-repo](https://github.com/christianh814/example-kubernetes-go-repo)

[christianh814/example-kubernetes-goflux-repo](https://github.com/christianh814/example-kubernetes-goflux-repo/)

[christianh814/example-openshift-go-repo](https://github.com/christianh814/example-openshift-go-repo)

* **Repo pattern**: Monorepo
* **Operator pattern**: Instance per Cluster
* **Operator**: [ArgoCD] [flux]
* **Boostrapping**: kubectl
* **Linking**: `kustomization.yaml`,
  * ArgoCD `Application`, `ApplicationSet` /
  * Flux  `Kustomization`
* **Features**:
  * Cross-cutting infra and app(s)
  * ArgoCD **and** Flux examples

![](src/repo-examples/6.svg)


### Environment variations

[kostis-codefresh/gitops-environment-promotion](https://github.com/kostis-codefresh/gitops-environment-promotion)

* Operator: ArgoCD  (Flux)
* Features:
  * Env variants for a single app
  * Promotion "via cp"

![](src/repo-examples/7.svg)

### Others

Here are some other examples that we haven't had a chance to look at in more detail:

* [Lp-Francois/argocd-multi-tenancy](https://github.com/Lp-Francois/argocd-multi-tenancy) 

## Synonyms

* Patterns ≈ strategies, models, approaches, best practices, standards
* GitOps process design ≈ GitOps repository structures,
* GitOps Operator ≈ GitOps controller ≈ GitOps agent
* Config Repo = GitOps repo, Infra repo, Payload repo  
  ![Config repo example](src/svg/config-repo-example.svg)
* App repo = Source code repo, Source repo  
  ![App repo example](src/svg/app-repo-example.svg)
* Environment = Stage
* Folder = Directory
* Templating ≈ Patching, Overlay, Rendering, Bundling, Packaging?


## References

[^1]: Article [A Comprehensive Overview of Argo CD Architectures – 2023](https://codefresh.io/blog/a-comprehensive-overview-of-argo-cd-architectures-2023/) by Dan Garfield  
[^2]: Article/Book [How to set up your GitOps directory structure](https://developers.redhat.com/articles/2022/09/07/how-set-your-gitops-directory-structure) by Christian Hernandez  
[^3]: Slides [The perfect GitOps process: repos, folders, stages, patterns](https://cloudogu.github.io/gitops-talks/2023-03-mastering-gitops/#/) by Johannes Schnatterer  
[^4]: Documentation [Flux | Ways of structuring your repositories](https://github.com/fluxcd/website/blob/a426979/content/en/flux/guides/repository-structure.md#repo-per-app)  
[^5]: Lesson [GitOps at Scale Lesson series - Git repository strategies](https://learning.codefresh.io/path-player?courseid=gitops-scale&unit=gitops-scale_63a08184b7f67Unit) by Codefresh (paywalled)  
[^6]: Talk [GitOps: Core Concepts & Ways of Structuring Your Repos](https://www.youtube.com/watch?v=vLNZA_2Na_s) by Pinky Ravi and Scott Rigby  
[^7]: Article [Stop Using Branches for Deploying to Different GitOps Environments](https://codefresh.io/blog/how-to-model-your-gitops-environments-and-promote-releases-between-them/) by Kostis Kapelonis  
[^8]: Video [Environments Based On Pull Requests (PRs): Using Argo CD To Apply GitOps Principles On Previews](https://www.youtube.com/watch?v=cpAaI8p4R60) by Viktor Farcic  
[^9]: Article [Creating Temporary Preview Environments Based On Pull Requests With Argo CD And Codefresh](https://codefresh.io/blog/creating-temporary-preview-environments-based-pull-requests-argo-cd-codefresh/) by Codefresh  
[^10]: Talk [GitOps Con Europe - Implementing Preview Environments with GitOps in Kubernetes ](https://www.youtube.com/watch?v=QNAiIJRIVWA&t=202s) by  François Le Pape, Remazing  
[^11]: Talk [Preview Environments with ArgoCD](https://www.youtube.com/watch?v=7ahiwZuiCBM) by Brandon Phillips  
[^12]: Documentation [Helm | Chart Development Tips and Tricks](https://github.com/helm/helm-www/blob/d2543/content/en/docs/howto/charts_tips_and_tricks.md#complex-charts-with-many-dependencies)  
[^13]: Article [Monitoring and Hardening the GitOps Delivery Pipeline with Flux](https://medium.com/mediamarktsaturn-tech-blog/monitoring-and-hardening-the-gitops-delivery-pipeline-with-flux-a226bdef0351) by Florian Heubeck  
[^14]: Article [Git best practices: Workflows for GitOps deployments ](https://developers.redhat.com/articles/2022/07/20/git-workflows-best-practices-gitops-deployments) by Christian Hernandez  
[^15]: Documentation [Vercel: Preview Deployments](https://vercel.com/docs/concepts/deployments/preview-deployments)  
[^16]: Documentation [Netlify: Deploy Previews](https://docs.netlify.com/site-deploys/deploy-previews/)  
[^17]: Documentation [ArgoCD: Cluster Bootstrapping - App Of Apps Pattern](https://github.com/argoproj/argo-cd/blob/v2.9.3/docs/operator-manual/cluster-bootstrapping.md#app-of-apps-pattern)  
[^18]: Talk  [Control Plane, Service, or Both? – Argo CD Multi-Cluster Architectures - Nicholas Morey, Akuity](https://www.youtube.com/watch?v=vyaZv4yM3_o), Article [How many do you need? - Argo CD Architectures Explained](https://akuity.io/blog/argo-cd-architectures-explained/) by Nicholas Morey  
[^19]: Documentation [Argo CD: Best Practices](https://github.com/argoproj/argo-cd/blob/v2.8.4/docs/user-guide/best_practices.md#separating-config-vs-source-code-repositories)  
[^20]: [Discussion on LinkedIn](https://www.linkedin.com/feed/update/urn:li:activity:7121084907526713346?commentUrn=urn%3Ali%3Acomment%3A%28activity%3A7121084907526713346%2C7121143258256166912%29&dashCommentUrn=urn%3Ali%3Afsd_comment%3A%287121143258256166912%2Curn%3Ali%3Aactivity%3A7121084907526713346%29) Benjamin Ruland and Johannes Schnatterer  
[^21]: Talk: [Mastering GitOps 2023: Keynote: GitOps Emerging Developments and Predictions](https://vimeo.com/805175348) by Alexis Richardson  
[^22]: Documentation [Flux | OCI cheatsheet](https://github.com/fluxcd/website/blob/a426979/content/en/flux/cheatsheets/oci-artifacts.md)  
[^23]: Talk: [GitOpsCon EU 23: The Art of GitOps: Rendered Manifests](https://gitopsconeu2023.sched.com/event/1Unk5/the-art-of-gitops-rendered-manifests-christian-hernandez-akuity?iframe=yes&w=100%&sidebar=yes&bg=no) by Christian Hernandez  
[^24]: Documentation [Argo CD | Config Management Plugins](https://github.com/argoproj/argo-cd/blob/v2.9.3/docs/operator-manual/config-management-plugins.md)
[^25]: [Discussion on LinkedIn](https://www.linkedin.com/feed/update/urn:li:activity:7138481326424276992?commentUrn=urn%3Ali%3Acomment%3A%28activity%3A7138481326424276992%2C7138548943428194304%29&dashCommentUrn=urn%3Ali%3Afsd_comment%3A%287138548943428194304%2Curn%3Ali%3Aactivity%3A7138481326424276992%29)  Christian Hernandez, Scott Rigby and Johannes Schnatterer  
[^26]: [Discussion on GitHub](https://github.com/argoproj/argo-cd/pull/17506#discussion_r1525548738)  Michael Crenshaw 