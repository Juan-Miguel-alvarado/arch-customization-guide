# Guía de Customización de Arch Linux (mi setup)

Guía hecha a la medida de **mi** stack — Arch + Omarchy + Hyprland + Kitty + matugen.
No es una guía genérica de Arch: cada sección refleja lo que **realmente** corre en esta máquina.

> **Filosofía:** el wallpaper manda. Todo lo demás (colores de terminal, waybar, hyprland borders, rofi, gtk, helium, spotify) se regenera automáticamente desde la imagen actual con `matugen`. No toco temas a mano: cambio wallpaper → todo el desktop cambia.

---

## 0. Stack actual (resumen de un vistazo)

| Pieza            | Programa                          | Config                                            |
|------------------|-----------------------------------|---------------------------------------------------|
| Distro base      | Arch Linux (rolling) + Omarchy    | `/etc/os-release`, `~/.local/share/omarchy/`      |
| Compositor       | Hyprland                          | `~/.config/hypr/`                                 |
| Barra            | Waybar                            | `~/.config/waybar/`                               |
| Terminal         | Kitty                             | `~/.config/kitty/`                                |
| Shell + prompt   | Bash + Starship                   | `~/.bashrc`, `~/.config/starship.toml`            |
| Launcher 1       | Rofi (drun, wallpaper picker)     | `~/.config/rofi/`                                 |
| Launcher 2       | Walker (Win+K menú)               | `~/.config/walker/`                               |
| Tema dinámico    | Matugen (Material You)            | `~/.config/matugen/config.toml`                   |
| Wallpaper        | swww                              | `~/.config/hypr/current_wallpaper` (symlink)      |
| Notificaciones   | swaync                            | `~/.config/swaync/`                               |
| Lock / Idle      | Hyprlock + Hypridle               | `~/.config/hypr/hyprlock.conf`, `hypridle.conf`   |
| OSD volumen/brillo | SwayOSD                         | `~/.config/swayosd/`                              |
| File manager GUI | Nautilus                          | —                                                 |
| File manager TUI | Yazi (`SUPER+SHIFT+E`)            | `~/.config/yazi/`                                 |
| Editor           | Neovim (LazyVim)                  | `~/.config/nvim/`                                 |
| Git / Docker TUI | Lazygit, Lazydocker               | `~/.config/lazygit/`, `~/.config/lazydocker/`     |
| Monitoreo        | btop, fastfetch                   | `~/.config/btop/`, `~/.config/fastfetch/`         |
| Runtime manager  | mise                              | `~/.config/mise/`                                 |
| AUR helper       | yay                               | —                                                 |

Monitores: **eDP-1** (laptop, workspaces 1-5) + **HDMI-A-1** (externo, workspaces 6-10).

---

## 1. Base: Arch + Omarchy

Omarchy = una distro encima de Arch que pre-arma Hyprland + Waybar + Rofi + ergonomía. Mis bashrc, defaults y muchos scripts vienen de `~/.local/share/omarchy/default/`.

```bash
# El bashrc sourcea los defaults de Omarchy:
source ~/.local/share/omarchy/default/bash/rc
```

**Regla #1 (mía):** NO usar `omarchy theme set <name>`.
Cicla el wallpaper, mata el palette de matugen y deja un `swaybg` pintando encima del de `swww`. Ya eliminé `~/.config/omarchy/{current,themes,themed}` por esto. El theming va 100% por matugen.

Para actualizar el sistema:

```bash
yay -Syu          # Arch + AUR en un solo comando
sudo pacman -Syu  # Solo repos oficiales
```

---

## 2. Hyprland (compositor)

### Estructura

```
~/.config/hypr/
├── hyprland.conf          # entrypoint
├── colors.conf            # generado por matugen — NO editar
├── current_wallpaper      # symlink al wallpaper activo
├── hypridle.conf
├── hyprlock.conf
├── configs/
│   ├── input.conf
│   ├── keybinds.conf
│   ├── looknfeel.conf
│   ├── tags.conf
│   ├── UserAnimations.conf
│   └── windowrules.conf
└── scripts/               # mis scripts custom
```

### Monitores y workspaces

En `hyprland.conf`:

```conf
monitor = eDP-1, 1366x768@60, 1366x0, 1
monitor = HDMI-A-1, 1366x768@59.79, 0x0, 1
monitor = , preferred, auto, 1   # cualquier otro = auto

# Workspaces 1-5 en laptop, 6-10 en monitor externo
workspace = 1, monitor:eDP-1, default:true
workspace = 6, monitor:HDMI-A-1, default:true
# ... etc
```

### Autostart (lo que arranca con la sesión)

```conf
exec-once = nm-applet
exec-once = waybar
exec-once = swww-daemon
exec-once = blueman-applet
exec-once = swaync
exec-once = systemctl --user start hyprpolkitagent
exec-once = hypridle
```

### Variables de programas

```conf
$terminal    = kitty
$fileManager = nautilus
$menu        = rofi -show drun
$mainMod     = SUPER
```

### Keybinds (los que más uso — `~/.config/hypr/configs/keybinds.conf`)

| Combo                 | Acción                                         |
|-----------------------|------------------------------------------------|
| `SUPER + Return`      | Abrir Kitty                                    |
| `SUPER + W`           | Cerrar ventana                                 |
| `SUPER + SHIFT + W`   | Matar proceso de la ventana activa             |
| `SUPER + SPACE`       | Rofi drun                                      |
| `SUPER + K`           | Walker (menú general)                          |
| `SUPER + Q`           | **Wallpaper picker** (rofi + matugen)          |
| `SUPER + E`           | Nautilus                                       |
| `SUPER + SHIFT + E`   | Yazi (kitty)                                   |
| `SUPER + L`           | Lock (hyprlock)                                |
| `SUPER + R`           | Reiniciar waybar                               |
| `SUPER + SHIFT + S`   | Screenshot                                     |
| `SUPER + P`           | Color picker (hyprpicker)                      |
| `SUPER + T`           | Toggle floating                                |
| `SUPER + SHIFT + F`   | Fullscreen                                     |
| `SUPER + BACKSPACE`   | Toggle transparencia ventanas                  |
| `SUPER + H`           | Hide/show waybar                               |
| `SUPER + CTRL + B`    | Menú de estilos de waybar                      |
| `SUPER + ALT + B`     | Menú de layouts de waybar                      |
| `SUPER + flechas`     | Mover focus                                    |
| `SUPER + SHIFT + flechas` | Mover ventana                              |
| `SUPER + 1..0`        | Cambiar workspace                              |
| `SUPER + SHIFT + 1..0`| Enviar ventana a workspace                     |
| `CTRL + ALT + Delete` | Salir de Hyprland                              |

Para cambiar un keybind: editar `~/.config/hypr/configs/keybinds.conf` y recargar con `hyprctl reload` (o `SUPER + R` que reinicia waybar pero hyprland se recarga solo al guardar si lo configurás así).

### Reglas de ventanas

`~/.config/hypr/configs/windowrules.conf` — útil para mandar apps a workspaces fijos, hacerlas flotantes, transparencia, etc.

```conf
# Ejemplo
windowrulev2 = float, class:^(pavucontrol)$
windowrulev2 = workspace 2, class:^(Brave-browser)$
```

### Look & feel

`~/.config/hypr/configs/looknfeel.conf` — gaps, borders, blur, rounding. Los **colores** de los borders vienen de `colors.conf` (matugen), así que no los hardcodees.

---

## 3. Waybar (barra)

```
~/.config/waybar/
├── config            # JSON principal — qué módulos se muestran
├── style.css         # estilos
├── colors.css        # generado por matugen — NO editar
├── Modules/          # módulos built-in
├── ModulesCustom/    # mis módulos custom
├── ModulesGroups/
├── ModulesWorkspaces/
└── UserModules/
```

- **Recargar:** `SUPER + R` (corre `~/.config/hypr/scripts/wbrestart.sh`).
- **Hot-reload de colores:** matugen le manda `SIGUSR2` a waybar, recarga el CSS sin reiniciar.
- **Esconder/mostrar:** `SUPER + H` (manda `SIGUSR1`).
- **Cambiar layout o estilo:** `SUPER + ALT + B` y `SUPER + CTRL + B` (menús de Omarchy).

Para añadir un módulo custom: crear archivo en `ModulesCustom/`, declararlo en `config`, estilarlo en `style.css` usando vars de `colors.css`.

---

## 4. Terminal: Kitty + Bash + Starship

### Kitty

```
~/.config/kitty/
├── kitty.conf       # config principal
└── colors.conf      # generado por matugen — NO editar
```

`kitty.conf` debería tener:
```conf
include colors.conf
```

Matugen, al regenerar `colors.conf`, manda `SIGUSR1` a kitty → recarga colores en vivo.

**Trucos kitty que uso:**
- `CTRL + SHIFT + Enter` — split horizontal
- `CTRL + SHIFT + T` — nueva tab
- `CTRL + SHIFT + L/H/J/K` — moverse entre splits
- `CTRL + SHIFT + +/-` — zoom

### Bash + Starship

`~/.bashrc` sourcea defaults de Omarchy. Para mis aliases/exports, agregar al final:

```bash
alias p='python'
alias g='git'
alias gst='git status'
alias yz='yazi'
alias lzg='lazygit'
alias lzd='lazydocker'
export EDITOR=nvim
```

Prompt en `~/.config/starship.toml` — minimal cyan, muestra dir + git branch + status. Para tunearlo: <https://starship.rs/config/>.

### Default terminal

`~/.config/xdg-terminals.list` apunta a kitty.

---

## 5. Launchers: Rofi + Walker

### Rofi (`SUPER + SPACE` para apps, base del wallpaper picker)

```
~/.config/rofi/
├── config.rasi
├── colors.rasi      # generado por matugen — NO editar
└── (themes...)
```

`config.rasi` debe `@import "colors.rasi"` para usar el palette.

Modos útiles:
- `rofi -show drun` — apps
- `rofi -show run` — comandos
- `rofi -show window` — switcher de ventanas
- `rofi -dmenu` — modo pipe (es lo que usa `wppicker.sh`)

### Walker (`SUPER + K`)

Menú estilo Spotlight más moderno que rofi. Theme actual en `~/.config/walker/themes/omarchy-solid/`. El CSS importa `colors.css` (matugen lo regenera) — por eso ya no depende del theme overlay de Omarchy.

---

## 6. Theming dinámico con Matugen (el corazón del setup)

`matugen` toma una imagen → extrae paleta Material You → renderiza templates → ejecuta post-hooks para recargar apps en vivo.

### Config: `~/.config/matugen/config.toml`

```toml
[config.wallpaper]
set = true
command = "swww img --transition-type any --transition-fps 60 {{ image }}"

[templates.waybar]
input_path  = '~/.config/matugen/templates/colors.css'
output_path = '~/.config/waybar/colors.css'
post_hook   = 'pkill -SIGUSR2 waybar'

[templates.kitty]
input_path  = '~/.config/matugen/templates/kitty-colors.conf'
output_path = '~/.config/kitty/colors.conf'
post_hook   = "kill -SIGUSR1 $(pidof kitty)"

[templates.hyprland]
input_path  = '~/.config/matugen/templates/hyprland-colors.conf'
output_path = '~/.config/hypr/colors.conf'
post_hook   = 'hyprctl reload'

# + gtk3, gtk4, rofi, cava, spicetify, walker, swayosd, btop, helium, vscode, discord, etc.
```

### Templates (`~/.config/matugen/templates/`)

Cada template usa sintaxis tipo Jinja con `{{ colors.primary.default.hex }}`, `{{ colors.surface.default.hex }}`, etc. Lista de vars: <https://github.com/InioX/matugen#templates>.

Para añadir una app nueva al sistema de theming:

1. Crear template en `~/.config/matugen/templates/miapp-colors.conf`.
2. Añadir entrada `[templates.miapp]` en `config.toml` con `input_path`, `output_path` y `post_hook` para recargar.
3. Que la app `include`/`@import` el output.
4. `matugen image ~/.config/hypr/current_wallpaper --prefer saturation` para regenerar.

### El flujo del wallpaper picker (`SUPER + Q`)

`~/.config/hypr/scripts/wppicker.sh`:

```bash
WALLPAPER_DIR="$HOME/Pictures/wallpapers"
SYMLINK_PATH="$HOME/.config/hypr/current_wallpaper"

# Rofi con preview, ordenado por nuevo
SELECTED_WALL=$(for a in $(ls -t *.jpg *.png *.gif *.jpeg 2>/dev/null); do
  echo -en "$a\0icon\x1f$a\n"
done | rofi -dmenu -p "")

matugen image "$WALLPAPER_DIR/$SELECTED_WALL" --prefer saturation
ln -sf "$WALLPAPER_DIR/$SELECTED_WALL" "$SYMLINK_PATH"
```

`matugen` se encarga del `swww img` por el bloque `[config.wallpaper]`. Si lo querés a mano:

```bash
matugen image ~/Pictures/wallpapers/foo.jpg --prefer saturation \
  && ln -sf ~/Pictures/wallpapers/foo.jpg ~/.config/hypr/current_wallpaper \
  && swww img ~/Pictures/wallpapers/foo.jpg
```

### Caso especial: Helium / Chromium policy

Helium (Chromium fork) lee `/etc/chromium/policies/managed/color.json` para `BrowserThemeColor`. Esa carpeta está `chmod 777` para que matugen pueda escribir sin sudo. Template: `helium-color.json`.

---

## 7. Notificaciones, Lock, Idle

### swaync (`~/.config/swaync/`)
Notificaciones + centro de control. Click en icono de campana de waybar abre el panel.

### Hyprlock (`SUPER + L`)
Lockscreen. Config en `~/.config/hypr/hyprlock.conf`. Background = `current_wallpaper`.

### Hypridle (`~/.config/hypr/hypridle.conf`)
Auto-lock + apagado de pantalla por inactividad. Toggle on/off con `SUPER + SHIFT + I`.

### SwayOSD
Notificaciones visuales para volumen, brillo, caps lock. Los scripts `volume.sh` y `brightness.sh` en `~/.config/hypr/scripts/` lo invocan.

### Wlogout (`~/.config/wlogout/`)
Menú de power (logout/reboot/shutdown). Invocado por `Wlogout.sh`.

---

## 8. Apps "donde vivo"

### Neovim (LazyVim)

```
~/.config/nvim/
├── init.lua
├── lazy-lock.json     # versiones fijas de plugins (commitear esto!)
├── lazyvim.json
└── lua/              # mi config
```

- Plugins se manejan con Lazy.nvim — `:Lazy` abre la UI.
- LSP/Mason — `:Mason` para instalar servers.
- Tu config personal va en `lua/config/` y `lua/plugins/`.

### Yazi

File manager TUI. `SUPER + SHIFT + E` abre `kitty yazi`. Atajos vim-like (`h/j/k/l`), `y` copiar, `x` cortar, `p` pegar, `dd` borrar, `space` seleccionar.

### Lazygit / Lazydocker

TUIs para git y docker. Solo correr `lazygit` o `lazydocker` en una terminal. Indispensables.

### btop

Monitor de sistema con buen UI. `btop` y listo. Tema viene de matugen (`btop.theme`).

### fastfetch

System info al estilo neofetch pero más rápido. Lo uso en welcome de terminal o on-demand: `fastfetch`.

### mise

Version manager universal (Node, Python, Ruby, Go, etc.):

```bash
mise use node@22       # en el proyecto actual
mise use -g python@3.13
mise ls                # ver instalados
```

---

## 9. Paquetes: pacman + yay

```bash
# Repos oficiales
sudo pacman -S <pkg>           # instalar
sudo pacman -Rns <pkg>         # eliminar (con deps no usadas)
sudo pacman -Ss <query>        # buscar
sudo pacman -Syu               # actualizar

# AUR
yay -S <pkg>                   # instalar
yay -Syu                       # actualizar todo (repos + AUR)
yay -Yc                        # limpiar deps huérfanas

# Listar
pacman -Qq                     # todos
pacman -Qqe                    # instalados explícitamente
pacman -Qm                     # del AUR / foreign
```

**Buenas prácticas:**
- Antes de instalar del AUR: leer el `PKGBUILD` (`yay -Ga <pkg>`).
- `paccache -rk2` (de `pacman-contrib`) para limpiar cache vieja.
- Si una actualización rompe algo, `/var/log/pacman.log` tiene la historia.

---

## 10. Workflow diario

1. Abro laptop → Hyprland arranca → waybar/swww/swaync ya cargados.
2. `SUPER + Return` → kitty con starship → directo a `~/Projects/algo`.
3. `nvim .` o `lazygit` según necesidad.
4. Aburrido del look? `SUPER + Q` → pico wallpaper → todo cambia en <1s.
5. Cambiar workspace: `SUPER + 1..5` en laptop, `SUPER + 6..0` en monitor externo.
6. Sleep: cerrar tapa o `SUPER + L` para lock manual.

---

## 11. Recetas rápidas (cómo modificar X sin romper Y)

### Quiero cambiar un color a mano (sin tocar wallpaper)

NO. Si lo hacés a `colors.conf`/`colors.css`, el próximo `SUPER + Q` lo sobreescribe. En lugar de eso: editar el **template** correspondiente en `~/.config/matugen/templates/` y regenerar:

```bash
matugen image ~/.config/hypr/current_wallpaper --prefer saturation
```

### Quiero añadir un keybind

`~/.config/hypr/configs/keybinds.conf` → guardar → `hyprctl reload`.

### Quiero añadir un módulo a waybar

1. Crear el módulo en `~/.config/waybar/ModulesCustom/`.
2. Referenciarlo en `~/.config/waybar/config`.
3. Estilarlo en `style.css` usando vars de `colors.css`.
4. `SUPER + R` para reiniciar.

### Quiero que una app arranque sola con la sesión

`exec-once = miapp` en `~/.config/hypr/hyprland.conf` (sección `### AUTOSTART ###`).

### Quiero una app en un workspace fijo

`windowrulev2 = workspace 3, class:^(MiApp)$` en `~/.config/hypr/configs/windowrules.conf`.
Para saber la `class` exacta: `hyprctl clients` con la app abierta.

### Rompí algo y no sé qué

```bash
journalctl --user -xe              # logs de la sesión
hyprctl monitors                   # estado de monitores
hyprctl clients                    # ventanas + clases
cat ~/.cache/hyprland/hyprland.log # log de hyprland
```

---

## 12. Repos de referencia

- Omarchy: <https://github.com/basecamp/omarchy>
- Setup que quiero matchear: <https://github.com/binnewbs/arch-hyprland>
- Matugen: <https://github.com/InioX/matugen>
- Hyprland wiki: <https://wiki.hyprland.org/>
- LazyVim: <https://www.lazyvim.org/>

---

## TL;DR cheat sheet

```
SUPER + Return        terminal
SUPER + SPACE         rofi (apps)
SUPER + K             walker
SUPER + Q             wallpaper picker → rewthemes desktop
SUPER + W             cerrar ventana
SUPER + L             lock
SUPER + 1..0          workspace
SUPER + SHIFT + 1..0  mover ventana a workspace
SUPER + R             reiniciar waybar
SUPER + SHIFT + E     yazi
SUPER + E             nautilus
SUPER + P             color picker
SUPER + H             toggle waybar
SUPER + BACKSPACE     toggle transparencia
CTRL + ALT + Delete   salir de Hyprland
```

**La regla de oro:** no edites archivos generados (`colors.*`). Editá templates o configs base. El wallpaper es la fuente de verdad del look.
