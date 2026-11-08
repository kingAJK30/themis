# Task Guides

Deeper instructions for specific parts of the codebase will live under `docs/` as the project evolves.

## Architecture & Modularity

- **No Monolithic Files:** Never dump everything into `configuration.nix`. Keep configurations strictly modular and decomposed by domain.
- **System vs. User Space Separation:**
  - Low-level system settings (bootloaders, core networking, hardware drivers, system users) belong in `hosts/default/configuration.nix` or system-level modules under `modules/`.
  - High-level user applications, window managers, dotfiles, and desktop UI elements (Sway, Waybar, Rofi, themes) must be implemented as modular **Home Manager** components.
- **Directory Layout:**
  - `flake.nix` - Root flake defining NixOS configurations and inputs.
  - `hosts/` - Host-specific hardware profiles and entry points.
  - `modules/desktop/` - Desktop environment modules (subdivided by component: `sway/`, `waybar/`, `theme/`, etc.).
  - `modules/services/` - System-level daemons and background services.
  - `modules/programs/` - CLI tools, shells, and editor configurations.
  - `modules/default.nix` - Central aggregation hub pulling in submodules via clean Nix `imports` lists.

## Style & Aesthetics

- Two spaces for indentation, no tabs.
- Write idiomatic Nix code: use clean attribute sets, `let ... in` blocks, and `lib` functions (`lib.mkIf`, `lib.mkEnableOption`).
- **Industrial Functionalism:** Enforce sharp window corners and zero-gap window parameters across all window manager and desktop layout modules.
- Centralize all styling and color definitions around the **Dracula** base16 color palette.

## Declarative State & Package Management

- **No Imperative Scripts:** Never write shell scripts or activation tasks that imperatively install packages (e.g., using `pacman`, `apt`, or external package managers) or manually write configuration files to `~/.config` at runtime.
- **Pure Declarative State:** All software packages must be declared in `environment.systemPackages` or via Home Manager package lists (`home.packages`). All configuration files must be managed declaratively via Home Manager (`xdg.configFile` or native module options).

## Git

- Commits should be atomic: include only one coherent change or fix, and do not mix unrelated work.
- Commit messages should be succinct and describe the change being made.

# Validation

- Always test syntax and flake evaluation before finishing changes:
  ```bash
  nix flake check
  ```
- Verify that module imports resolve cleanly and that no hardcoded absolute paths (such as /home/username) are introduced into shared configurations.
