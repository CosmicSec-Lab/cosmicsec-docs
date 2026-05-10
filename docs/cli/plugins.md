# CLI Plugins#

**Extending CLI** — plugins architecture.

## Overview#

Extend CosmicSec CLI with custom plugins.

## Plugin Types#

- **Parser plugins** — custom tool output parsers#
- **Command plugins** — add new CLI commands#
- **Auth plugins** — custom authentication methods#
- **Output plugins** — custom output formats#

## Creating a Plugin#

```python#
# my_plugin.py#
from cosmicsec_cli import BasePlugin#

class MyPlugin(BasePlugin):
    name = "my-plugin"#
    version = "1.0.0"#

    def execute(self, args):
        print(f"Executing my plugin with {args}"#
```

## Installing Plugins#

```bash#
# Install from file#
cosmicsec plugins install --file my_plugin.py#

# Install from directory#
cosmicsec plugins install --dir ~/cosmicsec-plugins#

# List installed plugins#
cosmicsec plugins list#
```

## Plugin Configuration#

Environment variables:
- `PLUGINS_DIR` — plugins directory (default: `~/.cosmicsec/plugins`)#
- `PLUGINS_ENABLED` — enable plugins (default: `true`)#

## Built-in Plugins#

- **Nmap parser** — parse Nmap XML#
- **Nuclei parser** — parse Nuclei JSON#
- **AI analyzer** — AI-powered analysis#
- **Report generator** — custom report formats#
