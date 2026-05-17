# my_sway_configs

Configs pessoais para uma sessão **SwayFX** com tema Catppuccin, Waybar, Foot, Fuzzel, Neofetch, Starship, Swayr, Neovim (LazyVim) e Zed.

> O `swayfx/config` apenas inclui o `sway/config` e adiciona os efeitos visuais por cima — então a mesma config roda em Sway puro (sem efeitos) ou SwayFX (com efeitos).

---

## Sumário

1. [Pré-requisitos](#pré-requisitos)
2. [Instalação](#instalação)
3. [Configs pessoais que VOCÊ precisa trocar](#configs-pessoais-que-você-precisa-trocar)
4. [Trocar o wallpaper](#trocar-o-wallpaper)
5. [Trocar / adicionar keybindings](#trocar--adicionar-keybindings)
6. [Tema claro / escuro](#tema-claro--escuro)
7. [Estrutura dos arquivos](#estrutura-dos-arquivos)

---

## Pré-requisitos

Pacotes necessários (nomes do Arch/AUR; ajuste para sua distro):

**Essenciais**
- `swayfx` (ou `sway` se preferir rodar sem efeitos)
- `waybar`
- `foot` (terminal)
- `fuzzel` (launcher)
- `mako` (notificações)
- `waypaper` (gerenciador de wallpaper) + `swaybg` ou `swww`

**Utilitários referenciados na config**
- `swayr` + `swayrd` (alt-tab estilo histórico de janelas)
- `autotiling`
- `flameshot` (screenshots)
- `playerctl`, `pactl` (mídia/áudio)
- `kdeconnect` (`kdeconnect-indicator`)
- `fcitx5` (input method — pode remover se não usar)
- `dolphin`, `cantata`, `mpv`, `firefox`, `kate`, `lutris`, `ksysguard` (apps dos atalhos `F1`–`F6`; troque pelos seus)

**Fontes/temas**
- `ttf-ubuntu-font-family` (usada em Waybar e Fuzzel)
- Uma Nerd Font para os ícones do Waybar (ex.: `ttf-jetbrains-mono-nerd`)
- Tema Catppuccin (já incluso nos arquivos `catppuccin-*.conf`)

**Opcionais por app**
- `starship` (prompt do shell)
- `neofetch`
- `neovim` (a config é LazyVim — vai instalar plugins na primeira execução)
- `zed`

---

## Instalação

Os arquivos seguem a estrutura `~/.config/...`, então copiar é direto.

```bash
git clone https://github.com/<seu-user>/my_sway_configs.git
cd my_sway_configs

# Faça backup se já tiver configs:
mv ~/.config/sway   ~/.config/sway.bak   2>/dev/null
mv ~/.config/swayfx ~/.config/swayfx.bak 2>/dev/null
mv ~/.config/waybar ~/.config/waybar.bak 2>/dev/null

# Copie tudo:
cp -r .config/* ~/.config/
```

Depois siga **obrigatoriamente** a próxima seção antes de logar no SwayFX, senão a sessão pode subir sem monitor configurado / sem wallpaper / com volume controlando o sink errado.

---

## Configs pessoais que VOCÊ precisa trocar

Tudo abaixo está em `~/.config/sway/config`. Edite antes do primeiro login.

### 1. Monitor (resolução, taxa, posição)

Arquivo: `~/.config/sway/config` — linha ~27

```
output HDMI-A-1 resolution 1920x1080@144Hz position 0,0
```

Troque `HDMI-A-1` pelo nome do seu output e ajuste resolução/taxa. Para descobrir os nomes e modos suportados:

```bash
swaymsg -t get_outputs
```

Exemplos:
```
output DP-1   resolution 2560x1440@165Hz position 0,0
output eDP-1  resolution 1920x1080@60Hz  position 1920,0
```

### 2. Wallpaper (caminho do arquivo)

Arquivo: `~/.config/sway/config` — linha ~26

```
output * bg /home/unlucky_m0rpheus/Images/wha-1920x1080.jpeg fill
```

Troque pelo caminho do seu wallpaper. Veja a seção [Trocar o wallpaper](#trocar-o-wallpaper) para o método recomendado (via `waypaper`).

### 3. Áudio (sink do PulseAudio/PipeWire)

Arquivo: `~/.config/sway/config` — linhas ~78–80

```
bindsym XF86AudioRaiseVolume exec --no-startup-id pactl set-sink-volume 0 +5%
bindsym XF86AudioLowerVolume exec --no-startup-id pactl set-sink-volume 0 -5%
bindsym XF86AudioMute        exec --no-startup-id pactl set-sink-mute   0 toggle
```

O `0` é o **índice do sink** (saída de áudio). Pode não ser o seu sink padrão.

Para descobrir e tornar isso resiliente, recomendo trocar `0` por `@DEFAULT_SINK@`:

```
bindsym XF86AudioRaiseVolume exec --no-startup-id pactl set-sink-volume @DEFAULT_SINK@ +5%
bindsym XF86AudioLowerVolume exec --no-startup-id pactl set-sink-volume @DEFAULT_SINK@ -5%
bindsym XF86AudioMute        exec --no-startup-id pactl set-sink-mute   @DEFAULT_SINK@ toggle
```

Liste seus sinks/sources com:
```bash
pactl list short sinks      # saídas (caixas/headset)
pactl list short sources    # entradas (microfone)
```

### 4. Microfone (mute do mic — opcional)

Não tem bind de mic por padrão. Se quiser adicionar, use a tecla `XF86AudioMicMute`:

```
bindsym XF86AudioMicMute exec --no-startup-id pactl set-source-mute @DEFAULT_SOURCE@ toggle
```

### 5. Mouse (perfil de aceleração — opcional)

Arquivo: `~/.config/sway/config` — linha ~20

```
input "1241:64560:USB_Gaming_Mouse" {
    accel_profile flat
    pointer_accel 0
}
```

`1241:64560:USB_Gaming_Mouse` é específico do meu mouse. Para o seu funcionar igual, descubra com:

```bash
swaymsg -t get_inputs
```

E troque a string entre aspas. Ou apague esse bloco inteiro se não quiser perfil customizado.

### 6. Apps dos atalhos F1–F8

Se você não usa `dolphin`, `cantata`, `kate`, `lutris` etc., troque ou remova as linhas correspondentes em `~/.config/sway/config` (busque por `bindsym $mod+F`).

---

## Trocar o wallpaper

Duas formas:

**A) Permanente (na config):** edite a linha `output * bg ...` em `~/.config/sway/config` apontando para o novo arquivo, e dê `Mod+Shift+C` (reload).

**B) Via `waypaper` (recomendado):** a config já chama `waypaper --restore` no startup, então basta:

```bash
waypaper           # abre GUI, escolhe a imagem
```

O `waypaper` salva a escolha em `~/.config/waypaper/config.ini` e ela é restaurada automaticamente no próximo login.

---

## Trocar / adicionar keybindings

Todas as binds estão em `~/.config/sway/config`. A variável da tecla modificadora é:

```
set $mod Mod4     # Mod4 = tecla Super/Windows. Troque por Mod1 para usar Alt.
```

**Sintaxe básica:**
```
bindsym <combinação>  <ação>
```

Exemplos prontos no arquivo (procure por `### Key bindings`, `### Moving around`, `### Workspaces`, `### Layout`):

| Combinação              | Ação                                |
|-------------------------|-------------------------------------|
| `Mod+Return`            | abre o terminal (`foot`)            |
| `Mod+d`                 | abre o launcher (`fuzzel`)          |
| `Mod+Shift+q`           | fecha a janela focada               |
| `Mod+Shift+c`           | recarrega a config do sway          |
| `Mod+1` … `Mod+0`       | troca para workspace 1–10           |
| `Mod+Shift+1` … `0`     | move janela para workspace 1–10     |
| `Mod+f` / `F11`         | fullscreen                          |
| `Mod+r`                 | modo de resize                      |
| `Alt+Tab` / `Alt+Shift+Tab` | swayr (próxima/anterior janela) |

**Para trocar uma bind**, simplesmente edite a linha e salve. Depois `Mod+Shift+C` para recarregar.

**Para adicionar uma nova bind**, por exemplo abrir o navegador com `Mod+g`:
```
bindsym $mod+g exec firefox
```

**Para remover uma bind**, apague ou comente a linha (`#` no início).

**Dica:** rode `swaymsg -t get_inputs` e teste teclas com `wev` para descobrir nomes de teclas exóticas.

---

## Tema claro / escuro

Na linha 1 do `~/.config/sway/config`:

```
include ~/.config/sway/catppuccin-latte.conf      # tema claro
# include ~/.config/sway/catppuccin-mocha.conf    # tema escuro
```

Troque qual está comentada e dê `Mod+Shift+C`.

---

## Estrutura dos arquivos

```
.config/
├── sway/
│   ├── config                  # config principal (binds, output, input, regras)
│   ├── catppuccin-latte.conf   # paleta clara
│   └── catppuccin-mocha.conf   # paleta escura
├── swayfx/
│   ├── config                  # include do sway + efeitos
│   └── effects.conf            # corner_radius, shadows, blur, dim
├── waybar/
│   ├── config.jsonc            # módulos da barra
│   └── style.css               # estilo da barra
├── foot/foot.ini               # terminal
├── fuzzel/fuzzel.ini           # launcher
├── swayr/config.toml           # alt-tab por histórico
├── neofetch/config.conf
├── starship.toml               # prompt do shell
├── nvim/                       # LazyVim
└── zed/settings.json
```

---

## Como rodar no SwayFX

Se você instalou `swayfx`, normalmente o display manager já oferece a sessão "SwayFX". Caso esteja iniciando manualmente:

```bash
swayfx --config ~/.config/swayfx/config
```

Para rodar sem efeitos (sway puro):
```bash
sway --config ~/.config/sway/config
```
