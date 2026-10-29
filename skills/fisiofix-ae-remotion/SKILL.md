---
name: fisiofix-ae-remotion
description: Aplica no After Effects 2022 (local, via MCP after-effects) as boas práticas de edição das skills do Remotion, adaptadas para os Reels da FisioFix — linha do tempo por cenas, timing por quadros com curvas bezier/spring em expressões, transições e overlays nos cortes, legendas sincronizadas por palavra, cortes de silêncio, efeitos sonoros no quadro exato, correção de cor primária, motion blur com custo controlado, parâmetros trocáveis (nexrender/Essential Graphics) e prévia antes do render final. Use ao montar, editar ou revisar qualquer Reels/Story da FisioFix no After Effects.
---

# FisioFix · práticas do Remotion traduzidas para o After Effects

Fonte: skills `remotion-best-practices` (fork olvjesse-beep/skills, Remotion 4.0.539), adaptadas para o **AE 2022 local** controlado pelo MCP `after-effects` (painel AE MCP Bridge). Nada roda na nuvem. Remotion: licença gratuita para a FisioFix (1 pessoa); se a equipe chegar a 4+, rever a licença antes de usar o Remotion em si.

## Arquivos de apoio (leia conforme a tarefa)
- [equivalencias.md](equivalencias.md): cada componente que funcionou no Remotion (`Premium.tsx`: Palavras, Rotulo, SaiTexto, Marca, Guia, Clarao, Varredura, Acabamento, câmera push/punch/tremor, Contador, CTA, loop) e a forma de reproduzir no AE pelo MCP.
- [expressoes.md](expressoes.md): expressões prontas (overshoot/spring, cascata por palavra, câmera, Time Remap por Slider para 3D por ângulo etc.).
- [marca-e-limites.md](marca-e-limites.md): cores, fontes, área segura, retenção, limites de render (15 min) e disco (200 MB), sandbox e AE 2022 sem H.264. **É a fonte única dessas regras**; este SKILL.md só resume.

## 0. Regras de trabalho
- **Prévia primeiro** (Remotion: "abra o Studio, não renderize por padrão"). Antes de qualquer render final: `ae_render_frame` dos quadros-chave e `ae_review_motion`. Render longo só com OK explícito do Jessé.
- **Preserve edições do usuário**: se o projeto mudou por fora, não sobrescreva; pergunte.
- **Sandbox fixo**: edite só `G:\CLAUDE FIX\ae-trabalho\trabalho.aep` (copie o .aep do vídeo para lá; devolva ao final). Nunca mexa na trava do painel.
- **Limites** de tempo, disco, marca e área segura: ver [marca-e-limites.md](marca-e-limites.md).

## 1. Linha do tempo por unidades editáveis (Sequence / TransitionSeries → pré-comps)
- Uma **pré-comp por cena** com nome claro ("01 gancho", "02 passo 1"…). Vídeo, legenda e overlays sincronizados ao mesmo clipe ficam **dentro da mesma pré-comp** (um relógio só): mover/aparar a cena move tudo junto.
- Cenas consecutivas: pré-comps encostadas no comp principal (equivalente ao TransitionSeries); a transição fica numa camada de ajuste/overlay **sobre o corte**, sem encurtar a timeline (= `TransitionSeries.Overlay`).
- Tempos sempre derivados do fps do comp: em expressões use `thisComp.frameDuration`/`time*30` explícito, nunca números soltos sem comentário.

## 2. Timing (interpolate + Easing → expressões)
- `interpolate(frame,[a,b],[v0,v1],{clamp})` → `linear(time, a, b, v0, v1)` (já é "clamp" no AE).
- `Easing.bezier(0.16,1,0.3,1)` (o padrão "saída rápida, pouso macio") → expressão:
  `function bz(t){ /* cubic-bezier(.16,1,.3,1) */ var p=[0,.16,.3,1], q=[0,1,1,1]; var u=t; for(var i=0;i<6;i++){ var x=3*(1-u)*(1-u)*u*p[1]+3*(1-u)*u*u*p[2]+u*u*u-t; var dx=3*(1-u)*(1-u)*p[1]+6*(1-u)*u*(p[2]-p[1])+3*u*u*(1-p[2]); if(Math.abs(dx)<1e-6)break; u-=x/dx; } return 3*(1-u)*(1-u)*u*q[1]+3*(1-u)*u*u*q[2]+u*u*u; }`
- `Easing.spring({damping:200})` (empurrão sem quique) → `1-Math.exp(-t*9)*(1+t*9)` normalizado no intervalo; com quique leve: `1-Math.exp(-8*t)*Math.cos(12*t)`.
- **Escala perceptual** (Remotion `output:'perceptual-scale'`): interpole o **log** da escala: `s = s0*Math.pow(s1/s0, p)` com `p` já com easing. Push-in e punch-in parecem constantes.
- Vários trechos: Hermite/`linear` por segmento com velocidades casadas (como a câmera do ombro). Não use eKeys/aeFunctions via `footage()` no AE 2022 (retorna null).

## 3. Transições e overlays no corte
- Sem plugins pagos: zoom-desfoque (CC Radial Fast Blur + motion blur da câmera), varredura de luz (CC Light Sweep), **light leak** = sólido com Ruído fractal colorido + Degradê radial em modo Adicionar, progresso 0→1→0 em ~12 quadros centrado no corte.
- Duração padrão 6–15 quadros; o overlay não muda o tempo das cenas.

## 4. Texto e legendas (captions)
- Legenda por palavra sincronizada à fala: importe SRT/transcrição → uma camada de texto por frase, palavras entrando em cascata (seletor por palavras) e **destaque da palavra falada** (animador de cor por índice). Tempo mínimo na tela, palavras por linha e área segura: [marca-e-limites.md](marca-e-limites.md). Expressões de cascata e destaque: [expressoes.md](expressoes.md).
- Destaques tipo "rough-notation" (sublinhar, circular, marca-texto) = Shape com Trim Paths animado por `progress` amarrado ao tempo — nunca caixa atrás do texto.
- Fontes e cores da marca: [marca-e-limites.md](marca-e-limites.md). Meça o texto (`sourceRectAtTime`) e reduza a escala para caber, em vez de quebrar a área segura.

## 5. Áudio
- **Silêncios** (jump cuts de fala): `ffmpeg -af loudnorm=print_format=json` → `input_thresh`; depois `silencedetect=noise=<thresh>dB:d=0.5`; corte nas bordas com 3–4 quadros de folga; nunca cortar palavra-chave, ressalva ou CTA.
- **SFX no quadro exato**: whoosh/whip nos cortes, impacto no quadro do clarão. Depois do MP4, confira com ffmpeg (pico de áudio × quadro do evento, tolerância 1 quadro). Impacto precisa de transiente: vácuo de ~0,2 s antes + golpe.
- Trilha ducking −10 a −14 dB sob a fala.

## 6. Cor e acabamento
- Ordem primária (como `colorCorrection()`): exposição → balanço de branco (temperatura/tint; jaleco neutro) → sombras/realces → pretos/brancos → contraste → saturação/vibrance. No AE: Lumetri numa camada de ajuste única para todos os planos; turquesa da marca preservado.
- Motion blur: obturador 180°, 8 amostras (até 12 só em planos rápidos). Mais amostras = render mais lento.

## 7. Parâmetros (Zod schema → dados trocáveis)
- Tudo que muda entre vídeos (títulos, CTA, cidade, cores, tempos de corte) fica em camadas nomeadas `TXT …`/controles de expressão, trocáveis por **nexrender** (`assets` tipo `data`) ou Essential Graphics. Um template, vários Reels.

## 8. Entrega
1080×1920, 30 fps; render pelo nexrender/aerender (CPU) → ffmpeg H.264 CRF 18, yuv420p, AAC 192k, `+faststart`; conferir quadros 0, cortes, impacto e último (loop) antes de enviar; apagar intermediários.
