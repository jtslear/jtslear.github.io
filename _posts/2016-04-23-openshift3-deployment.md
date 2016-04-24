---
layout: dark-post
title: Openshift v3 Deployment Strategy
type: post
published: true
status: publish
categories: []
tags:
  - docker
  - openshift
---
[Openshift version 3][openshift], or Openshift Origin, has been a fantastic [Platform as a
Service][paas] to play with.  The team I work on have come up with an excellent and
[not so obvious documented method][deployments], but certainly legit procedure of deploying
applications.

Openshift has a [model][build] that they've been concentrating on, which as a developer,
you ship it the location of your code and it'll build a working docker
image for you. Whether it be just your application code, or a Dockerfile inside
of the repo, both are good options and as long as openshift has an STI builder
for your code, you're good to go.

However, at our organization we've already got docker in some form of production state.
We also have a plethora of Jenkins boxes that run various things to
spit out the artifacts that run our applications.  Adding another build layer is
a bit unnecessary in our use case and would probably cause more headaches at
this moment in time.  So we've determined to utilize a model where a service
team creates a usable docker image, and we'll help throw it onto openshift.

The strategy is essentially the following:

1. A developer pushes their code
1. Our enormous Jenkins infrastructure kicks off the various pipelines (for
  testing and such)
1. An artifact is created a stored
1. A docker build job is completed
1. A push job shoves that docker image into the openshift internal registry
1. Which in turn completes a deploy

To utilize this strategy, one would need to configure the following:

* The built-in registry needs to be [exposed][exposed]
* An application configuration will need to have been pre-populated
  * The deployment configuration will point to the internal registry for the
    docker image
  * The deployment configuration should also have the appropriate triggers
* Any jobs that push to the registry need appropriate [access][access]

This strategy works by utilizing the [hooks][hooks] that openshift have built around the
docker registry.  These are the same hooks that are used if openshift was the
builder of the docker image.  When a user kicks off a deploy, their pre-built
docker image is simply pushed up to the exposed registry.  After the image is
pushed, the trigger is kicked forcing openshift to complete a deploy.

The most difficult part of this strategy is probably the configuration, which
we've got fully automated.  When we
spin up a Jenkins box, our tooling will look inside of openshift for the service
account that was setup for access, grab those credentials and shove them into
jenkins.  The deployment configurations for applications are also automated.
When we spin up a new application, it'll again, take a peak into openshift to
figure out where the registry endpoint is.  And it'll pre-populate the image
configuration for the container for us.  Despite an image not existing
beforehand in the registry, openshift will check the naming scheme of the
project and app name allowing one to push the docker image just fine.  The very
first deploy will probably always fail since the image will not yet exist.  You
certainly cannot push until the deployment configuration is in place, otherwise
the docker registry will choke.  Which is good, it'll help keep the trash out of
that registry.

The bonus we get out of this:

* Images are built outside of the realm of openshift.  This allows our developers
to easily grab the docker image and spin it up locally if needed for any
additional troubleshooting or testing.
* This easily integrates with our existing pipelines.  We have jenkins all over
  the place.  Hudson has literally thrown up in our environment.  It's
disgusting and I'd rather avoid touching what is in place as much as possible.
With this strategy, it's one additional job that takes what was already built
and sends it to a new location.
* Just as the purpose of openshift, it helps keep devs from having to know some
  of the inner bits of openshift.  Create a working container, ship it to
openshift and it'll just work.

The downsides to this:

* Images are built outside of the realm of openshift.  Our devs do not have
  direct access to openshift.  This is a challenge since it makes it hard for
devs to troubleshoot.  The final image needs to adhere to the security features
that openshift adds, which can be difficult to get over initially. Especially
considering when running the docker image locally you aren't going to be subject
to the same limitations.  However, openshift has great [documentation][image-constraints] around this.
* We aren't utilizing the features openshift has built around the use case of
  building source code.  The project appears to be taking the building portion and
expanding upon the available features in the future.  We'll simply be missing
out on whatever those features are.

The main push for us to utilize this strategy is to help integrate our existing
stuff into openshift.  Our environment is large and has been around for many
many years.  Switching to openshift would not be feasible if every service team
needed to rewrite their pipelines from the scratch to work with this platform.
Another reason we chose this route was due to the nature of which our devs can't
touch openshift.  Which is lame, but such is life in a large organization with
contractual obligations.

Plus 1 to the openshift team.  It's been a pleasure to work on and integrate
into our environment.

[access]: https://docs.openshift.org/latest/dev_guide/service_accounts.html "Service Accounts"
[build]: https://docs.openshift.org/latest/dev_guide/builds.html "Builds"
[deployments]: https://docs.openshift.org/latest/dev_guide/deployments.html "Deployments"
[exposed]: https://docs.openshift.org/latest/install_config/install/docker_registry.html#exposing-the-registry "Exposing the Docker Registry"
[hooks]: https://docs.openshift.org/latest/architecture/infrastructure_components/image_registry.html#integrated-openshift-registry "Openshift Registry"
[image-constraints]: https://docs.openshift.org/latest/creating_images/guidelines.html#openshift-specific-guidelines "Openshift Image Guidelines"
[openshift]: https://www.openshift.org/ "Openshift"
[paas]: https://en.wikipedia.org/wiki/Platform_as_a_service "Platform as a Service"
