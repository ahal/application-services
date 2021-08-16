# Bundling experiments with the application

* Status: rejected
* Deciders: teshaq, travis, k88hudson, jaredlockhart
* Date: 2021-08-16

Technical Story: https://mozilla-hub.atlassian.net/browse/SDK-323

## Context and Problem Statement

As an experimenter, I would like to run experiments early on a user's first run of the application. However, the experiment data is only available on the second run. We would like to have that experiment data available before the user's first run.
For more information: https://docs.google.com/document/d/1Qw36_7G6XyHvJZdM-Hxh4nqYZyCsYajG0L5mO33Yd5M/edit

## Decision Drivers

* Availability of experiments early on the first run
* No impact on experimentation data analysis
* Flexibility in creating experiments
* Ability to quickly disable experiments
* Simplicity of releases

## Considered Options

* **(A) Do Nothing**
    * Keep everything the way it is, preventing us from experimenting on users early on their first run
* **(B) Bundle Experiment data with app on release**
    * On release, have an `initial_experiments.json` that defines the experiments that will be applied early on the first run
    * Later on the first run, the client would retrieve the actual experiment data from remote-settings and overwrite the bundled data
* **(C) Retrieve Experiment data on first run, and deal with delay**
    * We can retrieve the experiment data on the first run, experiment data however will not be available until after a short delay (network I/O + some disk I/O)

## Decision Outcome

The proposed option: **(B) Bundle Experiment data with app on release** was **rejected**

The option was rejected mainly due to difficulty in disabling experiments and pausing enrollments. This can create a negative user experience as it prevents us from disabling any problematic experiments. Additionally, it ties experiment creation with application release cycles.

Moving forward, we are discussing **(C) Retrieve Experiment data on first run, and deal with delay** to address the first product experimentation requirement, which involves the ability to run experiments on a user's first run, but not necessarily early (The first experiments don't happen to involve onboarding)

### Positive Consequences

* Since the experiments are bundled with the app, they will always be available on first run, without having to wait on network I/O. This allows us to create experiments early on the user's first run (for example, onboarding experiments)

### Negative Consequences

* Inability to disable experiments without issuing a dot-release. The hinders our ability to quickly rollback any experiments that are causing problems. As a consequence, this will slow us down in that we would not be able to launch experiments and iterate quickly if it goes wrong
* Inability to pause enrollments remotely. We currently have a mechanism to pause enrollments directly on experimenter, however, since the experiment data from remote-settings is not applied until the second run, users would still get enrolled in experiments (whose enrollment has been paused) on their first run.
* Experiment creation will be tied to release cycles, any experiments that would like to target users on their first run would need to be created before a release is made. This removes flexibility in experiment creation and creates limitation on the timeline experiments can be created to be tied with release cycles (or would force us to create dot-releases for experiments)

## Pros and Cons of the Options

### Do nothing

* Good, because it keeps the flexibility in experiment creation
* Good, because disabling experiments can still done remotely for all experiments
* Bad, because it doesn't address the main problem of exposing experiments to user on their first run

### Bundle Experiment data with app on release
* Good, because it allows us to run experiments early on a user's first run
* Good, because it prevents us from having to wait for experiments, especially if a user has a slow network connection
* Bad, because it ties experiment creation with release cycles
* Bad, because it prevents us from disabling problematic first-run experiments without a dot release
* Bad, because it prevents us from pausing enrollment on first-run experiments without a dot release

### Retrieve Experiment data on first run, and deal with delay 

* Good, because it enables us to retrieve experiments for users on their first run
* Good, because it keeps the flexibility in experiment creation
* Good, because disabling experiments can still done remotely for all experiments
* Bad, because experiments may not be ready early on the user's experience

## Links

* [RFC for bundling into iOS and Fenix](https://docs.google.com/document/d/1Qw36_7G6XyHvJZdM-Hxh4nqYZyCsYajG0L5mO33Yd5M/edit#heading=h.b1n8hquqkety)
