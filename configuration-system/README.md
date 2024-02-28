# configuration-system

A common place to put computed values.

## Usage

Given the following files:

```sh
.chezmoitemplates/
  snippets/
    generate-config      # (imported) template snippet used by the configuration system
  config                 # (imported) template that automatically collects all computed config values, output is formatted as yaml
  config-hello           # your computed values
  config-my-nested-list  # your nested computed values

# .chezmoitemplates/config-hello
"world"

# .chezmoitemplates/config-my-nested-list
{{- list "apple" "banana" "cherry" | toYaml -}}
```

Retrieve config values like so:

```sh
{{- $config := includeTemplate "config" $ | fromYaml -}}
{{- $config.hello | repeat 3 | upper -}}
# $config is
#   hello: world
#   my:
#     nested:
#       list:
#         - apple
#         - banana
#         - cherry
# 
# And the output would be
#   WORLDWORLDWORLD
```

Files outside of `.chezmoitemplates` should reference the main config file, and files inside of `.chezmoitemplates` should reference the specific config value required so as to avoid circular dependencies. For example:

```sh
# .chezmoitemplates/config-myEmail
"alex@example.com"

# .chezmoitemplates/config-trustedEmails
{{- $myEmail := includeTemplate "config-myEmail" $ | fromYaml -}}
{{- list
    $myEmail
    "bob@example.com"
    "john@example.com"
    | toYaml -}}

# dot_config/trustinator/config.json.tmpl
{{- $config := includeTemplate "config" $ | fromYaml -}}
{{- dict
    "emails" $config.trustedEmails
    "user" (dict
      "email" $config.myEmail)
    | toPrettyJson -}}
```

To debug the output, I recommend piping to a text editor such as VS Code so you can collapse sections:

```sh
chezmoi execute-template '{{ includeTemplate "config" $ | fromYaml }}' | code -
```
