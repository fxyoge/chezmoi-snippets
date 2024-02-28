# chezmoi-snippets

Common snippets that I use.

## Usage (easy)

Copy paste.

## Usage (advanced)

There's no one way to install but this is what I'm doing:

Add the following to your `.chezmoiexternal.yaml`

```yaml
.local/share/chezmoi/1/..:
  type: archive
  url: https://github.com/fxyoge/chezmoi-snippets/archive/main.tar.gz
  stripComponents: 3
  refreshPeriod: 168h
  include:
    - "*/configuration-system/home/**/*"
    # add more snippets as desired
```

CAVEATS:
* `.local/share/chezmoi/1/..` is so that you can have multiple entries pointed to the same directory (lol).
* `chezmoi apply` will update externals (such as the snippets), and then immediately apply other dotfiles changes based off of the updated externals, without giving you a chance to review changes. This means that `chezmoi diff` output may differ from what actually gets applied! Consider running `chezmoi apply -v -i externals` and diffing a second time.
* It's strongly suggested that you pin to a specific commit, which will help protect you against the above issue.
