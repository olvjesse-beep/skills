# Expressões prontas (ExtendScript/JavaScript do AE)

Todas assumem 30 fps. `f` = quadro atual. Ajustar os números no topo de cada uma
ou ligar a Sliders no layer `CONTROLES`.

## 1. Saída suave (equivalente a `Easing.bezier(0.22, 1, 0.36, 1)`)

```js
// em qualquer propriedade numérica: anima de A para B entre os quadros d0 e d1
var d0 = 0, d1 = 12, A = 0, B = 1;
var t = clamp((timeToFrames(time) - d0) / (d1 - d0), 0, 1);
var e = 1 - Math.pow(1 - t, 3.2);   // aproximação da curva 0.22,1,0.36,1
A + (B - A) * e;
```

## 2. Mola com overshoot (equivalente a `spring({damping: 9-12})`)

```js
// Scale: entra com sobra e assenta. d = quadro de início
var d = 20, amp = 0.12, freq = 3.2, decay = 6;
var t = Math.max(0, (timeToFrames(time) - d) / 30);
var s = t <= 0 ? 0 : 1 - Math.exp(-decay * t) * Math.cos(freq * Math.PI * t) ;
s = Math.max(0, s + (t > 0 ? amp * Math.exp(-decay * t) * Math.sin(freq * Math.PI * t) : 0));
[100 * s, 100 * s];
```

## 3. Câmera sobre imagem: push + punch + tremor

```js
// Scale do layer de vídeo/pré-comp. EV = quadro do impacto
var EV = 39, f = timeToFrames(time);
var push = 112 + 12 * Math.pow(clamp(f / EV, 0, 1), 2);
var punch = linear(f, EV - 1, EV + 2, 0, 1);
punch = f <= EV + 2 ? punch : linear(f, EV + 2, EV + 14, 1, 0.4);
var s = push + 10 * punch;
[s, s];
```

```js
// Position do mesmo layer: tremor que decai em 10 quadros
var EV = 39, f = timeToFrames(time);
var k = f < EV ? 0 : (f <= EV + 2 ? linear(f, EV, EV + 2, 0, 1) : linear(f, EV + 2, EV + 10, 1, 0));
value + [k * Math.sin(f * 2.7) * 14, k * Math.cos(f * 3.3) * 14];
```

## 4. Onda do anel de marcação (repete a cada 40 quadros)

```js
// Scale da elipse de onda
var p = (timeToFrames(time) % 40) / 40;
var s = 90 + 60 * p; [s, s];
```
```js
// Opacity da mesma elipse
var p = (timeToFrames(time) % 40) / 40; (1 - p) * 70;
```

## 5. Contador de graus ligado a um Slider

```js
// Source Text. Slider "angulo" no layer CONTROLES
var a = Math.round(thisComp.layer("CONTROLES").effect("angulo")("Slider"));
a + "°";
```
```js
// Fill Color (efeito Fill) : vermelho a partir de 60°
var a = thisComp.layer("CONTROLES").effect("angulo")("Slider");
a >= 60 ? [1, 0.353, 0.306, 1] : [1, 1, 1, 1];
```

## 6. Flutuação lenta (fundo, imagens sem rotação)

```js
// Position: deriva suave, sem girar a imagem
var a = time;
value + [Math.sin(a * 0.3) * 80, Math.cos(a * 0.25) * 90];
```

## 7. 3D por estado: tempo → quadro da sequência

Importar a sequência (ex.: 237 JPG de 0° a 120°), ligar Time Remapping e usar:

```js
// Time Remap: Slider "angulo" (0–120) escolhe o quadro da sequência
var N = 237, MAX = 120, fpsSeq = 30;
var th = clamp(thisComp.layer("CONTROLES").effect("angulo")("Slider"), 0, MAX);
framesToTime(Math.round(th / MAX * (N - 1)), fpsSeq);
```

O Slider "angulo" recebe os keyframes da coreografia (ex.: 0 → 70 até o
impacto, segura, sobe a 120 e volta a 0). Contador, cor de dor e texto leem
o mesmo Slider, então tudo fica sincronizado.

## 8. Clarão

```js
// Opacity do sólido de clarão. EV = quadro do impacto
var f = timeToFrames(time) - 180;
f < 0 ? 0 : (f <= 3 ? linear(f, 0, 3, 0, 55) : linear(f, 3, 12, 55, 0));
```
