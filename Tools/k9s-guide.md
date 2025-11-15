*A Comprehensive Guide to Installing and Using k9s*

## Overview

k9s is a terminal-based UI to interact with your Kubernetes clusters. It provides a fast and efficient way to navigate, observe, and manage Kubernetes resources in real-time. Think of it as a powerful alternative to running multiple kubectl commands.

### Key Features

- Real-time monitoring of cluster resources
- Quick resource navigation with keyboard shortcuts
- Built-in resource editing and viewing
- Pod log streaming with multiple container support
- Port-forwarding capabilities
- Customizable skins and themes

## Installation

### Prerequisites

- kubectl installed and configured
- Access to a Kubernetes cluster
- Valid kubeconfig file (usually at ~/.kube/config)

### Installation Methods

#### macOS

Using Homebrew:
```bash
brew install derailed/k9s/k9s
```

Using MacPorts:
```bash
sudo port install k9s
```

#### Linux

Using a package manager (Arch):
```bash
yay -S k9s
```

Download binary directly:
```bash
wget https://github.com/derailed/k9s/releases/latest/download/k9s_Linux_amd64.tar.gz
tar -xzf k9s_Linux_amd64.tar.gz
sudo mv k9s /usr/local/bin/
```

#### Windows

Using Chocolatey:
```bash
choco install k9s
```

Using Scoop:
```bash
scoop install k9s
```

#### Verify Installation

```bash
k9s version
```

## Basic Usage

### Starting k9s

Launch k9s with your default context:
```bash
k9s
```

Launch with a specific context:
```bash
k9s --context my-cluster-context
```

Launch in a specific namespace:
```bash
k9s -n kube-system
```

### Essential Keyboard Shortcuts

| Key | Action |
|-----|--------|
| **?** | Show help menu with all keyboard shortcuts |
| **:** | Command mode (type resource name to switch views) |
| **/** | Filter resources (search within current view) |
| **Esc** | Exit command/filter mode or go back one level |
| **Enter** | Drill into resource (view details, logs, etc.) |
| **d** | Describe selected resource |
| **e** | Edit selected resource |
| **y** | View YAML manifest |
| **l** | View logs (for pods) |
| **s** | Shell into container |
| **Ctrl-d** | Delete selected resource |
| **Ctrl-k** | Kill selected pod |
| **Ctrl-a** | Show all resources in all namespaces |
| **Ctrl-c** | Copy resource name to clipboard |
| **q** | Quit k9s |

### Common Resource Navigation

Press `:` (colon) to enter command mode, then type the resource shorthand:

| Command | Resource | Full Name |
|---------|----------|-----------|
| **:po** | Pods | pods |
| **:deploy** | Deployments | deployments |
| **:svc** | Services | services |
| **:ns** | Namespaces | namespaces |
| **:node** | Nodes | nodes |
| **:ing** | Ingresses | ingresses |
| **:pv** | Persistent Volumes | persistentvolumes |
| **:pvc** | Persistent Volume Claims | persistentvolumeclaims |
| **:cm** | ConfigMaps | configmaps |
| **:sec** | Secrets | secrets |

## Quick Tips

### Navigation & Workflow

- **Use Pulse View:** Type `:pulse` to see cluster health overview with key metrics
- **Quick Pod Access:** From any resource, press Enter to drill down into related pods
- **Context Switching:** Type `:ctx` to quickly switch between Kubernetes contexts
- **Namespace Filtering:** Type `:ns` and select a namespace, or use 0 to see all namespaces
- **Resource Filtering:** Use `/` to filter, `-l` flag for label selectors (e.g., `/-l app=nginx`)
- **Column Sorting:** Use arrow keys to move between columns, press `s` to sort by selected column
- **Refresh Data:** k9s auto-refreshes every 2 seconds by default

### Log Management

- **View Logs:** Select a pod and press `l` to view logs
- **Previous Logs:** Press `p` while viewing logs to see previous container logs (after restart)
- **Toggle Auto-scroll:** Press `a` in log view to toggle auto-scrolling
- **Toggle Timestamps:** Press `t` to show/hide timestamps in logs
- **Toggle Wrap:** Press `w` to toggle line wrapping in log view
- **Container Selection:** For multi-container pods, select the container first, then view logs
- **Save Logs:** Press `Ctrl-s` while viewing logs to save them to a file

### Resource Management

- **Port Forwarding:** Select a pod/service, press `Shift-f` to set up port forwarding
- **Scale Resources:** On deployments/statefulsets, press `Ctrl-r` to scale up/down
- **Restart Deployments:** Select a deployment and press `r` to restart
- **Edit Resources:** Press `e` to edit resource YAML in your default editor
- **Resource Labels:** Press `Shift-l` to show all labels for a resource

### Troubleshooting Shortcuts

- **Events:** Type `:events` to see cluster events, great for troubleshooting
- **Screen Logs:** Type `:screendump` to save current screen output
- **Benchmark:** Type `:popeye` to run a cluster sanity check (requires Popeye installed)
- **Resource Usage:** Type `:top nodes` or `:top pods` to see resource consumption

## Advanced Usage Tips

### Configuration

k9s stores its configuration at `~/.config/k9s/config.yaml` (or `$XDG_CONFIG_HOME/k9s/config.yaml`). You can customize many aspects of k9s behavior.

#### Key Configuration Options

```yaml
k9s:
  refreshRate: 2         # Seconds between refreshes
  maxConnRetry: 5        # Max reconnection attempts
  readOnly: false        # Prevent resource modifications
  noExitOnCtrlC: false   # Require confirmation to exit
  ui:
    enableMouse: false   # Enable mouse support
    headless: false      # Hide top header
    logoless: false      # Hide k9s logo
    crumbsless: false    # Hide breadcrumbs
    skin: "default"      # UI theme
```

### Custom Views & Aliases

Create custom resource views in `~/.config/k9s/views.yaml`:

```yaml
k9s:
  views:
    v1/pods:
      columns:
        - NAME
        - READY
        - STATUS
        - CPU
        - MEM
```

Create command aliases in `~/.config/k9s/aliases.yaml`:

```yaml
aliases:
  pp: v1/pods
  dp: apps/v1/deployments
  ss: apps/v1/statefulsets
```

### Custom Skins & Themes

k9s supports custom color themes. Create or modify `~/.config/k9s/skins/myskin.yaml`:

```yaml
k9s:
  body:
    fgColor: "#d0d0d0"
    bgColor: "#1a1a1a"
  info:
    fgColor: "#4a90e2"
  table:
    fgColor: "#e0e0e0"
    cursorFgColor: "#000000"
```

Then set it in `config.yaml`:

```yaml
ui: { skin: "myskin" }
```

Popular community skins are available at: https://github.com/derailed/k9s/tree/master/skins

### Plugin System

k9s supports plugins for extending functionality. Create `~/.config/k9s/plugins.yaml`:

```yaml
plugins:
  debug:
    shortCut: Shift-d
    description: Debug pod
    scopes:
      - pods
    command: kubectl
    background: false
    args:
      - debug
      - $NAME
      - -n
      - $NAMESPACE
      - --image=busybox
```

### Hotkey Customization

Customize keyboard shortcuts in `~/.config/k9s/hotkeys.yaml`:

```yaml
hotKeys:
  pods:
    - description: View logs
      shortCut: Shift-l
      command: logs
    - description: Shell into pod
      shortCut: Shift-s
      command: shell
```

### Performance Tuning

#### For Large Clusters

1. Increase refresh rate to reduce API calls:
```yaml
k9s: { refreshRate: 5 }
```

2. Limit namespace scope by default:
```bash
k9s -n production
```

3. Use label selectors for filtering:
```
/-l environment=prod
```

### Shell Integration & Scripts

Useful bash aliases for your `.bashrc` or `.zshrc`:

```bash
alias k9s-prod='k9s --context production'
alias k9s-dev='k9s --context development'
alias k9s-ro='k9s --readonly'
alias k9s-logs='k9s -c po'  # Start in pods view
```

### Advanced Filtering Techniques

- **Regex Filtering:** Use `/pattern` with regex for complex filters
- **Inverse Filters:** Use `/!` to exclude matches (e.g., `/!Running` excludes running pods)
- **Label Selectors:** Use `/-l key=value` for label-based filtering
- **Multiple Labels:** Chain labels: `/-l app=nginx,env=prod`

### RBAC & Security

k9s respects your kubeconfig RBAC permissions. For read-only environments:

```bash
k9s --readonly
```

Or set permanently in config:

```yaml
k9s: { readOnly: true }
```

### Multi-Cluster Management

- Quick context switching with `:ctx` command
- Launch with specific context: `k9s --context cluster-name`
- Set default context in kubeconfig before launching
- Create shell aliases for frequently accessed clusters

## Common Workflows

### Debugging a Failing Pod

1. Launch k9s and navigate to pods: `:po`
2. Filter for your application: `/myapp`
3. Select the pod and press `d` to describe
4. Press `l` to view logs
5. If pod restarted, press `p` for previous logs
6. Press `s` to shell into the container for live debugging

### Rolling Deployment Update

1. Navigate to deployments: `:deploy`
2. Find your deployment with `/`
3. Press `e` to edit the deployment YAML
4. Update image version and save
5. Press Enter to drill into pods
6. Watch the rolling update in real-time

### Investigating High Resource Usage

1. Type `:top nodes` to see node resource usage
2. Type `:top pods` to see pod resource consumption
3. Sort by CPU or memory column (use `s` on column)
4. Select high-usage pod and press Enter
5. Review container resource limits with `d` (describe)

## Troubleshooting

### Common Issues

**Problem:** k9s shows 'Unable to connect to cluster'
- Verify kubeconfig: `kubectl cluster-info`
- Check current context: `kubectl config current-context`
- Try explicit context: `k9s --context your-context`

**Problem:** Can't edit resources
- Check RBAC permissions with kubectl
- Verify you're not in read-only mode (check config)
- Ensure EDITOR environment variable is set

**Problem:** k9s is slow or laggy
- Increase refresh rate in config (reduce API calls)
- Limit namespace scope
- Use filtering to reduce displayed resources

**Problem:** Logs not showing or truncated
- Verify pod is running and containers are healthy
- Check if using correct container in multi-container pods
- Try `kubectl logs` directly to verify log output

### Getting Help

- **In-app Help:** Press `?` anywhere in k9s for context-sensitive help
- **Official Documentation:** https://k9scli.io
- **GitHub Repository:** https://github.com/derailed/k9s
- **Community Slack:** Join #k9s on Kubernetes Slack
- **Check Version:** Run `k9s version` to ensure you're on latest
