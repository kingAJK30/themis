# Task Guides

Deeper instructions for specific kinds of work live in `agents/skills/`. Read the matching guide before starting:

- [`agents/skills/nix-modules.md`](https://www.google.com/search?q=agents/skills/nix-modules.md) - adding or organizing system modules under `modules/`
- [`agents/skills/home-manager.md`](https://www.google.com/search?q=agents/skills/home-manager.md) - managing user space, dotfiles, and desktop apps via Home Manager
- [`agents/skills/desktop-shell.md`](https://www.google.com/search?q=agents/skills/desktop-shell.md) - configuring Sway, Waybar, and desktop UI components
- [`agents/skills/theming.md`](https://www.google.com/search?q=agents/skills/theming.md) - managing base16 palettes and application styling
- [`agents/skills/validation.md`](https://www.google.com/search?q=agents/skills/validation.md) - running flake checks and testing Nix configurations

# Documentation Layout

Three documentation trees, split by genre and audience:

- `agents/skills/` - task procedure ("do this when doing X"), for anyone working on the codebase
- `docs/` - reference on how the system is shaped (flake architecture, module mapping, theming model), for anyone working on the codebase; skills link here for depth
- `manual/` - end-user documentation for using Themis, published; never codebase internals

# Style

- In Markdown documents (`plans/`, `docs/`, `manual/`), write full lines - no hard wrapping at 80 columns; break only at structural boundaries like headings and list items
- Two spaces for indentation, no tabs
- Write idiomatic Nix code: use clean attribute sets, let-in bindings, and `lib` functions (`lib.mkIf`, `lib.mkEnableOption`, `lib.mkDefault`)
- Never use monolithic files: decompose configurations logically across domain-specific directories under `modules/` rather than expanding `configuration.nix` indefinitely
- Enforce industrial functionalism: maintain sharp window corners and zero-gap window parameters across all window manager and desktop layout modules

# Architecture & Modularity

- **System vs. User Space Separation:**
  - Low-level system configurations (bootloaders, hardware drivers, core networking, system users) belong in `hosts/default/configuration.nix` or system-level modules.
  - High-level user applications, dotfiles, window managers, and desktop UI elements (Sway, Waybar, Rofi, themes) must be implemented as modular **Home Manager** components.

- **Directory Structure:**
  - `hosts/` - Host-specific hardware profiles and entry points.
  - `modules/desktop/` - Desktop environment modules (subdivided by component: `sway/`, `waybar/`, `theme/`).
  - `modules/services/` - System-level daemons and background services.
  - `modules/programs/` - CLI tools, shells, and editor configurations.
  - `modules/default.nix` - Central aggregation hub that pulls in submodules via clean Nix `imports` lists.

# Declarative State & Package Management

- **No Imperative Scripts:** Never write shell scripts or activation commands that imperatively install packages, use package managers (`pacman`, `apt`), or manually write configuration files to `~/.config` at runtime.
- **Pure Declarative State:** All software packages must be declared in `environment.systemPackages` or via Home Manager package lists (`home.packages`). All configuration files must be managed declaratively via Home Manager (`xdg.configFile` or native module options).
- **Theme Integration:** Centralize theme configurations around the Dracula base16 color palette across all desktop modules, terminal themes, and UI style templates.

# Git

- Commits should be atomic: include only one coherent change or fix, and do not mix unrelated work.
- Commit messages should be succinct and describe the change being made.

# Validation

- Always run a flake evaluation check to catch syntax errors or broken attribute sets before finalizing changes:

  ```bash
  nix flake check
  ```
  
  - Verify that module imports resolve cleanly and that no hardcoded absolute paths (such as /home/username) are introduced into shared configurations.
