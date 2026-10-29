# Equivalências Remotion → After Effects

Cada componente que funcionou nos Reels da FisioFix e como reproduzir no AE 2022.
Os nomes à esquerda são os do `Premium.tsx`, para facilitar conversas.

| Remotion | After Effects |
|---|---|
| `<Composition 1080×1920 30fps>` | `ae_create_comp` 1080×1920, 30 fps, duração em s |
| `<Sequence from durationInFrames>` | camada com In/Out (`ae_set_layer_props` inPoint/outPoint) ou pré-comp deslocada |
| `interpolate(..., ease)` | keyframes com Easy Ease ajustado (influência 85/10) ou expressão `easeOut`/curva própria |
| `spring()` | expressão de overshoot (expressoes.md §2) |
| `useCurrentFrame()` | `timeToFrames(time)` ou `time*30` dentro da expressão |
| `staticFile()` | `ae_import_file` a partir da pasta do Reels |
| props editáveis (Studio) | Slider/Color/Checkbox Control no layer `CONTROLES` + `ae_essential_graphics` |
| `npx remotion render --frames` | `ae_render_frame` nos quadros-chave |
| `<HtmlInCanvasMotionBlur>` | switch de motion blur na camada + comp (180°, 16 amostras) |
| `colorCorrection()/curves()/lut()` | Lumetri Color (ou Color Balance + Curves + Hue/Sat) / Apply Color LUT (nativo) |
| `vignette()` | sólido preto com máscara elíptica invertida, feather ~600 px, opacidade 62% (ou efeito Vignette do Lumetri) |
| `noise()` grão | Add Grain (nativo) intensidade baixa, ou Noise 7% em camada de ajuste com modo Overlay |
| `zoomBlur()` | CC Radial Fast Blur (nativo) em camada de ajuste por 6–10 quadros |
| `glow()` | Glow nativo, raio 40–80, intensidade 0,6 |
| `<ThreeCanvas>` (GLB) | não existe no AE 2022; usar sequência renderizada no Blender (SKILL.md, Anatomia 3D) |

## Componentes de marca

**Fundo** — sólido `#01090C` + Gradient Ramp radial (centro `#0D3F4A`, borda
`#01090C`) + uma mancha turquesa `#009784` a 22% com Gaussian Blur 30 que
flutua devagar (expressão de wiggle lento, expressoes.md §6). Grade técnica
opcional a 5% de opacidade.

**Sombra de leitura** (`Sombra`) — sólido preto com Gradient Ramp vertical:
88% no topo → 0 a 38% da altura; 0 a 70% → 80% na base. Sem caixas.

**Palavras** (título cinético) — camada de texto Bebas, centralizada, com
Animator: Position (0, 40), Opacity 0, e Blur (10, 10) em "Range Selector →
Advanced → Based On: Words", Shape Ease High, Offset animado de -100% a 100%
em (nº de palavras × 3 + 12) quadros com saída suave. Destaque turquesa ou
vermelho por palavra: segundo Animator com Fill Color e Range Selector
Start/End nas palavras certas (Units: Index, Based On: Words).

**Rotulo** — texto Inter 600, 28 px, tracking 320, `#2FE0C8`, com duas linhas
de forma de 60×2 px nos lados; as linhas crescem de 0 a 60 em 14 quadros.

**SaiTexto** — nos últimos 8 quadros: Opacity 100 → 0, Position Y −30,
Gaussian Blur 0 → 12.

**Marca** (anel na anatomia) — camada de forma: elipse com traço 2,5 px
turquesa + Trim Paths 0 → 100% em 18 quadros; segunda elipse em onda
(escala 90% → 150% e opacidade 70% → 0 a cada 40 quadros, expressoes.md §4);
ponto central de 5 px; 4 traços curtos nos pontos cardeais.

**Guia** (linha até o texto) — caminho de forma em L (horizontal e depois
vertical), traço 2 px, Trim Paths End 0 → 100% em 16 quadros, ponto final
de 4 px.

**Clarao** — sólido 1080×1920 com Gradient Ramp radial (centro `#D2FFF8`, borda
transparente), modo Screen, opacidade 0 → 55% → 0 em 3 e 12 quadros. Sempre
no quadro do impacto, estalo ou bolha, com marcador de som no mesmo quadro.

**Varredura** (transição de luz) — sólido 320 px de altura com degradê
vertical turquesa/branco, modo Screen, que atravessa a tela de cima para
baixo em 14–20 quadros com curva de ida e volta.

**Acabamento** — camada de ajuste no topo: vinheta (75% × 60%, 62% preto) +
grão 7% Overlay com seed mudando a cada quadro.

**Câmera sobre imagem** (`push`, `punch`, `shake`) — no layer de vídeo ou
na pré-comp: escala lenta 112% → 124% até o evento, soco de +10% em 3 quadros
no impacto voltando para +4% em 14, e tremor de 14 px que decai em 10
quadros (expressoes.md §3).

**Contador** (graus, números) — texto Bebas com Source Text por expressão
ligada a um Slider (expressoes.md §5); fica vermelho acima do limiar e dá um
pulo de escala no impacto.

**CTA** — pílula `#009784` com raio 80 px e sombra turquesa, texto Inter
800 54 px branco ("Comenta “X”"), escala com overshoot entrando 20 quadros
depois do título; seta ↓ oscilando 10 px; linha "Fisioterapia em Montes
Claros · (38) 99260-5237" Inter 600 36 px.

**Loop** — os últimos 12 quadros repetem o começo (mesma pré-comp do gancho
com o som original), para o Reels emendar.

## Som

- Trilha: drone grave + batimentos que aceleram até o impacto, riser a partir
  de ~3 s antes, impacto (boom + estalo) e pad calmo depois. Gerado no PC com
  Python/numpy (como `som12.py`) ou trilha livre de direitos.
- Volume da trilha ~0,55–0,9; som original da filmagem só onde importa (estalo).
- Usar `ae-audio-sync`: marcador no pico do áudio e animações ancoradas nele.
