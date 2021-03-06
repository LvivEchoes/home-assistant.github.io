---
layout: page
title: "Packages"
description: "Describes all there is to know about configuration packages in Home Assistant."
date: 2017-01-10 20:00 +0200
sidebar: true
comments: false
sharing: true
footer: true
redirect_from: /topics/packages/
---

Packages in Home Assistant provides a way to bundle different component's configuration together. We were already introduced to the two configuration styles (specifying platforms entries together or individually) on the [adding devices](/docs/configuration/devices/) page. Both of these configuration methods require you to create the component key in the main `configuration.yaml` file. With packages we have a way to include different components, or parts of configuration using any of the `!include` directives introduced in [splitting the configuration](/docs/configuration/splitting_configuration).

Packages are configured under the core `homeassistant/packages` in the configuration and take the format of a packages name (no spaces, all lower case) followed by a dictionary with the package config. For example, package `pack_1` would be created as:

```yaml
homeassistant:
  ...
  packages: 
    pack_1:
      ...package configuration here...
```

The package configuration can include: `switch`, `light`, `automation`, `groups` or the majority of the Home Assistant components. 

It can be specified inline or in a seperate YAML file using `!include`.

Inline example, main `configuration.yaml`:

```yaml
homeassistant:
  ...
  packages: 
    pack_1:
      switch:
        - platform: rest
          ...
      light:
        - platform: rpi
          ...
```

Include example, main `configuration.yaml`:

```yaml
homeassistant:
  ...
  packages: 
    pack_1: !include my_package.yaml
```

The file `my_package.yaml` contains the "top-level" configuration:

```
switch:
  - platform: rest
    ...
light:
  - platform: rpi
    ...
```

There are some rules for packages that will be merged:

1. Component names may only use the basic form (e.g. `switch` and `switch 1` or `switch aa` is not accepted).
2. Platform based components (`light`, `switch`, etc) can always be merged.
3. Components where entities are identified by a key that will represent the entity_id (`{key: config}`) need to have unique 'keys' between packages and the main configuration file. 

    For example if we have the following in the main config. You are not allowed to re-use "my_input" again for `input_boolean` in a package:
    
    ```yaml
    input_boolean:
      my_input:
    ```
4. Any component that is not a platform [2], or dictionaries with Entity ID keys [3] cannot be merged and can only occur once between all packages and the main configuration.

<p class='note tip'>
Components inside packages can only specify platform entries using configuration style 1, where all the platforms are grouped under the component name.
</p>

### {% linkable_title Create a packages folder %}

One way to organise packages would be to create a folder named "packages" in your Home Assistant configuration directory. In the packages directory you can store any number of packages in a YAML file. This entry in your `configuration.yaml` will load all packages:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

This uses the concept splitting the configuration and will include all files in a directory with the keys representing the filenames.
See the documentation about [splitting the configuration](/docs/configuration/splitting_configuration/) for more information about `!include_dir_named` and other include statements that might be helpful.
