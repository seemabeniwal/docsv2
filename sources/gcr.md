page_title: Google Container registry integration | Documentation | Shippable
page_description: Build and Deploy Docker applications to Google Cloud Platform with Shippable-GCR integration
page_keywords: shippable, Docker, Container, Google, Google container registry, Google Cloud Platform, Google Compute Engine, Google Container Engine

# Google Container Registry

The Google Container Registry (GCR) provides secure, private Docker image storage on the Google Cloud Platform. GCR has additional benefits such as fine grained access control, server-side encryption of images, and super fast deployment to Google Container Engine and Google Compute Engine.

To read more about GCR, you can read [Google's documentation](https://cloud.google.com/tools/container-registry) or [announcement blog](http://googlecloudplatform.blogspot.com/2015/01/secure-hosting-of-private-Docker-repositories-in-Google-Cloud-Platform.html).

## Setting up GCR integration on Shippable

If you want to interact with GCR in any part of your build workflow for your Shippable project, such as using your private images for your builds or pushing images to your repository, you need to connect your GCR project to your Shippable account.

Follow the following steps to set up GCR integration.

#### Create a Project in Google Dev Console

To use Shippable with GCR, you will need a project created using the Google Developers Console (GDC). According to [Google's documentation](https://developers.google.com/console/help/new/#managingprojects) - A project is a collection of settings, credentials, and metadata about the application or applications you're working on that make use of Google Developer APIs and Google Cloud resources.

If you already have a project you want to use, skip to the next step - *Setting up OAuth for your GDC project*.

To create a project -

- Sign in to the [Google Developers Console](https://console.developers.google.com/)
- Click on 'Create Project'
- Enter a name and project ID or accept the defaults.
- Click 'Create'


#### Setting up OAuth for your GDC project

- On the [Google Developers Console](https://console.developers.google.com/), select the project you just created
- In the sidebar on the left, expand 'APIs & auth' and select 'Credentials'
- Click 'Create new Client ID' and select 'Service Account' in the pop-up window
- Click on 'Create Client ID'. A dialog box appears. To proceed, click 'Okay, got it'
- Your new Public/Private key pair is generated and downloaded to your machine. Please store this carefully since you will not be able to retrieve this from your GDC account. You will need this key pair to set up GCR integration on Shippable.


#### Set up GCR Integration

- Login to Shippable
- Click on 'Settings' in the top navbar
- On the Account Settings page, click on 'Integrations', just below the Account Settings text.
- From the options presented, click on GCR
- Enter an Integration name, which will be used to refer to this integration on Shippable
- Copy the key pair generated during the last step and paste into the jsonKey field.
- Click on 'Save'

At this point, you have set up GCR integration at an account level. To push and pull from GCR, you will also need to enable repo-level access as described in the scenarios below.

-------

## Pull custom image from GCR

Shippable allows you to pull a custom image from GCR to run your builds on.

To enable GCR integration for the repository for which you want to pull a custom image -

- Go to your repository page on Shippable and click on 'Integrations' on the right sidebar
- Click on the dropdown for 'Hub' and select the Integration name you want to use.
- On the repo page, go to 'Settings'. Choose the following to pull an image from GCR -
    - Pull image from : gcr.io/gcr_project_id/image_name


-------

## Push to GCR

Shippable allows you to push an image to GCR after a successful build.

To enable GCR integration for the repository for which you want to push to GCR -

- Go to your repository page on Shippable and click on 'Integrations' on the right sidebar
- Click on the dropdown for 'Hub' and select the Integration name you want to use.
- On the repo page, go to 'Settings'. Choose the following to push to GCR -
    - Push Build : Yes
    - Push image to : gcr.io/gcr_project_id/image_name

*Note* - Pushing to GCR needs a credit card to be associated with your Google Cloud Platform account. If you do not have that configured in your [Google Dev Console](https://console.developers.google.com), the push will fail since GCR will reject it.

-------

## Build image from Dockerfile

You can run your build in a custom docker container by building a Docker image from a Dockerfile. Aside from providing a custom environment for your build, this image created can be pushed to your GDC account, for later use in your deployment step.

There are 2 ways to set up Docker build with Shippable - pre CI or post CI.

Pre CI workflow is:

- Build the image using Dockerfile at the root of your repo
- Pull code from GitHub/Bitbucket and test code in the container
- Push container to GCR

Post CI workflow is:

- Pull image specified from GCR (default is shippable/minv2)
- Pull code from GitHub/Bitbucket and test in container
- If CI passes, build container from Dockerfile at the root of the repo
- Push container to GCR

To use these workflows, your app must be "dockerized". Details on this can be found in Docker's official documentation [Docker's official documentation](https://docs.dockerhub.com). You can also look at our [Docker build sample app](https://github.com/cadbot/dockerized-nodejs).

### Pre CI Dockerbuild

- Enable the repository on Shippable
- Make sure that GCR integration is set up on Shippable and that GCR is enabled for your repo
- On the repo page, go to 'Settings'. Choose the following -
    - Docker Build : ON
    - CI order : Pre-CI
    - Push Build : Yes if you want to push to GCR, No if you don't want to push to GCR
    - Image name : gcr.io/(project id on GDC)/(image name)
      We need an image name for the image we build from your Dockerfile, even if you choose not to push to GCR
    - Source Location : (source code location where tests will be run)
- Make sure the Dockerfile for the image you want to build is at the root of your repo
- Trigger a manual or webhook build
- After the build is complete, make sure your GDC account shows the image you just pushed. The image should be tagged with the build number on Shippable.

### Post CI Dockerbuild

- Enable the repository on Shippable
- Make sure that GCR integration is set up on Shippable and that GCR is enabled for your repo
- On the repo page, go to 'Settings'. Choose the following -
    - Docker Build : ON
    - Dockerbuild order : Post-CI
    - Push Build : Yes if you want to push to GCR, No if you don't want to push to GCR
    - Push image to : gcr.io/(project id on GDC)/(image name)
    - Pull image from : Since your Dockerbuild is happening post CI, enter the image you want to use for CI
- Make sure the Dockerfile for the image you want to build is at the root of your repo
- Trigger a manual or webhook build
- After the build is complete, make sure your GDC account shows the image you just pushed. The image should be tagged with the build number on Shippable.

### Copying artifacts to prod image

If you are following the post-CI Dockerbuild workflow and  want to copy some build artifacts to your prod image, you should-

1. Create a shippable/buildoutput directory in your shippable.yml

```bash
before_script:
    - mkdir -p shippable/buildoutput
```

2. In the after_script section, copy whatever you want to this directory

```bash
  after_script:
    - cp -r (your artifacts) ./shippable/buildoutput
```

3. In your Dockerfile, you can now use ADD to put the artifacts wherever you want in your prod image

```bash
  ADD ./buildoutput/(artifacts file) (target)
```

And that's it. Any artifacts you need will be available in your prod image.
