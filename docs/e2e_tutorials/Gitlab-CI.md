# Connection to GitlabCI – internal usage

> In GitlabCI there is a playwright stage for running examples "automatically".

To execute playwright tests in GitlabCI we use docker images. For each branch, new docker image is created, which is saved in Gitlab registry: https://git.easy.cz/devel/devel/container_registry.

The name of the image contains the name of the branch for which it was created.

For debugging, each failed pipeline provides an option to download detailed backtrace zip file in 'Job artifacts' section. You can execute this zip file after downloading it with `yarn playwright show-trace` command. 
