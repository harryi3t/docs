main_section: CI
sub_section: Advanced config
page_title: Running matrix builds
page_description: How to use matrix builds in your Build Configuration
page_keywords: getting started, questions, documentation, shippable, config, yml, multiple languages, versions

# Matrix builds

In most cases, you want to trigger one build for each commit/pull request to your repository. However, there are times when you might want to trigger multiple builds for a single code change.

For example, you might want to test against multiple versions of Ruby, multiple aspect ratios for your Selenium tests, or multiple environment variables.

This scenario is handled by our **Matrix Build** feature, where certain configurations of your yml file lead to multiple builds being triggered per code commit or pull request. These configurations include -

-  specifying more than one language version
-  specifying more than one variable in the `env` section
-  specifying multiple gemfiles for ruby

##Basic config

To configure matrix builds, you just need to specify multiple language runtimes, and/or multiple environment variables, and/or multiple gemsets in case of Ruby.

```
rvm:
  - 1.9.2
  - 1.9.3
  - rbx
  - jruby

gemfile:
  - gemfiles/Gemfile.rails-2.3.x
  - gemfiles/Gemfile.rails-3.0.x

env:
  - ISOLATED=true
  - ISOLATED=false
```

The configuration above will generate a 16 build matrix!

Even though each build in the matrix is a separate build, the Shippable UI also shows an aggregated view in order to organize all builds into a single build number. For example, if the yml snippet above triggers build number 13, with each individual build in the matrix numbered 13.1 through 13.16.

<img src="../../images/ci/matrix-builds.png" alt="Build matrix with Shippable">


##Advanced config

###including/excluding specific jobs

The `matrix` tag lets you refine the build matrix by including/excluding specific combinations, or allowing specific jobs to fail without affecting overall build status.

You can exclude a specific job from a matrix by configuring your yml with an `exclude` tag:

```
rvm:
  - 1.9.2
  - 1.9.3
  - 2.0.0

gemfile:
  - gemfiles/Gemfile.rails-2.3.x
  - gemfiles/Gemfile.rails-3.0.x

matrix:
  exclude:
    - rvm: 1.9.2
      gemfiles/Gemfile.rails-3.0.x
```

The snippet above excludes the job for the combination of `runtime 1.9.2` and `gemfiles/Gemfile.rails-3.0.x` from the build matrix.

You can also include only specific combinations in a build matrix by using the `include` tag as shown below:

```
rvm:
  - 1.9.2
  - 1.9.3
  - rbx
  - jruby

gemfile:
  - gemfiles/Gemfile.rails-2.3.x
  - gemfiles/Gemfile.rails-3.0.x

env:
  - ISOLATED=true
  - ISOLATED=false

matrix:
  include:
    - rvm: 2.0.0
      gemfile: gemfiles/Gemfile.rails-3.0.x
      env: ISOLATED=false
```

### allowing failures

Allowed failures are items in your build matrix that are allowed to fail without causing the entire build to be shown as failed. You can define allowed failures in the build matrix as follows:

```
matrix:
  allow_failures:
    - rvm: 1.9.3
```
