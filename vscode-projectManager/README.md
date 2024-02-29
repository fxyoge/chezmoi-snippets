# vscode-projectManager

Automatic configuration management for the VS Code Project Manager extension. Depends on the `configuration-system` snippet.

## Usage

```sh
.chezmoitemplates/
  snippets/
    vscode-projectManager-nameGen-default  # (imported) the default name generator function (in the future there might be a way to override this)
    vscode-projectManager-projects.json    # (imported) the Project Manager settings file
  config-vscode-projectManager-repos       # your list of repos paths to display in the Project Manager window
  config-vscode-projectManager-tags        # your map of tags to assign to repos
dot_config/
  Code/
    User/
      globalStorage/
        alefragnani.project-manager/
          projects.json.tmpl          # (imported) Linux-only, Project Manager settings file
AppData/
  Roaming/
    Code/
      User/
        globalStorage/
          alefragnani.project-manager/
            projects.json.tmpl          # (imported) Windows-only, Project Manager settings file
```

The two custom config files can be set up however you would like. Below are a few examples.

### config-vscode-projectManager-repos

The list of repo paths that should display in the Project Manager window.

```sh
# .chezmoitemplates/config-vscode-projectManager-repos

# Example 1. Manually filled out, YAML format
- ~/.local/share/chezmoi
- ~/Repos/fxyoge/chezmoi-snippets
- ~/.var/app/com.example.flatpak/data

# Example 2. Generated via globs
{{ range concat
    (glob (joinPath $.chezmoi.homeDir ".local/share/chezmoi"))
    (glob (joinPath $.chezmoi.homeDir "Repos/*/*/.git"))
    (glob (joinPath $.chezmoi.homeDir ".var/app/com.example.flatpak/data/.git"))
    | uniq -}}
- {{ . | trimSuffix "/.git" | quote }}
{{ end -}}
```

### config-vscode-projectManager-tags

A map from (repo name) -> (list of tags) to assign to discovered repos. Note that the key is the repo name, not the repo path.

```sh
# .chezmoitemplates/config-vscode-projectManager-tags

# Example 1. Manually filled out, YAML format
fxyoge/chezmoi-snippets: []
com.example.flatpak/data: [personal]

# Example 2. Retrieve the config values from your .chezmoidata
{{- $.repos.tags | toYaml -}}
```
