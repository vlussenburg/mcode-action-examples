# Mayhem for Code: Example CI Integration

[![Mayhem for Code](https://drive.google.com/uc?export=view&id=1JXEbfCDMMwwnDaOgs5-XlPWQwZR93fv4)](https://app.mayhem.security/)

A GitHub Action walk through for using Mayhem for Code to check for reliability, performance, and security issues in your application binary (packaged as a containerized [Docker](https://docs.docker.com/get-started/overview/) image) as a part of a CI pipeline.

Visit the [Mayhem for Code GitHub Action](https://github.com/ForAllSecure/mcode-action/) to get more details on integrating Mayhem into your CI pipeline!

## Example GitHub Actions Integration

In this example, we've provided two targets that will be built, fuzzed, and fixed/patched to showcase a multi-target Mayhem for Code Action workflow within a CI pipeline: [Lighttpd](https://www.lighttpd.net/) version `1.4.15` and one of our `mayhem-example` targets, [c-base-executable](https://github.com/ForAllSecure/mayhem-examples/tree/main/c/base-executable/c-base-executable).

In particular, `lighttpd` version `1.4.15` was found to have vulnerabilities in the past, which were fixed in subsequent updates such as `1.4.52`. In tandem, our `c-base-executable` target has a bug that performs an `abort()` once a test case containing the string `bug` is input to the program, which we'll also fix in this example. Ultimately, we'll be using Mayhem in a CI pipeline to simulate a typical developer workflow in which we build and fuzz targets within a workflow to find and prove vulnerabilities exist within a target application, and then submit a subsequent PR to fix these vulnerabilities, which are confirmed via Mayhem's regression testing (crashing test cases of previous Mayhem runs for a target application are re-used again in future Mayhem runs of the same target) and accompanied by new behavior testing for the updated target application.

We have two branches in this repository: `main` and `vulnerable`.

> When executing a new workflow/pipeline using the Mayhem for Code GitHub Action, the corresponding `lighttpd` and `c-base-executable` targets will be built within a Docker image, which is pushed to the GitHub Container Registry, and ingested by Mayhem to fuzz the containerized targets. This is done using a [multi-stage Docker image build](https://docs.docker.com/build/building/multi-stage/).

The `main` branch contains the following targets:

* **lighttpd 1.4.52**:
    * [lighttpd 1.4.52 Dockerfile](https://github.com/ForAllSecure/mcode-action-examples/blob/main/mayhem/Dockerfile): Build instructions for settings up a containerized `lighttpd 1.4.52` application.
    * [lighttpd 1.4.52 Mayhemfile](https://github.com/ForAllSecure/mcode-action-examples/blob/main/mayhem/Mayhemfile.lighttpd): Configuration options for the resulting `lighttpd 1.4.52` CI pipeline Mayhem run.
* **c-base-executable**:
    * [c-base-executable Dockerfile](https://github.com/ForAllSecure/mcode-action-examples/blob/main/mayhem/Dockerfile): Build instructions for setting up a containerized `c-base-executable` application.
    * [c-base-executable Mayhemfile](https://github.com/ForAllSecure/mcode-action-examples/blob/main/mayhem/Mayhemfile.mayhemit): Configuration options for the resulting `c-base-executable` CI pipeline Mayhem run.

The `vulnerable` branch contains the following vulnerable targets:

* **(vulnerable) lighttpd 1.4.15**:
    * [lighttpd 1.4.15 Dockerfile](https://github.com/ForAllSecure/mcode-action-examples/blob/vulnerable/mayhem/Dockerfile): Build instructions for setting up a containerized `lighttpd 1.4.15` application.
    * [lighttpd 1.4.15 Mayhemfile](https://github.com/ForAllSecure/mcode-action-examples/blob/vulnerable/mayhem/Mayhemfile.lighttpd): Configuration options for the resulting `lighttpd 1.4.15` CI pipeline Mayhem run.
* **(vulnerable) c-base-executable**:
    * [c-base-executable Dockerfile](https://github.com/ForAllSecure/mcode-action-examples/blob/vulnerable/mayhem/Dockerfile): Build instructions for setting up a containerized (and vulnerable) `c-base-executable` application.
    * [c-base-executable Mayhemfile](https://github.com/ForAllSecure/mcode-action-examples/blob/vulnerable/mayhem/Mayhemfile.mayhemit): Configuration options for the resulting (vulnerable) `c-base-executable` CI pipeline Mayhem run.

## Getting Started

1. Fork this repository and create a Mayhem account to copy and paste your account token to GitHub Secrets for your repository:

    1. Navigate to [app.mayhem.security](https://app.mayhem.security/) to register an account.

    2. After logging in, on the bottom left:
       1. Click your profile icon.
       2. Click Account Settings.
       3. Switch to *API Tokens* section to access your account API token.

    3. Copy and paste your Mayhem token to your forked repo's [GitHub Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-an-organization). You will need the following secrets configured for your repository:

        * `MAYHEM_TOKEN`: Your Mayhem account API token.

2. On the `main` branch, navigate to your GitHub repository `Actions` tab and execute a CI pipeline for the `main` branch (assuming this is not already done automatically). This will build and push the `lighttpd 1.4.52` and `c-base-executable` containerized applications to the GitHub Container Registry and use Mayhem to fuzz the resulting Docker image. In addition, since no vulnerable versions are present on the mainline no issues will be reported in the `Security` tab.

    > **Note:** You may be required to set your package visibility settings to `Public` to give Mayhem permissions to ingest your Docker image from the GitHub Container Registry. Click on your package in the right-hand pane of your GitHub repository and go to *Package Settings*. Then, scroll down to *Package Visibility* and set the package to `Public`.

3. Now, switch to the `vulnerable` branch. Create a pull request and set the PR to merge to `main` (**for your forked repo**). The Mayhem for Code GitHub Action will automatically begin building and pushing the `(vulnerable) lighttpd 1.4.15` and `(vulnerable) c-base-executable` containerized applications to the GitHub Container Registry and use Mayhem to perform both regression testing and behavior testing for the updated target applications. Results can then be found in the PR or on the Mayhem server itself with more details about each specific run. Results can be found in the `Security` tab or on the Mayhem instance itself with more details about the specific run.

Congrats! You just integrated Mayhem in a multi-target CI pipeline for the `lighttpd` and `c-base-executable` applications! Extrapolating from this, you should now be able to incorporate the same steps to integrate Mayhem into your own CI pipeline for your custom code.

## About Us

ForAllSecure was founded with the mission to make the world’s critical software safe. The company has been applying its patented technology from over a decade of CMU research to solving the difficult challenge of making software safer. ForAllSecure has partnered with Fortune 1000 companies in aerospace, automotive and high-tech industries, as well as the US Department of Defense to integrate Mayhem into software development cycles for continuous security. Profitable and revenue-funded, the company is scaling rapidly.

* [https://mayhem.security/](https://mayhem.security/)
* [https://mayhem.security/code-security](https://mayhem.security/code-security)
