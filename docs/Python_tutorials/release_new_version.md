# Deployments and code delivery
[Back to index](./_index.md)

#ToC:
1. [Release new version using a script](#release-new-version-using-release-script)
2. [Release new version manually](#release-new-version-manually)
3. [Continue releasing in gitlab](#release-in-gitlab)

## Release new version using release script
run `python x_release.py` from this file directory  
`python cli/x_release.py` from GIT root directory  
continue with [Release in Gitlab](#release-in-gitlab) section

## Release new version manually
### Create branch X.Y.Z

```shell
# Check out master
git fetch master
# Create a new branch release/X_Y_Z
git checkout -b release/0_8_3 origin/master
```

### Change changelog.md

In changelog.md file, we should add a line with version under `## [Unreleased]`.

```markdown
## [Unreleased]

## 0.8.3 - 2024-10-22
```

### Change version.txt

Update version number in `version.txt` file

### Release in Gitlab

1. Create a new merge request with name `chore(release): 0.8.3`. This name will be used as a name of the commit to
   master.
2. Merge the Merge request
3. Go to `Branches` -> `master`
4. On the top of the sources, there is a link to the latest pipeline on the `master` branch
5. Run the `integration_tests` job
6. Run the `build_production_docker_image` job

A new task in esko is automatically created.\
You can see the task id at the end of the pipeline log.\
For example this one: https://es.easyproject.com/issues/636174
