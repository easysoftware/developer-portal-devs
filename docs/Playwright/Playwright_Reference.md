# Playwright Reference

In this part we will go through the topic of how to succesfully work with Playwright in EasySoftware. 

To create Playwright test we are using original documentation: https://playwright.dev/. 

Following parts explain description, quick start, connection to GitlabCI and how to get detailed information about why did the test fail. 

---

## Structure

We create all Playwright tests in “playwright” folder in core. 

Here is the structure of folders: 

1. fixtures

- This folder contains JSON files representing entities of our system. Both existing once and the new ones. 

2. pages 

- For creating playwright test we use “Page pattern”. In short, the logic of finding elements in a page is enclosed in files with file name extension .page. These are imported into the test and their methods are called. 

3. tests 

- This folder contains purely Playwright tests with defined expectations. 

---

## Quick start 

1. For testing, we use database from https://cypresssource-minor.easysoftware.com/ .
**It is forbidden to edit it without permission from QA!** We just simply dump the database instead and import dump into our locale database. 

2. In the default setting Playwright tests are executed in accordance with `yarn playwright test`.
We start our local server with a testing database and execute tests through the command mentioned earlier.
If we would like to execute Playwright in accordance with a deployment (it must contain testing database), we can use parameter BASE_URL. 

3. If our tests fail, Playwright gives us a hint to use `yarn playwright shot-trace command`, to which we add newly generated .zip file.
This local behavior can be used in the same way for tests failed in GitlabCI as well.
Zip files are saved the exact same way there, so you may download them and execute as well – more info in section “Connection to GitlabCI - internal usage”

## Connection to GitlabCI – internal usage 

To execute a playwright test in GitlabCI we use docker image. For each branch, new docker image is being created, which is saved in Gitlab registers: https://git.easy.cz/devel/devel/container_registry.

The name of the image contains the name of the branch for which it was created. 

Each failed pipeline provides an option to download detailed back-trace zip file in 'Job artifacts' section. You can execute this zip file after downloading it with `yarn playwright show-trace` command. 