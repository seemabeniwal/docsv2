page_title: Shippable CI User Guide | Documentation | Shippable
page_description: This section describes various concepts pertaining to Shippable CI
page_keywords: getting started, questions, documentation, shippable, config, yml

# Shippable CI User Guide

## Connecting your GitHub and Bitbucket accounts

If you want to use Shippable to build both GitHub and Bitbucket
repositories, you can connect the two accounts in order to get a
consolidated view of all your projects in one Shippable account.

To connect your accounts, sign in with the GitHub/BitBucket account that
you want to be your primary account. You will go through the auth flow
and land on your Shippable dashboard.

If you signed in with GitHub, you can click on the BitBucker icon on the
top right of your dashboard and then follow the auth flow for Bitbucket.

If you originally signed up with Bitbucket, you can click on the GitHub
icons on the top right of your dashboard and then follow the auth flow
for GitHub.

Once your accounts are both connected to Shippable, you should see a
consolidated list of orgs and projects in your account. You can sign in
with either of your credentials after this point.

* * * * *

## Permissions

We closely mimic GitHub and Bitbucket permissions for Orgs and projects.
Anyone who has access to an organization or repository in
GitHub/Bitbucket will also have access to build information and/or
repository and build actions on Shippable. This happens automatically,
so if you enable a repository in your Org on Shippable and another team
member signs in, they will see the enabled repository and build history
already present in their account.

We support 2 roles -

**Owner :** Owners have all privileges for an Org or Project. They can
enable, run and delete projects, upgrade pricing plans, and view/run,
cancel, and delete builds.

**Collaborator :** Collaborators can enable projects and view/run builds
on Shippable. They cannot delete enabled projects or upgrade pricing
plans.

* * * * *

## Minions

Minions are Docker based containers that run your builds and tests. You
can think of them as Build VMs. Each minion runs one build at a time, so
the more minions you have, the more concurrency you will get.

Minions are automatically provisioned whenever a build is triggered and
it will get deleted after the build finishes execution. We will
automatically add additional minions, as appropriate, based on your
subscription plan.

Each minion starts from a base image and can be customized by specifying
`before_install` scripts in the YML file. A minion can be configured to
run any package, library, or service that your application requires.
There are some preinstalled tools and services that you can use to
customize your minions even further.

### Operating systems

All our Linux minions start from a vanilla base image from the Docker
registry. In your YML file you can use the `build_image` option to
specify the image you want to use. We support all images as a starting
point for your minion. Minions can be further customized by using the
`before_install` and `install` tags in `shippable.yml` that is in the
root of your code repository.

* * * * *

## Pull requests

Shippable will integrate with github to show your pull request status on
CI. Whenever a pull request is opened for your repo, we will run the
build for the respective pull request and notify you about the status.
You can decide whether to merge the request or not, based on the status
shown. If you accept the pull request, Shippable will run one more build
for the merged repo and will send email notifications for the merged
repo. To rerun a pull request build, go to your project's page -\> Pull
Request tab and then click on the **Build this Pull Request** button.

* * * * *

## Build badge

Badges will display the status of your default branch. You can find the
build badges on the project's page. Click on the **Badge** button andopy the markdown to your README file to display the status of most
recent build on your Github or Bitbucket repo page.

* * * * *

## Build termination

Build will be forcefully terminated in the following scenarios:

-   If there has not been any log output or a command hangs for 10 minutes
-   If the build is still running after 60 minutes for Free Plans or 120 minutes for Paid Plans

When a build is forcefully terminated, the build status will indicate **timeout**.

* * * * *

## Skipping a build

Any changes to your source code will trigger a build automatically on
Shippable. So if you do not want to run build for a particular commit,
then add **[ci skip]** or **[skip ci]** to your commit message.

Our webhook processor will look for the string **[ci skip]** or **[skip
ci]** in the commit message and if it exists, then that particular
webhook build will not be executed.

## Build Images

Our default image, shippable/minv2, comes installed with popular versions of all
supported languages, tools and services.

However, you might prefer starting with a small image that only has
versions of your language installed. To help with this, we have open
sourced basic images for all supported languages. These images only come
with popular versions of a language and are NOT pre-installed with any
tools, addons or services.

Our build images are available on Docker Hub in the [shippableImages account]
(https://registry.hub.docker.com/repos/shippableimages/) . Dockerfiles
for these images are in our [GitHub repository](https://github.com/shippableImages).

You can choose language specific images for your project under **project settings**:

- Go to your Project page
- Click on the Settings tab
- Click on the dropdown against `Pull Image from` and choose an appropriate image

As mentioned above, our language specific images do not come with any
tools, addons, or services pre-installed. If you need pre-installed
tools, addons or services, then you should use shippable/minv2 image.

Check out our [language help page](languages.md) for language specific info about build images.

