---
title: "Reproducible Rebuilding for SLSA 3 Bazel Builder Artifacts"  
author: "Noah Elzner (Google)"
is_guest_post: false
---

Today, we are announcing the SLSA3 Bazel Builder, a [Github Actions workflow](https://github.com/slsa-framework/slsa-github-generator/tree/main/internal/builders/bazel) 
that achieves SLSA Build Track Level 3, currently the highest SLSA level of trustworthiness, 
for the new SLSA v1.0 [provenance](https://slsa.dev/provenance/v1) generation standards. 
[Bazel](https://bazel.build/) is an open source build tool that generates [hermetic](https://bazel.build/basics/hermeticity) builds that 
integrate easily with your CI/CD pipeline and supports many programming languages and platforms. The SLSA3 Bazel Builder builds 
artifacts with provenance that attests to the artifact as well as its build process. An artifact's provenance can be verified using 
the [slsa-verifier](https://github.com/slsa-framework/slsa-verifier#readme), and using the Bazel Rebuilder allows for 
the highest level of hardening for SLSA Level 3 Builds. With the Bazel Rebuilder on 
the [slsa-github-generator repo](https://github.com/slsa-framework/slsa-github-generator), it is possible to rebuild 
the artifact locally and confirm that the artifact does align with the attested build process. 
This SLSA 3 Bazel Builder is the result of a collaboration with Google's Open Source Security Team (GOSST).

## Building and Rebuilding ##

Like the [container-based SLSA 3 builder](https://slsa.dev/blog/2023/06/slsa-github-worfklows-container-based), [The Bazel Builder](https://github.com/slsa-framework/slsa-github-generator/tree/main/internal/builders/bazel) 
is a SLSA3 Builder from [slsa-github-generator repo](https://github.com/slsa-framework/slsa-github-generator) that provides reproducibility, but for 
projects that do not use container-based ecosystems, through the Bazel ecosystem . 
It uses the [same secure design](https://github.com/slsa-framework/slsa-github-generator/blob/main/SPECIFICATIONS.md) as the other SLSA builders, 
making use of Github reusable workflows and its ephemeral Github Runners. 
The Bazel Builder verifies its identity using [Github's OIDC](https://github.com/slsa-framework/slsa-github-generator/blob/main/SPECIFICATIONS.md#workflow-identity-using-oidc-and-keyless-signing) 
integration with [Sigstore](https://www.sigstore.dev/), which allows for the generation of an unforgeable provenance that attests to the _artifact_ and _build process_ used. 
Generic Builders can attest to an artifact, but ecosystem specific builders, like the Bazel Builder, are able to attest to the entire build process 
since the build is completed on an ephemeral Github Runner.

We are able to achieve a consistent environment between the GitHub Actions Runner (where the build happens) and the user's local machine (where the rebuild happens) 
by using a Docker Image.  The consistent environment and the hermeticity of Bazel Builds allows for the possibility of reproducibility for our Bazel Builder's artifacts. 

The Bazel Rebuilder also has the ability to verify the provenance of an artifact before rebuilding. 
While the [slsa-verifier](https://github.com/slsa-framework/slsa-verifier) can verify the source Github repository, the builder's identity, and build process, 
the [Bazel Rebuilder](https://github.com/slsa-framework/slsa-github-generator/blob/main/internal/builders/bazel/README.md) allows users to confirm that 
the artifact accurately reflects the build steps used to create it. The Bazel Rebuilder compares the checksums of the original and rebuilt artifact. 
Matching checksums confirm that the original artifact was constructed exactly as detailed in the provenance.With the Bazel Rebuilder, 
users can limit external trust and verify their artifacts for themselves, ultimately transforming the paradigm of trust by 
allowing users to have complete certainty in what they built. 

## Try the Bazel Builder ##  

To get started using our Bazel Builder, follow our [detailed instructions](https://github.com/slsa-framework/slsa-github-generator/blob/main/internal/builders/bazel/README.md#getting-started). 
The Bazel Builder currently supports Bazel built projects on Github that use a Github workflow. Your feedback is appreciated and will help us make improvements to the builder. 
We invite you to put the Bazel Builder to test and share your thoughts and feedback by opening [an issue](https://github.com/slsa-framework/slsa-github-generator/issues) on GitHub. 
One improvement that we are already working on is supporting Github Bazel projects that deviate away from the hermetic capability of 
Bazel (through its usage of BUILD, WORKSPACE, and .bazelrc files). We are also hopeful that the innovative use of Docker Images could be adopted by 
other ecosystem builders to enable reproducible capabilities.

The Bazel Builder and its Reproducible Rebuilder work collaboratively to streamline the build and verification of the artifact through its SLSA3 provenance. 
SLSA L3 provenance for Bazel Builder and its Reproducible Rebuilder prevents supply chain attacks. 
In the future, we estimate that [GUAC](https://guac.sh/) can take advantage of Bazel's hermeticity to extract 
all dependencies needed to build the Bazel project when parsing the SLSA provenance to further strengthen supply chain security.
