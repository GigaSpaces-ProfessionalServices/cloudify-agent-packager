Cloudify Agent Packager
=======================

This tool creates Cloudify agent packages.

### Overview

Cloudify's Agents are basically a virtualenv with a series of modules installed in it with a few configuration files attached.

This tool aims to:
- Solve the problem of compiling module requirements on different distributions, thus bridging the gap of user compiled images, unfamiliar/minor distros and so on.
- Allow users to create their own, personalized Cloudify agents with custom plugins of their choosing.
- Make the agent creation process seamless. One config file. One liner cmd.
- Allow users to override the `agent-installer` and `plugin-installer` modules so that they can implement their own.


### Installation

```shell
pip install cloudify-agent-packager
```

For development:

```shell
pip install https://github.com/cloudify-cosmo/cloudify-agent-packager/archive/master.tar.gz
```

### Usage

IMPORTANT NOTES:

- You must use this tool on the distribution you're intending for your agent to run in as it might require compilation.
- You must have the desired version of python installed on your chosen image.
- You must have the `tar` binary in your distribution.

```shell
cfy-ap -h

Script to run Cloudify's Agent Packager via command line

Usage:
    cfy-ap [--config=<path> --force -v]
    cfy-ap --version

Options:
    -h --help                   Show this screen
    -c --config=<path>          Path to config yaml (defaults to config.yaml)
    -f --force                  Forces deletion and creation of venv
    -v --verbose                verbose level logging
    --version                   Display current version
```


### The YAML config file

```yaml
distribution: Ubuntu
version: 3.0
venv: /home/nir0s/Ubuntu-agent/env
python_path: /usr/bin/python
base_modules:
    plugins_common: https://github.com/cloudify-cosmo/cloudify-plugins-common/archive/3.1m4.tar.gz
    rest_client: https://github.com/cloudify-cosmo/cloudify-rest-client/archive/3.1m4.tar.gz
    script_plugin: https://github.com/cloudify-cosmo/cloudify-script-plugin/archive/1.1m4.tar.gz
    diamond_plugin: https://github.com/cloudify-cosmo/cloudify-diamond-plugin/archive/1.1m4.tar.gz
management_modules:
    agent-installer: https://github.com/cloudify-cosmo/cloudify-fabric-plugin/archive/1.1m4.tar.gz
    plugin-installer: https://github.com/cloudify-cosmo/cloudify-fabric-plugin/archive/1.1m4.tar.gz
    windows-agent-installer: https://github.com/cloudify-cosmo/cloudify-fabric-plugin/archive/1.1m4.tar.gz
    windows-plugin-installer: https://github.com/cloudify-cosmo/cloudify-fabric-plugin/archive/1.1m4.tar.gz
additional_modules:
    - pyzmq==14.3.1
    - https://github.com/cloudify-cosmo/cloudify-fabric-plugin/archive/1.1m4.tar.gz
output_tar: /home/nir0s/Ubuntu-agent.tar.gz
```

#### Config YAML Explained

NOTE: `version` is mandatory if not all `base` and `management` modules are provided in the config file. See below for the list of required modules.

- `distribution` - Which distribution is this agent intended for. If this is omitted, the tool will try to retrieve the distribution by itself.
- `version` - Which version of the `base` and `management` modules would you like to use? This is actually a release or branch name in Github. If `latest` is provided, `master` branch will be used.
- `venv` - Path to the virtualenv you'd like to create.
- `python_path` - CURRENTLY unused
- `base_modules` - a `dict` of base modules to install into the package. This allows to override the defaults.
- `management_modules` - a `dict` of management modules to install into the package. If omitted, the original cloudify-manager code will be downloaded and all management modules will be installed from there.
- `additional_modules` - a `dict` of additional modules to install into the package. This is where you can add your plugins.
- `output_tar` - Path to the tar file you'd like to create.

#### Base Modules:

Currently, these are the base modules required for the agent:

- rest_client
- plugins_common
- script_plugin
- diamond_plugin

#### Management Modules:

Currently, these are the base management modules required for the agent:

- agent_installer
- plugin_installer
- windows_agent_installer
- windows_plugin_installer

#### Additional modules:

Note that if you want to use ZeroMQ in the script plugin, you'll have to explicitly configure it in the `additional_modules` section as shown above.