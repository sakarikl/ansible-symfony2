# Symfony2
[![Build Status](https://travis-ci.org/servergrove/ansible-symfony2.svg?branch=master)](https://travis-ci.org/servergrove/ansible-symfony2)

Ansible role to easily deploy Symfony2 applications.
It will clone a git repository, a specific branch or a tag, download and run composer install, and run assetic:dump when finished.
The resulting directory structure is similar to what capifony/capistrano creates:

```
project
    composer.phar
    releases
        release
    shared
        web
            uploads
        app
            config
            logs
    current -> symlink to latest deployed release
```

It will also keep the ```releases``` directory clean and deletes all but your configured amount of releases.
You're also able to switch on/off whether ansible should execute doctrine migrations, or mongodb schema update, etc.

## Requirements

Installed version of Ansible.

## Installation

### Ansible galaxy

```
    $ ansible-galaxy install servergrove.symfony2
```

### Usage

This playbook is taken from the travis testcase. You can always pass these values as commandline parameters.

```yaml
---
- hosts: servers
  roles:
    - ansible-symfony2

  vars:
    symfony_project_root: /tmp/test_app
    symfony_project_name: travis-test
    symfony_project_composer_path: /tmp/test_app/shared/composer.phar
    symfony_project_repo: https://github.com/symfony/symfony-standard.git
    symfony_project_env: prod

    symfony_project_console_opts: '--no-debug'
    symfony_project_keep_releases: 5

    symfony_project_branch: "2.6"
    symfony_project_php_path: php
    symfony_project_keep_releases: 5

    symfony_project_manage_composer: True
    symfony_project_composer_opts: '--no-dev --optimize-autoloader --no-interaction'
```

Commandline: ```~$ ansible-playbook -i inventory --extra-vars "symfony_project_release=20150417142505,symfony_project_branch=master" test.yml```

## Role Variables

### Possible variables

These are the possible role variables - you only need to have a small set defined, there are defaults.

```yaml
---
- vars:
    # necessary project vars
    symfony_project_root: Path where application will be deployed on server.
    symfony_project_composer_path: path where composer.phar will be stored (e.g. project_root/shared)
    symfony_project_repo: URL of git repository.
    symfony_project_release: Release number, can be numeric, we recommend to set it to release date/time, 20140327100911
    symfony_project_env: prod

    # optional parameters, covered by defaults
    symfony_project_post_folder_creation_tasks: task hook after folder creation
    symfony_project_pre_cache_warmup_tasks: after cache warmup
    symfony_project_pre_live_switch_tasks: before live symlink is switched
    symfony_project_post_live_switch_tasks: after live symlink is switched

    symfony_project_branch: git branch, commit hash or version tag to deploy - defaults to master
    symfony_project_php_path: php
    symfony_project_keep_releases: 5
    symfony_project_git_clone_depth: 1 # uses git shallow copy
    symfony_project_console_opts: ''
    symfony_project_parameters_file: parameters.yml # optional fixed parameters file in shared
    symfony_project_cache_command: cache:warmup

    symfony_project_manage_composer: True
    symfony_project_composer_opts: '--no-dev --optimize-autoloader --no-interaction'
    symfony_project_composer_run_install: True

    symfony_project_enable_cache_warmup: True warmup symfony cache, check out cache command!
    symfony_project_fire_schema_update: False # rund mongodb schema update if installed
    symfony_project_fire_migrations: run doctrine migrations, if installed
    symfony_project_symlink_assets: run assets:create with symlink options
```

### Role variable defaults

As you can see, the release number default is the current date/time with seconds to allow for easy multiple releases per day. But you can always overwrite with ```--extra-vars=""``` option.

```yaml
---
- vars
    symfony_project_release: <datetime> # internally replaced with YmdHis
    symfony_project_branch: master
    symfony_project_php_path: /usr/bin/php
    symfony_project_keep_releases: 5
    symfony_project_git_clone_depth: 1
    symfony_project_console_opts: ''
    symfony_project_composer_opts: '--no-dev --optimize-autoloader --no-interaction'
    symfony_project_fire_migrations: False
    symfony_project_symlink_assets: True
```

## hooks
If you need any more tasks and stuff in your deployment, you now have the option to include hook scripts.

possible hooks:

```
    symfony_project_post_folder_creation_tasks: task hook after folder creation
    symfony_project_pre_cache_warmup_tasks: after cache warmup
    symfony_project_pre_live_switch_tasks: before live symlink is switched
    symfony_project_post_live_switch_tasks: after live symlink is switched
```

These hooks trigger an include when defined.

## Dependencies

None

## Testing

The deployment contains a basic test, executed by travis. If you want to locally test the role, have a look into ```.travis.yml``` for the exceution statements and (maybe) remove the ```geerlingguy.php ``` from ```tests/test.yml``` if you have a local php executable (needed for composer install and symfony console scripts).

The test setup looks like this:

```
servergrove.symfony2
    tests
        inventory # hosts information
        test.yml # playbook
    .travis.yml # travis config
```

## License

[MIT](LICENSE) license

### Author Information

Contributions are welcome: https://github.com/servergrove/ansible-symfony2
