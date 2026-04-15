# OpenClash Project Overview

OpenClash is a graphical Mihomo (Clash) client for OpenWrt based on LuCI. It supports multiple protocols (Shadowsocks, ShadowsocksR, Vmess, Trojan, Snell) and provides flexible rule-based traffic routing.

## Technology Stack

- **Frontend**: LuCI (Lua Model-View-Controller framework for OpenWrt).
- **Backend Logic**: Bash Shell scripts and Ruby (for YAML processing).
- **Core Engine**: Mihomo (formerly Clash Meta) and Mihomo-Rust (High-performance Rust implementation).
- **Configuration**: UCI (OpenWrt Unified Configuration Interface).
- **Build System**: OpenWrt SDK / Buildroot.

## Branching Strategy

The project uses a multi-branch approach for development, distribution, and releases:

- **`master`**: The stable branch containing the production-ready LuCI app source code and associated logic.
- **`dev`**: The active development branch where new features and bug fixes are implemented and tested before being merged into `master`.
- **`core`**: A binary distribution branch dedicated to hosting pre-compiled Mihomo (Clash) core binaries for various architectures (amd64, arm64, mips, etc.). It serves as a download point for the app's update mechanism.
- **`package`**: A release branch that hosts compiled `.ipk` (OpenWrt) and `.apk` (Alpine/Android-like) packages, providing a convenient way for users to download and install specific versions.

## Architecture & Project Structure

The project follows the standard OpenWrt/LuCI package structure:

- **`luci-app-openclash/`**: Main application directory.
  - **`luasrc/`**: LuCI source files.
    - **`controller/openclash.lua`**: Defines web UI routes and actions.
    - **`model/cbi/openclash/`**: UCI configuration models (mapping `/etc/config/openclash` to UI fields).
    - **`view/openclash/`**: HTM templates for the web interface.
  - **`root/`**: Files that map directly to the target system's root directory.
    - **`etc/config/openclash`**: Default UCI configuration.
    - **`etc/init.d/openclash`**: System init script (procd).
    - **`usr/share/openclash/`**: Core logic scripts (Bash, Ruby, Lua) for updates, log management, and core control.
  - **`po/`**: Translation files (`.po`).
  - **`tools/po2lmo/`**: Utility to compile translations into `.lmo` format.

## Building and Running

### Building
The project is built as an OpenWrt IPK package.
- **Commands**:
  - Use the OpenWrt SDK: `make package/luci-app-openclash/compile V=99`.
  - Translations need to be compiled using `po2lmo` before packaging.
- **Dependencies**: `dnsmasq-full`, `bash`, `curl`, `ca-bundle`, `ruby`, `ruby-yaml`, `kmod-tun`, `unzip`, and various iptables/nftables modules.

### Running
- The service is managed via the standard OpenWrt init system:
  - Start: `/etc/init.d/openclash start`
  - Stop: `/etc/init.d/openclash stop`
  - Enable on boot: `/etc/init.d/openclash enable`
- UI is accessible via the OpenWrt web interface under **Services -> OpenClash**.

## Development Conventions

1.  **MVC Pattern**: LuCI uses an MVC-like pattern. Controllers handle routing, CBI models handle data (UCI), and Views handle presentation.
2.  **UCI first**: All persistent configuration should be stored in `/etc/config/openclash`.
3.  **Shell for Logic**: Use scripts in `/usr/share/openclash/` for heavy lifting, networking tasks, and asynchronous operations.
4.  **YAML Manipulation**: Ruby is preferred for complex YAML manipulation in Clash config files due to its robust YAML libraries.
5.  **Translations**: Add new strings to `po/*.po` files. Use `po2lmo` to test locally.
6.  **Scripting**: Many scripts source helper libraries like `/usr/share/openclash/log.sh` for consistent logging.

## Key Files

- `luci-app-openclash/root/usr/share/openclash/openclash.sh`: Central logic for configuration updates and processing.
- `luci-app-openclash/luasrc/controller/openclash.lua`: Entry point for all web actions.
- `luci-app-openclash/root/etc/init.d/openclash`: Service management script.
- `luci-app-openclash/root/usr/share/openclash/yml_change.sh`: Handles modifications to the Clash YAML configuration.
