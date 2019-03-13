### iOS CI Branching Model
------
![Branch Model Diagram](https://raw.githubusercontent.com/kcatigbe/holding_space/master/ci-branching.png)

- `master` is our mainline branch
  - This branch should always be buildable 
  - Last known/good state should be the same as what is live in production
- `develop` is our integration branch
  - All active feature work is merged here
  - Should always be _"ahead"_ of `master`
  - Should always buildable
- `feature/` branches are larger branches of code for single feature work
  - Always targeted to `develop`
- `bugfix/` branches are branches off of `develop`
  - Smaller, focused branches to fix bugs in `develop`
  - Always targeted to `develop`
  - Fixes done here may be `cherry-picked` to a `release/` branch if applicable.
- `release/` branches are _usually_ branched off `develop` to:
  - Prepare RC builds for release
  - Ensure focused work on an upcoming release
  - Once the release is complete (prod, dev, qa, pm sign-off) this branch gets merged to `master` and `tag`ged
- `hotfix/` branches are always branched from a `release` branch
  - Used to correct issues in a release candidate
  - Always targeted back to the corresponding `release` branch
  - Fixes done here should be `cherry-picked` to `develop` if applicable
- `tags` are used as the marker for **submission-ready** code
  - App-store builds will be generated from this branch
  - These tags will have all sign-offs as necessary per our CI process

> Note:  Branch targeting rules will be validated upon PR creation with Danger, so that for example we don't mistakenly target a `hotfix` branch to an incorrect base branch.  Similarly, `bugfix` and `feature` branches will be validated  as well.

### iOS Versioning Scheme

------

We will use [Semantic Versioning](http://semver.org) for application version.  

> Note:  Where applicable, we should append the `build #` to the semantic version, i.e. `5.0.5.128` so that we can easily identify a particular build under test.

### Build Jobs / Distribution Strategy

---

For the purpose of active development and quality assurance testing, the following table of jobs defines the code branches we'll build, and the conditions when they are built.

##### Triggered Jobs

|  Branch   |                    Condition                    |                          Artifacts                           |                   Destination                    |     Type      |
| :-------: | :---------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------: | :-----------: |
| `develop` |                After every merge                |   .ipa; Unit Test Report; Code Coverage Report; Build Logs   | Hockey (UAT); Published to Jenkins; BAT Test Rig |    `alpha`    |
| `feature` |               PR Opened / Updated               | Unit Test Report; Code Coverage Report; Build Logs; PR Report |      Published to Jenkins; Published to PR       | `pr-pipeline` |
| `release` | New `release` branch created; After every merge |   .ipa; Unit Test Report; Code Coverage Report; Build Logs   |            Hockey (Beta); TestFlight             |    `beta`     |
| `bugfix`  |               PR Opened / Updated               | Unit Test Report; Code Coverage Report; Build Logs; PR Report |      Published to Jenkins; Published to PR       | `pr-pipeline` |
| `hotfix`  |               PR Opened / Updated               | Unit Test Report; Code Coverage Report; Build Logs; PR Report |      Published to Jenkins; Published to PR       | `pr-pipeline` |
|   `tag`   |                  Tag Creation                   | Unit Test Report; Code Coverage Report; Build Logs; PR Report |         Hockey (Production); TestFlight          |    `prod`     |



##### Other Jobs

For ease of use, there will be additional build jobs setup to facilitate one-off builds, etc.

**Naming TBD**

##### Legacy Jobs

With the addition of these new jobs, we'll be replacing several legacy jobs / distributions.  The following table outlines these.

|   New   |               Old                |  New Hockey App  |
| :-----: | :------------------------------: | :--------------: |
| `alpha` | `iOS_Next` / `iOS_Next_Upcoming` | `iOS_Next_Alpha` |
| `beta`  |               N/A                | `iOS_Next_Beta`  |
| `prod`  |               N/A                | `iOS_Next_Prod`  |

