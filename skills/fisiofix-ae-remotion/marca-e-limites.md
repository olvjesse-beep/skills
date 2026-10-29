
# Reels FisioFix no After Effects

Este é o "fork" do que funcionou no Remotion (componentes de `Premium.tsx`,
`Ombro3D.tsx`, `Estalo.tsx` e as 12 skills oficiais do Remotion) traduzido
para o After Effects. A edição acontece no PC do Jessé, pelo AE MCP Bridge,
sem nuvem. Use junto das skills `ae-*` do mesmo fork: `ae-mcp-bridge`
(ferramentas), `ae-typography`, `ae-color`, `ae-audio-sync`, `ae-camera-3d`,
`ae-motion-proof` e `ae-deliver`.

Equivalências em detalhe: [equivalencias.md](equivalencias.md).
Expressões prontas: [expressoes.md](expressoes.md).

## Limites do ambiente (não negociar)

- After Effects 2022 (v22.0) em `G:\Adobe`. Sem GLB/Advanced 3D (exige 24.1+)
  e sem saída H.264: renderizar ProRes 422 LT/Proxy com `ae_render_video` ou
  aerender e converter para MP4 com o ffmpeg do Streamlabs; apagar o .mov na hora.
- Gravar só no projeto sandbox `G:\CLAUDE FIX\ae-trabalho\trabalho.aep`
  (copiar o .aep do Reels por cima, trabalhar, copiar de volta). Nunca editar
  `.ae-mcp-bridge.json` para contornar a trava.
- Render ≤ 15 min por Reels; se a estimativa passar disso, avisar o Jessé antes e
  mudar a abordagem. Nada de pré-render de planos, DOF pesado ou upscale de 8 camadas.
- Tamanho total ≤ 100–200 MB por Reels (intermediários incluídos).
- Abrir a barra de progresso `G:\CLAUDE FIX\progresso-render.ps1` durante o render.
- Não instalar plugins nem software sem autorização; sem plugins pagos.
- Não publicar nada. Não dizer que o MP4 está pronto sem conferir o arquivo.

## Formato

- Comp principal 1080×1920, 30 fps, pixel quadrado, fundo `#01090C`.
- Área segura do Reels: textos entre y = 230 e y = 1440, margem lateral ≥ 80 px
  (≥ 230 px à direita na base, onde ficam os botões do Instagram).
- Tudo centralizado em x = 540. Conferir o eixo antes de entregar.
- Um foco por cena: decidir o que o olho vê primeiro e montar o quadro em volta.

## Marca

- Turquesa claro `#2FE0C8` (destaque de palavra, rótulos, marcações).
- Turquesa médio `#009784` (pílula de CTA).
- Vermelho de dor/alerta `#FF5A4E` (ou `#FF7A5C` em texto sobre vídeo).
- Fontes em `G:\CLAUDE FIX\...\fontes` (cópia em after-effects/fontes):
  Bebas Neue (títulos), Inter SemiBold/ExtraBold (apoio, rótulos, CTA).
- Título: Bebas 124–150 px, entrelinha 0,92, sombra 0 10 40 preta a 60%.
  Mínimo 84 px. Texto de apoio: Inter 600, 44 px (mínimo 44), sombra leve.
- Rótulo técnico: Inter 600, 28 px, tracking ~320, turquesa, com traço de
  60 px dos dois lados.
- Sem caixas atrás do texto. A leitura vem de sombras em degradê no topo e na base.
- WhatsApp no CTA: (38) 99260-5237 · pino "Montes Claros · MG".

## Estrutura de retenção

- Quadro 0 já tem voz ou som e o texto do gancho na tela. Nada de 1,7 s mudo.
- Gancho de dor ou identificação, não informativo. Loop aberto (algo que só
  se resolve no fim) sempre que possível.
- Cada frase de legenda fica ≥ 3 s na tela.
- Reels de 12–30 s. CTA de comentário ou salvar, sem isca.
- Tom clínico e de credibilidade: nada de efeito sonoro de meme, nada lúdico.
- Nunca cortar palavras da fala do Jessé; se precisar encurtar, acelerar a voz levemente.

## Regras de animação (vindas do Remotion)

- Todo movimento por keyframe ou expressão ligada ao tempo. Nada de easing
  linear: usar a curva "saída suave" equivalente a `bezier(0.22, 1, 0.36, 1)`
  (influência de entrada ~85%, saída ~10%) e `bezier(0.65, 0, 0.35, 1)` para
  transições de ida e volta.
- Molas (spring) viram overshoot por expressão (ver expressoes.md).
- Motion blur ligado em toda camada que se move rápido; obturador 180°, 16 amostras.
- Textos entram palavra por palavra (sobe 40 px, desfoque 10 → 0, opacidade
  0 → 1, 12 quadros, 3–4 quadros entre palavras) e saem com desfoque 12 px + subida 30 px em 8 quadros.
- Valores editáveis ficam expostos como controles (Slider/Color Control no
  layer ou no Essential Graphics), equivalente às props do Remotion.

## Anatomia 3D

- Renderizar só os estados únicos (ângulos) no Blender/Cycles, em 720p e
  poucas amostras. Interpolar com ffmpeg minterpolate e subir para 1080×1920
  (lanczos + unsharp). Importar como sequência JPG.
- No AE, mapear tempo → estado com Time Remap por expressão (ver expressoes.md),
  em vez de renderizar a animação inteira.
- Nunca girar imagem 2D em 3D (parece papel). Nunca escrever rótulo do tipo
  "coluna 3D" ou "Ilustração educativa" na tela.
- Tendão não deforma: desliza rígido, só o ventre muscular encurta.
- Crédito do modelo vai só na legenda do post ("Modelos 3D: Z-Anatomy (CC BY-SA 4.0)").
- Sempre prévia em baixa resolução antes do render final.

## Cor das filmagens

Equivalente ao `colorCorrection()` do Remotion:
- Lumetri Color: Temperatura ~ -10 a -15 (jaleco branco, não amarelado),
  Tint 0 a -3, Contraste +10, Realces -10, Sombras +10, Vibração +15,
  Saturação 105. O conta-gotas de balanço de branco no jaleco é o melhor caminho.
- Se o script não conseguir escrever no Lumetri, usar efeitos nativos que
  aceitam valores por script: Color Balance, Curves, Hue/Saturation, Exposure, Levels.
- Acabamento final em toda comp: vinheta suave + grão de filme a ~7% (ver equivalencias.md).
- Padrão premium de fotógrafo, nunca filtro fraco.

## Fluxo de trabalho

1. Ler o roteiro e o material; estimar o tempo de render antes de começar.
2. Copiar o .aep do Reels para o sandbox ou criar comp nova com `ae_create_comp`.
3. Montar por cenas em pré-comps curtas (`S1_GANCHO`, `S2_...`, `S9_CTA`) só
   quando a cena precisar de linha do tempo própria.
4. Aplicar os componentes de equivalencias.md com `ae_text`, `ae_shape`,
   `ae_apply_effect`, `ae_set_expression`, `ae_add_keyframes` ou um único `ae_run_jsx`.
5. Sincronizar o som com marcadores (`ae_markers`) no impacto, estalo ou clarão.
6. `ae_proof` na comp principal (com `staging: true`), depois `ae_render_frame`
   em 4–6 quadros-chave e olhar cada um: centralização, legibilidade, cortes.
7. Render, conversão para MP4 (H.264, yuv420p, AAC 192k, faststart), conferir
   duração, tamanho e um quadro do arquivo final.
8. Salvar o .aep de volta na pasta do Reels; apagar intermediários.
