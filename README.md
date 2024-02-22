# TMC Flux Patching

This repo shows an example of using TMC installed flux to patch itself. There are a number of use cases where this might come into play but here are a few.

* adding proxy settings to the helm or source controller(example show in this repo)
* adding settings to the kustomize controller for mmutli-tenancy
* any other settings that are not configurable out of the box on TMC provided flux.


## Caution

When using this to modify the source controller take extra care in testing. If a setting in changed that causes the source controller to go into a crashloop then you will need to go manually modify the package to remove any overlay settings that were added that broke the controller. this is becuase we are using the source controller to pull down our changes so if it breaks we cannot fix it through gitops.


## How it works

Flux has the ability to update existing resource using a merge patch, you can find it in the docs [here](https://fluxcd.io/flux/faq/#how-to-patch-coredns-and-other-pre-installed-addons). TMC installs Flux using Carvel packages, so we can use this patch behavior to add an overlay to the TMC provided Flux carvel packages. Carvel allows for adding overlays to packages using k8s secrets and then annotating the package with the secret name. The docs on this can be found [here](https://carvel.dev/kapp-controller/docs/v0.32.0/package-install-extensions/). We can combine these two features in order to use flux to modify itself and add any modifications we want to the package.


## Usage

This repo has an example of the overlays and flux partial resources to do the patching. The two examples given are in the `gitops` directory. these will modify both the helm controller and source controller and add `HTTP_PROXY` settings. This is not a complete example of setting up gitops with TMC, if you want an example of that head over [here](https://github.com/warroyo/flux-tmc-multitenant).


1. enable TMC CD at the cluster group level
2. enable TCM helm releases at the cluster group level
3. create a git repo in TMC at the cluster group level and point it to the git repo that holds these configs.
4. create a cluster group level kustomization that references the git repo and the path to where these files are stored.
5. check the `pkgi` resources on the cluster for helm and source controller and see that the annotation was injected.
6. check the pods yaml to see that the env vars are set.