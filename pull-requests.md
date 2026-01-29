# Pull requests (PR)

## Aims

  - Keep PRs small and/or easy for reviewers to understand.
  - Keep PRs focused on one core issue.
  - Get changes that might affect other features being developed onto
    main in a timely fashion.
  - Avoid blocking where one PR can’t be merged because it requires features that are being
    developed on another branch that hasn’t been merged yet.
  - Improve situational awareness of each other’s work and encourage early feedback.

## Small features or changes: single PR

Small features or changes should be proposed using a single PR, which details the
problem being addressed or the enhancement made, what is being done and why. A good rule of thumb is
that if the work for the PR has only taken a couple of sessions or days, then it’s best
just to propose via a single PR.

## Large features or changes: single PR

Sometimes you might want to propose large changes that you have developed quickly,
for example, using AI. In this case, it is fine and preferred to use a single PR. However,
you should be considerate of the impact on reviewers. Please consider:

  - Are your changes adequately explained in the PR description?
  - Are the changes expected by the development team? If you are an external
    contributor, consider raising an issue before the PR to consult maintainers.
  - For AI-assisted contributions, a good guide is that the human effort involved in
    opening the PR should exceed the expected effort of maintainers to review the PR.
  - How else could you help reviewers digest the PR?

***If the work is taking weeks, and the visibility of the changes you have in the 
pipeline could be important for other developers, please use a
[multiple PR](#large-features-or-changes-multiple-prs) approach.***

## Large features or changes: multiple PRs

Large features developed over multiple weeks should usually use multiple PRs. It
makes it easier for reviewers to digest the changes, and improves visibility of what’s
in the pipeline. A multiple PR approach typically takes the following form:

  - Before starting coding, create an issue describing the proposed changes to provide
    visibility to other developers and provide a place for early discussion on the
    proposed changes.
  - Propose your changes incrementally across multiple PRs, with a single PR chunk
    containing roughly no more than a week of coding effort. Use your judgment and chunk
    the work in a logical fashion that fits your circumstances. The main thing is to
    avoid dumping large PRs on other developers when they could have been provided the
    changes in incremental chunks.
  - Each PR should reference the original issue, and the last PR should close the 
    issue. 
  - If the new code in a chunk isn’t fully complete, hide it behind a feature flag,
    an if-statement that prevents it from running unless a flag explicitly allows it
    to do so. Add your new flag to feature_flag.yaml with a default value of False.
  - The last PR for a new feature, once it is fully tested, should remove all the
    feature-flag if-statements and the feature flag from the YAML file. ***Note, more
    docs on setting up feature flags to be added once the first one is implemented.***

